---
layout: posts
title:  "Day 8 h:merge datasets by id"
date : 2025-03-07
categories: [study-logs]
tag: [python, blog, merge]
excerpt: "Merging datasets by ID and analyzing salary distributions across different countries."
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
# 1000명의 사람 데이터 만들기
# 1번 데이터셋
# ID : ID_1, ID_2, ... ID_1000
# 나이 : 20 ~ 60 랜덤
# 도시 : 특정 도시에서 랜덤으로 할당 ex) New York, Paris, Berlin, London, Seoul, Tokyo

# 2번 데이터셋
# ID : ID_1, ID_2, ... ID_1000
# 연봉 : 랜덤 (30,000 ~ 500,000)

# 3번 데이터셋 (작은 데이터)
# 도시 : 특정 도시에서 랜덤으로 할당 ex) New York, Paris, Berlin, London, Seoul, Tokyo
# 나라 : 특정 도시의 나라 ex) USA, France, Germany, UK, Korea, Japan

# 1. 1번 데이터셋과 2번 데이터셋 병합 (ID 기준) # merge
# 2. 병합된 데이터에 City를 기준으로 병합 # merge
# 3. 각 나라별 평균 연봉
# 4. 제일 연봉이 높은 사람 어느 나라 사람인지?
```


```python
import pandas as pd
import numpy as np

data1 = pd.DataFrame({
    'ID': ['ID_' + str(i) for i in range(1, 1001)],
    'Age': np.random.randint(20, 61, 1000),
    'City': np.random.choice(["New York", "Paris", "Berlin", "London", "Seoul", "Tokyo"], 1000)
})
data1
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
      <th>ID</th>
      <th>Age</th>
      <th>City</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ID_1</td>
      <td>34</td>
      <td>New York</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ID_2</td>
      <td>22</td>
      <td>New York</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ID_3</td>
      <td>47</td>
      <td>Seoul</td>
    </tr>
    <tr>
      <th>3</th>
      <td>ID_4</td>
      <td>21</td>
      <td>Berlin</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ID_5</td>
      <td>47</td>
      <td>New York</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>995</th>
      <td>ID_996</td>
      <td>50</td>
      <td>Berlin</td>
    </tr>
    <tr>
      <th>996</th>
      <td>ID_997</td>
      <td>49</td>
      <td>Tokyo</td>
    </tr>
    <tr>
      <th>997</th>
      <td>ID_998</td>
      <td>20</td>
      <td>Paris</td>
    </tr>
    <tr>
      <th>998</th>
      <td>ID_999</td>
      <td>30</td>
      <td>Seoul</td>
    </tr>
    <tr>
      <th>999</th>
      <td>ID_1000</td>
      <td>50</td>
      <td>New York</td>
    </tr>
  </tbody>
</table>
<p>1000 rows × 3 columns</p>
</div>



```python
data2 = pd.DataFrame({
    'ID': ['ID_' + str(i) for i in range(1, 1001)],
    'Salary': np.random.randint(30000, 500001, 1000)
})
data2
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
      <th>ID</th>
      <th>Salary</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ID_1</td>
      <td>69977</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ID_2</td>
      <td>122910</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ID_3</td>
      <td>328828</td>
    </tr>
    <tr>
      <th>3</th>
      <td>ID_4</td>
      <td>404185</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ID_5</td>
      <td>478549</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>995</th>
      <td>ID_996</td>
      <td>36099</td>
    </tr>
    <tr>
      <th>996</th>
      <td>ID_997</td>
      <td>238387</td>
    </tr>
    <tr>
      <th>997</th>
      <td>ID_998</td>
      <td>234915</td>
    </tr>
    <tr>
      <th>998</th>
      <td>ID_999</td>
      <td>243245</td>
    </tr>
    <tr>
      <th>999</th>
      <td>ID_1000</td>
      <td>83480</td>
    </tr>
  </tbody>
</table>
<p>1000 rows × 2 columns</p>
</div>



```python
data3 = pd.DataFrame({
    'City': ["New York", "Paris", "Berlin", "London", "Seoul", "Tokyo"],
    'Country': ["USA", "France", "Germany", "UK", "Korea", "Japan"]
})
data3
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
      <th>City</th>
      <th>Country</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>New York</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Paris</td>
      <td>France</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Berlin</td>
      <td>Germany</td>
    </tr>
    <tr>
      <th>3</th>
      <td>London</td>
      <td>UK</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Seoul</td>
      <td>Korea</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Tokyo</td>
      <td>Japan</td>
    </tr>
  </tbody>
</table>
</div>



