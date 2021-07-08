# AZ 120 모듈 1: Azure의 SAP 기초
# 랩 1a: Azure VM에서 Linux 클러스터링 구현

예상 시간: 90분

이 랩의 모든 작업은 Azure Portal(Bash Cloud Shell 세션 포함)에서 수행됩니다.  

   > **참고**: Cloud Shell을 사용하지 않는 경우 랩 가상 머신에 Azure CLI를 설치([**https://docs.microsoft.com/ko-kr/cli/azure/install-azure-cli-windows?view=azure-cli-latest**](https://docs.microsoft.com/ko-kr/cli/azure/install-azure-cli-windows?view=azure-cli-latest))하고 [**https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html**](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)에서 제공되는 SSH 클라이언트(예: PuTTY)를 포함해야 합니다.

랩 파일: 없음

## 시나리오
  
Azure 기반 SAP HANA 배포를 준비하기 위해 Adatum Corporation에서는 Linux의 SUSE 배포를 실행하는 Azure VM에 클러스터링을 구현하는 프로세스를 살펴보려고 합니다.

## 목적
  
이 랩을 완료하면 다음과 같은 작업을 수행할 수 있습니다.

- 고가용성 SAP HANA 배포를 지원하는 데 필요한 Azure 컴퓨팅 리소스 프로비전

- 고가용성 SAP HANA 설치를 지원하도록 Linux를 실행하는 Azure VM의 운영 체제 구성

- 고가용성 SAP HANA 배포를 지원하는 데 필요한 Azure 네트워크 리소스 프로비전

## 요구 사항

- 사용 가능한 DSv3 vCPU(2 x 4) 및 DSv2(1 x 1) vCPU의 수가 충분한 Microsoft Azure 구독

- Azure Cloud Shell 호환 웹 브라우저를 사용하는 랩 컴퓨터 및 Azure 액세스

## 연습 1: 고가용성 SAP HANA 배포를 지원하는 데 필요한 Azure 컴퓨팅 리소스 프로비전

소요 시간: 30분

이 연습에서는 Linux 클러스터링을 구성하는 데 필요한 Azure 인프라 컴퓨팅 구성 요소를 배포합니다. 여기에는 동일한 가용성 집합에서 Linux SUSE를 실행하는 Azure VM 쌍을 만드는 작업이 포함됩니다.

### 작업 1: Linux SUSE를 실행하는 Azure VM 배포

1. 랩 컴퓨터에서 웹 브라우저를 시작하고 Azure Portal(https://portal.azure.com)로 이동합니다.

1. 메시지가 표시되면 이 랩에 사용할 Azure 구독에 대한 소유자 또는 기여자 역할이 있는 직장, 학교 또는 개인 Microsoft 계정으로 로그인합니다.

1. Azure Portal에서 Azure Portal 페이지 상단의 **리소스, 서비스 및 문서 검색** 텍스트 상자를 사용하여 **근접 배치 그룹** 블레이드를 검색하고 탐색한 후 **근접 배치 그룹** 블레이드에서 **+ 추가**를 선택합니다.

1. **근접 배치 그룹** 블레이드의 **기본** 탭에서 다음 설정을 지정하고 **검토 + 만들기**:

   - 구독: *Azure 구독의 이름*

   - 리소스 그룹: 리소스 그룹: *새 리소스 그룹* **az12001a-RG**의 이름

   > **참고**: 리소스 배포에는 **East US** 또는 **East US2** 지역 사용을 고려합니다. 

   - 지역: *Azure VM을 배포할 수 있는 Azure 지역*

   - 근접 배치 그룹 이름: **az12001a-ppg**

1. **근접 배치 그룹 만들기** 블레이드의 **검토 + 만들기** 탭에서 **만들기**를 선택합니다.

   > **참고**: 프로비전이 완료될 때까지 기다립니다. 이 작업에는 1분 미만이 소요됩니다.

1. Azure Portal에서 Azure Portal 페이지 상단의 **리소스, 서비스 및 문서 검색** 텍스트 상자를 사용하여 **가상 머신** 블레이드를 검색하고 탐색한 후 **가상 머신** 블레이드에서 **+ 추가**를 선택하고 드롭다운 메뉴에서 **가상 머신**을 선택합니다.

1. **가상 머신 만들기** 블레이드의 **기본** 탭에서 다음 설정을 지정하고 **다음: 디스크 >**(다른 모든 설정은 기본값 유지)를 선택합니다.

   - 구독: *Azure 구독의 이름*

   - 리소스 그룹: *이 작업 앞부분에서 사용한 리소스 그룹의 이름*

   - 가상 머신 이름: **az12001a-vm0**

   - 지역: *근접 배치 그룹을 만들 때 선택한 동일한 Azure 지역*

   - 가용성 옵션: **가용성 집합**

   - 가용성 집합: *이름이* **az12001a-avset** *이고 2개의 장애 도메인과 5개의 업데이트 도메인이 있는 새 가용성 집합*

   - 이미지: **SAP 12 SP5 - BYOS용 SUSE Enterprise Linux**
   
   > **참고**: 이미지를 찾으려면 **이미지 선택** 블레이드에서 **모든 이미지 보기** 링크를 클릭하고 검색 텍스트 상자에 **SAP 12 BYOS용 SUSE Enterprise Linux**를 입력한 다음 결과 목록에서 **SAP 12 SP5 - BYOS용 SUSE Enterprise Linux**를 클릭합니다.

   - Azure Spot 인스턴스: **아니요**

   - 크기: **표준 D4s v3**

   - 인증 유형: **암호**

   - 사용자 이름: **student**

   - 암호: **Pa55w.rd1234**

1. **가상 머신 만들기** 블레이드의 **디스크** 탭에서 다음 설정을 지정하고 **다음: 네트워킹 >** (다른 모든 설정은 기본값 유지)을 선택합니다.

   - OS 디스크 유형: **프리미엄 SSD**

   - 암호화 유형: **(기본값) 플랫폼 관리 키가 있는 미사용 암호화**

1. **가상 머신 만들기** 블레이드의 **네트워킹** 탭에서 다음 설정을 지정하고 **다음: 관리 >** (다른 모든 설정은 기본값 유지)를 선택합니다.

   - 가상 네트워크: **az12001a-RG-vnet** *라는 이름의 새 가상 네트워크*

   - 주소 공간: **192.168.0.0/20**

   - 서브넷 이름: **subnet-0**

   - 서브넷 주소 범위: **192.168.0.0/24**

   - 공용 IP 주소: **az12001a-vm0-ip** *라는 이름의 새 IP 주소*

   - NIC 네트워크 보안 그룹: **고급**

   > **참고**: 이 이미지는 NSG 규칙을 미리 구성했습니다.

   - 가속화된 네트워킹: **켜기**

   - 기존 부하 분산 솔루션 뒤에 이 가상 머신을 배치: **아니요**

1. **가상 머신 만들기** 블레이드의 **관리** 탭에서 다음 설정을 지정하고 **다음: 고급 >** (다른 모든 설정은 기본값 유지)을 선택합니다.

   - 무료로 기본 계획 사용: **아니요**

   > **참고**: Azure Security Center 계획을 이미 선택한 경우 이 설정을 사용할 수 없습니다.

   - 부팅 진단: **관리되는 스토리지 계정으로 사용 가능(권장)**

   - OS 게스트 진단: **꺼짐**

   - 시스템 할당 관리 ID: **꺼짐**

   - 자동 종료 사용: **꺼짐**

1. **가상 머신 만들기** 블레이드의 **고급** 탭에서 다음 설정을 지정하고 **검토 + 만들기** (다른 모든 설정은 기본값 유지)를 선택합니다.

   - 근접 배치 그룹: **az12001a-ppg**

1. **근접 배치 그룹 만들기** 블레이드의 **검토 + 만들기** 탭에서 **만들기**를 선택합니다.

   > **참고**: 프로비전이 완료될 때까지 기다립니다. 이 작업은 약 3분 정도 소요됩니다.

1. Azure Portal에서 Azure Portal 페이지 상단의 **리소스, 서비스 및 문서 검색** 텍스트 상자를 사용하여 **가상 머신** 블레이드를 검색하고 탐색한 후 **가상 머신** 블레이드에서 **+ 추가**를 선택하고 드롭다운 메뉴에서 **가상 머신**을 선택합니다.

1. **가상 머신 만들기** 블레이드의 **기본** 탭에서 다음 설정을 지정하고 **다음: 디스크 >** (다른 모든 설정은 기본값 유지)를 선택합니다.

   - 구독: *Azure 구독의 이름*

   - 리소스 그룹: *이 작업 앞부분에서 사용한 리소스 그룹의 이름*

   - 가상 머신 이름: **az12001a-vm1**

   - 지역: *첫 번째 Azure VM을 만들 때 선택한 동일한 Azure 지역*

   - 가용성 옵션: **가용성 집합**

   - 가용성 집합: **az12001a-avset**

   - 이미지: **SAP 12 SP5 - BYOS용 SUSE Enterprise Linux**
   
   > **참고**: 이미지를 찾으려면 **이미지 선택** 블레이드에서 **모든 이미지 보기** 링크를 클릭하고 검색 텍스트 상자에 **SAP 12 BYOS용 SUSE Enterprise Linux**를 입력한 다음 결과 목록에서 **SAP 12 SP5 - BYOS용 SUSE Enterprise Linux**를 클릭합니다.

   - Azure Spot 인스턴스: **아니요**

   - 크기: **표준 D4s v3**

   - 인증 유형: **암호**

   - 사용자 이름: **student**

   - 암호: **Pa55w.rd1234**

1. **가상 머신 만들기** 블레이드의 **디스크** 탭에서 다음 설정을 지정하고 **다음: 네트워킹 >** (다른 모든 설정은 기본값 유지)을 선택합니다.

   - OS 디스크 유형: **프리미엄 SSD**

   - 암호화 유형: **(기본값) 플랫폼 관리 키가 있는 미사용 암호화**

1. **가상 머신 만들기** 블레이드의 **네트워킹** 탭에서 다음 설정을 지정하고 **다음: 관리 >** (다른 모든 설정은 기본값 유지)를 선택합니다.

   - 가상 네트워크: **az12001a-RG-vnet**

   - 서브넷: **subnet-0 (192.168.0.0/24)**

   - 공용 IP 주소: **az12001a-vm1-ip** *라는 이름의 새 IP 주소*

   - NIC 네트워크 보안 그룹: **고급**

   > **참고**: 이 이미지는 NSG 규칙을 미리 구성했습니다.

   - 가속화된 네트워킹: **켜기**

   - 기존 부하 분산 솔루션 뒤에 이 가상 머신을 배치: **아니요**

1. **가상 머신 만들기** 블레이드의 **관리** 탭에서 다음 설정을 지정하고 **다음: 고급 >** (다른 모든 설정은 기본값 유지)을 선택합니다.

   - 무료로 기본 계획 사용: **아니요**

   > **참고**: Azure Security Center 계획을 이미 선택한 경우 이 설정을 사용할 수 없습니다.

   - 부팅 진단: **관리되는 스토리지 계정으로 사용 가능(권장)**

   - OS 게스트 진단: **꺼짐**

   - 시스템 할당 관리 ID: **꺼짐**

   - 자동 종료 사용: **꺼짐**

1. **가상 머신 만들기** 블레이드의 **고급** 탭에서 다음 설정을 지정하고 **검토 + 만들기** (다른 모든 설정은 기본값 유지)를 선택합니다.

   - 근접 배치 그룹: **az12001a-ppg**

1. **근접 배치 그룹 만들기** 블레이드의 **검토 + 만들기** 탭에서 **만들기**를 선택합니다.

   > **참고**: 프로비전이 완료될 때까지 기다립니다. 이 작업은 약 3분 정도 소요됩니다.


### 작업 2: Azure VM 디스크 만들기 및 구성

1. Azure Portal에서 Cloud Shell의 Bash 세션을 시작합니다. 

   > **참고**: 현재 Azure 구독에서 Cloud Shell을 처음 시작하는 경우 Cloud Shell 파일을 보관할 Azure 파일 공유를 만들라는 메시지가 표시됩니다. 이 경우 기본값을 적용하면 자동으로 생성된 리소스 그룹에 스토리지 계정이 만들어집니다.

1. Cloud Shell 창에서 다음 명령을 실행하여 `RESOURCE_GROUP_NAME` 변수의 값을 이전 작업에서 프로비전한 리소스를 포함하는 리소스 그룹의 이름으로 설정합니다.

   ```cli
   RESOURCE_GROUP_NAME='az12001a-RG'
   ```

1. Cloud Shell 창에서 다음 명령을 실행하여 8개의 관리 디스크로 구성된 첫 번째 세트를 만듭니다. 이 세트는 이전 작업에서 배포한 첫 번째 Azure VM에 연결됩니다.

   ```cli
   LOCATION=$(az group list --query "[?name == '$RESOURCE_GROUP_NAME'].location" --output tsv)

   for I in {0..7}; do az disk create --resource-group $RESOURCE_GROUP_NAME --name az12001a-vm0-DataDisk$I --size-gb 128 --location $LOCATION --sku Premium_LRS; done
   ```

1. Cloud Shell 창에서 다음 명령을 실행하여 8개의 관리 디스크로 구성된 두 번째 세트를 만듭니다. 이 세트는 이전 작업에서 배포한 두 번째 Azure VM에 연결됩니다.

   ```cli
   for I in {0..7}; do az disk create --resource-group $RESOURCE_GROUP_NAME --name az12001a-vm1-DataDisk$I --size-gb 128 --location $LOCATION --sku Premium_LRS; done
   ```

1. Azure Portal에서 이전 작업에서 프로비전한 첫 번째 Azure VM(**az12001a-vm0**)의 블레이드로 이동합니다.

1. **az12001a-vm0** 블레이드에서 **az12001a-vm0 \|** 블레이드로 이동합니다. **디스크** 블레이드.

1. **az12001a-vm0 \|** 에서**디스크** 블레이드, **기존 디스크 연결**을 선택하고 다음 설정으로 데이터 디스크를 az12001a vm0에 연결합니다.

   - LUN: **0**

   - 디스크 이름: **az12001a-vm0-DataDisk0**

   - 리소스 그룹: *이 작업 앞부분에서 사용한 리소스 그룹의 이름*

   - 호스트 캐싱: **읽기 전용**

1. 이전 단계를 반복하여 나머지 7개의 디스크를 접두사 **az12001a-vm0-DataDisk**를 사용하여 연결합니다(총 8개). 디스크 이름의 마지막 문자와 일치하는 LUN 번호를 할당합니다. LUN **1**이 있는 디스크의 호스트 캐싱을 **읽기 전용**으로 설정하고 나머지 모든 디스크의 호스트 캐싱을 **없음**으로 설정합니다.

1. 변경 내용을 저장합니다. 

1. Azure Portal에서 이전 작업에서 프로비전한 두 번째 Azure VM(**az12001a-vm1**)의 블레이드로 이동합니다.

1. **az12001a-vm1** 블레이드에서 **az12001a-vm1 \|** 블레이드로 이동합니다. **디스크** 블레이드.

1. **az12001a-vm1 \|** 에서 **디스크** 블레이드에서 다음 설정으로 데이터 디스크를 az12001a-vm1에 연결합니다.

   - LUN: **0**

   - 디스크 이름: **az12001a-vm1-DataDisk0**

   - 리소스 그룹: *이 작업 앞부분에서 사용한 리소스 그룹의 이름*

   - 호스트 캐싱: **읽기 전용**

1. 이전 단계를 반복하여 나머지 7개의 디스크를 접두사 **az12001a-vm1-DataDisk**를 사용하여 연결합니다(총 8개). 디스크 이름의 마지막 문자와 일치하는 LUN 번호를 할당합니다. LUN **1**이 있는 디스크의 호스트 캐싱을 **읽기 전용**으로 설정하고 나머지 모든 디스크의 호스트 캐싱을 **없음**으로 설정합니다.

1. 변경 내용을 저장합니다. 

> **결과**: 이 연습을 완료한 후 고가용성 SAP HANA 배포를 지원하는 데 필요한 Azure 컴퓨팅 리소스가 프로비전되었습니다.


## 연습 2: 고가용성 SAP HANA 설치를 지원하도록 Linux를 실행하는 Azure VM의 운영 체제 구성

소요 시간: 30분

이 연습에서는 SAP HANA의 클러스터된 설치를 수용하도록 SUSE Linux Enterprise Server를 실행하는 Azure VM의 운영 체제 및 스토리지를 구성합니다.

### 작업 1: Azure Linux VM에 연결

1. Azure Portal에서 Cloud Shell의 Bash 세션을 시작합니다. 

1. Cloud Shell 창에서 다음 명령을 실행하여 `RESOURCE_GROUP_NAME` 변수의 값을 이전 연습에서 프로비전한 리소스를 포함하는 리소스 그룹의 이름으로 설정합니다.

   ```cli
   RESOURCE_GROUP_NAME='az12001a-RG'
   ```

1. Cloud Shell 창에서 다음 명령을 실행하여 이전 연습에서 배포한 첫 번째 Azure VM의 공용 IP 주소를 식별합니다.

   ```cli
   PIP=$(az network public-ip show --resource-group $RESOURCE_GROUP_NAME --name az12001a-vm0-ip --query ipAddress --output tsv)
   ```

1. Cloud Shell 창에서 다음 명령을 실행하여 이전 단계에서 식별한 IP 주소에 대한 SSH 세션을 설정합니다.

   ```cli
   ssh student@$PIP
   ```

1. 연결을 계속할지 묻는 메시지가 표시되면 `yes`를 입력하고 **Enter** 키를 누릅니다. 

1. 암호를 입력하라는 메시지가 표시되면 `Pa55w.rd1234`를 입력하고 **Enter** 키를 누릅니다. 

1. Cloud Shell 도구 모음에서 **새 세션 열기** 아이콘을 클릭하여 다른 Cloud Shell Bash 세션을 엽니다.

1. 새로 열린 Cloud Shell Bash 세션에서 이 작업의 모든 단계를 반복하여 **az12001a-vm0-ip** IP 주소를 통해 **az12001a-vm1** Azure VM에 연결합니다.


### 작업 2: Linux를 실행하는 Azure VM의 스토리지 구성

1. Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음 명령을 실행하여 권한을 상승시킵니다. 

   ```cli
   sudo su -
   ```

1. Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음 명령을 실행하여 새로 연결된 디바이스와 해당 LUN 번호 간의 매핑을 식별합니다.
   
   ```cli
   lsscsi
   ```

1. Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 6개(8개 중) 데이터 디스크에 대한 물리적 볼륨을 만듭니다.
   
   ```cli
   pvcreate /dev/sdc
   pvcreate /dev/sdd
   pvcreate /dev/sde
   pvcreate /dev/sdf
   pvcreate /dev/sdg
   pvcreate /dev/sdh
   ```

1. Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 볼륨 그룹을 만듭니다.
   
   ```cli
   vgcreate vg_hana_data /dev/sdc /dev/sdd
   vgcreate vg_hana_log /dev/sde /dev/sdf
   vgcreate vg_hana_backup /dev/sdg /dev/sdh
   ```

1. Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 논리적 볼륨을 만듭니다.

   ```cli
   lvcreate -l 100%FREE -n hana_data vg_hana_data
   lvcreate -l 100%FREE -n hana_log vg_hana_log
   lvcreate -l 100%FREE -n hana_backup vg_hana_backup
   ```

   > **참고**: 각 볼륨 그룹별로 단일 논리 볼륨을 만들 것입니다.

1. Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 논리적 볼륨을 포맷합니다.

   ```cli
   mkfs.xfs /dev/vg_hana_data/hana_data -m crc=1
   mkfs.xfs /dev/vg_hana_log/hana_log -m crc=1
   mkfs.xfs /dev/vg_hana_backup/hana_backup -m crc=1
   ```

   > **참고**: SUSE Linux Enterprise Server 12부터 XFS 메타데이터의 자동 체크섬, 파일 유형 지원 및 파일당 액세스 제어 목록 수에 대한 강화된 제한을 제공하는 XFS 파일 시스템의 새로운 디스크 형식(v5)을 사용할 수 있습니다. YaST를 사용하여 XFS 파일 시스템을 만드는 경우 새 형식이 자동으로 적용됩니다. 호환성을 이유로 이전 형식으로 XFS 파일 시스템을 만들려면 `-m crc=1` 옵션 없이 mkfs.xfs 명령을 사용합니다. 

1. Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 **/dev/sdi** 디스크의 파티션을 나눕니다.

   ```cli
   fdisk /dev/sdi
   ```

1. 메시지가 표시되면 `n`, `p`, `1`을 순서대로 입력(입력 후 매번 **Enter** 키를 누름)한 다음 **Enter** 키를 두 번 누르고 `w`를 입력하여 쓰기를 완료합니다.

1. Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 **/dev/sdj** 디스크의 파티션을 나눕니다.

   ```cli
   fdisk /dev/sdj
   ```

1. 메시지가 표시되면 `n`, `p`, `1`을 순서대로 입력(입력 후 매번 **Enter** 키를 누름)한 다음 **Enter** 키를 두 번 누르고 `w`를 입력하여 쓰기를 완료합니다.

1. Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 새로 생성된 파티션을 포맷합니다(확인 메시지가 표시될 때 `y`를 입력하고 **Enter** 키를 누릅니다).

   ```cli
   mkfs.xfs /dev/sdi -m crc=1 -f
   mkfs.xfs /dev/sdj -m crc=1 -f
   ```

1. Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 탑재 지점으로 사용할 디렉터리를 만듭니다.

   ```cli
   mkdir -p /hana/data
   mkdir -p /hana/log
   mkdir -p /hana/backup
   mkdir -p /hana/shared
   mkdir -p /usr/sap
   ```

1. Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 논리적 볼륨의 ID를 표시합니다.

   ```cli
   blkid
   ```

   > **참고**: **/dev/sdi** (**/hana/shared**에 대해 사용됨) 및 **dev/sdj** (**/usr/sap**에 사용됨)를 포함하여 새로 만들어진 볼륨 그룹 및 파티션과 연결된 **UUID** 값을 식별합니다.


1. Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 **/etc/fstab**을 vi 편집기에서 엽니다(자유롭게 다른 편집기를 사용할 수 있음).

   ```cli
   vi /etc/fstab
   ```

1. 편집기에서 **/etc/fstab**에 다음 항목을 추가합니다(여기서 `\<UUID of /dev/vg\_hana\_data-hana\_data\>`, `\<UUID of /dev/vg\_hana\_log-hana\_log\>`, `\<UUID of /dev/vg\_hana\_backup-hana\_backup\>`, `\<UUID of /dev/vg_hana_shared-hana_shared (/dev/sdi)\>`, 및 `\<UUID of /dev/vg_usr_sap-usr_sap (/dev/sdj)\>`는 이전 단계에서 식별한 ID를 나타냅니다).

   ```cli
   /dev/disk/by-uuid/<UUID of /dev/vg_hana_data-hana_data> /hana/data xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<UUID of /dev/vg_hana_log-hana_log> /hana/log xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<UUID of /dev/vg_hana_backup-hana_backup> /hana/backup xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<UUID of /dev/vg_hana_shared-hana_shared (/dev/sdi)> /hana/shared xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<UUID of /dev/vg_usr_sap-usr_sap (/dev/sdj)> /usr/sap xfs  defaults,nofail  0  2
   ```

1. 변경 내용을 저장하고 편집기를 닫습니다.

1. Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 새 볼륨을 탑재합니다.

   ```cli
   mount -a
   ```

1. Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 탑재에 성공했는지 확인합니다.

   ```cli
   df -h
   ```

1. Cloud Shell Bash 세션을 az12001a-vm1으로 전환하고 이 작업의 모든 단계를 반복하여 **az12001a-vm1**에서 스토리지를 구성합니다.


### 작업 3: 노드 간 암호 없는 SSH 액세스 사용

1. Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 암호 없는 SSH 키를 생성합니다.

   ```cli
   ssh-keygen -tdsa
   ```

1. 메시지가 표시되면 **Enter** 키를 세 번 누르고 다음을 실행하여 공개 키를 표시합니다. 

   ```cli
   cat /root/.ssh/id_dsa.pub
   ```

1. 키 값을 클립보드에 복사합니다.

1. **az12001a-vm1**에 대한 SSH 세션이 포함된 Cloud Shell 창으로 전환하고 다음을 실행하여 **/root/.ssh/** 디렉터리를 만듭니다.

   ```cli
   mkdir /root/.ssh
   ```

1. Cloud Shell 창의 az12001a-vm1에 대한 SSH 세션에서 다음을 실행하고 vi 편집기에서 **/root/.ssh/authorized\_keys** 파일을 만듭니다(자유롭게 다른 편집기를 사용할 수 있음).

   ```cli
   vi /root/.ssh/authorized_keys
   ```

1. 편집기 창에서 az12001a-vm0에서 생성한 키를 붙여 넣습니다.

1. 변경 내용을 저장하고 편집기를 닫습니다.

1. Cloud Shell 창의 az12001a-vm1에 대한 SSH 세션에서 다음을 실행하여 암호 없는 SSH 키를 생성합니다.

   ```cli
   ssh-keygen -tdsa
   ```

1. 메시지가 표시되면 **Enter** 키를 세 번 누르고 다음을 실행하여 공개 키를 표시합니다. 

   ```cli
   cat /root/.ssh/id_dsa.pub
   ```

1. 키 값을 클립보드에 복사합니다.

1. az12001a-vm0에 대한 SSH 세션이 포함된 Cloud Shell 창으로 전환하고 다음을 실행하여 vi 편집기에서 **/root/.ssh/authorized\_keys** 파일을 만듭니다(자유롭게 다른 편집기를 사용할 수 있음).

   ```cli
   vi /root/.ssh/authorized_keys
   ```

1. 편집기 창에서 az12001a-vm1에서 생성한 키를 붙여 넣습니다.

1. 변경 내용을 저장하고 편집기를 닫습니다.

1. Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 암호 없는 SSH 키를 생성합니다.

   ```cli
   ssh-keygen -t rsa
   ```

1. 메시지가 표시되면 **Enter** 키를 세 번 누르고 다음을 실행하여 공개 키를 표시합니다. 

   ```cli
   cat /root/.ssh/id_rsa.pub
   ```

1. 키 값을 클립보드에 복사합니다.

1. **az12001a-vm1**에 대한 SSH 세션이 포함된 Cloud Shell 창으로 전환하고 다음을 실행하여 vi 편집기에서 **/root/.ssh/authorized\_keys** 파일을 엽니다(자유롭게 다른 편집기를 사용할 수 있음).

   ```cli
   vi /root/.ssh/authorized_keys
   ```

1. 편집기 창에서 새 줄을 시작하여 az12001a-vm0에서 생성한 키를 붙여넣습니다.

1. 변경 내용을 저장하고 편집기를 닫습니다.

1. Cloud Shell 창의 az12001a-vm1에 대한 SSH 세션에서 다음을 실행하여 암호 없는 SSH 키를 생성합니다.

   ```cli
   ssh-keygen -t rsa
   ```

1. 메시지가 표시되면 **Enter** 키를 세 번 누르고 다음을 실행하여 공개 키를 표시합니다. 

   ```cli
   cat /root/.ssh/id_rsa.pub
   ```

1. 키 값을 클립보드에 복사합니다.

1. az12001a-vm0에 대한 SSH 세션이 포함된 Cloud Shell 창으로 전환하고 다음을 실행하여 vi 편집기에서 **/root/.ssh/authorized\_keys** 파일을 엽니다(자유롭게 다른 편집기를 사용할 수 있음).

   ```cli
   vi /root/.ssh/authorized_keys
   ```

1. 편집기 창에서 새 줄을 시작하여 az12001a-vm1에서 생성한 키를 붙여넣습니다.

1. 변경 내용을 저장하고 편집기를 닫습니다.

1. Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 vi 편집기에서 **/etc/ssh/sshd\_config** 파일을 엽니다(자유롭게 다른 편집기를 사용할 수 있음).

   ```cli
   vi /etc/ssh/sshd_config
   ```

1. **/etc/ssh/sshd\_config** 파일에서 **PermitRootLogin** 및 **AuthorizedKeysFile** 항목을 찾고 다음과 같이 구성합니다(필요한 경우 선행 문자 **#** 제거).

   ```cli
   PermitRootLogin yes
   AuthorizedKeysFile  /root/.ssh/authorized_keys
   ```

1. 변경 내용을 저장하고 편집기를 닫습니다.

1. Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 sshd 디먼을 다시 시작합니다.

   ```cli
   systemctl restart sshd
   ```

1. az12001a-vm1에서 이전 네 단계를 반복합니다.

1. 구성이 정상적으로 적용되었는지 확인하려면 Cloud Shell 창의 az12001a-vm0에 대한 SSH 세션에서 다음을 실행하여 **루트**로서 SSH 세션을 az12001a-vm0에서 az12001a-vm1으로 설정합니다. 

   ```cli
   ssh root@az12001a-vm1
   ```

1. 연결을 계속할지 묻는 메시지가 표시되면 `yes`를 입력하고 **Enter** 키를 누릅니다. 

1. 암호를 입력하라는 메시지가 표시되지 않는지 확인합니다.

1. 다음 명령을 실행하여 az12001a-vm0에서 az12001a-vm1로의 SSH 세션을 닫습니다. 

   ```cli
   exit
   ```

1. 다음 명령을 두 번 실행하여 az12001a-vm0에서 로그아웃합니다.

   ```cli
   exit
   ```

1. 구성이 정상적으로 적용되었는지 확인하려면 Cloud Shell 창의 az12001a-vm1에 대한 SSH 세션에서 다음을 실행하여 **루트**로서 SSH 세션을 az12001a-vm1에서 az12001a-vm0로 설정합니다. 

   ```cli
   ssh root@az12001a-vm0
   ```

1. 연결을 계속할지 묻는 메시지가 표시되면 `yes`를 입력하고 **Enter** 키를 누릅니다. 

1. 암호를 입력하라는 메시지가 표시되지 않는지 확인합니다.

1. 다음 명령을 실행하여 az12001a-vm1에서 az12001a-vm0로의 SSH 세션을 닫습니다. 

   ```cli
   exit
   ```

1. 다음 명령을 두 번 실행하여 az12001a-vm1에서 로그아웃합니다.

   ```cli
   exit
   ```

> **결과**: 이 연습을 완료한 후 고가용성 SAP HANA 설치를 지원하도록 Linux를 실행하는 Azure VM의 운영 체제가 구성되었습니다.


## 연습 3: 고가용성 SAP HANA 배포를 지원하는 데 필요한 Azure 네트워크 리소스 프로비전

소요 시간: 30분

이 연습에서는 SAP HANA의 클러스터된 설치를 수용하도록 Azure Load Balancer를 구현합니다.


### 작업 1: 부하 분산 설정을 지원하도록 Azure VM을 구성합니다.

   > **참고**: 표준 SKU의 Azure Load Balancer 한 쌍을 설정할 것이므로 먼저 부하 분산된 백 엔드 풀로 사용할 두 Azure VM의 네트워크 어댑터에 연결된 공용 IP 주소를 제거해야 합니다.

1. Azure Portal에서 **az12001a-vm0** Azure VM의 블레이드로 이동합니다.

1. **az12001a-vm0** 블레이드에서 **az12001a-vm0** 블레이드로 이동합니다. **| 네트워킹** 블레이드 및 **az12001a-vm0** 에서 **| 네트워킹** 블레이드에서 네트워크 어댑터에 연결된 **az12001a-vm0-ip** 공용 IP 주소를 나타내는 항목을 선택합니다.

1. **az12001a-vm0-ip** 블레이드에서 **연결 해제**를 선택하여 네트워크 인터페이스에서 공용 IP 주소 연결을 끊은 다음, **삭제**를 선택하여 이를 삭제합니다.

1. Azure portal에서 **az12001a-vm1** Azure VM의 블레이드로 이동합니다.

1. **az12001a-vm1** 블레이드에서 **az12001a-vm1** 블레이드로 이동합니다. **| 네트워킹** 블레이드 및 **az12001a-vm1** 에서 **| 네트워킹** 블레이드에서 네트워크 어댑터에 연결된 **az12001a-vm1-ip** 공용 IP 주소를 나타내는 항목을 선택합니다.

1. **az12001a-vm1-ip** 블레이드에서 **연결 해제**를 선택하여 네트워크 인터페이스에서 공용 IP 주소 연결을 끊은 다음, **삭제**를 선택하여 이를 삭제합니다.

1. Azure Portal에서 **az12001a-vm0** Azure VM의 블레이드로 이동합니다.

1. **az12001a-vm0** 블레이드에서 **az12001a-vm0** 블레이드로 이동합니다. **| 네트워킹** 블레이드. 

1. **az12001a-vm0** 에서 **| 네트워킹** 블레이드에서 az12001a-vm0의 네트워크 인터페이스를 나타내는 항목을 선택합니다. 

1. az12001a-vm0의 네트워크 인터페이스 블레이드에서 IP 구성 블레이드로 이동하여 **ipconfig1** 블레이드를 표시합니다.

1. **ipconfig1** 블레이드에서 개인 IP 주소 할당을 **고정**으로 설정하고 변경 내용을 저장합니다.

1. Azure portal에서 **az12001a-vm1** Azure VM의 블레이드로 이동합니다.

1. **az12001a-vm1** 블레이드에서 **az12001a-vm1** 블레이드로 이동합니다. **| 네트워킹** 블레이드. 

1. **az12001a-vm1** 에서 **| 네트워킹** 블레이드에서 az12001a-vm1의 네트워크 인터페이스로 이동합니다. 

1. az12001a-vm1의 네트워크 인터페이스 블레이드에서 IP 구성 블레이드로 이동하여 **ipconfig1** 블레이드를 표시합니다.

1. **ipconfig1** 블레이드에서 개인 IP 주소 할당을 **고정**으로 설정하고 변경 내용을 저장합니다.


### 작업 2: 인바운드 트래픽을 처리하는 Azure Load Balancer 만들기 및 구성

1. Azure Portal에서 Azure Portal 페이지 상단의 **검색 리소스, 서비스 및 문서** 텍스트 상자를 사용하여 **Load Balancer** 블레이드를 검색하여 이동하고 **Load Balancer** 블레이드에서 **+ 추가**를 선택합니다.

1. **부하 분산 장치 만들기** 블레이드의 **기본** 탭에서 다음 설정을 지정하고 **검토 + 만들기**를 선택합니다(기타 설정은 기본값을 유지함).

   - 구독: *Azure 구독의 이름*

   - 리소스 그룹: *이 랩 앞부분에서 사용한 리소스 그룹의 이름*

   - 이름: **az12001a-lb0**

   - 지역: *이 랩의 첫 번째 연습에서 Azure VM을 배포한 것과 동일한 Azure 지역*

   - 유형: **내부**

   - SKU: **표준**

   - 가상 네트워크: **az12001a-RG-vnet**

   - 서브넷: **subnet-0**

   - IP 주소 할당: **정적**

   - IP 주소: **192.168.0.240**

   - 가용성 영역: **영역 중복**

1. **검토 + 만들기** 블레이드에서 **만들기**를 선택합니다.

   > **참고**: 부하 분산 장치가 프로비전될 때까지 기다립니다. 1분 미만이 소요됩니다. 

1. Azure Portal에서 새로 프로비전된 **az12001a-lb0** 부하 분산 장치의 속성이 표시되는 블레이드로 이동합니다. 

1. **az12001a-lb0** 블레이드에서 **백 엔드 풀** 및 **+ 추가**를 선택하고 **백 엔드 풀 추가**에서 다음 설정을 지정합니다(기타 설정은 기본값을 유지함).

   - 이름: **az12001a-lb0-bepool**

   - IP 버전: **IPv4**

   - 가상 머신: **az12001a-vm0** IP 구성: **ipconfig1 (192.168.0.4)**

   - 가상 머신: **az12001a-vm1** IP 구성: **ipconfig1 (192.168.0.5)**

1. **az12001a-lb0** 블레이드에서 **상태 프로브** 및 **+ 추가**를 선택하고 **상태 프로브 추가** 블레이드에서 다음 설정을 지정합니다(기타 설정은 기본값을 유지함).

   - 이름: **az12001a-lb0-hprobe**

   - 프로토콜: **TCP**

   - 포트: **62500**

   - 간격: **5** *초*

   - 비정상 임계값: **2** *회 연속 실패*

1. **az12001a-lb0** 블레이드에서 **부하 분산 규칙** 및 **+ 추가**를 선택하고, **부하 분산 규칙 추가** 블레이드에서 다음 설정을 지정합니다(기타 설정은 기본값을 유지함).

   - 이름: **az12001a-lb0-lbruleAll**

   - IP 버전: **IPv4**

   - 프론트 엔드 IP 주소: **192.168.0.240(LoadBalancerFrontEnd)**

   - HA 포트: **사용**

   - 백 엔드 풀: **az12001a-lb0-bepool(가상 머신 2개)**

   - 상태 프로브:**az12001a-lb0-hprobe(TCP:62504)**

   - 세션 지속성: **없음**

   - 유휴 시간 제한(분): **4**

   - TCP 재설정: **사용 안 함**

   - 부동 IP(Direct Server Return): **사용**

### 작업 3: 아웃바운드 트래픽을 처리하는 Azure Load Balancer 만들기 및 구성

1. Azure Portal에서 Cloud Shell의 Bash 세션을 시작합니다. 

1. Cloud Shell 창에서 다음 명령을 실행하여 `RESOURCE_GROUP_NAME` 변수의 값을 이 랩의 첫 번째 연습에서 프로비전한 리소스를 포함하는 리소스 그룹의 이름으로 설정합니다.

   ```cli
   RESOURCE_GROUP_NAME='az12001a-RG'
   ```

1. Cloud Shell 창에서 다음 명령을 실행하여 두 번째 부하 분산 장치에 사용할 공용 IP 주소를 만듭니다.

   ```cli
   LOCATION=$(az group list --query "[?name == '$RESOURCE_GROUP_NAME'].location" --output tsv)

   PIP_NAME='az12001a-lb1-pip'

   az network public-ip create --resource-group $RESOURCE_GROUP_NAME --name $PIP_NAME --sku Standard --location $LOCATION
   ```

1. Cloud Shell 창에서 다음 명령을 실행하여 두 번째 부하 분산 장치를 만듭니다.

   ```cli
   LB_NAME='az12001a-lb1'

   LB_BE_POOL_NAME='az12001a-lb1-bepool'

   LB_FE_IP_NAME='az12001a-lb1-fe'

   az network lb create --resource-group $RESOURCE_GROUP_NAME --name $LB_NAME --sku Standard --backend-pool-name $LB_BE_POOL_NAME --frontend-ip-name $LB_FE_IP_NAME --location $LOCATION --public-ip-address $PIP_NAME
   ```

1. Cloud Shell 창에서 다음 명령을 실행하여 두 번째 부하 분산 장치의 아웃바운드 규칙을 만듭니다.

   ```cli
   LB_RULE_OUTBOUND='az12001a-lb1-ruleoutbound'

   az network lb outbound-rule create --resource-group $RESOURCE_GROUP_NAME --lb-name $LB_NAME --name $LB_RULE_OUTBOUND --frontend-ip-configs $LB_FE_IP_NAME --protocol All --idle-timeout 4 --outbound-ports 1000 --address-pool $LB_BE_POOL_NAME
   ```

1. Cloud Shell 창을 닫습니다.

1. Azure Portal에서 새로 만든 **az12001a-lb** Azure Load Balancer의 속성이 표시되는 블레이드로 이동합니다.

1. **az12001a-lb1** 블레이드에서 **백 엔드 풀**을 클릭합니다.

1. **az12001a-lb1 \|** 에서 **백 엔드 풀** 블레이드에서 **az12001a-lb1-bepool**을 클릭합니다.

1. **az12001a-lb1-bepool** 블레이드에서 다음 설정을 지정하고 **저장**을 클릭합니다.

   - 가상 네트워크: **az12001a-rg-vnet(VM 2개)**

   - 가상 머신: **az12001a-vm0** IP 구성: **ipconfig1 (192.168.0.4)**

   - 가상 머신: **az12001a-vm1** IP 구성: **ipconfig1 (192.168.0.5)**

### 작업 4: 점프 호스트 배포

   > **참고**: 2개의 클러스터된 Azure VM은 더 이상 인터넷에서 직접 액세스할 수 없으므로 점프 호스트 역할을 할 Windows Server 2019 Datacenter를 실행하는 Azure VM을 배포합니다. 

1. 노트북의 Azure Portal에서 Azure Portal 페이지 상단의 **검색 리소스, 서비스 및 문서** 텍스트 상자를 사용하여 **가상 머신** 블레이드를 검색하여 이동한 다음, **가상 머신** 블레이드에서 **+ 추가**를 선택하고 드롭다운 메뉴에서 **가상 머신**을 선택합니다.

1. **가상 머신 만들기** 블레이드의 **기본** 탭에서 다음 설정을 지정하고 **다음: 디스크 >** (다른 모든 설정은 기본값 유지)를 선택합니다.

   - 구독: *Azure 구독의 이름*

   - 리소스 그룹: *이 랩 앞부분에서 사용한 리소스 그룹의 이름*

   - 가상 머신 이름: **az12001a-vm2**

   - 지역: *이 랩의 첫 번째 연습에서 Azure VM을 배포한 것과 동일한 Azure 지역*

   - 가용성 옵션: **인프라 중복성 불필요**

   - 이미지: **Windows Server 2019 Datacenter - Gen1**

   - 크기: **표준 DS1 v2** *또는 유사한 항목*

   - Azure Spot 인스턴스: **아니요**

   - 사용자 이름: **학생**

   - 암호: **Pa55w.rd1234**

   - 공용 인바운드 포트: **선택한 포트 허용**

   - 선택한 인바운드 포트: **RDP(3389)**

   - 기존 Windows Server 라이선스를 사용하시겠습니까? **아니요**

1. **가상 머신 만들기** 블레이드의 **디스크** 탭에서 다음 설정을 지정하고 **다음: 네트워킹 >**(다른 모든 설정은 기본값 유지)을 선택합니다.

   - OS 디스크 유형: **표준 HDD**

   - 암호화 유형: **(기본값) 플랫폼 관리 키가 있는 미사용 암호화**

1. **가상 머신 만들기** 블레이드의 **네트워킹** 탭에서 다음 설정을 지정하고 **다음: 관리 >**(다른 모든 설정은 기본값 유지)를 선택합니다.

   - 가상 네트워크: **az12001a-RG-vnet**

   - 서브넷 이름: **subnet-0 (192.168.0.0/24)**

   - 공용 IP 주소: **az12001a-vm2-ip** *라는 이름의 새 IP 주소*

   - NIC 네트워크 보안 그룹: **기본**

   - 공용 인바운드 포트: **선택한 포트 허용**

   - 인바운드 포트 선택: **RDP(3389)**

   - 가속화된 네트워킹: **꺼짐**

   - 기존 부하 분산 솔루션 뒤에 이 가상 머신을 배치: **아니요**

1. **가상 머신 만들기** 블레이드의 **관리** 탭에서 다음 설정을 지정하고 **다음: 고급 >** (다른 모든 설정은 기본값 유지)을 선택합니다.

   - 무료로 기본 계획 사용: **아니요**

   > **참고**: Azure Security Center 계획을 이미 선택한 경우 이 설정을 사용할 수 없습니다.

   - 부팅 진단: **관리되는 스토리지 계정으로 사용 가능(권장)**

   - OS 게스트 진단: **꺼짐**

   - 시스템 할당 관리 ID: **꺼짐**

   - 자동 종료 사용: **꺼짐**

   - 백업 활성화: **꺼짐**

   - 게스트 OS 업데이트: **수동 패치: 패치를 직접 또는 다른 패치 솔루션을 통해 설치합니다.**

1. **가상 머신 만들기** 블레이드의 **고급** 탭에서 **검토 + 만들기**를 선택합니다(기타 설정은 기본값을 유지함).

1. **근접 배치 그룹 만들기** 블레이드의 **검토 + 만들기** 탭에서 **만들기**를 선택합니다.

   > **참고**: 프로비전이 완료될 때까지 기다립니다. 이 작업은 약 3분 정도 소요됩니다.

1. RDP를 통해 새로 프로비전된 Azure VM에 연결합니다. 

1. az12001a-vm2에 대한 RDP 세션 내에서 [**https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html**](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)의 PuTTY를 다운로드합니다.

1. 개인 IP 주소(각각 192.168.0.4 및 192.168.0.5)를 통해 az12001a-vm0 및 az12001a-vm1에 대한 SSH 세션을 설정할 수 있습니다. 

> **결과**: 이 연습을 완료한 후 고가용성 SAP HANA 배포를 지원하는 데 필요한 Azure 네트워크 리소스가 프로비전되었습니다.


## 연습 4: 랩 리소스 제거

소요 시간: 10분

이 연습에서는 이 랩에서 프로비전한 리소스를 제거합니다.

#### 작업 1: Cloud Shell 열기

1. 포털 상단에서 **Cloud Shell** 아이콘을 클릭하여 Cloud Shell 창을 열고 셸로 Bash를 선택합니다.

1. Cloud Shell 창에서 다음 명령을 실행하여 `RESOURCE_GROUP_PREFIX` 변수의 값을 이 랩에서 프로비전한 리소스를 포함하는 리소스 그룹의 접두사로 설정합니다.

   ```cli
   RESOURCE_GROUP_PREFIX='az12001a-'
   ```

1. Cloud Shell 창에서 다음 명령을 실행하여 이 랩에서 만든 모든 리소스 그룹을 나열합니다.

   ```cli
   az group list --query "[?starts_with(name,'$RESOURCE_GROUP_PREFIX')]".name --output tsv
   ```

1. 이 랩에서 만든 리소스 그룹만 출력에 포함되어 있는지 확인합니다. 이 리소스 그룹과 모든 관련 리소스는 다음 작업에서 삭제됩니다.

#### 작업 2: 리소스 그룹 삭제

1. Cloud Shell 창에서 다음 명령을 실행하여 리소스 그룹 및 해당 리소스를 삭제합니다.

   ```cli
   az group list --query "[?starts_with(name,'$RESOURCE_GROUP_PREFIX')]".name --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
   ```

1. Cloud Shell 창을 닫습니다.

> **결과**: 이 랩에서 사용한 리소스를 제거하여 이 연습을 완료해야 합니다.
