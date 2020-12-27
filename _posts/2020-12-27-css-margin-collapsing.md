---
title: CSS 마진 겹칩 현상(Collapsing Margins)
date: 2020-12-27 22:49:00 +0900
categories:
  - CSS
links:
  - name: Box model | W3C Recommendation
    url: https://www.w3.org/TR/CSS2/box.html#collapsing-margins
  - name: Mastering margin collapsing | MDN
    url: https://developer.mozilla.org/ko/docs/Web/CSS/CSS_Box_Model/Mastering_margin_collapsing
  - name: CSS 마진 상쇄(Margin-collapsing) 원리 완벽 이해 | raram2 - velog
    url: https://velog.io/@raram2/CSS-%EB%A7%88%EC%A7%84-%EC%83%81%EC%87%84Margin-collapsing-%EC%9B%90%EB%A6%AC-%EC%99%84%EB%B2%BD-%EC%9D%B4%ED%95%B4
---
## 마진 겹칩(마진 상쇄)

마진 겹칩(혹은 마진 상쇄)은 인접한 block level element의 margin이 서로 겹쳐서 상쇄되는 현상을 나타낸다. 서로 겹치는 block level element의 margin 중 큰 margin만 남게 된다. margin이 같을 경우는 하나의 margin만 남게 된다.

```css
.top-container {
  margin-bottom: 20px;
}
.bottom-container {
  margin-top: 10px;
}
```

on code | on browser |
--- | --- |
.top-container | .top-container|
20px(`margin-bottom`)<br/>10px(`margin-top`) | 20px|
.bottom-container | .bottom-container|

## 발생 Case 3

### 1. 인접한 elements

* 위의 예시처럼 .top-conatiner와 .bottom-container가 인접한 경우

### 2. 부모 element와 자식 element 사이가 분리되지 않는 경우

* 두 element 사이에 다른 element가 있지 않거나 `padding`, `border`로 margin이 분리되지 않는 경우

### 3. Empty element(height가 없는 경우)

* 해당 block element의 `margin-top`과 `margin-bottom`이 서로 상쇄된다.

3가지 케이스 모두 서로 다른 margin이 붙어있는 경우에 발생한다.

## 발생 예외 Case

* element가 block level element가 아닌 경우
* element가 `float: left/right` 속성을 가진 경우
* element가 `position: absolute` 속성을 가진 경우
