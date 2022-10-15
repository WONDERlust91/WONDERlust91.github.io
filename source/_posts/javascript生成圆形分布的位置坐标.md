---
title: javascript生成圆形分布的位置坐标
date: 2021-06-02 16:27:27
tags:
  - 圆形位置坐标
  - javascript
categories: javascript
---

## 圆形布局-应用场景

在使用 echarts 关系图 graph 时，若要使用圆形或引力自动布局坐标系必须为 null；想要使关系图连线有动态效果，又需要使用动态的路线图 lines，lines 必需使用 cartesian2d 或 geo 的坐标系；因此为了实现动效，只能放弃自动圆形布局，手动计算出一个圆形布局的坐标。

## 圆形布局-代码示例

```js
/**
 * 根据中心位置坐标、布局半径、布局数量及布局角度范围，按圆平均计算布局位置
 * @param {array} centerPosition 中心位置数组
 * @param {number} r 布局半径
 * @param {number} number 布局数量
 * @param {number} startAngle 布局起始角度，默认为0
 * @param {number} endAngle 布局结束角度，默认为360
 * @returns 生成的布局位置数组
 */
export function calcLayoutCircular(centerPosition, r, number, startAngle = 0, endAngle = 360) {
  /**
   * 将传入的任意角度转换为0-360度的角度
   * @param {number}} angle 传入角度
   * @returns 转换后的角度
   */
  function normalizeAngle(angle) {
    // 任意角度相对360度取余数
    const normalizedAngle = angle % 360;
    // 若为负角度则加上360度，转换为正角度
    return normalizedAngle < 0 ? normalizedAngle + 360 : normalizedAngle;
  }
  startAngle = normalizeAngle(startAngle);
  endAngle = normalizeAngle(endAngle);
  let intervalAngle;
  // 计算间隔角度
  // 若角度范围是完整圆周，起始角度与结束角度只占用一个布局数量，按布局数量平均分配角度即可；
  // 若角度范围不是完整圆周，则起始角度与结束角度会占用两个布局数量，需要以 number - 1 计算角度间隔，若number为1时不减1
  if (endAngle - startAngle === 0) {
    intervalAngle = 360 / number;
  } else {
    intervalAngle = normalizeAngle(endAngle - startAngle) / (number - 1 || 1);
  }
  // 根据布局数量与间隔角度计算所有布局位置
  const positionArr = [];
  for (let i = 0; i < number; i++) {
    let angle = normalizeAngle(startAngle + intervalAngle * i);
    const x = centerPosition[0] + r * Math.sin((angle * Math.PI) / 180);
    const y = centerPosition[1] + r * Math.cos((angle * Math.PI) / 180);
    positionArr.push({
      angle,
      position: [x, y],
    });
  }
  return positionArr;
}
```

## 标签位置-应用场景

使用圆形布局后，当元素标签不放在元素上居中显示，而要根据相对圆心的位置，显示在上、右、下、左等不同方位时，使用此方法计算。

## 标签位置-代码示例

```js
/**
 * 根据传入的角度（当前元素相对圆心的角度）计算出标签位置，简单模式只计算左右，非简单模式计算上下左右
 * @param {number} angle 传入的角度
 * @param {boolean} simple 是否简单模式
 * @returns 位置字符串
 */
export function calcLabelPositionByAngle(angle, simple = true) {
  if (simple) {
    if (angle >= 0 && angle < 180) {
      return "right";
    } else {
      return "left";
    }
  } else {
    if (angle > 275 || angle <= 45) {
      return "top";
    } else if (angle > 45 && angle <= 135) {
      return "right";
    } else if (angle > 135 && angle <= 215) {
      return "bottom";
    } else if (angle > 215 && angle <= 275) {
      return "left";
    } else {
      return "top";
    }
  }
}
```
