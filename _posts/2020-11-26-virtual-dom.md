---
layout: post
title: 그림자 돔과 가상돔, 증가형 돔
subtitle: Shadow DOM, Virtual DOM and Incremental DOM
gh-repo: daattali/beautiful-jekyll
gh-badge: [star, fork, follow]
tags: [DOM, Shadow DOM, Virtual DOM, Incremental DOM, 가상 돔, 증가형 돔, 그림자 돔]
comments: true
---

DOM은 이미 말씀드렸듯이, 태생적으로 느립니다. 브라우저는 DOM을 렌더링하는 엔진과 자바스크립트를 렌더링하는 엔진이 분리되어 있습니다. 

자바스크립트가 DOM을 컨트롤하려면 정말 먼 길을 돌아가야 하죠.

DOM 컨트롤 비용을 줄이는 것은 프론트엔드 개발자들의 주된 관심사였습니다(물론, 아예 관심없는 개발자들이 더 많긴 합니다).

하지만, 자바스크립트가 발전하고, React나 Angular와 같은 프레임워크나 라이브러리가 등장하면서 DOM컨트럴에도 획기적인 발전이 있었습니다. 

그 중 하나가 가상돔(Virtual DOM)입니다.

