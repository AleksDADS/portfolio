# Маркетинговая витрина данных
**Цель:** посчитать маркетинговые метрики.

**Описание:** 

**Структура:**

**Стек:**


## 1. Постановка задачи (сбор требований)


## 2. Проектирование
#### 2.1 Изучим типы данных в исодных файлах
> Подключаемся к Greenplum из Python и выводим типы
```python
from sqlalchemy import create_engine
import pandas as pd

engine = create_engine(
    f'postgresql+psycopg2://{user}:{password}@{host}:5432/sample_store'
)

user_payments = pd.read_sql(
    'SELECT * FROM sample_store.user_payments LIMIT 10',
    engine
)

user_payments.info()
```
<img src="images/2025-12-12_08-41-53.png" width="400" height="300">
> Подключаемся к S3 в YC
```python
import boto3

s3 = boto3.client(
    's3',
    endpoint_url='https://storage.yandexcloud.net',
    aws_access_key_id=aws_key_id,
    aws_secret_access_key=aws_secret_key,
    region_name='ru-central1'
)

bucket_name = 'yc-metrics-theme'
object_key  = '2022-06-30-site-visits.csv'
local_path  = '2022-06-30-site-visits.csv'

s3.download_file(bucket_name, object_key, local_path)

site_visits = pd.read_csv('2022-06-30-site-visits.csv')
site_visits.info()
```
<img src="images/2025-12-12_08-42-19.png" width="400" height="300">


## 3. Разработка

## 4. Тест?

## 4. Анализ данных
















