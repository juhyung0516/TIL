# Apache 설치 및 실행 확인, NginX 설치 및 실행 확인

이는 아마존 AWS EC2 Ubuntu를 SSH로 접근했다는 가정 하에 시작한다.

### 1. 우선 다음과 같은 준비물이 필요하다.

1. sudo 사용이 가능한 서버 유저
2. 필수가 아닌 포트를 차단하기 위한 기본 방화벽(basic firewall) 활성화

### 2. 방화벽 활성화

우분투의 기본적인 방화벽은 UFW(Ubuntu Firewall)이다. 명령어도 그러므로 UFW로 접근해야 한다.

Ubuntu 서버는 UFW 방화벽을 사용하여 특정 서비스에 대한 연결만 허용되도록 할 수 있으므로 이 응용 프로그램을 사용해서 기본 방화벽을 설정할 수 있다.

보통 EC2를 SSH로 실행하는 단계에서 SSH와 같은 응용 프로그램은 UFW에 프로필을 등록할 수 있다. 서버에 연결할 수 있는 OpenSSH가 UFW 프로필에 등록되어 있다.

```bash
ufw app list

Output
Available applications:
  OpenSSH
```

위 명령어로 실행하면 이용가능한 어플리케이션으로 OpenSSH를 볼 수 있다.

```bash
ufw allow OpenSSH

# ufw enable

ufw status
```

위 명령어로 이제 OpenSSH를 방화벽에 허용해준다.

그 뒤 방화벽은 기본적으로 비활성 상태이기 때문에 방화벽을 활성화해준다.

상태를 확인해보면 다음과 같이 허용되었음을 알 수 있다.

```bash
Output
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere
OpenSSH (v6)               ALLOW       Anywhere (v6)
```

그렇지만 **방화벽은 현재 SSH를 제외한 모든 연결을 차단하고 있으므로** 추가 서비스를 설치 및 구성하는 경우, 트래픽을 허용하도록 방화벽 설정을 조정해야 한다. 그러나 지금은 아니다.

## 아파치 설치 및 실행

### 1. **아파치 설치**

```bash
sudo apt update
```

위 명령어로 패키지 모듈을 업데이트한다.

기반이 되는 운영체제에 따라 명령어가 약간 다르다.

CentOs - yum

ubuntu - apt-get

```bash
$ sudo apt install apache2
```

우분투에서는 공홈에 따르면 apache가 아닌 apache2를 사용한다. 이를 설치해준다.

설치 후, apt가 Apache와 그에 필요한 종속성을 전부 설치해준다.

### 2. **방화벽 조정**

apache를 실행하기 전에 기본 웹 포트에 대한 외부 액세스를 허용하도록 방화벽 설정을 수정해야 한다. 이미 만든 서버에 대한 액세스를 제한하도록 구성된 UFW 방화벽이 있다.

아까 말했듯 UFW 방화벽은 알아서 apache가 설치되는 동안 이를 방화벽 허용 후보로 올려놓는다.

```bash
sudo ufw app list
```

리스트를 확인해보면 다음과 같다.

```bash
Output
Available applications:
  Apache
  Apache Full
  Apache Secure
  OpenSSH
```

왜 3개나 나오냐면 다음과 같은 이유에서다.

- **Apache**: 이 프로필은 **포트 80(http)만** 연다(일반, 암호화되지 않은 웹 트래픽).
- **Apache Full**: 이 프로필은 포트 80(일반, 암호화되지 않은 웹 트래픽)과 포트 443(TLS/SSL 암호화 트래픽)을 모두 연다. **(Http + Https)**
- **Apache Secure**
  : 이 프로필은 포트 443(TLS/SSL 암호화 트래픽)만 연다.**(Https만)**

지금 아직 서버 SSL을 구성하지 않았기 때문에 https보다는 http인 포트 80만 열어도 테스트가 가능하다.

```bash
sudo ufw allow 'Apache'
sudo ufw status
```

허용 후, 변경사항을 확인해보면 다음과 같다.

```bash
Status: active

To                         Action      From
--                         ------      ----
Apache                     ALLOW       Anywhere
80/tcp                     ALLOW       Anywhere
443/tcp                    ALLOW       Anywhere
22/tcp                     ALLOW       Anywhere
Anywhere                   ALLOW       xxx.xxx.xxx.xxx
Apache (v6)                ALLOW       Anywhere (v6)
80/tcp (v6)                ALLOW       Anywhere (v6)
443/tcp (v6)               ALLOW       Anywhere (v6)
22/tcp (v6)                ALLOW       Anywhere (v6)
```

이중 Apache가 허용되었다 뜨는 열들이 중요하다.

출력에 표시된 대로 프로필은 Apache 웹 서버에 대한 액세스를 허용하도록 활성화되었다.

### 3. 웹 서버 확인

설치 프로세스가 끝나면 Ubuntu 20.04가 Apache를 시작하므로 웹 서버는 이미 가동되어 실행 중이어야 한다.

서비스가 실행 중인지 확인 하려면 다음을 입력해 확인한다.

