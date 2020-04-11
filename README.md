# Проект для запуска [Apache Guacamole](https://guacamole.apache.org/) в контейнерах docker
Используется docker-compose

## Устанавливаем и запускаем Apache Guacamole в контейнеры docker
Всего 3 контейнера (подробнее [Installing Guacamole with Docker](https://guacamole.apache.org/doc/gug/guacamole-docker.html)):  
- guacamole/guacd  
- guacamole/guacamole  
- mysql  

Параметры для запуска настраиваются в файле *.env* (переименовать *.env-sample* в *.env*)  

### Запуск  
####  
```
docker-compose up --build -d --remove-orphans --force-recreate
```
#### С использованием reverse proxy
```shell
docker-compose -f docker-compose.yml -f docker-compose_nginx-le.yml up --build -d --remove-orphans --force-recreate
```

### Остановка  
```
docker-compose down --remove-orphans
```

### Если при запуске "белый экран" вместо авторизации  
То наверное это первый запуск и надо инициализировать БД, для этого  
#### Получаем из контейнера с *guacamole*  скрипт для инициализации БД  
```
docker exec -i -t guacamole_guacamole bash -c "/opt/guacamole/bin/initdb.sh --mysql > /init.sql/initdb.sql"
```
#### Применяем скрипт к контейнеру *mysql*  
```
docker exec -i -t guacamole_mysql bash -c 'mysql -uroot -p${MYSQL_ROOT_PASSWORD} ${MYSQL_DATABASE} < /init.sql/initdb.sql'
```

### Первый вход 
Ссылка http://ip_addr/guacamole/   
Логин и пароль по умолчанию *guacadmin*  

### Второй фактор TOTP для пользователей
Необходимо поместить plugin *guacamole-auth-totp* в папку *${DATA_PATH}/extensions*
Для версии 1.0.0
```
wget http://mirror.linux-ia64.org/apache/guacamole/1.0.0/binary/guacamole-auth-totp-1.0.0.tar.gz
tar -xzvf guacamole-auth-totp-1.0.0.tar.gz
mv guacamole-auth-totp-1.0.0/guacamole-auth-totp-1.0.0.jar ${DATA_PATH}/GUACAMOLE_HOME/extensions/
rm -rf guacamole-auth-totp-1.0.0*
```

### Использование Duo
Регистрируемся на [duosecurity.com](https://duosecurity.com/).  
Для версии 1.0.0  
```
wget http://apache-mirror.rbc.ru/pub/apache/guacamole/1.0.0/binary/guacamole-auth-duo-1.0.0.tar.gz
tar -xzvf guacamole-auth-duo-1.0.0.tar.gz
mv guacamole-auth-duo-1.0.0/guacamole-auth-duo-1.0.0.jar ${DATA_PATH}/GUACAMOLE_HOME/extensions/
rm -rf guacamole-auth-duo-1.0.0*
```
Настраиваем по [Duo two-factor authentication](http://guacamole.apache.org/doc/gug/duo-auth.html).  
Файл *guacamole.properties* помещаем в ${DATA_PATH}/GUACAMOLE_HOME/.  

### Авторизация из MS Active Directory
Заполнить в *.env* параметры
```
LDAP_HOSTNAME=192.168.XX.YY
LDAP_USER_BASE_DN=DC=org,DC=company,DC=ru
LDAP_GROUP_BASE_DN=DC=org,DC=company,DC=ru
LDAP_SEARCH_BIND_DN=cn=UserName,DC=org,DC=company,DC=ru
LDAP_SEARCH_BIND_PASSWORD=PassUserForUserName
LDAP_USERNAME_ATTRIBUTE=samaccountname
```

### Изменение логотипа и надписи
Поместить *branding.jar* в *<GUACAMOLE_HOME>/extensions/*  
Надпись меняется в *branding\translations\en.json*.  
Логотип - файл *branding\images\logo-placeholder.png*.  
По [мотивам](https://github.com/Zer0CoolX/guacamole-customize-loginscreen-extension)

### Обратный прокси и сертификаты LE
Для защиты канала можно использовать обратный прокси из контейнера [umputun/nginx-le](https://github.com/nginx-le/nginx-le) и basic авторизацию.  
Для этого создать файл *.htpasswd* и поместить в *${DATA_PATH}/nginx-le/etc/*.  
Скопировать *nginx-le/service.conf* в *${DATA_PATH}/nginx-le/etc/*.  
Заполнить параметры в *.env*.  
```
LE_FQDN=fqdn.domain.spb.ru,fqdn2.ru
LE_EMAIL=user@fqdn.domain.spb.ru
# timezone
TZ=Europe/Moscow
# адрес и порт на котором будет отвечать nginx, обязателен должен быть доступен из интернет
LETS_HTTP_IP_PORT=xx.yy.zz.aa:80
LETS_HTTPS_IP_PORT=xx.yy.zz.aa:443
```
### Обновить образы контейнеров
```
docker-compose pull
```
