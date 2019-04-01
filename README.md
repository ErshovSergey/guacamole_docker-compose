# Проект для запуска [Apache Guacamole](https://guacamole.apache.org/) в контейнерах docker
Используется docker-compose

## Устанавливаем и запускаем Apache Guacamole в контейнеры docker
Всего 3 контейнера (подробнее [Installing Guacamole with Docker](https://guacamole.apache.org/doc/gug/guacamole-docker.html)):  
- guacamole/guacd  
- guacamole/guacamole  
- mysql  

Параметры для запуска настраиваются в файле *.env* (переименовать *.env-sample* в *.env*)  

### Запуск  
```
docker-compose up --build -d --remove-orphans --force-recreate
```

### Остановка  
```
docker-compose down --remove-orphans
```

### Если при запуске "белый экран" вместо авторизации  
То наверное это первый запус и надо инициализировать БД, для этого  
#### Получаем из контейнера с *guacamole*  скрипт для инициализации БД  
```
docker exec -i -t guacamole_guacamole bash -c "/opt/guacamole/bin/initdb.sh --mysql > /init.sql/initdb.sql"
```
#### Применяем скрипт к контейнеру *mysql*  
```
docker exec -i -t guacamole_mysql bash -c 'mysql -uroot -p${MYSQL_ROOT_PASSWORD} ${MYSQL_DATABASE} < /init.sql/initdb.sql'
```

### Логин и пароль по умолчанию *guacadmin*  