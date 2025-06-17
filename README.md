# Task14
1) создать image (поднять nginx используя конфигурации из предыдущих заданий (проксипасс, редирект, картинки, музыка) )
```
  server {

        listen 80 default_server;
        listen [::]:80 default_server;


        root /var/www/html;

        index index.html index.htm index.nginx-debian.html;

        server_name _;

        location / {
                try_files $uri $uri/ =404;
        }

        location /image {
                        alias /var/www/image/;
                        index image.jpeg;

                }

        location /music {
                alias /var/www/music/;
                index music.mp3;
                try_files $uri $uri/ =404;
                add_header Content-Disposition "attachment";
        }


        location /redirect {
                return 301 https://nginx.org;

        }

        location /prox {
              proxy_pass  https://httpbin.org/;
              proxy_set_header Host $host;
              proxy_set_header X-Real-IP $remote_addr;
              proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
              proxy_set_header X-Forwarded-Proto $scheme;
        }

}

```

![image](https://github.com/user-attachments/assets/bb631e67-75b5-423b-b906-f5d4856a725c) <br>
![image](https://github.com/user-attachments/assets/de6e0e96-cb71-464c-8cb0-07f17deeaf44) <br>

Redirect: <br>
![image](https://github.com/user-attachments/assets/6de10217-915f-4de2-9923-454ccf51ee2f) <br>

 И Т.Д. <br>

2) Создание Docker контейнера <br>
![image](https://github.com/user-attachments/assets/b0291919-dcc3-4b7a-b210-2c33c9364e70) <br>

Dockerfile: <br>
```
  FROM nginx:alpine

COPY default.conf /etc/nginx/conf.d/default.conf

COPY music.mp3 /var/www/music/music.mp3
COPY image.jpeg /var/www/image/image.jpeg
COPY index.html /var/www/html/index.html

EXPOSE 80
```
Проверка работы: <br>
 ![image](https://github.com/user-attachments/assets/fb034671-702c-43c3-9bfa-602d56a149ac) <br>

![image](https://github.com/user-attachments/assets/dd7bd806-3eb1-4109-80bf-4cb49b1e8c41) <br>

![image](https://github.com/user-attachments/assets/dddf57a0-f1b9-4ca2-852d-e86b7ebd1e79) <br>
![image](https://github.com/user-attachments/assets/568dfdb4-0b35-41c3-931f-6f57a96a2e50) <br>

3) контейнер с скриптом парсинга логов <br>
![image](https://github.com/user-attachments/assets/452feab2-1fac-4eb6-8a64-42da8945719f) <br>
```
  
FROM python:3.9-alpine

WORKDIR /app
COPY log_parser.py .

CMD ["python", "log_parser.py"]
```
ИТОГ: <br>
![image](https://github.com/user-attachments/assets/7f6b6e87-19ac-4cbc-b1c3-1d3607b2ed84) <br>

4) Docker-compose
   1. ```
        version: '3.8'

        services:
          nginx:
            build:
              context: .
              dockerfile: Dockerfile
            ports:
              - "80:80"

      ```
![image](https://github.com/user-attachments/assets/ac79732d-6850-48e6-92d8-92951cae58d1) <br>

  2. ```
      version: '3.8'

      services:
         log-parser:
          image: python:3.9-alpine
          volumes:
            - ./parse.py:/app/parse.py
            - /var/log/nginx:/var/log/nginx  # Монтируем логи Nginx
          working_dir: /app
          command: python parse.py

     ```
![image](https://github.com/user-attachments/assets/9e71cfad-295f-435b-a338-aab10a562dd3) <br>

5) Создание и добаление в Dockerhub <br>
   ![image](https://github.com/user-attachments/assets/92316397-face-4450-905c-0ccf330b63ce)

     
        



