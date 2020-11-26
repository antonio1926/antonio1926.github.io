---
layout: post
title: 브라우저 렌더링과 성능 최적화
subtitle: "성능 최적화를 위한 기본"
gh-repo: daattali/beautiful-jekyll
gh-badge: [star, fork, follow]
tags: [Browser Rendering, Reflow, Repaint, DOM, CSSOM, Rendering Tree, 브라우저 렌더링, 리플로우, 리페인트]
comments: true
---

브라우저에서 사용자가 원하는 화면을 보여주기까지는 많은 작업을 수행해야 합니다. 이러한 작업 대부분은 개발자에게 숨겨져 있습니다. 
그렇다면, 브라우저가 HTML과 CSS 등을 통해 화면에 렌더링된 픽셀로 변환하는 과정은 어떻게 될까요?

성능을 최적화 하려면 이러한 과정을 주의깊에 들여다 볼 필요가 있습니다. HTML, CSS 및 자바스크립트 바이트를 수신한 후 렌더링된 픽셀로 변환하기 위해 필요한 처리 과정을 **'주요 렌더링 경로'**라고 합니다.

주요 렌더링 경로를 잘 알고 최적화 할 수 있다면 브라우저 렌더링 성능을 향상시킬 수 있습니다. 

하지만, 이것만으로는 충분치 않습니다. 사용자가 화면을 조작할 때마다 이벤트가 발생하고, 화면에도 변화가 일어납니다. 
그럴 때마다 DOM(다시 설명하겠습니다)의 수정이 일어나고, 각 개체의 위치를 다시 계산하고 화면을 새로 그립니다. 
이 과정을 **Reflow**와 **Repaint**라고 합니다. 

아래의 두 가지는 브라우저 성능 향상을 위해 알아두어야 할 아주 기초적인 부분입니다.

{: .box-note}
#### 1) 주요 렌더링 경로

{: .box-note}
#### 2) Relow와 Repaint

우리는 이 중에서 **Reflow**와 **Repaint** 부분을 주로 다루겠습니다. 
**'주요 렌더링 경로'** 관련 최적화는 시간이 생길 때 좀 더 자세히 다루도록 하겠습니다. 지금은 간단히 개념만 짚고 넘어가도록 하겠습니다. 


## 1. 주요 렌더링 경로


브라우저의 렌더링 단계를 빠르게 짚어보면 크게 아래의 5가지 경로로 정리할 수 있습니다. 

1. HTML 마크업을 처리하고 **DOM 트리**를 빌드합니다.

2. CSS 마크업을 처리하고 **CSSOM 트리**를 빌드합니다.

3. DOM 및 CSSOM을 결합하여 **렌더링 트리**를 형성합니다.

4. 렌더링 트리에서 레이아웃을 실행하여 **각 엘리먼트의 기하학적 구조(넓이와 높이)와 위치를 계산 (Reflow)**합니다.

5. 개별 엘리먼트를 **화면에 페인트 (Repaint)**합니다.

자, 그럼 1번부터 차근차근 짚어가 봅시다. 


### 1) HTML 마크업을 처리하고 **DOM 트리**를 빌드

DOM은 HTML문서(와 XML 문서)에서 동작하는 언어 독립적인 API입니다.

브라우저는 먼저, HTML마크업의 여러 태그 간의 관계를 정의하는 하나의 문서 모델을 형성합니다. 
이것을 DOM(Document Object Model)이라고 합니다. 

아래 그림과 같이 HTML 엘리먼트 밑에 Body 엘리먼트가 위치하는 식의 트리 구조로 만들어진 것이죠. 그래서 DOM 트리라고도 합니다.

브라우저는 이후 모든 페이지 처리에 이 DOM을 이용합니다.

![DOM](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/images/dom-tree.png?hl=ko)

{: .box-note}
**Tip**. <br>
DOM은 태생적으로 느립니다. <br><br>
브라우저는 DOM과 ECMAScript를 렌더링하는 엔진이 별도로 있습니다. <br>
가장 많이 사용되는 크롬의 경우 크로미움 엔진을 사용하는데, 크로미움은 V8이라는 자바스크립트 엔진과 Blink라는 렌더링 엔진을 사용하는 브라우저입니다.<br>
DOM은 Blink가 렌더링하고, ECMAScript는 v8 엔진이 렌더링합니다. <br><br>
이것은 성능에 중요한 의미가 있습니다. DOM이 하나의 대륙이고 ECMAScript가 다른 대륙인데, 이 두 대륙을 긴 다리로 연결했다고 생각하시면 됩니다. 
두 다리를 건너려면 성능이라는 통행료를 내야 합니다.<br><br>
이런 이유로, 최상의 성능을 유지하려면, DOM을 변화시키는 코드를 최소화 해야 합니다. <br>
가령, for문을 돌리면서 많은 양의 DOM을 변화시키거나 하는 건 그다지 좋은 아이디어가 아닙니다.<br> 
여러개의 DOM을 컨트럴해야 한다면, DOM 관련 변동 사항을 변수 등에 저장했다가 한 번에 변화시키는 것이 좋습니다.

