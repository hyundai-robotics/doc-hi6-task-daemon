
[__SOURCE](README.md)
# ${cont_model} 제어기 기능설명서 - 태스크 데몬 (Task Daemon)

[__SOURCE](0-about-this-manual/precautions.md)
# 사전 주의사항

{% include url="https://hrcontentsrelay-bmgae5hdbzapc4bc.koreacentral-01.azurewebsites.net/api/proxy?path=doc-common-pages/ko/precautions.md" %}

[__SOURCE](1-overview/README.md)
# 1. 개요

[__SOURCE](1-overview/1-prerequisite.md)
# 1.1 사전지식

본 설명서를 잘 이해하기 위해서는 아래의 지식을 갖추고 있어야 합니다.

* [${cont_model} 제어기 조작설명서](https://hrbook-hrc.web.app/#/view/doc-hi6-operation/ko-tp630/README)

[__SOURCE](1-overview/2-about.md)
# 1.2 태스크 데몬 (Task Daemon) 기능에 대해서

{% hint style="info" %}
이 기능은 V60.30-00 및 이후 버전부터 지원됩니다.
{% endhint %}

일반적으로 ${cont_model} 제어기의 job 프로그램은 자동모드일 때, 혹은 수동모드이면서 StepFWD를 누르고 있을 때만 수행됩니다.

그러나 때로는, 이러한 재생 조건이 아닐 때에도, 백그라운드에서 job 프로그램을 실행해야 할 경우가 있습니다. 
가령, 제어기의 현재 상태를 외부로 보고하는 네트워크 서비스 기능을 job으로 구현했다면, 이 job은 위와 같은 재생 조건과 무관하게 항상 실행되고 있어야 유용할 것입니다.

태스크 데몬 (Task Daemon) 기능을 활용하여, 특정한 job을 원하는 태스크에 할당하고 이를 재생 조건과 무관하게 항상 실행할 수 있습니다.

{% hint style="info" %}
데몬 (daemon)이란, 보통 백그라운드에서 계속 수행되면서 주로 외부 시스템으로부터의 서비스 요청을 처리해주는 프로그램을 의미하는 용어입니다.
{% endhint %}

{% hint style="warning" %}

TaskDaemon 기능은 아래와 같은 제약이 있습니다.

- move 등 스텝 명령문은 에러 없이 무시됩니다. 즉, 로봇이나 부가축 장치를 움직일 수는 없습니다.

- 태스크 0이나 멀티태스크 기능에 의해 사용 중인 태스크 번호는 데몬으로 사용할 수 없습니다.

- 아래와 같은 기타 명령문들은 대부분 동작하지 않습니다.

```python
cowork, axisctrl, filter, brake_check, gasp_check, softxyz, fctrl, softjoint, toolchng, load_esti, 등...
```

- 아래와 같은 로봇 응용 명령문들은 대부분 동작하지 않습니다.

```python
arcon, lvs, multipass, cv.wait, heightsen, 등...
```

- TaskDaemon으로 실행 중인 job을 편집하면, 경우에 따라 해당 태스크의 daemon 실행은 정지할 수도 있습니다.

{% endhint %}

[__SOURCE](2-how-to-use/README.md)
# 2. 사용 방법


[__SOURCE](2-how-to-use/1-setting.md)
# 2.1. 설정

`[F2: 시스템] - 4: 응용 파라미터 - 15: 태스크 데몬`을 선택하십시오.

![태스크 데몬 메뉴](../_assets/menu.png)

<br>

아래와 같은 설정화면이 열립니다.  
태스크 1 ~ 태스크 7에 대한 설정이 가능합니다. (태스크 0은 데몬으로 사용할 수 없습니다.)

- `Job 번호` 항목에 job 번호를 입력하면, 그 번호를 메인 프로그램으로 하여 데몬으로 실행하도록 태스크가 설정됩니다.  
0으로 설정되어 있으면, 해당 태스크는 데몬으로 사용하지 않습니다. 즉 데몬 OFF 상태입니다.

- `자동 실행`을 체크하면, 설정을 완료하거나 제어기를 부팅했을 때 자동으로 데몬이 실행됩니다.
- `반복`을 체크하면, job CYCLE이 완료되었을 때 처음부터 다시 반복 수행됩니다. 즉, `[F7: 조건설정] - 동작 사이클` 설정을 `반복`으로 설정한 것과 같은 개념입니다.

- `상태` 항목에는 태스크의 현재 상태와 함께 괄호 안에 현재 프로그램 카운터(프로그램 번호/스텝 번호/펑션 번호)가 표시됩니다.

  - OFF (꺼짐) : 데몬으로 사용하지 않는 상태입니다.
  - OCCUPIED (점유) : 멀티태스크 기능에 의해 점유되어 사용 중인 태스크입니다. 데몬으로 사용할 수 없습니다.
  - READY (준비) : 프로그램 헤더에서 기동(START) 되기를 기다리는 상태입니다.
  - RUN (실행) : 데몬으로 재생 중인 상태입니다.
  - STOP (정지) : 실행이 정지된 상태입니다.
  - WAITING (대기) : `delay`문이나 `wait`문, `input`문 등에서 대기 중인 상태입니다.
  - ERROR (에러) : 에러가 발생한 상태입니다. 에러코드가 같이 표시되기도 합니다.
  - END (종료) : job CYCLE이 완료된 상태입니다.

![태스크 데몬 설정 화면](../_assets/setting.png)

하단의 F키들로 현재 커서가 위치한 태스크에 대해 수동 조작을 할 수 있습니다.

- `[F1: 리셋]` : 선택된 태스크를 정지시키고, 리셋을 수행합니다. `R0,[ENTER]`를 수행한 것과 같은 개념입니다. 모든 호출 정보와 지역변수가 클리어되고, 프로그램 카운터는 메인 프로그램 헤더 위치에 놓입니다.
- `[F2: 실행]` : STOP 혹은 READY 나 END 상태의 태스크 데몬을 기동시킵니다. `START` (기동) 버튼을 누르는 것과 같은 개념입니다.
- `[F3: 정지]` : RUN 혹은 WAITING 상태의 태스크 데몬을 정지시킵니다. `STOP` (정지) 버튼을 누르는 것과 같은 개념입니다.

- `[F7: 확인]` : 설정을 저장하고, 설정 화면을 닫습니다. `자동 실행`으로 설정한 태스크 데몬은 실행을 시작합니다.

[__SOURCE](2-how-to-use/2-monitoring.md)
# 2.2. 모니터링

![태스크 데몬 모니터링](../_assets/monitoring.png)

`창조정 - [F1: 선택] - 멀티태스크` 모니터링 창에서 태스크 데몬의 현재 프로그램 카운터를 확인할 수 있습니다.

[__SOURCE](2-how-to-use/3-job-editing.md)
# 2.3. job 편집

태스크 데몬의 메인 job 프로그램이나 서브 job 프로그램들은 자유롭게 편집할 수 있습니다. 다만 이 프로그램들이 호출 스택에 있을 때(즉, 실행 중에 있을 때)에 명령문 삽입/삭제를 수행하면, 아래와 같은 확인 대화상자가 나타납니다.

![정지 및 리셋 확인 대화상자](../_assets/stop_reset_dialog.png)

`[ENTER]` 키를 누르면, 해당 태스크 데몬이 정지 및 리셋됩니다. `[ESC]` 키를 누르면, 편집이 취소됩니다.

또한 호출 스택에 있는 job 프로그램을 삭제하면, 해당 daemon 실행이 동작이 중단되고 초기화됩니다.

[__SOURCE](appendices/README.md)
# 별첨

  



[__SOURCE](appendices/rules-occupational-safety.md)
# 산업안전보건기준에 관한 규칙 및 안전검사 고시

당해 산업용 로봇은 산업안전보건기준에 관한 규칙 및 안전검사 고시(검사 대상일 경우)의 검사 기준을 고려하여 설치하여야 한다.

"[산업안전보건기준에 관한 규칙](https://hrbook-hrc.web.app/#/view/rules-on-occupational-safety-and-health-standards/ko/README)"

[__SOURCE](quality-assurance.md)
# 품질보증

"[품질보증](https://hrbook-hrc.web.app/#/view/quality-assurance/ko/README)"
