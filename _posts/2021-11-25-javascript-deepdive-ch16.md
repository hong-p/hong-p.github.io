---
title: "모던 자바스크립트 딥 다이브 16장 프로퍼티 어트리뷰트"
excerpt: "[javascript] 모던 자바스크립트 Deep Dive 16장 프로퍼티 어트리뷰트"
categories:
 - Javascript
tags:
 - Javascript
toc: true
toc_sticky: true
last_modified_at: 2021-11-25
---

# 프로퍼티 어트리뷰트

## 1.내부 슬롯과 내부 메서드
내부 슬롯과 내부 메서드는 자바스크립트 엔진의 구현 알고리즘을 설명하기 위해 ECMAScript 사양에서 사용하는 의사 프로퍼티`pseudo property`와 의사 메서드`pseudo method`다.  
ECAMScript 사양에 등장하는 이중 대괄호 `[[...]]`로 감싼 이름들이 내부 슬롯과 내부 메서드다.

