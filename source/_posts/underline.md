---
title: 仿微信公众号文章内容划线技术方案研究
date: 2023-06-03
tags: [JavaScript]
categories: 
- 技术方案
---

微信公众号上线了一个功能：用户可以对微信公众号内的文字进行划线，也能看到好友对同一篇文章的划线。该功能现在正在灰度中。我们思考一下如何去实现这样的功能？

### 长按的文字操作栏的自定义

长按选中后，禁止系统的操作控件，通知H5展示操作工具栏。

- 安卓：监听：onPrepareActionMode，阻止弹出系统的工具栏。监听：onActionModeStarted，通知H5展示操作工具。
- IOS：监听UIMenuController的显示，通知H5弹出自定义弹窗
- H5：如何确定弹窗的位置？`window.getSelection()`对象，可以拿到选中内容的位置信息。这样我们就可以实现一个跟随选中内容的弹窗了。

### 划线记录的数据是什么？

记录文本位置和文本内容。

```js
// 获取文章的文本信息
function getArticleText() {
  var articleBody = $('article')[0].cloneNode(true) // 拷贝后的DOM结构
  var innerText = articleBody.innerText
  return innerText
}

// 获取文章选中内容的位置【该方法有缺陷，如果段落的文字一模一样，就没有办法区分位置了。建议这里给文章的段落进行编号】
function getSelectionPos() {
  var selection = window.getSelection() // 获取Selection对象
  var range = selection.getRangeAt(0) // 获取Range对象
  // 获取选中文本在整篇文章文本中的起始位置
  var startIndex = range.startOffset
  var startContainer = range.startContainer
  var startContainerIndex = getArticleText().indexOf(startContainer.wholeText) // 当前段落相对于整体的

  var endIndex = range.endOffset
  var endContainer = range.endContainer
  var endContainerIndex = getArticleText().indexOf(endContainer.wholeText) // 当前段落相对于整体的
  return [startContainerIndex + startIndex, endIndex + endContainerIndex]
}
```

### 如何将选中的内容进行划线&如何通过给定的文本位置把指定内容进行划线

关键方法：`document.createTreeWalker()`

大概思路就是把文章的内容进行分块，文本按照DOM节点、文本节点进行分块，然后根据`window.getSelection()`对象拿到选中文字的位置，在我们的小分块中找到对应的内容，然后用一个类名包裹。

