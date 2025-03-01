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
	SELECT user_id,
	COUNT(event_type)
	FROM ad_funnel
	WHERE event_type = 'conv'
	AND category_id = 425
	GROUP BY 1;
```
-----
