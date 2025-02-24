@startuml
actor Оператор as op
actor "Менеджер по продажам" as seller
participant MES as mes
participant "MES API" as mes_api
participant Cache as cache
participant CRM as crm
database "MES DB" as mes_db

group Запрос данных о заказах оператором
op -> mes : Запрос данных о заказах
mes -> mes_api : Запрос данных о заказах
alt Данные есть в кэше
mes_api -> cache : Запрос данных о заказах
cache -> mes_api : Информация об актуальных заказах
else Данных нет в кэше
mes_api -> cache : Запрос данных о заказах
cache -> mes_db : Запрос данных о заказах
mes_db -> cache : Информация об актуальных заказах
cache -> mes_api : Информация об актуальных заказах
end alt

mes_api -> mes : Информация об актуальных заказах
mes -> op : Информация об актуальных заказах
end

group Взятие заказа в работу оператором
op -> mes : Запрос на взятие заказа в работу
mes -> mes_api : Запрос на взятие заказа в работу
mes_api -> cache : Обновление статуса заказа на MANUFACTURING_STARTED
cache -> mes_db : Обновление статуса заказа на MANUFACTURING_STARTED
mes_api -> mes : Ответ об успешности взятия заказа в работу
mes -> op : Ответ об успешности взятия заказа в работу
end

group Подтверждение заказа менеджером
seller -> crm : Подтверждает заказ
crm -> mes_api : Подтверждение заказа
mes_api -> cache : Обновление статуса заказа на MANUFACTURING_APPROVED
cache -> mes_db : Обновление статуса заказа на MANUFACTURING_APPROVED
end

@enduml