
# AZ 120: 랩 전제 조건

## vCPU 코어 요구 사항

-   이 과정의 마지막 랩을 완료하려면 이 랩에 배포된 Azure VM이 상주할 가용성 영역을 지원하는 Azure 지역에서 vCPU 28개 이상이 제공되는 Microsoft Azure 구독이 필요합니다.

    -   Standard_DS1_v2(각각 vCPU 1개) x 4 = 4

    -   6 x Standard_D4s_v3(각각 vCPU 4개) = 24

    > **참고**: 리소스 배포에는 **East US** 또는 **East US2** 지역 사용을 고려합니다.

    > **참고**: 가용성 영역을 지원하는 Azure 지역을 확인하려면 [https://docs.microsoft.com/ko-kr/azure/availability-zones/az-overview]<https://docs.microsoft.com/ko-kr/azure/availability-zones/az-overview>를 참조하십시오.

이 과정의 처음 3개 랩에서는 vCPU 요구 사항을 쉽게 충족할 수 있지만, 모든 랩의 요구 사항을 충족하도록 할당량 증가를 요청하는 것이 좋습니다. 할당량 증가 요청 자체는 대개 당일(영업일 기준)에 완료되지만, 할당량 증가 프로세스는 시간이 다소 걸리기 때문입니다.

## 실습 랩을 시작하기 전에

소요 시간: 120분

### 작업 1: 충분한 수의 vCPU 코어가 있는지 확인

1.  Azure Portal(<http://portal.azure.com>) 

1.  Azure Portal의 Cloud Shell에서 PowerShell 세션을 시작합니다. 

    > **참고**: 현재 Azure 구독에서 Cloud Shell을 처음 시작하는 경우 Cloud Shell 파일을 보관할 Azure 파일 공유를 만들라는 메시지가 표시됩니다. 이 경우 기본값을 적용하면 자동으로 생성된 리소스 그룹에 스토리지 계정이 만들어집니다.

1.  Azure Portal의 **Cloud Shell** 창 내 PowerShell 프롬프트에서 다음 명령을 실행합니다. 여기서 `<Azure_region>`은 이 랩에 사용할 대상 Azure 지역을 지정합니다(예: `eastus`):

    ```powershell
    Get-AzVMUsage -Location '<Azure_region>' | Where-Object {$_.Name.Value -eq 'StandardDSv3Family'}

    Get-AzVMUsage -Location '<Azure_region>' | Where-Object {$_.Name.Value -eq 'StandardDSv2Family'}
    ``` 

    > **참고**: Azure 지역의 이름을 식별하려면 **Cloud Shell**의 Bash 프롬프트에서 `(Get-AzLocation).Location`을 실행합니다.
   
1.  이전 단계에서 실행한 명령 출력의 현재 값과 제한 항목을 검토하고 대상 Azure 지역에 충분한 수의 vCPU가 있는지 확인합니다.

1.  vCPU 수가 충분하지 않은 경우 Azure Portal에서 구독 블레이드로 다시 이동한 다음 **사용량 + 할당량**을 클릭합니다. 

1.  구독의 **사용량 + 할당량** 블레이드에서 **증가 요청**을 클릭합니다.

1.  **기본** 블레이드에서 다음을 지정하고 **다음**을 클릭합니다.

    -   문제 유형: **서비스 및 구독 제한(할당량)**

    -   구독: 이 랩에서 사용할 Azure 구독의 이름

    -   할당량 유형: **컴퓨팅/VM(코어/vCPU) 구독 제한 증가**

1.  **세부 정보** 블레이드에서 **세부 정보 입력** 블레이드를 클릭합니다. 

1.  **할당량 세부 정보** 블레이드에서 다음을 지정하고 **저장 및 계속**을 클릭합니다.

    -   배포 모델: **Resource Manager**

    -   위치: 이 랩에서 사용하려는 대상 Azure 지역

    -   SKU 제품군: **DSv3 시리즈** 및 **DSv2 시리즈**

1.  **세부 정보** 블레이드에서 각 SKU 시리즈에 대한 새 제한을 지정하고 **다음**을 클릭합니다. **검토 + 만들기**:

    -   심각도: **C - 최소 영향**

    -   기본 연락 방법: 원하는 옵션을 선택하고 연락처 세부 정보 입력

1.  **검토 + 만들기** 블레이드에서 **만들기**를 클릭합니다.

   > **참고**: 할당량 증가 요청은 일반적으로 당일(영업일 기준) 중에 완료됩니다.