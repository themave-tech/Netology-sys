# `Домашнее задание к занятию 12.1 "Базы данных"` - `Болдыш Леонид`
### Задание 1

1. `Сотрудники`
2. `Должности`
3. `Подразделения`
4. `Структурное подразделение`
5. `Города`
6. `Области`
7. `Адрес филиала`
8. `Проект`
9. `Заказчики`

```
Сотрудники (
    идентификатор первичный ключ serial,
    фамилия varchar(50) not null,
    имя varchar(50) not null,
    отчество varchar(50),
    оклад varchar(30) money,
    дата_найма date,
    идентификатор структурного подразделения, внешний ключ, integer
    идентификатор подразделения, внешний ключ, integer
    идентификатор должности, внешний ключ, integer
)
```
```
Должности (
    идентификатор первичный ключ serial,
    категория varchar(50),
    должность varchar(50) not null,
    )
```
```
Подразделения (
    идентификатор первичный ключ serial,
    тип_подразделения varchar(50),
    )
```
```
Структурные_подразделения(
    идентификатор первичный ключ serial,
    структурное_подразделение varchar(50) not null,
    идентификатор подразделения, внешний ключ, integer
)
```
```
Города(
    идентификатор первичный ключ serial,
    название_города varchar(50) not null,
    идентификатор область, внешний ключ, integer
)
```
```
Области(
    идентификатор первичный ключ serial,
    название_области varchar(50) not null,
    )
```
```
Адрес_филиала(
    идентификатор первичный ключ serial,
    идентификатор города, внешний ключ integer,
    идентификатор области, внешний ключ integer,
    идентификатор сотрудники, внешний ключ integer,
    адрес varchar(50),
)
```
```
Проект(
    идентификатор первичный ключ serial,
    идентификатор города, внешний ключ integer,
    наименование varchar(50),
    идентификатор заказчики, внешний ключ integer,
    идентификатор структурного подразделения, внешний ключ, integer
)
```
```
Заказчики(
    идентификатор первичный ключ serial,
    наименование varchar(50),
    идентификатор города, внешний ключ integer,
    идентификатор области, внешний ключ integer,
)
```