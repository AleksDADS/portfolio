Исходные данные

Таблица 1. Статистика о рекламных событиях
ad_funnel
(
event_datetime - дата и время события (пример: '2024-01-01') (VARCHAR),
alid_id - уникальный id события (INT),
event_type - тип события: показ (show), клик (click), конверсия (conv) (VARCHAR),
user_id - id пользователя (INT),
campaign_id - id рекламной кампании (INT),
category_id - id категории (INT)
)

Таблица 2. Словарь с названиями категорий
category_list
(
category_id - id категории (INT),
category_name - название категории (VARCHAR)
)

```sql
SELECT DISTINCT DATE_TRUNC('MONTH', v.creation_time::TIMESTAMP) year_month,
	COUNT(vacancy_id) vacancy_amount
FROM vacancy v
	JOIN area a ON v.area_id = a.area_id
WHERE a.country_name = 'Россия'
	AND v.creation_time::DATE BETWEEN '2020-01-01' AND '2021-12-31'
	AND v.name = 'Водитель'
	AND v.work_schedule = 'Гибкий график'
	AND v.disabled IS FALSE
GROUP BY 1
ORDER BY 1
```
-----
