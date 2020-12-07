---
title: . 데이터베이스 표시-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 데이터베이스 표시를 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: eb3e1dd16d36af5d92019b710f3d5790a24b1296
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320488"
---
# <a name="show-database"></a>.show database

컨텍스트 데이터베이스의 속성을 보여 주는 테이블을 반환 합니다.

모든 레코드가 사용자가 액세스할 수 있는 클러스터의 데이터베이스에 해당 하는 테이블을 반환 하려면를 참조 하십시오 [`.show databases`](show-databases.md) .

**구문**

`.show` `database` [`details` | `identity` | `policies` | `datastats`]

옵션이 지정 되지 않은 기본 호출은 ' identity ' 옵션과 같습니다.

**' Identity ' 옵션에 대 한 출력**
 
|출력 매개 변수 |형식 |설명 
|---|---|---
|DatabaseName  |String |데이터베이스 이름입니다. 데이터베이스 이름은 대/소문자를 구분 합니다. 
|PersistentStorage  |String |데이터베이스가 저장 되는 영구 저장소 URI입니다. 이 필드는 임시 데이터베이스에 대해 비어 있습니다. 
|버전  |String |데이터베이스 버전 번호입니다. 이 번호는 데이터베이스의 각 변경 작업 (예: 데이터 추가 및 스키마 변경)에 대해 업데이트 됩니다. 
|IsCurrent  |부울 |현재 연결이 가리키는 데이터베이스 이면 True입니다. 
|DatabaseAccessMode  |String |클러스터가 데이터베이스에 연결 되는 방법입니다. 예를 들어 데이터베이스가 ReadOnly 모드로 연결 된 경우 클러스터는 모든 요청에 대해 데이터베이스를 수정 하는 것을 실패 합니다. 
|PrettyName |String |데이터베이스 이름입니다.
|CurrentUserIsUnrestrictedViewer |부울 | 현재 사용자가 데이터베이스에서 제한 없는 뷰어 인지 여부를 지정 합니다.
|DatabaseId |Guid |데이터베이스의 고유 ID입니다.

**' Details ' 옵션에 대 한 출력**
 
|출력 매개 변수 |형식 |설명 
|---|---|---
|DatabaseName  |String |데이터베이스 이름입니다. 데이터베이스 이름은 대/소문자를 구분 합니다. 
|PersistentStorage  |String |데이터베이스가 저장 되는 영구 저장소 URI입니다. 이 필드는 임시 데이터베이스에 대해 비어 있습니다. 
|버전  |String |데이터베이스 버전 번호입니다. 이 번호는 데이터베이스의 각 변경 작업 (예: 데이터 추가 및 스키마 변경)에 대해 업데이트 됩니다. 
|IsCurrent  |부울 |현재 연결이 가리키는 데이터베이스 이면 True입니다. 
|DatabaseAccessMode  |String |클러스터가 데이터베이스에 연결 되는 방법입니다. 예를 들어 데이터베이스가 ReadOnly 모드로 연결 된 경우 클러스터는 모든 요청에 대해 데이터베이스를 수정 하는 것을 실패 합니다. 
|PrettyName |String |데이터베이스 이름입니다.
|AuthorizedPrincipals |String | 데이터베이스의 인증 된 보안 주체 컬렉션입니다 (JSON 형식으로 직렬화 됨).
|RetentionPolicy |String | 데이터베이스의 보존 정책입니다 (JSON 형식으로 직렬화 됨).
|MergePolicy |String | 데이터베이스의 익스텐트 병합 정책입니다 (JSON 형식으로 직렬화 됨).
|CachingPolicy |String | 데이터베이스의 캐싱 정책입니다 (JSON 형식으로 직렬화 됨).
|ShardingPolicy |String | 데이터베이스의 분할 정책입니다 (JSON 형식으로 직렬화 됨).
|StreamingIngestionPolicy |String | 데이터베이스의 스트리밍 수집 정책입니다 (JSON 형식으로 직렬화 됨).
|IngestionBatchingPolicy |String | 데이터베이스의 수집 일괄 처리 정책입니다 (JSON 형식으로 직렬화 됨).
|TotalSize |Real | 데이터베이스의 익스텐트 전체 크기입니다.
|DatabaseId |Guid |데이터베이스의 고유 ID입니다.

**' 정책 ' 옵션에 대 한 출력**
 
