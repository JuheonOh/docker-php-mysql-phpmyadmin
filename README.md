# Docker로 PHP, MySQL, phpMyAdmin 환경 구축하기

- XAMPP의 htdocs 폴더를 번갈아 바꾸는 불편함을 해결하고, 여러 PHP 프로젝트를 독립적으로 동시에 실행할 수 있도록 개발 환경을 구성했습니다.

## 목차

- [Docker로 PHP, MySQL, phpMyAdmin 환경 구축하기](#docker로-php-mysql-phpmyadmin-환경-구축하기)
  - [목차](#목차)
  - [사용한 Docker 이미지 및 버전](#사용한-docker-이미지-및-버전)
  - [파일 구조](#파일-구조)
    - [mysql/db\_volume](#mysqldb_volume)
    - [mysql/sql](#mysqlsql)
    - [php/php.ini](#phpphpini)
    - [htdocs/](#htdocs)
    - [.env](#env)
  - [시작하기 전 주의사항](#시작하기-전-주의사항)
  - [시작하기](#시작하기)
    - [phpMyAdmin 접속](#phpmyadmin-접속)

## 사용한 Docker 이미지 및 버전

- PHP: 8.3.21
  - `gd`, `pdo`, `pdo_mysql` 확장 모듈 포함
- MySQL: 9.3.0
  - 비밀번호 없는 루트 계정
  - `.env` -> `MYSQL_DATABASE` 환경 변수로 데이터베이스 이름 설정
  - `.sql` 파일을 통해 초기 데이터베이스 생성 (`sql` 폴더 사용)
- phpMyAdmin: 5.2.2
  - 랜덤 포트 할당
  - `mysql` 자동 로그인

## 파일 구조

### mysql/db_volume

- `db_volume` 폴더는 MySQL 데이터베이스의 영구 저장소로 사용됩니다. 이 폴더에 저장된 데이터는 컨테이너가 재시작되더라도 유지됩니다.

### mysql/sql

- `sql` 폴더는 MySQL 데이터베이스 초기화 스크립트를 저장하는 곳입니다. 이 폴더에 SQL 파일을 추가하면 컨테이너 시작 시 자동으로 실행됩니다.

---

### php/php.ini

- `php.ini` 파일을 수정하여 PHP 설정을 변경할 수 있습니다.
  - 변경된 설정 사항은 다음과 같습니다:

```ini
  # 스크립트 최대 실행 시간
  # max_execution_time = 30
  max_execution_time = 120

  # 오류 보고 수준 설정
  # 모든 오류와 경고를 보고하지만(E_ALL), 사용 중단 예정 경고(E_DEPRECATED)와 엄격한 규칙 위반 경고(E_STRICT)는 제외합니다.
  # error_reporting = E_ALL
  error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT

  # 업로드 파일 크기 제한
  # upload_max_filesize = 2M
  upload_max_filesize = 40M
```

### htdocs/

- `htdocs` 폴더는 웹 서버의 루트 디렉토리로, PHP 파일을 저장하는 곳입니다. 이 폴더에 PHP 파일을 추가하면 웹 브라우저를 통해 접근할 수 있습니다.

### .env

- `.env` 파일은 환경 변수를 설정하는 곳입니다.
- `WEB_PORT` 변수로 웹 서버 포트를 설정할 수 있습니다. (기본 값: `8080`)
- `MYSQL_DATABASE` 변수로 초기 데이터베이스를 생성할 수 있습니다. (기본 값: `my_database`)

---

## 시작하기 전 주의사항

- 컨테이너를 시작하기 전에 `.env` 파일을 적절히 설정해야 합니다.
- MySQL 컨테이너 최초 실행시에만 초기 데이터베이스가 생성됩니다. 최초 실행 이후에는 `MYSQL_DATABASE` 환경 변수를 변경해도 데이터베이스가 생성되지 않습니다.

## 시작하기

1. `.env` 파일을 열고 필요한 환경 변수를 설정합니다.

   ```dotenv
   WEB_PORT=8080
   MYSQL_DATABASE=my_database
   ```

2. Docker Compose를 사용하여 컨테이너를 시작합니다.

   ```bash
    docker-compose up -d
   ```

3. 웹 브라우저에서 `http://localhost:8080`에 접속하여 PHP 환경을 확인합니다.

4. 컨테이너를 중지하려면 다음 명령어를 사용합니다.

   ```bash
   docker-compose down
   ```

### phpMyAdmin 접속

- phpMyAdmin은 랜덤 포트로 할당됩니다.
- 할당된 포트는 `docker-compose ps` 명령어로 확인할 수 있습니다.
- 예시: `http://localhost:포트번호`

```bash
$ docker-compose ps
NAME                                IMAGE                        COMMAND                  SERVICE      CREATED         STATUS         PORTS
php-mysql-phpmyadmin-mysql-1        mysql:9.3.0                  "docker-entrypoint.s…"   mysql        5 seconds ago   Up 3 seconds   3306/tcp, 33060/tcp
php-mysql-phpmyadmin-php-1          juheonoh/php:8.3.21-apache   "docker-php-entrypoi…"   php          4 seconds ago   Up 3 seconds   0.0.0.0:8080->80/tcp
php-mysql-phpmyadmin-phpmyadmin-1   phpmyadmin:5.2.2-apache      "/docker-entrypoint.…"   phpmyadmin   4 seconds ago   Up 3 seconds   0.0.0.0:32773->80/tcp
```
