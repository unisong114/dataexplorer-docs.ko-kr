---
title: Kusto 클라이언트 라이브러리 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 Kusto 클라이언트 라이브러리에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/15/2020
ms.openlocfilehash: 8a3ab9bb3727efdc80e1887ab802f2ad4e3c7cce
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502604"
---
# <a name="kusto-client-library"></a>쿠스토 클라이언트 라이브러리
    
Kusto 클라이언트 SDK(Kusto.Data)는 ADO.NET 유사한 프로그래밍 방식 API를 노출하므로 .NET을 경험한 사용자도 자연스럽게 사용할 수 있습니다. Kusto 엔진 서비스, 데이터베이스, 인증 방법 등을 가리키는 연결 문자열 개체에서 쿼리 클라이언트 ()`ICslQueryProvider`또는 제어 명령 공급자 ()`ICslAdminProvider`중 하나를 만듭니다. 그런 다음 적절한 Kusto 쿼리 언어 문자열을 지정하여 데이터 쿼리 또는 제어 명령을 발급하고 반환된 `IDataReader` 개체를 통해 하나 이상의 데이터 테이블을 다시 얻을 수 있습니다.

보다 구체적으로 Kusto에 대한 쿼리를 허용하는 ADO.NET 같은 클라이언트를 만들려면 클래스에서 정적 메서드를 `Kusto.Data.Net.Client.KustoClientFactory` 사용합니다. 이러한 연결 문자열을 가져 와서 스레드 안전, 일회용 클라이언트 개체를 만듭니다. 이 개체의 "너무 많은" 인스턴스를 만들지 않는 것이 좋습니다. 대신 클라이언트 코드는 연결 문자열당 개체를 만들고 필요한 기간 동안 개체를 유지해야 합니다.) 이렇게 하면 클라이언트 개체가 리소스를 효율적으로 캐시할 수 있습니다.

일반적으로 클라이언트의 모든 메서드는 두 가지 예외를 `Dispose`제외하고 스레드에서 안전합니다. 일관된 결과를 얻으려면 두 메서드를 동시에 호출하지 마십시오.

다음은 몇 가지 예제입니다. 추가 샘플은 [여기에서](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client)찾을 수 있습니다.

**예: 행 계산**
 
다음 코드는 다음 데이터베이스에서 명명된 `StormEvents` 테이블의 `Samples`행을 계산하는 것을 보여 줍니다.

```csharp
var client = Kusto.Data.Net.Client.KustoClientFactory.CreateCslQueryProvider("https://help.kusto.windows.net/Samples;Fed=true");
var reader = client.ExecuteQuery("StormEvents | count");
// Read the first row from reader -- it's 0'th column is the count of records in MyTable
// Don't forget to dispose of reader when done.
```

**예: Kusto 클러스터에서 진단 정보 얻기**

```csharp
var kcsb = new KustoConnectionStringBuilder(cluster URI here). WithAadUserPromptAuthentication();
using (var client = KustoClientFactory.CreateCslAdminProvider(kcsb))
{
    var diagnosticsCommand = CslCommandGenerator.GenerateShowDiagnosticsCommand();
    var objectReader = new ObjectReader<DiagnosticsShowCommandResult>(client.ExecuteControlCommand(diagnosticsCommand));
    DiagnosticsShowCommandResult diagResult = objectReader.ToList().FirstOrDefault();
    // DO something with the diagResult    
}
```



## <a name="the-kustoclientfactory-client-factory"></a>쿠스토클라이언트팩토리 클라이언트 팩토리

정적 클래스는 `Kusto.Data.Net.Client.KustoClientFactory` Kusto를 사용하는 클라이언트 코드 작성자의 기본 진입점을 제공합니다. 다음과 같은 중요한 정적 메서드를 제공합니다.

|방법                                      |반환                                |사용 대상                                                      |
|--------------------------------------------|---------------------------------------|--------------------------------------------------------------|
|`CreateCslQueryProvider`                    |`ICslQueryProvider`                    |Kusto 엔진 클러스터로 쿼리 보내기                    |
|`CreateCslAdminProvider`                    |`ICslAdminProvider`                    |모든 종류의 Kusto 클러스터로 제어 명령을 전송합니다.    |
|`CreateRedirectProvider`                    |`IRedirectProvider`                    |Kusto 요청에 대한 리디렉션 HTTP 응답 메시지 만들기|

