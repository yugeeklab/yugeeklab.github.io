---
published: true
title: (Chromium) Report input type time bug
date: '2021-10-19 13:00:00 +0900'
categories:
  - Chromium
  - Issue
tags:
  - chromium
  - issue
---
input type time 태그에 버그가 발견되어서 이를 보고했다.

## 어떤 버그인가?

`<input type="time" value="13:50:02.922">`은 기본적으로 분 단위로 값 변경이 가능하다. 따라서 시간 필드와 분 필드, 초 필드, 밀리초 필드가 있지만 초 필드와 밀리 초 필드는 비활성화된다.

하지만 옆에 있는 시계 버튼을 눌러 그래픽 편집기를 열면 초와 밀리초를 변경할 수 있게 된다. 이는 의도치 않은 동작이라고 생각했다.

## 버그 보고하기

버그를 보고하기 위해 간단하게 test file을 만들었다.

```
<!DOCTYPE html>
<html>
<body>
  <input type="time" value="13:50:02.922">
  <input type="time" value="13:50:02.922" step=1>
</body>
</html>
```

그리고 chromium 팀에서 제공하는 양식에 맞추어 글을 작성했다.

**What steps will reproduce the problem?**

(1) Set <input type="time" value="13:50:02.922">. And Default GetStepRange().Step() will be 1 minute.
(2) Second field and millisecond field will be disabled.
(3) Click the time picker button.

**What is the expected result?**

Second and Millisecond should be uneditable.

**What happens instead?**

Second and Millisecond are editable.

## Marked as Available

이렇게 버그를 보고하면 Chromium 관리자들이 내가 올린 버그를 자신의 컴퓨터에서 재현해본다. 그리고 그 버그가 확실하다고 여겨지면 Available 상태로 바꾼다.

내가 올린 버그가 Available 상태로 바뀌었다!
