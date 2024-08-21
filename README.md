# infra_sprint1
Kittygram — социальная сеть для обмена фотографиями любимых питомцев. Это проект, который состоит из бэкенд-приложения на Django и фронтенд-приложения на React.
# Как развернуть проект на сервере:
Для подключения к Вашему серверу по протоколу SSH Вам понадобятся следующие данные:
* Получите доменное имя, по которому будет доступно приложение.(Например, kittydo.zapto.org)
* login для подключения к серверу;
* ip - IP-адреc сервера, а также желательно доменное имя;
* passphrase - пароль от закрытого SSH-ключа;
* файл с расширением .pub — открытый SSH-ключ;
* файл без расширения — закрытый SSH-ключ.

Если вы работаете под ОС Linux или macOS, то у вас наверняка есть SSH-клиент, он по умолчанию встроен в терминал.
Пользователям Windows мы рекомендуем установить Git Bash версии 2.36.0 или выше. SSH-клиент встроен в этот терминал.
Чтобы убедиться, что SSH-клиент у Вас есть, откройте терминал и введите команду:
```
ssh -V
```
Для подключения  к серверу используйте команду:
```
ssh -i путь_до_файла_с_SSH_ключом/название_файла_закрытого_SSH-ключа login@ip
```
## Подключить удаленный сервер к аккаунту на GitHub
Установить на сервере git, используя команду:
```
sudo apt install <имя_пакета>
```
или проверьте, что git уже установлен:
```
sudo apt update
git --version
```

