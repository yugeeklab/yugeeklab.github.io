---
title: "(Chromium) Fix accessibility bug for date_time_field"
date: 2021-10-05 15:44:00 +0900
categories: [Chromium, Issue]
tags: [chromium, issue]
---

크로미움에서 해결한 두번째 이슈다.
Accessibility란 장애를 가지고 있는 사람들도 장애를 가지고 있지 않은 사람들과 동일한 경험을 할 수 있도록 도와주는 기능을 뜻한다.
이번에 해결한 issue는 Accessibility에 관련된 이슈다.

이 이슈를 해결하기 위해서 많은 사전 지식이 필요했다. 내가 알고 있는 지식은 해당 issue를 해결할 수 있을 정도의 깊이이니 참고하길 바란다.

# Issue 설명

## psudo-element

## aria attribute

> **접근가능한 리치 인터넷 어플리케이션**(Accessible Rich Internet Applications, **ARIA**)은 장애를 가진 사용자가 웹 콘텐츠와 웹 어플리케이션(특히 JavaScript를 사용하여 개발한 경우)에 더 쉽게 접근할 수 있는 방법을 정의하는 여러 특성을 말합니다.

aria attiribute를 사용함으로써 accessibilty를 구현하는데 도움을 주는 듯하다.

## 무엇이 문제일까?

html element 중 date_time_field가 있다. 이 date_time_field의 spin button이 disabled 되었을 때 aria-disabled도 같이 되어야한다. 하지만 disabled만 되고 aria-disabled는 적용되지 않았다.

# 어디에 Bug가 살고 있을까

이번 이슈는 친절하게 어디를 고쳐야하는지 알려주었다.

`third_party/blink/renderer/core/html/forms/date_time_field_element.cc`를 살펴보자.

# 이슈 해결하기

이슈 해결도 비교적 간단했다.

```
void DateTimeFieldElement::SetDisabled(){}
```

위와 같은 함수에 aria disabled를 설정해주는 line을 추가해주자.

# 테스트 파일 추가

앞의 과정은 무난했으나 여기서부터가 고난이었다.

## 어디에 테스트 파일을 추가해야하지?

테스트 파일은 무수히 많다. 그 중 어느 파일에 테스트 파일을 추가해야할까?

# Merge 완료

이렇게 Merge가 완료되었다!
