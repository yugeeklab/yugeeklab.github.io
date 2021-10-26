---
title: "(Chromium) CodeHealth : Remove use of ListValue::GetDictionary()"
date: 2021-10-26T07:36:19.834Z
categories:
  - chromium
  - issue
tags:
  - Chromium
  - Issue
math: false
---
## Code Health Issue 란

Code Health 이슈는 가장 접근하기 쉬운 이슈이다.
오래된 함수를 새 함수로 교체하는 작업을 해준다.

나는 ListValue::Get() 함수를 Value::GetList()\[] 함수로 바꾸는 작업을 해주었다.

## OOB란?

이 작업을 하고 리뷰를 받는 중에 Reviewer가 물었다.

> "Just confirming, this CHECKS on range OOB, right?"

Out Of Bound의 준말이다. Out Of Bound 체크를 안할 경우 잘못된 메모리에 접근하더라도 프로그램이 종료되지 않는다.

나는 chromium의 소스들을 몇 시간 동안 탐색한 뒤에 답할 수 있었다.

> Actually, I'm not sure. But I can guess it performs bounds CHECKs according to the last paragraph of "base/containers/checked_range.h"

```
In "base/containers/checked_range.h"
// CheckedContiguousRange is a light-weight wrapper around a container modeling
// the ContiguousContainer requirement \[1, 2]. Effectively this means that the
// container stores its elements contiguous in memory. Furthermore, it is
// expected that base::data(container) and base::size(container) are valid
// expressions, and that data() + idx is dereferenceable for all idx in the
// range \[0, size()). In the standard library this includes the containers
// std::string, std::vector and std::array, but other containers like
// std::initializer_list and C arrays are supported as well.
//
// In general this class is in nature quite similar to base::span, and its API
// is inspired by it. Similarly to base::span (and other view-like containers
// such as base::StringPiece) callers are encouraged to pass checked ranges by
// value.
//
// However, one important difference is that this class stores a pointer to the
// underlying container (as opposed to just storing its data() and size()), and
// thus is able to deal with changes to the container, such as removing or
// adding elements.
//
// Note however that this class still does not extend the life-time of the
// underlying container, and thus callers need to make sure that the container
// outlives the view to avoid dangling pointers and references.
//
// Lastly, this class leverages base::CheckedContiguousIterator to perform
// bounds CHECKs, causing program termination when e.g. dereferencing the end
// iterator.
//
// [1] <https://en.cppreference.com/w/cpp/named_req/ContiguousContainer>
// [2]
// <https://eel.is/c++draft/container.requirements.general#def:contiguous_container>
```



> And I can find there's also death test in "checked_range_unitttest.cc"
```
TEST(CheckedContiguousRange, OutOfBoundsDeath) {

std::vector<int> empty_vector; CheckedContiguousRange<std::vector<int>> empty_range(empty_vector); ASSERT_DEATH_IF_SUPPORTED(empty_range\[0], ""); ASSERT_DEATH_IF_SUPPORTED(empty_range.front(), ""); ASSERT_DEATH_IF_SUPPORTED(empty_range.back(), ""); static constexpr int array\[] = {0, 1, 2}; constexpr CheckedContiguousRange<const int\[3]> range(array); ASSERT_DEATH_IF_SUPPORTED(range\[3], ""); }
```

리뷰가 잘 끝났고 Merge가 완료되었다.