```bash
sudo systemctl status apache2
```

```bash
● apache2.service - The Apache HTTP Server
     Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor prese>
     Active: active (running) since Fri 2022-05-06 05:41:18 UTC; 10h ago
       Docs: https://httpd.apache.org/docs/2.4/
    Process: 144213 ExecStart=/usr/sbin/apachectl start (code=exited, status=0/>
   Main PID: 144217 (apache2)
      Tasks: 55 (limit: 1147)
     Memory: 9.0M
     CGroup: /system.slice/apache2.service
             ├─144217 /usr/sbin/apache2 -k start
             ├─144218 /usr/sbin/apache2 -k start
             └─144219 /usr/sbin/apache2 -k start

May 06 05:41:18 ip-xxx-xx-xx-xxx systemd[1]: Starting The Apache HTTP Server...
May 06 05:41:18 ip-xxx-xx-xx-xxx systemd[1]: Started The Apache HTTP Server.
```

active 부분, 그리고 맨 아래 부분의 문구로 Apache HTTP 서버가 실행되고 있음을 알 수 있다.

이제 aws에 등록된 private ip로 들어가면 서버 실행을 테스트할 수 있다!

## NginX 설치 및 실행

이미 우리의 서버는 apache를 실행 중이다. 그러므로 우리는 apache를 중단한 다음, nginx를 설치하고 실행해야 한다.

다음 명령어 중 하나를 입력한다.

```bash
// stop Apache 2 web server
sudo /etc/init.d/apache2 stop

sudo systemctl stop apache2
```

/etc/init.d는 리눅스의 서비스 관리프로그램을 이용해서 apache2를 스크립트(start, stop, restart, reload)로 관리하는 것이다.

systemctl은 systemd라는 리눅스 전반에 걸쳐 있는 프로세스를 이용한 방법이다.

둘 다, 지금은 아파치 서버를 중단시키려한다는 목적은 동일하다. 나중에 restart나, start를 stop 대신 붙여서 다시 이용할 수 있다.

Nginx를 처음부터 설치한다면 방화벽 설정도 해줘야 하지만, 지금은 방화벽 설정을 아까 했기 때문에 특별히 할 필요가 없다. 그냥 후보로 올라간 Nginx를 올리기만 하면 된다.

```bash
sudo apt update
sudo apt install nginx

sudo ufw app list
```

```bash
Output
Available applications:
  Nginx Full
  Nginx HTTP
  Nginx HTTPS
  OpenSSH
```

옵션도 아까와 유사하다. HTTP+HTTPS인가, HTTP인가, HTTPS인가. 포트도 동일하다.

80번으로 테스트해볼 것이므로 마찬가지로 설정해준다.

```bash
sudo ufw allow 'Nginx HTTP'
```

그 뒤, 확인해주면 다음과 같다.

```bash
sudo ufw status
```

```bash
Apache                     ALLOW       Anywhere
80/tcp                     ALLOW       Anywhere
443/tcp                    ALLOW       Anywhere
22/tcp                     ALLOW       Anywhere
Anywhere                   ALLOW       172.31.46.251
Nginx HTTP                 ALLOW       Anywhere
Apache (v6)                ALLOW       Anywhere (v6)
80/tcp (v6)                ALLOW       Anywhere (v6)
443/tcp (v6)               ALLOW       Anywhere (v6)
22/tcp (v6)                ALLOW       Anywhere (v6)
Nginx HTTP (v6)            ALLOW       Anywhere (v6)
```

Nginx가 제대로 실행되는지 확인하기 위해 다음을 입력한다.

```bash
systemctl status nginx
```

```bash
● nginx.service - nginx - high performance web server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
     Active: active (running) since Fri 2022-05-06 16:27:20 UTC; 6s ago
       Docs: https://nginx.org/en/docs/
    Process: 152622 ExecStart=/usr/sbin/nginx -c /etc/nginx/nginx.conf (code=exited, status=0/SUCCESS)
   Main PID: 152625 (nginx)
      Tasks: 2 (limit: 1147)
     Memory: 3.1M
     CGroup: /system.slice/nginx.service
             ├─152625 nginx: master process /usr/sbin/nginx -c /etc/nginx/nginx.conf
             └─152626 nginx: worker process

May 06 16:27:20 ip-xxx-xxx-xxx-xxx systemd[1]: Starting nginx - high performance web server...
May 06 16:27:20 ip-xxx-xxx-xxx-xxx systemd[1]: nginx.service: Can't open PID file /run/nginx.pid (yet?) after start: Operation not permitted
May 06 16:27:20 ip-xxx-xxx-xxx-xxx systemd[1]: Started nginx - high performance web server.
```

여기도 매한가지로 활성화가 되어 있다.

참고로 나는 아파치를 시작 → 정지 → Nginx를 시작 → 정지, 그리고, 다시 아파치를 재시작→정지, Nginx를 재시작했다.

이후, 아마존 private ip를 입력해 해결되었는지 여부를 확인한다.
