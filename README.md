# Домашнее задание к занятию «Индексы» - Иван Серебреннников

## Задание 1
Напишите запрос к учебной базе данных, который вернёт процентное отношение общего размера всех индексов к общему размеру всех таблиц.
SELECT
    ROUND(SUM(index_length) / SUM(data_length + index_length) * 100, 2) AS index_percentage
FROM
    information_schema.tables
WHERE
    table_schema = 'sakila';
![1.png](https://github.com/Skiledqo/sdb-12.05/blob/main/1.png)

## Задание 2
Выполните explain analyze следующего запроса:

select distinct concat(c.last_name, ' ', c.first_name), sum(p.amount) over (partition by c.customer_id, f.title)
from payment p, rental r, customer c, inventory i, film f
where date(p.payment_date) = '2005-07-30' and p.payment_date = r.rental_date and r.customer_id = c.customer_id and i.inventory_id = r.inventory_id

перечислите узкие места;
оптимизируйте запрос: внесите корректировки по использованию операторов, при необходимости добавьте индексы.

Ответ: 
![2.png](https://github.com/Skiledqo/sdb-12.05/blob/main/2.png)
![3.png](https://github.com/Skiledqo/sdb-12.05/blob/main/3.png)
![4.png](https://github.com/Skiledqo/sdb-12.05/blob/main/4.png)

Как можно увидеть из анализа, то запрос выполняется продолжительное время из-за своей сложности, что может вызвать еще большую нагрузку при большом объеме данных.
Чтобы оптимизировать данный запрос, можно добавить оптимизирующие индексы на столбцах, используемых для объединения и фильтрации (например, payment_date, customer_id, inventory_id, title), это может ускорить выполнение запроса.
![5.png](https://github.com/Skiledqo/sdb-12.05/blob/main/5.png)
![6.png](https://github.com/Skiledqo/sdb-12.05/blob/main/6.png)
![7.png](https://github.com/Skiledqo/sdb-12.05/blob/main/7.png)

EXPLAIN ANALYZE
SELECT DISTINCT CONCAT(c.last_name, ' ', c.first_name), SUM(p.amount) OVER (PARTITION BY c.customer_id, f.title)
FROM payment p
JOIN rental r ON p.payment_date = r.rental_date
JOIN customer c ON r.customer_id = c.customer_id
JOIN inventory i ON i.inventory_id = r.inventory_id
JOIN film f ON i.film_id = f.film_id
WHERE DATE(p.payment_date) = '2005-07-30';


