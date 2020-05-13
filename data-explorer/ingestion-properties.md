---
title: Azure 데이터 탐색기에 대 한 데이터 수집 속성
description: Azure 데이터 탐색기에서 지 원하는 다양 한 데이터 수집 속성에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: c66d7e11b3f64633a0dda33f7a3fa2f974536caa
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373736"
---
# <a name="azure-data-explorer-data-ingestion-properties"></a>Azure 데이터 탐색기 데이터 수집 속성 

데이터 수집은 테이블에 데이터를 추가 하 고 Azure 데이터 탐색기에서 쿼리에 사용할 수 있도록 하는 프로세스입니다. 키워드 뒤에 수집 명령에 속성을 추가 `with` 합니다.

## <a name="ingestion-properties"></a>수집 속성

다음 표에서는 Azure 데이터 탐색기에서 지 원하는 속성을 나열 하 고이에 대해 설명 하 고 예제를 제공 합니다. 

|속성              |설명                                              |예제                                             |
|----------------------|---------------------------------------------------------|----------------------------------------------------|
|`ingestionMapping`    |원본 파일의 데이터를 테이블의 실제 열에 매핑하는 방법을 나타내는 문자열 값입니다. `format`관련 매핑 유형을 사용 하 여 값을 정의 합니다. [데이터 매핑](kusto/management/mappings.md)을 참조하세요.|`with (format="json", ingestionMapping = "[{\"column\":\"rownumber\", \"Properties\":{\"Path\":\"$.RowNumber\"}}, {\"column\":\"rowguid\", \"Properties\":{\"Path\":\"$.RowGuid\"}}]")`<br>(사용되지 않음: `avroMapping`, `csvMapping`, `jsonMapping`) |
|`ingestionMappingReference`|명명된 매핑 정책 개체를 사용하여 원본 파일에서 테이블의 실제 열로 데이터를 매핑하는 방법을 나타내는 문자열 값입니다. `format`관련 매핑 유형을 사용 하 여 값을 정의 합니다. [데이터 매핑](kusto/management/mappings.md)을 참조하세요.|`with (format="csv", ingestionMappingReference = "Mapping1")`<br>(사용되지 않음: `avroMappingReference`, `csvMappingReference`, `jsonMappingReference`)|
|`creationTime` |수집 데이터 익스텐트의 생성 시 사용할 datetime 값 (ISO8601 문자열로 형식이 지정 됨)입니다. 지정하지 않으면 현재 값(`now()`)이 사용됩니다. 기본값을 재정의 하는 것은 보존 정책이 올바르게 적용 되도록 이전 데이터를 수집 때 유용 합니다.|`with (creationTime="2017-02-13T11:09:36.7992775Z")`|
|`extend_schema`|지정하면 테이블의 스키마를 확장하도록 명령에 지시하는 부울 값(기본값: `false`)입니다. 이 옵션은 `.append` 및 `.set-or-append` 명령에만 적용됩니다. 유일하게 허용되는 스키마 확장에는 테이블의 끝에 추가되는 추가 열이 있습니다.|원래 테이블 스키마가 인 경우 `(a:string, b:int)` 유효한 스키마 확장은 이지만 `(a:string, b:int, c:datetime, d:string)` `(a:string, c:datetime)` 유효 하지 않습니다.|
|`folder` |[쿼리 기반 쿼리](kusto/management/data-ingestion/ingest-from-query.md) 명령의 경우 테이블에 할당할 폴더입니다. 테이블이 이미 있는 경우이 속성은 테이블의 폴더를 재정의 합니다.|`with (folder="Tables/Temporary")`|
|`format` |데이터 형식 ( [지원 되는 데이터 형식](ingestion-supported-formats.md)참조)|`with (format="csv")`|
|`ingestIfNotExists`|지정하면 동일한 값을 가진 `ingest-by:` 태그로 태그가 지정된 데이터가 테이블에 이미 있는 경우 수집하지 못하게 하는 문자열 값입니다. 이렇게 하면 idempotent(멱등원) 데이터 수집이 보장됩니다. 자세한 내용은 [수집 방법: 태그](kusto/management/extents-overview.md#ingest-by-extent-tags)를 참조 하세요.|속성은 `with (ingestIfNotExists='["Part0001"]', tags='["ingest-by:Part0001"]')` 태그가 있는 데이터가 이미 있는 경우 현재 수집을 완료 하지 않음을 표시 합니다 `ingest-by:Part0001` . 아직 존재 하지 않는 경우이 새 수집에서이 태그를 설정 해야 합니다 (이후 수집에서 동일한 데이터를 다시 수집 하려고 시도 하는 경우).|
|`ignoreFirstRecord` |`true`로 설정하면 수집에서 모든 파일의 첫 번째 레코드를 무시해야 함을 나타내는 부울 값입니다. 이 속성은 `CSV` 파일의 첫 번째 레코드가 열 이름이 면 및 유사한 형식의 파일에 유용 합니다. 기본적으로 `false` 는로 가정 합니다.|`with (ignoreFirstRecord=false)`|
|`persistDetails` |지정하면 [.show operation details](kusto/management/operations.md#show-operation-details) 명령에서 자세한 결과를 검색할 수 있도록 명령이 해당 결과를 유지해야 함(성공한 경우에도)을 나타내는 부울 값입니다. 기본값은 `false`입니다.|`with (persistDetails=true)`|
|`policy_ingestiontime`|지정하면 이 명령으로 만든 테이블에서 [수집 시간 정책](kusto/management/ingestiontimepolicy.md)을 사용하도록 설정할지 여부를 설명하는 부울 값입니다. 기본값은 `true`입니다.|`with (policy_ingestiontime=false)`|
|`recreate_schema` |지정하면 명령에서 테이블의 스키마를 다시 만들 수 있는지 여부를 설명하는 부울 값입니다. 이 속성은 명령에만 적용 됩니다 `.set-or-replace` . 둘 다 설정 된 경우이 속성은 속성 보다 우선적으로 적용 `extend_schema` 됩니다.|`with (recreate_schema=true)`|
|`tags`|JSON 문자열로 형식이 지정 된 수집 데이터와 연결할 [태그](kusto/management/extents-overview.md#extent-tagging) 의 목록입니다. |`with (tags="['Tag1', 'Tag2']")`|
|`validationPolicy`|수집 중에 실행할 유효성 검사를 나타내는 JSON 문자열입니다. 다양 한 옵션에 대 한 설명은 [데이터](kusto/management/data-ingestion/index.md) 수집을 참조 하세요.| `with (validationPolicy='{"ValidationOptions":1, "ValidationImplications":1}')`이는 실제로 기본 정책입니다.|
|`zipPattern`|ZIP 보관 파일이 있는 저장소의 데이터를 수집 때이 속성을 사용 합니다. 이 값은 수집할 ZIP 보관 파일에서 파일을 선택할 때 사용할 정규식을 나타내는 문자열 값입니다.  보관 파일의 다른 모든 파일은 무시됩니다.|`with (zipPattern="*.csv")`|

## <a name="next-steps"></a>다음 단계

* [데이터](ingest-data-overview.md) 수집에 대 한 자세한 정보
* [지원 되는 데이터 형식](ingestion-supported-formats.md) 에 대 한 자세한 정보
