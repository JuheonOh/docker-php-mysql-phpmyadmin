# Docker로 PHP, MySQL, phpMyAdmin 환경 구축하기

- 이 문서는 Docker를 사용하여 PHP, MySQL, phpMyAdmin 환경을 구축하는 방법을 설명합니다.

- ~~고등학생 시절 기능경기대회 문제를 쉽게 실행할 수 있는 환경을 만들고자 작성되었습니다.~~

## 목차

- [Docker로 PHP, MySQL, phpMyAdmin 환경 구축하기](#docker로-php-mysql-phpmyadmin-환경-구축하기)
  - [목차](#목차)
  - [이미지 정보](#이미지-정보)
  - [파일 구조](#파일-구조)
    - [web/config](#webconfig)
    - [web/htdocs](#webhtdocs)
    - [web/Dockerfile](#webdockerfile)
    - [mysql/db\_volume](#mysqldb_volume)
    - [mysql/sql](#mysqlsql)
    - [.env](#env)
  - [주의사항](#주의사항)
  - [시작하기](#시작하기)

## 이미지 정보

- PHP: 8.3.21
  - `pdo`, `pdo_mysql` 확장 모듈 포함
- MySQL: 9.3.0
  - 비밀번호 없는 루트 계정
  - `.env` -> `MYSQL_DATABASE` 환경 변수로 데이터베이스 이름 설정
  - `.sql` 파일을 통해 초기 데이터베이스 생성 (`sql` 폴더 사용)
- phpMyAdmin: 5.2.2
  - 랜덤 포트 할당
  - `mysql` 자동 로그인

## 파일 구조

### web/config

- `php.ini` 파일을 수정하여 PHP 설정을 변경할 수 있습니다.
  - 변경된 설정 사항은 다음과 같습니다:

```ini
  # 스크립트 최대 실행 시간 (기본값: 30)
  max_execution_time = 120

  # 오류 보고 수준 설정 (기본값: E_ALL)
  # 모든 오류와 경고를 보고하지만(E_ALL), 사용 중단 예정 경고(E_DEPRECATED)와 엄격한 규칙 위반 경고(E_STRICT)는 제외합니다.
  error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT

  # 업로드 파일 크기 제한 (기본값: 2M)
  upload_max_filesize = 40M
```

### web/htdocs

- `htdocs` 폴더는 웹 서버의 루트 디렉토리로, PHP 파일을 저장하는 곳입니다. 이 폴더에 PHP 파일을 추가하면 웹 브라우저를 통해 접근할 수 있습니다.

### web/Dockerfile

- `Dockerfile`은 PHP 환경을 설정하는 데 사용됩니다. 이 파일에서 PHP 확장 모듈을 설치하고, 필요한 설정을 적용합니다.

---

### mysql/db_volume

- `db_volume` 폴더는 MySQL 데이터베이스의 영구 저장소로 사용됩니다. 이 폴더에 저장된 데이터는 컨테이너가 재시작되더라도 유지됩니다.

### mysql/sql

- `sql` 폴더는 MySQL 데이터베이스 초기화 스크립트를 저장하는 곳입니다. 이 폴더에 SQL 파일을 추가하면 컨테이너 시작 시 자동으로 실행됩니다.

---

### .env

- `.env` 파일은 환경 변수를 설정하는 곳입니다.
- `WEB_PORT` 변수로 웹 서버 포트를 설정할 수 있습니다.
- `MYSQL_DATABASE` 변수로 초기 데이터베이스를 생성할 수 있습니다.

---

## 주의사항

- `sql` 폴더에 있는 SQL 파일은 `MYSQL_DATABASE` 환경 변수로 지정된 데이터베이스에 적용됩니다.
- 컨테이너를 시작하기 전에 `.env` 파일을 적절히 설정해야 합니다.

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