```js
function defaultGetKeyByRange({ start: e, end: t }) {
  return `${e}-${t}`;
}
function getKeyByRange(e) {
  return `${e.start}-${e.end}`;
}
function needFilterNode(e) {
  return e.classList &&
    ("IFRAME" == e.nodeName ||
      e.classList.contains("ignoreDom")
    ? NodeFilter.FILTER_REJECT
    : NodeFilter.FILTER_ACCEPT;
}
function splitRange(e) {
  const {
    startContainer: t,
    startOffset: n,
    endContainer: i,
    endOffset: A,
  } = e,
    o = document.createRange(),
    r = getCharBottom(t, n);
  if (r === getCharBottom(i, A - 1))
    return o.setStart(t, n), o.setEnd(i, A), [o];
  {
    const e = findRowLastChar(r, t, n, i, A);
    o.setStart(t, n), o.setEnd(e.node, e.offset + 1);
    const s = e.offset + 1 === e.node.textContent.length;
    return [
      o,
      ...splitRange({
        startContainer: s ? e.node._next : e.node,
        startOffset: s ? 0 : e.offset + 1,
        endContainer: i,
        endOffset: A,
      }),
    ];
  }
}

function UnderlineAction(e) {
  // 如果没有getKeyByRange方法，则使用默认方法
  !e.getKeyByRange && (e.getKeyByRange = defaultGetKeyByRange);
  let t = [];
  const n = {},
    i = {};
  // 判断节点是否为文本节点
  function A(e) {
    return "#text" === e.nodeName && e.textContent.length;
  }
  // 在指定范围内插入下划线
  function insertSpanInRange(i, A, o, r = !1) {
    const s = [];
    function a(n, i, A, o) {
      const r = n.textContent.slice(0, i),
        a = n.textContent.slice(i, A),
        l = n.textContent.slice(A),
        c = n.splitText(i),
        d = document.createDocumentFragment(),
        p = (function (t, n) {
          const i = document.createElement(e.tag || "span");
          return (
            (i.textContent = t),
            (i.className = "underline"),
            s.push(i),
            Object.keys(n).forEach((e) => (i[e] = n[e])),
            i
          );
        })(a, o),
        m = p.childNodes[0];
      // 将下划线的位置添加到t数组中
      if (
        (t.fill(m, n._wordoffset + i, n._wordoffset + A),
          (m._wordoffset = n._wordoffset + i),
          d.appendChild(p),
          l)
      ) {
        const e = document.createTextNode(l);
        (m._next = e),
          (e._prev = m),
          (e._wordoffset = n._wordoffset + A),
          n._next
            ? ((e._next = n._next),
              (n._next._prev = e),
              t.fill(e, n._wordoffset + A, n._next._wordoffset))
            : t.fill(e, n._wordoffset + A),
          d.appendChild(e);
      } else n._next && ((m._next = n._next), (n._next._prev = m));
      // 将下划线添加到文本节点中
      return (
        r
          ? ((n._next = m), (m._prev = n))
          : (n._prev && ((n._prev._next = m), (m._prev = n._prev)), n.remove()),
        c.parentNode.insertBefore(d, c),
        c.remove(),
        m
      );
    }
    try {
      if (A <= i) return;
      const l = e.getKeyByRange({ start: i, end: A, props: o }),
        c = t[i],
        d = t[A - 1];
      if (!c || !d) return;
      let p = c;
      if (c === d)
        a(p, i - p._wordoffset, A - p._wordoffset, { ...o, underlineKey: l });
      else
        do {
          if (p === c)
            p = a(p, i - p._wordoffset, p.textContent.length, {
              ...o,
              underlineKey: l,
            });
          else {
            if (p === d) {
              p = a(p, 0, A - p._wordoffset, { ...o, underlineKey: l });
              break;
            }
            p = a(p, 0, p.textContent.length, { ...o, underlineKey: l });
          }
          p = p._next;
        } while (p);
      // 将下划线的位置添加到n或i对象中
      return r ? s : ((n[l] = s), l);
    } catch (l) {
      console.error(l);
    }
  }
  function r(e) {
    const n = e.parentNode;
    let i, o;
    for (; (i = e.childNodes[0]);) n.insertBefore(i, e);
    e.remove(),
      n.childNodes.forEach((e) => {
        A(e)
          ? (o || (o = e),
            t.fill(o, e._wordoffset, e._wordoffset + e.textContent.length),
            e._next && (e._next._prev = o),
            (o._next = e._next))
          : (o = null);
      }),
      n.normalize();
  }
  function removeSpanByKey(e, t = !1) {
    t
      ? i[e] && (i[e].forEach((e) => e.remove()), delete i[e])
      : n[e] && (n[e].forEach(r), delete n[e]);
  }
  function getSpanByKey(e, t = !1) {
    return t ? i[e] || [] : n[e] || [];
  }
  /**
 * 该函数用于获取文本节点数组
 * @returns {Array} 返回文本节点数组
 */
  function l() {
    console.log('看看这一行')
    t = [];
    const n =
      "string" == typeof e.selector
        ? document.querySelector(e.selector)
        : e.selector;
    if (!n) return;
    let i = 0,
      o = null;
    const r = document.createTreeWalker(
      n,
      NodeFilter.SHOW_ALL,
      { acceptNode: e.needFilterNode || (() => NodeFilter.FILTER_ACCEPT) },
      !0
    );
    let s = r.currentNode;
    for (; s;) {
      if (((s._wordoffset = i), A(s))) {
        o && ((o._next = s), (s._prev = o));
        const e = i + s.textContent.length;
        (t.length = e), t.fill(s, i, e), (i = e), (o = s);
      }
      s = r.nextNode();
    }
    window.t = t
    console.log("t", t) // 分块的内容
    return t;
  }
  return (
    l(),
    {
      insertSpanInRange: insertSpanInRange,
      removeSpanByKey: removeSpanByKey,
      getSpanByKey: getSpanByKey
    }
  )
}

window.underlineAction = UnderlineAction({
  selector: 'article',
  getKeyByRange: getKeyByRange,
  needFilterNode: needFilterNode
})
```

### 如果文本内容发生了改变，已经记录的划线的位置该如何处理？

把改动的细节上报给服务端，服务端去更新已经记录的划线数据。

```js
// 生成文本对比工具
const JsDiff = require('diff')

function compareText(text1, text2) {
  const diff = JsDiff.diffChars(text1, text2)
  let index = 0 // 添加计数器变量
  const newDiff = []
  diff.forEach(part => {
    part.index = text1.indexOf(part.value, index) // 添加 part 在 text1 中的索引
    if(part.added || part.removed) {
      part.index = index 
      newDiff.push(part)
    } else {
      index = part.index + part.value.length // 更新计数器变量
    }
  })
  return newDiff // 返回索引数组
}

// 测试
const text1 = 'Hello, world!';
const text2 = 'Hello, JavaScript!';
const comparedText = compareText(text1, text2)
console.log(comparedText)
```

```js
// 输出内容
```

根据我们已划线的位置和内容的变化，我们最终计算出新的文本里的划线位置。

