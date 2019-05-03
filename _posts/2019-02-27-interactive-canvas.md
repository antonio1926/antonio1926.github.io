---
layout: post
title: svg convert to image
subtitle: "A Gentle Introduction to Making HTML5 Canvas Interactive"
gh-repo: daattali/beautiful-jekyll
gh-badge: [star, fork, follow]
tags: [html5 canvas]
comments: true
---

d3 로 제작한 차트 등의 svg를 이미지로 저장해야 할 때가 있다.
codepen이나 github 등에 충분히 많은 예제들이 존재하지만, 최대한 상세하게 정리해 보려 한다.
(이 포스트는 d3.js를 사용한 경우를 가정하였다. 하지만, 다른 경우에도 충분히 활용 가능하다.)

일단, svg를 [cloneNode](https://developer.mozilla.org/ko/docs/Web/API/Node/cloneNode) 를 활용해 복사본을 만들어 둔다.
cloneNode() 메서드는 이 메시지를 호출한 Node의 복사본을 반환한다.
깊은 복사를 해야 하므로, cloneNode(true)를 실행한다.

```javascript
const cloneSvg = svg.node().cloneNode(true);
```

### Foreign Object 를 제거하자

먼저, svg를 이미지로 저장하기 위해서는 foreignObject를 모두 제거할 필요가 있다.
svg의 foreignObject SVG 요소는 다른 XML 네임 스페이스의 요소를 포함한다. 브라우저 컨텍스트에서 가장 가능성있는 것은 xHTML이다.
[foreignObject](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/foreignObject)는 svg에 xhtml 요소를 추가해 툴팁 등의 간단한 요소를 쉽게 추가할 수 있지만, svg와 같은 벡터 그래픽 요소가 아니므로, 이미지로 저장할 수는 없다.

만약, 이미지로 저장할 것을 염두에 둔다면 foreignObject는 차트의 툴팁 등 중요하지 않은 요소에 제한적으로 사용할 것을 권한다.

```javascript
const cloneSvg = svg.node().cloneNode(true);

const foreignObject = d3.select(cloneSvg).selectAll('foreignObject');
foreignObject.remove();
```

**Here is some bold text**

## Here is a secondary heading

Here's a useless table:

| Number | Next number | Previous number |
| :------ |:--- | :--- |
| Five | Six | Four |
| Ten | Eleven | Nine |
| Seven | Eight | Six |
| Two | Three | One |


How about a yummy crepe?

![Crepe](https://s3-media3.fl.yelpcdn.com/bphoto/cQ1Yoa75m2yUFFbY2xwuqw/348s.jpg)

Here's a code chunk:

~~~
var foo = function(x) {
  return(x + 5);
}
foo(3)
~~~

And here is the same code with syntax highlighting:

```javascript
var foo = function(x) {
  return(x + 5);
}
foo(3)
```

And here is the same code yet again but with line numbers:

{% highlight javascript linenos %}
var foo = function(x) {
  return(x + 5);
}
foo(3)
{% endhighlight %}

## Boxes
You can add notification, warning and error boxes like this:

### Notification

{: .box-note}
**Note:** This is a notification box.

### Warning

{: .box-warning}
**Warning:** This is a warning box.

### Error

{: .box-error}
**Error:** This is an error box.