```python
# 1. 1번 데이터셋과 2번 데이터셋 병합 (ID 기준) # merge
#merged_data = pd.merge(data1, data2, on='ID', how='inner')
#merged_data

#how='inner' 사용하지 않고(기본값이기 때문)도 merge 중복된 값 기준으로 병합 가능 
merged_data = pd.merge(data1, data2, on='ID')
merged_data
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
      <th>ID</th>
      <th>Age</th>
      <th>City</th>
      <th>Salary</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ID_1</td>
      <td>34</td>
      <td>New York</td>
      <td>413756</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ID_2</td>
      <td>22</td>
      <td>New York</td>
      <td>284810</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ID_3</td>
      <td>47</td>
      <td>Seoul</td>
      <td>198306</td>
    </tr>
    <tr>
      <th>3</th>
      <td>ID_4</td>
      <td>21</td>
      <td>Berlin</td>
      <td>168178</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ID_5</td>
      <td>47</td>
      <td>New York</td>
      <td>195502</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>995</th>
      <td>ID_996</td>
      <td>50</td>
      <td>Berlin</td>
      <td>417496</td>
    </tr>
    <tr>
      <th>996</th>
      <td>ID_997</td>
      <td>49</td>
      <td>Tokyo</td>
      <td>274777</td>
    </tr>
    <tr>
      <th>997</th>
      <td>ID_998</td>
      <td>20</td>
      <td>Paris</td>
      <td>143146</td>
    </tr>
    <tr>
      <th>998</th>
      <td>ID_999</td>
      <td>30</td>
      <td>Seoul</td>
      <td>457611</td>
    </tr>
    <tr>
      <th>999</th>
      <td>ID_1000</td>
      <td>50</td>
      <td>New York</td>
      <td>124492</td>
    </tr>
  </tbody>
</table>
<p>1000 rows × 4 columns</p>
</div>



```python
# 2. 병합된 데이터에 City를 기준으로 병합 # merge
final_data = pd.merge(merged_data, data3, on='City')
final_data
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
      <th>ID</th>
      <th>Age</th>
      <th>City</th>
      <th>Salary</th>
      <th>Country</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ID_1</td>
      <td>34</td>
      <td>New York</td>
      <td>413756</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ID_2</td>
      <td>22</td>
      <td>New York</td>
      <td>284810</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ID_5</td>
      <td>47</td>
      <td>New York</td>
      <td>195502</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>3</th>
      <td>ID_16</td>
      <td>26</td>
      <td>New York</td>
      <td>134161</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ID_17</td>
      <td>50</td>
      <td>New York</td>
      <td>282374</td>
      <td>USA</td>
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
      <th>995</th>
      <td>ID_959</td>
      <td>48</td>
      <td>Tokyo</td>
      <td>316524</td>
      <td>Japan</td>
    </tr>
    <tr>
      <th>996</th>
      <td>ID_977</td>
      <td>20</td>
      <td>Tokyo</td>
      <td>244497</td>
      <td>Japan</td>
    </tr>
    <tr>
      <th>997</th>
      <td>ID_982</td>
      <td>53</td>
      <td>Tokyo</td>
      <td>383480</td>
      <td>Japan</td>
    </tr>
    <tr>
      <th>998</th>
      <td>ID_983</td>
      <td>50</td>
      <td>Tokyo</td>
      <td>139717</td>
      <td>Japan</td>
    </tr>
    <tr>
      <th>999</th>
      <td>ID_997</td>
      <td>49</td>
      <td>Tokyo</td>
      <td>274777</td>
      <td>Japan</td>
    </tr>
  </tbody>
</table>
<p>1000 rows × 5 columns</p>
</div>



```python
# 3. 각 나라별 평균 연봉
average_salary_by_country = final_data.groupby('Country')['Salary'].mean()
average_salary_by_country
```

<pre>
Country
France     257290.920732
Germany    262098.905405
Japan      261826.875000
Korea      259120.923858
UK         265735.378049
USA        277944.942857
Name: Salary, dtype: float64
</pre>

```python
# 4. 연봉이 제일 높은 사람 어느 나라 사람인지?
highest_salary_row = final_data.loc[final_data['Salary'].idxmax()]
highest_salary_country = highest_salary_row['Country']
highest_salary = highest_salary_row['Salary']
print(f"연봉이 제일 높은 사람은 {highest_salary_country} 사람이고, 연봉은 {highest_salary}입니다.")
```

<pre>
연봉이 제일 높은 사람은 France 사람이고, 연봉은 499833입니다.
</pre>

```python
```


```python
```
