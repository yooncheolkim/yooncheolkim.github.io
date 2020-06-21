---
layout: post
title: "Validation"
date: 2020-06-02 21:00:00
tags: spring Validation
description: spring Validation 추상화
---

# Validation 추상화
- org.springframework.validation.Validator
- 일반적인 객체 검증용 인터페이스
- 모든 계층에서 사용가능
- 구현체로는 JSR-303(Bean Validation 1.0)과 JSR-349(Bean Validation 1.1)을 지원한다. (LocalValidatorFactoryBean)
- DataBinder에 들어가 바인딩 할 때 같이 사용되기도 한다.

## Validator 인터페이스 구현
- boolean supports(Class clazz): 어떤 타입의 객체를 검증할 때 사용할 것인지 구현
- void validate(Obejct obj, Erros e) : 실제 검증 로직
    - 구현 시, ValidationUtils 사용

## 스프링 부터 2.0.5 이상 버전 사용시
- LocaValidatorFactoryBean 빈으로 자동 등록
- JSR-380(Bean Validation 2.0.1) 구현체로 hibernate-validator 사용
- https://beanvalidation.org/
