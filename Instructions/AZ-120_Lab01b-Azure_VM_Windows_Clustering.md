---
lab:
    title: '3b: Azure VM에서 Windows 클러스터링 구현'
    module: '모듈 3: Azure의 SAP 인증 제공'
---

# AZ 120 모듈 3: Azure의 SAP 인증 제공
# 랩 3b: Azure VM에서 Windows 클러스터링 구현

예상 시간: 120분

이 랩의 모든 작업은 Azure Portal(PowerShell Cloud Shell 세션 포함)에서 수행됩니다.  

   > **참고**: Cloud Shell을 사용하지 않는 경우 랩 가상 머신에 Azure PowerShell 모듈이 설치되어 있어야 합니다. [**https://docs.microsoft.com/ko-kr/powershell/azure/install-az-ps-msi?view=azps-2.8.0**](https://docs.microsoft.com/ko-kr/powershell/azure/install-az-ps-msi?view=azps-2.8.0))

랩 파일: 없음

## 시나리오
  
SQL Server를 데이터베이스 관리 시스템으로 사용하여 Azure 기반 SAP NetWeaver 배포를 준비하기 위해 Adatum Corporation에서는 Windows Server 2019를 실행하는 Azure VM에 클러스터링을 구현하는 프로세스를 살펴보려고 합니다.

## 목표
  
이 랩을 완료하면 다음과 같은 역량을 갖추게 됩니다.

-   고가용성 SAP NetWeaver 배포를 지원하는 데 필요한 Azure 컴퓨팅 리소스 프로비전

-   고가용성 SAP NetWeaver 배포를 지원하도록 Windows Server 2019를 실행하는 Azure VM의 운영 체제 구성

-   고가용성 SAP NetWeaver 배포를 지원하는 데 필요한 Azure 네트워크 리소스 프로비전

## 요구 사항

-   사용 가능한 DSv3 vCPU(4 x 4) 및 DSv2(1 x 1) vCPU의 수가 충분한 Microsoft Azure 구독

-   Windows 10, Windows Server 2016 또는 Windows Server 2019를 실행하고 Azure에 액세스할 수 있는 랩 컴퓨터

## 연습 1: 고가용성 SAP NetWeaver 배포를 지원하는 데 필요한 Azure 컴퓨팅 리소스 프로비전

재생 시간: 50분

이 연습에서는 Windows Server 2019를 실행하는 Azure VM에서 장애 조치(failover) 클러스터링을 구성하는 데 필요한 Azure 인프라 컴퓨팅 구성 요소를 배포합니다. Active Directory 도메인 컨트롤러 한 쌍을 배포한 다음 Windows Server 2019를 실행하는 Azure VM을 동일한 가상 네트워크의 동일한 가용성 집합 안에 배포합니다. 도메인 컨트롤러 배포를 자동화하려면 Azure Resource Manager 빠른 시작 템플릿(<https://github.com/Azure/azure-quickstart-templates/tree/master/active-directory-new-domain-ha-2-dc>)을 사용합니다.

### 작업 1: Azure Resource Manager 템플릿을 사용하여 고가용성 Active Directory 도메인 컨트롤러를 실행하는 Azure VM 쌍 배포

1.  랩 컴퓨터에서 웹 브라우저를 시작하고 Azure Portal(https://portal.azure.com)로 이동합니다.

1.  메시지가 표시되면 이 랩에 사용할 Azure 구독에 대한 소유자 또는 기여자 역할이 있는 직장, 학교 또는 개인 Microsoft 계정으로 로그인합니다.

1.  Azure Portal에서 **+ 리소스 만들기**를 클릭합니다.

1.  **새로 만들기** 블레이드에서 새 **템플릿 배포(사용자 지정 템플릿을 사용하여 배포)** 만들기를 시작합니다.

1.  **사용자 지정 배포** 블레이드의 **GitHub 빠른 시작 템플릿 로드** 드롭다운 목록에서 **active-directory-new-domain-ha-2-dc** 항목을 선택하고 **템플릿 선택**을 클릭합니다.

    > **참고**: 또는, Azure 빠른 시작 템플릿 페이지(<https://github.com/Azure/azure-quickstart-templates>)로 이동하여 **Create 2 new Windows VMs, create a new AD Forest, Domain, and 2 DCs in an availability set** 라는 이름의 템플릿의 찾고, **Deploy to Azure** 단추를 클릭하여 배포를 시작할 수 있습니다.
	
1.  **도메인 컨트롤러 2개가 포함된 새 AD 도메인 만들기** 블레이드에서 **템플릿 편집**을 클릭합니다.

1.   **템플릿** 블레이드에서 **adVMSize** 변수에 값을 할당하는 줄을 찾습니다.

```
"adVMSize": "Standard_DS2_v2"

```

1.  **템플릿 편집** 블레이드에서 **adVMSize** 변수의 값을 **Standard_D4S_v3**로 설정하고 **저장**을 클릭합니다.

```
"adVMSize": "Standard_D4S_v3"

```

1.  **도메인 컨트롤러 2개가 포함된 새 AD 도메인 만들기** 블레이드로 돌아와 다음 설정을 지정하고 **구매**를 클릭하여 배포를 시작합니다.

    -   구독: *Azure 구독의 이름*

    -   리소스 그룹: **az12001b-ad-RG***라는 새 리소스 그룹*

    -   위치: *Azure VM을 배포할 수 있고 랩 위치와 가장 가까운 Azure 지역*

    -   관리자 사용자 이름: **Student**

    -   암호: **Pa55w.rd1234**

    -   도메인 이름: **adatum.com**

    -   DnsPrefix: *유효한 모든 고유 DNS 접두사*

    -   Pdc RDP 포트: **3389**

    -   Bdc RDP 포트: **13389**

    -   _artifacts 위치: **https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/active-directory-new-domain-ha-2-dc**

    -   _artifacts 위치 SAS 토큰: *비워 둠*

    -   위치: **[resourceGroup().location]**

    -   위에 명시된 사용 약관에 동의함: *사용*

    > **참고**: 배포에는 약 35분이 소요됩니다. 다음 작업을 진행하기 전에 배포가 완료될 때까지 기다립니다.

### 작업 2: 동일한 가용성 집합에서 Windows Server 2016을 실행하는 Azure VM 쌍을 배포합니다.

1.  랩 컴퓨터의 Azure Portal에서 **+ 리소스 만들기** 를 클릭합니다.

1.  **새로 만들기** 블레이드에서 다음 설정으로 **Windows Server 2019 Datacenter** Azure VM 프로비전을 시작합니다.

    -   구독: *Azure 구독의 이름*

    -   리소스 그룹: **az12001b-cl-RG***라는 새 리소스 그룹*

    -   가상 머신 이름: **az12001b-cl-vm0**

    -   지역: *이전 작업에서 Azure VM을 배포한 것과 동일한 Azure 지역*

    -   가용성 옵션: **가용성 집합**

    -   가용성 집합: *이름이* **az12001b-cl-avset***이고 2개의 장애 도메인과 5개의 업데이트 도메인이 있는 새 가용성 집합*

    -   이미지: **Windows Server 2019 Datacenter**

    -   크기: **표준 D4s v3**

    -   사용자 이름: **Student**

    -   암호: **Pa55w.rd1234**

    -   공용 인바운드 포트: **선택한 포트 허용**

    -   인바운드 포트 선택: **RDP (3389)**

    -   이미 Windows 라이선스가 있습니까? **아니요**

    -   OS 디스크 유형: **프리미엄 SSD**

    -   가상 네트워크: **adVNET**

    -   서브넷 이름: **clSubnet***이라는 이름의 새 서브넷*

    -   서브넷 주소 범위: **10.0.1.0/24**

    -   공용 IP 주소: **az12001b-cl-vm0-ip***라는 이름의 새 IP 주소*

    -   NIC 네트워크 보안 그룹: **기본**

    -   공용 인바운드 포트: **선택한 포트 허용**

    -   인바운드 포트 선택: **RDP (3389)**

    -   가속화된 네트워킹: **켜기**

    -   기존 부하 분산 솔루션 뒤에 이 가상 머신을 배치: **아니요**

    -   무료로 기본 계획 사용: **아니요**

    -   부팅 진단: **꺼짐**

    -   OS 게스트 진단: **꺼짐**

    -   시스템 할당 관리 ID: **꺼짐**
	
    -	AAD 자격 증명으로 로그인(미리 보기): **해제**

    -   자동 종료 사용 설정: **꺼짐**
	
    -	백업 사용: **해제**

    -   확장: *없음*

    -   태그: *없음*

1.  프로비전이 완료될 때까지 기다리지 말고 다음 단계를 계속합니다.

1.  다음 설정으로 다른 **Windows Server 2019 Datacenter** Azure VM을 프로비전합니다.

    -   구독: *Azure 구독의 이름*

    -   리소스 그룹: **az12001b-cl-RG**

    -   가상 머신 이름: **az12001b-cl-vm1**

    -   지역: *이전 작업에서 Azure VM을 배포한 것과 동일한 Azure 지역*

    -   가용성 옵션: **가용성 집합**

    -   가용성 집합: **az12001b-cl-avset**

    -   이미지: **Windows Server 2019 Datacenter**

    -   크기: **표준 D4s v3**

    -   사용자 이름: **Student**

    -   암호: **Pa55w.rd1234**

    -   공용 인바운드 포트: **선택한 포트 허용**

    -   인바운드 포트 선택: **RDP (3389)**

    -   이미 Windows 라이선스가 있습니까? **아니요**

    -   OS 디스크 유형: **프리미엄 SSD**

    -   가상 네트워크: **adVNET**

    -   서브넷 이름: **clSubnet**

    -   공용 IP 주소: **az12001b-cl-vm1-ip***라는 이름의 새 IP 주소*

    -   NIC 네트워크 보안 그룹: **기본**

    -   공용 인바운드 포트: **선택한 포트 허용**

    -   인바운드 포트 선택: **RDP (3389)**

    -   가속화된 네트워킹: **켜기**

    -   기존 부하 분산 솔루션 뒤에 이 가상 머신을 배치: **아니요**

    -   무료로 기본 계획 사용: **아니요**

    -   부팅 진단: **꺼짐**

    -   OS 게스트 진단: **꺼짐**

    -   시스템 할당 관리 ID: **꺼짐**
	
    -	AAD 자격 증명으로 로그인(미리 보기): **해제**

    -   자동 종료 사용 설정: **꺼짐**
	
    -	백업 사용: **해제**

    -   확장: *없음*

    -   태그: *없음*

1.  프로비전이 완료될 때까지 기다립니다. 이 작업은 몇 분 정도 걸립니다.

### 작업 3: Azure VM 디스크 만들기 및 구성

1.  Azure Portal에서 Cloud Shell의 PowerShell 세션을 시작합니다. 

    > **참고**: 현재 Azure 구독에서 Cloud Shell을 처음 시작하는 경우 Cloud Shell 파일을 유지할 Azure 파일 공유를 만들라는 메시지가 표시됩니다. 이 경우 기본값을 허용하면 자동으로 생성된 리소스 그룹에 스토리지 계정이 만들어집니다.

1.  Cloud Shell 창에서 다음 명령을 실행하여 4개의 관리 디스크로 구성된 첫 번째 세트를 만듭니다. 이 세트는 이전 작업에서 배포한 첫 번째 Azure VM에 연결됩니다.

```
$resourceGroupName = 'az12001b-cl-RG'

$location = (Get-AzResourceGroup -Name $resourceGroupName).Location

$diskConfig = New-AzDiskConfig -Location $location -DiskSizeGB 128 -AccountType Premium_LRS -OsType Windows -CreateOption Empty

for ($i=0;$i -lt 4;$i++) {New-AzDisk -ResourceGroupName $resourceGroupName -DiskName az12001b-cl-vm0-DataDisk$i -Disk $diskConfig}
```

1.  Cloud Shell 창에서 다음 명령을 실행하여 4개의 관리 디스크로 구성된 두 번째 세트를 만듭니다. 이 세트는 이전 작업에서 배포한 두 번째 Azure VM에 연결됩니다.

```
for ($i=0;$i -lt 4;$i++) {New-AzDisk -ResourceGroupName $resourceGroupName -DiskName az12001b-cl-vm1-DataDisk$i -Disk $diskConfig}
```

1.  Azure Portal에서 이전 작업에서 프로비전한 첫 번째 Azure VM(**az12001b-cl-vm0**)의 블레이드로 이동합니다.

1.  **az12001b-cl-vm0** 블레이드에서 **az12001b-cl-vm0 - 디스크** 블레이드로 이동합니다.

1.  **az12001b-cl-vm0 - 디스크** 블레이드에서 다음 설정을 사용하여 데이터 디스크를 az12001b-cl-vm0에 연결합니다.

    -   LUN: **0**

    -   디스크 이름: **az12001b-cl-vm0-DataDisk0**

    -   리소스 그룹: **az12001b-cl-RG**

    -   호스트 캐싱: **읽기 전용**

1.  이전 단계를 반복하여 나머지 3개의 디스크를 접두사 **az12001b-cl-vm0-DataDisk**를 사용하여 연결합니다(총 4개). 디스크 이름의 마지막 문자와 일치하는 LUN 번호를 할당합니다. 마지막 디스크(LUN **4**)의 경우 호스트 캐싱을 **없음**으로 설정합니다.

1.  변경 내용을 저장합니다. 

1.  Azure Portal에서 이전 작업에서 프로비전한 두 번째 Azure VM(**az12001b-cl-vm1**)의 블레이드로 이동합니다.

1.  **az12001b-cl-vm1** 블레이드에서 **az12001b-cl-vm1 - 디스크** 블레이드로 이동합니다.

1.  **az12001b-cl-vm1 - 디스크** 블레이드에서 다음 설정을 사용하여 데이터 디스크를 az12001b-cl-vm1에 연결합니다.

    -   LUN: **0**

    -   디스크 이름: **az12001b-cl-vm1-DataDisk0**

    -   리소스 그룹: **az12001b-cl-RG**

    -   호스트 캐싱: **읽기 전용**

1.  이전 단계를 반복하여 나머지 3개의 디스크를 접두사 **az12001b-cl-vm1-DataDisk**를 사용하여 연결합니다(총 4개). 디스크 이름의 마지막 문자와 일치하는 LUN 번호를 할당합니다. 마지막 디스크(LUN **4**)의 경우 호스트 캐싱을 **없음**으로 설정합니다.

1.  변경 내용을 저장합니다. 

> **결과**: 이 연습을 완료하면 고가용성 SAP NetWeaver 배포를 지원하는 데 필요한 Azure 컴퓨팅 리소스를 프로비전했습니다.


## 연습 2: 고가용성 SAP NetWeaver 설치를 지원하도록 Windows Server 2019를 실행하는 Azure VM의 운영 체제 구성

재생 시간: 40분

### 작업 1: Windows Server 2019 Azure VM을 Active Directory 도메인에 가입합니다.

   > **참고**: 이 작업을 시작하기 전에 이전 연습의 마지막 작업에서 시작한 템플릿 배포가 완료되었는지 확인합니다. 

1.  Azure Portal에서 이 랩의 첫 번째 연습에서 자동으로 프로비전된 가상 네트워크 **adVNET**의 블레이드로 이동합니다.

1.  **adVNET - DNS 서버** 블레이드를 표시하고 가상 네트워크가 이 랩의 첫 번째 연습에서 DNS 서버로 도메인 컨트롤러에 할당된 개인 IP 주소로 구성되었는지 확인합니다.

1.  Azure Portal에서 Cloud Shell의 PowerShell 세션을 시작합니다. 

1.  Cloud Shell 창에서 다음 명령을 실행하여 이전 연습의 두 번째 작업에서 배포한 Windows Server 2019 Azure VM을 **adatum.com** Active Directory 도메인에 가입시킵니다.

```
$resourceGroupName = 'az12001b-cl-RG'

$location = (Get-AzureRmResourceGroup -Name $resourceGroupName).Location

$settingString = '{"Name": "adatum.com", "User": "adatum.com\\Student", "Restart": "true", "Options": "3"}'

$protectedSettingString = '{"Password": "Pa55w.rd1234"}'

$vmNames = @('az12001b-cl-vm0','az12001b-cl-vm1')

foreach ($vmName in $vmNames) { Set-AzVMExtension -ResourceGroupName $resourceGroupName -ExtensionType 'JsonADDomainExtension' -Name 'joindomain' -Publisher "Microsoft.Compute" -TypeHandlerVersion "1.0" -Vmname $vmName -Location $location -SettingString $settingString -ProtectedSettingString $protectedSettingString }
```

1.  A다음 태스크를 진행하기 전에 스크립트가 완료될 때까지 기다립니다.


### 작업 2: 고가용성 SAP NetWeaver 설치를 지원하도록 Windows Server 2019를 실행하는 Azure VM의 스토리지 구성

1.  Azure Portal에서 이 랩의 첫 번째 연습에서 프로비전한 가상 머신 **az12001b-cl-vm0**의 블레이드로 이동합니다.

1.  **az12001b-cl-vm0** 블레이드에서 원격 데스크톱을 사용하여 가상 머신 게스트 운영 체제에 연결합니다. 인증하라는 메시지가 표시되면 다음 자격 증명을 입력합니다.

    -   사용자 이름: **ADATUM\Student**

    -   암호: **Pa55w.rd1234**

1.  az12001b-cl-vm0에 대한 RDP 세션 내의 서버 관리자에서 **로컬 서버** 보기로 이동하고 **IE 고급 보안 구성**을 일시적으로 끕니다.

1.  az12001b-cl-vm0에 대한 RDP 세션 내의 서버 관리자에서 **파일 및 스토리지 서비스** -> **서버** 노드로 이동합니다. 

1.  **스토리지 풀** 보기로 이동하여 이전 연습에서 Azure VM에 연결한 모든 디스크가 표시되는지 확인합니다.

1.  **새 스토리지 풀 마법사**에서 다음 설정으로 새 스토리지 풀을 만듭니다.

    -   이름: **Data Storage Pool**

    -   실제 디스크: *처음 3개의 LUN 번호(0~2)에 해당하는 디스크 번호가 있는 디스크 3개를 선택하고 할당을* **자동**으로 설정합니다.

    > **참고**: **섀시** 열의 항목을 사용하여 **LUN** 번호를 식별합니다.

1.  **새 가상 디스크 마법사**를 사용하여 다음 설정으로 새 가상 디스크를 만듭니다.

    -   가상 디스크 이름: **Data Virtual Disk**

    -   스토리지 레이아웃: **단순**

    -   프로비전: **고정**

    -   크기: **최대 크기**

1.  **새 볼륨 마법사**를 사용하여 다음 설정으로 새 볼륨을 만듭니다.

    -   서버 및 디스크: *기본값 수락*

    -   크기: *기본값 수락*

    -   드라이브 문자: **M**

    -   파일 시스템: **ReFS**

    -   할당 단위 크기: **기본값**

    -   볼륨 레이블: **데이터**

1.  **스토리지 풀** 보기로 돌아가 **새 스토리지 풀 마법사**를 사용하여 다음 설정으로 새 스토리지 풀을 만듭니다.

    -   이름: **Log Storage Pool**

    -   실제 디스크: *마지막 4개 디스크를 선택하고 할당을* **자동**으로 설정합니다.

1.  **새 가상 디스크 마법사**를 사용하여 다음 설정으로 새 가상 디스크를 만듭니다.

    -   가상 디스크 이름: **Log Virtual Disk**

    -   스토리지 레이아웃: **단순**

    -   프로비전: **고정**

    -   크기: **최대 크기**

1.  **새 볼륨 마법사**를 사용하여 다음 설정으로 새 볼륨을 만듭니다.

    -   서버 및 디스크: *기본값 수락*

    -   크기: *기본값 수락*

    -   드라이브 문자: **L**

    -   파일 시스템: **ReFS**

    -   할당 단위 크기: **기본값**

    -   볼륨 레이블: **로그**

1.  이 태스크의 이전 단계를 반복하여 az12001b-cl-vm1에서 스토리지를 구성합니다.

### 작업 3: Windows Server 2019을 실행하는 Azure VM에서 고가용성 SAP NetWeaver 설치를 지원하는 장애 조치(failover) 클러스터링의 구성을 준비합니다.

1.  az12001b-cl-vm0에 대한 RDP 세션 내에서 Windows PowerShell ISE 세션을 시작하고 az12001b-cl-vm0과 az12001b-cl-vm1 모두에서 다음을 실행하여 장애 조치(failover) 클러스터링 및 원격 관리 도구 기능을 설치합니다.

```
$nodes = @('az12001b-cl-vm1', 'az12001b-cl-vm0')

Invoke-Command $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools} 

Invoke-Command $nodes {Install-WindowsFeature RSAT -IncludeAllSubFeature -Restart} 
```

    > **참고**: 이렇게 하면 두 Azure VM의 게스트 운영 체제가 다시 시작됩니다.

1.  랩 컴퓨터의 Azure Portal에서 **+ 리소스 만들기**를 클릭합니다.

1.  **새로 만들기** 블레이드에서 다음 설정으로 새 **스토리지 계정** 만들기를 시작합니다.

    -   구독: *Azure 구독의 이름*

    -   리소스 그룹: **az12001b-cl-RG**

    -   스토리지 계정 이름: *3~24자의 문자와 숫자로 구성된 고유한 이름*

    -   위치: *이전 연습에서 Azure VM을 배포한 것과 동일한 Azure 지역*

    -   성능: **표준**

    -   계정 종류: **스토리지(범용 v1)**

    -   복제: **LRS(로컬 중복 스토리지)**
	
    -	연결 방법: **공용 엔드포인트(모든 네트워크)**

    -   보안 전송 필요: **사용**

    -   대용량 파일 공유: **사용 안 함**

    -   Blob 일시 삭제: **사용 안 함**

    -   계층 구조 네임스페이스: **사용 안 함**

### 작업 4: Windows Server 2019을 실행하는 Azure VM에서 고가용성 SAP NetWeaver 설치를 지원하는 장애 조치(failover) 클러스터링을 구성합니다.

1.  Azure Portal에서 이 랩의 첫 번째 연습에서 프로비전한 가상 머신 **az12001b-cl-vm0**의 블레이드로 이동합니다.

1.  **az12001b-cl-vm0** 블레이드에서 원격 데스크톱을 사용하여 가상 머신 게스트 운영 체제에 연결합니다. 인증하라는 메시지가 표시되면 다음 자격 증명을 입력합니다.

    -   사용자 이름: **ADATUM\\Student**

    -   암호: **Pa55w.rd1234**

1.  az12001b-cl-vm0에 대한 RDP 세션 내에서 서버 관리자의 **도구** 메뉴에서 **Active Directory 관리 센터**를 시작합니다.

1.  Active Directory 관리 센터에서 adatum.com 도메인의 루트에 **Clusters**라는 새 조직 구성 단위를 만듭니다.

1.  Active Directory 관리 센터에서 **az12001b-cl-vm0** 및 **az12001b-cl-vm1**의 컴퓨터 계정을 **컴퓨터** 컨테이너에서 **Clusters** 조직 구성 단위로 이동합니다.

1.  az12001b-cl-vm0에 대한 RDP 세션 내에서 Windows PowerShell ISE 세션을 시작하고 다음을 실행하여 새 클러스터를 만듭니다.

```
$nodes = @('az12001b-cl-vm0','az12001b-cl-vm1')

New-Cluster -Name az12001b-cl-cl0 -Node $nodes -NoStorage -StaticAddress 10.0.1.6
```

1.  az12001b-cl-vm0에 대한 RDP 세션 내에서 **Active Directory 관리 센터** 콘솔로 전환합니다.

1.  Active Directory 관리 센터에서 **Clusters** 조직 구성 단위로 이동하고 해당 **속성** 창을 표시합니다. 

1.  **Clusters** 조직 구성 단위의 **속성** 창에서 **확장** 섹션으로 이동하여 **보안** 탭을 표시합니다. 

1.  **보안** 탭에서 **고급** 단추를 클릭하여 **클러스터에 대한 고급 보안 설정** 창을 엽니다. 

1.  **컴퓨터에 대한 고급 보안 설정**의 **사용 권한** 탭에서 **추가**를 클릭합니다.

1.  **클러스터에 대한 사용 권한 항목** 창에서 **보안 주체 선택**을 클릭합니다.

1.  **사용자, 서비스 계정 또는 그룹 선택** 대화 상자에서 **개체 유형**을 클릭하고 **컴퓨터** 항목 옆의 확인란을 사용하도록 설정한 다음 **확인**을 클릭합니다. 

1.  **사용자, 컴퓨터, 서비스 계정 또는 그룹 선택** 대화 상자로 돌아가 **선택할 개체 이름을 입력하십시오**에 **az12001b-cl-cl0**을 입력하고 **확인**을 클릭합니다.

1.  **클러스터에 대한 사용 권한 항목** 창에서 **유형** 드롭다운 목록에 **허용**이 나타나는지 확인합니다. 다음으로 **적용 대상** 드롭다운 목록에서 **이 개체 및 모든 하위 개체**를 선택합니다. **사용 권한** 목록에서 **컴퓨터 개체 만들기** 및 **컴퓨터 개체 삭제** 확인란을 선택하고 **확인**을 두 번 클릭합니다.

1.  Windows PowerShell ISE 세션 내에서 다음을 실행하여 Az PowerShell 모듈을 설치합니다.

```
Install-PackageProvider -Name NuGet -Force

Install-Module -Name Az -Force
```

1.  Windows PowerShell ISE 세션 내에서 다음 명령을 실행하여 Azure AD 자격 증명으로 인증합니다.

```
Add-AzAccount
```

    > **참고**: 메시지가 표시되면 이 랩에 사용할 Azure 구독에 대한 소유자 또는 기여자 역할이 포함된 회사 또는 학교 또는 개인 Microsoft 계정을 사용하여 로그인합니다.

1.  Windows PowerShell ISE 세션 내에서 다음을 실행하여 새 클러스터의 클라우드 감시 쿼럼을 설정합니다.

```
$resourceGroupName = 'az12001b-cl-RG'

$cwStorageAccountName = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName)[0].StorageAccountName

$cwStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $cwStorageAccountName).Value[0]

Set-ClusterQuorum -CloudWitness -AccountName $cwStorageAccountName -AccessKey $cwStorageAccountKey
```

1.  결과 구성을 확인하려면 az12001b-cl-vm0에 대한 RDP 세션 내에서 서버 관리자의 **도구** 메뉴에서 **장애 조치(failover) 클러스터 관리자**를 시작합니다.

1.  **장애 조치(failover) 클러스터 관리자** 콘솔에서 **az12001b-cl-cl0** 클러스터 구성(노드와 감시 및 네트워크 설정 포함)을 검토합니다. 클러스터에는 공유 스토리지가 없습니다.

1.  az12001b-cl-vm0에 대한 RDP 세션을 종료합니다.

> **결과**: 이 연습을 완료한 후 고가용성 SAP NetWeaver 설치를 지원하도록 Windows Server 2019를 실행하는 Azure VM의 운영 체제가 구성되었습니다.


## 연습 3: 고가용성 SAP NetWeaver 배포를 지원하는 데 필요한 Azure 네트워크 리소스 프로비전

재생 시간: 30분

이 연습에서는 SAP NetWeaver의 클러스터된 설치를 수용하도록 Azure Load Balancer를 구현합니다.

### 작업 1: 부하 분산 설정을 지원하도록 Azure VM을 구성합니다.

   > **참고**: 표준 SKU의 Azure Load Balancer 한 쌍을 설정할 것이므로 먼저 부하 분산된 백 엔드 풀로 사용할 두 Azure VM의 네트워크 어댑터에 연결된 공용 IP 주소를 제거해야 합니다.

1.  랩 컴퓨터의 Azure Portal에서 Azure VM **az12001b-cl-vm0**의 블레이드로 이동합니다.  

1.  **az12001b-cl-vm0** 블레이드에서 네트워크 어댑터에 연결된 공용 IP 주소 **az12001b-cl-vm0-ip**의 블레이드로 이동합니다.

1.  **az12001b-cl-vm0-ip** 블레이드에서 먼저 네트워크 인터페이스의 공용 IP 주소 연결을 끊은 다음 삭제합니다.

1.  Azure Portal 인터페이스에서 Azure VM **az12001b-cl-vm1**의 블레이드로 이동합니다.  

1.  **az12001b-cl-vm1** 블레이드에서 네트워크 어댑터에 연결된 공용 IP 주소 **az12001b-cl-vm1-ip**의 블레이드로 이동합니다.

1.  **az12001b-cl-vm1-ip** 블레이드에서 먼저 네트워크 인터페이스의 공용 IP 주소 연결을 끊은 다음 삭제합니다.

1.  Azure Portal에서 **az12001a-vm0** Azure VM의 블레이드로 이동합니다.

1.  **az12001a-vm0** 블레이드에서 해당 **네트워킹** 블레이드로 이동합니다. 

1.  **az12001a-vm0 - 네트워킹** 블레이드에서 az12001a-vm0의 네트워크 인터페이스로 이동합니다. 

1.  az12001a-vm0의 네트워크 인터페이스 블레이드에서 IP 구성 블레이드로 이동하여 **ipconfig1** 블레이드를 표시합니다.

1.  **ipconfig1** 블레이드에서 프라이빗 IP 주소 할당을 **정적**으로 설정하고 변경 내용을 저장합니다.

1.  Azure Portal에서 **az12001a-vm1** Azure VM의 블레이드로 이동합니다.

1.  **az12001a-vm1** 블레이드에서 해당 **네트워킹** 블레이드로 이동합니다. 

1.  **az12001a-vm1 - 네트워킹** 블레이드에서 az12001a-vm1의 네트워크 인터페이스로 이동합니다. 

1.  az12001a-vm1의 네트워크 인터페이스 블레이드에서 IP 구성 블레이드로 이동하여 **ipconfig1** 블레이드를 표시합니다.

1.  **ipconfig1** 블레이드에서 프라이빗 IP 주소 할당을 **정적**으로 설정하고 변경 내용을 저장합니다.

### 작업 2: 인바운드 트래픽을 처리하는 Azure Load Balancer 만들기 및 구성

1.  Azure Portal에서 **+ 리소스 만들기**를 클릭합니다.

1.  **새로 만들기** 블레이드에서 다음 설정으로 새 Azure Load Balancer 만들기를 시작합니다.

    -   구독: *Azure 구독의 이름*

    -   리소스 그룹: **az12001b-cl-RG**

    -   이름: **az12001b-cl-lb0**

    -   지역: *이 랩의 첫 번째 연습에서 Azure VM을 배포한 것과 동일한 Azure 지역*

    -   유형: **내부**

    -   SKU: **Standard**

    -   가상 네트워크: **adVNET**

    -   서브넷: **clSubnet**

    -   IP 주소 할당: **정적**

    -   IP 주소: **10.0.1.240**

    -   가용성 영역: **영역 중복**

1.  부하 분산 장치가 프로비전될 때까지 기다린 다음 Azure Portal에서 해당 블레이드로 이동합니다.

1.  **az12001b-cl-lb0** 블레이드에서 다음 설정으로 백 엔드 풀을 추가합니다.

    -   이름: **az12001b-cl-lb0-bepool**

    -   가상 네트워크: **adVNET**

    -   가상 머신: **az12001b-cl-vm0**  IP 주소: **ipconfig1**

    -   가상 머신: **az12001b-cl-vm1**  IP 주소: **ipconfig1**

1.  **az12001b-cl-lb0** 블레이드에서 다음 설정으로 상태 프로브를 추가합니다.

    -   이름: **az12001b-cl-lb0-hprobe**

    -   프로토콜: **TCP**

    -   포트: **59999**

    -   간격: **5***초*

    -   비정상 임계값: **2***회 연속 실패*

1.  **az12001b-cl-lb0** 블레이드에서 다음 설정으로 네트워크 부하 분산 규칙을 추가합니다.

    -   이름: **az12001b-cl-lb0-lbruletcp1433**

    -   IP 버전: **IPv4**

    -   프론트 엔드 IP 주소: **192.168.0.240(LoadBalancerFrontEnd)**

    -   HA 포트: **사용 안 함**

    -   프로토콜: **TCP**

    -   포트: **1433**

    -   백 엔드 포트: **1433**

    -   백 엔드 풀: **az12001b-cl-lb0-bepool(가상 머신 2개)**

    -   상태 프로브:**az12001b-cl-lb0-hprobe(TCP:59999)**

    -   세션 지속성: **없음**

    -   유휴 시간 초과(분): **4**

    -   부동 IP(Direct Server Return): **사용**

### 작업 3: 아웃바운드 트래픽을 처리하는 Azure Load Balancer 만들기 및 구성

1.  Azure Portal의 Cloud Shell에서 PowerShell 세션을 시작합니다. 

1.  Cloud Shell 창에서 다음 명령을 실행하여 두 번째 부하 분산 장치에 사용할 공용 IP 주소를 만듭니다.

```
$resourceGroupName = 'az12001b-cl-RG'

$location = (Get-AzResourceGroup -Name $resourceGroupName).Location

$pipName = 'az12001b-cl-lb0-pip'

az network public-ip create --resource-group $resourceGroupName --name $pipName --sku Standard --location $location
```

1.  Cloud Shell 창에서 다음 명령을 실행하여 두 번째 부하 분산 장치를 만듭니다.

```
$lbName = 'az12001b-cl-lb1'

$lbFeName = 'az12001b-cl-lb1-fe'

$lbBePoolName = 'az12001b-cl-lb1-bepool'
   
$pip = Get-AzPublicIpAddress -ResourceGroupName $resourceGroupName -Name $pipName

$feIpconfiguration = New-AzLoadBalancerFrontendIpConfig -Name $lbFeName -PublicIpAddress $pip

$bePoolConfiguration = New-AzLoadBalancerBackendAddressPoolConfig -Name $lbBePoolName

New-AzLoadBalancer -ResourceGroupName $resourceGroupName -Location $location -Name $lbName -Sku Standard -BackendAddressPool $bePoolConfiguration -FrontendIpConfiguration $feIpconfiguration
```

1.  Cloud Shell 창을 닫습니다.

1.  Azure Portal에서 Azure Load Balancer **az12001b-cl-lb1**의 속성이 표시되는 블레이드로 이동합니다.

1.  **az12001b-cl-lb1** 블레이드에서 **백 엔드 풀**을 클릭합니다.

1.  **az12001b-cl-lb1 - 백 엔드 풀** 블레이드에서 **az12001b-cl-lb1-bepool**을 클릭합니다.

1.  **az12001b-cl-lb1-bepool** 블레이드에서 다음 설정을 지정하고 **저장**을 클릭합니다.

    -   가상 네트워크: **adVNET(VM 4개)**

    -   가상 머신: **az12001b-cl-vm0**  IP 주소: **ipconfig1**

    -   가상 머신: **az12001b-cl-vm1**  IP 주소: **ipconfig1**

1.  **az12001b-cl-lb1** 블레이드에서 **상태 프로브**를 클릭합니다.

1.  **az12001b-cl-lb1 - 상태 프로브** 블레이드에서 다음 설정으로 상태 프로브를 추가합니다.

    -   이름: **az12001b-cl-lb1-hprobe**

    -   프로토콜: **TCP**

    -   포트: **80**

    -   간격: **5***초*

    -   비정상 임계값: **2***회 연속 실패*

1.  **az12001b-cl-lb1** 블레이드에서 **부하 분산 규칙**을 클릭합니다.

1.  **az12001b-cl-lb1 - 부하 분산 규칙** 블레이드에서 다음 설정으로 네트워크 부하 분산 규칙을 추가합니다.

    -   이름: **az12001b-cl-lb1-lbharule**

    -   IP 버전: **IPv4**

    -   프런트 엔드 IP 주소: *기본값 수락*

    -   프로토콜: **TCP**

    -   포트: **80**

    -   백 엔드 포트: **80**

    -   백 엔드 풀: **az12001b-cl-lb1-bepool(가상 머신 2개)**

    -   상태 프로브:**az12001b-cl-lb1-hprobe(TCP:80)**

    -   세션 지속성: **없음**

    -   유휴 시간 초과(분): **4**

    -   부동 IP(Direct Server Return): **사용 안 함**

### 작업 4: 점프 호스트 배포

   > **참고**: 2개의 클러스터된 Azure VM은 더 이상 인터넷에서 직접 액세스할 수 없으므로 점프 호스트 역할을 할 Windows Server 2019 Datacenter를 실행하는 Azure VM을 배포합니다. 

1.  랩 컴퓨터의 Azure Portal에서 **+ 리소스 만들기**를 클릭합니다.

1.  **새로 만들기** 블레이드에서 **Windows Server 2019 Datacenter** 이미지에 따라 새 Azure VM 만들기를 시작합니다.

1.  다음 설정으로 Azure VM을 프로비전합니다.

    -   구독: *Azure 구독의 이름*

    -   리소스 그룹: **az12001b-cl-RG**

    -   가상 머신 이름: **az12001b-vm2**

    -   지역: *이 랩의 첫 번째 연습에서 Azure VM을 배포한 것과 동일한 Azure 지역*

    -   가용성 옵션: **인프라 중복성 불필요**

    -   이미지: **Windows Server 2019 Datacenter**

    -   크기: **표준 DS1 v2**

    -   사용자 이름: **student**

    -   암호: **Pa55w.rd1234**

    -   공용 인바운드 포트: **선택한 포트 허용**

    -   인바운드 포트 선택: **RDP (3389)**

    -   이미 Windows 라이선스가 있습니까? **아니요**

    -   OS 디스크 유형: **표준 HDD**

    -   가상 네트워크: **adVNET**

    -   서브넷: **bastionSubnet***이라는 이름의 새 서브넷*

    -   주소 범위: **10.0.255.0/24**

    -   공용 IP 주소: **az12001b-vm2-ip***라는 이름의 새 IP 주소*

    -   NIC 네트워크 보안 그룹: **기본**

    -   공용 인바운드 포트: **선택한 포트 허용**

    -   인바운드 포트 선택: **RDP (3389)**

    -   가속화된 네트워킹: **꺼짐**

    -   기존 부하 분산 솔루션 뒤에 이 가상 머신을 배치: **아니요**

    -   부팅 진단: **꺼짐**

    -   OS 게스트 진단: **꺼짐**

    -   시스템 할당 관리 ID: **꺼짐**
	
    -	AAD 자격 증명으로 로그인(미리 보기): **해제**

    -   자동 종료 사용 설정: **꺼짐**
	
    -	백업 사용: **해제**

    -   확장: *없음*

    -   태그: *없음*

1.  프로비전이 완료될 때까지 기다립니다. 이 작업은 몇 분 정도 걸립니다.

1.  RDP를 통해 새로 프로비전된 Azure VM에 연결합니다. 

1.  az12001b-vm2에 대한 RDP 세션 내에서 개인 IP 주소(각각 10.0.1.4 및 10.0.1.5)를 통해 az12001b-cl--vm0 및 az12001b-cl--vm1에 대한 SSH 세션을 설정할 수 있는지 확인합니다. 

> **결과**: 이 연습을 완료한 후 고가용성 SAP NetWeaver 배포를 지원하는 데 필요한 Azure 네트워크 리소스가 프로비전되었습니다.

## 연습 4: 랩 리소스 제거

재생 시간: 10분

이 연습에서는 이 랩에서 프로비전한 리소스를 제거합니다.

#### 태스크 1: Cloud Shell 열기

1.  Portal 상단에서 **Cloud Shell** 아이콘을 클릭하여 Cloud Shell 창을 열로 셸로 PowerShell을 선택합니다.

1.  Portal 하단의 **Cloud Shell** 명령 프롬프트에 다음 명령을 입력하고 **Enter** 키를 눌러 이 랩에서 만든 모든 리소스 그룹의 목록을 표시합니다.

```
Get-AzResourceGroup | Where-Object {$_.ResourceGroupName -like 'az12001b-*'} | Select-Object ResourceGroupName
```

1.  이 랩에서 만든 리소스 그룹만 출력에 포함되어 있는지 확인합니다. 다음 태스크에서 이러한 그룹을 삭제합니다.

#### 태스크 2: 리소스 그룹 삭제

1.  **Cloud Shell** 명령 프롬프트에 다음 명령을 입력하고 **Enter** 키를 눌러 이 랩에서 만든 리소스 그룹을 삭제합니다.

```
Get-AzResourceGroup | Where-Object {$_.ResourceGroupName -like 'az12001b-*'} | Remove-AzResourceGroup -Force  
```

1.  Portal 하단의 **Cloud Shell** 프롬프트를 닫습니다.


> **결과**: 이 랩에서 사용한 리소스를 제거하여 이 연습을 완료해야 합니다.