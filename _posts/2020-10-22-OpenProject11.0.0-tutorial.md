---
layout: post
title: "[Tutorial] OpenProject 11.0.0 튜토리얼"
date:   2020-10-22
categories:
tags: post
---

```
Requires: CentOS 8
```

# OpenProject
OpenProject는 전체 프로젝트 수명주기 동안 팀을 지원하는 무료 오픈 소스 소프트웨어 입니다. 
OpenProject를 사용하면 초기 프로젝트 아이디어부터 프로젝트 종료 및 문서화까지 시스템 중단없이 프로젝트 협업 및 커뮤니케이션이 가능합니다.

![img0](/assets/img/2020-10-22-OpenProject11.0.0-tutorial-img0.png)


# OpenProject 설치
OpenProject는 다음 세 가지 방법으로 설정할 수 있습니다.

DEB/RPM 패키지로 설치              : OpenProject를 설치하는 데 권장되는 방법입니다.
Docker로 설치                      : 이를 통해 Docker를 사용하여 격리 된 방식으로 OpenProject를 설정할 수 있습니다.
Univention Corporate Server로 설치 : OpenProject는 App Center에서 사용할 수 있으며 ID관리와 통합됩니다.
다른                               : Kubernetes와 같은 특정 플랫폼에 OpenProject를 설치하는 방법입니다.

이 튜토리얼에서는 DEB/RPM 패키지로 설치하는 방법에 대해 설명합니다.


# CentOS 8에 OpenProject 11.0.0 설치
