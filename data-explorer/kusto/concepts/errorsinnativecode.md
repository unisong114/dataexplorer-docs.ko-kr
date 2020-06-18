---
title: 네이티브 코드의 오류-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 네이티브 코드의 오류에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/15/2020
ms.openlocfilehash: 5446b98283a6533ceacd1c84fa3043732fe79e6a
ms.sourcegitcommit: a8575e80c65eab2a2118842e59f62aee0ff0e416
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2020
ms.locfileid: "84942729"
---
# <a name="errors-in-native-code"></a>네이티브 코드 오류

Kusto 엔진은 네이티브 코드로 작성 되 고 음수 값을 사용 하 여 오류를 보고 합니다 `HRESULT` . 이러한 오류는 일반적으로 프로그래밍 API에서 표시 되지 않지만 발생할 수 있습니다. 예를 들어 작업 실패는 " `Exception from HRESULT:` *HRESULT-CODE*" 상태를 표시할 수 있습니다.

Kusto 네이티브 오류 코드는 다음을 포함 하는 Windows 매크로를 사용 하 여 정의 됩니다 `MAKE-HRESULT` .

* 심각도 설정`1`
* 기능이로 설정 됩니다.`0xDA`
  
정의 되는 오류 코드는 다음과 같습니다.

|매니페스트 상수                  |코드 |값        |의미                                                                                                        |
|-----------------------------------|-----|-------------|---------------------------------------------------------------------------------------------------------------|
|`E_EXTENT_LOAD_FAILED`             | `0`  |`0x80DA0000`|데이터 분할 된 데이터를 로드할 수 없습니다.                                                                                  |
|`E_RUNAWAY_QUERY`                  | `1`  |`0x80DA0001`|허용 된 리소스를 초과 하 여 쿼리 실행이 중단 되었습니다.                                                   |
|`E_STREAM_FORMAT`                  | `2`  |`0x80DA0002`|형식이 잘못 되었기 때문에 데이터 스트림을 구문 분석할 수 없습니다.                                    |
|`E_QUERY_RESULT_SET_TOO_LARGE`     | `3`  |`0x80DA0003`|이 쿼리의 결과 집합이 허용 되는 레코드/크기 제한을 초과 했습니다.                                            |
|`E_STREAM_ENCODING_VERSION`        | `4`  |`0x80DA0004`|버전을 알 수 없으므로 결과 스트림을 구문 분석할 수 없습니다.                                                   |
|`E_KVDB_ERROR`                     | `5`  |`0x80DA0005`|키/값 데이터베이스 작업을 수행 하지 못했습니다.                                                              |
|`E_QUERY_CANCELLED`                | `6`  |`0x80DA0006`|쿼리가 취소 되었습니다.                 |
|`E_LOW_MEMORY_CONDITION`           | `7`  |`0x80DA0007`|사용 가능한 프로세스 메모리 부족으로 인해 작업이 중단 되었습니다.                                              |
|`E_WRONG_NUMBER_OF_FIELDS`         | `8`  |`0x80DA0008`|수집을 위해 전송 된 csv 문서에 잘못 된 수의 필드를 포함 하는 레코드가 있습니다 (다른 레코드에 상대적).|
|`E_INPUT_STREAM_TOO_LARGE`         | `9`  |`0x80DA0009`|수집 하기 위해 전송 된 문서가 허용 길이를 초과 했습니다.                                           |
|`E_ENTITY_NOT_FOUND`               | `10` |`0x80DA000A`|요청 된 엔터티를 찾을 수 없습니다.                                                                             |
|`E_CLOSING_QUOTE_MISSING`          | `11` |`0x80DA000B`|수집을 위해 전송 된 csv 문서에 따옴표가 누락 된 필드가 있습니다.                                        |
|`E_OVERFLOW`                       | `12` |`0x80DA000C`|산술 오버플로 오류를 나타냅니다 (계산 결과가 대상 형식에 비해 너무 큼).    |
|`E_RS_BLOCKED_ROWSTOREKEY_ERROR`   | `101`|`0x80DA0065`|차단 된 RowStore 키에 액세스 하려고 했습니다.                                                          |
|`E_RS_SHUTTINGDOWN_ERROR`          | `102`|`0x80DA0066`|RowStore를 종료 하는 중                                                                                     |
|`E_RS_LOCAL_STORAGE_FULL_ERROR`    | `103`|`0x80DA0067`|RowStore에 할당 된 로컬 디스크 저장소가 꽉 찼습니다.                                                             |
|`E_RS_CANNOT_READ_WRITE_AHEAD_LOG` | `104`|`0x80DA0068`|RowStore 저장소에서 읽지 못했습니다.                                                                      |
|`E_RS_CANNOT_RETRIEVE_VALUES_ERROR`| `105`|`0x80DA0069`|RowStore 저장소에서 값을 검색 하지 못했습니다.                                                              |
|`E_RS_NOT_READY_ERROR`             | `106`|`0x80DA006A`|RowStore를 초기화 하는 중                                                                                      |
|`E_RS_INSERTION_THROTTLED_ERROR`   | `107`|`0x80DA006B`|RowStore에 대 한 값 삽입이 제한 되었습니다.                                                                   |
|`E_RS_READONLY_ERROR`              | `108`|`0x80DA006C`|RowStore는 읽기 전용 상태로 연결 됩니다.                                                                       |
|`E_RS_UNAVAILABLE_ERROR`           | `109`|`0x80DA006D`|RowStore를 현재 사용할 수 없습니다.                                                                             |
|`E_RS_DOES_NOT_EXIST_ERROR`           | `110`|`0x80DA006E`| RowStore가 없습니다.                        |
|`E_SB_QUERY_THROTTLED_ERROR`           | `200`|`0x80DA00C8`|샌드 박싱된 쿼리가 정체 되었습니다.                                                                           |
|`E_SB_QUERY_CANCELLED`           | `201`|`0x80DA00C9`|샌드박스 쿼리가 취소 되었습니다.                                                                          |
|`E_UNSUPPORTED_INSTRUCTION_SET`           | `202`|`0x80DA00CA`|이 CPU에서 엔진의 필수 명령 집합을 지원 하지 않습니다.                                                                            |
