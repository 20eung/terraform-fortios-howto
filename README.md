# terraform-fortios-howto

How to use FortiOS as terraform provider.

Terraform에서 FortiGate 장비를 사용하기 위한 방법 입니다.

사전 준비해야 할 것이 있으며, 모듈 module 사용 시 주의가 필요합니다.


> ## 1. Create a REST API administrator:
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

```
config system accprofile
    edit "api_super_admin"
        set sysgrp read-write
        set netgrp read-write
        set fwgrp read-write
        set vpngrp read-write
    next
end

config system api-user
    edit "terraform"
        set comments "terraform automation"
        set api-key ~~~~~~
        set accprofile "super_admin"
        set vdom "root"
        config trusthost
            edit 1
                set ipv4-trusthost 1.2.3.4 255.255.255.255
            next
        end
    next
end
```

> ## 2. CA 인증서 생성

[ 인증서 만들기 ](https://github.com/20eung/make_crt_file) 바로가기


> ## 3. CA 인증서 등록

1. _System_ > Certificates > [ _Import_ ]

* _Local Certificate_ 선택

  ![CA Import](./img/system-certificates-import-01.png "CA Import")

* _CRT_, _KEY_ 파일 등록, 인증서 만들 때 사용한 _Password_ 등록

  ![CA Import](./img/system-certificates-import-02.png "CA Import")

* 등록 완료 화면

  ![CA Import](./img/system-certificates-import-03.png "CA Import")

```
config certificate local
    edit "terraform"
        set password pass-phrase-for-terraform-key-file
        set private-key "-----BEGIN ENCRYPTED PRIVATE KEY-----
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
-----END ENCRYPTED PRIVATE KEY-----"
        set certificate "-----BEGIN CERTIFICATE-----
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
-----END CERTIFICATE-----"
    next
end

config vpn certificate local
    edit "terraform"
        set range global
    next
end
```

> ## 4. 환경변수 설정

#### terraform:~/ $ _cat ~/.bashrc_              
```
export "FORTIOS_ACCESS_HOSTNAME"="10.10.10.10"    # FortiGate 장비 IP 
export "FORTIOS_ACCESS_TOKEN"="9kNtm6bx0~~~~~"    # REST API key(1-5에서 생성)
export "FORTIOS_INSECURE"="true"
```


> ## 5. Terraform에서 사용하기

>> ### provider 정의

#### terraform:~/fortios (main) $ _cat provider.tf_
```
terraform {
    required_providers {
        fortios	= {
	        source	= "fortinetdev/fortios"
	    }
    }
}

provider "fortios" {}
```

>> ### module에서 사용하는 방법


#### terraform:~/fortios (main) $ _tree_ ####
```
.
├── main.tf
├── modules
│   ├── bgp
│   │   ├── outputs.tf
│   │   ├── route.tf
│   │   ├── sdwan.tf
│   │   ├── system_interface.tf
│   │   ├── variables.tf
│   │   └── vpn.tf
│   └── static
│       ├── route.tf
│       ├── variables.tf
│       └── vpn.tf
├── provider.tf
├── terraform.tfstate
├── terraform.tfstate.backup
├── variables.auto.tfvars
└── variables.tf
```

#### terraform:~/fortios (main) $ _cat main.tf_
```
module "fortios_vpn" {
    source              = "./modules/bgp"

    azure_vpns          = var.azure_us_vpns
    us_site             = var.us_site
    sla_server          = var.sla_server
}
```

모듈에서도 provider 정의를 다시 한번 해줘야 함.

정의하지 않으면 hashicorp/fortios를 참조하려고 하며 에러 발생.

#### terraform:~/fortios/modules/bgp (main) $ _cat vpn.tf_
```
terraform {
    required_providers {
        fortios	= {
	        source	= "fortinetdev/fortios"
	    }
    }
}

resource "fortios_vpnipsec_phase1interface" "azure-us-1" {
:
}
```
