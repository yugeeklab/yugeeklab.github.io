---
title: (Chromium) Test 
date: 2021-09-19 23:11:00 +0900
categories: [Chromium]
tags: [chromium]
---



Chromium의 이슈를 지금까지 3개 해결했다. 그리고 3개의 이슈 모두 테스트 파일을 작성했다. 이 글에서는 Chromium의 이슈를 해결하면서 경험한 Test 파일 작성에 대해 쓰겠다.



# Test 파일을 언제 작성할까?

패치를 올렸는데 리뷰어가 테스트 파일도 같이 올려달라고 한다. Test 파일을 언제 작성하는 건가?
: 기능을 추가했거나 원래 있던 기능을 변경했을 때 테스트 파일을 수정하거나 새로 작성한다. 혹시 테스트 파일을 작성해야할지 말지 모르겠다면 일단 패치를 올리고 리뷰를 받아보자. 그럼 테스트 파일이 필요한 패치라면 테스트 파일을 추가해달라고 할 것이다.(물론 처음부터 작성해주면 좋다.)

# 어떤 Test 파일을 수정해야하나?

그래 Test 파일을 수정해야 한다는 것은 알겠다. 그럼 어떤 테스트 파일을 수정해야하나? 테스트 파일이 너무 많은데?
: 솔직히 잘 모르겠다. 나같은 경우 크로미엄 소스코드를 볼 수 있는 사이트를 접속해서 변경한 함수를 검색해보거나, 이전에 해결된 패치 중 비슷한 부분을 수정한 패치를 검색해본다. 그럼 대략적으로 무엇을 수정해야할지 알 수 있다. 그래도 어떤 테스트 파일을 작성해야할지 모르겠다면 리뷰어에게 물어보자. 

# 어떻게 Test 파일을 수정할까?

어떤 Test 파일을 수정할지 또는 추가할지 정했다면 어떻게 수정할지를 생각해보자. Test 파일은 종류가 많다.

## Unit Tests

먼저 유닛 테스트다. 모든 Unit Tests가 C++로 작성되는지는 모르겠지만 내가 경험한 것은 C++로 작성되었다. [gtest](https://github.com/google/googletest) 프레임워크를 사용한다. 앞에 g가 붙은 것으로 봐서 Google이 만든 것 같다.

### 빌드하기

`autoninja -C out/Default blink_unittests`

### 실행하기

`out/Default/blink_unittests --gtest_filter='Foo*'`

### 작성법

이 [글](https://github.com/google/googletest/blob/master/docs/primer.md#simple-tests)에 잘 설명되어 있다.

## Web Tests

다음 [링크](https://chromium.googlesource.com/chromium/src/+/refs/heads/main/docs/testing/web_tests.md)를 참조하세요.

### 빌드하기

`autoninja -C out/Default blink_tests`

### 실행하기

`third_party/blink/tools/run_web_tests.py -t Default`

### 초기 expected 파일 생성하기



### 작성법

Web Tests의 경우 종류가 많다. 보다 자세한 내용은 [여기서](https://source.chromium.org/chromium/chromium/src/+/main:docs/testing/writing_web_tests.md) 확인이 가능하다.

#### JavaScript Tests

js로 된 테스트 파일을 수정하거나 작성하려고 하면 아래 두가지 타입을 발견하게 될 것이다.

##### Legacy js-test

js-test는 아래와 같은 script를 import하는 것으로 시작한다.

<script src="../resources/js-test.js"></script>

하지만 js-test는 오래된 것이다. 나같은 경우 리뷰어가 js-test 파일을 아래에서 설명할 test harness로 바꿔주면 고맙겠다고 말했다.

##### Test Harness

test harness는 아래와 같은 script를 import하는 것으로 시작한다.

<script src="../resources/testharness.js"></script>
<script src="../resources/testharnessreport.js"></script>

그리고 아래와 같이 3개 타입의 테스트를 작성할 수 있다.

<script>
// Synchronous test example.
test(() => {
    const value = true;
    assert_true(value, 'true literal');  
    assert_equals(value.toString(), 'true', 'the string representation of true'); }, 'The literal true in a synchronous test case');
// Asynchronous test example.
async_test(t => {
    const originallyTrue = true;
    setTimeout(t.step_func_done(() => {
        assert_equals(originallyTrue, true);
    }), 0); }, 'The literal true in a setTimeout callback');
// Promise test example.
promise_test(() => {
    return new Promise((resolve, reject) => {
        resolve(true);
    }).then(value => {
        assert_true(value);
    }); }, 'The literal true used to resolve a Promise');
</script>

#### Reference Tests

html 파일과 html 파일을 픽셀 단위로 비교한다.

#### Pixel Tests

html 파일이 생성한 픽셀을 png 파일과 비교한다. Pixel Tests는 플랫폼별로 달라질 수 있기에 플랫폼 별로 png파일을 생성할 필요가 있다.

#### Text Tests

Text Tests도 세가지 종류가 있다.

Layout tree test
: Layout tree를 검사한다. Layout tree test의 경우 플랫폼 별로 결과가 달라질 수 있기에 플랫폼 별로 *-expected.txt 파일을 생성해야한다.

Text dump test
: 메인 프레임의 text contents를 검사한다.

Markup dump test
: 메인 프레임의 Dom을 검사한다. 

#### Audio Tests

*-expected.wav 파일과 생성된 오디오를 비교힌다.









