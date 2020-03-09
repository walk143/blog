---
title: 前端json对象升/降阶处理
tags:
  - 前端
  - json
  - 方法
categories:
  - 技术
  - 前端
date: 2020-03-09 23:29:04
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
    let parsed = this.reduceJson(child); //解析此节点
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
function reduceJson (json) {
    for (let childKey in json) {
        let child = json[childKey];
        if (typeof child === "object") {
            let parsed = this.reduceJson(child); //解析此节点
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

### 添加位置属性

为了降阶后可以还原回原json，降阶后的json节点需要能够保留原节点位置信息。

#### 思路

保留原节点信息有两种思路：

1. 添加到节点的key中。如原josn节点`"field111": "value"`改为`"field.field11.field111": "value"`。此种方式会改变原有key，使用时将key分割取数组最后一个元素即可。

2. 给节点添加一个属性表示路径。如原josn节点`"field111": "value"`改为`"field111": {"value": "value","path": "field1.field11"}`。此种方法也会对节点结构进行影响，但只要规范好属性名，对降阶后的使用更方便一点。

#### 实现

```javascript
function reduceJson (json, path) {
    for (let childKey in json) {
        let child = json[childKey];
        if (typeof child === "object") {
            let parsed = this.reduceJson(child, path+"."+childKey); //解析此节点
            // 将此节点解析后的内容添加到当前根节点，删除原有节点
            for (let parsedKey in parsed) {
                // let attribute = { //非object已经转换，此处直接添加
                //     "value": parsed[parsedKey],
                //     "path": path+"."+parsedKey
                // }
                eval("json."+parsedKey+" = parsed[parsedKey]"); //初始化节点
            }
            delete json[childKey];
        } else {
            let attribute = {
                "value": child,
                "path": path+"."+childKey
            }
            json[childKey] = attribute;
        }
    }
    return json;
}
```

转换后json如下

```json
{
    "field3": {
        "value": "value",
        "path": "root.field3"
    },
    "field12": {
        "value": "value",
        "path": "root.field1.field12"
    },
    "field111": {
        "value": "value",
        "path": "root.field1.field11.field111"
    },
    "field112": {
        "value": "value",
        "path": "root.field1.field11.field112"
    }
}
```

# 升阶

## 思路

之前转换后的json，已经有了path属性。所以只需要遍历所有节点，将value值赋给对应path节点。赋值的时候检查对应父节点是否存在，不存在先初始化。

## 实现

```javascript
function ascendJson (json) {
    let ascendedJson = {};
    for (let childKey in json) {
        let child = json[childKey];
        let key = "";
        if (typeof child.path != "undefined") {
            let paths = child.path.split(".");
            paths.shift(); //移除第一个root
            paths.map(path => {
                key+= "."+path;
                if (eval("typeof ascendedJson"+key) === "undefined") {
                    eval("ascendedJson"+key+"= {}");
                }
            })
            eval("ascendedJson"+key+"= child.value");
        }
    }
    return ascendedJson;
}
```

# 总结

总体上实现了对json的升/降阶的处理。上述程序只能实现对无数组的处理，且处理的最小单位为`string`，无法实现处理后`value`内容是`object`的需求。不过只是提供一个升降阶的思路，对于其他形式也可类似处理。

# 尚存疑点
- 降阶时，给处理的json添加内容后，for(let in)为何没有重复处理