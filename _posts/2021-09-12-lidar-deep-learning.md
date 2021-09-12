---
title: (Paper) Deep Learning for LiDAR 
date: 2021-09-12 23:11:00 +0900
categories: [Paper, LiDAR]
tags: [lidar]     # TAG names should always be lowercasetitle
---

LiDAR 데이터의 Data Augmentation에 관한 논문을 읽고 있다가 해당 주제와 관련된 연구 동향에 대해 잘 정리된 부분이 있어 관련 글을 작성한다.



## 3D Object Detection

3D object detection은 크게 세가지 방법으로 나누어진다.

* Projection / View-based
* Voxel-based
  * VoxelNet
  * SECOND
* Raw point cloud-based
  * Point RCNN
  * Point Pillars

그리고 Voxel의 방법과 Raw point cloud의 방법을 합친 방법(PVRCNN)도 존재한다.



## LiDAR Data Augmentation

LiDAR Data, 즉 포인트 클라우드에 사용되는 Data Augmentation도 다양하게 연구되고 있다. Data Augmentation에는 크게 세가지 방법이 존재한다.

> simulation approach
> : Unreal 같은 툴 또는 GTA 같은 게임을 이용해 얻어낸 가상 환경 데이터를 사용하는 방법이다. 이 경우 실제 데이터와의 차이가 존재해 domain adaptation이 문제가 된다.(이 도메인 adaptation 문제를 해결하려는 연구도 존재한다.)
>
> real + virtual
> : 실제 라이다를 통해 얻어낸 데이터에 가상의 물체를 섞는 기법이다.
>
> global approach
> : 라이다 데이터를 돌리거나 자르거나하는 2D image에서도 보편적으로 사용되는 방법이다.

### 세부적인 방법들

이 논문에서 소개한 방법들은 다음과 같다.

> Instaboost
> : instance segmentation에 사용되는 방법이다.
>
> PointMixup
> : 2D image에서 사용되는 augmentation 기법인 mixup을 차용한 방법이다.
>
> GT-Aug
> : object detection에 사용되는 방법이다.
>
> Pointaugment
> : object classification에 사용되는 방법이다.

