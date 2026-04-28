## DevDoc Agent

RAG 기반 + 터미널 실행 + 자동 문서화를 결합한 개발 에이전트
디버깅 로그와 코드를 입력하면, 근거 기반 분석 → 명령 실행 → 결과 검증 → 개발 문서 생성까지 자동 수행

### Overview
개발 과정에서 생기는 문제를
[입력 → 분석 → (RAG 검색) → 명령 실행 → 결과 검증 → 문서화]의 흐름으로 처리

### Core Features
1. Debug Log → Structured Document
터미널 로그, 에러 메시지 입력
원인 분석 + 해결 과정 정리
2. Terminal Execution (Controlled)
제한된 명령어만 실행
실행 결과 기반으로 다음 행동 결정
3. RAG 기반 근거 검색
공식 문서 / API docs / 레퍼런스 기반 분석
잘못된 추론 방지
4. Dev Note 자동 생성
Markdown 형식 개발 문서 출력
재사용 가능한 지식 자산화
--- 
**문제 정의**
개발 중 발생하는 오류 로그, 수정 과정, 공식 문서 확인 과정이 분산되어
동일한 문제를 반복적으로 해결해야 하는 비효율이 발생한다.

**접근 방법**
DevDoc Agent는 입력된 로그와 코드를 기반으로
문제 분석, 실행, 검증, 문서화를 자동화하여
개발 지식을 구조화된 형태로 축적한다.

**Architecture**
```
User Input
   ↓
Planner (LLM)
   ↓
Retriever (RAG)
   ↓
Executor (Terminal)
   ↓
Verifier
   ↓
Doc Generator
```
---

### Project Structure
```
devdoc-agent/
 ├── main.py            # CLI entrypoint
 ├── planner.py         # action decision (LLM)
 ├── retriever.py       # RAG module
 ├── executor.py        # terminal execution
 ├── verifier.py        # result validation
 ├── docgen.py          # markdown generator
 ├── config.yaml        # settings
 ├── logs/              # run logs (JSONL)
 └── artifacts/         # generated docs
```
### Stack
```
[CLI UX]
  ├─ prompt_toolkit
  ├─ rich
  └─ InquirerPy

[Core Agent]
  ├─ factchat (LLM)
  ├─ requests / httpx
  └─ pydantic

[Execution]
  ├─ subprocess
  └─ sandbox rules

[RAG]
  ├─ simple loader (초기)
  ├─ (옵션) FAISS / Chroma
  └─ 문서 파일들

[Infra]
  ├─ dotenv
  ├─ yaml
  └─ logging(JSONL)
 
```
### Installation

```
git clone https://github.com/yourname/devdoc-agent.git
cd devdoc-agent

python -m venv .venv
source .venv/bin/activate

pip install -r requirements.txt
```

### Usage
```
기본 실행
python main.py
예시 입력
>> pytest 실행했는데 ModuleNotFoundError 발생
예시 출력
# Debug Note

## 문제 상황
pytest 실행 시 모듈 import 실패

## 에러 메시지
ModuleNotFoundError: No module named 'xxx'

## 원인 추정
- PYTHONPATH 미설정
- 패키지 설치 누락

## 시도한 해결 방법
- pip install xxx
- 환경변수 확인

## 최종 해결
pip install xxx로 해결됨

## 재발 방지 체크리스트
- requirements.txt 관리
- venv 활성화 확인
```

### Safety
```
허용된 명령어만 실행
ALLOWED_COMMANDS = [
    "ls",
    "cat",
    "python",
    "pip",
    "pytest"
]
차단 대상
rm
sudo
시스템 변경 명령
```
### Agent Loop
1. 문제 분석
2. 필요한 작업 결정
3. (필요 시) 문서 검색
4. 명령 실행
5. 결과 평가
6. 반복 또는 종료

### Roadmap
```
MVP
 로그 → 문서 정리
 명령어 추천
 Markdown 출력
v1
 제한된 명령 실행
 실행 결과 기반 재시도
v2
 RAG (Notion API docs)
 코드 리뷰 기능
v3
 Notion 자동 업로드
 프로젝트 통합
``` 
### Design Principles
CLI-first (터미널 중심)
Text-based workflow (AI 친화)
Reproducibility (로그/문서 저장)
Safe execution (sandbox)
