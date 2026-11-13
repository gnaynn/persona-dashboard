# K-Persona Lab: 가상 사용자 리서치 플랫폼

한국인 합성 페르소나에게 설문을 돌리고, 응답을 분석·시각화하는 리서치 플랫폼입니다.
실제 사용자 조사에 몇 주가 걸리는 일을, 최대 100명의 가상 응답으로 몇 분 안에 미리 확인할 수 있습니다.

> 한양대학교 딥러닝 수업 기말 프로젝트 (Team 11)

[![K-Persona Lab 데모 영상](https://img.youtube.com/vi/ZUjzO8aM-Yc/maxresdefault.jpg)](https://www.youtube.com/watch?v=ZUjzO8aM-Yc)

📄 [발표 자료 (PDF)](docs/presentation.pdf) · ▶️ [데모 영상 (YouTube)](https://www.youtube.com/watch?v=ZUjzO8aM-Yc)

## 주요 기능

- **페르소나 샘플링:** NVIDIA Nemotron-Personas-Korea(700만 명) 중 1,000명을 ko-sroberta로 임베딩해 OpenSearch에 색인하고, 성별·나이·지역·직업 조건과 kNN 검색으로 응답자를 고릅니다.
- **LLM 응답 생성:** LangGraph 파이프라인이 페르소나별 프롬프트를 만들고 LUXIA LLM(luxia3-llm-32b)에 병렬로 질문합니다.
  입력 검증 → 페르소나 선택 → RAG → 프롬프트 생성 → 병렬 호출 → 품질 필터(중국어·영어 섞임, AI 정체 노출 검출) → 분석
- **대시보드:** 객관식 응답 분포, 주관식 응답 군집(K-means + LLM 군집 요약), 히트맵·막대 차트, PNG/PDF 내보내기를 제공합니다.

## 평가

| 항목 | 결과 |
|---|---|
| 인구통계 정렬 | 100명 샘플과 전체 700만 명 분포 비교, 카이제곱 p > 0.05 |
| 응답 일관성 | 같은 페르소나 8회 반복 시 객관식 88~100% 같은 선택 |
| 실제 설문과 비교 | OpenSurvey 카페 트렌드 2025(2,000명)와 문항별 유사도 37.5~78.5% |

## 구조

| 폴더 | 내용 |
|---|---|
| `src/` | React 프론트엔드 (랜딩, 실험 입력 폼, 대시보드), Vercel 배포 |
| `backend/` | FastAPI 서버(`/run-experiment`, EC2), LangGraph 오케스트레이터, 결과 S3 저장 |

## 팀

| 이름 | 역할 | 맡은 일 |
|---|---|---|
| 이현진 | Backend + Infra | AWS(EC2, S3, OpenSearch) 구축, FastAPI 서버, 부하·응답 시간·비용 평가 |
| 이연수 | LLM + Prompt | 프롬프트 설계(Few-shot, CoT), LUXIA 연동, 응답 현실성 평가 |
| 김나영 | RAG + Retrieval | Nemotron 데이터 전처리, ko-sroberta 임베딩, OpenSearch 인덱스, 페르소나 충실도 평가 |
| 김나연 | Agent Pipeline | LangGraph 오케스트레이터, 페르소나 선택부터 병렬 호출까지 자동화, 반복 응답 일관성 평가 |
| 천송은 | Analysis + Frontend | 응답 임베딩·K-means 군집화, 대시보드 시각화, 인구통계·다양성 평가 |

## 실행

```bash
npm install && npm start                          # 프론트엔드 (http://localhost:3000)
cd backend && pip install -r requirements.txt
uvicorn main:app --reload                         # 백엔드 (LUXIA_API_KEY 필요)
```
