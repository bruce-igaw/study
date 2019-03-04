# Docker 의 기본 명령어

### 이 문서의 내용은 다음 웹 페이지의 내용을 정리한 것입니다.

* 링크
    * https://subicura.com/2017/01/19/docker-guide-for-beginners-2.html

# Docker 설치

* https://docs.docker.com/install/
    * 각 배포판 별로 설치하는 방법이 있습니다.
* 아래 명령어는 사용자를 docker 그룹으로 할당하여 sudo 권한 없이 사용하는 방법입니다.
    ```
    sudo usermod -aG docker {user}
    ```
* docker 설치되었는지 확인하려면 아래 명령어로 확인할 수 있습니다.
    ```
    docker version
    ```

# Docker 기본 명령어

* https://docs.docker.com/engine/reference/commandline/ 
    * docker 명령어에 대한 메뉴얼 페이지입니다.

## docker run

* 컨테이너 실행하는 명령어입니다.
    ```
    docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]
    ```

* 옵션  
    -d&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;detached mode 흔히 말하는 백그라운드 모드  
    -p&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;호스트와 컨테이너의 포트를 연결 (포워딩)  
    -v&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;호스트와 컨테이너의 디렉토리를 연결 (마운트)  
    -e&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;컨테이너 내에서 사용할 환경변수 설정  
    –name&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;컨테이너 이름 설정  
    –rm&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;프로세스 종료시 컨테이너 자동 제거  
    -it&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-i와 -t를 동시에 사용한 것으로 터미널 입력을 위한 옵션  
    –link&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;컨테이너 연결 [컨테이너명:별칭]

### docker run 예제

* ubuntu 컨테이너를 실행
    ```
    docker run --rm -it ubuntu:16.04 /bin/bash
    ```
    * 컨테이너 내부에 들어가기 위해 bash 쉘을 실행하고 키보드 입력을 위해 -it 옵션을 사용합니다.
    * 추가적으로 프로세스가 종료되면 컨테이너가 자동으로 삭제되도록 --rm 옵션도 추가하였습니다.

* redis 컨테이너 백그라운드로 실행
    ```
    docker run -d -p 1234:6379 redis
    ```
    * 백그라운드 모드로 실행하기 위해 -d 옵션을 사용합니다.
    * 컨테이너의 포트를 호스트의 포트로 연결 -p 옵션을 사용합니다.
    * localhost의 1234포트로 접속하면 하면 redis를 사용할 수 있습니다.

* mysql 컨테이너 실행 시 컨테이너명 및 환경변수 추가
    ```        
    docker run -d -p 3306:3306 \
               -e MYSQL_ALLOW_EMPTY_PASSWORD=true \
               --name mysql \
               mysql:5.7
    ```
    * -e 옵션을 이용하여 환경변수를 설정하였습니다.
    * --name 옵션을 이용하여 컨테이너 이름을 mysql 로 하였습니다.

* wordpress 컨테이너 실행 시, mysql 컨테이너 연결하기
    ```
    #mysql 에 접속합니다.
    mysql -h127.0.0.1 -uroot 

    $ create database wp CHARACTER SET utf8;
    $ grant all privileges on wp.* to wp@'%' identified by 'wp';
    $ flush privileges;
    $ quit

    # wordpress 컨테이너를 실행합니다.
    docker run -d -p 8080:80 \
               --link mysql:mysql \
               -e WORDPRESS_DB_HOST=mysql \
               -e WORDPRESS_DB_NAME=wp \
               -e WORDPRESS_DB_USER=wp \
               -e WORDPRESS_DB_PASSWORD=wp \
               wordpress
    ```
    * -link 옵션을 이용하여 MySQL 컨테이너를 연결하였습니다.
    * 컨테이너가 제대로 실행되었는지 웹 브라우저로 확인해봅니다.
    * --link 옵션은 deprecated 되어 곧 사용할 수 없습니다.  
      대신 Docker network 기능을 이용해야 하지만 쉬운 이해를 돕기 위해 사용하였습니다.

