---
title: 네이티브 코드의 오류-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 네이티브 코드의 오류에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/15/2020
ms.openlocfilehash: f1c076ba2c85ee18474372e4e19b285c133633d7
ms.sourcegitcommit: 0d15903613ad6466d49888ea4dff7bab32dc5b23
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "86013837"
---
# <a name="errors-in-native-code"></a>네이티브 코드 오류

Kusto 엔진은 네이티브 코드로 작성 되 고 음수 값을 사용 하 여 오류를 보고 합니다 `HRESULT` . 이러한 오류는 프로그래밍 방식 API에서 비정상 이지만 발생할 수 있습니다. 예를 들어 작업 실패는 " `Exception from HRESULT:` *HRESULT-CODE*" 상태를 표시할 수 있습니다.

Kusto 네이티브 오류 코드는 다음을 포함 하는 Windows 매크로를 사용 하 여 정의 됩니다 `MAKE-HRESULT` .

* 심각도 설정`1`
* 기능이로 설정 됩니다.`0xDA`
  
정의 되는 오류 코드는 다음과 같습니다.

|매니페스트 상수                  |코드  |값       |의미                                                                                                        |
|-----------------------------------|------|------------|---------------------------------------------------------------------------------------------------------------|
|`E_EXTENT_LOAD_FAILED`             | `0`  |`0x80DA0000`|데이터 범위를 로드할 수 없습니다.                                                                                 |
|`E_RUNAWAY_QUERY`                  | `1`  |`0x80DA0001`|허용 되는 리소스를 초과 하 여 쿼리 실행이 중단 되었습니다.                                              |
|`E_STREAM_FORMAT`                  | `2`  |`0x80DA0002`|형식이 잘못 되었기 때문에 데이터 스트림을 구문 분석할 수 없습니다.                                                     |
|`E_QUERY_RESULT_SET_TOO_LARGE`     | `3`  |`0x80DA0003`|이 쿼리의 결과 집합이 허용 되는 레코드/크기 제한을 초과 했습니다.                                         |
|`E_STREAM_ENCODING_VERSION`        | `4`  |`0x80DA0004`|해당 버전을 알 수 없으므로 결과 스트림을 구문 분석할 수 없습니다.                                                 |
|`E_KVDB_ERROR`                     | `5`  |`0x80DA0005`|포함 된 키/값 저장소 구성 요소에서 오류가 발생 했습니다.                                                              |
|`E_QUERY_CANCELLED`                | `6`  |`0x80DA0006`|쿼리가 취소 되었습니다.                                                                                             |
|`E_LOW_MEMORY_CONDITION`           | `7`  |`0x80DA0007`|메모리 부족 상태가 발생 했습니다.                                                                                  |
|`E_WRONG_NUMBER_OF_FIELDS`         | `8`  |`0x80DA0008`|입력 스트림 내의 필드 개수가 잘못 되었습니다.                                                                 |
|`E_INPUT_STREAM_TOO_LARGE`         | `9`  |`0x80DA0009`|필드/레코드/스트림의 입력 크기가 허용 길이를 초과 했습니다.                                          |
|`E_ENTITY_NOT_FOUND`               | `10` |`0x80DA000A`|요청 된 엔터티를 찾을 수 없습니다.                                                                              |
|`E_CLOSING_QUOTE_MISSING`          | `11` |`0x80DA000B`|수집을 위해 전송 된 csv 스트림에 따옴표가 누락 된 필드가 있습니다.                                          |
|`E_OVERFLOW`                       | `12` |`0x80DA000C`|산술 오버플로 오류를 나타냅니다. 계산 결과가 대상 형식에 비해 너무 깁니다.     |
|`E_INCOMPATIBLE_TOKENIZERS`        | `13` |`0x80DA000D`|인덱스 병합의 호환 되지 않는 토크 나이저으로 인해 익스텐트 병합이 실패 했습니다.                                    |
|`E_DYNAMIC_PROPERTY_BAG_TOO_LARGE` | `14` |`0x80DA000E`|속성 모음의 고유 키 조합 크기가 너무 깁니다.                                             |
|`E_RS_BLOCKED_ROWSTOREKEY_ERROR`   | `101`|`0x80DA0065`|차단 된 RowStore 키에 액세스 하려고 했습니다.                                                           |
|`E_RS_SHUTTINGDOWN_ERROR`          | `102`|`0x80DA0066`|RowStore를 종료 하는 중                                                                                      |
|`E_RS_LOCAL_STORAGE_FULL_ERROR`    | `103`|`0x80DA0067`|RowStore에 대 한 로컬 디스크 저장소가 꽉 찼습니다.                                                                        |
|`E_RS_CANNOT_READ_WRITE_AHEAD_LOG` | `104`|`0x80DA0068`|RowStore 미리 쓰기 로그 저장소에서 읽기 실패                                                           |
|`E_RS_CANNOT_RETRIEVE_VALUES_ERROR`| `105`|`0x80DA0069`|RowStore 저장소에서 값을 검색 하지 못했습니다.                                                               |
|`E_RS_NOT_READY_ERROR`             | `106`|`0x80DA006A`|RowStore를 초기화 하는 중                                                                                       |
|`E_RS_INSERTION_THROTTLED_ERROR`   | `107`|`0x80DA006B`|RowStore에 대 한 값 삽입이 제한 되었습니다.                                                                    |
|`E_RS_READONLY_ERROR`              | `108`|`0x80DA006C`|RowStore는 읽기 전용 상태로 연결 됩니다.                                                                        |
|`E_RS_UNAVAILABLE_ERROR`           | `109`|`0x80DA006D`|RowStore를 현재 사용할 수 없습니다.                                                                              |
|`E_RS_DOES_NOT_EXIST_ERROR`        | `110`|`0x80DA006E`|RowStore가 없습니다.                                                                                         |
|`E_SB_QUERY_THROTTLED_ERROR`       | `200`|`0x80DA00C8`|샌드 박싱된 쿼리가 정체 되었습니다.                                                                                  |
|`E_SB_QUERY_CANCELLED`             | `201`|`0x80DA00C9`|샌드박스 쿼리가 취소 되었습니다.                                                                                   |
|`E_UNSUPPORTED_INSTRUCTION_SET`    | `202`|`0x80DA00CA`|이 CPU에서 엔진의 필수 명령 집합을 지원 하지 않습니다.                                                   |