---
title: vue监听对象内的属性
date: 2019-02-11 16:14:33
tags:
- vue
- 监听对象属性
categories: vue
---

# 监听对象内的属性

通过vue的官方文档我们知道watch函数可以监听data中的各种变量，但若要监听的变量是一个对象中的某个属性，就很难做到了。

```javascript
data() {
    return {
        normalVar: 0,
        myObject: {
            myKey: myValue
        }
    }
},
watch() {
    // 这是对普通对象的监听，要监听myObject中的myKey就无法直接用watch做到了
    normalVar() {
        console.log('normalVar changed');
    }
}
```

那么我们如何监听到myObject中的myKey变化呢？这里就要用到computed属性做为中间层了，通过computed做为中间层就可以完美监听到myObject中myKey的变化了。

```javascript
data() {
    return {
        myObject: {
            myKey: myValue
        }
    }
},
computed() {
    computeMyKey() {
        return this.myObject.myKey
    }
},
watch() {
    computeMyKey() {
        console.log('myKey changed');
    }
}
```