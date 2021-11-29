---
title: (Chromium) Fix a bug that marker pseudo-element is adding extra margin-right
date: 2021-11-29T07:20:59.171Z
categories:
  - Chromium
  - Issue
tags:
  - chromium
  - issue
math: true
---
# 서론

이슈를 찾아 여행하던 중..굉장히 해결하기 쉬울 것 같은 이슈를 잡았다.(하지만 그것은 착각이었다;;)

https://bugs.chromium.org/p/chromium/issues/detail?id=1241974

굉장히 쉽게 해결할 수 있을줄 알았지만...

# 본론

## 설명

다른 브라우저들과 달리 list marker의 margin이 chromium에서는 너무 길게 할당되어 이를 짧게 수정하였다.

무슨 뜻인지 잘 모를 것이다. 사진으로 확인해보자.

![chromium-margin-before](/assets/img/uploads/chromium-margin-before.jpg "chromium-margin-before")

위의 사진이 before의 사진이다.

![chromium-margin-after](/assets/img/uploads/chromium-margin-after.jpg "chromium-margin-after")

위의 사진이 수정을 한 후 after 사진이다.

차이를 못느끼겠는가? 물론 그렇게 느낄 수 있다. 하지만 약간의 margin이 after에서는 없어진 것을 볼 수 있다.

이런 것을 왜 수정해야하는가?

차이는

list-style-position: inside

옵션을 주었을 때 발생한다. 수정 전에는 text의 끝이 일치하지 않는다. 아래 그림과 같이 말이다.

![bug](https://00f74ba44b382116ded03cc4bd57a6ee29556fb053-apidata.googleusercontent.com/download/storage/v1/b/monorail-prod.appspot.com/o/16%2Fattachments%2Fa3dfa6f2-501e-4378-8682-e31cf56a27e9?jk=AFshE3Vr8if15QnaLf7ddJRX2C9VGXotg6xGVN5V2z3g8b4DnA6i0f5vNLcFHrHGO_jJSxcGlWhtsX2ROwtr_ryAOseUM8h0nATmmWLAEBl1G0ldDmmkVJqDlS0QuCYW4rV8Fz-3hypWxz_CZwgSYqgnfAsy3-a9yvuMLKHvlQurs5lwct0QeuIvZ05l6M1Z14ZnCUYk-gfbGvHttvrCf7CSQUkHtJMju3va1eW7alXG-vExVwu7K8bPAt5jBtSFS00KybJ3E7Zm1VwF3z4m1hXeB2Xb_N_1HxTKm3mkWivObKZIR_DKBNcSzOw3qIU4SAiIA9de5YZGhS2lyc7bXAowrF12pjcQcWNwEAgO25FIWKQPdpXDgpJ3E3Bja4v_ft9z-k6Rfml4Awf7VfpmZLPzhVacD9awhiSVU6T1nUzla30NmJEc30kDg6l4wCeUVTucYdf_bl_n1LqS9Vp_RaNfs0SYR86qlbKSVJjI6-yQY944IE_DkvuzyTIAtrf_fp1IaEt9Jy7aVzzullhMXi60BthIYs0caiena8T6vZbJ41kxf3m1EuNlYjAb0kkcdAb8Fr9IbxRKJXiqmsBHoy97lSbjPKjRLsYDwVq_flr0uyDQWCeo4GTbITbw2bwfV4SupE2wy9rbX97l_NI3JJNnZKarSOSWKQ_Q3FZomNPPO3808Z7Ihrro-CuKKWNKHuaAjKosrMDg2RuV8zVOWTJjGfRgvkUkai3kI6iDqM2BLyOsd9Opsztpr9YZIUB099FmI7jkV_nJhEBQ9yQNvd0jzi3Y_BI5siIgg354CbVCLkDvEpHV5_6ihrQf8c8cmH9uZji-49UsUSYOep4SS4weUiJaRfA_OeMRTm7K7zlgJjO5ksPZNcAihv2j367hK2nnjJXOj9zo2M61c2ZfBa4pYOQr1vN6W83Zj2bXR7Y8hH4Wg8PTxd5gPz15qZXJDeufG_tSwF5jfl9rU-2mDhsD-aGsq2k_t9wd8RfEwjlgglpgBrJQwrKHA1pxOiIZeJsKDfNNUbJrlcZ00LFU3eP506oiTBhgeynnkSqq4qNLoAYPxOKcrxu9bmY-C45mqav6CV5eRXrIFm43anWobR_c17vaiqLdBSrY-ejfJfyH9llZ_5VdW55jy61Jaukfw7Q2nqwcUy__jDhbCKgFmN3Mq7U4txiKKRHdqUjlfHSoQdJze4UMMCGivP1kBGRPyWmd_2PPqpU8G_MIxOlKoqQbOWKjPe7u-0q671PvxNAwNtk-mni2-8w2Cs23GlGTsj9JuExhDX6TrLRYHSW3NiGdrr2WcB3yriRWuMMMveogdaZtrFwAEf668v1SAUJQ5V7ncMTh_K8Um696KE5foNSjsDHYbruGAraz5Z6qKPvVp4QtScZVEqJ8ItokQ5E5YtM4SufRpbIJB0BogeIrKtoYsCNWntzyR6q86_DNwghhl0t-IJkyABGo9A9MTRbi2mQTRe3cuUT5IV2JL4WOGQyE5qqd9a80MGh9jKmKcu1J0NJxKdxkk6jjrr_uGmZHVtPV4TTYsE4krslScTvF2SV0KL506lhh0b0Bssqnl_LIErIU-FV0oj3XCdqSdxG0Jx-Kxp5Bm41rNsCzGCnBaIylMea_3T_EbwNhLIEfqp-hLVqRONjOtZoA5_9fSXMMPc_IAky9784pFLkGoy1BfoOKq4nJ "bug")

파이어 폭스에서는 위와 같은 동작이 발생하지 않는다고 한다.

크로미엄에서는 다른 브라우저와 똑같이 동작하지 않는다는 이유로 버그 등록이 가능하다.

## 난관

수정은 간단했지만 난관이 존재했다. 일단 수정해야하는 테스트 파일이 너무나 많았다.

![chromium-linux-test](/assets/img/uploads/chromium-linux-test.jpg "chromium-linux-test")

리눅스 운영체제에 종속된 테스트 파일만 하더라도 엄청났다.
그런데 더 힘든 것은 윈도우와 맥에서도 테스트 파일을 작성해야한다는 것이다. 윈도우는 내가 가지고 있지만 맥을 찾는 것이 힘들었다. 난 맥을 한번도 써보지 않았다...

맥..비싸다..

# 결론

정말 많은 테스트 파일을 수정해야한다. Mac, Windows, Linux의 운영체제에서 크로미엄을 돌려야하는 상황이다. 하지만 난 Mac이 없는걸....

결국 이 이슈는 눈물을 머금고 클로즈를 눌러야했다.