
---
layout: post
title: Version의 규칙
tags: [Programming]
image:
  path: /images/abstract-1.jpg
  feature: abstract-1.jpg
---

이번 글에서는 Version의 정의와 그 표기법에 대해서 알아보도록 하겠습니다.

참고 자료 :
1. https://en.wikipedia.org/wiki/Software_versioning
2. http://seorenn.blogspot.com/2012/02/version.html
3. https://usinuniverse.bitbucket.io/blog/version.html
4. https://m.blog.naver.com/PostView.nhn?blogId=youngchanmmm&logNo=220752702977&proxyReferer=https%3A%2F%2Fwww.google.com%2F

## 1. Version의 정의

**Version이란?** 일반적으로 배포되는 패키지, 프로그램에는 각각의 버전이 존재합니다. 수정이나 보완이 이루어졌을 경우 Version이 바뀌며, "v1.0.0"와 같이 표기됩니다. Version 표기는 물론 코드 작성자가 마음대로 할 수도 있지만, 통용되는 규칙이 있습니다.

## 2. Version의 표기

우선 보통의 버전은 세 자리의 숫자와 마침표로 이루어집니다. "v4.2.1"이 그 예시입니다. 각각의 숫자는 앞에서부터, **Major**, **Minor**, **Patch**로 이루어집니다. 

1) **Major**는 엄청나게 큰 변화가 일어났을 때에 변하는 수치입니다. 지난 2019년 기존 [Tensorflow](https://www.tensorflow.org/?hl=ko)가 1.0에서 2.0으로 바뀌었는데, 이는 그 안의 상당한 변화를 고려하여 Major version을 바꾼 것이라 볼 수 있겠습니다.

2) **Minor**는 기능적인 면에서 변경점이 있거나 추가된 사항이 있을 때 변경됩니다. 예를 들어, [Pytorch](https://pytorch.org/)의 경우에는 기존 코딩 방식은 크게 변화가 없지만 세부 기능들이 추가되고 변경되어 1.0부터 1.3까지 변화하고 있습니다.

3) **Patch**는 사소한 변경이 이루어졌을 때 증가합니다. 오류들을 잡아내는 행위가 대표적인 예시라고 할 수 있겠습니다. 다만 여기에는 몇 가지 변형이 존재하는 듯합니다.

## 3. 개발 단계(Development Stage)

우선 **Alpha, Beta, Relese Candidate, Release to Manufacturing**에 대해서 알아볼 필요가 있습니다. **Alpha**는 내부에서 테스트 중인 버전을 의미합니다. 아직 여러 확인을 거쳐 오류를 잡아내고 있는 상태입니다. **Beta**는 사용자들에게 접근 권한을 주고 무료로 배포하여 개발자들이 미처 잡아내지 못한 오류를 검출하고 수정하는 데에 사용합니다. 이제 Beta Version을 모두 거치게 되면, 최종 배포(Release)하기 전 단계인 **RC(Relase Candidate)** 버전으로 진입한 뒤 추가로 여러 테스트를 실행합니다. 그 후 마무리가 되면 배포를 하여 최종인 **RTM(Release to Manufacturing)** - 혹은 그냥 **Relase** - 버전이 나오게 됩니다.

위와 같은 개발 단계를 Version에 함께 표현하기 위해 다양한 표기법이 사용되고 있습니다. 아래는 다양한 예시입니다.

1) Alpha, Beta, RC, RTM을 0, 1, 2, 3으로 표기한 후 Version의 맨끝에 한 자리를 더 추가하는 것입니다. 예를 들어, "v1.2.0.1"은 "v1.2"의 alpha 단계의 1번째 Version이라는 것입니다. 다르게 표현하면 "v1.2-a1"로 표현할 수 있습니다. 마찬가지로 "v1.2.3.5"는 "v1.2-r5"로 표현할 수 있습니다.

2) 개발 단계를 따로 언급하는 것입니다. "v1.0.2-alpha"와 같이 표기하는 것이 예시라고 할 수 있겠습니다. alpha 단계에서 수정이 이루어지면 "v1.0.3-alpha"과 같이 표기할 수 있으며, 베타테스트를 시작할 때 "v1.0.3-beta"와 같이 표기할 수 있습니다.

3) 물론 개발 단계 없이 "v.1.10.10"과 같이 병기할 수도 있습니다. 이 경우 Major Version이 1이고, Minor Version은 10이며 여기에서 10번의 자잘한 오류 수정(Patch)가 일어났다는 것을 의미합니다.

## 4. Version의 중요성

사용자 입장에서는 사실 Release Version만을 주로 사용하기 때문에 관계없을 수도 있지만, 자신이 개발자이고 추후 독자적인 프로젝트를 실행하게된다면 Version의 표기가 매우 중요하다고 할 수 있습니다. 해당 프로젝트의 진척도는 물론이고, Version을 기준으로 변경 사항을 Mapping하거나 Roleback할 수 있기 때문에 익숙해지시는 것을 추천합니다.
