# Настройка сервера

Подключение по ssh

```
ssh root@ip
```


# Создания пользователя
# Пользователь

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

# Запуск Nuxt
