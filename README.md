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
