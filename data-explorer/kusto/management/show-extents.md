---
title: . 익스텐트 표시-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 익스텐트 표시 명령을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: c0e2ffa76becc747b03b907dfe8a356e4c1a49a3
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060709"
---
# <a name="show-extents"></a>. 익스텐트 표시

지정 된 데이터베이스 또는 테이블의 익스텐트를 표시 합니다.

> [!NOTE]
> 데이터 분할은 Kusto에서 **익스텐트에서** 호출 되며 모든 명령은 "익스텐트" 또는 "익스텐트"를 동의어로 사용 합니다.
> 익스텐트에 대 한 자세한 내용은 [익스텐트 (데이터 분할) 개요](extents-overview.md)를 참조 하세요.

## <a name="cluster-level"></a>클러스터 수준

`.show` `cluster` `extents` [`hot`]

클러스터에 있는 익스텐트 (데이터 분할)에 대 한 정보를 표시 합니다.
`hot`이 지정 된 경우-핫 캐시에 있을 것으로 예상 되는 익스텐트가 표시 됩니다.

## <a name="database-level"></a>데이터베이스 수준

`.show``database` *DatabaseName* `extents` [ `(` *ExtentId1* `,` ExtentId1 `,` *Extentidn* `)` ] [ `hot` ] [ `where` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag1* [ `and` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag2*]]

지정 된 데이터베이스에 있는 익스텐트 (데이터 분할)에 대 한 정보를 표시 합니다.
`hot`을 지정한 경우-핫 캐시에 있을 것으로 예상 되는 익스텐트가 표시 됩니다.

## <a name="table-level"></a>테이블 수준

`.show``table` *TableName* `extents` [ `(` *ExtentId1* `,` ExtentId1 `,` ... *Extentidn* `)` ] [ `hot` ] [ `where` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag1* [ `and` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag2*]]

`.show``tables` `(` *TableName1* `,` TableName1 ... `,` *TableNameN* `)` `extents`[ `(` *ExtentId1* `,` ExtentId1 `,` ... *Extentidn* `)` ] [ `hot` ] [ `where` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag1* [ `and` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag2*]]

지정 된 테이블에 있는 익스텐트 (데이터 분할)에 대 한 정보를 표시 합니다. 데이터베이스는 명령의 컨텍스트에서 가져옵니다.
을 `hot` 지정 하면는 핫 캐시에 있을 것으로 예상 되는 익스텐트만 표시 합니다.

## <a name="notes"></a>참고

* 데이터베이스 또는 테이블 수준 명령을 사용 하면 `| where DatabaseName == '...' and TableName == '...'` 클러스터 수준 명령의 결과를 필터링 (추가) 하는 것 보다 훨씬 빠릅니다.
* 익스텐트 Id의 선택적 목록이 제공 되는 경우 반환 된 데이터 집합은 해당 익스텐트로만 제한 됩니다.
    * 이 방법은 `| where ExtentId in(...)` "완전" 명령의 결과를 필터링 (에 추가) 하는 것 보다 훨씬 빠릅니다.
* `tags`필터를 지정 하는 경우:
    * 반환 된 목록은 태그 컬렉션이 제공 된 *모든* 태그 필터를 따르는 하는 범위로 제한 됩니다.
    * 이 방법은 `| where Tags has '...' and Tags contains '...'` "완전" 명령의 결과를 필터링 (에 추가) 하는 것 보다 훨씬 빠릅니다.
    * `has`필터는 같음 필터입니다. 지정 된 태그 중 하나로 태그가 지정 되지 않은 익스텐트가 필터링 됩니다.
    * `!has`필터는 같음 부정 필터입니다. 지정 된 태그 중 하나를 사용 하 여 태그가 지정 된 익스텐트는 필터링 됩니다.
    * `contains`필터는 대/소문자를 구분 하지 않는 부분 문자열 필터입니다. 지정 된 문자열이 해당 태그의 하위 문자열로 지정 되지 않은 익스텐트는 필터링 됩니다.
    * `!contains`필터는 대/소문자를 구분 하지 않는 부분 문자열 음수 필터입니다. 지정 된 문자열이 해당 태그의 하위 문자열로 지정 된 익스텐트는 필터링 됩니다.
  
