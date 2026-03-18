---
title: "심플 템플릿 간단 설명"
date: 2026-03-18 09:58:00 +0900
categories: [Markdown]
tags: [Tip, Markdown]
description: "내가 쓸 템플릿을 간단하게 적어둠."

# 2. 고급 설정 (필요할 때만 true 또는 경로 입력)
pin: false                   # true로 설정하면 메인화면 최상단에 고정됨 (공지사항 등)
math: true                   # 수식($$)을 사용할 경우 true
mermaid: true                # 다이어그램(flowchart)을 그릴 경우 true
toc: true                    # 오른쪽 목차 표시 여부 (기본값 true)
comments: false               # 댓글 기능 켜기/끄기

---

1. 포스트 설정 (Front Matter)
글의 제목, 날짜, 카테고리, 태그 등 블로그 시스템이 인식해야 할 필수 메타데이터를 정의하는 영역.


``` Markdown
# 위 아래에 --- 있어야 적용됨 주석으로 설정을 헤딩 하는것 같음.
---
title: "포스트 제목"
date: 2026-03-18 09:58:00 +0900
categories: [Category1, Category2]
tags: [tag1, tag2]
description: "이 포스트의 핵심 내용을 한 줄로 요약합니다."

# 2. 고급 설정 (필요할 때만 true 또는 경로 입력)
pin: false                   # true로 설정하면 메인화면 최상단에 고정됨 (공지사항 등)
math: true                   # 수식($$)을 사용할 경우 true
mermaid: true                # 다이어그램(flowchart)을 그릴 경우 true
toc: true                    # 오른쪽 목차 표시 여부 (기본값 true)
comments: false               # 댓글 기능 켜기/끄기
---
```

2. 글의 시작과 개요 (Overview)
본격적인 내용을 시작하기 전, 독자가 글의 목적을 빠르게 파악할 수 있도록 인용문 형식을 빌려 요약합니다.

Markdown
## 1. 개요 (Overview)

> **요약:** 이 포스트에서는 특정 기술의 설치 방법과 트러블슈팅 과정을 다룹니다.
3. 알림 상자 (Prompts)
Chirpy 테마에서 가장 유용하게 쓰이는 기능입니다. 상황에 맞는 아이콘과 색상 박스로 정보를 시각적으로 구분합니다.

Tip: 꿀팁이나 추가 설명

Info: 참고용 정보

Warning: 주의 사항

Danger: 치명적인 오류나 위험 요소

Markdown
> **팁 (Tip)**
> 이 명령어를 사용하면 설치 시간을 단축할 수 있습니다.
> {: .prompt-tip }

> **정보 (Info)**
> 현재 버전은 v2.0 이상에서만 동작합니다.
> {: .prompt-info }

> **주의 (Warning)**
> 설정 파일을 수정하기 전 반드시 백업을 생성하세요.
> {: .prompt-warning }

> **위험 (Danger)**
> 이 명령어를 실행하면 기존 데이터가 모두 삭제됩니다.
> {: .prompt-danger }

