---
title: PowerShell에서 .NET 클라이언트 라이브러리 사용 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 PowerShell에서 .NET 클라이언트 라이브러리를 사용하는 것을 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/29/2019
ms.openlocfilehash: 635a23021a1a8c30347bfa27ecd65886b46a6fea
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503216"
---
# <a name="using-the-net-client-libraries-from-powershell"></a>PowerShell에서 .NET 클라이언트 라이브러리 사용

Azure 데이터 탐색기 .NET 클라이언트 라이브러리는 PowerShell의 기본 제공 통합을 통해 PowerShell 스크립트에서 임의(비 PowerShell) .NET 라이브러리와 사용할 수 있습니다.

## <a name="getting-the-net-client-libraries-for-scripting-with-powershell"></a>PowerShell을 통해 스크립팅을 위한 .NET 클라이언트 라이브러리 받기

PowerShell을 사용하여 Azure 데이터 탐색기 .NET 클라이언트 라이브러리로 작업을 시작하려면 다음을 수행하십시오.

1. [ `Microsoft.Azure.Kusto.Tools` NuGet 패키지를](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)다운로드합니다.
2. 패키지에서 '도구' 디렉토리의 내용을 추출합니다(예: 7-zip 사용).
3. 필요한 `[System.Reflection.Assembly]::LoadFrom("path")` 라이브러리를 로드하려면 powershell에서 호출합니다. 
    - 명령에 `"path"` 대한 매개 변수는 추출된 파일의 위치를 나타내야 합니다.
4. 모든 종속 .NET 어셈블리가 로드되면 Kusto 연결 문자열을 만들고 *쿼리 공급자* 또는 *관리자 공급자를*인스턴스화하고 쿼리 또는 명령을 실행합니다(아래 [예제와](powershell.md#examples) 같이).

자세한 내용은 [Azure 데이터 탐색기 클라이언트 라이브러리를](../netfx/about-kusto-data.md)참조하십시오.

## <a name="examples"></a>예

### <a name="initialization"></a>초기화

```powershell
#  Part 1 of 3
#  ------------
#  Packages location - This is an example to the location where you extract the Microsoft.Azure.Kusto.Tools package.
#  Please make sure you load the types from a local directory and not from a remote share.
$packagesRoot = "C:\Microsoft.Azure.Kusto.Tools\Tools"

#  Part 2 of 3
#  ------------
#  Loading the Kusto.Client library and its dependencies
dir $packagesRoot\* | Unblock-File
[System.Reflection.Assembly]::LoadFrom("$packagesRoot\Kusto.Data.dll")

#  Part 3 of 3
#  ------------
#  Defining the connection to your cluster / database
$clusterUrl = "https://help.kusto.windows.net;Fed=True"
$databaseName = "Samples"

#   Option A: using AAD User Authentication
$kcsb = New-Object Kusto.Data.KustoConnectionStringBuilder ($clusterUrl, $databaseName)
 
#   Option B: using AAD application Authentication
#     $applicationId = "application ID goes here"
#     $applicationKey = "application key goes here"
#     $authority = "authority goes here"
#     $kcsb = $kcsb.WithAadApplicationKeyAuthentication($applicationId, $applicationKey, $authority)
```

### <a name="example-running-an-admin-command"></a>예: 관리자 명령 실행

```powershell
$adminProvider = [Kusto.Data.Net.Client.KustoClientFactory]::CreateCslAdminProvider($kcsb)
$command = [Kusto.Data.Common.CslCommandGenerator]::GenerateDiagnosticsShowCommand()
Write-Host "Executing command: '$command' with connection string: '$($kcsb.ToString())'"
$reader = $adminProvider.ExecuteControlCommand($command)
$reader.Read() # this reads a single row/record. If you have multiple ones returned, you can read in a loop 
$isHealthy = $Reader.GetBoolean(0)
Write-Host "IsHealthy = $isHealthy"
```

그리고 출력은 다음과 같은 것입니다.
```
IsHealthy = True
```

### <a name="example-running-a-query"></a>예: 쿼리 실행

```powershell
$queryProvider = [Kusto.Data.Net.Client.KustoClientFactory]::CreateCslQueryProvider($kcsb)
$query = "StormEvents | limit 5"
Write-Host "Executing query: '$query' with connection string: '$($kcsb.ToString())'"
#   Optional: set a client request ID and set a client request property (e.g. Server Timeout)
$crp = New-Object Kusto.Data.Common.ClientRequestProperties
$crp.ClientRequestId = "MyPowershellScript.ExecuteQuery." + [Guid]::NewGuid().ToString()
$crp.SetOption([Kusto.Data.Common.ClientRequestProperties]::OptionServerTimeout, [TimeSpan]::FromSeconds(30))

#   Execute the query
$reader = $queryProvider.ExecuteQuery($query, $crp)

# Do something with the result datatable, for example: print it formatted as a table, sorted by the 
# "StartTime" column, in descending order
$dataTable = [Kusto.Cloud.Platform.Data.ExtendedDataReader]::ToDataSet($reader).Tables[0]
$dataView = New-Object System.Data.DataView($dataTable)
$dataView | Sort StartTime -Descending | Format-Table -AutoSize
```

그리고 출력은 다음과 같은 것입니다.

|StartTime           |EndTime             |에피소드 ID |EventId |시스템 상태          |EventType         |부상직접 |부상 간접 |죽음직접 |사망간접
|---------           |-------             |--------- |------- |-----          |---------         |-------------- |---------------- |------------ |--------------
|2007-12-30 16:00:00 |2007-12-30 16:05:00 |    11749 |  64588 |그루지야        |뇌우를 동반한 바람 |             0 |               0 |           0 |             0
|2007-12-20 07:50:00 |2007-12-20 07:53:00 |    12554 |  68796 |미시시피    |뇌우를 동반한 바람 |             0 |               0 |           0 |             0
|2007-09-29 08:11:00 |2007-09-29 08:11:00 |    11091 |  61032 |애틀랜틱 사우스 |워터스푸드        |             0 |               0 |           0 |             0
|2007-09-20 21:57:00 |2007-09-20 22:05:00 |    11078 |  60913 |플로리다        |토네이도           |             0 |               0 |           0 |             0
|2007-09-18 20:00:00 |2007-09-19 18:00:00 |    11074 |  60904 |플로리다        |호우        |             0 |               0 |           0 |             0