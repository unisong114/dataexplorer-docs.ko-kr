---
title: 스트리밍 수집을 위해 캐시 된 스키마 지우기-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 캐시 된 데이터베이스 스키마를 지우기 위한 관리 명령을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/20/2020
ms.openlocfilehash: 23d86e528dfe687b79ce225b16712184a9bfcde4
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84784500"
---
# <a name="clear-schema-cache-for-streaming-ingestion"></a>스트리밍 수집을 위한 스키마 캐시 지우기

클러스터 노드는 스트리밍 수집을 통해 데이터를 수신 하는 데이터베이스의 스키마를 캐시 합니다. 이 프로세스는 클러스터 리소스의 성능과 사용률을 최적화 하지만 스키마가 변경 될 때 전파가 지연 될 수 있습니다.
캐시를 지워 후속 스트리밍 수집 요청에서 데이터베이스 또는 테이블 스키마 변경을 통합 하도록 보장 합니다.
자세한 내용은 [스트리밍 수집 및 스키마 변경](streaming-ingestion-schema-changes.md)을 참조 하세요.

**스키마 캐시 지우기**

`.clear cache streamingingestion schema`명령은 캐시 된 스키마를 모든 클러스터 노드에서 플러시합니다.

**구문**

`.clear``table` &lt; &gt; 테이블 `cache` 이름 `streamingingestion``schema`

`.clear` `database` `cache` `streamingingestion` `schema`

**반환**

이 명령은 다음 열이 있는 테이블을 반환 합니다.

|Column    |형식    |Description
|---|---|---
|NodeId|`string`|클러스터 노드의 식별자입니다.
|상태|`string`|성공/실패

**예제**

```kusto
.clear database cache streamingingestion schema

.show table T1 cache streamingingestion schema
```

|NodeId|상태|
|---|---|
|Node1|성공
|Node2|실패

> [!NOTE]
> 명령이 실패 하거나 반환 된 테이블의 행 중 하나에 *Status = Failed* 가 포함 되어 있으면 명령을 안전 하 게 다시 시도할 수 있습니다.
