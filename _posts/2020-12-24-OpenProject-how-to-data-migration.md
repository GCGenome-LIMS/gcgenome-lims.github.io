---
layout: post
title: "[How-To] OpenProject Data Migration"
date:   2020-12-24
categories:
tags: post 
---

# OpenProject Data Migration
172.19.210.164 서버(NAS) 에서 172.19.208.121 서버(virgo) 로 OpenProject migration 하기

# Prerequisites
시작하기 전에, 기본 Docker 개념에 대해 이미 알고 있다고 가정합니다.   
Docker 개념에 대해 잘 모른다면 Docker에 대한 자습서를 살펴볼 것을 권장합니다.

# How-To
1. NAS서버에서 openproject data 확인합니다.

 ![img1](/assets/img/2020-12-24-OpenProject-how-to-data-migration-img1.png)  
 → /volume1/OpenProject/var/lib/openproject에서 pgdata 와 static을 migration 


2. scp를 사용하여 virgo서버의 /data/openproject/opdata로 이동
- pgdata
![img2](/assets/img/2020-12-24-OpenProject-how-to-data-migration-img2.png)   
- static
![img3](/assets/img/2020-12-24-OpenProject-how-to-data-migration-img3.png)



3. Docker로 OpenProject를 설치합니다.
 - 다음 명령을 사용하여 컨테이너가 다시 시작될 때 데이터가 저장 될 로컬 디렉토리를 만들고 해당 디렉토리가 마운트 된 상태로 컨테이너를 시작할 수 있습니다.
 ex) /data/openproject/opdata : 모든 데이터가 저장 될 디렉토리

 ![img5](/assets/img/2020-12-24-OpenProject-how-to-data-migration-img5.png)
 - 컨테이너 이름을 지정 한 후 다음을 실행하여 중지할 수 있습니다.  
 docker stop openproject
 - 그리고 다시 시작합니다.  
 docker start openproject


4. migration 확인

 ![img6](/assets/img/2020-12-24-OpenProject-how-to-data-migration-img6.png)

