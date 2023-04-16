# DML: агрегация и сортировка, CTE, аналитические функции  
#### 1. Создайте таблицу и наполните ее данными  
<image src="https://github.com/ArinichElena/DML_2/blob/main/create%20table.png">

#### 2. Заполнить данными
<image src="https://github.com/ArinichElena/DML_2/blob/main/insert.png">

#### 3. Написать запрос суммы очков с группировкой и сортировкой по годам
```sql
select
  year_game,
  sum(points) as sum_points
from
  statistic
group by
  year_game
order by
  year_game;
```
<image src="https://github.com/ArinichElena/DML_2/blob/main/group%20by.png">

```sql
select 
	player_name,
	year_game, 
	sum(points)
from 
	public.statistic
group by rollup (player_name, year_game)
order by 
	player_name,
	year_game;
```
<image src="https://github.com/ArinichElena/DML_2/blob/main/rollup.png">
	
#### 4. Написать cte показывающее тоже самое
```sql
with sum_player as
(
	select
		player_name,
		sum(points) as sum_points
	from
		public.statistic
	group by
		player_name
),
sum_total as (
	select
		sum(points) as sum_points
	from
		public.statistic
),
base_total as (
select
	player_name,
	year_game,
	points
from 
	public.statistic
union all 
select
	player_name,
	null,
	sum_points
from 
	sum_player
union all 
select
	null,
	null,
	sum_points
from 
	sum_total)
select
	player_name,
	year_game,
	points
from 
	base_total
order by
	player_name,
	year_game
```
<image src="https://github.com/ArinichElena/DML_2/blob/main/cte.png">
	
#### 5. Используя функцию LAG вывести кол-во очков по всем игрокам за текущий код и за предыдущий.
```sql
select
	player_name,
	year_game,
	points,
	lag(points) over (partition by player_name order by year_game)
from 
	public.statistic;	
```
<image src="https://github.com/ArinichElena/DML_2/blob/main/lag.png">
