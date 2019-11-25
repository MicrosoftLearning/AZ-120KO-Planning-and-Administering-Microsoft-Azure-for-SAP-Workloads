---
lab:
    title: '3a: Azure VM에서 Linux 클러스터링 구현'
    module: '모듈 3: Azure의 SAP 인증 제공'
---

# AZ 120 모듈 3: Azure의 SAP 인증 제공
# 랩 3a: Azure VM에서 Linux 클러스터링 구현

예상 시간: 90분

이 랩의 모든 작업은 Azure Portal(Bash Cloud Shell 세션 포함)에서 수행됩니다.  

   > **참고**: Cloud Shell을 사용하지 않는 경우 랩 가상 머신에 Azure CLI를 설치([**https://docs.microsoft.com/ko-kr/cli/azure/install-azure-cli-windows?view=azure-cli-latest**](https://docs.microsoft.com/ko-kr/cli/azure/install-azure-cli-windows?view=azure-cli-latest))하고 [**https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html**](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)에서 제공되는 SSH 클라이언트(예: PuTTY)를 포함해야 합니다.

랩 파일: 없음

## 시나리오
  
Azure 기반 SAP HANA 배포를 준비하기 위해 Adatum Corporation에서는 Linux의 SUSE 배포를 실행하는 Azure VM에 클러스터링을 구현하는 프로세스를 살펴보려고 합니다.

## 목표
  
이 랩을 완료하면 다음과 같은 역량을 갖추게 됩니다.

-   고가용성 SAP HANA 배포를 지원하는 데 필요한 Azure 컴퓨팅 리소스 프로비전

-   고가용성 SAP HANA 설치를 지원하도록 Linux를 실행하는 Azure VM의 운영 체제 구성

-   고가용성 SAP HANA 배포를 지원하는 데 필요한 Azure 네트워크 리소스 프로비전

## 요구 사항

-   Microsoft Azure 구독

-   Windows 10, Windows Server 2016 또는 Windows Server 2019를 실행하고 Azure에 액세스할 수 있는 랩 컴퓨터


## 연습 1: 고가용성 SAP HANA 배포를 지원하는 데 필요한 Azure 컴퓨팅 리소스 프로비전

재생 시간: 30분

이 연습에서는 Linux 클러스터링을 구성하는 데 필요한 Azure 인프라 컴퓨팅 구성 요소를 배포합니다. 여기에는 동일한 가용성 집합에서 Linux SUSE를 실행하는 Azure VM 쌍을 만드는 작업이 포함됩니다.

### 작업 1: Linux SUSE를 실행하는 Azure VM 배포

1.  랩 컴퓨터에서 웹 브라우저를 시작하고 Azure Portal(https://portal.azure.com)로 이동합니다.

1.  메시지가 표시되면 이 랩에 사용할 Azure 구독에 대한 소유자 또는 기여자 역할이 있는 직장, 학교 또는 개인 Microsoft 계정으로 로그인합니다.

1.  Azure Portal에서 **+ 리소스 만들기** 를 클릭합니다.

1.  **새로 만들기** 블레이드에서 다음 설정을 사용하여 **SLES 12 SP3(BYOS)** 이미지에 따라 새 Azure VM 만들기를 시작합니다.

    -   구독: *Azure 구독의 이름*

    -   리소스 그룹: **az12001a-RG***라는 이름의 새 리소스 그룹*

    -   가상 머신 이름: **az12001a-vm0**

    -   지역: *랩 위치에 가장 가깝고 Azure VM을 배포할 Azure 지역*

    -   가용성 옵션: **가용성 집합**

    -   가용성 집합: *이름이* **az12001a-avset***이고 2개의 장애 도메인과 5개의 업데이트 도메인이 있는 새 가용성 집합*

    -   이미지: **SUSE Linux Enterprise Server (SLES) 12 SP3(BYOS)**

    -   크기: **표준 D4s v3**

    -   인증 유형: **암호**

    -   사용자 이름: **student**

    -   암호: **Pa55w.rd1234**

    -   공용 인바운드 포트: **선택한 포트 허용**

    -   선택한 인바운드 포트: **SSH (22)**

    -   OS 디스크 유형: **프리미엄 SSD**

    -   가상 네트워크: **az12001a-RG-vnet***라는 이름의 새 가상 네트워크*

    -   주소 공간: **192.168.0.0/20**

    -   서브넷 이름: **subnet-0**

    -   서브넷 주소 범위: **192.168.0.0/24**

    -   공용 IP 주소: **az12001a-vm0-ip***라는 이름의 새 IP 주소*

    -   NIC 네트워크 보안 그룹: **고급**

    -   네트워크 보안 그룹 구성: *이름이* **az12001a-vm0-nsg** 이고 *기본 설정(인바운드 SSH 연결 허용)을 사용하는 새 네트워크 보안 그룹*

    -   가속화된 네트워킹: **꺼짐**

    -   기존 부하 분산 솔루션 뒤에 이 가상 머신을 배치: **아니요**

    -   무료로 기본 계획 사용: **아니요**

    -   부팅 진단: **꺼짐**

    -   OS 게스트 진단: **꺼짐**

    -   시스템 할당 관리 ID: **꺼짐**
	
    -	AAD 자격 증명으로 로그인(미리 보기): **해제**

    -   자동 종료 사용 설정: **꺼짐**

    -   확장: *없음*

    -   태그: **없음**

1.  프로비전이 완료될 때까지 기다립니다. 이 작업은 몇 분 정도 걸립니다.

1.  Azure Portal에서 **+ 리소스 만들기**를 클릭합니다.

1.  **새로 만들기** 블레이드에서 다음 설정을 사용하여 **SLES 12 SP3(BYOS)** 이미지에 따라 새 Azure VM 만들기를 시작합니다.

    -   구독: *Azure 구독의 이름*

    -   리소스 그룹: **az12001a-RG**

    -   가상 머신 이름: **az12001a-vm1**

    -   지역: *첫 번째 Azure VM을 배포한 지역과 동일한 Azure 지역*

    -   가용성 옵션: **가용성 집합**

    -   가용성 세트: **az12001a-avset**

    -   이미지: **SUSE Linux Enterprise Server (SLES) 12 SP3(BYOS)**

    -   크기: **표준 D4s v3**

    -   인증 유형: **암호**

    -   사용자 이름: **student**

    -   암호: **Pa55w.rd1234**

    -   공용 인바운드 포트: **선택한 포트 허용**

    -   선택한 인바운드 포트: **SSH (22)**

    -   OS 디스크 유형: **프리미엄 SSD**

    -   가상 네트워크: **az12001a-RG-vnet**

    -   서브넷 이름: **subnet-0 (192.168.0.0/24)**

    -   공용 IP 주소: **az12001a-vm1-ip***라는 이름의 새 IP 주소*

    -   NIC 네트워크 보안 그룹: **고급**

    -   네트워크 보안 그룹 구성: *이름이* **az12001a-vm1-nsg**이고 *기본 설정(인바운드 SSH 연결 허용)을 사용하는 새 네트워크 보안 그룹*

    -   가속화된 네트워킹: **꺼짐**

    -   기존 부하 분산 솔루션 뒤에 이 가상 머신을 배치: **아니요**

    -   무료로 기본 계획 사용: **아니요**

    -   부팅 진단: **꺼짐**

    -   OS 게스트 진단: **꺼짐**

    -   시스템 할당 관리 ID: **꺼짐**
	
    - 	AAD 자격 증명으로 로그인(미리 보기): **해제**

    -   자동 종료 사용 설정: **꺼짐**

    -   확장: *없음*

    -   태그: **없음**

1.  프로비전이 완료될 때까지 기다립니다. 이 작업은 몇 분 정도 걸립니다.


### 작업 2: Azure VM 디스크 만들기 및 구성

1.  Azure Portal에서 Cloud Shell의 Bash 세션을 시작합니다. 

    > **참고**: 현재 Azure 구독에서 Cloud Shell을 처음 시작하는 경우 Cloud Shell 파일을 유지할 Azure 파일 공유를 만들라는 메시지가 표시됩니다. 이 경우 기본값을 허용하면 자동으로 생성된 리소스 그룹에 스토리지 계정이 만들어집니다.

1.  Cloud Shell 창에서 다음 명령을 실행하여 8개의 관리 디스크로 구성된 첫 번째 세트를 만듭니다. 이 세트는 이전 작업에서 배포한 첫 번째 Azure VM에 연결됩니다.

```
RESOURCE_GROUP_NAME='az12001a-RG'

LOCATION=$(az group list --query "[?name == '$RESOURCE_GROUP_NAME'].location" --output tsv)

for I in {0..7}; do az disk create --resource-group $RESOURCE_GROUP_NAME --name az12001a-vm0-DataDisk$I --size-gb 128 --location $LOCATION --sku Premium_LRS; done
```

1.  Cloud Shell 창에서 다음 명령을 실행하여 8개의 관리 디스크로 구성된 두 번째 세트를 만듭니다. 이 세트는 이전 작업에서 배포한 두 번째 Azure VM에 연결됩니다.

```
for I in {0..7}; do az disk create --resource-group $RESOURCE_GROUP_NAME --name az12001a-vm1-DataDisk$I --size-gb 128 --location $LOCATION --sku Premium_LRS; done
```

1.  Azure Portal에서 이전 작업에서 프로비전한 첫 번째 Azure VM(**az12001a-vm0**)의 블레이드로 이동합니다.

1.  **az12001a-vm0** 블레이드에서 **az12001a-vm0 - 디스크** 블레이드로 이동합니다.

1.  **az12001a-vm0 - 디스크** 블레이드에서 다음 설정을 사용하여 데이터 디스크를 az12001a-vm0에 연결합니다.

    -   LUN: **0**

    -   디스크 이름: **az12001a-vm0-DataDisk0**

    -   리소스 그룹: **az12001a-RG**

    -   호스트 캐싱: **없음**

1.  이전 단계를 반복하여 나머지 7개의 디스크를 접두사 **az12001a-vm0-DataDisk**를 사용하여 연결합니다(총 8개). 디스크 이름의 마지막 문자와 일치하는 LUN 번호를 할당합니다.

1.  변경 내용을 저장합니다. 

1.  Azure Portal에서 이전 작업에서 프로비전한 두 번째 Azure VM(**az12001a-vm1**)의 블레이드로 이동합니다.

1.  **az12001a-vm1** 블레이드에서 **az12001a-vm1 - 디스크** 블레이드로 이동합니다.

1.  **az12001a-vm1 - 디스크** 블레이드에서 다음 설정을 사용하여 데이터 디스크를 az12001a-vm1에 연결합니다.

    -   LUN: **0**

    -   디스크 이름: **az12001a-vm1-DataDisk0**

    -   리소스 그룹: **az12001a-RG**

    -   호스트 캐싱: **읽기 전용**

1.  이전 단계를 반복하여 나머지 7개의 디스크를 접두사 **az12001a-vm1-DataDisk**를 사용하여 연결합니다(총 8개). 디스크 이름의 마지막 문자와 일치하는 LUN 번호를 할당합니다. LUN **1**이 있는 디스크의 호스트 캐싱을 **읽기 전용**으로 설정하고 나머지 모든 디스크의 호스트 캐싱을 **없음**으로 설정합니다.

1.  변경 내용을 저장합니다. 

> **결과**: 이 연습을 완료한 후 고가용성 SAP HANA 배포를 지원하는 데 필요한 Azure 컴퓨팅 리소스가 프로비전되었습니다.


## 연습 2: 고가용성 SAP HANA 설치를 지원하도록 Linux를 실행하는 Azure VM의 운영 체제 구성

재생 시간: 30분

이 연습에서는 SAP HANA의 클러스터된 설치를 수용하도록 SUSE Linux Enterprise Server를 실행하는 Azure VM의 운영 체제 및 스토리지를 구성합니다.

### 작업 1: Azure Linux VM에 연결

1.  Azure Portal에서 Cloud Shell의 Bash 세션을 시작합니다. 

1.  Cloud Shell 창에서 다음 명령을 실행하여 이전 연습에서 배포한 첫 번째 Azure VM의 공용 IP 주소를 식별합니다.

```
RESOURCE_GROUP_NAME='az12001a-RG'

PIP=$(az network public-ip show --resource-group $RESOURCE_GROUP_NAME --name az12001a-vm0-ip --query ipAddress --output tsv)
```

1.  Cloud Shell 창에서 다음 명령을 실행하여 이전 단계에서 식별한 IP 주소에 대한 SSH 세션을 설정합니다.

```
ssh student@$PIP
```

1.  연결을 계속할지 묻는 메시지가 표시되면 `yes`를 입력하고 **Enter** 키를 누릅니다. 

1.  암호를 입력하라는 메시지가 표시되면 `Pa55w.rd1234`를 입력하고 **Enter** 키를 누릅니다. 

1.  Cloud Shell 도구 모음에서 **새 세션 열기** 아이콘을 클릭하여 다른 Cloud Shell Bash 세션을 엽니다.

1.  새로 열린 Cloud Shell Bash 세션에서 이 태스크의 모든 단계를 반복하여 IP 주소 **az12001a-vm0-ip** 를 통해 **az12001a-vm1** Azure VM에 연결합니다.


### 작업 2: Linux를 실행하는 Azure VM의 스토리지 구성

1.  Cloud Shell 창에서 실행 중인 az12001a-vm0으로의 SSH 세션에서 다음 명령을 실행하여 권한을 높이고 메시지가 표시되면 Student 사용자 계정의 암호를 입력합니다.  

```
sudo su -
```

1.  암호를 입력하라는 메시지가 표시되면 `Pa55w.rd1234`를 입력하고 **Enter** 키를 누릅니다. 

1.  Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 `lsscsi`를 실행하여 새로 연결된 장치와 해당 LUN 번호 간의 매핑을 식별합니다.
    
```
lsscsi
```

1.  Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 6개(8개 중) 데이터 디스크에 대한 물리적 볼륨을 만듭니다.
    
```
pvcreate /dev/sdc
pvcreate /dev/sdd
pvcreate /dev/sde
pvcreate /dev/sdf
pvcreate /dev/sdg
pvcreate /dev/sdh
```

1.  Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 볼륨 그룹을 만듭니다.
    
```
vgcreate vg_hana_data /dev/sdc /dev/sdd
vgcreate vg_hana_log /dev/sde /dev/sdf
vgcreate vg_hana_backup /dev/sdg /dev/sdh
```

1.  Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 논리적 볼륨을 만듭니다.

```
lvcreate -l 100%FREE -n hana_data vg_hana_data
lvcreate -l 100%FREE -n hana_log vg_hana_log
lvcreate -l 100%FREE -n hana_backup vg_hana_backup
```

    > **참고**: 각 볼륨 그룹별로 단일 논리 볼륨을 만들 것입니다.

1.  Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 논리적 볼륨을 포맷합니다.

```
mkfs.xfs /dev/vg_hana_data/hana_data
mkfs.xfs /dev/vg_hana_log/hana_log
mkfs.xfs /dev/vg_hana_backup/hana_backup
```

1.  Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 **/dev/sdi** 디스크의 파티션을 나눕니다.

```
fdisk /dev/sdi
```

1.  메시지가 표시되면 `n`, `p`, `1`을 순서대로 입력(입력 후 매번 **Enter** 키를 누름)한 다음 **Enter** 키를 두 번 누르고 `w`를 입력하여 쓰기를 완료합니다.

1.  Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 **/dev/sdj** 디스크의 파티션을 나눕니다.

```
fdisk /dev/sdj
```

1.  메시지가 표시되면 `n`, `p`, `1`을 순서대로 입력(입력 후 매번 **Enter** 키를 누름)한 다음 **Enter** 키를 두 번 누르고 `w`를 입력하여 쓰기를 완료합니다.

1.  Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 새로 만든 파티션을 포맷합니다.

```
mkfs.xfs /dev/sdi -f
mkfs.xfs /dev/sdj -f
```

1.  Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 탑재 지점으로 사용할 디렉터리를 만듭니다.

```
mkdir -p /hana/data
mkdir -p /hana/log
mkdir -p /hana/backup
mkdir -p /hana/shared
mkdir -p /usr/sap
```

1.  Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 논리적 볼륨의 ID를 표시합니다.

```
blkid
```

    > **참고**: **/dev/sdi**(**/hana/shared**에 대해 사용됨) 및 **dev/sdj**(**/usr/sap**에 사용됨)를 포함하여 새로 만들어진 볼륨 그룹 및 파티션과 연결된 **UUID** 값을 식별합니다.


1.  Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 **/etc/fstab**을 vi 편집기에서 엽니다(자유롭게 다른 편집기를 사용할 수 있음).

```
vi /etc/fstab
```

1.  편집기에서 **/etc/fstab**에 다음 항목을 추가합니다(여기서 `\<UUID of /dev/vg\_hana\_data-hana\_data\>`, `\<UUID of /dev/vg\_hana\_log-hana\_log\>`, `\<UUID of /dev/vg\_hana\_backup-hana\_backup\>`, `\<UUID of /dev/vg_hana_shared-hana_shared (/dev/sdi)\>` 및 `\<UUID of /dev/vg_usr_sap-usr_sap (/dev/sdj)\>`는 이전 단계에서 식별한 ID를 나타냄).

```
/dev/disk/by-uuid/<UUID of /dev/vg_hana_data-hana_data> /hana/data xfs  defaults,nofail  0  2
/dev/disk/by-uuid/<UUID of /dev/vg_hana_log-hana_log> /hana/log xfs  defaults,nofail  0  2
/dev/disk/by-uuid/<UUID of /dev/vg_hana_backup-hana_backup> /hana/backup xfs  defaults,nofail  0  2
/dev/disk/by-uuid/<UUID of /dev/vg_hana_shared-hana_shared (/dev/sdi)> /hana/shared xfs  defaults,nofail  0  2
/dev/disk/by-uuid/<UUID of /dev/vg_usr_sap-usr_sap (/dev/sdi)> /usr/sap xfs  defaults,nofail  0  2
```

1.  변경 내용을 저장하고 편집기를 닫습니다.

1.  Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 새 볼륨을 탑재합니다.

```
mount -a
```

1.  Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 탑재에 성공했는지 확인합니다.

```
df -h
```

1.  Cloud Shell Bash 세션을 az12001a-vm1로 전환하고 이 태스크의 모든 단계를 반복하여 **az12001a-vm1** 에서 스토리지를 구성합니다.


### 작업 3: 노드 간 암호 없는 SSH 액세스 사용

1.  Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 암호 없는 SSH 키를 생성합니다.

```
ssh-keygen -tdsa
```

1.  메시지가 표시되면 **Enter** 키를 세 번 누르고 다음을 실행하여 공개 키를 표시합니다. 

```
cat /root/.ssh/id_dsa.pub
```

1.  키 값을 클립보드에 복사합니다.

1.  SSH 세션이 포함된 Cloud Shell 창을 **az12001a-vm1**로 전환하고 다음 명령을 실행하여 **/root/.ssh/** 디렉터리를 만듭니다.

```
mkdir /root/.ssh
```

1.  Cloud Shell 창의 az12001a-vm1에 대한 SSH 세션에서 다음을 실행하고 vi 편집기에서 **/root/.ssh/authorized\_keys** 파일을 만듭니다(자유롭게 다른 편집기를 사용할 수 있음).

```
vi /root/.ssh/authorized_keys
```

1.  편집기 창에서 az12001a-vm0에서 생성한 키를 붙여 넣습니다.

1.  변경 내용을 저장하고 편집기를 닫습니다.

1.  Cloud Shell 창의 az12001a-vm1에 대한 SSH 세션에서 다음을 실행하여 암호 없는 SSH 키를 생성합니다.

```
ssh-keygen -tdsa
```

1.  메시지가 표시되면 **Enter** 키를 세 번 누르고 다음을 실행하여 공개 키를 표시합니다. 

```
cat /root/.ssh/id_dsa.pub
```

1.  키 값을 클립보드에 복사합니다.

1.  az12001a-vm0으로의 SSH 세션이 포함된 Cloud Shell 창으로 전환하고 다음 명령을 실행하여 vi 편집기에서 **/root/.ssh/authorized\_keys** 파일을 만듭니다. 다른 편집기를 사용해도 됩니다.

```
vi /root/.ssh/authorized_keys
```

1.  편집기 창에서 az12001a-vm1에서 생성한 키를 붙여 넣습니다.

1.  변경 내용을 저장하고 편집기를 닫습니다.

1.  Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 암호 없는 SSH 키를 생성합니다.

```
ssh-keygen -t rsa
```

1.  메시지가 표시되면 **Enter** 키를 세 번 누르고 다음을 실행하여 공개 키를 표시합니다. 

```
cat /root/.ssh/id_rsa.pub
```

1.  키 값을 클립보드에 복사합니다.

1.  **az12001a-vm1** 로의 SSH 세션이 포함된 Cloud Shell 창으로 전환하고 다음 명령을 실행하여 vi 편집기에서 **/root/.ssh/authorized\_keys** 파일을 엽니다. 다른 편집기를 사용해도 됩니다.

```
vi /root/.ssh/authorized_keys
```

1.  편집기 창에서 새 줄을 시작하여 az12001a-vm0에서 생성한 키를 붙여넣습니다.

1.  변경 내용을 저장하고 편집기를 닫습니다.

1.  Cloud Shell 창의 az12001a-vm1에 대한 SSH 세션에서 다음을 실행하여 암호 없는 SSH 키를 생성합니다.

```
ssh-keygen -t rsa
```

1.  메시지가 표시되면 **Enter** 키를 세 번 누르고 다음을 실행하여 공개 키를 표시합니다. 

```
cat /root/.ssh/id_rsa.pub
```

1.  키 값을 클립보드에 복사합니다.

1.  az12001a-vm0으로의 SSH 세션이 포함된 Cloud Shell 창으로 전환하고 다음 명령을 실행하여 vi 편집기에서 **/root/.ssh/authorized\_keys** 파일을 엽니다. 다른 편집기를 사용해도 됩니다.

```
vi /root/.ssh/authorized_keys
```

1.  편집기 창에서 새 줄을 시작하여 az12001a-vm1에서 생성한 키를 붙여넣습니다.

1.  변경 내용을 저장하고 편집기를 닫습니다.

1.  Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 vi 편집기에서 **/etc/ssh/sshd\_config** 파일을 엽니다(자유롭게 다른 편집기를 사용할 수 있음).

```
vi /etc/ssh/sshd_config
```

1.  **/etc/ssh/sshd\_config** 파일에서 **PermitRootLogin** 및 **AuthorizedKeysFile** 항목을 찾고 다음과 같이 구성합니다 필요한 경우 선행 **#** 문자를 제거합니다.
```
PermitRootLogin yes
AuthorizedKeysFile      /root/.ssh/authorized_keys
```

1.  변경 내용을 저장하고 편집기를 닫습니다.

1.  Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 sshd 디먼을 다시 시작합니다.

```
systemctl restart sshd
```

1.  az12001a-vm1에서 이전 네 단계를 반복합니다.

1.  구성이 정상적으로 적용되었는지 확인하려면 Cloud Shell 창에 표시된 az12001a-vm0으로의 SSH 세션에서 다음 명령을 실행하여 **root** 사용자로 az12001a-vm0에서 az12001a-vm1로의 SSH 세션을 설정합니다.

```
ssh root@az12001a-vm1
```

1.  연결을 계속할지 묻는 메시지가 표시되면 `yes`를 입력하고 **Enter** 키를 누릅니다.

1.  암호를 입력하라는 메시지가 표시되지 않는지 확인합니다.

1.  다음 명령을 실행하여 az12001a-vm0에서 az12001a-vm1로의 SSH 세션을 닫습니다.

```
exit
```

1.  다음 명령을 두 번 실행하여 az12001a-vm0에서 로그아웃합니다.

```
exit
```

1.  구성이 정상적으로 적용되었는지 확인하려면 Cloud Shell 창에 표시된 az12001a-vm1으로의 SSH 세션에서 다음 명령을 실행하여 **root** 사용자로 az12001a-vm1에서 az12001a-vm0로의 SSH 세션을 설정합니다.

```
ssh root@az12001a-vm0
```

1.  연결을 계속할지 묻는 메시지가 표시되면 `yes` 를 입력하고 **Enter** 키를 누릅니다.

1.  암호를 입력하라는 메시지가 표시되지 않는지 확인합니다.

1.  다음 명령을 실행하여 az12001a-vm1에서 az12001a-vm0로의 SSH 세션을 닫습니다.

```
exit
```

1.  다음 명령을 두 번 실행하여 az12001a-vm1에서 로그아웃합니다.

```
exit
```

> **결과**: 이 연습을 완료한 후 고가용성 SAP HANA 설치를 지원하도록 Linux를 실행하는 Azure VM의 운영 체제가 구성되었습니다.


## 연습 3: 고가용성 SAP HANA 배포를 지원하는 데 필요한 Azure 네트워크 리소스 프로비전

재생 시간: 30분

이 연습에서는 SAP HANA의 클러스터된 설치를 수용하도록 Azure Load Balancer를 구현합니다.


### 작업 1: 부하 분산 설정을 지원하도록 Azure VM을 구성합니다.

   > **참고**: 표준 SKU의 Azure Load Balancer 한 쌍을 설정할 것이므로 먼저 부하 분산된 백 엔드 풀로 사용할 두 Azure VM의 네트워크 어댑터에 연결된 공용 IP 주소를 제거해야 합니다.

1.  Azure Portal에서 **az12001a-vm0** Azure VM의 블레이드로 이동합니다. 

1.  **az12001a-vm0** 블레이드에서 네트워크 어댑터에 연결된 공용 IP 주소 **az12001a-vm0-ip** 의 블레이드로 이동합니다.

1.  **az12001a-vm0-ip** 블레이드에서 먼저 네트워크 인터페이스의 공용 IP 주소 연결을 끊은 다음 삭제합니다.

1.  Azure Portal에서 **az12001a-vm1** Azure VM의 블레이드로 이동합니다. 

1.  **az12001a-vm1** 블레이드에서 네트워크 어댑터에 연결된 공용 IP 주소 **az12001a-vm1-ip** 의 블레이드로 이동합니다.

1.  **az12001a-vm1-ip** 블레이드에서 먼저 네트워크 인터페이스의 공용 IP 주소 연결을 끊은 다음 삭제합니다.

1.  Azure Portal에서 **az12001a-vm0** Azure VM의 블레이드로 이동합니다.

1.  **az12001a-vm0** 블레이드에서 해당 **네트워킹** 블레이드로 이동합니다. 

1.  **az12001a-vm0 - 네트워킹** 블레이드에서 az12001a-vm0의 네트워크 인터페이스로 이동합니다. 

1.  az12001a-vm0의 네트워크 인터페이스 블레이드에서 IP 구성 블레이드로 이동하여 **ipconfig1** 블레이드를 표시합니다.

1.  **ipconfig1** 블레이드에서 프라이빗 IP 주소 할당을 **정적** 으로 설정하고 변경 내용을 저장합니다.

1.  Azure Portal에서 **az12001a-vm1** Azure VM의 블레이드로 이동합니다.

1.  **az12001a-vm1** 블레이드에서 해당 **네트워킹** 블레이드로 이동합니다. 

1.  **az12001a-vm1 - 네트워킹** 블레이드에서 az12001a-vm1의 네트워크 인터페이스로 이동합니다. 

1.  az12001a-vm1의 네트워크 인터페이스 블레이드에서 IP 구성 블레이드로 이동하여 **ipconfig1** 블레이드를 표시합니다.

1.  **ipconfig1** 블레이드에서 프라이빗 IP 주소 할당을 **정적**으로 설정하고 변경 내용을 저장합니다.


### 작업 2: 인바운드 트래픽을 처리하는 Azure Load Balancer 만들기 및 구성

1.  Azure Portal에서 **+ 리소스 만들기** 를 클릭합니다.

1.  **새로 만들기** 블레이드에서 다음 설정으로 새 Azure Load Balancer 만들기를 시작합니다.

    -   구독: *Azure 구독의 이름*

    -   리소스 그룹: **az12001a-RG**

    -   이름: **az12001a-lb0**

    -   지역: *이 랩의 첫 번째 연습에서 Azure VM을 배포한 것과 동일한 Azure 지역*

    -   유형: **내부**

    -   SKU: **Standard**

    -   가상 네트워크: **az12001a-RG-vnet**

    -   서브넷: **subnet-0**

    -   IP 주소 할당: **정적**

    -   IP 주소: **192.168.0.240**

    -   가용성 영역: **영역 중복**

1.  부하 분산 장치가 프로비전될 때까지 기다린 다음 Azure Portal에서 해당 블레이드로 이동합니다.

1.  **az12001a-lb0** 블레이드에서 다음 설정으로 백 엔드 풀을 추가합니다.

    -   이름: **az12001a-lb0-bepool**

    -   가상 네트워크: **az12001a-rg-vnet**

    -   가상 머신: **az12001a-vm0**  IP 주소: **ipconfig1**

    -   가상 머신: **az12001a-vm1**  IP 주소: **ipconfig1**

1.  **az12001a-lb0** 블레이드에서 다음 설정으로 상태 프로브를 추가합니다.

    -   이름: **az12001a-lb0-hprobe**

    -   프로토콜: **TCP**

    -   포트: **62500**

    -   간격: **5***초*

    -   비정상 임계값: **2***회 연속 실패*

1.  **az12001a-lb0** 블레이드에서 다음 설정으로 네트워크 부하 분산 규칙을 추가합니다.

    -   이름: **az12001a-lb0-lbruleAll**

    -   IP 버전: **IPv4**

    -   프론트 엔드 IP 주소: **192.168.0.240(LoadBalancerFrontEnd)**

    -   HA 포트: **사용**

    -   백 엔드 풀: **az12001a-lb0-bepool(가상 머신 2개)**

    -   상태 프로브:**az12001a-lb0-hprobe(TCP:62504)**

    -   세션 지속성: **없음**

    -   유휴 시간 초과(분): **4**

    -   부동 IP(Direct Server Return): **사용**

### 작업 3: 아웃바운드 트래픽을 처리하는 Azure Load Balancer 만들기 및 구성

1.  Azure Portal에서 Cloud Shell의 Bash 세션을 시작합니다. 

1.  Cloud Shell 창에서 다음 명령을 실행하여 두 번째 부하 분산 장치에 사용할 공용 IP 주소를 만듭니다.

```
RESOURCE_GROUP_NAME='az12001a-RG'

LOCATION=$(az group list --query "[?name == '$RESOURCE_GROUP_NAME'].location" --output tsv)

PIP_NAME='az12001a-lb1-pip'

az network public-ip create --resource-group $RESOURCE_GROUP_NAME --name $PIP_NAME --sku Standard --location $LOCATION
```

1.  Cloud Shell 창에서 다음 명령을 실행하여 두 번째 부하 분산 장치를 만듭니다.

```
LB_NAME='az12001a-lb1'

LB_BE_POOL_NAME='az12001a-lb1-bepool'

LB_FE_IP_NAME='az12001a-lb1-fe'

az network lb create --resource-group $RESOURCE_GROUP_NAME --name $LB_NAME --sku Standard --backend-pool-name $LB_BE_POOL_NAME --frontend-ip-name $LB_FE_IP_NAME --location $LOCATION --public-ip-address $PIP_NAME
```

1.  Cloud Shell 창에서 다음 명령을 실행하여 두 번째 부하 분산 장치의 아웃바운드 규칙을 만듭니다.

```
LB_RULE_OUTBOUND='az12001a-lb1-ruleoutbound'

az network lb outbound-rule create --resource-group $RESOURCE_GROUP_NAME --lb-name $LB_NAME --name $LB_RULE_OUTBOUND --frontend-ip-configs $LB_FE_IP_NAME --protocol All --idle-timeout 4 --outbound-ports 1000 --address-pool $LB_BE_POOL_NAME
```

1.  Cloud Shell 창을 닫습니다.

1.  Azure Portal에서 Azure Load Balancer **az12001a-lb1**의 속성이 표시되는 블레이드로 이동합니다.

1.  **az12001a-lb1** 블레이드에서 **백 엔드 풀**을 클릭합니다.

1.  **az12001a-lb1 - 백 엔드 풀** 블레이드에서 **az12001a-lb1-bepool**을 클릭합니다.

1.  **az12001a-lb1-bepool** 블레이드에서 다음 설정을 지정하고 **저장**을 클릭합니다.

    -   가상 네트워크: **az12001a-rg-vnet(VM 2개)**

    -   가상 머신: **az12001a-vm0**  IP 주소: **ipconfig1**

    -   가상 머신: **az12001a-vm1**  IP 주소: **ipconfig1**

### 작업 4: 점프 호스트 배포

   > **참고**: 2개의 클러스터된 Azure VM은 더 이상 인터넷에서 직접 액세스할 수 없으므로 점프 호스트 역할을 할 Windows Server 2019 Datacenter를 실행하는 Azure VM을 배포합니다. 

1.  랩 컴퓨터의 Azure Portal에서 **+ 리소스 만들기** 를 클릭합니다.

1.  **새로 만들기** 블레이드에서 **Windows Server 2019 Datacenter** 이미지에 따라 새 Azure VM 만들기를 시작합니다.

1.  다음 설정으로 Azure VM을 프로비전합니다.

    -   구독: *Azure 구독의 이름*

    -   리소스 그룹: **az12001a-RG**

    -   가상 머신 이름: **az12001a-vm2**

    -   지역: *이 랩의 첫 번째 연습에서 Azure VM을 배포한 것과 동일한 Azure 지역*

    -   가용성 옵션: **인프라 중복성 불필요**

    -   이미지: **Windows Server 2019 Datacenter**

    -   크기: **표준 DS1 V2**

    -   사용자 이름: **Student**

    -   암호: **Pa55w.rd1234**

    -   공용 인바운드 포트: **선택한 포트 허용**

    -   선택한 인바운드 포트: **RDP (3389)**

    -   이미 Windows 라이선스가 있습니까? **아니요**

    -   OS 디스크 유형: **표준 HDD**

    -   가상 네트워크: **az12001a-RG-vnet**

    -   서브넷 이름: **subnet-0 (192.168.0.0/24)**

    -   공용 IP 주소: **az12001a-vm2-ip***라는 이름의 새 IP 주소*

    -   NIC 네트워크 보안 그룹: **기본**

    -   공용 인바운드 포트: **선택한 포트 허용**

    -   인바운드 포트 선택: **RDP (3389)**

    -   가속화된 네트워킹: **꺼짐**

    -   기존 부하 분산 솔루션 뒤에 이 가상 머신을 배치: **아니요**

    -   무료로 기본 계획 사용: **아니요**

    -   부팅 진단: **꺼짐**

    -   OS 게스트 진단: **꺼짐**

    -   시스템 할당 관리 ID: **꺼짐**
	
    -   AAD 자격 증명으로 로그인(미리 보기): **해제**	

    -   자동 종료 사용 설정: **꺼짐**
	
    -   백업 사용: **해제**

    -   확장: *없음*

    -   태그: **없음**

1.  프로비전이 완료될 때까지 기다립니다. 이 작업은 몇 분 정도 걸립니다.

1.  RDP를 통해 새로 프로비전된 Azure VM에 연결합니다. 

1.  az12001a-vm2에 대한 RDP 세션 내에서 [**https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html**](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)의 PuTTY를 다운로드합니다.

1.  개인 IP 주소(각각 192.168.0.4 및 192.168.0.5)를 통해 az12001a-vm0 및 az12001a-vm1에 대한 SSH 세션을 설정할 수 있습니다.

> **결과**: 이 연습을 완료한 후 고가용성 SAP HANA 배포를 지원하는 데 필요한 Azure 네트워크 리소스가 프로비전되었습니다.


## 연습 4: 랩 리소스 제거

재생 시간: 10분

이 연습에서는 이 랩에서 프로비전한 리소스를 제거합니다.

#### 태스크 1: Cloud Shell 열기

1.  Portal 상단에서 **Cloud Shell** 아이콘을 클릭하여 Cloud Shell 창을 열로 셸로 Bash를 선택합니다.

1.  Portal 하단의 **Cloud Shell** 명령 프롬프트에 다음 명령을 입력하고 **Enter** 키를 눌러 이 랩에서 만든 모든 리소스 그룹의 목록을 표시합니다.

```
az group list --query "[?starts_with(name,'az12001a-')]".name --output tsv
```

1.  이 랩에서 만든 리소스 그룹만 출력에 포함되어 있는지 확인합니다. 다음 태스크에서 이러한 그룹을 삭제합니다.

#### 태스크 2: 리소스 그룹 삭제

1.  **Cloud Shell** 명령 프롬프트에 다음 명령을 입력하고 **Enter** 키를 눌러 이 랩에서 만든 리소스 그룹을 삭제합니다.

```
az group list --query "[?starts_with(name,'az12001a-')]".name --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
```

1.  Portal 하단의 **Cloud Shell** 프롬프트를 닫습니다.

    
> **결과**: 이 랩에서 사용한 리소스를 제거하여 이 연습을 완료해야 합니다.