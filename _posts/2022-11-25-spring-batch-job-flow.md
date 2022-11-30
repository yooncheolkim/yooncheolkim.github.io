---
layout: post
title: spring batch job flow
date: 2022-11-25 14:00
tags: spring batch job flow
description: spirng batch job flow
---

### batch job flow

- 하나의 배치 잡이 하나의 쿼리로 끝나는게 아니라, 여러번의 쿼리 + 외부 api 호출이 연계 되어있다면, 프로세스에 따라 job flow 분기를 해야한다.
- job flow는 A step이 이렇게 종료되면 C step으로 가! 라는 동작을 구현할 수 있게, job 흐름처리를 할수있게 지원한다.

#### 예시

```java
return jobBuilderFactory
                .get(JOB_NAME)
                .start(Step1.step())
                  .on("FAILED")
                  .end()
                .from(Step1.step())
                  .on("COMPLETED")
                  .to(Step2())
                .from(Step2())
                  .on("COMPLETED")
                  .to(Step3.step())
                  .on("FAILED")
                  .to(finallyStep())
                .from(Step3.step())
                  .on("COMPLETED")
                  .to(Step4.step())
                  .on("FAILED")
                  .to(finallyStep())
                .from(Step4.step())
                  .on("COMPLETED")
                  .to(Step5.step())
                  .on("FAILED")
                  .to(finallyStep())
                .from(Step5.step())
                  .on("COMPLETED")
                  .to(Step6.step())
                  .on("FAILED")
                  .to(finallyStep())
                .from(Step6.step())
                  .on("*")
                  .to(finallyStep())
                .from(finallyStep())
                  .on("FAILED")
                  .fail()
                .from(finallyStep())
                  .on("COMPLETED")
                  .end()
                .end()
                .listener(jobNotStartSameJobNameSameJobParameterLogListener)
                .listener(jobCompletionLogListenerl)
                .listener(customStepExecutionListener)
                .listener(
                        new JobExecutionListenerSupport() {
                            @Override
                            public void beforeJob(JobExecution jobExecution) {
                                jobExecution.getExecutionContext().put("jobName", JOB_NAME);
                            }
                        })
                .build();
```

- 모든 스텝에서, ExitStatus가 FAILED로 끝났을때(runtime error or afterStep에서 "FAILED"로 리턴할때) on("Failed").to 를 해줌.
-

### spring batch를 프로젝트에서 사용하면서...

- spring batch를 처음 접하면서, 맛만 본 느낌.
- 이전 프로젝트에서는 쿼리 한방에 끝내자.(tasklet 처럼) 라는 생각이 있었는데, spring batch 하면서, chunk 기반의 batch를 작성할 수 있었음.
- batch에서 중요한것은
  - 최소 수행 단위(chunk 단위)를 정하는것
  - 외부 호출이 있을경우 http payload 확인하기
  - 재수행 관련 프로세스

#### aws batch랑 같이 사용하다보니...

- aws batch랑 같이 사용하다보니, jvm 이 실행되어, 하나의 job를 수행하고 종료된다.
  - 매 job마다, spring이 실행되고 끝나고 반복되다 보니 애플리케이션의 변수,상태 관리가 불필요했다.
  - 만약,