* tensorflow
    ```
    docker run -d -p 8888:8888 -p 6006:6006 teamlab/pydata-tensorflow:0.1
    ```
    * 컨테이너가 실행되면 웹 브라우저에서 jupyter에 접속하여 머신러닝을 시작해 봅시다!
    * 내부 구조나 설치과정은 자세히 모르지만, 간단한 도커 명령어로 여러개의 서비스를 순식간에 실행하고 사용할 수 있다니 정말 놀랍습니다.

## docker ps

* 컨테이너 목록을 확인하는 명령어입니다.
    ```
    docker ps [OPTIONS]
    ```

### docker ps 예제

* 실행 중인 컨테이너 목록을 확인하기
    ```
    docker ps

    CONTAINER ID        IMAGE                           COMMAND                  CREATED              STATUS              PORTS                                                    NAMES
    6a1d027b604f        teamlab/pydata-tensorflow:0.1   "/opt/start"             About a minute ago   Up About a minute   0.0.0.0:6006->6006/tcp, 22/tcp, 0.0.0.0:8888->8888/tcp   desperate_keller
    52a516f87ceb        wordpress                       "docker-entrypoint.sh"   8 minutes ago        Up 8 minutes        0.0.0.0:8080->80/tcp                                     happy_curran
    2e2c569115b9        mysql:5.7                       "docker-entrypoint.sh"   9 minutes ago        Up 9 minutes        0.0.0.0:3306->3306/tcp                                   mysql
    56341072b515        redis                           "docker-entrypoint.sh"   16 minutes ago       Up 9 minutes        0.0.0.0:1234->6379/tcp                                   furious_tesla
    ```

* 종료된 컨테이너(Exited (0)) 목록까지 확인하기
    ```
    docker ps -a

    CONTAINER ID        IMAGE                           COMMAND                  CREATED             STATUS                      PORTS                                                    NAMES
    6a1d027b604f        teamlab/pydata-tensorflow:0.1   "/opt/start"             2 minutes ago       Up 2 minutes                0.0.0.0:6006->6006/tcp, 22/tcp, 0.0.0.0:8888->8888/tcp   desperate_keller
    52a516f87ceb        wordpress                       "docker-entrypoint.sh"   9 minutes ago       Up 9 minutes                0.0.0.0:8080->80/tcp                                     happy_curran
    2e2c569115b9        mysql:5.7                       "docker-entrypoint.sh"   10 minutes ago      Up 10 minutes               0.0.0.0:3306->3306/tcp                                   mysql
    56341072b515        redis                           "docker-entrypoint.sh"   18 minutes ago      Up 10 minutes               0.0.0.0:1234->6379/tcp                                   furious_tesla
    e1a00c5934a7        ubuntu:16.04                    "/bin/bash"              32 minutes ago      Exited (0) 32 minutes ago                                                            berserk_visvesvaraya
    ```

## docker stop

* 실행중인 컨테이너를 중지하는 명령어는 다음과 같습니다.
    ```
    docker stop [OPTIONS] CONTAINER [CONTAINER...]
    ```

## docker rm

* 종료된 컨테이너를 완전히 제거하는 명령어는 다음과 같습니다.
    ```
    docker rm [OPTIONS] CONTAINER [CONTAINER...]
    ```
* tip) 명령어를 입력하면 중지된 컨테이너 ID를 가져와서 한번에 삭제합니다.
    ```
    docker rm -v $(docker ps -a -q -f status=exited)
    ```

## docker images

* 도커 이미지 목록을 보는 명령어는 다음과 같습니다.
    ```
    docker images [OPTIONS] [REPOSITORY[:TAG]]
    ```

* 도커 이미지 목록을 확인하기
    ```
    REPOSITORY                  TAG                 IMAGE ID            CREATED             SIZE
    wordpress                   latest              b1fe82b15de9        43 hours ago        400.2 MB
    redis                       latest              45c3ea2cecac        44 hours ago        182.9 MB
    mysql                       5.7                 f3694c67abdb        46 hours ago        400.1 MB
    ubuntu                      16.04               104bec311bcd        4 weeks ago         129 MB
    teamlab/pydata-tensorflow   0.1                 7bdf5d7e0191        6 months ago        3.081 GB
    ```

