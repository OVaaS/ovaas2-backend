# Docker+Django+MySQL
 
## 実行環境
 - Windows
 - Docker Desktop
 - Docker for Visual Studio Code
 
***
 
## 環境構築
### 1.　作業ディレクトリとファイルの作成
 
```bash
> mkdir ovaas
> cd ovaas
> code .
```
 
### 作成するファイル
以下を`ovaas`直下に作成し、書き込む
 - Dockerfile
 - docker-compose.yml
 - requirements.txt
 
Dockerfile
```dockerfile
FROM python:3
ENV PYTHONUNBUFFERED 1
RUN mkdir /code
WORKDIR /code
COPY requirements.txt /code/
RUN pip install --upgrade pip && pip install -r requirements.txt
 
COPY . /code/
```
 
docker-compose.yml
 
```yml
version: '3'
services:
  db:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: django-db
      MYSQL_USER: django
      MYSQL_PASSWORD: django
      TZ: 'Asia/Tokyo'
    command: mysqld --character-set-server=utf8 --collation-server=utf8_unicode_ci
 
  web:
    build: .
    command: python3 manage.py runserver 0.0.0.0:8000
    volumes:
      - .:/code
    ports:
      - "8000:8000"
    depends_on:
      - db
```
 
requirements.txt
 
```
django
djangorestframework
mysqlclient
```
 
### 2.　プロジェクトの作成
 
```
> docker-compose run web django-admin startproject ovaas .
```
 
`ovaas/settings.py`のデータベースを以下に書き換える
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'django-db',
        'USER': 'django',
        'PASSWORD': 'django',
        'HOST': 'db',
        'PORT': '3306'
    }
}
```
 
コンテナを起動
```
> docker-compose up -d
```
 
[http://localhost:8000](http://localhost:8000)にアクセスし、起動を確認。
 
***
 
## 参照記事
[Django+MySQLの開発環境をdocker-composeで構築する](https://qiita.com/bakupen/items/f23ce3d2325b4491a2dd)