### 2) CSS 마크업을 처리하고 **CSSOM 트리**를 빌드

DOM 트리를 생성한 후, CSS 정보를 통해 CSSOM 트리를 생성합니다. 
아래 이미지처럼 말이죠.

![CSSOM](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/images/cssom-tree.png?hl=ko)


### 3) DOM 및 CSSOM을 결합하여 **렌더링 트리**를 형성

DOM 트리와 CSSOM 트리만으로는 화면을 그릴 수 없습니다. 

BODY, P, DIV, IMG 등의 각 엘리먼트의 위치와 크기, 바탕색, 선 굵기 등을 알아야 각자의 위치에 알맞게 아름다운 화면을 만들 수 있습니다. DOM 트리와 CSSOM 트리가 결합되어야 하는 것이죠.

이 것을 <strong><u>렌더링 트리</u></strong>라고 합니다.

렌더링 트리는 화면에 표시되는 각 엘리먼트의 위치를 계산하고, 배치하며, 화면에 색칠을 하는 등등의 구실을 합니다. <u>렌더링 트리가 만들어져야 브라우저는 엘리먼트를 화면에 표시(페인트)</u>할 수 있습니다.

![Rendering Tree](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/images/render-tree-construction.png?hl=ko)


{: .box-note}
**Tip**. <br>
DOM트리와 렌더링 트리의 구조가 같지는 않습니다. <br>
위 그림에서 보듯이, <u>css에서 display: none 된 DOM 속성(P 아래에 있는 span)은 렌더링 트리에는 존재하지 않습니다</u>.<br>
<u>화면에 페인트할 필요가 없기 때문</u>입니다. <br>
이 특징은 잘 알아둘 필요가 있습니다. 성능 개선(리플로우 회피)을 위한 중요한 방법을 제공하기 때문입니다. <br>
이에 대해서는 아래 글에서 계속 설명하도록 하겠습니다. <br><br>
(물론, DOM 트리에 있는 script 태그와 meta 태그 등도 렌더링 트리에는 존재하지 않습니다.)<br>

### 4) Reflow

렌더링 트리는 화면에 페인트하기 위해서 각 엘리먼트의 크기와와 위치를 계산합니다. 
엘리먼트의 테두리 두께를 바꾸거나, 문단에 텍스트를 추가해서 줄이 늘어나는 등의 변화가 생기면 엘리먼트의 크기와 위치도 다시 계산해야 합니다.
이를 리플로우라고 합니다. 

![Replow](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/images/layout-viewport.png?hl=ko)

아래와 같은 퍼즐을 생각하면 이해가 쉽습니다. 
수많은 퍼즐의 각 면이 아주 잘 짜맞춰져 하나의 아름다운 그림을 만들어냅니다. 브라우저의 화면도 이와 마찬가지입니다. 

![Puzzle](/img/20201019/puzzle.jpg)

 
그런데, 만약 퍼즐 조각 중 하나의 크기가 지금보다 두 배 더 커진다고 가정해 봅시다. 
여러 엘리먼트가 맞물려 있는데, 그 중 하나만 크기가 커지면 틀이 망가지게 됩니다. 이때 그 한 개를 제외한 나머지 모든 퍼즐의 높이와 넓이, 위치를 모두 다시 계산해서 재배치 해야 합니다. 

이 재배치를 위해 크기와 위치를 다시 계산하는 것을 Replow라고 이해하면 됩니다. <br>
영문 뜻 그대로 각 엘리먼트의 크기와 그에 따른 위치 등을 다시 계산해서 <u>레이아웃이 자연스럽게 흐르게 만든다(Reflow)</u>는 뜻입니다.

### 5) Repaint

리플로우가 끝나면 브라우저는 영향을 받은 부분을 깨끗한 붓으로 다시 그립니다. 이를 Repaint라고 합니다.
렌더링 트리에 대한 모든 변경이 리플로우를 일으키는 것은 아닙니다.
만약, 배경색만을 변경했다면 크기 위치 등이 바뀌지 않으므로 Repaint만 일어납니다.

