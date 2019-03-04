# Docker Compose

### 컨테이너를 업데이트한다면?

* 도커에서 컨테이너를 업데이트 하려면 새 버전의 이미지를 다운(pull)받고 기존 컨테이너를 삭제(stop, rm) 한 후 새 이미지를 기반으로 새 컨테이너를 실행(run)하면 됩니다. 
* 컨테이너를 삭제한다는 건 컨테이너에서 생성된 파일이 사라진다는 뜻입니다.
* 즉, 데이터베이스라면 그동안 쌓였던 데이터가 모두 사라진다는 것이고 웹 어플리케이션이라면 그동안 사용자가 업로드한 이미지가 모두 사라진다는 것입니다.
* 이런 상황을 방지하기 위해 컨테이너 삭제시 유지해야하는 데이터는 반드시 컨테이너 내부가 아닌 외부 스토리지에 저장해야 합니다.

### volume 옵션을 사용한 mysql 컨테이너 생성

* docker run 명령어에서 소개한 옵션중에 -v 옵션을 사용해 보겠습니다.
* MySQL이라면 /var/lib/mysql디렉토리에 모든 데이터베이스 정보가 담기므로 호스트의 특정 디렉토리를 연결해주면 됩니다.
    ```
    # before
    docker run -d -p 3306:3306 \
    -e MYSQL_ALLOW_EMPTY_PASSWORD=true \
    --name mysql \
    mysql:5.7

    # after
    docker run -d -p 3306:3306 \
    -e MYSQL_ALLOW_EMPTY_PASSWORD=true \
    --name mysql \
    -v /my/own/datadir:/var/lib/mysql \ # <- volume mount
    mysql:5.7
    ```
* 위 샘플은 호스트의 /my/own/datadir디렉토리를 컨테이너의 /var/lib/mysql디렉토리로 마운트 하였습니다. 
* 이제 데이터베이스 파일은 호스트의 /my/own/datadir디렉토리에 저장되고 컨테이너를 삭제해도 데이터는 사라지지 않습니다. 

### 이러한 설정들이 많아진다면?

* 지금은 간단한 작업만 했기 때문에 명령이 길지 않지만 컨테이너 조합이 많아지고 여러가지 설정이 추가되면 명령어가 금방 복잡해집니다.
* 도커는 복잡한 설정을 쉽게 관리하기 위해 YAML방식의 설정파일을 이용한 Docker Compose라는 툴을 제공합니다. 

## Docker Compose 설치

* Docker for Mac 또는 Docker for Windows를 설치했다면 자동으로 설치됩니다.
* 리눅스라면 https://docs.docker.com/compose/install/ 에 접속하여 Linux 용 설치 방법을 참고하여 절차대로 따라해서 설치하면 됩니다.

## Docker Compose 사용 예

* Docker Compose yaml 파일로 wordpress 를 만들어 보겠습니다.
* 먼저 빈 디렉토리를 하나 만들고 docker-compose.yml 파일을 만들어 아래 설정을 입력합니다.
    ```
    version: '2'

    services:
        db:
            image: mysql:5.7
            volumes:
            - db_data:/var/lib/mysql
            restart: always
            environment:
            MYSQL_ROOT_PASSWORD: wordpress
            MYSQL_DATABASE: wordpress
            MYSQL_USER: wordpress
            MYSQL_PASSWORD: wordpress

        wordpress:
            depends_on:
            - db
            image: wordpress:latest
            volumes:
            - wp_data:/var/www/html
            ports:
            - "8000:80"
            restart: always
            environment:
            WORDPRESS_DB_HOST: db:3306
            WORDPRESS_DB_PASSWORD: wordpress

    volumes:
        db_data:
        wp_data:
    ```
* 실행해 봅니다.
    ```
    docker-compose up
    ```

# 이미지 만들고 배포하기

* [다음 페이지](이미지&#32;만들고&#32;배포하기.md)에서는 직접 이미지를 만들고 컨테이너를 여러 서버로 배포하는 방법에 대해 알아보겠습니다.