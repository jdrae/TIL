# M:N relation to one row

**WHAT I HAVE**

```sql
mysql> select * from recipe_ingredients limit 5;
+-----+-----------+---------------+-------+
| id  | recipe_id | ingredient_id | grams |
+-----+-----------+---------------+-------+
| 161 |        24 |           116 |   100 |
| 162 |        24 |           117 |     2 |
| 163 |        24 |           118 |     3 |
| 164 |        24 |           119 |   200 |
| 165 |        24 |           120 |     8 |
+-----+-----------+---------------+-------+

```

**WHAT I WANT**

`mysql > select * from recipes;`

| id   | name | ingredients |
| ---- | ---- | ----------- |
| 24   | 칼륨 듬뿍 고구마죽 | 고구마, 설탕, 찹쌀가루, 물, 잣 |
| 25   | 누룽지 두부 계란죽 | 애호박, 표고버섯, 당근, 누룽지, 순두부, 달걀, 참기름, 소금, 참깨, 흰 후추 |
| 26   | 오색지라시 스시 | 밥, 식초, 설탕, 소금, 달걀노른자, 표고버섯, 새송이버섯, 새우, 홍피망, 청피망, 양파, 대두유, 참기름, 식용유 |
| 27   | 두부 곤약 나물 비빔밥 | 두부, 흰쌀, 현미쌀, 찹쌀, 실곤약, 콩나물, 표고버섯, 애호박, 고사리, 당근, 소금, 초고추장, 플레인요거트, 참기름, 새싹채소 |



## 1. Join three tables and Create new one

 [Joining three tables using MySQL](https://stackoverflow.com/questions/3709560/joining-three-tables-using-mysql)

[MySQL - How to create a new table that is a join on primary key of two existing tables](https://stackoverflow.com/questions/2112043/mysql-how-to-create-a-new-table-that-is-a-join-on-primary-key-of-two-existing)

```sql
create table ri_test as
(select ri.id, ri.recipe_id, ri.ingredient_id, r.name as recipe_name, i.name as ingredient_name
from ingredients i
	inner join recipe_ingredients ri on i.id = ri.ingredient_id
	inner join recipes r on ri.recipe_id = r.id
order by ri.id);
```

```sql
mysql> select * from ri_test limit 5;
+-----+-----------+---------------+----------------------------+-----------------+
| id  | recipe_id | ingredient_id | recipe_name                | ingredient_name |
+-----+-----------+---------------+----------------------------+-----------------+
| 161 |        24 |           116 | 칼륨 듬뿍 고구마죽         | 고구마          |
| 162 |        24 |           117 | 칼륨 듬뿍 고구마죽         | 설탕            |
| 163 |        24 |           118 | 칼륨 듬뿍 고구마죽         | 찹쌀가루        |
| 164 |        24 |           119 | 칼륨 듬뿍 고구마죽         | 물              |
| 165 |        24 |           120 | 칼륨 듬뿍 고구마죽         | 잣              |
+-----+-----------+---------------+----------------------------+-----------------+

```

## 2. Concat ingredient rows by recipe id

[Can I concatenate multiple MySQL rows into one field?](https://stackoverflow.com/questions/276927/can-i-concatenate-multiple-mysql-rows-into-one-field)

```sql
create table recipes_test as
(
select r.id,r.name, ri.ingredients from recipes r
left join (
select recipe_id, GROUP_CONCAT(ingredient_name SEPARATOR ', ') as ingredients
from ri_test
group by recipe_id
) ri
on r.id = ri.recipe_id
);
```

```
mysql> select * from recipes_test limit 5;
+----+------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| id | name                                           | ingredients                                                                                                                                                            |
+----+------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| 24 | 칼륨 듬뿍 고구마죽                             | 고구마, 설탕, 찹쌀가루, 물, 잣                                                                                                                                         |
| 25 | 누룽지 두부 계란죽                             | 애호박, 표고버섯, 당근, 누룽지, 순두부, 달걀, 참기름, 소금, 참깨, 흰 후추                                                                                              |
| 26 | 오색지라시 스시                                | 밥, 식초, 설탕, 소금, 달걀노른자, 표고버섯, 새송이버섯, 새우, 홍피망, 청피망, 양파, 대두유, 참기름, 식용유                                                             |
| 27 | 두부 곤약 나물 비빔밥                          | 두부, 흰쌀, 현미쌀, 찹쌀, 실곤약, 콩나물, 표고버섯, 애호박, 고사리, 당근, 소금, 초고추장, 플레인요거트, 참기름, 새싹채소                                               |
| 28 | 저염 간장을 이용한 닭개장 비빔밥               | 쌀, 검은 쌀, 닭가슴살, 후추, 숙주, 토란대, 고사리, 달걀, 대파, 들기름, 실고추, 소금, 들깨가루, 고추기름, 꿀, 저염간장                                                  |
+----+------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

```



