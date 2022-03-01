# Настройка сервера

Подключение по ssh

```
ssh root@ip
```


# Создания пользователя

Добавляет пользователя

```
adduser user
```

Даёт права sudo

```
adduser user sudo
```

<!-- Открывает настройку ssh

```
nano /etc/ssh/sshd_config
```

Запрещает вход по Root (Включить после )
```
PermitRootLogin no
```

Рестартует ssh

```
systemctl restart sshd
``` -->

Выходит

```
exit
```

Подключается от user

```
ssh user@ip
```

Создаёт папку для ключей

```
mkdir ~/.ssh
```

Локально: копирует публичный ключ

```
cat ~/.ssh/id_rsa.pub
```

Сервер: вставляет ключ

```
nano ~/.ssh/authorized_keys
```

Выставляет папке права

```
chmod -R 700 ~/.ssh/
```

Открывает настройку ssh

```
sudo nano /etc/ssh/sshd_config
```

Отключает вход по паролю

```
PasswordAuthentication no
```

Рестартует ssh

```
sudo systemctl restart sshd
```

Открывает настройку sudo

```
sudo visudo
```

Настраивает sudo без пароля

```
%sudo   ALL=(ALL:ALL) NOPASSWD:ALL
```

# Настройка NodeJS

```
curl -fsSL https://deb.nodesource.com/setup_14.x | sudo -E bash -
```
```
sudo apt-get install -y nodejs
```
```
node -v && npm -v
```

# Установка Nginx

```
sudo apt update
```
```
sudo apt install nginx
```

# Настройка Nginx

```
sudo ufw app list
```
```
sudo ufw allow 22
```
```
sudo ufw allow 'Nginx Full'
```
```
sudo ufw status
```

Статус, остановка, запуск, перезагрузка Nginx

```
systemctl status nginx
```
```
sudo systemctl stop nginx
```
```
sudo systemctl start nginx
```
```
sudo systemctl restart nginx
```
```
sudo systemctl reload nginx
```

# Установка pm2

```
sudo npm install pm2 -g
```
# Сертификат SSL

Генерирует ключ

```
openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048
```

Создаёт папку для снипетов

```
mkdir -p /etc/nginx/snippets/
```

Создаёт снипет для SSL

```
nano /etc/nginx/snippets/ssl-params.conf
```

```
ssl_session_timeout 1d;
ssl_session_cache shared:SSL:10m;
ssl_session_tickets off;
ssl_dhparam /etc/ssl/certs/dhparam.pem;
ssl_protocols TLSv1.2 TLSv1.3;
ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;
ssl_prefer_server_ciphers off;
add_header Strict-Transport-Security "max-age=63072000" always;
```

Обновляет snapd

```
snap install core
```

Устанавливает certbot

```
snap install --classic certbot
```

Проверка certbot

```
ln -s /snap/bin/certbot /usr/bin/certbot
```

Выпускает сертификат

```
certbot certonly --nginx
```

# Создание сайта

```
sudo mkdir -p /var/www/my-app-tobe.ru
```
```
sudo chown -R $USER:$USER /var/www/my-app-tobe.ru
```
```
sudo chmod -R 755 /var/www/my-app-tobe.ru
```
```
sudo nano /etc/nginx/sites-available/my-app-tobe.ru
```
```
map $sent_http_content_type $expires {
    "text/html"                 epoch;
    "text/html; charset=utf-8"  epoch;
    default                     off;
}

server {
    listen          80;             # the port nginx is listening on
    server_name     my-app-tobe.ru www.my-app-tobe.ru;    # setup your domain here
    return 301 https://my-app-tobe.ru$request_uri;

    gzip            on;
    gzip_types      text/plain application/xml text/css application/javascript;
    gzip_min_length 1000;

    location / {
        expires $expires;

        proxy_redirect                      off;
        proxy_set_header Host               $host;
        proxy_set_header X-Real-IP          $remote_addr;
        proxy_set_header X-Forwarded-For    $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto  $scheme;
        proxy_read_timeout          1m;
        proxy_connect_timeout       1m;
        proxy_pass                          http://127.0.0.1:3000; # set the address of the Node.js instance here
    }
}

server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name www.my-app-tobe.ru;
    return 301 https://my-app-tobe.ru$request_uri;

    location / {
      expires $expires;

      proxy_redirect                      off;
      proxy_set_header Host               $host;
      proxy_set_header X-Real-IP          $remote_addr;
      proxy_set_header X-Forwarded-For    $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto  $scheme;
      proxy_read_timeout          1m;
      proxy_connect_timeout       1m;
      proxy_pass                          http://127.0.0.1:3000; # set the address of the Node.js instance here
    }

    ssl_certificate /etc/letsencrypt/live/my-app-tobe.ru/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/my-app-tobe.ru/privkey.pem;
    ssl_trusted_certificate /etc/letsencrypt/live/my-app-tobe.ru/chain.pem;

    include snippets/ssl-params.conf;
}

```

```
sudo ln -s /etc/nginx/sites-available/my-app-tobe.ru /etc/nginx/sites-enabled/
```
```
sudo nano /etc/nginx/nginx.conf
```
Разкомментировать опцию

```
http {
    ...
    server_names_hash_bucket_size 64;
    ...
}
```
Проверка Nginx и перезагрузка
```
sudo nginx -t
```
```
sudo systemctl restart nginx
```

Проверяет домен

- `curl http://my-app-tobe.ru` — редирект
- `curl https://my-app-tobe.ru` — сайт

# Запуск Nuxt

Переносит проект на сервер и выполняет его сборку
```
cd /var/www/my-app-tobe.ru/
```
```
sudo git clone Ссылка на репозиторий ./
```
```
sudo npm install
```
```
sudo npm run build
```
```
sudo npm run start
```
Запускает nuxt при помощи pm2
```
pm2 start
```
