---
layout: post
title: web storage 사용에 대하여
gh-repo: daattali/beautiful-jekyll
gh-badge: [star, fork, follow]
tags: [javascript memory, heap memory, javascript memory leak, 자바스크립트 메모리 누수, 메모리 관리]
comments: true
---

브라우저의 로컬스토리지와 세션스토리지는 굉장히 편리한 수단이죠. 

간단한 데이터를 브라우저에 저장하고 언제든 손쉽게 꺼내쓸 수 있습니다. <br>
복잡하고 장황한 코드도 필요 없습니다. 한 줄이면 쉽게 처리 가능하죠. 

아래처럼요. 

{: .box-note}
localStorage.setItem(storageName, JSON.stringify(storageData));
localStorage.getItem(storageName, JSON.stringify(storageData));

하지만, 저는 이 로컬스토리지 사용을 더 이상 권장하지 않습니다. 구글 개발팀도 더 이상 사용을 권장하지 않는군요. 
