One-line summary: an AI major who builds the environment LLM agents work in (tools, safety rules enforced in code, a trial-and-error log) and uses run records and measurements to improve that environment and the services built on it.

## 소개

AI 를 전공하고 있으며, 아래 저장소 여덟 개는 LLM 과 에이전트를 직접 만들어 보며 쌓은 결과물이다. 관심은 모델 자체보다 에이전트가 일하는 환경, 곧 도구와 안전 장치와 시행착오 기록에 있고, 그 환경을 직접 짓고 매일 쓴다.

안전 장치는 문서의 부탁이 아니라 코드로 건다. browser-atlas 는 막힌 사이트, robots.txt 가 금지한 주소, 결제와 제출처럼 되돌릴 수 없는 클릭을 사람이 터미널에서 허락하기 전까지 도구가 거부한다. 처음에는 "에이전트는 여기서 멈춘다"고 문서에만 적었다가 코드로 옮긴 과정을 저장소에 남겼다. small_AGENT 는 에이전트가 함정을 밟을 때마다 "증상, 원인, 대응"을 기록장에 적고, 다음 작업 전에 그 기록장을 읽게 한다.

환경은 감이 아니라 측정으로 고친다. small_AGENT 에서 고정 대기 6초를 "필요한 요소가 보일 때까지만" 기다리는 방식으로 바꿔 같은 작업이 18.0초에서 0.97초로 줄었고, 크롬 연결을 직접 구현해 한 번 붙는 데 1,567ms 가 21ms 로 줄었다(한 PC 에서 잰 값이며 커밋 기록에서 옮겼다). ChartWithLlm 은 LLM 이 뽑은 규칙이 예측을 실제로 낫게 하는지 규칙이 있을 때와 없을 때의 정확도를 비교하도록 만들었고, 그 비교에 시간순 분리가 빠져 있다는 한계도 함께 적어 두었다. 금융 분석 환경에서는 단계 진행 상태를 배열 위치로 기록하던 탓에 중간에 새 단계를 넣으면 돌리지 않은 단계가 통과로 보이는 문제가 있어, 상태를 단계 이름 기준으로 복구하도록 고쳤다.

## 저장소

에이전트 하네스(에이전트가 일하는 환경)

- [browser-atlas](https://github.com/Gachon-junghyun/browser-atlas): 사람이 검토한 사이트 127곳의 지도, 표준 라이브러리만 쓰는 크롬 조종기, 경험 메모리, 코드로 강제하는 안전 규칙을 하나의 MCP 서버로 묶은 프로젝트이다.
- [DeGaJaFinance](https://github.com/Gachon-junghyun/DeGaJaFinance): 금융 뉴스, 공시, 시세, 수급을 모으고 분석하는 Python 모듈 묶음과, LLM 에이전트가 단계별 지시를 읽으며 그 모듈을 쓰는 작업 환경이다(수집 서버와 분석 클라이언트를 나눈다).
- [small_AGENT](https://github.com/Gachon-junghyun/small_AGENT): 실제 크롬을 조종해 "가서 보고 오는" 일을 시키는 Claude Code 스킬 묶음과, 에이전트가 읽고 쓰는 시행착오 기록장이다.
- [agent_platform](https://github.com/Gachon-junghyun/agent_platform): 처리 단계를 파일 하나로 추가하면 자동으로 색인되고, 실행 순서는 LLM 이 사람이 정한 하드룰 안에서 고르는 에이전트 실행 플랫폼이다.
- [agent_lab](https://github.com/Gachon-junghyun/agent_lab): 요약에서 빠진 항목을 잡는 에이전트를 고정 파이프라인과 LLM 오케스트레이션 두 방식으로 만들고, 재사용 부품을 분리해 가는 실험 저장소이다.

데이터와 LLM 실험

- [macro-graph](https://github.com/Gachon-junghyun/macro-graph): 한국 경제 뉴스에서 Gemini 로 인과 체인을 뽑아 쌓고, 사건 하나가 어디로 번지는지 FastAPI 와 D3.js 화면으로 탐색하는 웹앱이다.
- [HAN_LAB](https://github.com/Gachon-junghyun/HAN_LAB): 종목 스크리너, 로컬 LLM 브라우저 에이전트, 유튜브 주장 추출 같은 실험에서 두 곳 이상에 쓰인 코드만 공용 폴더로 올리는 개인 모노레포이다.
- [ChartWithLlm](https://github.com/Gachon-junghyun/ChartWithLlm): KOSPI 200 일봉을 텍스트 차트로 바꿔 LLM 에게 읽히고, 맞은 사례와 틀린 사례의 차이에서 뽑은 규칙이 예측을 개선하는지 검증하는 실험 파이프라인이다.

## 일하는 방식

시행착오는 기록으로 남겨 다음 작업이 같은 실수를 하지 않게 한다. browser-atlas 와 small_AGENT 는 현장 기록장을 두고, 여덟 저장소 모두 README 의 "배운 점과 한계"에 무엇을 재현했고 무엇을 검증하지 못했는지를 나눠 적는다. 효과를 재지 못한 변경은 효과가 있었다고 쓰지 않고 재지 못했다고 쓴다. 코드의 상당 부분은 AI 코딩 도구가 작성하고 작성자는 방향과 구조를 정하며, 그 역할 분담도 저장소 문서에 적어 둔다.
