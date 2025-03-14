---
title: 접근성을 위한 미디어 쿼리 사용하기
slug: Web/CSS/Media_Queries/Using_Media_Queries_for_Accessibility
---
**미디어쿼리(Media Queries)**는 장애를 가진 사용자가 웹사이트를 더 쉽게 이해할 수 있도록 도울 수 있습니다.

## 애니메이션 동작 축소하기(Reduced Motion)

깜빡이거나 반짝이는 애니메이션은 주의력결핍 과잉행동증후군(ADHD)와 같은 인지 문제를 가진 사람들에게 문제가 될 수 있습니다. 이러한 종류의 애니메이션 동작들은 전정편두통, 뇌전증, 편두통과 광과민증을 유발할 수 있습니다.

또한, 이 애니메이션 동작을 줄이는 방법을 통해 배터리가 부족한 사람이나 보급형 스마트폰 및 컴퓨터를 사용하는 사람들에게 도음을 줄 수 있습니다.

### 문법

- `no-preference`
  - : 사용자가 시스템에 관련된 설정을 하지 않았다는 것을 의미합니다.
- `reduce`
  - : 사용자가 시스템에 애니메이션 동작을 최소화하도록 설정하였다는 것을 의미합니다. 가급적이면 필수적이지 않은 동작은 모두 제거됩니다.

### 예제

이 예제는 접근성 설정에서 동작 줄이기를 설정하지 않으면 난잡한 애니메이션이 실행됩니다.

#### HTML

```html
<div class="animation">animated box</div>
```

#### CSS

```css
.animation {
  -webkit-animation: vibrate 0.3s linear infinite both;
  animation: vibrate 0.3s linear infinite both;
}

@media (prefers-reduced-motion: reduce) {
  .animation {
    animation: none;
  }
}
```

## 고대비 모드 (High Contrast Mode)

{{CSSRef}}{{Non-standard_header}}

**-ms-high-contrast** [CSS media 속성](/ko/docs/Web/CSS)은 애플리케이션이 고대비 모드인지, 어떤 색상으로 강조되어 있는지를 정의하는 [Microsoft 확장기능](/ko/docs/Web/CSS/Microsoft_extensions)입니다.

저시력 사용자나 대비감도에 문제가 있는 사람뿐만 아니라 직사광선 아래에서 컴퓨터나 휴대폰을 사용하는 사람에게도 도움이 됩니다.

### 문법

**`-ms-high-contrast`** 미디어 속성(Media Feature)은 다음 값 중 하나를 지정하여 사용할 수 있습니다.

### 속성값

- `active`
  - : 시스템이 색상과 관계없이 고대비 모드로 설정되었을 때 후속 스타일링 규칙이 적용됨을 나타냅니다.
- `black-on-white`
  - : 시스템이 흰 바탕에 검은 글씨 고대비 모드로 설정되었을 때 후속 스타일링 규칙이 적용됨을 나타냅니다.
- `white-on-black`
  - : 시스템이 검은 바탕에 흰 글씨 고대비 모드로 설정되었을 때 후속 스타일링 규칙이 적용됨을 나타냅니다.

### 예제

다음 선언된 스타일들은 어플리케이션이 모든 종류의 색상, black-on-white 색상, white-on-black 색상의 고대비 모드로 표시되도록 합니다.

```css
@media screen and (-ms-high-contrast: active) {
  /* 모든 고대비 모드에 대한 스타일 규칙입니다 */
}
@media screen and (-ms-high-contrast: black-on-white) {
  div { background-image: url('image-bw.png'); }
}
@media screen and (-ms-high-contrast: white-on-black) {
  div { background-image: url('image-wb.png'); }
}
```
