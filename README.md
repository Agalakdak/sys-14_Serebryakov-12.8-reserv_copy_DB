# Домашнее задание к занятию 12.8. «Резервное копирование баз данных» - Серебряков Руслан

### Задание 1. Резервное копирование

### Кейс
Финансовая компания решила увеличить надёжность работы баз данных и их резервного копирования.

Необходимо описать, какие варианты резервного копирования подходят в случаях:

1.1. Необходимо восстанавливать данные в полном объёме за предыдущий день.

1.2. Необходимо восстанавливать данные за час до предполагаемой поломки.

1.3.* Возможен ли кейс, когда при поломке базы происходило моментальное переключение на работающую или починенную базу данных.

*Приведите ответ в свободной форме.*

---
1.1 Я думаю, что в данном кейсе подойдёт дифференциальное копирование с созданием полной копии в конце или начале каждой рабочей недели.

1.2 Самый оптимальный вариант это инкрементное резервное копирование т.к. оно будет занимать минимум места (относительно других типов резервного копирования). Важно также не создавать только инкрементыне копии т.к. стоит получить повреждение 1 такого файла и все предыдущие копии будут бесполезны. 

1.3 В настоящее время конечно возможен. Но потребуется дополнительный сервер. Структура будет выглядеть следующим образом: 1 master-сервер и 1 slave-сервер. С master осуществляется основная работа, а slave в это время копирует информацию с него. В случае отказа master - система переключается на slave-сервер и уже работает с ним как с новым master-сервером. Далее останется толко разобраться почему отказал основной master и переключить его либо обратно в master либо в slave 

### Задание 2. PostgreSQL

2.1. С помощью официальной документации приведите пример команды резервирования данных и восстановления БД (pgdump/pgrestore).

2.1.* Возможно ли автоматизировать этот процесс? Если да, то как?

*Приведите ответ в свободной форме.*

---

2.1 Если я всё правильно понял, то для создания дампа потребуется ввести команду "pg_dump имя_базы > файл_дампа".
Для восстановления - потребуется ввести "psql имя_базы < файл_дампа".
Для ответа использовалась эта документация "https://postgrespro.ru/docs/postgresql/15/backup-dump"

2.2 На сколько я понял - создание бэкапа осуществляется при помощи одной команды, а значит можно просто в системе настроить задачу в каком-нибудь cron на создание резервной копии по расписанию (например ночью, когда база не нагружена). 

### Задание 3. MySQL

3.1. С помощью официальной документации приведите пример команды инкрементного резервного копирования базы данных MySQL.

3.1.* В каких случаях использование реплики будет давать преимущество по сравнению с обычным резервным копированием?

*Приведите ответ в свободной форме.*

---

3.1 В документации это было сложно найти, по этому я воспользовался сторонними ресурсами :>
Для создания инкреметной резервной копии - используется программа xtrabackup. Ставится она путём добавления репозитория, с последующей установкой percona-xtrabackup-80.
Для примера представлена команда для полного бэкапа "xtrabackup --backup --user=root --password='R(zDXcVUmI[zwx%aNBTN' --target-dir=/root/backupdb/full"
Далее проверяем целостность данных "xtrabackup --prepare --target-dir=/root/backupdb/full"

Инкрементный бэкап "xtrabackup --backup -u root -p "pass" --target-dir=/root/backupdb/inc1 --incremental-basedir=/root/backupdb/full"

3.1 По идее преимущества будут в очень многих случаях т.к. в случае отказа основного сервера - можно переключиться на запасной. Единственное - это дороже т.к. необходимо постоянно поддерживать дополнительный сервер.



Задания, помеченные звёздочкой, — дополнительные, то есть не обязательные к выполнению, и никак не повлияют на получение вами зачёта по этому домашнему заданию. Вы можете их выполнить, если хотите глубже шире разобраться в материале.
