# Работа с требованиями, разделение системы на элементы

### Event Storming модель проекта

![es](Event%20Storming.png)

В ходе построения ES-модели у меня получились следующие контексты:
- Создание заявки
- Комплектация заявки
- Формирование пула воркеров
- Подготовка воркерка для работы
- Просмотр заявки
- Выполнение заявки
- Управление статусом заявок
- Оплата
- Проверка качества
- Система ставок

Команды и события логическим образом сложились в определенные домены, по которым и формировал контексты. 

При чем при первой итерации контекстов было гораздо больше. Так у меня присутствовал контекст для отмены заказа, где воркер был актором, а его команда "пропуск заказа" запускала весь процесс создания заявки почти с начала. В итоге, рассмотрев взаимодействия между события и командами, решил объединить 2 контекста в один "создание заявки". 

Важно отметить, что нотификации по почте присутствуют почти в каждом контексте, где они важны. Сознательно не стал выносить их в отдельный, так как они представляют один цельный бизнес домен. 

### Модель данных для системы

![dm](Data%20Modeling.png)

### Общая модель всех полученных коммуникаций в системе

![es+dm](ES%20+%20DM.png)

### Реализация проекта

Так как проект собирается с нуля, нет готовой инфраструктуры и неизвестно сколько человек (команд) будет работать над проектом, то Монолит выглядит предпочтительнее. 

К тому же с текущими требованиями непонятно как возможно разделить архитектуру по микросервисам, чтобы каждому обеспечить изолированную базу данных, а синхронизация состояний между БД при текущих данных может оказаться неоправданно дорогим решением. 

Выглядит так, что с текущим состоянием дел (по крайней мере на первой стадии проекта) Монолит разделенный на модули выглядит оптимальным решением.

Микросервисная архитектура не подходит для быстрого развертывания проекта, так как требует в момент гораздо больше ресурсов на первоначальное проектирование системы и ее дальнейшую поддержку. 

### Спорные места в пользовательских историях

1. Совершенно непонятно почему статусы задачи может менять только клиент[US-030]. Это выглядит нелогично и приходится систему подстраивать под эти требования, иначе архитектура получилась бы другой.
2. Требования не уточняют кому нужно знать о том, что кто-то и за что-то начислил деньги, а от этого может зависеть реализация [US-240]
3. Можем ли менять состав заявки после расчета стоимости, если клиента она не устроит, например? [US-020]
4. Кто обрабатывает результаты теста? [US-100]
5. Система ставок выглядит сомнительно. Есть предложение перевести ее целиком в тетрадку менеджера, так как частино она все равно там живет [US-250]
6. Может ли клиент иметь несколько заказов одновременно?