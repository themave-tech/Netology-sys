# `Домашнее задание к занятию 12.1 "Базы данных"` - `Болдыш Леонид`
### Задание 1

1. `ФИО`
2. `Должность`
3. `Тип подразделения`
4. `Структурное подразделение`
6. `Адрес филиала`
7. `Проект`

```
CREATE TABLE employee (
    employee_id        char(5) CONSTRAINT PRIMARY KEY UNIQUE,
    employee_surname        varchar(50),
    employee_type_division        varchar (50) NOT NULL
    employee_division        varchar (50) NOT NULL
    );
```