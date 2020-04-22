---
title: .create 테이블 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 .create 테이블에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: 1c84b9b6cec5a5bb7ab620871f59c188bf71cef4
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744242"
---
# <a name="create-table"></a>.create table

새 빈 테이블을 만듭니다.

명령은 특정 데이터베이스의 컨텍스트에서 실행되어야 합니다.

[데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요합니다.

**구문**

`.create``table` *테이블* 이름([열 이름:열유형], ...)  [`with` `(``docstring` `=` [ [`,` `folder` `=` *문서* `)`] [ 폴더 *이름*] ]

테이블이 이미 있는 경우 명령이 성공을 반환합니다.

**예제** 

```kusto
.create table MyLogs ( Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32 ) 
```
 
**출력 반환**

다음과 같이 JSON 형식으로 테이블스키마를 반환합니다.

```kusto
.show table MyLogs schema as json
```

> [!NOTE]
> 여러 테이블을 만들려면 [.create 테이블](/create-tables.md) 명령을 사용하여 성능을 향상시키고 클러스터의 부하를 줄입니다.

## <a name="create-merge-table"></a>.create-병합 테이블

새 테이블을 만들거나 기존 테이블을 확장합니다. 

명령은 특정 데이터베이스의 컨텍스트에서 실행되어야 합니다. 

[데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요합니다.

**구문**

`.create-merge``table` *테이블* 이름([열 이름:열유형], ...)  [`with` `(``docstring` `=` [ [`,` `folder` `=` *문서* `)`] [ 폴더 *이름*] ]

테이블이 없으면 ".create table" 명령과 정확히 함수됩니다.

테이블 T가 존재하고 ".create-merge 테이블 T<columns specification>()" 명령을 보내는 경우 다음을 수행합니다.

* 이전에 T에 <columns specification> 존재하지 않았던 열은 T스키마의 끝에 추가됩니다.
* T에 없는 <columns specification> 열은 T에서 제거되지 않습니다.
* T에 <columns specification> 있지만 다른 데이터 형식을 가진 모든 열은 명령이 실패하게 됩니다.