|출력 매개 변수 |형식 |설명 
|---|---|---
|DatabaseName  |String |데이터베이스 이름입니다. 데이터베이스 이름은 대/소문자를 구분 합니다. 
|PersistentStorage  |String |데이터베이스가 저장 되는 영구 저장소 URI입니다. 이 필드는 임시 데이터베이스에 대해 비어 있습니다. 
|버전  |String |데이터베이스 버전 번호입니다. 이 번호는 데이터베이스의 각 변경 작업 (예: 데이터 추가 및 스키마 변경)에 대해 업데이트 됩니다. 
|IsCurrent  |부울 |현재 연결이 가리키는 데이터베이스 이면 True입니다. 
|DatabaseAccessMode  |String |클러스터가 데이터베이스에 연결 되는 방법입니다. 예를 들어 데이터베이스가 ReadOnly 모드로 연결 된 경우 클러스터는 모든 요청에 대해 데이터베이스를 수정 하는 것을 실패 합니다. 
|PrettyName |String |데이터베이스의 이름입니다.
|DatabaseId |Guid |데이터베이스의 고유 ID입니다.
|AuthorizedPrincipals |String | 데이터베이스의 인증 된 보안 주체 컬렉션입니다 (JSON 형식으로 직렬화 됨).
|RetentionPolicy |String | 데이터베이스의 보존 정책입니다 (JSON 형식으로 직렬화 됨).
|MergePolicy |String | 데이터베이스의 익스텐트 병합 정책입니다 (JSON 형식으로 직렬화 됨).
|CachingPolicy |String | 데이터베이스의 캐싱 정책입니다 (JSON 형식으로 직렬화 됨).
|ShardingPolicy |String | 데이터베이스의 분할 정책입니다 (JSON 형식으로 직렬화 됨).
|StreamingIngestionPolicy |String | 데이터베이스의 스트리밍 수집 정책입니다 (JSON 형식으로 직렬화 됨).
|IngestionBatchingPolicy |String | 데이터베이스의 수집 일괄 처리 정책입니다 (JSON 형식으로 직렬화 됨).

**' Datastats ' 옵션에 대 한 출력**

|출력 매개 변수 |형식 |설명 
|---|---|---
|DatabaseName  |String |데이터베이스 이름입니다. 데이터베이스 이름은 대/소문자를 구분 합니다.
|PersistentStorage  |String |데이터베이스가 저장 되는 영구 저장소 URI입니다. 이 필드는 임시 데이터베이스에 대해 비어 있습니다. 
|버전  |String |데이터베이스 버전 번호입니다. 이 번호는 데이터베이스의 각 변경 작업 (예: 데이터 추가 및 스키마 변경)에 대해 업데이트 됩니다. 
|IsCurrent  |부울 |현재 연결이 가리키는 데이터베이스 이면 True입니다. 
|DatabaseAccessMode  |String |클러스터가 데이터베이스에 연결 되는 방법입니다. 예를 들어 데이터베이스가 ReadOnly 모드로 연결 된 경우 클러스터는 모든 요청에 대해 데이터베이스를 수정 하는 것을 실패 합니다. 
|PrettyName |String |데이터베이스의 이름입니다.
|DatabaseId |Guid |데이터베이스의 고유 ID입니다.
|OriginalSize |Real | 데이터베이스의 익스텐트 총 원래 크기입니다.
|ExtentSize |Real | 데이터베이스의 익스텐트 총 크기 (데이터 + 인덱스)입니다.
|CompressedSize |Real | 데이터베이스의 익스텐트 총 데이터 압축 크기입니다.
|IndexSize |Real | 데이터베이스의 익스텐트 총 인덱스 크기입니다.
|RowCount |long | 데이터베이스의 익스텐트 총 행 수입니다.
|HotOriginalSize |Real | 데이터베이스의 핫 익스텐트 총 원래 크기입니다.
|HotExtentSize |Real | 데이터베이스의 핫 익스텐트 전체 크기 (데이터 + 인덱스)입니다.
|HotCompressedSize |Real | 데이터베이스의 핫 익스텐트 총 데이터 압축 크기입니다.
|HotIndexSize |Real | 데이터베이스의 핫 익스텐트 총 인덱스 크기입니다.
|HotRowCount |long | 데이터베이스의 핫 익스텐트 총 행 수입니다.