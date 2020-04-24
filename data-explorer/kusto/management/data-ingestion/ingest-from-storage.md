---
title: 명령으로 .ingest (저장소에서 데이터 가져옵니다) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 .inest into 명령(저장소에서 데이터 끌어오기)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 1f304f9dc064094c6d55cb32f3fb453f32114979
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521440"
---
# <a name="the-ingest-into-command-pull-data-from-storage"></a>명령으로 .ingest (저장소에서 데이터를 가져옵니다)

명령은 `.ingest into` 하나 이상의 클라우드 저장소 아티팩트에서 데이터를 "끌어당기"하여 데이터를 테이블로 수집합니다.
예를 들어 명령은 Azure Blob Storage에서 1000개의 CSV 형식의 Blob을 검색하고 구문 분석한 다음 단일 대상 테이블로 함께 통합할 수 있습니다.
데이터는 기존 레코드에 영향을 주지 않고 테이블의 스키마를 수정하지 않고 테이블에 추가됩니다.

**구문**

`.ingest`[`async` `into` `table` ] *테이블네임* *소스DataLocator* `with` `(` [ *인제션속성 이름* `=` *수집속성값* [...]`,` `)`]

**인수**

* `async`: 지정하면 명령이 즉시 반환되고 백그라운드에서 계속 수행됩니다. 명령의 결과에는 명령과 `OperationId` 함께 `.show operation` 사용하여 처리 완료 상태 및 결과를 검색할 수 있는 값이 포함됩니다.
  
* *테이블 이름*: 데이터를 수집할 테이블의 이름입니다.
  테이블 이름은 항상 컨텍스트의 데이터베이스를 기준으로 하며 해당 스키마는 스키마 매핑 개체가 제공되지 않는 경우 데이터에 대해 가정되는 스키마입니다.

* *SourceDataLocator*: 문자 `string`문자로 둘러싸인 `(` 이러한 리터럴 및 `)` 가져올 데이터를 포함하는 저장소 아티팩트를 나타내는 문자 문자 또는 쉼표 구분 목록입니다. [저장소 연결 문자열을](../../api/connection-strings/storage.md)참조하십시오.

> [!NOTE]
> 실제 자격 증명을 포함하는 *SourceDataPointer에* [난독 처리된 문자열 리터럴을](../../query/scalar-data-types/string.md#obfuscated-string-literals) 사용하는 것이 좋습니다.
> 서비스는 내부 추적, 오류 메시지 등에서 자격 증명을 스크러빙해야합니다.

* *인스티온속성,* *인과속성값*: 인기 프로세스에 영향을 주는 모든 수의 [인기 속성입니다.](https://docs.microsoft.com/azure/data-explorer/ingestion-properties)

**결과**

명령의 결과는 명령에 의해 생성된 데이터 샤드("익스텐츠")만큼의 레코드가 있는 테이블입니다.
데이터 샤드가 생성되지 않은 경우 빈(값 0) 익스텐트 ID를 사용하여 단일 레코드가 반환됩니다.

|속성       |Type      |Description                                                                |
|-----------|----------|---------------------------------------------------------------------------|
|익스텐트 ID   |`guid`    |명령에 의해 생성된 데이터 샤드의 고유 식별자입니다.|
|항목로드됨 |`string`  |이 레코드와 관련된 하나 이상의 저장소 아티팩트입니다.             |
|Duration   |`timespan`|섭취를 수행하는 데 걸린 시간입니다.                                     |
|하스 오류  |`bool`    |이 레코드가 인레이션 실패를 나타내는지 여부입니다.                |
|오퍼레이션 ID|`guid`    |작업을 나타내는 고유 ID입니다. `.show operation` 명령과 함께 사용할 수 있습니다.|

**주의**

이 명령은 인제되는 테이블의 스키마를 수정하지 않습니다.
필요한 경우 데이터는 수집 하는 동안이 스키마에 "강제" 되 고, 다른 방법으로 (추가 열무시 되 고 누락 된 열 null 값으로 처리 됩니다).

**예**

다음 예제에서는 엔진에 Azure Blob Storage에서 두 개의 Blob을 CSV 파일로 읽고 `T`해당 내용을 테이블로 들어 오도록 지시합니다. 는 `...` 각 Blob에 대한 읽기 액세스를 제공하는 Azure Storage 공유 액세스 서명(SAS)을 나타냅니다. 또한 SAS가 기록되지 않도록 난독 `h` 처리된 문자열(문자열 값 앞)을 사용합니다.

```kusto
.ingest into table T (
    h'https://contoso.blob.core.windows.net/container/file1.csv?...',
    h'https://contoso.blob.core.windows.net/container/file2.csv?...'
)
```

다음 예제는 Azure 데이터 레이크 저장소 세대 2(ADLSv2)의 데이터를 수집하기 위한 것입니다. 여기서 사용되는 자격`...`증명 () 은 저장소 계정 자격 증명(공유 키)이며 연결 문자열의 비밀 부분에 대해서만 문자열 난독 처리를 사용합니다.

```kusto
.ingest into table T (
  'abfss://myfilesystem@contoso.dfs.core.windows.net/path/to/file1.csv;'
    h'...'
)
```

다음 예제에서는 Azure 데이터 레이크 저장소(ADLS)에서 단일 파일을 수집합니다.
사용자의 자격 증명을 사용하여 ADLS에 액세스하므로 저장소 URI를 비밀이 포함된 것으로 취급할 필요가 없습니다. 또한 인기 변환 속성을 지정하는 방법도 보여 주어 있습니다.

```kusto
.ingest into table T ('adl://contoso.azuredatalakestore.net/Path/To/File/file1.ext;impersonate')
  with (format='csv')
```

