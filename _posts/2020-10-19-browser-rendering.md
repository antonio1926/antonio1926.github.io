---
layout: post
title: 브라우저 렌더링과 성능 최적화
subtitle: "성능 최적화를 위한 기본 1"
gh-repo: daattali/beautiful-jekyll
gh-badge: [star, fork, follow]
tags: [html5 canvas]
comments: true
---

브라우저에서 사용자가 원하는 화면을 보여주기까지는 많은 작업을 수행해야 합니다. 이러한 작업 대부분은 개발자에게 숨겨져 있습니다. 
그렇다면, 브라우저가 HTML과 CSS 등을 통해 화면에 렌더링된 픽셀로 변환하는 과정은 어떻게 될까요?

성능을 최적화 하려면 이러한 과정을 주의깊에 들여다 볼 필요가 있습니다. HTML, CSS 및 자바스크립트 바이트를 수신한 후 렌더링된 픽셀로 변환하기 위해 필요한 처리 과정을 **'주요 렌더링 경로'**라고 합니다.

주요 렌더링 경로를 잘 알고 최적화 할 수 있다면 브라우저 렌더링 성능을 향상시킬 수 있습니다. 

하지만, 이 것만으로는 충분치 않습니다. 사용자가 화면을 조작할 때마다 이벤트가 발생하고, 화면에도 변화가 일어납니다. 
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

4. 렌더링 트리에서 레이아웃을 실행하여 **각 노드의 기하학적 형태를 계산 (Reflow)**합니다.

5. 개별 노드를 **화면에 페인트 (Repaint)**합니다.

자, 그럼 1번부터 차근차근 짚어가 봅시다. 


### 1) HTML 마크업을 처리하고 **DOM 트리**를 빌드

DOM은 HTML문서(와 XML 문서)에서 동작하는 언어 독립적인 API입니다.

브라우저는 먼저, HTML마크업의 여러 태그 간의 관계를 정의하는 하나의 문서 모델을 형성합니다. 
이것을 DOM(Document Object Model)이라고 합니다. 

아래 그림과 같이 HTML Object 밑에 Body Object가 위치하는 식의 트리 구조로 만들어진 것이죠. 그래서 DOM 트리라고도 합니다.

브라우저는 이후 모든 페이지 처리에 이 DOM을 이용합니다.

![DOM](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/images/dom-tree.png?hl=ko)

{: .box-note}
**Tip**. <br>
DOM은 태생적으로 느립니다. <br><br>
브라우저는 DOM과 Javascript를 렌더링하는 엔진이 별도로 있습니다. <br>
가장 많이 사용되는 크롬의 경우 크로미움 엔진을 사용하는데, 크로미움은 V8이라는 자바스크립트 엔진과 Blink라는 렌더링 엔진을 사용하는 브라우저입니다.<br>
DOM은 Blink가 렌더링하고, javascript는 v8 엔진이 렌더링합니다. <br><br>
이것은 성능에 중요한 의미가 있습니다. DOM이 하나의 대륙이고 javascript(ECMAScript)가 다른 대륙인데, 이 두 대륙을 긴 다리로 연결했다고 생각하시면 됩니다. 
두 다리를 건너려면 성능이라는 통행료를 내야 합니다.<br><br>
이런 이유로, 최상의 성능을 유지하려면, DOM을 변화시키는 코드를 최소화 해야 합니다. <br>
가령, for문을 돌리면서 많은 양의 DOM을 변화시키거나 하는 건 최악입니다.<br> 
여러개의 DOM을 컨트럴해야 한다면, DOM 관련 변동 사항을 변수 등에 저장했다가 한 번에 변화시키는 것이 최선입니다.

### 2) CSS 마크업을 처리하고 **CSSOM 트리**를 빌드

DOM 트리를 생성한 후, CSS 정보를 통해 CSSOM 트리를 생성합니다. 
아래 이미지처럼 말이죠.

![CSSOM](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/images/cssom-tree.png?hl=ko)


### 3) DOM 및 CSSOM을 결합하여 **렌더링 트리**를 형성

DOM 트리만으로는 화면을 그릴 수 없습니다. 
BODY, P, DIV, IMG 등의 각 요소의 위치와 크기, 바탕색, 선 굵기 등을 알아야 각자의 위치에 알맞게 아름다운 화면을 만들 수 있습니다.

이 구실을 하는 것이 <strong><u>렌더링 트리</u></strong>입니다. 렌더링 트리는 DOM 트리와 CSSOM 트리가 결합해서 만들어집니다.

렌더링 트리는 화면에 표시되는 각 요소의 위치를 계산하고, 각자의 위치에  화면에 색칠을 하는 등등의 구실을 합니다. <u>렌더링 트리가 만들어지면 브라우저는 페이지 요소를 화면에 표시(페인트)</u>할 수 있습니다.

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

