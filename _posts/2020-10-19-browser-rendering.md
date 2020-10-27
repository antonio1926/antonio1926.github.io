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

   - 렌더링 트리에서 레이아웃을 실행하여 **각 노드의 기하학적 형태를 계산 (Reflow)**합니다.

   - 개별 노드를 **화면에 페인트 (Repaint)**합니다.

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


## 2. Reflow와 Repaint
