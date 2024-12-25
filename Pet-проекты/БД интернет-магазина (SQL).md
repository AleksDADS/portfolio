# База данных интернет-магазина

### Таблица clients:
- client_id – идентификатор клиента, первичный ключ таблицы 
- first_name – имя 
- last_name – фамилия 
- middle_name – отчество

### Таблица orders:
- order_id – идентификатор заказа, первичный ключ таблицы 
- client_id – идентификатор клиента, внешний ключ, отсылающий к таблице clients 
- order_date – дата заказа 
- order_sum – сумма заказа 
- region – регион заказа 

### Таблица items: 
- order_id – идентификатор заказа, внешний ключ, отсылающий к таблице orders 
- item_id – идентификатор товара, первичный ключ таблицы 
- item_name – название товара 
- item_prod – компания изготовитель товара 

### Таблица delivery: 
- order_id – идентификатор заказа, внешний ключ, отсылающий к таблице orders 
- driver_id – идентификатор водителя, выполнившего доставку 
- delivery_date – дата доставки 
- rating – рейтинг доставки

## Задачи

### Задача 0
Разработать БД, подходящую для решения задач 1-4. 

### Задача 1 
Цель: список клиентов (ФИО).

Условия: совершали заказы в период с 01.01.18 по 02.01.18 
В заказах был товар X, производителя Y.

#### Решение: 
`sql
SELECT DISTINCT c.client_id,

	last_name,
 
	first_name,
 
	middle_name
 
FROM clients c

	JOIN orders USING(client_id)
 
	JOIN items USING(order_id)
 
WHERE order_date BETWEEN '2018-01-01' AND '2018-01-02'

	AND item_name = 'X'
 
	AND item_prod = 'Y
`

### Задача 2
Цель: список заказов от клиентов с именем Иван.

Условия: совершали заказы в период с 01.01.18 по 02.01.18
Сумма заказов от клиентов за период больше или равно 10 т.р.
В заказах не было товара X, но был товар Y.

#### Решение:

SELECT DISTINCT order_id

FROM (

		SELECT order_id,
  
			sum(order_sum) OVER (PARTITION BY o.client_id) AS total_sum
   
		FROM clients
  
			JOIN orders o USING(client_id)
   
		WHERE order_date BETWEEN '2018-01-01' AND '2018-01-02'
  
			AND first_name = 'Иван'
   
			AND order_id IN (
   
				SELECT order_id
    
				FROM items
    
				WHERE item_name = 'Y'
    
				EXCEPT
    
				SELECT order_id
    
				FROM items
    
				WHERE item_name = 'X'
    
			)
   
	) AS table_a
 
WHERE total_sum >= 10000


### Задача 3
Цель: список клиентов, не совершающих покупки на протяжении 6 месяцев.

Условия: Клиенты, которые были с оборотом выше среднего в аналогичном периоде
прошлого года. Данные клиенты из топ-3 регионов по продажам.

#### Решение:

SELECT client_id

FROM orders

WHERE order_date NOT BETWEEN '2022-10-18' AND '2023-04-18'

	AND client_id IN (
 
		SELECT client_id
  
		FROM orders
  
		WHERE order_date BETWEEN '2021-10-18' AND '2022-04-18'
  
		GROUP BY 1
  
		HAVING SUM(order_sum) > (
  
				SELECT SUM(order_sum) / COUNT(DISTINCT client_id)
    
				FROM orders
    
				WHERE order_date BETWEEN '2021-10-18' AND '2022-04-18'
    
			)
   
	)
 
	AND region IN (
 
		SELECT region
  
		FROM orders
  
		GROUP BY 1
  
		ORDER BY sum(order_sum) DESC
  
		LIMIT 3
  
	)


### Задача 4
Цель: список заказов с условно проблемными доставками.

Условия: Доставка была сделана на этой неделе водителями с плохими откликами за
текущий месяц от клиентов. Данные клиенты - высокодоходные за все время работы.

#### Решение:

SELECT o.order_id

FROM orders o

	JOIN delivery USING(order_id)
 
WHERE delivery_date BETWEEN '2023-04-17' AND '2023-04-18'

	AND driver_id IN (
 
		SELECT driver_id
  
		FROM delivery
  
		WHERE delivery_date BETWEEN '2023-04-01' AND '2023-04-18'
  
		GROUP BY 1
  
		HAVING AVG(rating) < 3
  
	)
 
	AND client_id IN (
 
		SELECT client_id
  
		FROM orders
  
		GROUP BY 1
  
		HAVING CASE
  
				WHEN EXTRACT(
    
					days
     
					FROM (MAX(order_date) - MIN(order_date))
     
				) < 30 THEN NULL
    
				ELSE SUM(order_sum) /(
    
					EXTRACT(
     
						days
      
						FROM (MAX(order_date) - MIN(order_date))
      
					) / 30
     
				) > 100000
    
			END
   
	)
 
