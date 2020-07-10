---
title: PowerShell에서 kusto .NET 클라이언트 라이브러리-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 PowerShell에서 .NET 클라이언트 라이브러리를 사용 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/29/2019
ms.openlocfilehash: 6804b71ff3985de17460dddfa60f081f3bb910c0
ms.sourcegitcommit: b286703209f1b657ac3d81b01686940f58e5e145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86188424"
---
# <a name="using-the-net-client-libraries-from-powershell"></a>PowerShell에서 .NET 클라이언트 라이브러리 사용

Powershell 스크립트는 powershell이 아닌 임의의 (PowerShell) .NET 라이브러리와의 기본 제공 통합을 통해 Azure 데이터 탐색기 .NET 클라이언트 라이브러리를 사용할 수 있습니다.

## <a name="getting-the-net-client-libraries-for-scripting-with-powershell"></a>PowerShell을 사용 하 여 스크립팅을 위한 .NET 클라이언트 라이브러리 가져오기

PowerShell을 사용 하 여 Azure 데이터 탐색기 .NET 클라이언트 라이브러리 작업을 시작 합니다.

1. [ `Microsoft.Azure.Kusto.Tools` NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)를 다운로드 합니다.
    * Powershell 7 이상을 사용 하는 경우 [ `Microsoft.Azure.Kusto.Tools.NETCore` NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools.NETCore/)를 다운로드 합니다.
1. 패키지에서 ' tools ' 디렉터리의 콘텐츠를 추출 합니다 (와 같은 보관 도구 사용 `7-zip` ).
1. `[System.Reflection.Assembly]::LoadFrom("path")`PowerShell에서를 호출 하 여 필요한 라이브러리를 로드 합니다. 
    * `path`명령의 매개 변수는 추출 된 파일의 위치를 나타내야 합니다.
1. 모든 종속 .NET 어셈블리가 로드 되 면 다음을 수행 합니다.
   1. Kusto 연결 문자열을 만듭니다.
   1. *쿼리 공급자나* *관리 공급자*를 인스턴스화합니다.
   1. 아래 [예제](powershell.md#examples) 와 같이 쿼리 또는 명령을 실행 합니다.

자세한 내용은 [Azure 데이터 탐색기 클라이언트 라이브러리](../netfx/about-kusto-data.md)를 참조 하세요.

## <a name="examples"></a>예

### <a name="initialization"></a>초기화

```powershell
#  Part 1 of 3
#  ------------
#  Packages location - This is an example of the location from where you extract the Microsoft.Azure.Kusto.Tools package.
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

#   Option A: using Azure AD User Authentication
$kcsb = New-Object Kusto.Data.KustoConnectionStringBuilder ($clusterUrl, $databaseName)
 
#   Option B: using Azure AD application Authentication
#     $applicationId = "application ID goes here"
#     $applicationKey = "application key goes here"
#     $authority = "authority goes here"
#     $kcsb = $kcsb.WithAadApplicationKeyAuthentication($applicationId, $applicationKey, $authority)
#
#   NOTE: if you're running with Powershell 7 (or above) and the .NET Core library,
#         AAD user authentication with prompt will not work, and you should choose
#         a different authentication method.
```

### <a name="example-running-an-admin-command"></a>예: 관리 명령 실행

```powershell
$adminProvider = [Kusto.Data.Net.Client.KustoClientFactory]::CreateCslAdminProvider($kcsb)
$command = [Kusto.Data.Common.CslCommandGenerator]::GenerateDiagnosticsShowCommand()
Write-Host "Executing command: '$command' with connection string: '$($kcsb.ToString())'"
$reader = $adminProvider.ExecuteControlCommand($command)
$reader.Read() # this reads a single row/record. If you have multiple ones returned, you can read in a loop 
$isHealthy = $Reader.GetBoolean(0)
Write-Host "IsHealthy = $isHealthy"
```

출력은 다음과 같습니다.
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

출력은 다음과 같습니다.

|StartTime           |EndTime             |EpisodeID |EventID |시스템 상태          |EventType         |InjuriesDirect |InjuriesIndirect |DeathsDirect |DeathsIndirect
|---------           |-------             |--------- |------- |-----          |---------         |-------------- |---------------- |------------ |--------------
|2007-12-30 16:00:00 |2007-12-30 16:05:00 |    11749 |  64588 |그루지야        |뇌우를 동반한 바람 |             0 |               0 |           0 |             0
|2007-12-20 07:50:00 |2007-12-20 07:53:00 |    12554 |  68796 |MISSISSIPPI    |뇌우를 동반한 바람 |             0 |               0 |           0 |             0
|2007-09-29 08:11:00 |2007-09-29 08:11:00 |    11091 |  61032 |대서양 남부 |워터 spout       |             0 |               0 |           0 |             0
|2007-09-20 21:57:00 |2007-09-20 22:05:00 |    11078 |  60913 |플로리다        |토네이도           |             0 |               0 |           0 |             0
|2007-09-18 20:00:00 |2007-09-19 18:00:00 |    11074 |  60904 |플로리다        |과도 한 비        |             0 |               0 |           0 |             0