## 2. Reflow와 Repaint 최소화

자, 리플로우와 리페인트의 원리를 알았다면 이제 그에 대한 해결책을 알아볼 시간입니다. 
<u>크롬 개발자 도구의 Performance 툴을 아주 잘 사용하는 사람들은 잘 알겠지만, Repaint보다는 리플로우가 더 빈번한 문제를 낳습니다.</u>
Repaint는 구형 익스플로러 등에서는 여전히 꽤 골치 아픈 문제이지만, 최근의 모던 브라우저에서는 이 차이가 꽤 줄었습니다. 
그래서, Repaint보다는 리플로우에 더 집중해서 설명해 보겠습니다. 

방법은 의외로 간단합니다. 리플로우가 왜 발생하는 지 잘 이해했다면, 해결책도 어렵지 않게 찾을 수 있습니다. 

{: .box-note}
1) 렌더 트리를 자주 바꾸지 말고, 가급적 모았다가 한 번에 처리하기

{: .box-note}
2) 렌더 트리 밖에서 변경 사항을 처리한 후, 렌더 트리에 한 번에 반영하기

꽤 쉽죠?


### 1) 렌더 트리 변경을 모았다가 한 번에 처리하기 

리플로우마다 다시 계산하려면 비용이 많이 들기 때문에 브라우저 대부분은 렌더 트리 변경을 큐에 모았다가 한 번에 처리하는 방식으로 리플로우를 최소화 합니다. 

브라우저 자체가 이미 리플로우에 대응하는 식으로 발전하고 있는 거죠.
하지만, 이 큐를 모두 비우고 즉시 리플로우를 실행하게 만드는 속성들이 있습니다. 

| offsetTop, offsetLeft, offsetWidth, offsetHeight |
| scrollTop, scrollLeft, scrollWidth, scrollHeight |
| clientTop, clientLeft, clientwidth, clientHeight |
| getComputedStyle() |

예를 들어, 어떤 HTML엘리먼트의 정확한 위치 값을 알기 위해 element.offsetTop을 요청하는 순간, 브라우저는 정확한 offsetTop 값을 반환하기 위해 모든 큐를 비우고, 즉시 엘리먼트의 크기와 위치값들을 재계산합니다. 리플로우가 일어나는 거죠.

element.offsetTop을 요청하는 것은 뼈다귀를 물고 있는 개에게 짖어보라고 요구하는 것과 마찬가지입니다. 짖으려면 일단 뼈다귀를 내려놔야죠. <br>
밥 먹는데 말 시키는 것과도 같습니다. 밥 먹는데 자꾸 말시키면 얹히기 마련입니다.  

루프문 내에 offsetTop 등의 값을 지속적으로 반복해서 구한다거나 하는 것은 최악입니다. 불가피하게 offsetTop 값을 구해야 한다면 이를 최소화 하고, 반복문 등에 사용해야 할 경우 루프 내에서 사용하지 말고, 루프 밖 변수에 저장해서 사용하는 것이 좋습니다.

```javascript
// 나쁜 예
const top = element.offsetTop; //말 시키기
element.style.top = top; //밥 먹기
const left = element.offsetLeft; //다시 대답해봐
element.style.left = left; //밥 먹기
const width = element.offsetLeft; //잘 안들려. 뭐라고?
element.style.width = width; //밥 먹기

// 개선
// 먼저 말시키고,
const top = element.offsetTop;
const left = element.offsetLeft;
const width = element.offsetLeft;
// 밥 먹기
element.style.top = top;
element.style.left = left;
element.style.width = width;
```

```javascript
// 나쁜 예
for (let i = 0; i < element.length; i++) {
  const height = node.offsetHeight; //밥 먹는데 끊임없이 말시키기
  element.style.height = height * 2;
}

// 개선
const height = node.offsetHeight;
for (let i = 0, j = element.length; i < j; i++) {  
  element[i].style.height = height * 2;
}
```

실제 위의 예처럼 코딩할 일은 없겠지만, 이해를 돕기 위해 간단한 예를 들어 보았습니다. 

### 2) 렌더 트리 밖에서 변경 사항을 처리하기

렌더 트리 밖에서 변경 사항을 처리하는 방법은 아래의 세가지 방법이 있습니다. 

{: .box-note}
1) 엘리먼트를 숨기고 변경한 후 다시 드러냅니다.

{: .box-note}
2) 현재 DOM 바깥에서 문서 조각을 만들어 변경한 후 문서에 복사합니다.

