# tmw - Пробуем наш workflow на VDS от TimeWeb - CDK

## Интро:
Знакомство с timeweb у меня произошло много лет назад, когда лишь начинал изучать веб разработку, и на самом деле все понравилось, но так как мои клиенты не из стран СНГ, работать вплотную мне так и не удалось. Но не так давно в RU сообществе WordPress, уважаемый мной человек Дмитрий (@beyondkayne), поведал о VDS timeweb, и предложил накидать свой конфиг, и я с радостью согласился. 

## Выбор конфига для WordPress + WooCommerce:
Для начала был приятно удивлен тем, что все конфигурации начинаются с двух ядер, может я что то и пропустил, но с одним ядром я и не нашел, а искал потому, что мне для простой установки два ядра и не надо. Дальше мне предложили взять 2 гигабайта виртуально памяти, от чего я отказался, так как для нескольких проектов мне и одного с головой, плюс для своих клиентов я всегда начинаю с 1 гигабайта. С диском не запаривался, логично что везде сегодня используют SSD, и в размере тоже не обделят. 

## Выбор OS - Ubuntu 18.04:
Уже давно работаю с этой OS, много использовал для изучения и экспериментов разные стек сервисы такие как:
* EasyEngine
* Serverpilot
* Runcloud
* Moss
* Custom configs
Плюс в последнее время мы почти на всех серверах гоняем Runcloud, и Ubuntu нам подходит больше всех.

## Server stack:
Что мы обычно используем на наших VDS:
* Nginx
* PHP72/PHP73
* MariaDB
* Redis
* GIT/Atomic Deploy
* Let's Encrypt
* Fail2ban etc
Все это и даже больше есть из коробки Runcloud, что мы и установим.

## Установка:
В принципе кроме подключения через root к серваку, после создания сервера с образом Ubuntu, ничего и не надо. 
1. Подключаемся через ssh к серверу
2. Запускаем скрипт установки конфига

*конфиг получаем в веб панели Runcloud*
![server init](https://user-images.githubusercontent.com/12497991/53122535-7e619380-355f-11e9-910f-6e35681f2a3e.png)

Сам процесс забирает несколько минут, после завершения, я запускаю скритп с дополнительными пакетами и сервисами:
* [nano syntax highlighting](https://github.com/scopatz/nanorc)
* [wp cli](https://wp-cli.org)
* [adminer](https://github.com/vrana/adminer) installer - run from your app public directory  `adminer.sh`
* [srdb](https://github.com/interconnectit/Search-Replace-DB) installer - run from your app public directory  `srdb.sh`
* [zImageOptimizer](https://github.com/zevilz/zImageOptimizer) - Simple bash script for lossless image optimizing - `zio.sh` 
* [rcdk](https://github.com/RunCloud-cdk/shell-api-wrapper) - Runcloud shell api wrapper - `rcdk` 
* [jq](https://github.com/stedolan/jq) - jq is a lightweight and flexible command-line JSON processor
* [yarn](https://yarnpkg.com/en) - yarn - fast, reliable, and secure dependency management
* [gulp](https://gulpjs.com) - gulp is a toolkit for automating painful or time-consuming tasks in your development workflow
* [rocket-nginx](https://github.com/maximejobin/rocket-nginx) - Rocket-Nginx is a Nginx configuration for the WordPress cache plugin WP-Rocket.

*сервер готов и можно приступать к созданию проекта*
![server dashboard](https://user-images.githubusercontent.com/12497991/53122973-a9001c00-3560-11e9-9286-53406ed6f48c.png)
