# mipt-devops-flyway

    mkdir flyway-demo  
    cd ./flyway-demo

### Установка Flyway Command-line tool:

    wget -qO- https://repo1.maven.org/maven2/org/flywaydb/flyway-commandline/7.8.1/flyway-commandline-7.8.1-linux-x64.tar.gz | tar xvz && sudo ln -s pwd/flyway-7.8.1/flyway /usr/local/bin 

### Установка и настройка Spawn:

    curl -sL https://run.spawn.cc/install | sh  
    export PATH=$PATH:$HOME/.spawnctl/bin  
    spawnctl version  
    spawnctl auth  
    spawnctl onboard

    spawnctl create data-container \  
      --image postgres:flyway-getting-started \  
      --name flyway-container \  
      --lifetime 24h

    "Data container 'flyway-container' created!  
    -> Host=instances.spawn.cc;Port=xxxxx;Username=xxxx;Database=foobardb;Password=xxxxxxxxx"
  
#### Получить данные для соединения можно так же следующей командой:

    spawnctl get data-container flyway-container -o yaml
    
### Добавим в конфигурационный файл данные для соединения с базой данных:

    cd flyway-7.8.1  
    vim ./conf/flyway.conf

    flyway.url=jdbc:postgresql://instances.spawn.cc:<Port>/foobardb  
    flyway.user=<User>  
    flyway.password=<Password> 

### Создадим миграцию базы данных:

    cd ./sql  
    vim V1__Create_student_table.sql

    create table STUDENT (  
      ID int not null,  
      NAME varchar(100) not null,  
      SURNAME varchar(100) not null  
    );  

### Применим миграцию:

    cd ../  
    flyway migrate

    psql -h instances.spawn.cc -p <Port> -U <User>

    <User>=# \l  
    <User>=# \c foobardb  
    foobardb=# \d flyway_schema_history  
    foobardb=# \d student  
    foobardb=# SELECT *  
    foobardb-# FROM flyway_schema_history;  
    foobardb=# exit
  
### Добавим еще одну миграцию:

    cd ./sql  
    vim V2__Add_students.sql

    insert into STUDENT (ID, NAME, SURNAME) values (1, 'Alexey', 'Kozhevnikov');  
    insert into STUDENT (ID, NAME, SURNAME) values (2, 'Elizaveta', 'Dobraya');  
    insert into STUDENT (ID, NAME, SURNAME) values (3, 'Petr', 'Ivanov');

    cd ../  
    flyway migrate

    psql -h instances.spawn.cc -p <Port> -U <User>

    <User>=# \l  
    <User>=# \c foobardb  
    foobardb=# SELECT *  
    FROM flyway_schema_history;  
    foobardb=# SELECT *  
    FROM student;  
    foobardb=# exit

### Вернуть БД к первоначальному состоянию:

    flyway clean

    sql -h instances.spawn.cc -p 32683 -U spawn_admin_uBsj

    spawn_admin_uBsj=# \c foobardb  
    foobardb=# \d student

    "Did not find any relation named "student".
  
