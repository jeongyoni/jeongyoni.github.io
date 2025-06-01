---
layout: post
title:  "감정사전 V1 구축 및 실험"
categories: [projects, stock-prediction]
tag: [감정사전, 감성분석, 금융뉴스, 주가예측]
toc: true

---

<head>
  <style>
    table.dataframe {
      white-space: normal;
      width: 100%;
      height: 240px;
      display: block;
      overflow: auto;
      font-family: Arial, sans-serif;
      font-size: 0.9rem;
      line-height: 20px;
      text-align: center;
      border: 0px !important;
    }

    table.dataframe th {
      text-align: center;
      font-weight: bold;
      padding: 8px;
    }

    table.dataframe td {
      text-align: center;
      padding: 8px;
    }

    table.dataframe tr:hover {
      background: #b8d1f3; 
    }

    .output_prompt {
      overflow: auto;
      font-size: 0.9rem;
      line-height: 1.45;
      border-radius: 0.3rem;
      -webkit-overflow-scrolling: touch;
      padding: 0.8rem;
      margin-top: 0;
      margin-bottom: 15px;
      font: 1rem Consolas, "Liberation Mono", Menlo, Courier, monospace;
      color: $code-text-color;
      border: solid 1px $border-color;
      border-radius: 0.3rem;
      word-break: normal;
      white-space: pre;
    }

  .dataframe tbody tr th:only-of-type {
      vertical-align: middle;
  }

  .dataframe tbody tr th {
      vertical-align: top;
  }

  .dataframe thead th {
      text-align: center !important;
      padding: 8px;
  }

  .page__content p {
      margin: 0 0 0px !important;
  }

  .page__content p > strong {
    font-size: 0.8rem !important;
  }

  </style>
</head>



```python
import pandas as pd
from konlpy.tag import Okt
from collections import Counter
import numpy as np
import warnings
warnings.filterwarnings("ignore") 
```


```python
def calculate_sentiment(text, sentiment_dict):
    if pd.isna(text):
        return 0, []

    okt = Okt()
    tokens = okt.morphs(text)
    token_counts = Counter(tokens)

    score = 0
    hit_words = []

    for word, count in token_counts.items():
        if word in sentiment_dict:
            adj_score = sentiment_dict[word] * np.log1p(count)
            score += adj_score
            hit_words.append((word, sentiment_dict[word], count, round(adj_score, 3)))

    # bigram
    for i in range(len(tokens) - 1):
        bigram = tokens[i] + tokens[i+1]
        if bigram in sentiment_dict:
            score += sentiment_dict[bigram]
            hit_words.append((bigram, sentiment_dict[bigram], 1, sentiment_dict[bigram]))

    # trigram
    for i in range(len(tokens) - 2):
        trigram = tokens[i] + tokens[i+1] + tokens[i+2]
        if trigram in sentiment_dict:
            score += sentiment_dict[trigram]
            hit_words.append((trigram, sentiment_dict[trigram], 1, sentiment_dict[trigram]))

    return round(score, 3), hit_words
```


```python
#감정사전
sentiment_df = pd.read_csv("combined_sentiment_dict.csv")
sentiment_dict = {
    row['word']: row['score'] * row['weight'] 
    for _, row in sentiment_df.iterrows()
}
```


