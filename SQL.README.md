# Yandex_Practicum
Projects
SQL
Анализ данных о фондах и инвестициях
1. Посчитали сколько компаний закрылось
SELECT COUNT(status)
FROM company
WHERE status = 'closed'
2. Отобразили количество привлечённых средств для новостных компаний США (из таблицы company) и отсортировали таблицу по убыванию значений в поле funding_total.
# SELECT funding_total
FROM company
WHERE category_code = 'news'
AND country_code = 'USA'
ORDER BY funding_total DESC

3. Нашли общую сумму сделок по покупке одних компаний другими в долларах.Отобрали сделки, которые осуществлялись только за наличные с 2011 по 2013 год включительно.
   SELECT SUM(price_amount)
FROM acquisition
WHERE term_code = 'cash'
AND EXTRACT(YEAR FROM CAST(acquired_at AS date)) BETWEEN 2011 AND 2013;
4. Отобразили имя, фамилию и названия аккаунтов людей в твиттере, у которых названия аккаунтов начинаются на 'Silver'
   SELECT first_name, last_name, twitter_username
FROM people
WHERE twitter_username LIKE 'Silver%'
5. Вывели на экран всю информацию о людях, у которых названия аккаунтов в твиттере содержат подстроку 'money', а фамилия начинается на 'K'
   SELECT *
FROM people
WHERE twitter_username LIKE '%money%'
AND last_name LIKE 'K%'
6. Для каждой компании нашли количество учебных заведений, которые окончили её сотрудники. Вывели название компании и число уникальных названий учебных заведений. Составили топ-5 компаний по количеству университетов.
   SELECT c.name, COUNT(DISTINCT e.instituition)
FROM company AS c
JOIN people AS p ON c.id = p.company_id
JOIN education AS e ON p.id = e.person_id
GROUP BY c.name
ORDER BY COUNT(DISTINCT e.instituition) DESC
LIMIT 5
7. Составили список с уникальными названиями закрытых компаний, для которых первый раунд финансирования оказался последним.
   SELECT DISTINCT c.name
FROM company AS c
JOIN funding_round AS f ON c.id = f.company_id
WHERE c.status = 'closed'
AND f.is_first_round = 1
AND f.is_last_round = 1
8. Составили список уникальных номеров сотрудников, которые работают в компаниях, отобранных в предыдущем задании.
   WITH a AS 
(SELECT DISTINCT c.id, c.name
FROM company AS c
JOIN funding_round AS f ON c.id = f.company_id
WHERE c.status = 'closed'
AND f.is_first_round = 1
AND f.is_last_round = 1
GROUP BY c.id, c.name)
SELECT DISTINCT p.id
FROM people AS p 
JOIN a ON a.id = p.company_id
