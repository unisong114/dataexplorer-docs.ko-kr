---
title: 명령 수집 (저장소에서 데이터 끌어오기)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 명령을 수집 (저장소에서 데이터 끌어오기) 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 05d62aaa7b123f7f6d02b784402fd06335e155b2
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373419"
---
# <a name="the-ingest-into-command-pull-data-from-storage"></a>명령 수집 (저장소에서 데이터 끌어오기)

`.ingest into`명령은 하나 이상의 클라우드 저장소 아티팩트에서 데이터를 "풀링" 하 여 테이블에 데이터를 수집 합니다.
예를 들어 명령은 Azure Blob Storage에서 1000 CSV 형식 blob을 검색 하 고, 구문 분석 하 고, 단일 대상 테이블로 모을 수 있습니다.
기존 레코드에 영향을 주지 않고 테이블의 스키마를 수정 하지 않고 테이블에 데이터를 추가 합니다.

**구문**

`.ingest`[ `async` ] `into` `table` *TableName* *SourceDataLocator* [ `with` `(` *IngestionPropertyName* `=` *IngestionPropertyValue* [ `,` ...] `)` ]

**인수**

* `async`: 지정 된 경우 명령은 즉시 반환 되 고 백그라운드에서 계속 수집 됩니다. 명령의 결과에는 명령을 사용 하 여 수집 `OperationId` `.show operation` 완료 상태 및 결과를 검색 하는 데 사용할 수 있는 값이 포함 됩니다.
  
* *TableName*: 데이터를 수집 하는 테이블의 이름입니다.
  테이블 이름은 항상 컨텍스트의 데이터베이스에 상대적 이며 스키마 매핑 개체가 제공 되지 않은 경우 해당 스키마는 데이터에 대해 가정 하는 스키마입니다.

* *SourceDataLocator*: `string` `(` `)` 끌어올 데이터를 포함 하는 저장소 아티팩트를 나타내는 및 문자로 둘러싸인, 형식의 리터럴 또는 쉼표로 구분 된 이러한 리터럴의 목록입니다. [저장소 연결 문자열](../../api/connection-strings/storage.md)을 참조 하세요.

> [!NOTE]
> 실제 자격 증명을 포함 하는 *SourceDataPointer* 에 난독 처리 된 [문자열 리터럴을](../../query/scalar-data-types/string.md#obfuscated-string-literals) 사용 하는 것이 좋습니다.
> 이 서비스는 내부 추적, 오류 메시지 등의 자격 증명을 삭제 해야 합니다.

* *IngestionPropertyName*, *IngestionPropertyValue*: 수집 프로세스에 영향을 주는 수집 [속성](../../../ingestion-properties.md) 은 수에 제한이 없습니다.

**결과**

명령의 결과는 명령에 의해 생성 된 데이터 분할 ("익스텐트") 만큼의 레코드를 포함 하는 테이블입니다.
데이터 분할 생성 되지 않은 경우에는 빈 (0 값) 익스텐트 ID를 사용 하 여 단일 레코드가 반환 됩니다.

|속성       |Type      |설명                                                                |
|-----------|----------|---------------------------------------------------------------------------|
|ExtentId   |`guid`    |명령으로 생성 된 데이터 분할에 대 한 고유 식별자입니다.|
|ItemLoaded |`string`  |이 레코드와 관련 된 하나 이상의 저장소 아티팩트입니다.             |
|Duration   |`timespan`|수집을 수행 하는 데 걸리는 시간입니다.                                     |
|HasErrors  |`bool`    |이 레코드가 수집 오류를 나타내는지 여부를 나타냅니다.                |
|OperationId|`guid`    |작업을 나타내는 고유 ID입니다. 명령과 함께 사용할 수 있습니다 `.show operation` .|

**주의**

이 명령은 수집 중인 테이블의 스키마를 수정 하지 않습니다.
필요한 경우 데이터는 수집 중에이 스키마로 "강제 변환" 됩니다 (추가 열은 무시 되 고 누락 된 열은 null 값으로 처리 됨).

**예**

다음 예에서는 Azure Blob Storage의 두 blob을 CSV 파일로 읽고 해당 내용을 테이블에 수집 하도록 엔진에 지시 합니다 `T` . 는 `...` 각 blob에 대 한 읽기 권한을 부여 하는 SAS (Azure Storage 공유 액세스 서명)를 나타냅니다. 또한 난독 처리 된 문자열 ( `h` 문자열 값의 앞에 있는)을 사용 하 여 SAS가 기록 되지 않도록 합니다.

```kusto
.ingest into table T (
    h'https://contoso.blob.core.windows.net/container/file1.csv?...',
    h'https://contoso.blob.core.windows.net/container/file2.csv?...'
)
```

다음 예제는 ADLSv2 (Azure Data Lake Storage Gen 2)의 수집 데이터를 위한 것입니다. 여기에 사용 되는 자격 증명 ( `...` )은 저장소 계정 자격 증명 (공유 키) 이며, 연결 문자열의 비밀 부분에 대해서만 문자열 난독 처리를 사용 합니다.

```kusto
.ingest into table T (
  'abfss://myfilesystem@contoso.dfs.core.windows.net/path/to/file1.csv;'
    h'...'
)
```

다음 예에서는 Azure Data Lake Storage (ADLS)에서 단일 파일을 수집 합니다.
사용자의 자격 증명을 사용 하 여 ADLS에 액세스 합니다. 따라서 저장소 URI를 암호를 포함 하는 것으로 처리할 필요가 없습니다. 또한 수집 속성을 지정 하는 방법을 보여 줍니다.

```kusto
.ingest into table T ('adl://contoso.azuredatalakestore.net/Path/To/File/file1.ext;impersonate')
  with (format='csv')
```