## <a name="output-parameters"></a>출력 매개 변수

|출력 매개 변수 |형식 |설명 |
|---|---|---|
|ExtentId |Guid |익스텐트의 ID 
|DatabaseName |String |범위가 속한 데이터베이스입니다.
|TableName |String |익스텐트가 속하는 테이블
|MaxCreatedOn |DateTime |익스텐트를 만든 날짜/시간입니다. 병합 된 익스텐트의 경우 원본 익스텐트 간의 최대 생성 시간
|OriginalSize |Double |익스텐트 데이터의 원래 크기 (바이트)입니다.
|ExtentSize |Double |메모리 내 익스텐트 크기 (압축 된 + 인덱스)
|CompressedSize |Double |메모리의 익스텐트 데이터 압축 크기
|IndexSize |Double |익스텐트 데이터의 인덱스 크기
|블록 |Long |익스텐트의 데이터 블록 수
|세그먼트 |Long |익스텐트의 데이터 세그먼트 수
|AssignedDataNodes |String | 사용 되지 않음 (빈 문자열)
|LoadedDataNodes |String |사용 되지 않음 (빈 문자열)
|ExtentContainerId |String | 범위가 있는 익스텐트 컨테이너의 ID입니다.
|RowCount |Long |익스텐트의 행 수
|MinCreatedOn |DateTime |익스텐트를 만든 날짜/시간입니다. 병합 된 익스텐트의 경우 원본 익스텐트 중 최소 생성 시간
|Tags|String|범위에 대해 정의 된 태그 (있는 경우)
 
## <a name="examples"></a>예

### <a name="tagged-extent"></a>태그가 지정 된 범위

`E`테이블의 익스텐트 `T` 는 태그, 및 태그가 지정 됩니다 `aaa` `BBB` `ccc` .

* 이 쿼리는 다음을 반환 합니다 `E` .
    
   ```kusto
    .show table T extents where tags has 'aaa' and tags contains 'bb'
   ```
   
* 이 쿼리 `E` 는 태그가 지정 되지 않으므로 반환 되지 않습니다 `aa` .
    
   ```kusto
    .show table T extents where tags has 'aa' and tags contains 'bb'
   ```
    
* 이 쿼리는 다음을 반환 합니다 `E` .
    
   ```kusto
    .show table T extents where tags contains 'aaa' and tags contains 'bb' 
   ```

### <a name="show-volume-of-extents-created"></a>만든 익스텐트의 볼륨 표시

특정 데이터베이스에서 시간당 만들어지는 익스텐트의 볼륨 표시

```kusto 
.show database MyDatabase extents | summarize count(ExtentId) by MaxCreatedOn bin=time(1h) | render timechart  
```

### <a name="show-volume-of-data-arriving-by-table-per-hour"></a>시간당 테이블에 도착 한 데이터 볼륨 표시

```kusto 
.show database MyDatabase extents  
| summarize sum(OriginalSize) by TableName, MaxCreatedOn bin=time(1h)  
| render timechart
```

### <a name="show-data-size-distribution-by-table"></a>테이블 별로 데이터 크기 분포 표시

```kusto 
.show database MyDatabase extents | summarize sum(OriginalSize) by TableName
```

### <a name="show-all-extents-in-the-database-named-gamesdb"></a>' GamesDB ' 라는 데이터베이스의 모든 익스텐트를 표시 합니다.

```kusto 
.show database GamesDB extents
```

### <a name="show-all-extents-in-the-table-named-games"></a>' 게임 ' 이라는 테이블의 모든 익스텐트를 표시 합니다.

```kusto 
.show table Games extents
```

### <a name="show-all-extents-in-specific-tables"></a>특정 테이블의 모든 익스텐트 표시

' Tag1 ' 및 ' tag2 ' 모두로 태그가 지정 된 ' TaggingGames1 ' 및 ' TaggingGames2 ' 이라는 테이블의 모든 익스텐트를 표시 합니다.

```kusto 
.show tables (TaggingGames1,TaggingGames2) extents where tags has 'tag1' and tags has 'tag2'
```
