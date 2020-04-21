---
title: 네이티브 코드오류 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 네이티브 코드의 오류를 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/12/2019
ms.openlocfilehash: fa36bec3586a5e316b08ebc2949617268f0270ab
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523259"
---
# <a name="errors-in-native-code"></a>네이티브 코드 오류

Kusto 엔진은 기본 코드로 작성되며 음수 `HRESULT` 값을 사용하여 오류를 보고합니다. 이러한 오류는 일반적으로 프로그래밍 방식 API에서는 표시되지 않지만 발생할 수 있습니다. 예를 들어 작업 실패는`Exception from HRESULT:` *"HRESULT-CODE"의*상태를 나타낼 수 있습니다.

Kusto 기본 오류 코드는 `MAKE-HRESULT` 다음과 같이 Windows 매크로를 사용하여 정의됩니다.

* 심각도가`1`
* 시설 설정`0xDA`
  
다음 오류 코드가 정의됩니다.

|매니페스트 상수                  |코드 |값        |의미                                                                                                        |
|-----------------------------------|-----|-------------|---------------------------------------------------------------------------------------------------------------|
|`E_EXTENT_LOAD_FAILED`             | `0`  |`0x80DA0000`|데이터 샤드를 로드할 수 없습니다.                                                                                  |
|`E_RUNAWAY_QUERY`                  | `1`  |`0x80DA0001`|허용된 리소스를 초과하면 쿼리 실행이 중단되었습니다.                                                   |
|`E_STREAM_FORMAT`                  | `2`  |`0x80DA0002`|데이터 스트림의 형식이 잘못되어 구문 분석할 수 없습니다.                                                      |
|`E_QUERY_RESULT_SET_TOO_LARGE`     | `3`  |`0x80DA0003`|이 쿼리에 대한 결과 집합이 허용된 레코드/크기 제한을 초과합니다.                                            |
|`E_STREAM_ENCODING_VERSION`        | `4`  |`0x80DA0004`|버전을 알 수 없으므로 결과 스트림을 구문 분석할 수 없습니다.                                                   |
|`E_KVDB_ERROR`                     | `5`  |`0x80DA0005`|키/값 데이터베이스 작업을 수행하지 못합니다.                                                              |
|`E_QUERY_CANCELLED`                | `6`  |`0x80DA0006`|쿼리가 취소되었습니다.                                                                                            |
|`E_LOW_MEMORY_CONDITION`           | `7`  |`0x80DA0007`|사용 가능한 프로세스 메모리가 부족하여 작업이 중단되었습니다.                                              |
|`E_WRONG_NUMBER_OF_FIELDS`         | `8`  |`0x80DA0008`|인비전에 제출된 csv 문서에 잘못된 필드 수의 레코드가 있습니다(다른 레코드에 비해).|
|`E_INPUT_STREAM_TOO_LARGE`         | `9`  |`0x80DA0009`|허용 된 길이를 초과 했습니다.                                           |
|`E_ENTITY_NOT_FOUND`               | `10` |`0x80DA000A`|요청된 엔터티를 찾을 수 없습니다.                                                                             |
|`E_CLOSING_QUOTE_MISSING`          | `11` |`0x80DA000B`|인비전에 제출된 csv 문서에 누락된 견적이 있는 필드가 있습니다.                                        |
|`E_OVERFLOW`                       | `12` |`0x80DA000C`|산술 오버플로 오류를 나타냅니다(계산 결과가 대상 유형에 비해 너무 큽니다).    |
|`E_RS_BLOCKED_ROWSTOREKEY_ERROR`   | `101`|`0x80DA0065`|차단된 행 저장소 키에 액세스하려고 시도했습니다.                                                          |
|`E_RS_SHUTTINGDOWN_ERROR`          | `102`|`0x80DA0066`|행 저장소가 종료됩니다.                                                                                     |
|`E_RS_LOCAL_STORAGE_FULL_ERROR`    | `103`|`0x80DA0067`|행 저장소 저장소에 할당된 디스크 공간이 가득 찼습니다.                                                             |
|`E_RS_CANNOT_READ_WRITE_AHEAD_LOG` | `104`|`0x80DA0068`|행 저장소 저장소에서 읽기가 실패했습니다.                                                                      |
|`E_RS_CANNOT_RETRIEVE_VALUES_ERROR`| `105`|`0x80DA0069`|행 저장소 저장소에서 값을 검색하지 못함                                                              |
|`E_RS_NOT_READY_ERROR`             | `106`|`0x80DA006A`|행 저장소가 초기화되고 있습니다.                                                                                      |
|`E_RS_INSERTION_THROTTLED_ERROR`   | `107`|`0x80DA006B`|행 저장소에 대한 값 삽입이 제한되었습니다.                                                                   |
|`E_RS_READONLY_ERROR`              | `108`|`0x80DA006C`|행 저장소가 읽기 전용 상태로 연결됩니다.                                                                       |
|`E_RS_UNAVAILABLE_ERROR`           | `109`|`0x80DA006D`|행 저장소를 현재 사용할 수 없습니다.                                                                             |