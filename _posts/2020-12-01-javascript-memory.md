---
layout: post
title: javascript 메모리 관리
gh-repo: daattali/beautiful-jekyll
gh-badge: [star, fork, follow]
tags: [javascript memory, heap memory, javascript memory leak, 자바스크립트 메모리 누수, 메모리 관리]
comments: true
---

자료구조와 알고리즘을 공부하기 위해 C 언어를 공부한 적이 있는데요. C에서는 개발자가 직접 메모리를 할당하고 삭제해야 했습니다. 

C에서의 메모리 구조는 대략 아래와 같습니다. 

{: .box-note}
** C 메모리 구조**<br><br>
1) 코드 영역: 프로그램의 코드가 저장되는 공간<br><br>
2) 데이터 영역: 전역변수, static으로 선언되는 변수 저장 공간<br><br>
3) 스택(Stack) 영역: 지역변수와 매개변수 저장 공간<br><br>
4) 힙(Heap) 영역: 프로그래머가 원하는 시점에 변수를 할당하고 소멸시킬 수 있음. 프로그래머가 직접 메모리를 할당하는 변수의 저장 공간

C는 malloc 메서드로 메모리를 할당하고, free 함수로 메모리를 제거해 주어야 합니다. 
개발자가 메모리를 직접 관리해 줘야 하죠.

위 메모리 구조는 다른 언어도 대동소이합니다. javascript도 마찬가지죠. 하지만, 힙 메모리 영역은 다릅니다. Java와 같은 고수준 언어는 메모리를 할당하고 제거하는 일을 일일이 할 필요가 없습니다. 필요한 메모리를 자동으로 할당하고 