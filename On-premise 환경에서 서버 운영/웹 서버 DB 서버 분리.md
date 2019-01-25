# Web Server / Database Server 분리

* 대게 웹 서버와 Database 서버를 분리합니다.
* 이를 통해 성능 향상을 할 수 있는데, 그 이유는 웹 서버 및 Database 서버가 각각 Server Machine 의 자원(CPU/Memory)를 사용하기 때문입니다.
  ![db_web_sep](img/2_db_web_sep.png)
* 여기서 또 하나 얻을 수 있는 이점은 웹 서버는 Public Network 에서 운영하고, Database 서버는 Private Network 에서 운영할 수 있다는 것입니다.

# 네트워크 망 분리

* Web Application 은 기본적으로 모든 사람이 접근할 수 있어햐 하기 때문에 Public Network 망에서 운영을 합니다. 하지만 이는 보안상 취약할 수 밖에 없을 것입니다.
* 따라서 Database Server 를 분리하여, Database 는 Private Network 에 운영하는 것이 Infra 를 구축하는 데 있어서 기본적으로 해야될 사항일 것입니다.
  ![network_sep](img/3_network_sep.png)

# HA(High Availability) 구성

* HA(High Availability) 는 한국어로 고가용성이라고 합니다. 고가용성이란 절대 죽지 않는 것을 말합니다.
* HA 를 구성하는 방식에는 크게 두 가지가 있습니다.
* 첫 번째는 물리적으로 HA 구성하는 방식이 있고,
* 두 번째는 어플리케이션 단위를 HA 구성하는 방식이 있습니다.
* [다음 페이지](웹&#32;서버&#32;이중화&#32;구성&#32;-&#32;L4&#32;switch.md)에서 L4 Switch 를 통해 Web Server 를 HA 구성하는 방법에 대해 알아보겠습니다.