---
title: Azure 데이터 탐색기의 데이터 수집 속성
description: Azure 데이터 탐색기에서 지원하는 다양한 데이터 수집 속성에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 04f7d0de687d168fe6df7bfcc535cbafde2d4446
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496460"
---
# <a name="azure-data-explorer-data-ingestion-properties"></a>Azure 데이터 탐색기 데이터 수집 속성 

데이터 수집은 데이터가 테이블에 추가되고 Azure Data Explorer에서 쿼리에 사용할 수 있게 되는 프로세스입니다. 키워드 다음의 인기 명령에 `with` 속성을 추가합니다.

## <a name="ingestion-properties"></a>수집 속성

다음 표에서는 Azure Data Explorer에서 지원하는 속성을 나열하고 설명하고 예제를 제공합니다. 

|속성              |Description                                              |예제                                             |
|----------------------|---------------------------------------------------------|----------------------------------------------------|
|`ingestionMapping`    |원본 파일의 데이터를 테이블의 실제 열에 매핑하는 방법을 나타내는 문자열 값입니다. 관련 `format` 매핑 유형으로 값을 정의합니다. [데이터 매핑](kusto/management/mappings.md)을 참조하세요.|`with (format="json", ingestionMapping = "[{\"column\":\"rownumber\", \"Properties\":{\"Path\":\"$.RowNumber\"}}, {\"column\":\"rowguid\", \"Properties\":{\"Path\":\"$.RowGuid\"}}]")`<br>(사용되지 않음: `avroMapping`, `csvMapping`, `jsonMapping`) |
|`ingestionMappingReference`|명명된 매핑 정책 개체를 사용하여 원본 파일에서 테이블의 실제 열로 데이터를 매핑하는 방법을 나타내는 문자열 값입니다. 관련 `format` 매핑 유형으로 값을 정의합니다. [데이터 매핑](kusto/management/mappings.md)을 참조하세요.|`with (format="csv", ingestionMappingReference = "Mapping1")`<br>(사용되지 않음: `avroMappingReference`, `csvMappingReference`, `jsonMappingReference`)|
|`creationTime` |수집된 데이터 익스텐트 생성 시 사용할 날짜 시간 값(ISO8601 문자열로 서식이 지정)입니다. 지정하지 않으면 현재 값(`now()`)이 사용됩니다. 기본값을 재정의하면 보존 정책이 올바르게 적용되도록 이전 데이터를 수집할 때 유용합니다.|`with (creationTime="2017-02-13T11:09:36.7992775Z")`|
|`extend_schema`|지정하면 테이블의 스키마를 확장하도록 명령에 지시하는 부울 값(기본값: `false`)입니다. 이 옵션은 `.append` 및 `.set-or-append` 명령에만 적용됩니다. 유일하게 허용되는 스키마 확장에는 테이블의 끝에 추가되는 추가 열이 있습니다.|원래 테이블 스키마가 `(a:string, b:int)`있는 경우 유효한 스키마 `(a:string, b:int, c:datetime, d:string)`확장이 `(a:string, c:datetime)` 되지만 유효하지 않습니다.|
|`folder` |[쿼리에서 인제스트](kusto/management/data-ingestion/ingest-from-query.md) 명령의 경우 테이블에 할당할 폴더입니다. 테이블이 이미 있는 경우 이 속성은 테이블의 폴더를 재정의합니다.|`with (folder="Tables/Temporary")`|
|`format` |데이터 [형식(지원되는 데이터 형식](ingestion-supported-formats.md)참조).|`with (format="csv")`|
|`ingestIfNotExists`|지정하면 동일한 값을 가진 `ingest-by:` 태그로 태그가 지정된 데이터가 테이블에 이미 있는 경우 수집하지 못하게 하는 문자열 값입니다. 이렇게 하면 idempotent(멱등원) 데이터 수집이 보장됩니다. 자세한 내용은 [인제스트바이: 태그 를](kusto/management/extents-overview.md#ingest-by-extent-tags)참조하십시오.|속성은 `with (ingestIfNotExists='["Part0001"]', tags='["ingest-by:Part0001"]')` 태그가 `ingest-by:Part0001` 있는 데이터가 이미 있는 경우 현재 수집을 완료하지 않는다는 것을 나타냅니다. 아직 존재하지 않는 경우 이 새 수집에는 이 태그가 설정되어 있어야 합니다(향후 수집이 동일한 데이터를 다시 수집하려고 시도하는 경우).|
|`ignoreFirstRecord` |`true`로 설정하면 수집에서 모든 파일의 첫 번째 레코드를 무시해야 함을 나타내는 부울 값입니다. 이 속성은 파일의 `CSV`첫 번째 레코드가 열 이름인 경우 비슷한 형식의 파일에 유용합니다. 기본적으로 가정됩니다. `false`|`with (ignoreFirstRecord=false)`|
|`persistDetails` |지정하면 [.show operation details](kusto/management/operations.md#show-operation-details) 명령에서 자세한 결과를 검색할 수 있도록 명령이 해당 결과를 유지해야 함(성공한 경우에도)을 나타내는 부울 값입니다. 기본값은 `false`입니다.|`with (persistDetails=true)`|
|`policy_ingestiontime`|지정하면 이 명령으로 만든 테이블에서 [수집 시간 정책](kusto/management/ingestiontimepolicy.md)을 사용하도록 설정할지 여부를 설명하는 부울 값입니다. 기본값은 `true`입니다.|`with (policy_ingestiontime=false)`|
|`recreate_schema` |지정하면 명령에서 테이블의 스키마를 다시 만들 수 있는지 여부를 설명하는 부울 값입니다. 이 속성은 `.set-or-replace` 명령에만 적용됩니다. 이 속성은 둘 `extend_schema` 다 설정된 경우 속성보다 우선합니다.|`with (recreate_schema=true)`|
|`tags`|JSON 문자열로 서식이 지정된 수집된 데이터와 연결할 [태그](kusto/management/extents-overview.md#extent-tagging) 목록 |`with (tags="['Tag1', 'Tag2']")`|
|`validationPolicy`|수집 중에 실행할 유효성 검사를 나타내는 JSON 문자열입니다. 다양한 옵션에 대한 설명은 [데이터 수집을](kusto/management/data-ingestion/index.md) 참조하십시오.| `with (validationPolicy='{"ValidationOptions":1, "ValidationImplications":1}')`(이것은 실제로 기본 정책입니다)|
|`zipPattern`|ZIP 아카이브가 있는 저장소에서 데이터를 수집할 때 이 속성을 사용합니다. 이 값은 ZIP 아카이브에서 수집할 파일을 선택할 때 사용할 정규식을 나타내는 문자열 값입니다.  보관 파일의 다른 모든 파일은 무시됩니다.|`with (zipPattern="*.csv")`|

## <a name="next-steps"></a>다음 단계

* [데이터 수집에](/azure/data-explorer/ingest-data-overview) 대해 자세히 알아보기
* [지원되는 데이터 형식에](ingestion-supported-formats.md) 대해 자세히 알아보기
