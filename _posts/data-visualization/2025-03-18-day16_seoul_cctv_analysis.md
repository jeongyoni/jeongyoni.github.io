---
layout: single
title:  "Seoul CCTV Analysis"
categories: data-analysis
tag: [python, data-cleaning, matplotlib, visualization]
toc: true
author_profile: false
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
import matplotlib.pyplot as plt 
import pandas as pd 
from matplotlib import rc 
import numpy as np 
import seaborn as sns 
import warnings
rc('font', family='AppleGothic')
rc('axes', unicode_minus=False)
warnings. filterwarnings( 'ignore')
```


```python
cctv = pd.read_csv('서울시 자치구 CCTV 설치현황.csV', encoding='utf-8')
cctv.head(3)
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
      <th>순번</th>
      <th>구분</th>
      <th>총 계</th>
      <th>2015년 이전</th>
      <th>2015년</th>
      <th>2016년</th>
      <th>2017년</th>
      <th>2018년</th>
      <th>2019년</th>
      <th>2020년</th>
      <th>2021년</th>
      <th>2022년</th>
      <th>2023년</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>종로구</td>
      <td>2031</td>
      <td>549</td>
      <td>158</td>
      <td>128</td>
      <td>143</td>
      <td>142</td>
      <td>67</td>
      <td>237</td>
      <td>82</td>
      <td>225</td>
      <td>300</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>중 구</td>
      <td>2718</td>
      <td>275</td>
      <td>236</td>
      <td>239</td>
      <td>372</td>
      <td>386</td>
      <td>155</td>
      <td>364</td>
      <td>419</td>
      <td>137</td>
      <td>135</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>용산구</td>
      <td>3258</td>
      <td>88</td>
      <td>38</td>
      <td>295</td>
      <td>491</td>
      <td>147</td>
      <td>335</td>
      <td>473</td>
      <td>486</td>
      <td>500</td>
      <td>405</td>
    </tr>
  </tbody>
</table>
</div>



```python
# 1. 순번, 총계 삭제 
# 2. '구분' -> '자치구' 변경 
# 3. '2015년 이전'-> '2014년' 변경
# 4. '년' 글자를 열이름에서 삭제
```


```python
# 1. 순번, 총계 삭제 
columns_to_drop = [col for col in ['순번', '총계'] if col in cctv.columns]
cctv = cctv.drop(columns=columns_to_drop)
```


```python
# 2. '구분' -> '자치구' 변경 
if '구분' in cctv.columns:
    cctv = cctv.rename(columns={'구분': '자치구'})
```


```python
# 3. '2015년 이전'-> '2014년' 변경
if '2015년 이전' in cctv.columns:
    cctv = cctv.rename(columns={'2015년 이전': '2014년'})
```


```python
# 4. '년' 글자를 열이름에서 삭제
cctv.columns = [col.replace('년', '') for col in cctv.columns]
```


```python
cctv.head(3)
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
      <th>자치구</th>
      <th>총 계</th>
      <th>2014</th>
      <th>2015</th>
      <th>2016</th>
      <th>2017</th>
      <th>2018</th>
      <th>2019</th>
      <th>2020</th>
      <th>2021</th>
      <th>2022</th>
      <th>2023</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>종로구</td>
      <td>2031</td>
      <td>549</td>
      <td>158</td>
      <td>128</td>
      <td>143</td>
      <td>142</td>
      <td>67</td>
      <td>237</td>
      <td>82</td>
      <td>225</td>
      <td>300</td>
    </tr>
    <tr>
      <th>1</th>
      <td>중 구</td>
      <td>2718</td>
      <td>275</td>
      <td>236</td>
      <td>239</td>
      <td>372</td>
      <td>386</td>
      <td>155</td>
      <td>364</td>
      <td>419</td>
      <td>137</td>
      <td>135</td>
    </tr>
    <tr>
      <th>2</th>
      <td>용산구</td>
      <td>3258</td>
      <td>88</td>
      <td>38</td>
      <td>295</td>
      <td>491</td>
      <td>147</td>
      <td>335</td>
      <td>473</td>
      <td>486</td>
      <td>500</td>
      <td>405</td>
    </tr>
  </tbody>
</table>
</div>



```python
```
