# Маркетинговая витрина данных
**Цель:** посчитать маркетинговые метрики.

**Описание:** 
user_payments - данные по транзакциям из Greenplum  
site_visits - информация о поведении пользователей на сайте (данные Яндекс Метрики из S3)

**Структура:**

**Стек:**


## 1. Постановка задачи (сбор требований)


## 2. Проектирование
#### 2.1 Изучим данные в исxодных файлах
> подключаемся к Greenplum из Python и выводим колонки и типы данных
 
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

> подключаемся к S3 в YC
 
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

#### 2.1 Создаем слои данных в ClickHouse
> подключаемся к ClickHouse из DBeaver и создаем слои tmp и raw

```sql
CREATE DATABASE tmp;
CREATE DATABASE raw;
```

> далее создаем таблицы с необходимыми полями и соответсвующими типами данных, выбираем подходящие движки, партицирование и ключ сортировки

```sql
CREATE TABLE tmp.site_visits  (
date              String,
timestamp         String,
user_client_id    Int64,
action_type       String,
placement_type    String,
placement_id      Int64,
user_visit_url    String
) ENGINE = Log; -- подходит для временного хранения

CREATE TABLE tmp.user_payments (
date              String,
timestamp         String,
user_client_id    Int64,
item              String,
price             Int64,
quantity          Int64,
amount            Float64,
discount          Float64,
order_id          Int64,
status            String
) ENGINE = Log;

CREATE TABLE raw.site_visits (
date              DateTime,
timestamp         DateTime,
user_client_id    Int64,
action_type       String,
placement_type    String,
placement_id      Int64,
user_visit_url    String,
insert_time DateTime, -- время вставки
hash String -- хеш для всех полей
) ENGINE = MergeTree()
PARTITION BY toYYYYMM(date) -- партиции
ORDER BY date; -- и ключ сортировки по дате

CREATE TABLE raw.user_payments(
date              DateTime,
timestamp         DateTime,
user_client_id    Int64,
item              String,
price             Int64,
quantity          Int64,
amount            Float64,
discount          Float64,
order_id          Int64,
status            String,
insert_time DateTime,
hash String
) ENGINE = MergeTree()
PARTITION BY toYYYYMM(date)
ORDER BY date;
```





## 3. Разработка

## 4. Тест?

## 4. Анализ данных
















