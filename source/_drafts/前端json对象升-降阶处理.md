---
title: 前端json对象升/降阶处
date: 2020-03-08 22:35:04
tags:
    - 前端
    - json
    - 方法
categories:
    - 技术
    - 前端
---

当前端请求是`application/json`时，填写查询参数时只关心每个节点的内容,拼接参数应该由后台实现。本篇探索对一个多层json进行升/降阶处理的方法。

<!-- more -->

当一个实际请求为如下json时，填写查询参数时前端应该只关心值带"value"的节点，如`field111,field112`。对于field1展示出来没有必要，其对应的节点关系应该由后台负责，所以需要对json进行降阶处理。此种方法不适合json中带数组的情况，只是解决上述需求。

```json
{
    "field1": {
        "field11": {
            "field111": "value",
            "field112": "value"
        },
        "field12": "value"
    },
    "field3": "value"
}
```

# 降阶

由于只是前端展示填写参数的时候无层级，实际请求的时候仍需要json多层结构，所以降阶后的json需要能够保留层级信息。思路是给原来的元素加一个`parent`属性来标识。

如对于上述json，转换后应该如下。

```json
{
    "field111": {
        "value": "value",
        "parent": "/path_to_parent"
    },
    "field112": {
        "value": "value",
        "parent": "/path_to_parent"
    },
    "field12": {
        "value": "value",
        "parent": "/path_to_parent"
    },
    "field3": {
        "value": "value",
        "parent": "/path_to_parent"
    }
}
```

## 思路

实现上述需要满足三个需求

- 能够遍历递归
- 能够记录当前节点到根节点的路径
- 删除转换后的空节点

## 实现

### 遍历递归

对于object对象，遍历可以通过for循环实现。递归通过调用本身，判断结束条件实现。

#### 遍历

通过`let in`可取得json里面的`key`。即`field1,field3`之类。然后通过`json[childKey]`即可取得元素值。
```javascript
for (let childKey in json) {
    let child = json[childKey];
}
```

#### 递归

处理逻辑：如果有子节点，就将子节点处理成一个一层json，处理完后将里面的元素依次加到当前根节点。如果没有子节点，不处理。过程如下：

```json
{
    "field11": {
        "field111": "value",
        "field112": "value"
    },
    "field12": "value",
    "field3": "value"
}
```
```json
{
    "field111": "value",
    "field112": "value",
    "field12": "value",
    "field3": "value"
}
```

通过判断`child`的类型，如果是object，那说明还有子节点，如果是string等其他类型，默认为没有子节点。
```javascript
if (typeof child === "object") {
    let parsed = this.parseJson(child); //解析此节点
    // 将此节点解析后的内容添加到当前根节点，删除原有节点
    for (let parsedKey in parsed) {
        json.parsedkey = parsed[parsedKey];
    }
    delete json.childKey;
} else {
    // 无需处理
}
```

完整代码为

```javascript
function parseJson (json) {
    for (let childKey in json) {
        let child = json[childKey];
        if (typeof child === "object") {
            let parsed = this.parseJson(child); //解析此节点
            // 将此节点解析后的内容添加到当前根节点，删除原有节点
            for (let parsedKey in parsed) {
                eval("json."+parsedKey+" = parsed[parsedKey]"); // 因为是新加属性，需要用到eval取parsedKey的值而不是其本身
            }
            delete json[childKey];
        } else {
            // 无需处理
        }
    }
    return json;
}
```