{: .box-note}
3) 현재 엘리먼트를 문서 밖의 노드에 복사해서 사본을 변경한 후 원래 엘리먼트를 대체합니다.


#### 가) 엘리먼트를 숨기고 변경한 후 다시 드러내는 방법

위에서 display css 속성이 none인 경우, 렌더링 트리에 나타나지 않는다고 말씀 드렸습니다. 

이 특징을 이용하는 것입니다. display 속성을 none으로 주면 렌더링 트리에서 빠지므로, 아무리 많은 변화를 주어도 리플로우가 일어나지 않습니다. 

display = none 상태에서 엘리먼트에 대한 수정 작업을 모두 마친 후, 다시 display속성을 block이나 inline 등으로 변경하면 리플로우를 획기적으로 줄일 수 있습니다.

```javascript
const ul = document.getElementById('myList');

ul.style.display = 'none';
appendDataElement(ul, data); //DOM 변경 의사 코드입니다.
ul.style.display = 'block';
```
<br>
#### 나) 현재 DOM 바깥에서 문서 조각을 만들어 변경한 후 문서에 복사해 넣는 방법

DOM에서 완전히 분리된 조각을 만들어 수정한 후 원래 목록에 추가해도 리플로우를 최소화 할 수 있습니다. 

Document.createDocumentFragment()를 활용해 이런 방법을 사용할 수 있습니다. 

createDocumentFragment는 DOM 노드입니다. 하지만, 메인 DOM 트리에는 속하지 않습니다. <br>
렌더링 트리에도 속하지 않으므로, 변화를 주어도 리플로우가 일어나지 않습니다. 

```javascript
const fragment = document.createDocumentFragment();
appendDataElement(fragment, data); //DOM 변경 의사 코드입니다.
document.getElementById('myList').appendChild(fragment);
```
<br>
#### 다) 현재 엘리먼트를 문서 밖의 노드에 복사해서 사본을 변경한 후 원래 엘리먼트를 대체하는 방법

마지막 방법은 수정하려는 엘리먼트를 복사해서 사본을 수정한 후, 원래 엘리먼트를 사본으로 대체하는 방법입니다. 

```javascript
const old = document.getElementById('myList');

const clone = old.cloneNode();
appendDataElement(clone, data); //DOM 변경 의사 코드입니다.
old.parentNode.replaceChild(clone, old);
```

> 위 3가지 방법 중 <u>'나) 현재 DOM 바깥에서 문서 조각을 만들어 변경한 후 문서에 복사해 넣는 방법'</u>이 리플로우가 가장 적게 발생합니다. 가능한 한 두 번째 방법을 사용할 것을 권합니다. 

## 3. Virtual DOM(가상돔)

개발자의 DOM 컨트롤에 대한 부담을 덜어줄 대안으로 Virtual DOM이 있습니다.

virtual DOM은 느린 DOM 컨트롤을 위해 도입된 개념입니다. 말 그대로, 실제 브라우저에서 읽어들이는 DOM 이외에 가상의 DOM을 하나 더 만드는 것입니다. 

virtual DOM의 작동 방식을 간단히 설명하면 아래와 같습니다. 

> 1. 데이터가 업데이트 되면, 전체 UI를 Virtual DOM에 리렌더링 합니다.<br><br>
> 2. 실제 브라우저의 DOM과 Virtual DOM에 있는 내용을 비교합니다.<br><br>
> 3. 바뀐 부분만 실제 DOM에 적용 합니다. 

가상의 DOM을 만들어 놓고, 실제 바뀐 부분만 지능적으로 컨트롤 하기 때문에 DOM 컨트롤 속도가 훨씬 빨라집니다.

요즘은 React나 Vue 등의 라이브러리나 프레임워크를 사용하다 보면 성능이 좋아진다는 느낌을 받을 때가 있습니다. VIrtual DOM 덕분입니다. 

하지만, Angular나 Ember 등의 프레임워크는 Virtual DOM을 도입하지 않았습니다. 

Angular와 Ember는 React와 Vue 등과는 달리 html을 별도의 템플릿으로 관리합니다. 같은 컴포넌트에 html 엘리먼트를 같이 렌더링하는 React 와 Vue 등의 프레임워크(라이브러리)와 달리 Viutaul DOM을 도입하기 어렵습니다. 그 대신, Angular는 9 버전부터 Incremental DOM(증가형 돔)이라는 것을 도입했습니다. 

이에 대해서는 다음 포스팅에서 상세히 다루도록 하겠습니다. 

긴 글 읽어주셔서 감사합니다.

