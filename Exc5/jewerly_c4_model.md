@startuml
actor Оператор as op
actor "Менеджер по продажам" as seller
participant MES as mes
participant "MES API" as mes_api
participant Redis as redis
participant CRM as crm
database "MES DB" as mes_db

group Приложение запускается
mes_api -> mes_db : Запрос данных о заказах в статусе MANUFACTURING_APPROVED
mes_db -> mes_api : Данные о заказах
mes_api -> redis : Обновление кэша
end

group Запрос данных о заказах оператором
op -> mes : Запрос данных о заказах
mes -> mes_api : Запрос данных о заказах
mes_api -> redis : Запрос данных о заказах
redis -> mes_api : Информация об актуальных заказах
mes_api -> mes : Информация об актуальных заказах
mes -> op : Информация об актуальных заказах
end

group Взятие заказа в работу оператором
op -> mes : Запрос на взятие заказа в работу
mes -> mes_api : Запрос на взятие заказа в работу
mes_api -> redis : Обновление статуса заказа на MANUFACTURING_STARTED
mes_api -> mes_db : Обновление статуса заказа на MANUFACTURING_STARTED
mes_api -> mes : Ответ об успешности взятия заказа в работу
mes -> op : Ответ об успешности взятия заказа в работу
end

group Подтверждение заказа менеджером
seller -> crm : Подтверждает заказ
crm -> mes_api : Подтверждение заказа
mes_api -> redis : Обновление статуса заказа на MANUFACTURING_APPROVED
mes_api -> mes_db : Обновление статуса заказа на MANUFACTURING_APPROVED
end

@enduml