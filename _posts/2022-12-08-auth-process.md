---
layout: post
title: auth 인증 프로세스
date: 2022-12-08 13:30
tags: authorizaion cognito redis
description: auth 인증 프로세스
published: false
---

### 인증 프로세스 개발

- 프로젝트 진행하면서, 기본적인 인증 프로세스는 아키텍쳐께서 잡아주셨다.
- 하지만, 실제로 구현은 개발자가 해야했기때문에,, 인증 전반적인 프로세스와 중요 포인트를 정리한다.
- 인증 프로세스 전반이 왜 이렇게 설계 되었는지 정확히 이해되진 않지만, 각 데이터들의 역할을 파악하여 정리하였다.


#### [WIP] 기본 인증 프로세스

- [인증프로세스 다이어그램](https://drive.google.com/file/d/1V7keSAh4EyD0qE__YGn5g4t_ZBCqAgur/view?usp=sharing)

- cognito에서 생성된 accessToken, refreshToken은 client에 내려주지 않는다.
- cognito에서 생성된 accessToken과 refreshToken은 감춘채로, idToken을 accessToken처럼 관리하여, 인증프로세스를 구현한듯하다...
- redis에 (key : idToken, value : accessToken, refreshToken)을 관리하고, idToken과 refreshId라는 uuid를 생성하여 client에 전달하도록 설계 되어있다.


#### 재인증 관련
- idToken을 accessToken으로 생각하자.
- redis에 accessToken을 key로 저장하지만, 이것만 저장하면 안된다.
- 보통 보안을 위해, accessToken의 expired기간을 짧게 가져가고, 재인증을 위해 refreshToken을 사용하여, cognito에 accessToken을 재요청한다.
- 이때 우리는 redis에 (key : accessToken, value : refreshToken) 으로 저장하도록 했기 때문에, 이 key/value가 만료되면, refreshToken까지 사라져 버린다.
- 그렇다고 refreshToken까지 client에 내려주면, token 탈취자도 계속해서 재인증을 할 수 있기 때문에, 이것은 보안상 문제가 된다.
- 그래서, redis에 accessToken과 refreshToken을 저장할때 다음과 같이 두개의 key/value를 저장하도록 설계 되었다.

```javascript
// redis key/value
{ 
    'accessToken' : 'refreshToken, id 등등 변하지 않는 부가 정보',
    're_accessToken' : 'refreshToken, id 등등 변하지 않는 부가 정보'
}
```
- accessToken은 만료기간이 짧은 key 이고, re_accessToken 만료기간이 긴 key라면, accessToken이 만료되더라도, re_accessToken로 refreshToken을 가져와서 재인증 절차를 진행할 수 있다.



### 결론
- 인증 서버 구현은 어떻게 구성하고 개발할건지 정답이 없다. 설계하기 나름이다.
- 아키텍처께서 먼저 프로젝트 철수하셔서, 정확히 왜 이렇게 설계 하셨는지 물어볼 수 없었다. 다음에는 꼭 물어봐야지...