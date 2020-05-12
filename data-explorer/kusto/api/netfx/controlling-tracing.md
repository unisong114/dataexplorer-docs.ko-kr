---
title: Kusto SDK 클라이언트 쪽 추적 제어 및 억제-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 Kusto SDK 클라이언트 쪽 추적을 제어 하 고 억제 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 10/23/2018
ms.openlocfilehash: 159036bbbe6e0415f56b36827b1913cba90fb705
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226179"
---
# <a name="controlling-and-suppressing-kusto-sdk-client-side-tracing"></a>Kusto SDK 클라이언트 쪽 추적 제어 및 억제

Kusto 클라이언트 라이브러리는 추적에 공통 플랫폼을 사용 합니다. 플랫폼은 많은 수의 추적 소스 ( `System.Diagnostics.TraceSource` )를 사용 하며 각는 생성 중에 추적 수신기 ()의 기본 집합에 연결 됩니다 `System.Diagnostics.Trace.Listeners` .

응용 프로그램에 기본 인스턴스와 연결 된 추적 수신기가 있는 경우 `System.Diagnostics.Trace` (예: 파일을 통해 `app.config` ), Kusto 클라이언트 라이브러리는 해당 수신기에 추적을 내보냅니다.

추적을 프로그래밍 방식으로 또는 구성 파일을 통해 표시 하거나 숨길 수 있습니다.

## <a name="suppress-tracing-programmatically"></a>프로그래밍 방식으로 추적 표시 안 함

Kusto 클라이언트 라이브러리에서 프로그래밍 방식으로 추적 하지 않도록 하려면 관련 라이브러리를 로드할 때이 코드 조각을 호출 합니다.

```csharp
Kusto.Cloud.Platform.Utils.TraceSourceManager.SetTraceVerbosityForAll(
    Kusto.Cloud.Platform.Utils.TraceVerbosity.Fatal
    );
```

## <a name="use-a-config-file-to-suppress-tracing"></a>구성 파일을 사용 하 여 추적 표시 안 함 

구성 파일을 통해 Kusto client 라이브러리에서 추적을 표시 하지 않으려면 파일 `Kusto.Cloud.Platform.dll.tweaks` (라이브러리에 포함 됨)을 수정 `Kusto.Data` 합니다.

```xml
    <!-- Overrides the default trace verbosity level -->
    <add key="Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel" value="0" />
```

> [!NOTE]
> 조정에 적용 하려면 값에 빼기 기호를 사용 해야 합니다.`key`

또 다른 방법은 다음과 같습니다.

```csharp
Kusto.Cloud.Platform.Utils.Anchor.Tweaks.SetProgrammaticAppSwitch(
    "Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel",
    "0"
    );
```

## <a name="enable-the-kusto-client-libraries-tracing"></a>Kusto 클라이언트 라이브러리 추적 사용

Kusto 클라이언트 라이브러리에서 추적을 사용 하도록 설정 하려면 응용 프로그램의 *app.config 파일*에서 .net 추적을 사용 하도록 설정 합니다. 예를 들어 응용 프로그램에서 `MyApp.exe` Kusto. Data 클라이언트 라이브러리를 사용 한다고 가정 합니다. 다음을 포함 하도록 *myapp.exe* 파일을 변경 하면 다음에 응용 프로그램을 시작할 때 추적을 사용할 수 있습니다 `Kusto.Data` .

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.diagnostics>
    <trace indentsize="4">
      <listeners>
        <add type="Kusto.Cloud.Platform.Utils.RollingCsvTraceListener2, Kusto.Cloud.Platform" name="RollingCsvTraceListener" initializeData="RollingLogs" />
        <remove name="Default" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
```

이 코드는 *RollingLogs*라는 하위 디렉터리의 CSV 파일에 쓰는 추적 수신기를 구성 합니다. 하위 디렉터리는 프로세스의 디렉터리에 있습니다.

> [!NOTE]
> 일부. NET 호환 추적 수신기 클래스도 사용할 수 있습니다.

## <a name="enable-the-azure-ad-client-libraries-adal-tracing"></a>ADAL (Azure AD 클라이언트 라이브러리) 추적을 사용 하도록 설정

Kusto 클라이언트 라이브러리에 대 한 추적이 사용 하도록 설정 된 후에는 Azure AD 클라이언트 라이브러리에의 한 추적입니다. Kusto 클라이언트 라이브러리는 ADAL 추적을 자동으로 구성 합니다.
