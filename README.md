# SQL_DWH_Заполнение измерения "Товар"

```
INSERT INTO dim.product (code, name, artist, product_type, product_category, unit_price, unit_cost, status, effective_ts, expire_ts, is_current)
WITH director as (
	select distinct
film_id, first_value("name") over(partition by film_id) as director
	FROM nds.films_to_director ftd
	left join nds.films f on f.id = ftd.film_id
	left join nds.films_director fd on fd.id = ftd.director_id
	)
select 
	f.id::varchar as code,
	f.title as "name",
	coalesce(d.director, 'Неизвестно') as artist,
	'Фильм' as product_type,
	coalesce(fg.name, 'Неизвестно') as product_category,
	price as unit_price,
	"cost" as unit_cost,
	CASE
      WHEN status = 'p' THEN 'Ожидается'
      WHEN status = 'o' THEN 'Доступен'
      WHEN status = 'e' THEN 'Не продаётся'
  	END AS status,
  	f.start_ts as effective_ts,
 	f.end_ts as expire_ts,
  	f.is_current
  	FROM nds.films f
left join nds.films_genre fg on f.genre_id = fg.id
left join director d on d.film_id = f.id
```