## docker pull

* 도커 이미지를 다운로드하는 명령어는 다음과 같습니다.
    ```
    docker pull [OPTIONS] NAME[:TAG|@DIGEST]
    ```

* 도커 이미지를 다운로드하기  
  docker run 명령어를 입력하면 이미지가 없을 때 자동으로 pull 받습니다.  
  같은 태그지만 이미지가 업데이트된 경우 pull 명령어를 통해 새로 다운로드 받을 수 있습니다.
    ```
    docker pull ubuntu:14.04
    ```

## docker rmi

* 도커 이미지 삭제하는 방법은 다음과 같습니다.
    ```
    docker rmi [OPTIONS] IMAGE [IMAGE...]
    ```

## docker logs

* 컨테이너가 정상적으로 동작하는지 확인하는 좋은 방법은 로그를 확인하는 것 입니다. 로그를 확인하는 방법은 다음과 같습니다.
    ```
    docker logs [OPTIONS] CONTAINER
    ```

* 기본 옵션과, -f, --tail 옵션을 살펴봅니다.  
  기존에 생성해 놓은 워드프레스 컨테이너 로그를 확인해 보겠습니다.
    ```
    docker ps
    docker logs ${WORDPRESS_CONTAINER_ID}
    ```

* 너무 많으니 --tail옵션으로 마지막 10줄만 출력해 보겠습니다.
    ```
    docker logs --tail 10 ${WORDPRESS_CONTAINER_ID}
    ```

* 이제 실시간으로 로그가 생성되는 걸 확인해보겠습니다. -f 옵션으로 실행합니다.  
  로그 보기를 중지하려면 ctrl + c를 입력하면 됩니다.
    ```
    docker logs -f ${WORDPRESS_CONTAINER_ID}
    ```

### 로그에 대해 좀 더 자세히

* 프로그램마다 로그 파일은 제각각 생길텐데 어떻게 저 로그가 나올까 라는 의문이 생깁니다. 
* 도커는 로그파일을 자동으로 알아채는게 아니라 표준 스트림Standard streams 중 stdout, stderr를 수집합니다. 
* 따라서 컨테이너에서 실행되는 프로그램의 로그 설정을 파일이 아닌 표준출력으로 바꾸어야 합니다. 
* 단지 출력 방식만 바꾸는 것으로 모든 컨테이너는 로그에 대해 같은 방식으로 관리할 수 있게 됩니다.
* 또하나 중요한 점은 컨테이너의 로그파일은 json 방식으로 어딘가에 저장이 됩니다. 
* 로그가 많으면 은근히 파일이 차지하는 용량이 커지므로 주의해야합니다. 
* 도커는 다양한 플러그인을 지원하여 json이 아닌 특정 로그 서비스에 스트림을 전달할 수 있습니다. 
* 어느 정도 앱의 규모가 커지면 기본적인 방식 대신 로그 서비스를 이용하는 걸 고려해야 합니다.

## docker exec

* 컨테이너를 관리하다 보면 실행중인 컨테이너에 들어가보거나 컨테이너의 파일을 실행하고 싶을 때가 있습니다.  
  (컨테이너에 SSH 를 설치하면 되지 않을까? 라고 생각할 수 있지만 SSH는 권장하지 않습니다.)
* 컨테이너 명령어를 실행하는 방법은 다음과 같습니다.
    ```
    docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
    ```
* run 명령어와 유사해 보입니다. 차이는 run은 새로 컨테이너를 만들어서 실행하고 exec는 실행중인 컨테이너에 명령어를 내리는 정도입니다.

# Docker Compose

* [다음 페이지](Docker&#32;의&#32;기본&#32;명령어.md)에서 복잡한 설정을 쉽게 관리하기 위한 Docker Compose 에 대해 알아보겠습니다.