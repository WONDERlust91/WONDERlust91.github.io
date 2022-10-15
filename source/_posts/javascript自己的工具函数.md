---
title: javascript自己的工具函数
date: 2019-02-09 20:35:52
tags:
  - 工具函数
  - javascript
categories: javascript
---

使用 export 语法将工具函数暴露出去

<!-- more -->

```javascript
/**
 * @function 生成UUID
 * @export 导出为工具函数
 * @return {String} 返回值为生成的UUID
 */
export const generateUUID = () => {
  let date = new Date().getTime();
  let uuid = "xxxx_xxxx_xxxx_yxxx".replace(/[xy]/g, function (char) {
    let random = (date + Math.random() * 16) % 16 | 0;
    date = Math.floor(date / 16);
    return (char == "x" ? random : (random & 0x7) | 0x8).toString(16);
  });
  return uuid;
};

/**
 * @function 深拷贝对象
 * @export 导出为工具函数
 * @param {Object|Array} 入参为被拷贝对象或数组
 * @return {Object|Array} 返回值为拷贝出的对象或数组
 */
export const deepCopyObject = (obj) => {
  let newObj;
  Array.isArray(obj) ? (newObj = []) : (newObj = {});
  const keyArray = Object.keys(obj);
  keyArray.forEach((key) => {
    if (typeof obj[key] == "object" && obj[key] != null) {
      newObj[key] = deepCopyObject(obj[key]);
    } else {
      newObj[key] = obj[key];
    }
  });
  return newObj;
};

/**
 * @function 根据id和parentId生成树
 * @export 导出为工具函数
 * @param {Object} 所有参数都在对象中
 * @param {Array} [data=[]] 生成树的原始一维数组，默认为[]
 * @param {String|Number} [rootId = 0] 根节点id，默认为0
 * @param {String} [id="id"] id字段名，默认为"id"
 * @param {String} [parentId="parentId"] parentId字段名，默认为"parentId"
 * @param {String} [children="children"] children字段名，默认为"children"
 * @return {Array} 返回生成树的数组
 */
export const createTree = ({ data = [], rootId = 0, id = "id", parentId = "parentId", children = "children" } = {}) => {
  // 过滤遍历树原始数组，当前遍历项为父节点，返回根节点数组
  return data.filter((parent) => {
    parent.expand = true;
    // 嵌套过滤遍历树原始数组，当前遍历项为子节点，返回当前父节点的所有子节点
    let branchArr = data.filter((child) => {
      return parent[id] == child[parentId];
    });
    if (branchArr.length > 0) {
      // 若存在子节点，则给父节点添加一个children属性，并将branchArr赋值给父节点
      parent[children] = branchArr;
    }
    return parent[parentId] == rootId; //返回根节点数组
  });
};
/**
 * 通过id与parentId的一维数组生成树（Typescript）
 * 该方法相较上面的filter递归方式执行效率更高，时间复杂度由O(n2)降低为O(n)
 * @param 配置对象 data为原始数据；rootId为根节点对应的id值；idAttr为id字段名，默认为id；parentAttr为父id字段名，默认为parentId；childrenAttr为子节点字段名，默认为children
 * @returns 树数组
 */
export const createTree = ({
  data = [],
  rootId = 0,
  idAttr = "id",
  parentAttr = "parentId",
  childrenAttr = "children",
}: {
  data: { [key: string]: any }[],
  rootId?: number,
  idAttr?: string,
  parentAttr?: string,
  childrenAttr?: string,
}) => {
  // 生成用于查找父节点的map
  const map = data.reduce((map, item, index) => {
    map[item[idAttr]] = index;
    item[childrenAttr] = [];
    return map;
  }, {});
  // 将每个元素挂载到对应的父节点
  return data.reduce((tree, item, index, arr) => {
    // 若当前节点的父节点为根节点，则放入最终的树数组
    if (item[parentAttr] === rootId) tree.push(item);
    // 否则将当前节点挂载到相应的父节点上
    else {
      // 判断父节点是否存在于map中，存在则挂载，否则报错
      if (map[item[parentAttr]] !== undefined) arr[map[item[parentAttr]]][childrenAttr].push(item);
      else console.error(`Id ${item[idAttr]} No Parent Found`);
    }
    return tree;
  }, []);
};

/**
 * @function iview手风琴树（每次只展开一个树节点）
 * @export 导出为工具函数
 * @param {Object} 入参1：当前节点对象
 * @param {Array} 入参2：树的数组数据
 */
export const accordionTree = (currentNode, treeData) => {
  /**
   * @function 查找当前节点父节点
   * @param {Array} 入参1：树的数组数据
   * @param {Object} 入参2： 当前节点对象
   * @return {Object} 返回值为当前节点的父节点对象
   */
  function findParent(treeData, currentNode) {
    // 声明最终父节点
    let finalParentNode = null;
    /**
     * @function 查找当前节点父节点的内部递归函数
     * @param {Array} 入参1：树的数组数据
     * @param {Object} 入参2：当前点击节点对象
     * @param {Object} 入参3：此时遍历节点的父节点
     */
    function findParentInner(treeData, currentNode, parentNode) {
      // 内部查找函数，若当前节点nodeKey与遍历节点nodeKey相同时，则此时的parentNode为要找的最终父节点，否则判断遍历节点是否存在子节点，若存在则递归执行函数，若不存在则什么都不做
      treeData.forEach((item) =>
        item.nodeKey === currentNode.nodeKey
          ? (() => {
              finalParentNode = parentNode;
            })()
          : (() => {
              item.children ? findParentInner(item.children, currentNode, item) : "";
            })()
      );
    }
    // 执行内部函数
    findParentInner(treeData, currentNode, finalParentNode);
    return finalParentNode;
  }
  /**
   * @function 折叠树其它节点
   * @param {Array} 入参1：树的数组数据
   * @param {Object} 入参2：当前点击节点对象
   */
  function collapseTree(treeData, currentNode) {
    treeData.forEach((item) => {
      // 若遍历节点为折叠则什么都不做，若为展开则判断遍历节点与当前节点nodeKey是否相同，若相同则什么都不做，否则设为折叠
      item.expand
        ? (() => {
            item.nodeKey === currentNode.nodeKey ? "" : (item.expand = false);
          })()
        : (() => {})();
      // 若遍历节点存在子节点则递归此函数，否则什么都不做
      item.children ? collapseTree(item.children, currentNode) : "";
    });
  }
  // 找到当前点击节点的父节点
  let parentNode = findParent(treeData, currentNode);
  // 折叠树，若父节点为null，则为根节点，传入所有树数据（根节点）；若不为null，则传入父节点的子节点
  parentNode === null ? collapseTree(treeData, currentNode) : collapseTree(parentNode.children, currentNode);
};
```

> 参考资料 [生成树方法 createTree](https://stackoverflow.com/questions/18017869/build-tree-array-from-flat-array-in-javascript?page=1&tab=votes#tab-top)
