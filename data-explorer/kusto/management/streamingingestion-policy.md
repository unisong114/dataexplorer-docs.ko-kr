---
title: Kusto streaming 수집 정책 관리-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 스트리밍 수집 정책 관리에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 6f7efe2916bc5c5344bf4a6c4fa4a60bfaa167b7
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967352"
---
# <a name="streaming-ingestion-policy-command"></a>스트리밍 수집 정책 명령

테이블에 스트리밍 수집 정책을 설정 하 여이 테이블에 대 한 스트리밍 수집을 허용할 수 있습니다. 또한 데이터베이스 수준에서 정책을 설정 하 여 현재 테이블과 이후 테이블 모두에 동일한 설정을 적용할 수 있습니다.

자세한 내용은 [스트리밍](../../ingest-data-streaming.md)수집을 참조 하세요. 스트리밍 수집 정책에 대 한 자세한 내용은 스트리밍 수집 [정책](streamingingestionpolicy.md)을 참조 하세요.

## <a name="display-the-policy"></a>정책 표시

이 `.show policy streamingingestion` 명령은 데이터베이스 또는 테이블의 스트리밍 수집 정책을 보여 줍니다.
 
**구문**

`.show``{database|table}` &lt; 엔터티 &gt; 이름 `policy``streamingingestion`

**반환**

이 명령은 다음 열이 있는 테이블을 반환 합니다.

|Column    |Type    |Description
|---|---|---
|PolicyName|`string`|정책 이름-StreamingIngestionPolicy
|EntityName|`string`|데이터베이스 또는 테이블 이름
|정책    |`string`|[스트리밍 수집 정책 개체](#streaming-ingestion-policy-object)

**예**

```kusto
.show database DB1 policy streamingingestion

.show table T1 policy streamingingestion
```

|PolicyName|EntityName|정책|ChildEntities|EntityType|
|---|---|---|---|---|
|StreamingIngestionPolicy|DB1|{"IsEnabled": true, "HintAllocatedRate": null}

## <a name="change-the-policy"></a>정책 변경

`.alter[-merge] policy streamingingestion`명령 패밀리는 데이터베이스 또는 테이블의 스트리밍 수집 정책을 수정 하는 방법을 제공 합니다.

**구문**

* `.alter``{database|table}` &lt; &gt; 엔터티 `policy` 이름 `streamingingestion``[enable|disable]`

* `.alter``{database|table}` &lt; 엔터티 이름 &gt; `policy` `streamingingestion` &lt; [스트리밍 수집 정책 개체](#streaming-ingestion-policy-object)&gt;

* `.alter-merge``{database|table}` &lt; 엔터티 이름 &gt; `policy` `streamingingestion` &lt; [스트리밍 수집 정책 개체](#streaming-ingestion-policy-object)&gt;

> [!Note]
>
> * 정책의 다른 속성을 수정 하지 않고 스트리밍 수집의 사용/사용 안 함 상태를 변경 하거나, 정책이 이전에 엔터티에 정의 되지 않은 경우 속성을 기본값으로 설정할 수 있습니다.
>
> * 엔터티에서 전체 스트리밍 수집 정책을 바꿀 수 있습니다. [스트리밍 수집 정책 개체](#streaming-ingestion-policy-object) 는 모든 필수 속성을 포함 해야 합니다.
>
> * 엔터티에서 스트리밍 수집 정책의 지정 된 속성만 바꿀 수 있습니다. [스트리밍 수집 정책 개체](#streaming-ingestion-policy-object) 에는 필수 속성 중 일부 또는 없음이 포함 될 수 있습니다.

**반환**

이 명령은 테이블이 나 데이터베이스 `streamingingestion` 정책 개체를 수정한 [ `.show policy` `streamingingestion` ](#display-the-policy) 다음 해당 명령의 출력을 반환 합니다.

**예**

```kusto
.alter database DB1 policy streamingingestion enable

.alter table T1 policy streamingingestion disable

.alter database DB1 policy streamingingestion '{"IsEnabled": true, "HintAllocatedRate": 2.1}'

.alter table T1 streamingingestion '{"IsEnabled": true}'

.alter-merge database DB1 policy streamingingestion '{"IsEnabled": false}'

.alter-merge table T1 policy streamingingestion '{"HintAllocatedRate": 1.5}'
```

## <a name="delete-the-policy"></a>정책 삭제

이 `.delete policy streamingingestion` 명령은 데이터베이스 또는 테이블에서 streamingingestion 정책을 삭제 합니다.

**구문**

`.delete``{database|table}` &lt; 엔터티 &gt; 이름 `policy``streamingingestion`

**반환**

이 명령은 테이블이 나 데이터베이스 streamingingestion 정책 개체를 삭제 한 다음 해당 하는 [. show policy streamingingestion](#display-the-policy) 명령의 출력을 반환 합니다.

**예**

```kusto
.delete database DB1 policy streamingingestion

.delete table T1 policy streamingingestion
```

### <a name="streaming-ingestion-policy-object"></a>스트리밍 수집 정책 개체

관리 명령의 입력 및 출력에서 스트리밍 수집 정책 개체는 다음 속성을 포함 하는 JSON 형식 문자열입니다.

|속성|Type|Description|필수/선택
|---|---|---|---
|IsEnabled|`bool`|엔터티에 대해 스트리밍 수집을 사용 하도록 설정 되어 있습니다.| 필수
|HintAllocatedRate|`double`|데이터 조절기의 예상 률 (Gb/시간)|선택 사항
