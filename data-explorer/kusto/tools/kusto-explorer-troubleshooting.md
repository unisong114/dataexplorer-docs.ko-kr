---
title: Kusto 탐색기에서 발생 하는 일반적인 문제 해결
description: Kusto 탐색기 및 솔루션을 설치 하 고 실행 하는 일반적인 문제에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: dd312c4a36bb095dbe4b5e475e758c141bbab6e0
ms.sourcegitcommit: aacea5c4c397479e8254c1fe6ed0b2f333307b14
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86470097"
---
# <a name="troubleshooting"></a>문제 해결

이 문서에서는를 실행 하 고 Kusto 탐색기를 사용 하는 일반적인 문제를 제공 하 고 솔루션을 제공 합니다. 이 문서에서는 [Kusto. 탐색기를 다시 설정 하는 방법](#reset-kustoexplorer)에 대해서도 설명 합니다.

## <a name="kustoexplorer-fails-to-start"></a>Kusto 탐색기 시작 실패

### <a name="kustoexplorer-shows-error-dialog-during-or-after-start-up"></a>시작 하는 동안 또는 이후에 탐색기에서 오류 대화 상자 표시

**증상**

시작 시 Kusto 탐색기에서 오류를 표시 합니다. `InvalidOperationException`

**가능한 해결 방법**

이 오류가 발생 하면 운영 체제가 손상 되거나 일부 필수 모듈이 누락 될 수 있습니다.
누락 되거나 손상 된 시스템 파일을 확인 하려면 여기에 설명 된 단계를 따르세요.   
[https://support.microsoft.com/help/929833/use-the-system-file-checker-tool-to-repair-missing-or-corrupted-system](https://support.microsoft.com/help/929833/use-the-system-file-checker-tool-to-repair-missing-or-corrupted-system)

## <a name="kustoexplorer-always-downloads-even-when-there-are-no-updates"></a>업데이트가 없는 경우에도 kusto 탐색기는 항상를 다운로드 합니다.

**증상**

Kusto. 탐색기를 열 때마다 새 버전을 설치 하 라는 메시지가 표시 됩니다. Kusto 탐색기는 이미 설치 된 버전을 업데이트 하지 않고 전체 패키지를 다운로드 합니다.

**가능한 해결 방법**

이 증상은 로컬 ClickOnce 저장소의 손상으로 인해 발생할 수 있습니다. 관리자 권한 명령 프롬프트에서 다음 명령을 실행 하 여 로컬 ClickOnce 저장소를 지울 수 있습니다.

> [!Important]
> 1. ClickOnce 응용 프로그램의 다른 인스턴스 또는이 있는 경우 `dfsvc.exe` 이 명령을 실행 하기 전에 해당 인스턴스를 종료 합니다.
> 1. 앱 바로 가기에 저장 된 원래 설치 위치에 액세스할 수 있는 경우 모든 ClickOnce 앱은 다음에 실행할 때 자동으로 다시 설치 됩니다. 앱 바로 가기는 삭제 되지 않습니다.

```kusto
rd /q /s %userprofile%\appdata\local\apps\2.0
```

[설치 미러](kusto-explorer.md#installing-kustoexplorer)중 하나에서 Kusto. 탐색기를 다시 설치 해 보세요.

### <a name="clickonce-error-cannot-start-application"></a>ClickOnce 오류: 응용 프로그램을 시작할 수 없습니다.

**증상**  

프로그램이 시작 되지 않고 다음 오류 중 하나가 표시 됩니다. 
* `External component has thrown an exception`
* `Value does not fall within the expected range`
* `The application binding data format is invalid.` 
* `Exception from HRESULT: 0x800736B2`
* `The referenced assembly is not installed on your system. (Exception from HRESULT: 0x800736B3)`

다음 오류 대화 상자를 클릭 하 여 오류 정보를 탐색할 수 있습니다 `Details` .

![ClickOnce 오류](./Images/kusto-explorer-troubleshooting/clickonce-err-1.png)

```kusto
Following errors were detected during this operation.
    * System.ArgumentException
        - Value does not fall within the expected range.
        - Source: System.Deployment
        - Stack trace:
            at System.Deployment.Application.NativeMethods.CorLaunchApplication(UInt32 hostType, String applicationFullName, Int32 manifestPathsCount, String[] manifestPaths, Int32 activationDataCount, String[] activationData, PROCESS_INFORMATION processInformation)
            at System.Deployment.Application.ComponentStore.ActivateApplication(DefinitionAppId appId, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.SubscriptionStore.ActivateApplication(DefinitionAppId appId, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.ApplicationActivator.Activate(DefinitionAppId appId, AssemblyManifest appManifest, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.ApplicationActivator.ProcessOrFollowShortcut(String shortcutFile, String& errorPageUrl, TempFile& deployFile)
            at System.Deployment.Application.ApplicationActivator.PerformDeploymentActivation(Uri activationUri, Boolean isShortcut, String textualSubId, String deploymentProviderUrlFromExtension, BrowserSettings browserSettings, String& errorPageUrl)
            at System.Deployment.Application.ApplicationActivator.ActivateDeploymentWorker(Object state)
```

**제안 된 솔루션 단계**

1. `Programs and Features`()를 사용 하 여 Kusto 탐색기를 제거 합니다. `appwiz.cpl`

1. 실행 `CleanOnlineAppCache` 을 시도한 후 Kusto 탐색기를 다시 설치 해 보세요. 
    관리자 권한 명령 프롬프트에서 다음을 수행 합니다. 
    
    ```kusto
    rundll32 %windir%\system32\dfshim.dll CleanOnlineAppCache
    ```

    [설치 미러](kusto-explorer.md#installing-kustoexplorer)중 하나에서 Kusto 탐색기를 다시 설치 합니다.

1. 응용 프로그램이 아직 시작 되지 않으면 로컬 ClickOnce 저장소를 삭제 합니다. 앱 바로 가기에 저장 된 원래 설치 위치에 액세스할 수 있는 경우 모든 ClickOnce 앱은 다음에 실행할 때 자동으로 다시 설치 됩니다. 앱 바로 가기는 삭제 되지 않습니다.

    관리자 권한 명령 프롬프트에서 다음을 수행 합니다.

    ```kusto
    rd /q /s %userprofile%\appdata\local\apps\2.0
    ```

    [설치 미러](kusto-explorer.md#installing-kustoexplorer) 중 하나에서 다시 Kusto 탐색기를 설치 합니다.

1. 응용 프로그램이 여전히 시작 되지 않는 경우:
    1. 임시 배포 파일을 제거 합니다.
    1. Kusto. Explorer local AppData 폴더의 이름을 바꿉니다.

        관리자 권한 명령 프롬프트에서 다음을 수행 합니다.

        ```kusto
        rd /s/q %userprofile%\AppData\Local\Temp\Deployment
        ren %LOCALAPPDATA%\Kusto.Explorer Kusto.Explorer.bak
        ```

    1. [설치 미러](kusto-explorer.md#installing-kustoexplorer) 중 하나에서 다시 Kusto 탐색기를 설치 합니다.

    1. 관리자 권한 명령 프롬프트에서 Kusto. Explorer.exe에서 연결을 복원 하려면 다음을 수행 합니다.

        ```kusto
        copy %LOCALAPPDATA%\Kusto.Explorer.bak\User*.xml %LOCALAPPDATA%\Kusto.Explorer
        ```

1. 응용 프로그램이 여전히 시작 되지 않는 경우:
    1. 아래에서 LogVerbosityLevel 문자열 값 1을 만들어 자세한 ClickOnce 로깅을 사용 하도록 설정 합니다.

        ```kusto
        HKEY_CURRENT_USER\Software\Classes\Software\Microsoft\Windows\CurrentVersion\Deployment
        ```
    
    1. 다시 재현 합니다.
    1. 자세한 정보 출력을로 보냅니다 KEBugReport@microsoft.com . 

### <a name="clickonce-error-your-administrator-has-blocked-this-application-because-it-potentially-poses-a-security-risk-to-your-computer"></a>ClickOnce 오류: 컴퓨터에 보안 위험이 발생할 수 있으므로 관리자가이 응용 프로그램을 차단 했습니다.

**증상**  
다음 오류 중 하나를 사용 하 여 응용 프로그램을 설치 하지 못했습니다.
* `Your administrator has blocked this application because it potentially poses a security risk to your computer`.
* `Your security settings do not allow this application to be installed on your computer.`

**해결 방법**

이 증상은 다른 응용 프로그램에서 기본 ClickOnce 신뢰 프롬프트 동작을 재정의 하기 때문에 발생할 수 있습니다. 
1. 기본 구성 설정을 확인 합니다.
1. 구성 설정을 컴퓨터의 실제 구성과 비교 합니다.
1. [이 방법 문서에서](https://docs.microsoft.com/visualstudio/deployment/how-to-configure-the-clickonce-trust-prompt-behavior)설명 하는 대로 필요에 따라 구성 설정을 다시 설정 합니다.

### <a name="cleanup-application-data"></a>응용 프로그램 데이터 정리

경우에 따라 이전 문제 해결 단계를 시작 하는 데 도움이 되지 않는 경우 로컬에 저장 된 데이터를 정리 하는 것이 도움이 될 수 있습니다.

Kusto. 탐색기 응용 프로그램에서 저장 한 데이터는에서 찾을 수 `C:\Users\[your username]\AppData\Local\Kusto.Explorer` 있습니다.

> [!NOTE]
> 데이터를 정리 하면 열린 탭 (복구 폴더), 저장 된 연결 (연결 폴더) 및 응용 프로그램 설정 (UserSettings 폴더)이 손실 됩니다.

## <a name="reset-kustoexplorer"></a>Kusto 탐색기 다시 설정

필요한 경우 Kusto. 탐색기를 완전히 다시 설정할 수 있습니다. 다음 절차에서는 컴퓨터에서 완전히 제거 될 때까지 Kusto 탐색기나를 점진적으로 다시 설정 하 여 처음부터 설치 해야 하는 방법을 설명 합니다.

1. Windows에서 **프로그램 변경 또는 제거** ( **프로그램 및 기능이**라고도 함)를 엽니다.
1. 로 시작 하는 모든 항목을 선택 `Kusto.Explorer` 합니다.
1. **제거**를 선택합니다.

   이 절차에서 응용 프로그램을 제거 하지 못한 경우 (ClickOnce 응용 프로그램의 알려진 문제) 자세한 내용은 [이 문서](https://stackoverflow.com/questions/10896223/how-do-i-completely-uninstall-a-clickonce-application-from-my-computer)를 참조 하세요.

1. `%LOCALAPPDATA%\Kusto.Explorer`모든 연결, 기록 등을 제거 하는 폴더를 삭제 합니다.

1. 폴더를 삭제 합니다 `%APPDATA%\Kusto` . 이렇게 하면 Kusto. 탐색기 토큰 캐시가 제거 됩니다. 모든 클러스터로 다시 인증 해야 합니다.

또한 특정 버전의 Kusto. 탐색기로 되돌릴 수 있습니다.

1. `appwiz.cpl`을 실행합니다.
1. **Kusto 탐색기** 를 선택 하 고 **제거/변경**을 선택 합니다.
1. **응용 프로그램을 이전 상태로 복원을**선택 합니다.

## <a name="next-steps"></a>다음 단계

* [Kusto 탐색기 사용자 인터페이스](kusto-explorer.md#overview-of-the-user-interface) 에 대 한 자세한 정보
* [명령줄에서 .Kusto 탐색기를 실행 하는](kusto-explorer-using.md#kustoexplorer-command-line-arguments) 방법을 알아봅니다.
* [Kusto 쿼리 언어에 대해 알아보기 (KQL)](https://docs.microsoft.com/azure/kusto/query/)
