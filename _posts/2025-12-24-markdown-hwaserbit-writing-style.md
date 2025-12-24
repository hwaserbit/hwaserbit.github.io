---
title: "마크다운 문서 작성 팁 (hwaserbit's style)"
date: 2025-12-24 20:25:32 +0900
categories: [Markdown]
tags: [Tip]
description: "hwaserbit 스타일의 마크 다운 문서 작성 문법들의 모음"

# 2. 고급 설정 (필요할 때만 true 또는 경로 입력)
pin: false                   # true로 설정하면 메인화면 최상단에 고정됨 (공지사항 등)
math: true                   # 수식($$)을 사용할 경우 true
mermaid: true                # 다이어그램(flowchart)을 그릴 경우 true
toc: true                    # 오른쪽 목차 표시 여부 (기본값 true)
comments: true               # 댓글 기능 켜기/끄기

# 3. 이미지/배너 설정
#image:
#  path: /assets/img/banners/banner_name.jpg  # 배너 이미지 경로
#  alt: 배너 설명 텍스트                       # 시각장애인용 설명 (SEO)
---

> 이 블로그를 운영하고 포스트를 지속하려면  
굳이 검색없이 포스트를 메모용으로 사용하면 좋을 것 같어서 적었습니디.

# Markdown 문법 모음
## 2. 자주쓰는 Markdown 문법

### 1. [기밀문서 스타일] 검은색 마스킹
글자도 검정, 배경도 검정으로 칠해서 "드래그 하면 보이는" 방식입니다.  
`보안 로그 가리기, 퀴즈 정답용, 중요 하지 않은 내용` 으로 사용하기에 좋습니다.

```HTML
<span style="color: black; background-color: black;">여기를 드래그하면 보입니다</span>
```

👉 결과 미리보기: <span style="color: black; background-color: black;">여기를 드래그하면 보입니다</span>

### 2. 테이블 (Tables) - 하드웨어 스펙 비교할 때 필수 📊
템플릿에 표(Table)가 빠져 있더군요. HP Z640 vs i5-13600k 비교하거나, 포트 목록 정리할 때 꼭 필요합니다.

```Markdown
| 서버명 | CPU | RAM | 역할 |
| :--- | :---: | ---: | :--- |
| **1** | Xeon | 0GB | ? |
| **2** | i5 | 0GB | ? |
| **Pi** | ARM | 0GB | ? |
```
- `:---` (왼쪽 정렬), `:---:` (가운데 정렬), `---:` (오른쪽 정렬)


### 2. 접기/펼치기 (Details) - "긴 로그" 숨길 때 최고 🔽
서버 엔지니어는 **로그 파일(Log File)이나 설정 파일(Config)**이 수백 줄이 넘어갈 때가 많습니다. 이걸 그대로 올리면 스크롤 압박이 심하죠. 이때 "클릭하면 펼쳐지는" 기능을 쓰면 아주 깔끔합니다.

```HTML
<details>
<summary>클릭하여 전체 에러 로그 보기 (syslog)</summary>

예시
# ... (엄청 긴 내용) ...
</details>
```
<details>
<summary>클릭하여 예시 보기</summary>

예시
</details>

**활용:** `nginx.conf` 전체 설정이나, 긴 에러 스택 트레이스를 올릴 때 쓰세요.

-----

### 3. 취소선 (Strikethrough) - "삽질 기록" 남길 때 🖍️

기술 블로그의 묘미는 "이렇게 했다가 망해서, 저렇게 바꿨다"는 기록이죠.
잘못된 시도였음을 표현할 때 씁니다.

```markdown
처음에는 ~~CentOS 7~~을 설치했으나, 지원 종료 문제로 Ubuntu 22.04로 변경함.
```
예시: 처음에는 ~~CentOS 7~~을 설치했으나...


### 4. 새 탭으로 링크 열기 (Target Blank) 🔗
블로그 글을 읽다가 외부 링크(공식 문서 등)를 클릭했을 때, 블로그가 닫히면 번거로우니 Chirpy(Jekyll)에서는 링크 뒤에 속성을 붙여서 새 창으로 띄울 수 있습니다.

```Markdown
[Docker 공식 문서 바로가기](https://docs.docker.com){: target="_blank" }
```
👉 [hwaserbit.com](https://hwaserbit.com){: target="_blank" }


### 5. 형광펜 강조 (Highlight) ✨
볼드체(Bold)말고, 진짜 형광펜 칠한 것처럼 강조하고 싶을 때가 있습니다.

```Markdown
서버 재부팅 전 <mark>반드시 데이터 백업</mark>을 수행해야 합니다.
```
예시: 서버 재부팅 전 <mark>반드시 데이터 백업</mark>을 수행해야 합니다.

<br>

## ✅ 결론 (Conclusion)
**제가 아니더라도 이걸 보고 계시는 분들도 이 글로 손쉽게 마크다운을 작성 해보세요.**
**필요한 문법을 알게 되면 계속 채워 넣는 글이 되겠습니다. 감사합니다.**