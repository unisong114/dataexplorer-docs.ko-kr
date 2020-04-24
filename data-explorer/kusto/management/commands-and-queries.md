---
title: 명령 및 쿼리 관리 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 명령 및 쿼리 관리에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/19/2019
ms.openlocfilehash: f8c01709d69a0b439ce51b4782eb8f747db15d65
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521916"
---
# <a name="commands-and-queries-management"></a>명령 및 쿼리 관리

## <a name="show-commands-and-queries"></a>명령 및 쿼리를 .show 

`.show``commands-and-queries` 은 최종 상태에 도달한 관리자 명령 및 쿼리가 있는 테이블을 반환합니다. 이러한 명령 및 쿼리는 30일 동안 쿼리할 수 있습니다.

명령의 출력에 표시되는 정보는 [.show 명령](commands.md) 및 [.show 쿼리에서](queries.md)제공하는 정보와 유사하지만 기본적으로 두 결과 집합을 간단한 방식으로 결합할 수 있습니다.

**구문**

`.show` `commands-and-queries`
 
**출력**
 
출력 스키마는 다음과 같습니다.

| ColumnName               | ColumnType |
|--------------------------|------------|
| 클라이언트 활동 ID         | 문자열     |
| CommandType              | 문자열     |
| 텍스트                     | 문자열     |
| 데이터베이스                 | 문자열     |
| 시작 일                | Datetime   |
| 마지막 업데이트 켜기            | Datetime   |
| Duration                 | timespan   |
| 시스템 상태                    | 문자열     |
| 실패 이유            | 문자열     |
| RootActivityId           | guid       |
| 사용자                     | 문자열     |
| 애플리케이션              | 문자열     |
| 주 서버                | 문자열     |
| 클라이언트 요청 속성  | 동적    |
| 총 Cpu                 | timespan   |
| 메모리 피크               | long       |
| 캐시통계          | 동적    |
| 스캔익통계 | 동적    |
| 결과 집합 통계      | 동적    |

쿼리의 경우 의 `CommandType` 값은 `Query`입니다.