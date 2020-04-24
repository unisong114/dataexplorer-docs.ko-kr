---
title: 데이터를 저장소로 내보내기 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 저장소로 데이터 내보내기에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 12800955d1680a280aa4772db86d8b71e44e7089
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521576"
---
# <a name="export-data-to-storage"></a>데이터를 저장소로 내보내기

쿼리를 실행하고 [저장소 연결 문자열에](../../api/connection-strings/storage.md)의해 지정된 외부 저장소에 첫 번째 결과 집합을 씁니다.

**구문**

`.export`[`async`[`compressed` `to` [ [ *[ [ [ [ [ 출력데이터 형식* 
 `(` *스토리지연결 문자열* [`,` ...] `)` `with` [ `(` *속성 이름* `=` `,` *속성값* [...] `)`] `<|` *쿼리*

**인수**

* `async`: 지정하면 명령이 비동기 모드에서 실행중임을 나타냅니다.
  이 모드의 동작에 대한 자세한 내용은 아래를 참조하십시오.

* `compressed`: 지정하면 출력 저장소 아티팩트가 `.gz` 파일로 압축됩니다. 마루 `compressionType` 파일을 스냅으로 압축하는 것을 참조하십시오. 

* *OutputDataFormat*: 명령에 의해 작성된 저장소 아티팩트의 데이터 형식을 나타냅니다. 지원되는 값은 `tsv` `json`" `csv` `parquet`및 .

* *StorageConnectionString*: 데이터를 쓸 [저장소를](../../api/connection-strings/storage.md) 나타내는 하나 이상의 저장소 연결 문자열을 지정합니다. (확장 가능한 쓰기에 대해 두 개 이상의 저장소 연결 문자열을 지정할 수 있습니다.) 이러한 각 연결 문자열은 저장소에 쓸 때 사용할 자격 증명을 나타내야 합니다.
  예를 들어 Azure Blob Storage에 쓸 때 자격 증명은 저장소 계정 키또는 읽기, 쓰기 및 Blob 을 나열할 수 있는 권한이 있는 공유 액세스 키(SAS)일 수 있습니다.

> [!NOTE]
> Kusto 클러스터 자체와 동일한 지역에 공동 위치한 저장소로 데이터를 내보내는 것이 좋습니다. 여기에는 내보낸 데이터가 포함되므로 다른 지역의 다른 클라우드 서비스로 전송할 수 있습니다. 읽기는 원격으로 발생할 수 있지만 쓰기는 로컬로 수행해야 합니다.

* *PropertyName*/*PropertyValue*: 0 개 이상의 선택적 내보내기 속성:

|속성        |Type    |Description                                                                                                                |
|----------------|--------|---------------------------------------------------------------------------------------------------------------------------|
|`sizeLimit`     |`long`  |압축 하기 전에 작성 되는 단일 저장소 아티팩트의 바이트의 크기 제한입니다. 허용된 범위는 100MB(기본값)에서 1GB로 입니다.|
|`includeHeaders`|`string`|출력의 경우 `csv` / `tsv` 열 헤더 생성을 제어합니다. `none` (기본값, 헤더 줄이 내보내지지 `all` 않는다), (모든 저장소 아티팩트에 `firstFile` 헤더 줄 내림차호 를 내보내거나) (첫 번째 저장소 아티팩트에만 헤더 줄 내림차호)일 수 있습니다.|
|`fileExtension` |`string`|저장소 아티팩트의 "확장" 부분(예: `.csv` `.tsv`또는)을 나타냅니다. 압축을 사용하는 `.gz` 경우, 또한 추가됩니다.|
|`namePrefix`    |`string`|생성된 각 저장소 아티팩트 이름에 추가할 접두사를 나타냅니다. 지정되지 않은 상태로 두면 임의 접두사가 사용됩니다.       |
|`encoding`      |`string`|(기본값) 또는 `UTF8NoBOM` `UTF8BOM`텍스트를 인코딩하는 방법을 나타냅니다. |
|`compressionType`|`string`|사용할 압축 유형을 나타냅니다. 가능한 값은 `gzip` 또는 `snappy`입니다. 기본값은 `gzip`입니다. `snappy`형식에 `parquet` 사용할 수 있습니다( 선택사항). |
|`distribution`   |`string`  |배포 힌트`single` `per_node`( `per_shard`, , ). 값이 같으면 `single`단일 스레드가 저장소에 씁니다. 그렇지 않으면 내보내기는 쿼리를 병렬로 실행하는 모든 노드에서 작성됩니다. [플러그인 연산자 평가를](../../query/evaluateoperator.md)참조하십시오. 기본값은 `per_shard`입니다.
|`distributed`   |`bool`  |분산 내보내기를 사용하지 않도록 설정/사용하도록 설정합니다. false로 설정하면 `single` 배포 힌트와 동일합니다. 기본값은 true입니다.
|`persistDetails`|`bool`  |명령이 결과를 유지해야 한다는 것을 `async` 나타냅니다(플래그 참조). `true` 비동기에서 실행되는 기본값이지만 호출자가 결과를 필요로 하지 않는 경우 해제할 수 있습니다. 기본값은 `false` 동기 실행으로 설정되지만 이러한 실행에서도 켜질 수 있습니다. |
|`parquetRowGroupSize`|`int`  |데이터 형식이 마루인 경우에만 관련이 있습니다. 내보낸 파일의 행 그룹 크기를 제어합니다. 기본 행 그룹 크기는 100000 레코드입니다.|

**결과**

명령은 생성된 저장소 아티팩트를 설명하는 테이블을 반환합니다.
각 레코드는 단일 아티팩트를 설명하고 아티팩트에 대한 저장 경로와 아티팩트가 보유한 데이터 레코드 수를 포함합니다.

|경로|숫자 레코드|
|---|---|
|http://storage1.blob.core.windows.net/containerName/export_1_d08afcae2f044c1092b279412dcb571b.csv|10|
|http://storage1.blob.core.windows.net/containerName/export_2_454c0f1359e24795b6529da8a0101330.csv|15|

**비동기 모드**

플래그를 `async` 지정하면 비동기 모드에서 명령이 실행됩니다.
이 모드에서는 명령이 작업 ID를 사용하여 즉시 반환되고 데이터 내보내기가 완료될 때까지 백그라운드에서 계속됩니다. 명령에 의해 반환된 작업 ID는 다음 명령을 통해 진행 률과 궁극적으로 결과를 추적하는 데 사용할 수 있습니다.

* [.show 작업](../operations.md#show-operations): 진행 상황을 추적합니다.
* [.show 작업 세부 정보](../operations.md#show-operation-details): 완료 결과를 가져옵니다.

예를 들어 성공적으로 완료한 후 다음을 사용하여 결과를 검색할 수 있습니다.

```kusto
.show operation f008dc1e-2710-47d8-8d34-0d562f5f8615 details
```

**예** 

이 예제에서 Kusto는 쿼리를 실행한 다음 쿼리에서 생성된 첫 번째 레코드 집합을 하나 이상의 압축된 CSV Blob에 내보냅니다.
열 이름 레이블은 각 Blob의 첫 번째 행으로 추가됩니다.

```kusto 
.export
  async compressed
  to csv (
    h@"https://storage1.blob.core.windows.net/containerName;secretKey",
    h@"https://storage1.blob.core.windows.net/containerName2;secretKey"
  ) with (
    sizeLimit=100000,
    namePrefix=export,
    includeHeaders=all,
    encoding =UTF8NoBOM
  )
  <| myLogs | where id == "moshe" | limit 10000
```

**알려진 문제**

*내보내기 명령 중 저장소 오류*

기본적으로 내보내기 명령은 쓰기를 저장소로 동시에 내보낼 데이터를 포함하는 모든 [익스텐스가](../extents-overview.md) 배포됩니다. 대규모 내보내기에서 이러한 익스텐트의 수가 많을 때 저장소에 부하가 많이 발생하여 저장소 제한이 발생하거나 일시적인 저장소 오류가 발생할 수 있습니다. 이러한 경우 내보내기 명령에 제공된 저장소 계정 수를 늘리거나(계정 간에 로드가 분산됩니다) 배포 힌트를 설정하여 `per_node` 동시성을 줄이는 것이 좋습니다(명령 속성 참조). 배포를 완전히 비활성화할 수도 있지만 이는 명령 성능에 큰 영향을 줄 수 있습니다.
 