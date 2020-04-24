---
title: Kusto SDK 클라이언트 측 추적 제어 또는 억제 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 Kusto SDK 클라이언트 측면 추적제어 또는 억제에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 65964d7e990cdb639bd5bfe319d11874ea3de15d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502740"
---
# <a name="controlling-or-suppressing-kusto-sdk-client-side-tracing"></a>Kusto SDK 클라이언트 측 추적 제어 또는 억제

Kusto 클라이언트 라이브러리는 추적을 위한 공통 플랫폼을 사용합니다. 이 플랫폼은 생성 하는 동안`System.Diagnostics.TraceSource`추적 수신기 ()`System.Diagnostics.Trace.Listeners`의 기본 집합에 연결 된 많은 수의 추적 소스 () 를 사용 합니다.

이 것의 한 가지 의미는 응용 프로그램에 기본 `System.Diagnostics.Trace` 인스턴스와 관련 된 `app.config` 추적 리스너 (예를 들어, 해당 파일을 통해) 다음 Kusto 클라이언트 라이브러리 해당 수신기에 추적을 내보전.

이 동작은 프로그래밍 방식으로 또는 구성 파일을 통해 억제하거나 제어할 수 있습니다.

## <a name="suppress-tracing-programmatically"></a>프로그래밍 방식으로 추적 억제

Kusto 클라이언트 라이브러리에서 프로그래밍 방식으로 추적을 억제하려면 관련 라이브러리를 로드할 때 다음 코드를 초기에 호출합니다.

```csharp
Kusto.Cloud.Platform.Utils.TraceSourceManager.SetTraceVerbosityForAll(
    Kusto.Cloud.Platform.Utils.TraceVerbosity.Fatal
    );
```

## <a name="suppressing-tracing-by-using-a-config-file"></a>구성 파일을 사용하여 추적 억제

구성 파일을 통해 Kusto 클라이언트 라이브러리에서 추적을 억제하려면 `Kusto.Cloud.Platform.dll.tweaks` 적절한 "비틀기"가 읽도록 `Kusto.Data` 파일(라이브러리에 포함된)을 수정합니다.

```xml
    <!-- Overrides the default trace verbosity level -->
    <add key="Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel" value="0" />
```

(비틀기를 적용 하려면 .) `key`값에 마이너스 기호가 필요 합니다.)

다른 방법은 다음을 수행하는 것입니다.

```csharp
Kusto.Cloud.Platform.Utils.Anchor.Tweaks.SetProgrammaticAppSwitch(
    "Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel",
    "0"
    );
```

## <a name="how-to-enable-the-kusto-client-libraries-tracing"></a>Kusto 클라이언트 라이브러리 추적을 활성화하는 방법

Kusto 클라이언트 라이브러리에서 추적을 활성화하려면 응용 프로그램의 app.config 파일에서 .NET 추적을 사용하도록 설정합니다. 예를 들어 응용 프로그램이 `MyApp.exe` Kusto.Data 클라이언트 라이브러리를 사용하고 있다고 가정합니다. 그런 다음 `MyApp.exe.config` 다음을 포함하도록 파일을 변경하면 다음에 응용 프로그램이 시작될 때 Kusto.Data 추적을 사용할 수 있습니다.

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

이렇게 하면 프로세스의 디렉터리에 있는 하위 디렉터리에서 `RollingLogs` CSV 파일에 기록하는 추적 수신기가 구성됩니다. (물론, 어떤 . NET 호환 추적 수신기 클래스도 사용할 수 있습니다. 

## <a name="how-to-enable-the-aad-client-libraries-adal-tracing"></a>AAD 클라이언트 라이브러리(ADAL) 추적을 활성화하는 방법

Kusto 클라이언트 라이브러리에 대한 추적이 활성화되면 AAD 클라이언트 라이브러리에서 내보내는 추적도 마찬가지입니다(Kusto 클라이언트 라이브러리는 자동으로 ADAL 추적을 구성함).

