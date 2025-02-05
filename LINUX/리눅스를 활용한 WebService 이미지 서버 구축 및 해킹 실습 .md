### **1. Virtual Machine으로 리눅스 환경 맞추기**
( ~~UTM 환경 문제로 Virtual Machine 으로 변경~~ )

1. VirtualBox / Ubuntu 설치
    1. [VirtualBox 7.1.6 paltform packages](https://www.virtualbox.org/wiki/Downloads) 설치 (macOS / Apple Silicon hosts)
    2. [ubuntu-24.04.1-live-server-arm64.iso](https://ubuntu.com/download/server/arm) 다운로드
    3. ‘Done’ 을 마구마구 누르면 (대부분 Dafault로 넘김) 설치가 완료된다.
        1. 🚨 참고로 disk 용량은 좀 넉넉하게 하는 것이 좋을 것 같다.
        
        ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/cf024025-486d-4514-84ae-3a7c5951c17c/a6c21e6a-de02-4a46-9519-245c1d2d968c/image.png)
        
        *(처음에 용량이 없어서 다시 깔았다.)*
        
2. 설치 후 사진
    
    ![스크린샷 2025-01-24 오전 2.22.03.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/cf024025-486d-4514-84ae-3a7c5951c17c/f021935d-bd65-40dc-9ff3-4835150407bc/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2025-01-24_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_2.22.03.png)
    
3. 리눅스 기본 서비스 설정
    
    ```bash
    # 시스템 업데이트
    sudo apt update
    sudo apt upgrade
    sudo apt-get update
    
    # LAMP 스택 설치
    sudo apt install apache2
    sudo apt install mysql-server
    sudo apt install php libapache2-mod-php php-mysql php-curl php-gd php-mbstring php-xml php-xmlrpc php-soap php-intl php-zip
    ```
    
4. 양방향 복사 설정
    1. 장치 > 클립보드 공유 > 양방향 > 재부팅
        
        ![스크린샷 2025-01-24 오전 9.49.44.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/cf024025-486d-4514-84ae-3a7c5951c17c/b823799a-ab26-4720-ab82-590f7ec02791/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2025-01-24_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_9.49.44.png)
        
    2. 🚨 게스트확장 CD 삽입 오류
    3. Intelchip / windows와 같은 방식으로 설치하려고 함.
        1. 맥 실리콘에서는 amd.iso 이미지는 지원되지 않고, arm 버전은 없다고 한다…
        2. 그래서 강제로 이미지 수동 삽입을 해보았다.
        3. https://ciksiti.com/ko/chapters/3174-how-do-i-enable-copy-and-paste-between-virtualbox-and-host
    
    ```bash
    # 먼저 새로 생성 /mnt/cdrom 예배 규칙서:
    $ sudo mkdir -p /mnt/cdrom
    
    # 생성 후 이미지를 마운트 /mnt/cdrom:
    $ sudo mount /dev/cdrom /mnt/cdrom
    
    # 디렉토리를 다음으로 변경 /mnt/cdrom "를 실행VBoxLinuxAddition.run" 스크립트:
    $ cd /mnt/cdrom
    $ sudo sh ./VBoxLinuxAdditions.run --nox11
    
    # 강제로 VirtualBox Guest Additions 설치
    $ sudo apt install -y virtualbox-guest-utils virtualbox-guest-module
    
    # 스크립트가 실행을 완료하면. 아래에 언급된 명령을 사용하여 시스템을 재부팅하십시오.
    $ sudo 종료 -r 지금
    ```
    
    - 결과
        
        [화면 기록 2025-01-24 오전 2.31.03.mov](https://prod-files-secure.s3.us-west-2.amazonaws.com/cf024025-486d-4514-84ae-3a7c5951c17c/689d30b7-d395-4a79-9229-090a69daade4/%E1%84%92%E1%85%AA%E1%84%86%E1%85%A7%E1%86%AB_%E1%84%80%E1%85%B5%E1%84%85%E1%85%A9%E1%86%A8_2025-01-24_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_2.31.03.mov)
        
    - *참고로 : 리눅스 내에서 복사 붙여넣기는 Ctrl+C Ctrl+V (Command 아닙니다.)*
5. 공유폴더 설정
    1. 설정 > 공유 폴더
        
        ![스크린샷 2025-01-24 오전 10.08.13.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/cf024025-486d-4514-84ae-3a7c5951c17c/648c7006-6764-41c1-a8ab-e4879e84feb6/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2025-01-24_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_10.08.13.png)
        
    2. 마운트 설정
        
        ```bash
        # 필요한 패키지 설치
        sudo apt update
        sudo apt install -y build-essential linux-headers-$(uname -r)
        
        # 마운트 포인트 생성
        sudo mkdir /mnt/shared
        
        # 공유폴더 마운트
        sudo mount -t vboxsf 공유폴더명 /mnt/shared
        
        # 권한 설정
        sudo usermod -aG vboxsf $USER
        ```
        
6. 어댑터 브릿지 설정
    1. 어댑터 브릿지란?
        1. 인터넷 공유기에서 제공하는 DHCP 기능을 활용하여 HOST OS와 동일한 Class 대역의 IP를 Virtual BOX에 할당
            
            ![스크린샷 2025-01-24 오전 10.06.59.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/cf024025-486d-4514-84ae-3a7c5951c17c/f655acb0-4746-4bc1-94db-c476cf159a5a/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2025-01-24_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_10.06.59.png)
            
    2. 설정 > 네트워크 > 어댑터 1 > **[NAT → 어댑터에 브리지 변경]**
        
        ![스크린샷 2025-01-24 오전 10.07.40.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/cf024025-486d-4514-84ae-3a7c5951c17c/2cc173fd-46ef-48d1-b189-50cc00839a82/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2025-01-24_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_10.07.40.png)
        
    

### 2. 서비스 구축

1. SSH 서버 설치 및 설정
    
    ```bash
    # SSH 서버 설치
    sudo apt update
    sudo apt install openssh-server
    
    # SSH 서비스 상태 확인
    sudo systemctl status ssh
    
    # SSH 서비스 시작
    sudo systemctl start ssh
    
    # 시스템 부팅시 자동 시작 설정
    sudo systemctl enable ssh
    ```
    
2. 방화벽 설정
    
    ```bash
    sudo ufw allow ssh
    sudo ufw enable
    ```
    
3. 아파치 (Apache 웹서버 설정)
    
    ```bash
    # 아파치2 설치
    sudo apt install apache2
    
    # 아파치 서비스 시작
    sudo systemctl start apache2
    
    # 시스템 부팅시 자동 시작 설정
    sudo systemctl enable apache2
    
    # 아파치 상태 확인
    sudo systemctl status apache2
    ```
    
4. 방화벽에 웹 서버 포트 허용
    
    ```bash
    sudo ufw allow 'Apache'
    ```
    
5. net-tools 설치
    
    ```bash
    sudo apt install net-tools
    ```
    

### 3. APM 활용한 워드 프레스 구축 및 이력서

1. APM(Apache + PHP + MySQL) 은 LAMP 스택 설치할 때 이미 설치함
2. 한 번에 관리하고 싶어서 Docker-Compose 로 설치함
    1. [**Docker로 WordPress 설치**](https://www.notion.so/Docker-WordPress-183c3df01503807e85b7d9ef9de653eb?pvs=21) 
3. 🚨docker-compose.yml 수정
    1. *~~ARM64 버전은 오류가 너무 많구나~~*
    2. https://stackoverflow.com/questions/54470921/is-there-a-mysql-docker-image-for-arm64-that-works-like-the-official-image
    3. `ARM64` is [being supported](https://hub.docker.com/r/mysql/mysql-server/) with mySQL 8.0 (only);
    
    ```yaml
    version: '3.8'
    services:
      db:
        image: arm64v8/mysql:8.0
        command: '--default-authentication-plugin=mysql_native_password'
        volumes:
          - db_data:/var/lib/mysql
        restart: always
        environment:
          - MYSQL_ROOT_PASSWORD=somewordpress
          - MYSQL_DATABASE=wordpress
          - MYSQL_USER=wordpress
          - MYSQL_PASSWORD=wordpress
        expose:
          - 3306
          - 33060
    
      wordpress:
        image: arm64v8/wordpress:latest
        ports:
          - "8081:80"
        restart: always
        environment:
          - WORDPRESS_DB_HOST=db
          - WORDPRESS_DB_USER=wordpress
          - WORDPRESS_DB_PASSWORD=wordpress
          - WORDPRESS_DB_NAME=wordpress
    
      nginx-proxy:
        image: 'jc21/nginx-proxy-manager:latest'
        ports:
          - "8080:80"
          - "443:443"
        volumes:
          - proxy_data:/data
          - proxy_letsencrypt:/etc/letsencrypt
        environment:
          - DB_SQLITE_FILE=/data/database.sqlite
        networks:
          - proxy
    
    volumes:
      db_data:
      proxy_data:
      proxy_letsencrypt:
    
    networks:
      proxy:
        driver: bridge
    ```
    
4. 🚨 이미지 오류
    1. 증상 : host 컴퓨터에서 http://192.168.1.6:8081/ 접속 시 다른 자원들이 표시가 안됨
    
    ![스크린샷 2025-01-24 오전 9.38.08.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/cf024025-486d-4514-84ae-3a7c5951c17c/f2e71328-418f-4df1-adfb-1e0f6a01875a/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2025-01-24_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_9.38.08.png)
    
5. 해결:
내부적으로 [localhost](http://localhost) 기준으로 자원(이미지 등)의 경로를 저장하고 있어다. 외부 IP로 설정해줌.
    
    ![스크린샷 2025-01-24 오전 9.42.16.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/cf024025-486d-4514-84ae-3a7c5951c17c/ab3c35ff-9247-4863-bbd3-1aadcc365de6/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2025-01-24_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_9.42.16.png)
    
6. **동영상캡쳐**
    
    [화면 기록 2025-01-24 오전 10.47.35.mov](https://prod-files-secure.s3.us-west-2.amazonaws.com/cf024025-486d-4514-84ae-3a7c5951c17c/d40c9999-db50-41f4-b26a-8d33dc8a66e8/%E1%84%92%E1%85%AA%E1%84%86%E1%85%A7%E1%86%AB_%E1%84%80%E1%85%B5%E1%84%85%E1%85%A9%E1%86%A8_2025-01-24_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_10.47.35.mov)
    
7. 소스코드. zip
    
    [my-resume.2.3.8.zip](https://prod-files-secure.s3.us-west-2.amazonaws.com/cf024025-486d-4514-84ae-3a7c5951c17c/eaad7479-9959-40b9-b680-4e84d09a5611/my-resume.2.3.8.zip)
    

### 4. 칼리 리눅스를 활용한 SQL Injection 공격 및 방어 방안

1. 칼리 리눅스 설치
    1. [**Kali Linux 2024.4 Changelog (**Apple Silicon (ARM 64))](https://www.kali.org/get-kali/#kali-installer-images) 다운로드\
    2. 설치 완료
2. 칼리 리눅스 세팅
    1. 리눅스 기본 세팅 (1-3 ~ 과정과 동일)
    2. 🚨 한글 깨짐 현상 해결
        1. https://codelabs.tistory.com/16
    3. 한글 입력 설치
        1. https://blog.naver.com/snova84/223375729207
    4. victim / attacker 복제 후, 네트워크 : 어댑터에 브리지 설정 완료
        
        ![스크린샷 2025-01-24 오후 6.33.18.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/cf024025-486d-4514-84ae-3a7c5951c17c/fe5fccd0-3aac-4175-b841-cf02d18c6294/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2025-01-24_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_6.33.18.png)
        
    5. 서로 연결 확인 완료
        
        ![스크린샷 2025-01-24 오후 6.32.43.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/cf024025-486d-4514-84ae-3a7c5951c17c/c74e091a-b4f2-4042-9efa-9db7518a072b/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2025-01-24_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_6.32.43.png)
        
    6. (현재 본인 비밀번호가 8자리 여서 : 4억개 이상의 조합을 만들어야해서 bruteForce 에러 발생)