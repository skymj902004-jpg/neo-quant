아주 핵심적인 피드백입니다. LLM 아키텍처를 "보조 분석 계층 + Provider 추상화 + RAG + Router/Fallback" 구조로 확장하여 구현 계획을 업데이트하겠습니다.

Viewed implementation_plan.md:1-528

전체 내용을 확인했습니다. 이제 LLM 아키텍처 섹션을 대폭 확장하고, 디렉터리 구조와 설정, 의존성, Open Questions도 함께 업데이트하겠습니다.

Edited implementation_plan.md

구현 계획을 업데이트했습니다. 주요 반영 내용을 요약하면:

### 추가된 핵심 설계

1. **정량 vs 비정형 영역 분리 원칙** — LLM 없이도 앱의 모든 정량 엔진(재무/차트/매크로/스캐너/점수/Conviction/Rebalancing)이 완전히 동작하는 것을 명시했습니다

2. **2계층 LLM Router** — Local LLM(1차 분석가) → Cloud LLM(심층 분석가) 구조를 Mermaid 다이어그램으로 설계했습니다. 50개 후보 중 10개만 Cloud로 보내서 **비용 약 80% 절감**

3. **Fallback/Escalation 정책** — confidence가 낮거나 Critical Risk가 탐지되면 자동으로 Cloud LLM으로 승격하는 로직

4. **RAG 파이프라인** — Parser → Chunker → Embedding → Vector DB → Relevant Chunks → LLM 구조로, 수백 페이지 보고서를 통째로 넣지 않고 관련 chunk만 LLM에 전달

5. **`llm/` 디렉터리 확장** — `base.py`, `router.py`, `local_provider.py`, `gemini_provider.py`, `prompts/`, `schemas/`, `cache/`, `rag/` 하위 구조

6. **하드웨어 가이드** — RTX 3070 + 32GB RAM 기준으로 7B~14B Q4 모델이 적합한 작업 범위 제시

7. **LLM 역할 경계** — "이 종목을 사야 하는가?"는 ❌, "이 문서에서 공급망 위험 문장을 추출하라"는 ⭕ — 명확한 경계를 정의했습니다

검토하신 후 승인해 주시면 Phase 0 코드 구현을 시작하겠습니다.