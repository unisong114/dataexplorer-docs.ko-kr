---
title: 파일 포함
description: 포함 파일
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 07/13/2020
ms.author: orspodek
ms.reviewer: alexefro
ms.custom: include file
ms.openlocfilehash: 1ee658d2c16bcf4174bb82d61ddc8c9b2d7d126f
ms.sourcegitcommit: 537a7eaf8c8e06a5bde57503fedd1c3706dd2b45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2020
ms.locfileid: "86423110"
---
## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>스트리밍 수집을 사용 하 여 데이터를 클러스터로 수집 합니다.

두 개의 스트리밍 수집 형식이 지원 됩니다.

* 데이터 원본으로 사용 되는 [**이벤트 허브**](../ingest-data-event-hub.md) 또는 [**IoT Hub**](../ingest-data-iot-hub.md)입니다.
* **사용자 지정** 수집을 사용 하려면 Azure 데이터 탐색기 [클라이언트 라이브러리](../kusto/api/client-libraries.md)중 하나를 사용 하는 응용 프로그램을 작성 해야 합니다. 샘플 응용 프로그램에 대 한 [스트리밍 수집 샘플](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) 을 참조 하세요.

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>적절 한 스트리밍 수집 유형 선택

|조건과|이벤트 허브|사용자 지정 수집|
|---------|---------|---------|
|수집 시작 및 쿼리에 사용할 수 있는 데이터 간의 데이터 지연 | 지연 시간 초과 | 짧은 지연  |
|개발 오버 헤드 | 빠르고 쉬운 설치, 개발 오버 헤드 없음 | 응용 프로그램에서 오류를 처리 하 고 데이터 일관성을 유지 하기 위한 높은 개발 오버 헤드 |