Находясь на сервере, сгенерируйте пару SSH-ключей, выведите публичный ключ в консоль, скопируйте его и запишите в настройки вашего аккаунта на GitHub.
(можно по [инструкции](https://file.notion.so/f/f/1c00a917-6a50-4d8a-a607-d24904249cd0/cce9043b-0706-44bc-9730-53923cef05ab/Настройка_SSH_для_GitHub.pdf?table=block&id=2ccd2557-d585-4e6d-980b-b0a3319229cd&spaceId=1c00a917-6a50-4d8a-a607-d24904249cd0&expirationTimestamp=1724284800000&signature=FpJxT18lS-aTUEcG6PS41N-sm05oMkufeXhAAnlMZjc&downloadName=Настройка+SSH+для+GitHub.pdf))

### Клонируйте код приложения с GitHub на сервер:
Находясь на сервере, выполните команду:
```
git clone git@github.com:Ваш_аккаунт/taski.git
```

1. Установливаем на сервер необходимые компоненты: интерпретатор Python, менеджер пакетов pip, утилиту для создания виртуального окружения venv. Если Ваш сервер работает под управлением операционной системы Ubuntu, в неё предустановлен интерпретатор Python третьей версии. Убедитесь в этом — находясь на сервере, выполните команду:

```
python3 -V
```
Далее установите на сервер пакетный менеджер и утилиту для создания виртуального окружения. Сделать это можно одной командой, перечислив все необходимые к установке пакеты:

```
sudo apt install python3-pip python3-venv -y 
```

2. Установить зависимости из файла requirements.txt:
```
# Переходим в директорию backend-приложения проекта.
cd taski/backend/
# Создаём виртуальное окружение.
python3 -m venv venv
# Активируем виртуальное окружение.
source venv/bin/activate
# Обновляем pip в виртуальном окружении
pip install --upgrade pip
# Устанавливаем зависимости.
pip install -r requirements.txt
```
3. Выполнить миграции и создайте суперпользователя:
```
# Применяем миграции.
python manage.py migrate
# Создаём суперпользователя.
python manage.py createsuperuser
```
## Запускаем бекэнд
Чтобы проект запустился через внешний интерфейс, нужно явно добавить IP-адрес вашего сервера в «список разрешённых хостов» ALLOWED_HOSTS в файл settings.py:
```
# Открываем файл settings.py
nano settings.py
```
В список ALLOWED_HOSTS добавьте:
внешний IP-адрес вашего сервера для доступа к приложению по внешнему интерфейсу,
адреса 127.0.0.1 и localhost для доступа к приложению по внутреннему интерфейсу:
```
# Вместо xxx.xxx.xxx.xxx укажите IP вашего сервера.
ALLOWED_HOSTS = ['xxx.xxx.xxx.xxx', '127.0.0.1', 'localhost'] 
```
Запускаем сервер командой:
```
python manage.py runserver 0:9000
```
Теперь можно перейти в админку по ссылке http://ваш_публичный_IP:8000/admin/ и авторизоваться от имени созданного вами суперпользователя или можно перейти по ссылке http://ваш_публичный_IP:8000/api/, чтобы открыть встроенный интерфейс отладки Browsable API.
Останавливаем бекэнд сервер.

## Запускаем фронтенд
Фронтенд-приложение проекта Taski написано на React. Чтобы его запустить, нужно:
* Установить на сервер пакетный менеджер npm. Самый простой способ начать работу с npm — это установить на сервер Node.js, с которой менеджер пакетов идёт в комплекте. Находясь на сервере, из любой директории выполните команду (скопируйте её целиком и вставьте в терминал):
```
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash - &&\
sudo apt install -y nodejs
```
После установки Node.js сервер, скорее всего, снова попросит перезагрузить операционную систему. Перезагружайте её и двигайтесь дальше.
Далее убедитесь, что пакетный менеджер npm тоже установился. Выполните команду:
```
npm -v
```
В ответ должна вывестись версия пакетного менеджера.
* Установить зависимости для фронтенд-приложения. Перейдите в директорию taski/frontend/ и выполните команду:
```
npm i
```
Запустите приложение командой:
```
npm run start
```
проверяем по по ссылке http://ваш_публичный_IP:3000 и останавливаем фронтенд сервер.
## Устанавливаем и запускаем Gunicorn
На удалённом сервере при активированном виртуальном окружении проекта Taski установите пакет gunicorn:
```
pip install gunicorn==20.1.0
```
Перейдите в директорию с файлом manage.py (infra_sprint1/backend) и запустите Gunicorn:
```
gunicorn --bind 0.0.0.0:9000 kittygram_backend.wsgi
```
Создаём юнит для Gunicorn. В директории /etc/systemd/system/ создайте файл gunicorn_kittygram.service и откройте его в Nano. Для создания файла в системной папке нужны права администратора, их даёт команда sudo. Выполните команду:
```
sudo nano /etc/systemd/system/gunicorn_kittygram.service 
```
В файле gunicorn_kittygram.service опишите конфигурацию процесса. 
Подставьте в код из листинга свои данные, добавьте этот код без комментариев в файл gunicorn_kittygram.service и сохраните изменения:
```
[Unit]
# Это текстовое описание юнита, пояснение для разработчика.
Description=gunicorn_kittygram daemon 

# Условие: при старте операционной системы запускать процесс только после того, 
# как операционная система загрузится и настроит подключение к сети.
# Ссылка на документацию с возможными вариантами значений 
# https://systemd.io/NETWORK_ONLINE/
After=network.target 

[Service]
# От чьего имени будет происходить запуск:
# укажите имя пользователя, под которым вы подключались к серверу по ssh.
User=yc-user 

# Путь к директории проекта:
# /home/<имя-пользователя-в-системе>/
# <директория-с-проектом>/<директория-с-файлом-manage.py>/.
# Например:
WorkingDirectory=/home/yc-user/infra_sprint1/backend/

# Команду, которую вы запускали руками, теперь будет запускать systemd:
# /home/<имя-пользователя-в-системе>/
# <директория-с-проектом>/<путь-до-gunicorn-в-виртуальном-окружении> --bind 0.0.0.0:9000 kittygram_backend.wsgi
ExecStart=/home/yc-user/taski/backend/venv/bin/gunicorn --bind 0.0.0.0:9000 kittygram_backend.wsgi

[Install]
# В этом параметре указывается вариант запуска процесса.
# Значение <multi-user.target> указывают, чтобы systemd запустил процесс,
# доступный всем пользователям и без графического интерфейса.
WantedBy=multi-user.target
```
Для просмотра процессов вопользуемся командой:
```
sudo systemctl
```
Чтобы запустить, остановить или перезапустить процесс, используется команда sudo systemctl с параметрами start, stop или restart.
Запустите процесс gunicorn_kittygram.service:
```
sudo systemctl start gunicorn_kittygram
```
Дополнительной командой добавьте процесс Gunicorn в список автозапуска операционной системы на удалённом сервере:
```
sudo systemctl enable gunicorn_kittygram
```
После небольшого ожидания можно будет проверить работоспособность запущенного демона. Для этого воспользуйтесь командой:
```
sudo systemctl status gunicorn_kittygram
```
 Вы установили и настроили WSGI-сервер Gunicorn: он будет запускаться при старте операционной системы на удалённом сервере, а если возникнут проблемы — автоматически перезагрузится. Вручную им тоже можно управлять, для этого есть команды sudo systemctl start/stop/restart gunicorn.

 ## Веб- и обратный прокси-сервер Nginx: установка и настройка
 Находясь на удалённом сервере, из любой директории выполните команду:
 ```
 sudo apt install nginx -y
 ```
 Далее сервер, скорее всего, попросит вас перезагрузить операционную систему — сделайте это. А потом запустите Nginx командой:
 ```
 sudo systemctl start nginx
 ```
 Нужно оставить возможность отправлять запросы только на некоторые порты, например:
* 80 — HTTP;
* 443 — HTTPS;
* 22 — SSH.
В комплекте с операционной системой Ubuntu идёт файрвол ufw, но по умолчанию он выключен. Задача — указать порты, которые нужно открыть, и включить программу. Укажите файрволу, какие порты должны остаться открытыми. Для этого выполните на сервере две команды по очереди:
```
sudo ufw allow 'Nginx Full'
sudo ufw allow OpenSSH
```
Команда sudo ufw allow OpenSSH активирует разрешение для порта 22 — это стандартный порт для соединения по SSH. Если этот порт не открыть, то доступ к удалённому серверу будет закрыт сразу после включения файрвола, и вы туда больше не попадёте. Теперь включите файрвол:
```
sudo ufw enable
```
Далее проверьте внесённые изменения:
```
sudo ufw status
```
Файрвол ufw сообщит вам, что он «активен» и разрешает принимать запросы на порты, которые вы указали.

Собираем статику фронтенд-приложения. Перейдите в директорию infra_sprint1/frontend и выполните команду:
```
npm run build
```
Чтобы Nginx раздавал статику, он должен знать, где она лежит. У веб-сервера есть системная директория, которую он использует по умолчанию для доступа к статическим файлам, — /var/www/. Скопируйте в эту директорию содержимое папки .../frontend/build/:
```
# Команда cp — копировать, ключ -r — рекурсивно, включая вложенные папки и файлы.
sudo cp -r /home/yc-user/infra_sprint1/frontend/build/. /var/www/kittygram/ 
# Точка после build важна — будет скопировано содержимое директории.
```
Описываем настройки для работы со статикой фронтенд-приложения:
```
 sudo nano /etc/nginx/sites-enabled/default
 ```
 В этом файле уже есть первоначальные настройки для работы веб-сервера. Именно поэтому при обращении по IP-адресу сервера отображается стартовая страница Nginx. 
Удалите все настройки из файла, запишите и сохраните новые.Настраиваем сразу проксирование запросов:
```
server {
    server_name 158.160.91.227 kittydo.zapto.org;
    
    location / {
        root   /var/www/kittygram;
        index  index.html index.htm;
        try_files $uri /index.html;
    }

    location /api/ {
        proxy_pass http://127.0.0.1:9000;
    }

    location /admin/ {
        proxy_pass http://127.0.0.1:9000;
    }

    location /media/ {
        root /var/www/kittygram;
}
```
Сохраните изменения, проверьте и перезагрузите конфигурацию веб-сервера:
```
sudo nginx -t
sudo systemctl reload nginx
```
Чтобы подготовить бэкенд-приложение для сбора статики, в файле settings.py укажите директорию, куда эту статику нужно сложить. 
Через редактор Nano откройте файл settings.py, укажите новое значение для константы STATIC_URL и создайте константу STATIC_ROOT:
```
# Замените стандартное значение 'static' на 'static_backend',
# чтобы не было конфликта запросов к статике фронтенда и бэкенда.
STATIC_URL = '/static_backend/'
# Укажите директорию, куда бэкенд-приложение должно сложить статику.
STATIC_ROOT = BASE_DIR / 'static_backend'
```
На удалённом сервере при активированном виртуальном окружении перейдите в директорию с файлом manage.py и выполните команду:
```
python manage.py collectstatic
```
В директории проекта infra_sprint1/backend/ будет создана директория static_backend/ со всей статикой бэкенд-приложения. Вот такой ответ терминала подтверждает это:
```
161 static files copied to '/home/yc-user/infra_sprint1/backend/static_backend'.
```
Перейдите в корень проекта Infra_sprint1 и скопируйте директорию static_backend/ в директорию /var/www/kittygram/. Для этого выполните команду:
```
sudo cp -r /home/yc-user/infra_sprint1/backend/static_backend/ /var/www/kittygram/
```
Чтобы изменения в файле settings.py вступили в силу, перезапустите Gunicorn:
```
sudo systemctl restart gunicorn
```
## Добавление доменного имени в настройки Django
Находясь на сервере, перейдите в директорию /infra_sprint1/backend/kittygram_backend, с помощью Nano откройте файл settings.py и добавьте в список ALLOWED_HOSTS полученное доменное имя:
```
ALLOWED_HOSTS = ['xxx.xxx.xxx.xxx', '127.0.0.1', 'localhost', 'ваш-домен'] 
# Вместо xxx.xxx.xxx.xxx — IP вашего сервера.
# Домен вводится в формате 'project.hopto.org'.
```
Сохраните изменения и перезапустите Gunicorn командой sudo systemctl restart gunicorn, чтобы изменения вступили в силу.

Измените конфигурационный файл Nginx. Откройте его в Nano:
```
sudo nano /etc/nginx/sites-enabled/default
```
Найдите в файле строку, которая начинается с server_name. Там уже указан IP, через пробел добавьте выданное вам доменное имя без < >:
```
server {
...
    server_name <ваш-ip> <ваш-домен>;
...
}
```
После этого проверьте конфигурацию sudo nginx -t и перезагрузите её командой sudo systemctl reload nginx, чтобы изменения вступили в силу.
## Шифрование. HTTPS
SSL-сертификат от Let’s Encrypt можно установить на веб-сервер вручную, но можно и автоматизировать этот процесс. Для этого вам понадобится специальное ПО, например пакет certbot — его предоставляет центр сертификации Let’s Encrypt специально для Linux-систем.
Чтобы установить certbot, вам понадобится пакетный менеджер snap. Установите его командой:
```
sudo apt install snapd
```
Далее сервер, скорее всего, попросит вам перезагрузить операционную систему. Сделайте это, а потом последовательно выполните команды:
```
# Установка и обновление зависимостей для пакетного менеджера snap.
sudo snap install core; sudo snap refresh core
# При успешной установке зависимостей в терминале выведется:
# core 16-2.58.2 from Canonical✓ installed 

# Установка пакета certbot.
sudo snap install --classic certbot
# При успешной установке пакета в терминале выведется:
# certbot 2.3.0 from Certbot Project (certbot-eff✓) installed

# Создание ссылки на certbot в системной директории,
# чтобы у пользователя с правами администратора был доступ к этому пакету.
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```
Чтобы начать процесс получения сертификата, введите команду:
```
sudo certbot --nginx
```
Далее система оповестит вас о том, что учётная запись зарегистрирована и попросит указать имена, для которых вы хотели бы активировать HTTPS:
```
Account registered.

Which names would you like to activate HTTPS for?
We recommend selecting either all domains, or all domains in a VirtualHost/server block.

1: <доменное_имя_вашего_проекта>

Select the appropriate numbers separated by commas and/or spaces, or leave input
blank to select all options shown (Enter 'c' to cancel):
```
Введите 1 или не вводите ничего и нажмите Enter.
После этого certbot отправит ваши данные на сервер Let's Encrypt, и там будет выпущен сертификат, который автоматически сохранится на вашем сервере в системной директории /etc/ssl/. Также будет автоматически изменена конфигурация Nginx: в файл /etc/nginx/sites-enabled/default добавятся новые настройки и будут прописаны пути к сертификату.
Откройте файл /etc/nginx/sites-enabled/default и убедитесь в этом:
```
server {
    server_name 158.160.91.227 kittydo.zapto.org;

    location / {
        root   /var/www/kittygram;
        index  index.html index.htm;
        try_files $uri /index.html;
    }

    location /api/ {
        proxy_pass http://127.0.0.1:9000;
    }

    location /admin/ {
        proxy_pass http://127.0.0.1:9000;
    }

    location /media/ {
        root /var/www/kittygram;
    }

# Далее идут новые настройки и описание путей к сертификату.

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/kittydo.zapto.org/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/kittydo.zapto.org/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

}

server {
    if ($host = kittydo.zapto.org) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    server_name 158.160.91.227 kittydo.zapto.org;
    listen 80;
    return 404; # managed by Certbot


}
```
Перезагрузите конфигурацию Nginx:
```
sudo systemctl reload nginx
```
Настройка автоматического обновления SSL-сертификата. Срок действия SSL-сертификатов ограничен: например, сертификат от Let’s Encrypt действует 90 дней. Но обновлять его вручную вам не придётся, это будет делать за вас пакет certbot, если в его конфигурации ничего не менялось. 
Чтобы узнать актуальный статус сертификата и сколько дней осталось до его перевыпуска, используйте команду:
```
sudo certbot certificates
```
Вывод в терминал будет примерно таким:
```
Found the following certs:
  Certificate Name: ваше_доменное_имя
    Serial Number: 4f8d9d12b7f7e41124321322233db9024446372120
    Key Type: ECDSA
    Domains: ваше_доменное_имя
    Expiry Date: <дата> <время> (VALID: 89 days)
    Certificate Path: /etc/letsencrypt/live/ваше_доменное_имя/fullchain.pem
    Private Key Path: /etc/letsencrypt/live/ваше_доменное_имя/privkey.pem
```
Теперь убедитесь, что сертификат будет обновляться автоматически:
```
sudo certbot renew --dry-run
```
Если не выведется ошибка, значит, всё в порядке.
Вручную сертификат можно обновить командой:
```
sudo certbot renew --pre-hook "service nginx stop" --post-hook "service nginx start"
```
Эта команда обновит сертификат и перезапустит nginx.

## Полезные команды:
Остановить процессы:
```
pkill gunicorn (или номер процесса)
```
чем занят порт
```
sudo lsof -i tcp:8080
```
освободить порт
```
sudo fuser -k 8080/tcp
```
остановить Django server
```
pkill -f runserver
```        
проверить место на сервере
```
sudo df
```
копировал на сервер файлы:
```
scp -i "C:\Dev\vm_access\yc-khadakhanoff"/yc-khadakhanoff docs \
    yc-user@158.160.91.227:/home/yc-user/foodgram/docs
```


Автор проекта: [khadakhan](https://github.com/khadakhan/)