# Настройка сервера

Подключает

```
ssh root@ip
```

Установка NodeJS

```
curl -fsSL https://deb.nodesource.com/setup_14.x | sudo -E bash -
```
```
sudo apt-get install -y nodejs
```
```
node -v && npm -v
```

Установка pm2

```
sudo npm install pm2 -g
```

Установка Nginx

```
sudo apt update
```
```
sudo apt install nginx
```

Настройка Nginx

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

Создания сайта
