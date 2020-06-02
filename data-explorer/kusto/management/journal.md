---
title: 저널 관리-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 저널 관리에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/19/2019
ms.openlocfilehash: 64b0f8179a328ce811747b05a90516fd8b6029be
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294409"
---
# <a name="journal-management"></a>저널 관리

 `Journal`Azure 데이터 탐색기 데이터베이스에서 수행 되는 메타 데이터 작업에 대 한 정보를 포함 합니다.

메타 데이터 작업은 사용자가 실행 한 제어 명령이 나 시스템에서 실행 하는 내부 제어 명령 (예: 보존에의 한 익스텐트 삭제)으로 인해 발생할 수 있습니다.

> [!NOTE]
> * 새 범위 (예:, 및 기타) *추가* 를 포함 하는 메타 데이터 작업은에 표시 되는 `.ingest` `.append` `.move` 일치 하는 이벤트를 포함 하지 않습니다 `Journal` .
> * 결과 집합의 열과 해당 열이 표시 되는 형식에 대 한 데이터는 계약 되지 않습니다. 
  종속성을 만드는 것은 권장 되지 않습니다.

|이벤트        |EventTimestamp     |데이터베이스  |EntityName|UpdatedEntityName|EntityVersion|EntityContainerName|
|-------------|-------------------|----------|----------|-----------------|-------------|-------------------|
|테이블 만들기 |2017-01-05 14:25:07|InternalDb|MyTable1  |MyTable1         |v1.0         |InternalDb         |
|테이블 이름 바꾸기 |2017-01-13 10:30:01|InternalDb|MyTable1  |MyTable2         |v8.0         |InternalDb         |  

|OriginalEntityState|UpdatedEntityState                                              |ChangeCommand                                                                                                          |주 서버            |
|-------------------|----------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|---------------------|
|.                  |이름: MyTable1, Attributes: Name = ' [MyTable1]. [col1] ', Type = ' I32 '|. create table MyTable1 (col1: int)                                                                                      |imike@fabrikam.com
|.                  |데이터베이스 속성 (너무 길어서 여기에 표시할 수 없음)         |. create database TestDB persist (@ " https://imfbkm.blob.core.windows.net/md ", @ " https://imfbkm.blob.core.windows.net/data ")|Azure AD 앱 id = 762633545644e9c404
|이름: MyTable1, Attributes: Name = ' [MyTable1]. [col1] ', Type = ' I32 '|이름: MyTable2, Attributes: Name = ' [MyTable1]. [col1] ', Type = ' I32 '|. MyTable1 테이블의 이름을 MyTable2로 바꿉니다.|rdmik@fabrikam.com

|항목                 |Description                                                              |                                
|---------------------|-------------------------------------------------------------------------|
|이벤트                |메타 데이터 이벤트 이름입니다.                                                  |
|EventTimestamp       |이벤트 타임 스탬프입니다.                                                      |                        
|데이터베이스             |이벤트에 따라이 데이터베이스의 메타 데이터가 변경 되었습니다.                |
|EntityName           |변경 하기 전에 작업이 실행 된 엔터티 이름입니다.    |
|UpdatedEntityName    |변경 후의 새 엔터티 이름입니다.                                     |
|EntityVersion        |변경을 수행 하는 새 메타 데이터 버전 (db/클러스터)               |
|EntityContainerName  |엔터티 컨테이너 이름 (entity = column, container = table)               |
|OriginalEntityState  |변경 전의 엔터티 (엔터티 속성) 상태입니다.            |
|UpdatedEntityState   |변경 후의 새 상태                                           |
|ChangeCommand        |메타 데이터 변경을 트리거한 실행 된 제어 명령입니다.          |
|주 서버            |제어 명령을 실행 한 보안 주체 (사용자/앱)               |
    
## <a name="show-journal"></a>. 저널 표시

`.show journal`이 명령은 사용자에 게 관리자 액세스 권한이 있는 데이터베이스 또는 클러스터의 메타 데이터 변경 목록을 반환 합니다.

**권한**

모든 사람 (클러스터 액세스)에서 명령을 실행할 수 있습니다. 

반환 되는 결과는 다음과 같습니다. 
- 명령을 실행 하는 사용자의 모든 저널 항목입니다. 
- 사용자가 명령을 실행 하는 데이터베이스의 모든 저널 항목에 대 한 관리자 액세스 권한이 있습니다. 
- 명령을 실행 하는 사용자가 클러스터 관리자 인 경우 모든 클러스터 저널 항목이 사용 됩니다. 

## <a name="show-database-databasename-journal"></a>. 데이터베이스 *DatabaseName* 저널 표시 

`.show` `database` *DatabaseName* `journal` 명령은 특정 데이터베이스 메타 데이터 변경 내용에 대 한 저널을 반환 합니다.

**권한**

모든 사람 (클러스터 액세스)에서 명령을 실행할 수 있습니다. 반환 되는 결과는 다음과 같습니다. 
- 명령을 실행 하는 사용자가 *databasename*의 데이터베이스 관리자 인 경우 데이터베이스 *databasename* 의 모든 저널 항목입니다. 
- 그렇지 않으면 데이터베이스의 모든 저널 항목과 `DatabaseName` 명령을 실행 하는 사용자의입니다. 
