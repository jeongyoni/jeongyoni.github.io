---
layout: post
title: "[FinTone] 감정사전 V1 구축기"
date: 2025-06-01
categories: [projects, stock-prediction]
tags: [감정사전, 감성분석, 금융텍스트, 주가예측]
---

# 📊 FinTone 감정사전 프로젝트

**프로젝트 목적**  
금융 뉴스 및 산업군별 주식 데이터 분석을 위한 감정사전을 구축하고,  
정성적 텍스트 데이터를 정량적 감정 점수로 변환하여 주가 예측 모델에 활용합니다.

---

## 🔍 프로젝트 구조

| 파일 | 설명 |
|------|------|
| `dict_0.ipynb` | 초기 감정사전 조사 내용 |
| `dict_v1.ipynb` | 감정사전 병합 및 적용 실험 |
| `combined_sentiment_dict.csv` | 현재까지 정리된 감정사전 (CSV 포맷) |
| `utils/sentiment_score.py` | 감정 점수 계산을 위한 함수 |
| `dict_v2_plan.md` | 감정사전 개선 방향 기획안 정리 |

---

## ✅ 감정 점수 기준

- **+2** : 확실한 이익, 구조개선 등 미래의 기대감이 높고 영향력 크면
- **+1** : 긍정적인 움직임이나 결과를 보이지만 영향이 미미한경우
- ** 0** : 맥락에 따라 감성방향 달라지는 거. (기준금리, 거래소 등)
- **-1** : 부정적 상황이지만 즉각적인 피해나 파급력 미미
- **-2** : 중대한 손실 위기 피해와 같이 강력한 부정적 영향..

---

## 🧪 사용 예시 (Python)

```python
from utils.sentiment_score import calculate_sentiment_score

text = "테슬라 주가 급등, 목표가 상향 조정"
score, hit_words = calculate_sentiment_score(text)
print("점수:", score)