```python
dict_v1_df= pd.read_csv("last_news_cleaned_file.csv")
dict_v1_df
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>industry</th>
      <th>keyword</th>
      <th>date</th>
      <th>title</th>
      <th>content</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>전기차</td>
      <td>자율주행</td>
      <td>2025-05-20</td>
      <td>AI선장님, 배 몰고 태평양 건넜다…'바다 테슬라' 노리는 K조선</td>
      <td>망망대해에 웬 자율운항 선원 등 사람의 개입이 전혀 없거나 최소한의 개입으로 소프트...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>반도체</td>
      <td>칩</td>
      <td>2025-05-20</td>
      <td>이 식용유, 한 방울 먹였더니…대장용종 5배 넘게 늘었다</td>
      <td>아래 텍스트는 영상 스크립트입니다. 포도씨유 카놀라유 아니면 그냥 식용유 라고 부르...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>반도체</td>
      <td>IM</td>
      <td>2025-05-19</td>
      <td>SKT 전 가입자 유심 털렸다…복제폰 우려 IMEI도 유출 가능성</td>
      <td>SK텔레콤 해킹으로 가입자 전원의 유심 USIM 정보뿐 아니라 개인정보가 관리되는 ...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>반도체</td>
      <td>공정</td>
      <td>2025-05-20</td>
      <td>굴착기·휠로더 생산력 56% 향상…HD현대건설기계 신공장 준공</td>
      <td>HD현대건설기계는 20일 울산 동구 울산캠퍼스에서 스마트팩토리 준공식을 개최했다고 ...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>전기차</td>
      <td>현대차</td>
      <td>2025-05-20</td>
      <td>4대 그룹, 3년간 자산·이익 늘었지만 고용은 '제자리'</td>
      <td>최근 3년간 상위 4대 그룹 삼성 SK 현대차 LG의 자산총액과 순이익이 늘었으나 ...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>11681</th>
      <td>바이오</td>
      <td>면역</td>
      <td>2025-05-18</td>
      <td>[강민성의 헬스토리] 굽힐때 `시큰`…무릎 통증 유발하는 최악의 운동은?</td>
      <td>무릎은 우리 몸의 체중을 대부분 지탱해 주는 중요한 부위로 우리가 어떻게 사용하는지...</td>
    </tr>
    <tr>
      <th>11682</th>
      <td>플랫폼</td>
      <td>플랫폼</td>
      <td>2025-05-18</td>
      <td>글로벌 가상자산, 일부 알트코인 급락세 전환</td>
      <td>알트코인 중심 하락세 이어지며 투자자 긴장이번주 글로벌 가상자산 시장이 전반적인 하...</td>
    </tr>
    <tr>
      <th>11683</th>
      <td>반도체</td>
      <td>공정</td>
      <td>2025-05-18</td>
      <td>[P2E 재조명下] 게임토큰, 넥슨 'NXPC'가 변곡점 될까…'위믹스'는 상폐 위기</td>
      <td>NXPC 바이낸스 이어 업비트 빗썸 상장 3000원대에 거래중 NXPC 의 뒷배는 ...</td>
    </tr>
    <tr>
      <th>11684</th>
      <td>플랫폼</td>
      <td>네이버</td>
      <td>2025-05-18</td>
      <td>카카오, 다음 분사 작업 이달 중 마무리…상반기 이사회 전망</td>
      <td>이달까지 직원 이동 동의 완료 계획 2014년 합병 이후 11년만의 분리성남시 카카...</td>
    </tr>
    <tr>
      <th>11685</th>
      <td>바이오</td>
      <td>바이오</td>
      <td>2025-05-18</td>
      <td>200가지 바이러스 품은 박쥐는 어떻게 멀쩡히 생존할까</td>
      <td>국내 연구팀 세계 최대 박쥐 오가노이드 플랫폼 구축5종 박쥐 오가노이드로 바이러스 ...</td>
    </tr>
  </tbody>
</table>
<p>11686 rows × 5 columns</p>
</div>



```python
dict_v1_df['sentiment_score'] = dict_v1_df['content'].apply(lambda x: calculate_sentiment(x, sentiment_dict)[0])
dict_v1_df['hit_words'] = dict_v1_df['content'].apply(lambda x: calculate_sentiment(x, sentiment_dict)[1])
```

<pre>
WARNING: A restricted method in java.lang.System has been called
WARNING: java.lang.System::load has been called by org.jpype.JPypeContext in an unnamed module (file:/opt/homebrew/Caskroom/miniforge/base/envs/tf-m1/lib/python3.9/site-packages/org.jpype.jar)
WARNING: Use --enable-native-access=ALL-UNNAMED to avoid a warning for callers in this module
WARNING: Restricted methods will be blocked in a future release unless native access is enabled

</pre>

```python
dict_v1_df
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>industry</th>
      <th>keyword</th>
      <th>date</th>
      <th>title</th>
      <th>content</th>
      <th>sentiment_score</th>
      <th>hit_words</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>전기차</td>
      <td>자율주행</td>
      <td>2025-05-20</td>
      <td>AI선장님, 배 몰고 태평양 건넜다…'바다 테슬라' 노리는 K조선</td>
      <td>망망대해에 웬 자율운항 선원 등 사람의 개입이 전혀 없거나 최소한의 개입으로 소프트...</td>
      <td>3.537</td>
      <td>[(실수, -1.0, 1, -0.693), (능력, 1.0, 1, 0.693), (...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>반도체</td>
      <td>칩</td>
      <td>2025-05-20</td>
      <td>이 식용유, 한 방울 먹였더니…대장용종 5배 넘게 늘었다</td>
      <td>아래 텍스트는 영상 스크립트입니다. 포도씨유 카놀라유 아니면 그냥 식용유 라고 부르...</td>
      <td>-1.460</td>
      <td>[(좋은, 3.0, 3, 4.159), (안정, 1.0, 6, 1.946), (성,...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>반도체</td>
      <td>IM</td>
      <td>2025-05-19</td>
      <td>SKT 전 가입자 유심 털렸다…복제폰 우려 IMEI도 유출 가능성</td>
      <td>SK텔레콤 해킹으로 가입자 전원의 유심 USIM 정보뿐 아니라 개인정보가 관리되는 ...</td>
      <td>-23.326</td>
      <td>[(유출, -3.0, 5, -5.375), (피해, -1.0, 2, -1.099),...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>반도체</td>
      <td>공정</td>
      <td>2025-05-20</td>
      <td>굴착기·휠로더 생산력 56% 향상…HD현대건설기계 신공장 준공</td>
      <td>HD현대건설기계는 20일 울산 동구 울산캠퍼스에서 스마트팩토리 준공식을 개최했다고 ...</td>
      <td>7.603</td>
      <td>[(고도화, -2.0, 1, -1.386), (능력, 1.0, 2, 1.099), ...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>전기차</td>
      <td>현대차</td>
      <td>2025-05-20</td>
      <td>4대 그룹, 3년간 자산·이익 늘었지만 고용은 '제자리'</td>
      <td>최근 3년간 상위 4대 그룹 삼성 SK 현대차 LG의 자산총액과 순이익이 늘었으나 ...</td>
      <td>18.461</td>
      <td>[(상위, 2.0, 1, 1.386), (돌파, 3.0, 1, 2.079), (꾸준...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>11681</th>
      <td>바이오</td>
      <td>면역</td>
      <td>2025-05-18</td>
      <td>[강민성의 헬스토리] 굽힐때 `시큰`…무릎 통증 유발하는 최악의 운동은?</td>
      <td>무릎은 우리 몸의 체중을 대부분 지탱해 주는 중요한 부위로 우리가 어떻게 사용하는지...</td>
      <td>2.714</td>
      <td>[(해, -2.0, 1, -1.386), (중요한, 1.0, 1, 0.693), (...</td>
    </tr>
    <tr>
      <th>11682</th>
      <td>플랫폼</td>
      <td>플랫폼</td>
      <td>2025-05-18</td>
      <td>글로벌 가상자산, 일부 알트코인 급락세 전환</td>
      <td>알트코인 중심 하락세 이어지며 투자자 긴장이번주 글로벌 가상자산 시장이 전반적인 하...</td>
      <td>-12.317</td>
      <td>[(하락, -3.0, 12, -7.695), (상위, 2.0, 2, 2.197), ...</td>
    </tr>
    <tr>
      <th>11683</th>
      <td>반도체</td>
      <td>공정</td>
      <td>2025-05-18</td>
      <td>[P2E 재조명下] 게임토큰, 넥슨 'NXPC'가 변곡점 될까…'위믹스'는 상폐 위기</td>
      <td>NXPC 바이낸스 이어 업비트 빗썸 상장 3000원대에 거래중 NXPC 의 뒷배는 ...</td>
      <td>-2.811</td>
      <td>[(상폐, -6.0, 2, -6.592), (위기, -3.0, 2, -3.296),...</td>
    </tr>
    <tr>
      <th>11684</th>
      <td>플랫폼</td>
      <td>네이버</td>
      <td>2025-05-18</td>
      <td>카카오, 다음 분사 작업 이달 중 마무리…상반기 이사회 전망</td>
      <td>이달까지 직원 이동 동의 완료 계획 2014년 합병 이후 11년만의 분리성남시 카카...</td>
      <td>-0.405</td>
      <td>[(정, 1.0, 2, 1.099), (해, -2.0, 2, -2.197), (함께...</td>
    </tr>
    <tr>
      <th>11685</th>
      <td>바이오</td>
      <td>바이오</td>
      <td>2025-05-18</td>
      <td>200가지 바이러스 품은 박쥐는 어떻게 멀쩡히 생존할까</td>
      <td>국내 연구팀 세계 최대 박쥐 오가노이드 플랫폼 구축5종 박쥐 오가노이드로 바이러스 ...</td>
      <td>-10.924</td>
      <td>[(바이러스, -1.0, 20, -3.045), (공포, -6.0, 1, -4.15...</td>
    </tr>
  </tbody>
</table>
<p>11686 rows × 7 columns</p>
</div>



```python
dict_v1_df.to_csv("sentiment_dict_v1.csv", index=False)
```


```python
zero_score_df = dict_v1_df[dict_v1_df['sentiment_score'] == 0]
zero_score_df
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>industry</th>
      <th>keyword</th>
      <th>date</th>
      <th>title</th>
      <th>content</th>
      <th>sentiment_score</th>
      <th>hit_words</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>11</th>
      <td>플랫폼</td>
      <td>AI</td>
      <td>2025-05-20</td>
      <td>[사진] 취업 면접 연습도 이제는 AI로</td>
      <td>19일 서울 강남구 코엑스에서 열린 2025 글로벌 탤런트 페어 채용박람회를 찾은 ...</td>
      <td>0.0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>18</th>
      <td>전기차</td>
      <td>현대차</td>
      <td>2025-05-20</td>
      <td>[Biz &amp; Now] 보스턴 다이내믹스, DHL에 로봇 1000대 공급</td>
      <td>현대차그룹 로봇 계열사 보스턴 다이내믹스가 글로벌 종합물류기업 DHL그룹에 물류 자...</td>
      <td>0.0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>19</th>
      <td>플랫폼</td>
      <td>미디어</td>
      <td>2025-05-20</td>
      <td>황금연휴 때 해외서 돈 펑펑…카드결제 증가율 국내의 5배</td>
      <td>지난 1 6일 황금연휴 기간 해외 소비가 국내 소비보다 크게 늘어난 것으로 나타났다...</td>
      <td>0.0</td>
      <td>[(도움, 1.0, 1, 0.693), (해, -2.0, 1, -1.386), (쓴...</td>
    </tr>
    <tr>
      <th>32</th>
      <td>플랫폼</td>
      <td>미디어</td>
      <td>2025-05-19</td>
      <td>황금연휴에 국내 카드 결제 3.1% 늘었지만, 해외는 17.5%↑</td>
      <td>지난 1 6일 황금연휴 기간 해외 소비가 국내 소비보다 크게 늘어난 것으로 나타났다...</td>
      <td>0.0</td>
      <td>[(도움, 1.0, 1, 0.693), (해, -2.0, 1, -1.386), (쓴...</td>
    </tr>
    <tr>
      <th>94</th>
      <td>바이오</td>
      <td>셀트리온</td>
      <td>2025-05-16</td>
      <td>서정진 회장 “미국의 의약품 관세부과, 사업 성장 기회”</td>
      <td>서정진 사진 셀트리온 회장이 미국 트럼프 행정부의 의약품 정책 변화와 관련해 바이오...</td>
      <td>-0.0</td>
      <td>[(해, -2.0, 2, -2.197), (기회, 3.0, 2, 3.296), (없...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>10896</th>
      <td>플랫폼</td>
      <td>네이버</td>
      <td>2025-04-17</td>
      <td>한국공항공사, 카카오맵 실내지도 서비스 전국공항 확대</td>
      <td>한국공항공사는 올해 상반기 안에 카카오맵 실내지도 서비스를 전국 공항으로 확대한다고...</td>
      <td>0.0</td>
      <td>[(쉽게, 1.0, 1, 0.693), (함께, 1.0, 1, 0.693), (해,...</td>
    </tr>
    <tr>
      <th>10973</th>
      <td>에너지</td>
      <td>에너지</td>
      <td>2025-03-31</td>
      <td>김승연 회장, ㈜한화 지분 11.32% 세 아들에 증여</td>
      <td>김승연 한화그룹 회장이 한화 지분 22.65 중 11.32 를 세 아들에게 증여한다...</td>
      <td>0.0</td>
      <td>[(공시, 0.0, 1, 0.0), (오해, -1.0, 1, -0.693), (측은...</td>
    </tr>
    <tr>
      <th>11016</th>
      <td>반도체</td>
      <td>삼성전자</td>
      <td>2025-03-19</td>
      <td>삼성전자 오늘 주주총회...국내 기업 주총 시즌 본격 개막</td>
      <td>삼성전자의 제56기 정기 주주총회가 19일 오전 경기도 수원컨벤션센터에서 개최된다....</td>
      <td>0.0</td>
      <td>[(성, 0.0, 4, 0.0), (위기, -3.0, 1, -2.079), (개선,...</td>
    </tr>
    <tr>
      <th>11203</th>
      <td>반도체</td>
      <td>공정</td>
      <td>2025-01-21</td>
      <td>첫 직선제, ‘맥 빠진 선거’되나… 예비후보 등록 접수 ‘한산’ [제1회 전국동시새...</td>
      <td>중앙선거관리위원회에 위임 MG새마을금고중앙회 60년 역사 사상 처음 직선제로 치러지...</td>
      <td>0.0</td>
      <td>[(해, -2.0, 1, -1.386), (위반, -3.0, 1, -2.079), ...</td>
    </tr>
    <tr>
      <th>11580</th>
      <td>플랫폼</td>
      <td>AI</td>
      <td>2025-05-18</td>
      <td>오픈AI, UAE 세계 최대 규모 데이터센터 건설 지원 예정</td>
      <td>오픈AI로고. 연합뉴스챗GPT개발사 오픈AI가 아랍에미리트 UAE 에 들어설 세계 ...</td>
      <td>0.0</td>
      <td>[(함께, 1.0, 3, 1.386), (해, -2.0, 1, -1.386), (성...</td>
    </tr>
  </tbody>
</table>
<p>262 rows × 7 columns</p>
</div>



```python
no_hits_df = dict_v1_df[dict_v1_df['hit_words'].apply(lambda x: len(x) == 0)]
no_hits_df
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>industry</th>
      <th>keyword</th>
      <th>date</th>
      <th>title</th>
      <th>content</th>
      <th>sentiment_score</th>
      <th>hit_words</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>11</th>
      <td>플랫폼</td>
      <td>AI</td>
      <td>2025-05-20</td>
      <td>[사진] 취업 면접 연습도 이제는 AI로</td>
      <td>19일 서울 강남구 코엑스에서 열린 2025 글로벌 탤런트 페어 채용박람회를 찾은 ...</td>
      <td>0.0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>18</th>
      <td>전기차</td>
      <td>현대차</td>
      <td>2025-05-20</td>
      <td>[Biz &amp; Now] 보스턴 다이내믹스, DHL에 로봇 1000대 공급</td>
      <td>현대차그룹 로봇 계열사 보스턴 다이내믹스가 글로벌 종합물류기업 DHL그룹에 물류 자...</td>
      <td>0.0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>129</th>
      <td>플랫폼</td>
      <td>AI</td>
      <td>2025-05-15</td>
      <td>[사진] 이리 오세요 … AI·VR 기술의 초대장</td>
      <td>한 관람객이 14일 서울 강남구 코엑스에서 열린 국제인공지능대전에서 인공지능 AI ...</td>
      <td>0.0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>138</th>
      <td>플랫폼</td>
      <td>메타</td>
      <td>2025-05-15</td>
      <td>[Biz &amp; Now] 두산스코다파워, 체코 음악축제 후원</td>
      <td>두산에너빌리티의 체코 자회사 두산스코다파워가 체코 최대 음악 축제인 프라하의 봄 국...</td>
      <td>0.0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>147</th>
      <td>플랫폼</td>
      <td>카카오</td>
      <td>2025-05-14</td>
      <td>카카오톡에서도  '…' 보인다…실시간 입력 중 표시</td>
      <td>카카오톡에서 메시지 입력 상태를 보여주는 기능이 추가된다. 14일 업계에 따르면 카...</td>
      <td>0.0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>10401</th>
      <td>반도체</td>
      <td>CE</td>
      <td>2024-12-13</td>
      <td>하나캐피탈 신임 대표에 김용석 하나은행 부행장</td>
      <td>김용석 하나캐피탈 대표 후보자 양동원 하나저축은행 대표 후보자 하나캐피탈의 신임 대...</td>
      <td>0.0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>10409</th>
      <td>플랫폼</td>
      <td>모바일</td>
      <td>2024-12-13</td>
      <td>소비자 만족도, 통신3사보다 알뜰폰에 완전히 기울어</td>
      <td>하반기 통신사 만족도 조사에서 알뜰폰이 693점으로 통신 3사 평균 676점보다 1...</td>
      <td>0.0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>10456</th>
      <td>전기차</td>
      <td>현대차</td>
      <td>2024-12-10</td>
      <td>기아 노조, 현대차 이어 내일 '정권 퇴진 요구' 부분파업</td>
      <td>기아 오토랜드 광주 현대차에 이어 기아 노동조합이 정권 퇴진 운동 에 나선 민주노총...</td>
      <td>0.0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>10737</th>
      <td>반도체</td>
      <td>엔비디아</td>
      <td>2024-11-25</td>
      <td>글로벌 스타트업 축제 '컴업' 내달 개최…40여 개국 참가</td>
      <td>다음 달 11일과 12일 서울에서 글로벌 스타트업 축제인 컴업 2024 가 열립니다...</td>
      <td>0.0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>10868</th>
      <td>반도체</td>
      <td>공정</td>
      <td>2025-04-25</td>
      <td>애플, '탈중국 현실화'…"내년부터 아이폰 전량, 인도서 생산"</td>
      <td>애플이 내년부터 미국에서 판매되는 모든 아이폰의 조립 공정을 인도로 이전할 것이라고...</td>
      <td>0.0</td>
      <td>[]</td>
    </tr>
  </tbody>
</table>
<p>99 rows × 7 columns</p>
</div>



```python
no_hits_df.to_csv("sentiment_dict_v1_score=0.csv", index=False)
```
