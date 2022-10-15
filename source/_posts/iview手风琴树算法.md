---
title: iview手风琴树算法
date: 2019-02-06 15:26:40
tags:
    - accordion tree
    - 手风琴树
    - 算法
categories: iview
---

# iview 手风琴树算法

设置外部变量 lastNode 保存上次节点（若 lastNode 为 null 则什么都不做）以判断父子关系

依据父子关系：

若点击自身或子树，不操作。
若点击父树，则折叠所有子树。
若点击父树的兄弟树，则折叠与兄弟树同级的所有其他父树

如果 lastNode 和 currentNode 在同一树分支中，父树的 nodeKey 一定比子树小，遍历 nodeKey 小的树，来确定 lastNode 和 currentNode 是父子树关系，还是兄弟树关系

若确定 currentNode 是 lastNode 的兄弟树。则查找 currentNode 与 lastNode 的公共父树。（由于 accordion 模式，若是兄弟树关系则 currentNode 一定是 lastNode 父树的兄弟树，因此 currentNode 的父树也是 lastNode 的父树，是双方的公共父树）

用到的函数：查找兄弟树父树、折叠除当前节点外，当前节点父树下所有的结点、判断树关系、递归查找是否为子节点

<!-- more -->

```javascript
/*
 * 传入当前点击的树节点与整个树的数据
 * 修改树的数据以实现手风琴树
 */
let lastNode = null;
function toggleMenuTreeOrigin(currentNode, treeData) {
  /*
   * 根据节点nodeKey的大小传入两个节点
   * 判断第二个节点是否为第一个节点的子节点
   */
  function isChild(parent, child) {
    // 遍历判断第二个节点的nodeKey是否与第一个节点的子节点相同，若相同则返回true，否则判断第一个节点的子节点是否还有子节点，若无则返回false，若有则递归判断。
    return parent.children.some(item =>
      item.nodeKey === child.nodeKey
        ? (() => {
            return true;
          })()
        : (() => (item.children ? isChild(item, child) : false))()
    );
  }
  /*
   * 传入当前点击节点与上次点击节点
   * 判断当前点击节点与上次点击节点是相同节点、父节点、子节点还是兄弟节点关系
   */
  function treeRelationship(currentNode, lastNode) {
    // 若当前节点nodeKey与上次点击节点nodeKey相同，则返回"same"，若当前节点nodeKey大于上次节点nodeKey，根据iview生成nodeKey的规律，当前节点只可能是上次节点的子节点或兄弟节点，若当前节点nodeKey小于上次节点nodeKey，则当前节点只可能是上次节点的父节点或兄弟节点，使用isChild方法判断是否父子节点，根据判断结果返回"parent"、"child"、"brother"
    if (currentNode.nodeKey === lastNode.nodeKey) {
      return "same";
    } else if (currentNode.nodeKey > lastNode.nodeKey) {
      return isChild(lastNode, currentNode) ? "child" : "brother";
    } else {
      return isChild(currentNode, lastNode) ? "parent" : "brother";
    }
  }
  /*
   * 传入树数据和当前节点
   * 返回当前节点的父节点
   */
  function findParent(treeData, currentNode) {
    // 最终父节点
    let finalParentNode = null;
    /*
     * 传入树数据，当前点击节点、与此时遍历节点的父节点
     * 将最终父节点赋值为当前点击节点的父节点
     */
    function findParentInner(treeData, currentNode, parentNode) {
      // 内部查找函数，若当前节点nodeKey与遍历节点nodeKey相同时，则此时的parentNode为要找的最终父节点，否则判断遍历节点是否存在子节点，若存在则递归执行函数，若不存在则什么都不做
      treeData.forEach(item =>
        item.nodeKey === currentNode.nodeKey
          ? (() => {
              finalParentNode = parentNode;
            })()
          : (() => {
              item.children
                ? findParentInner(item.children, currentNode, item)
                : "";
            })()
      );
    }
    // 执行内部函数
    findParentInner(treeData, currentNode, finalParentNode);
    return finalParentNode;
  }
  /*
   * 传入树数据，当前点击节点
   * 折叠树
   */
  function collapseTree(treeData, currentNode) {
    treeData.forEach(item => {
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
  // 若上次点击节点为空则什么都不做，否则判断当前点击节点与上次点击节点关系，若为相同或子节点，则什么都不做；若为父节点，则折叠当前节点所有子节点；若为兄弟节点，则找到当前节点父节点，若父节点为空则说明点击了根节点，折叠整个树，否则折叠父节点的所有子节点
  lastNode === null
    ? (() => {})()
    : (() => {
        let parentNode;
        switch (treeRelationship(currentNode, lastNode)) {
          case "same":
          case "child":
            break;
          case "parent":
            collapseTree(currentNode.children, currentNode);
            break;
          case "brother":
            parentNode = findParent(treeData, currentNode);
            parentNode === null
              ? collapseTree(treeData, currentNode)
              : collapseTree(parentNode.children, currentNode);
            break;
        }
      })();
  // 将上次点击节点赋值为本次点击节点
  lastNode = currentNode;
}
```

前方高能预警

---

以上全是Bullshit，算法冗余太多。

# 最终精简算法

无需判断父子关系，直接找到当前点击节点 currentNode 的父节点 parrentNode，折叠除当前节点 currentNode 外，父节点 parentNode 下所有子节点。

用到的函数：查找父结点，折叠除父节点外所有子节点

```javascript
/*
 * iview手风琴树（每次只展开一个树节点）
 * 传入当前节点与树数据（类型Array）
 */
export const accordionTree = (currentNode, treeData) => {
  /*
   * 传入树数据和当前节点
   * 返回当前节点的父节点
   */
  function findParent(treeData, currentNode) {
    // 声明最终父节点
    let finalParentNode = null;
    /*
     * 传入树数据，当前点击节点、与此时遍历节点的父节点
     * 将最终父节点赋值为当前点击节点的父节点
     */
    function findParentInner(treeData, currentNode, parentNode) {
      // 内部查找函数，若当前节点nodeKey与遍历节点nodeKey相同时，则此时的parentNode为要找的最终父节点，否则判断遍历节点是否存在子节点，若存在则递归执行函数，若不存在则什么都不做
      treeData.forEach(item =>
        item.nodeKey === currentNode.nodeKey
          ? (() => {
              finalParentNode = parentNode;
            })()
          : (() => {
              item.children
                ? findParentInner(item.children, currentNode, item)
                : "";
            })()
      );
    }
    // 执行内部函数
    findParentInner(treeData, currentNode, finalParentNode);
    return finalParentNode;
  }
  /*
   * 传入树数据，当前点击节点
   * 折叠树
   */
  function collapseTree(treeData, currentNode) {
    treeData.forEach(item => {
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
  parentNode === null
    ? collapseTree(treeData, currentNode)
    : collapseTree(parentNode.children, currentNode);
};
```