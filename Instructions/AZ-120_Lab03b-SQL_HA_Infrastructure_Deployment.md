# AZ 120 모듈 3: Azure의 SAP 구현
# 랩 3b: Windows를 실행하는 Azure VM에서 SAP 아키텍처 구현

예상 시간: 150분

이 랩의 모든 작업은 Azure Portal(PowerShell Cloud Shell 세션 포함)에서 수행됩니다.  

   > **참고**: Cloud Shell을 사용하지 않는 경우 랩 가상 머신에 Az PowerShell 모듈이 설치되어 있어야 합니다([**https://docs.microsoft.com/en-us/powershell/azure/install-az-ps-msi?view=azps-2.8.0**](https://docs.microsoft.com/en-us/powershell/azure/install-az-ps-msi?view=azps-2.8.0)).

랩 파일: 없음

## 시나리오
  
Adatum Corporation에서는 Azure에 배포할 SAP NetWeaver를 준비하는 과정에서 Windows Server 2016을 실행하는 Azure VM에 구현되는 고가용성 SAP NetWeaver를 설명할 데모를 구현하려고 합니다.

## 목적
  
이 랩을 완료하면 다음과 같은 작업을 수행할 수 있습니다.

-   고가용성 SAP NetWeaver 배포를 지원하는 데 필요한 Azure 리소스 프로비전

-   Windows를 실행하는 Azure VM의 운영 체제를 고가용성 SAP NetWeaver 배포를 지원하도록 구성

-   Windows를 실행하는 Azure VM의 운영 체제에 고가용성 SAP NetWeaver 배포를 지원하는 클러스터링 구성

## 요구 사항

-   Microsoft Azure 구독

-   Windows 10, Windows Server 2016 또는 Windows Server 2016를 실행하고 Azure에 액세스할 수 있는 랩 컴퓨터


## 연습 1: 고가용성 SAP NetWeaver 배포를 지원하는 데 필요한 Azure 리소스 프로비전

소요 시간: 60분

이 연습에서는 Windows 클러스터링을 구성하는 데 필요한 Azure 인프라 컴퓨팅 구성 요소를 배포합니다. 여기에는 동일한 가용성 집합에서 Windows Server 2016을 실행하는 Azure VM 쌍을 만드는 작업이 포함됩니다.

### 작업 1: Azure Resource Manager 템플릿을 사용하여 고가용성 Active Directory 도메인 컨트롤러를 실행하는 Azure VM 쌍 배포

1.  랩 컴퓨터에서 웹 브라우저를 시작하고 Azure Portal(https://portal.azure.com)로 이동합니다.

1.  메시지가 표시되면 이 랩에 사용할 Azure 구독에 대한 소유자 또는 기여자 역할이 있는 직장, 학교 또는 개인 Microsoft 계정으로 로그인합니다.

1.  Azure Portal 인터페이스에서 **+ 리소스 만들기** 를 클릭합니다.

1.  **새로 만들기** 블레이드에서 새 **템플릿 배포(사용자 지정 템플릿을 사용한 배포)** 만들기를 시작합니다.

1.  **사용자 지정 배포** 블레이드의 **GitHub 빠른 시작 템플릿 로드** 드롭다운 목록에서 **active-directory-new-domain-ha-2-dc-zones** 항목을 선택하고 **템플릿 선택**을 클릭합니다.

    > **참고**: 또는 Azure 빠른 시작 템플릿 페이지(<https://github.com/Azure/azure-quickstart-templates>)로 이동하여 **각기 다른 가용성 영역에서 새 Windows VM 2개, 새 AD 포리스트, 도메인 및 DC 2개 만들기** 템플릿을 찾은 다음 **Azure에 배포** 단추를 클릭하여 배포를 시작할 수도 있습니다.

1.  **가용성 영역을 사용하여 DC가 2개인 새 AD 도메인 만들기** 블레이드에서 다음 설정을 지정하고 **구매**를 클릭하여 배포를 시작합니다.

    -   구독: *Azure 구독의 이름*

    -   리소스 그룹: **az12003b-ad-RG** *라는 새 리소스 그룹*

    -   위치: *Azure VM을 배포할 수 있고 랩 위치와 가장 가까운 Azure 지역*

    -   관리자 사용자 이름: **Student**

    -   위치: *위에서 지정한 곳과 같은 Azure 지역*

    -   암호: **Pa55w.rd1234**

    -   도메인 이름: **adatum.com**

    -   DnsPrefix: *기본값 수락*

    -   V 크기: **표준 D4S\_v3**

    -   _artifacts 위치: *기본값 수락*

    -   _artifacts 위치 SAS 토큰: *비워 둠*

    -   위에 명시된 사용 약관에 동의함: *사용*

    > **참고**: 배포에는 약 35분이 소요됩니다. 다음 작업을 진행하기 전에 배포가 완료될 때까지 기다립니다.

    > **참고**: CustomScriptExtension 구성 요소를 배포하는 동안 배포가 **충돌** 오류 메시지와 함께 실패하면 다음 단계를 사용하여 이 문제를 해결합니다.

       - Azure Portal의 **배포** 블레이드에서 배포 세부 정보를 검토하고 CustomScriptExtension 설치가 실패한 VM을 식별합니다.
       - Azure Portal에서, 이전 단계에서 식별한 VM의 블레이드로 이동하여 **확장**을 선택하고, **확장** 블레이드에서 CustomScript 확장을 제거합니다.
       - Azure Portal에서 **az12003b-sap-RG** 리소스 그룹 블레이드로 이동하여 **배포**를 선택하고 실패한 배포에 대한 링크를 선택한 후 **재배포**를 선택하고 대상 리소스 그룹(**az12003b-sap-RG**)을 선택한 다음 루트 계정의 암호(**Pa55w.rd1234**)를 입력합니다.

### 작업 2: 고가용성 SAP NetWeaver 배포 및 S2D 클러스터를 실행하는 Azure VM을 호스트하는 서브넷을 프로비전합니다.

1.  Azure Portal에서 **az12003b-ad-RG** 리소스 그룹의 블레이드로 이동합니다.

1.  **az12003b-ad-RG** 리소스 그룹 블레이드의 리소스 목록에서 **adVNET** 가상 네트워크를 찾고 해당 항목을 클릭하여 **adVNET** 블레이드를 표시합니다.

1.  **adVNET** 블레이드에서 **adVNET - 서브넷** 블레이드로 이동합니다. 

1.  **adVNET - 서브넷** 블레이드에서 다음 설정을 사용하여 새 서브넷을 만듭니다.

    -   이름: **sapSubnet**

    -   주소 범위(CIDR 블록): **10.0.1.0/24**

1.  **adVNET - 서브넷** 블레이드에서 다음 설정을 사용하여 새 서브넷을 만듭니다.

    -   이름: **s2dSubnet**

    -   주소 범위(CIDR 블록): **10.0.2.0/24**

1.  Azure Portal에서 Cloud Shell의 PowerShell 세션을 시작합니다. 

    > **참고**: 현재 Azure 구독에서 Cloud Shell을 처음 시작하는 경우 Cloud Shell 파일을 보관할 Azure 파일 공유를 만들라는 메시지가 표시됩니다. 이 경우 기본값을 적용하면 자동으로 생성된 리소스 그룹에 스토리지 계정이 만들어집니다.

1.  Cloud Shell 창에서 다음 명령을 실행하여 이전 작업에서 만든 가상 네트워크를 식별합니다.

    ```
    $resourceGroupName = 'az12003b-ad-RG'

    $vNetName = 'adVNet'

    $subnetName = 'sapSubnet'
    ```

1.  Cloud Shell 창에서 다음 명령을 실행하여 새로 만든 서브넷의 리소스 ID를 식별합니다.

    ```
    $vNet = Get-AzVirtualNetwork -ResourceGroupName $resourceGroupName -Name $vNetName
    
    (Get-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vNet).Id
    ```

1.  결과 값을 클립보드에 복사합니다. 다음 작업에 필요합니다.

### 작업 3: 고가용성 SAP NetWeaver 배포를 호스트할 Windows Server 2016을 실행하는 Azure VM을 프로비전하는 Azure Resource Manager 템플릿 배포

1.  랩 컴퓨터의 Azure Portal에서 **템플릿 배포(사용자 지정 템플릿을 사용한 배포)** 를 검색하여 선택합니다.

1.  **사용자 지정 배포** 블레이드의 **템플릿 선택(면책 조항)** 드롭다운 목록에서 **sap-3-tier-marketplace-image-md** 를 입력하고 **템플릿 선택**을 클릭합니다.

    > **참고**: Microsoft Edge 또는 타사 브라우저를 사용해야 합니다. Internet Explorer를 사용하지 마십시오.

1.  **SAP NetWeaver 3-tier (managed disk)** 블레이드에서 다음 설정을 사용하여 배포를 시작합니다.

    -   구독: *Azure 구독의 이름*

    -   리소스 그룹: **az12003b-sap-RG** *라는 새 리소스 그룹*

    -   위치: *이 연습의 첫 번째 작업에서 지정한 것과 동일한 Azure 지역*

    -   SAP 시스템 ID: **I20**

    -   스택 유형: **ABAP**

    -   OS 유형: **Windows Server 2016 Datacenter**

    -   Dbtype: **SQL**

    -   SAP 시스템 크기: **데모**

    -   시스템 가용성: **HA**

    -   관리자 사용자 이름: **Student**

    -   인증 유형: **암호**

    -   관리자 암호 또는 키: **Pa55w.rd1234**

    -   서브넷 ID: *이전 작업에서 클립보드에 복사한 값*

    -   가용성 영역: **1,2**

    -   위치: **[resourceGroup().location]**

    -   _artifacts 위치: **https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sap-3-tier-marketplace-image-md/**

    -   _artifacts 위치 SAS 토큰: *비워 둠*

    -   위에 명시된 사용 약관에 동의함: *사용*

1.  배포가 완료될 때까지 기다리지 말고 다음 작업을 진행하세요. 

### 작업 5: SOFS(스케일 아웃 파일 서버) 클러스터 배포

이 작업에서는 GitHub([**https://github.com/robotechredmond/301-storage-spaces-direct-md**](https://github.com/robotechredmond/301-storage-spaces-direct-md))의 Azure Resource Manager 빠른 시작 템플릿을 사용하여 SAP ASCS 서버에 대한 파일 공유를 호스트할 SOFS(스케일 아웃 파일 서버) 클러스터를 배포합니다. 

1.  랩 컴퓨터에서 브라우저를 시작하고 [**https://github.com/robotechredmond/301-storage-spaces-direct-md**](https://github.com/robotechredmond/301-storage-spaces-direct-md)로 이동합니다. 

    > **참고**: Microsoft Edge 또는 타사 브라우저를 사용해야 합니다. Internet Explorer를 사용하지 마십시오.

1.  **Use Managed Disks to Create a Storage Spaces Direct (S2D) Scale-Out File Server (SOFS) Cluster with Windows Server 2016** 페이지에서 **Azure에 배포**를 클릭합니다. 브라우저가 Azure Portal로 자동으로 리디렉션되고 **사용자 지정 배포** 블레이드가 표시됩니다.

1.  **사용자 지정 배포** 블레이드에서 다음 설정을 사용하여 배포를 시작 합니다.

    -   구독: **Azure 구독 이름**.

    -   리소스 그룹: **az12003b-s2d-RG** *라는 새 리소스 그룹*

    -   지역: *이 연습의 이전 작업에서 Azure VM을 배포한 것과 동일한 Azure 지역*

    -   이름 접두사: **i20**

    -   V 크기: **표준 D4S\_v3**

    -   가속 네트워킹 사용: **true**

    -   이미지 SKU: **2016-Datacenter-Server-Core**

    -   VM 개수: **2**

    -   VM 디스크 크기: **128**

    -   VM 디스크 개수: **3**

    -   기존 도메인 이름: **adatum.com**

    -   관리자 사용자 이름: **Student**

    -   관리자 암호: **Pa55w.rd1234**

    -   기존 가상 네트워크 RG 이름: **az12003b-ad-RG**

    -   기존 가상 네트워크 이름: **adVNet**

    -   기존 서브넷 이름: **s2dSubnet**

    -   SOFS 이름: **sapglobalhost**

    -   공유 이름: **sapmnt**

    -   예약된 업데이트 요일: **일요일**

    -   예약된 업데이트 시간: **오전 3:00**

    -   실시간 맬웨어 방지 사용: **false**

    -   예약된 맬웨어 방지 사용: **false**

    -   예약된 맬웨어 방지 시간: **120**

    -   \_artifacts 위치: **기본값 수락**

    -   \_artifacts 위치 SAS 토큰: **기본 vnet1을남겨두십시오**

    -   위에 명시된 사용 약관에 동의함: *사용*

1.  배포에는 약 20분이 소요될 수 있습니다. 배포가 완료될 때까지 기다리지 말고 다음 작업을 진행하세요.

### 작업 5: 점프 호스트 배포

   > **참고**: 이전 작업에서 배포한 Azure VM은 인터넷에서 액세스할 수 없으므로 점프 호스트 역할을 할 Windows Server 2016 Datacenter를 실행하는 Azure VM을 배포합니다. 

1.  랩 컴퓨터의 Azure Portal 인터페이스에서 **+ 리소스 만들기** 를 클릭합니다.

1.  **새로 만들기** 블레이드에서 **Windows Server 2019 Datacenter** 이미지에 따라 새 Azure VM 만들기를 시작합니다.

1.  다음 설정으로 Azure VM을 프로비전합니다.

    -   구독: *Azure 구독의 이름*

    -   리소스 그룹: **az12003b-dmz-RG** *라는 새 리소스 그룹*

    -   가상 머신 이름: **az12003b-vm0**

    -   지역: *이 연습의 이전 작업에서 Azure VM을 배포한 것과 동일한 Azure 지역*

    -   가용성 옵션: **인프라 중복성 불필요**

    -   이미지: **Windows Server 2019 Datacenter**

    -   크기: **표준 D2s v3**

    -   사용자 이름: **Student**

    -   암호: **Pa55w.rd1234**

    -   공용 인바운드 포트: **선택한 포트 허용**

    -   인바운드 포트 선택: **RDP(3389)**

    -   이미 Windows 라이선스가 있습니까? **아니요**

    -   OS 디스크 유형: **표준 HDD**

    -   가상 네트워크: **adVNET**

    -   서브넷: **dmzSubnet (10.0.255.0/24)** *이라는 이름의 새 서브넷*

    -   공용 IP: **az12003b-vm0-ip** *라는 이름의 새 IP 주소*

    -   NIC 네트워크 보안 그룹: **기본**

    -   공용 인바운드 포트: **선택한 포트 허용**

    -   인바운드 포트 선택: **RDP(3389)**

    -   가속화된 네트워킹: **꺼짐**

    -   기존 부하 분산 솔루션 뒤에 이 가상 머신을 배치: **아니요**

    -   부팅 진단: **꺼짐**

    -   OS 게스트 진단: **꺼짐**

    -   시스템 할당 관리 ID: **꺼짐**

    -   AAD 자격 증명으로 로그인(미리 보기): **꺼짐**

    -   자동 종료 사용: **꺼짐**

    -   백업 활성화: **꺼짐**

    -   확장: *없음*

    -   태그: **없음**

1.  프로비전이 완료될 때까지 기다립니다. 이 작업은 몇 분 정도 걸립니다.

> **결과**: 이 연습을 완료한 후 고가용성 SAP NetWeaver 배포를 지원하는 데 필요한 Azure 리소스가 프로비전되었습니다.


## 연습 2: Windows를 실행하는 Azure VM의 운영 체제를 고가용성 SAP NetWeaver 배포를 지원하도록 구성

소요 시간: 60분

이 연습에서는 고가용성 SAP NetWeaver 배포를 수용하도록 Windows Server를 실행하는 Azure VM의 운영 체제를 구성합니다.

### 작업 1: Windows Server 2016 Azure VM을 Active Directory 도메인에 연결합니다.

   > **참고**: 이 태스크를 시작하기 전에 이전 연습에서 시작한 템플릿 배포가 정상적으로 완료되었는지 확인합니다. 

1.  Azure Portal에서 이 랩의 첫 번째 연습에서 자동으로 프로비전된 **adVNET**이라는 이름의 가상 네트워크 블레이드로 이동합니다.

1.  **adVNET - DNS 서버** 블레이드를 표시하고 가상 네트워크가 이 랩의 첫 번째 연습에서 DNS 서버로 도메인 컨트롤러에 할당된 개인 IP 주소로 구성되었는지 확인합니다.

1.  Azure Portal에서 Cloud Shell의 PowerShell 세션을 시작합니다. 

1.  Cloud Shell 창에서 다음 명령을 실행하여 이전 연습의 세 번째 작업에서 배포한 Windows Server Azure VM을 **adatum.com** Active Directory 도메인에 가입합니다.

    ```
    $resourceGroupName = 'az12003b-sap-RG'

    $location = (Get-AzResourceGroup -Name $resourceGroupName).Location

    $settingString = '{"Name": "adatum.com", "User": "adatum.com\\Student", "Restart": "true", "Options": "3"}'

    $protectedSettingString = '{"Password": "Pa55w.rd1234"}'

    $vmNames = @('i20-ascs-0','i20-ascs-1','i20-db-0','i20-db-1','i20-di-0','i20-di-1')

    foreach ($vmName in $vmNames) { Set-AzVMExtension -ResourceGroupName $resourceGroupName -ExtensionType 'JsonADDomainExtension' -Name 'joindomain' -Publisher "Microsoft.Compute" -TypeHandlerVersion "1.0" -Vmname $vmName -Location $location -SettingString $settingString -ProtectedSettingString $protectedSettingString }
    ```

### 작업 2: 데이터베이스 계층 Azure VM의 스토리지 구성을 검사합니다.

1.  랩 컴퓨터에서 Azure Portal의 **az12003b-vm0** 블레이드로 이동합니다.

1.  **az12003b-vm0** 블레이드에서 원격 데스크톱을 통해 Azure VM az12003b-vm0에 연결합니다. 메시지가 표시되면 다음 자격 증명을 입력합니다.

    -   로그인 이름: **student**

    -   암호: **Pa55w.rd1234**

1.  az12003b-vm0에 대한 RDP 세션에서 원격 데스크톱을 사용하여 **i20-db-0.adatum.com** Azure VM에 연결합니다. 메시지가 표시되면 다음 자격 증명을 입력합니다.

    -   로그인 이름: **ADATUM\\Student**

    -   암호: **Pa55w.rd1234**

1.  원격 데스크톱에서 동일한 자격 증명을 사용하여 **i20-db-1.adatum.com** Azure VM에 연결합니다.

1.  i20-db-0.adatum.com으로의 RDP 세션 내에서 서버 관리자의 파일 및 스토리지 서비스를 사용하여 디스크 구성을 검사합니다. 데이터베이스 및 로그 파일용 스토리지를 제공하기 위해 볼륨 탑재를 통해 데이터 디스크 하나가 구성되었습니다. 

1.  i20-db-1.adatum.com으로의 RDP 세션 내에서 서버 관리자의 파일 및 스토리지 서비스를 사용하여 디스크 구성을 검사합니다. 데이터베이스 및 로그 파일용 스토리지를 제공하기 위해 볼륨 탑재를 통해 데이터 디스크 하나가 구성되었습니다. 


### 작업 3: Windows Server 2016을 실행하는 Azure VM에서 고가용성 SAP NetWeaver 설치를 지원하는 장애 조치 클러스터링의 구성을 준비합니다.

1.  i20-db-0.adatum.com에 대한 RDP 세션 내에서 Windows PowerShell ISE 세션을 시작하고 각각 ASCS 및 SQL Server 클러스터의 노드가 될 ASCS 및 DB 서버 쌍에서 다음 명령을 실행하여 장애 조치(failover) 클러스터링 및 원격 관리 도구 기능을 설치합니다.

    ```
    $nodes = @('i20-ascs-0','i20-ascs-1','i20-db-0','i20-db-1')

    Invoke-Command $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools} 

    Invoke-Command $nodes {Install-WindowsFeature RSAT -IncludeAllSubFeature -Restart} 
    ```

    > **참고**: 이 작업을 수행하면 Azure VM 4개에서 모두 게스트 운영 체제가 다시 시작될 수 있습니다.

1.  랩 컴퓨터의 Azure Portal에서 **+ 리소스 만들기** 를 클릭합니다.

1.  **새로 만들기** 블레이드에서 다음 설정으로 새 **스토리지 계정** 만들기를 시작합니다.

    -   구독: *Azure 구독의 이름*

    -   리소스 그룹: **az12003b-sap-RG**

    -   스토리지 계정 이름: *3~24자의 문자와 숫자로 구성된 고유한 이름*

    -   위치: *이전 연습에서 Azure VM을 배포한 것과 동일한 Azure 지역*

    -   성능: **표준**

    -   계정 종류: **스토리지(범용 v1)**

    -   복제: **LRS(로컬 중복 스토리지)**

    -   연결 방법: **공용 엔드포인트(모든 네트워크)**

    -   필요한 보안 전송: **사용**

    -   대용량 파일 공유: **사용 안 함**

    -   Blob 일시 삭제: **사용 안 함**

    -   계층 구조 네임스페이스: **사용 안 함**


### 작업 4: Windows Server 2016을 실행하는 Azure VM에서 SAP NetWeaver 설치의 고가용성 데이터베이스 계층을 지원하는 장애 조치(failover) 클러스터링을 구성합니다.

1.  필요한 경우 az12003b-vm0으로의 RDP 세션에서 원격 데스크톱을 사용하여 **i20-db-0.adatum.com** Azure VM에 다시 연결합니다. 메시지가 표시되면 다음 자격 증명을 입력합니다.

    -   로그인 이름: **ADATUM\\Student**

    -   암호: **Pa55w.rd1234**

1.  i20-db-0.adatum.com에 대한 RDP 세션 내의 서버 관리자에서 **로컬 서버** 보기로 이동하고 **IE 고급 보안 구성**을 끕니다.

1.  i20-db-0.adatum.com에 대한 RDP 세션 내에서 서버 관리자의 **도구** 메뉴에서 **Active Directory 관리 센터**를 시작합니다.

1.  Active Directory 관리 센터에서 adatum.com 도메인의 루트에 **Clusters**라는 새 조직 구성 단위를 만듭니다.

1.  Active Directory 관리 센터에서 i20-db-0 및 i20-db-1의 컴퓨터 계정을 컴퓨터 컨테이너에서 클러스터 조직 구성 단위로 이동합니다.

1.  i20-db-0에 대한 RDP 세션 내에서 Windows PowerShell ISE 세션을 시작하고 다음을 실행하여 새 클러스터를 만듭니다.

    ```
    $nodes = @('i20-db-0','i20-db-1')

    New-Cluster -Name az12003b-db-cl0 -Node $nodes -NoStorage -StaticAddress 10.0.1.6
    ```

1.  i20-db-0.adatum.com에 대한 RDP 세션 내에서 **Active Directory 관리 센터** 콘솔로 전환합니다.

1.  Active Directory 관리 센터에서 **Clusters** 조직 구성 단위로 이동하고 해당 **속성** 창을 표시합니다. 

1.  **Clusters** 조직 구성 단위의 **속성** 창에서 **확장** 섹션으로 이동하여 **보안** 탭을 표시합니다. 

1.  **보안** 탭에서 **고급** 단추를 클릭하여 **클러스터에 대한 고급 보안 설정** 창을 엽니다. 

1.  **컴퓨터에 대한 고급 보안 설정**의 **사용 권한** 탭에서 **추가**를 클릭합니다.

1.  **클러스터에 대한 사용 권한 항목** 창에서 **보안 주체 선택**을 클릭합니다.

1.  **사용자, 서비스 계정 또는 그룹 선택** 대화 상자에서 **개체 유형**을 클릭하고 **컴퓨터** 항목 옆의 확인란을 사용하도록 설정한 다음 **확인**을 클릭합니다. 

1.  다시 **사용자, 컴퓨터, 서비스 계정 또는 그룹 선택** 대화 상자의 **선택할 개체 이름 입력**에서 **az12003b-db-cl0**을 입력하고 **확인**을 클릭합니다.

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
    $resourceGroupName = 'az12003b-sap-RG'

    $cwStorageAccountName = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName)[0].StorageAccountName

    $cwStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $cwStorageAccountName).Value[0]

    Set-ClusterQuorum -CloudWitness -AccountName $cwStorageAccountName -AccessKey $cwStorageAccountKey
    ```

1.  결과 구성을 확인하려면 i20-db-0.adatum.com에 대한 RDP 세션 내에서 서버 관리자의 **도구** 메뉴에서 **장애 조치(failover) 클러스터 관리자** 를 시작합니다.

1.  **장애 조치(failover) 클러스터 관리자** 콘솔에서 **az12003b-db-cl0** 클러스터 구성(노드와 감시 및 네트워크 설정 포함)을 검토합니다. 클러스터에는 공유 스토리지가 없습니다.


### 작업 6: Windows Server 2016을 실행하는 Azure VM에서 SAP NetWeaver 설치의 고가용성 ASCS 계층을 지원하는 장애 조치(failover) 클러스터링을 구성합니다.

> **참고**: 이 작업을 시작하기 전에 연습 1의 작업 4에서 시작한 S2D 클러스터의 배포가 완료되었는지 확인합니다.

1.  az12003b-vm0에 대한 RDP 세션에서 원격 데스크톱을 사용하여 **i20-db-0.ascs.com** Azure VM에 연결합니다. 메시지가 표시되면 다음 자격 증명을 입력합니다.

    -   로그인 이름: **ADATUM\\Student**

    -   암호: **Pa55w.rd1234**

1.  i20-db-0.ascs.com에 대한 RDP 세션 내의 서버 관리자에서 **로컬 서버** 보기로 이동하고 **IE 고급 보안 구성**을 끕니다.

1.  i20-ascs-0.adatum.com에 대한 RDP 세션 내에서 서버 관리자의 **도구** 메뉴에서 **Active Directory 관리 센터**를 시작합니다.

1.  Active Directory 관리 센터에서 **컴퓨터** 컨테이너로 이동합니다. 

1.  Active Directory 관리 센터에서 i20-ascs-0 및 i20-ascs-1의 컴퓨터 계정을 컴퓨터 컨테이너에서 클러스터 조직 구성 단위로 이동합니다.

1.  i20-ascs-0.adatum.com에 대한 RDP 세션 내에서 Windows PowerShell ISE 세션을 시작하고 다음을 실행하여 새 클러스터를 만듭니다.

    ```
    $nodes = @('i20-ascs-0','i20-ascs-1')

    New-Cluster -Name az12003b-ascs-cl0 -Node $nodes -NoStorage -StaticAddress 10.0.1.7
    ```

1.  i20-db-0.ascs.com에 대한 RDP 세션 내에서 **Active Directory 관리 센터** 콘솔로 전환합니다.

1.  Active Directory 관리 센터에서 **Clusters** 조직 구성 단위로 이동하고 해당 **속성** 창을 표시합니다. 

1.  **Clusters** 조직 구성 단위의 **속성** 창에서 **확장** 섹션으로 이동하여 **보안** 탭을 표시합니다. 

1.  **보안** 탭에서 **고급** 단추를 클릭하여 **클러스터에 대한 고급 보안 설정** 창을 엽니다. 

1.  **컴퓨터에 대한 고급 보안 설정**의 **사용 권한** 탭에서 **추가**를 클릭합니다.

1.  **클러스터에 대한 사용 권한 항목** 창에서 **보안 주체 선택**을 클릭합니다.

1.  **사용자, 서비스 계정 또는 그룹 선택** 대화 상자에서 **개체 유형**을 클릭하고 **컴퓨터** 항목 옆의 확인란을 사용하도록 설정한 다음 **확인**을 클릭합니다. 

1.  다시 **사용자, 컴퓨터, 서비스 계정 또는 그룹 선택** 대화 상자의 **선택할 개체 이름 입력**에서 **az12003b-ascs-cl0** 을 입력하고 **확인**을 클릭합니다.

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
    $resourceGroupName = 'az12003b-sap-RG'liveid

    $cwStorageAccountName = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName)[0].StorageAccountName

    $cwStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $cwStorageAccountName).Value[0]

    Set-ClusterQuorum -CloudWitness -AccountName $cwStorageAccountName -AccessKey $cwStorageAccountKey
    ```

1.  결과 구성을 확인하려면 i20-ascs-0.adatum.com에 대한 RDP 세션 내에서 서버 관리자의 **도구** 메뉴에서 **장애 조치(failover) 클러스터 관리자** 를 시작합니다.

1.  **장애 조치(failover) 클러스터 관리자** 콘솔에서 **az12003b-ascs-cl0** 클러스터 구성(노드와 감시 및 네트워크 설정 포함)을 검토합니다. 클러스터에는 공유 스토리지가 없습니다.


### 작업 7: \\\\GLOBALHOST\\sapmnt 공유에 대한 권한 설정

이 작업에서는 **\\\\GLOBALHOST\\sapmnt** 공유에 대한 공유 수준 사용 권한을 설정합니다.

> **참고**: 기본적으로 모든 권한은 ADATUM\Student 계정에만 부여됩니다. 

1.  i20-ascs-0.adatum.com에 대한 원격 데스크톱 세션 내의 **Windows PowerShell ISE** 창에서 다음을 실행합니다.

    ```
    $remoteSession = New-CimSession -ComputerName SAPGLOBALHOST

    Grant-SmbShareAccess -Name sapmnt -AccountName 'ADATUM\Domain Admins' -AccessRight Full -CimSession $remoteSession -Force
   
    ```

### 작업 8: SAP NetWeaver ASCS 및 데이터베이스 구성 요소 설치를 위한 운영 체제 선행 조건 구성

1.  i20-ascs-0.adatum.com에 대한 원격 데스크톱 세션 내의 Windows PowerShell ISE 세션에서 다음을 실행하여 SAP ASCS 구성 요소의 설치 및 가상 이름 사용을 용이하게 하는 데 필요한 레지스트리 항목을 구성합니다.

    ```
    $nodes = ('i20-db-0','i20-db-1')

    Invoke-Command $nodes {
        $registryPath = 'HKLM:\SYSTEM\CurrentControlSet\Services\lanmanworkstation\parameters'
        $registryEntry = 'DisableCARetryOnInitialConnect'
        $registryValue = 1
        New-ItemProperty -Path $registryPath -Name $registryEntry -Value $registryValue -PropertyType DWORD -Force
    }

    Invoke-Command $nodes {
        $registryPath = 'HKLM:\SYSTEM\CurrentControlSet\Control\LSA'
        $registryEntry = 'DisableLoopbackCheck'
        $registryValue = 1
        New-ItemProperty -Path $registryPath -Name $registryEntry -Value $registryValue -PropertyType DWORD -Force
    }

    Invoke-Command $nodes {
        $registryPath = 'HKLM:\SYSTEM\CurrentControlSet\Services\lanmanserver\parameters'
        $registryEntry = 'DisableStrictNameChecking'
        $registryValue = 1
        New-ItemProperty -Path $registryPath -Name $registryEntry -Value $registryValue -PropertyType DWORD -Force
    }
    ```

> **결과**: 이 연습을 완료한 후 고가용성 SAP NetWeaver 배포를 지원하도록 Windows를 실행하는 Azure VM의 운영 체제가 구성되었습니다.


## 연습 3: 랩 리소스 제거

소요 시간: 10분

이 연습에서는 이 랩에서 프로비전한 리소스를 제거합니다.

#### 작업 1: Cloud Shell 열기

1. Portal 상단에서 **Cloud Shell** 아이콘을 클릭하여 Cloud Shell 창을 열고 셸로 PowerShell을 선택합니다.

1. 포털 하단의 **Cloud Shell** 명령 프롬프트에서 다음 명령을 입력하고 **Enter**를 눌러 이 랩에서 만든 모든 리소스 그룹을 나열합니다.

    ```
    Get-AzResourceGroup | Where-Object {$_.ResourceGroupName -like 'az12003b-*'} | Select-Object ResourceGroupName
    ```

1. 이 랩에서 만든 리소스 그룹만 출력에 포함되어 있는지 확인합니다. 다음 태스크에서 이러한 그룹을 삭제합니다.

#### 작업 2: 리소스 그룹 삭제

1. **Cloud Shell** 명령 프롬프트에서 다음 명령을 입력하고 **Enter** 키를 눌러 이 랩에서 만든 리소스 그룹을 삭제합니다.

    ```
    Get-AzResourceGroup | Where-Object {$_.ResourceGroupName -like 'az12003b-*'} | Remove-AzResourceGroup -Force  
    ```

1. 포털 하단의 **Cloud Shell** 프롬프트를  닫습니다.


> **결과**: 이 랩에서 사용한 리소스를 제거하여 이 연습을 완료해야 합니다.