렌더링 트리는 화면에 페인트하기 위해서 각 노드 요소의 기하학적 구조(넓이와 높이)와 위치를 계산합니다. 
요소의 테두리 두께를 바꾸거나, 문단에 텍스트를 추가해서 줄이 늘어나는 등의 변화가 생기면 요소의 기하학적 구조와 위치도 다시 계산해야 합니다.
이를 Reflow라고 합니다. 

![Replow](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/images/layout-viewport.png?hl=ko)

아래와 같은 퍼즐을 생각하면 이해가 쉽습니다. 
수많은 퍼즐의 각 면이 아주 잘 짜맞춰져 하나의 아름다운 그림을 만들어냅니다. 브라우저의 화면도 이와 마찬가지입니다. 

![Puzzle](/img/20201019/puzzle.jpg)

 
그런데, 만약 퍼즐 조각 중 하나의 크기가 지금보다 두 배 더 커진다고 가정해 봅시다. 
여러 요소가 맞물려 있는데, 그 중 하나만 크기가 커지면 틀이 망가지게 됩니다. 이때 그 한 개를 제외한 나머지 모든 퍼즐의 높이와 넓이, 위치를 모두 다시 계산해서 재배치 해야 합니다. 이 때 이 재배치를 위해 기하학적 구조와 위치를 다시 계산하는 것을 Replow라고 이해하면 됩니다. 
영문 뜻 그대로 각 요소의 높이와 넓이, 그에 따른 위치 등을 다시 계산해서 <u>레이아웃이 자연스럽게 흐르게 만든다(Reflow)</u>는 뜻입니다.

### 5) Repaint

Reflow가 끝나면 브라우저는 영향을 받은 부분을 깨끗한 붓으로 다시 그립니다. 이를 Repaint라고 합니다.
렌더링 트리에 대한 모든 변경이 Reflow를 일으키는 것은 아닙니다.
만약, 배경색만을 변경했다면 넓이와 높이, 위치 등이 바뀌지 않으므로 Repaint만 일어납니다.

## 2. Reflow와 Repaint 최소화

자, Reflow와 Repaint의 원리를 알았다면 이제 그에 대한 해결책을 알아볼 시간입니다. 
크롬 개발자 도구의 Performance 툴을 아주 잘 사용하는 사람들은 잘 알겠지만, Repaint보다는 Reflow가 더 빈번한 문제를 낳습니다. 
Repaint는 구형 익스플로러 등에서는 여전히 꽤 골치 아픈 문제이지만, 최근의 모던 브라우저에서는 이 차이가 꽤 줄었습니다. 
그래서, Repaint보다는 Reflow에 더 집중해서 설명해 보겠습니다. 

방법은 의외로 간단합니다. Reflow가 왜 발생하는 지 잘 이해했다면, 해결책도 어렵지 않게 찾을 수 있습니다. 

{: .box-note}
1) 렌더 트리를 자주 바꾸지 말고, 가급적 모았다가 한 번에 처리하기

{: .box-note}
2) 렌더 트리 밖에서 변경 사항을 처리한 후, 렌더 트리에 한 번에 반영하기

꽤 쉽죠?


### 1) 렌더 트리 변경을 모았다가 한 번에 처리하기 

Reflow마다 다시 계산하려면 비용이 많이 들기 때문에 브라우저 대부분은 렌더 트리 변경을 큐에 모았다가 한 번에 처리하는 방식으로 리플로우를 최소화 합니다. 
브라우저 자체가 이미 리플로우에 대응하는 식으로 발전하고 있는 거죠.
하지만, 이 큐를 모두 비우고 즉시 리플로우를 실행하게 만드는 속성들이 있습니다. 

| offsetTop, offsetLeft, offsetWidth, offsetHeight |
| scrollTop, scrollLeft, scrollWidth, scrollHeight |
| clientTop, clientLeft, clientwidth, clientHeight |
| getComputedStyle() |

예를 들어, 어떤 HTML엘리먼트의 정확한 위치 값을 알기 위해 element.offsetTop을 호출하는 순간, 브라우저는 정확한 offsetTop 값을 반환하기 위해 모든 큐를 비우고, 즉시 엘리먼트의 크기와 위치값들을 재계산합니다. 리플로우가 일어나는 거죠.

루프문 내에 offsetTop 등의 값을 지속적으로 반복해서 구한다거나 하는 것은 최악입니다. 불가피하게 offsetTop 값을 구해야 한다면 이를 최소화 하고, 반복문 등에 사용해야 할 경우 루프 내에서 사용하지 말고, 루프 밖 변수에 저장해서 사용하는 것이 좋습니다.


### 2) 렌더 트리 밖에서 변경 사항을 처리하기






