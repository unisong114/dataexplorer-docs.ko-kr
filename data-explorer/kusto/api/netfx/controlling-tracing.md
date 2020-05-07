---
title: Kusto SDK 클라이언트 쪽 추적 제어 또는 억제-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 Kusto SDK 클라이언트 쪽 추적을 제어 하거나 억제 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 10/23/2018
ms.openlocfilehash: cbda69063e3b1a20549dbadb4641fc9fd3f51f57
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82862057"
---
# <a name="controlling-or-suppressing-kusto-sdk-client-side-tracing"></a>Kusto SDK 클라이언트 쪽 추적 제어 또는 표시 안 함

Kusto 클라이언트 라이브러리는 추적에 공통 플랫폼을 사용 합니다. 플랫폼은 생성 중에 추적 수신기 (`System.Diagnostics.TraceSource``System.Diagnostics.Trace.Listeners`)의 기본 집합에 연결 된 많은 수의 추적 소스 ()를 사용 합니다.

이에 대 한 한 가지 의미는 응용 프로그램에 기본 `System.Diagnostics.Trace` 인스턴스와 연결 된 추적 수신기가 있는 경우 (예: `app.config` 파일을 통해), Kusto 클라이언트 라이브러리는 해당 수신기에 추적을 내보냅니다.

이 동작은 프로그래밍 방식으로 또는 구성 파일을 통해 억제 되거나 제어 될 수 있습니다.

## <a name="suppress-tracing-programmatically"></a>프로그래밍 방식으로 추적 표시 안 함

Kusto 클라이언트 라이브러리에서 프로그래밍 방식으로 추적 하지 않도록 하려면 관련 라이브러리를 로드할 때 먼저 다음 코드 조각을 호출 합니다.

```csharp
Kusto.Cloud.Platform.Utils.TraceSourceManager.SetTraceVerbosityForAll(
    Kusto.Cloud.Platform.Utils.TraceVerbosity.Fatal
    );
```

## <a name="suppressing-tracing-by-using-a-config-file"></a>구성 파일을 사용 하 여 추적 표시 안 함

구성 파일을 통해 Kusto client 라이브러리에서 추적을 표시 하지 않으려면 이제 적절 한 `Kusto.Cloud.Platform.dll.tweaks` "조정"이 다음과 같이 `Kusto.Data` 읽도록 파일 (라이브러리에 포함 됨)을 수정 합니다.

```xml
    <!-- Overrides the default trace verbosity level -->
    <add key="Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel" value="0" />
```

(조정에 적용 하려면 값에 빼기 기호가 없어야 `key`합니다.)

또 다른 방법은 다음을 수행 하는 것입니다.

```csharp
Kusto.Cloud.Platform.Utils.Anchor.Tweaks.SetProgrammaticAppSwitch(
    "Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel",
    "0"
    );
```

## <a name="how-to-enable-the-kusto-client-libraries-tracing"></a>Kusto 클라이언트 라이브러리 추적을 사용 하도록 설정 하는 방법

Kusto 클라이언트 라이브러리에서 추적을 사용 하도록 설정 하려면 응용 프로그램의 app.config 파일에서 .NET 추적을 사용 하도록 설정 합니다. 예를 들어 응용 프로그램 `MyApp.exe` 에서 Kusto. Data 클라이언트 라이브러리를 사용 하 고 있다고 가정 합니다. 그런 다음 다음을 `MyApp.exe.config` 포함 하도록 파일을 변경 하면 다음에 응용 프로그램이 시작 될 때 Kusto. 데이터 추적이 가능 합니다.

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

이렇게 하면 프로세스의 디렉터리에 있는 라는 `RollingLogs` 하위 디렉터리에 있는 CSV 파일에 기록 하는 추적 수신기가 구성 됩니다. (물론 모든. NET 호환 추적 수신기 클래스도 사용할 수 있습니다.

## <a name="how-to-enable-the-aad-client-libraries-adal-tracing"></a>ADAL (AAD 클라이언트 라이브러리) 추적을 사용 하도록 설정 하는 방법

Kusto 클라이언트 라이브러리에 대 한 추적을 사용 하도록 설정 하면 AAD 클라이언트 라이브러리에서 내보낸 추적 (Kusto 클라이언트 라이브러리가 ADAL 추적을 자동으로 구성)
