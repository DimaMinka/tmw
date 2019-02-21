# tmw - Пробуем наш workflow на VDS от TimeWeb - CDK
![timeweb vds intro](https://user-images.githubusercontent.com/12497991/53172230-7fd99d00-35ed-11e9-84f6-225339c4f2dd.jpg)
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

## Создание проекта:
Я думаю большенство работают через панель, но я люблю автоматизировать процесс, мы не так давно создали shell wrapper для API Runcloud и теперь все это возможно:
1. Создаю пользователя на сервере под проект для изоляции - `rcdk sysusers create tmw`
2. Запускаю наш bundler, который запросит нужные данные о проекте, как имя для базы, юзера для проекта, стэк проекта, ssl etc. - `rcdk bundle`

*проект готов вместе с базой и нужными настройками*
![app list](https://user-images.githubusercontent.com/12497991/53124997-83c1dc80-3565-11e9-8b84-f34269692726.png)

Теперь я подключу свой репозиторий к GIT проекта на Runcloud, тут к сожелению надо руками будет создать ключ через веб панель для моего пользователя, и добавить его в репозиторий.

*генерация ключа для GitHub repo*
![deployment key](https://user-images.githubusercontent.com/12497991/53125901-ae149980-3567-11e9-84c2-64a677154695.png)

*подключение репозитория к проекту*
![runcloud app to git](https://user-images.githubusercontent.com/12497991/53126044-fa5fd980-3567-11e9-901b-8cd7d37ed42f.png)

На этом этапе код уже был переброшен к нам на сервер, и пришла пора собирать сам проект.

## Настройка Atomic Deploy:
Atomic deployment позволят нам собирать проекты с мульти задачностью, логами, различными конфигами и симлинками. 
1. Перейду в менюшке в Atomic deploy и создам новый процесс:

*создаем atomic deploy*
![create atomic deployment](https://user-images.githubusercontent.com/12497991/53126641-495a3e80-3569-11e9-9ff1-73f16d379013.png)
![atomic config](https://user-images.githubusercontent.com/12497991/53126983-0f3d6c80-356a-11e9-9700-972f3029494b.png)

2. Далее сам процесс конфига проекта через .env и custom-config.yml, тут будут указаны какие плагины/темы ставить и на какие проекты, данные базы и много другое.

*секция настройки конфига*
![atomic config](https://user-images.githubusercontent.com/12497991/53127238-aacedd00-356a-11e9-9348-82ec2099bb08.png)

3. Сам процесс сборки проекта будет настроен в deployment script, там тоже мы поколдовали, и надо лишь запусть `bash wpi.sh`

*в зависимости от сложности проекта создаются разные конфиги сборки*
![atomic deployment scripts](https://user-images.githubusercontent.com/12497991/53128025-7f4cf200-356c-11e9-94d3-c09a7a5ff2f5.png)

Процесс был запущен и сервер спулил последнюю версию кода и собрал весь проект, настроил WordPress по заданным сетапам в конфиге, и теперь можно уже логинится в админку.

*процесс сборки с логами*
![atomic config](https://user-images.githubusercontent.com/12497991/53128025-7f4cf200-356c-11e9-94d3-c09a7a5ff2f5.png)

## Процесс настройки WordPress и плагинов:
1. Активация всего добра, что было установлено

*так выглядит конфиг проекта*
![custom yml config](https://user-images.githubusercontent.com/12497991/53128501-a1933f80-356d-11e9-84fb-a0827c25cad9.png)

2. Далее произведем нужные настройки во всех плагина, кеш, cloudflare, сео, вуу, закинем демо данные и в принципе сайт готов для тестов.

## Похайпим скоростью:
Я прекрасно понимаю, что сервисы по проверкам скорости загрузки сайта это то еще дело, но клиенты любят видеть цифры, а еще больше требовать. Поэтому на завершение мы так же пробежимся по списку популярных:
* https://tools.pingdom.com
* https://gtmetrix.com
* https://www.webpagetest.org
* https://developers.google.com/speed/pagespeed/insights/

Важно заметить, что были выполнены базовые настройки по оптимизации, и всегда есть над чем поработать и что доработать, но мне для обзора хватило с головой!

*tools.pingdom.com*
![tools.pingdom.com - germany](https://user-images.githubusercontent.com/12497991/53129247-c983a280-356f-11e9-8201-ec3e54ef38b9.png)

*developers.google.com/speed/pagespeed/insights*
![google page speed insights](https://user-images.githubusercontent.com/12497991/53129536-7827e300-3570-11e9-972c-002521126798.png)

*gtmetrix.com*
![gtmetrix.com - canada](https://user-images.githubusercontent.com/12497991/53129719-fe442980-3570-11e9-920b-eb933f69c06a.png)

*www.webpagetest.org*
![www.webpagetest.org - pragua](https://user-images.githubusercontent.com/12497991/53130098-0486d580-3572-11e9-9331-5604143237ac.png)

## Итог:
В общем первое знакомство с VDS timeweb прошло более, чем приятно. Все очень удобно, быстро, просто.
На сборку было потрачено не больше часа вместе с базовой настройкой оптимизации, стресс тесты не производил, так как это требует еще времени, которого нет. 
Хочу пожелать ребятам с timeweb продолжать радовать сообщество WordPress новыми продуктами и сервисами, хотелось бы больше автоматизации, API, интересных сборок под WordPress и конечно удачи! :)

## Hello World!
