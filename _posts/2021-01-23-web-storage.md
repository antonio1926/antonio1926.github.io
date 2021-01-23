---
layout: post
title: web storage 사용에 대하여
gh-repo: daattali/beautiful-jekyll
gh-badge: [star, fork, follow]
tags: [web storage, service worker, indexedDB, cache API, cache PWA, Progressive Web App]
comments: true
---

브라우저의 로컬스토리지와 세션스토리지는 굉장히 편리한 수단이죠. 간단한 데이터를 브라우저에 저장하고 언제든 손쉽게 꺼내쓸 수 있습니다. <br>
복잡하고 장황한 코드도 필요 없습니다. 한 줄이면 쉽게 처리 가능하죠. 

아래처럼요. 

```javascript
localStorage.setItem(storageName, JSON.stringify(storageData));
localStorage.getItem(storageName, JSON.stringify(storageData));
```

하지만, 구글 개발팀은 더 이상 사용을 권장하지 않는군요. 

구글은 로컬스토리지보다는 캐시API나 IndexedDB를 사용할 것을 권장하고 있습니다. 

물론, 구글과 모질라가 서비스워커를 주도적으로 개발했고, 캐시API는 서비스워커가 제공하는 기능이므로 구글이 캐시API 사용을 권장하는 것은 이해됩니다. 

캐시API는 사용이 어렵지도 않습니다. 하지만, 로컬스토리지만큼 사용이 편리하지는 않습니다. 간단한 데이터를 저장한다면 여전히 로컬스토리지가 훨씬 강점이 있습니다. 

다만, 조금 더 복잡하고 용량이 큰 데이터라면 캐시API나 IndexedDB를 사용하는 것이 낫습니다. 저 역시 최신 프로젝트에서 캐시API와 IndexedDB 사용을 고려하고 있습니다. 

지도 서비스와 같은 대용량 이미지 뷰어를 제작해야 하는데요. 구글의 지도 서비스와 같은 맥락이라고 보시면 될 것 같습니다. 

대용량의 이미지이지만 사용자는 이 대용량의 이미지를 매우 빠르게 로드하고 드래그 및 확대/축소해 보길 원합니다. <br>
답은 이미지를 아주 작게 조각내서 브라우저 캐시에 저장하는 것입니다. 아래 그림과 같이 말이죠.



그리고, 사용자가 이미지를 드래그 및 확대/축소할 때 서버로의 요청을 최대한 자제하고 캐시에 미리 저장된 이미지를 브라우저에 로드하는 식으로 제작하는 방법을 고려하고 있습니다.

물론, 대용량 이미지를 잘게 조각낼 경우 

#### 참조

> 1) [https://web.dev/storage-for-the-web/](Storage for the web)<br>
> 2) 