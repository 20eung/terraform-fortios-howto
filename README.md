# terraform-fortios-howto

How to use FortiOS as terraform provider

> ## Create a REST API administrator:
1. _System_ > _Admin Profile_ > [ _+Create New_ ]
* Name: _api_super_admin_
* Access Permissions: [ None | Read | Read/Write | Custom ]
![Admin Profile](./img/system-admin_profiles-01.png "Admin Profile")

2. _OK_

3. _System_ > _Administrators_ > [ _+Create New_ ] > _REST API Admin_
* Username: terraform-api
* Administrator Profile: api_super_admin
![Administrators](./img/system-administrators-rest-api-01.png "Administrators")

4. Trusted Hosts 설정

5. _OK_
* API Key는 한 번만 표시되므로 복사하여 안전한 곳에 저장
* API key는 terraform에서 token 으로 사용됨

> ## CA 인증서 생성
[ 인증서 만들기 ](https://github.com/20eung/make_crt_file) 바로가기

> ## CA 인증서 등록
1. _System_ > Certificates > [ _Import_ ]
* _Local Certificate_ 선택

  ![CA Import](./img/system-certificates-import-01.png "CA Import")

* _CRT_, _KEY_ 파일 등록, 인증서 만들 때 사용한 _Password_ 등록

  ![CA Import](./img/system-certificates-import-02.png "CA Import")

* 등록 완료 화면

  ![CA Import](./img/system-certificates-import-03.png "CA Import")
