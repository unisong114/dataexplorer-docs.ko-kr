---
title: 개요 - Azure Data Explorer
description: 이 문서에서는 Azure Data Explorer의 개요를 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/07/2019
ms.openlocfilehash: f6e91beaf52e19a0571efa3f29aa4e67a5ef4353
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241543"
---
# <a name="overview"></a>개요

Kusto 쿼리는 데이터를 처리하고 결과를 반환하기 위한 읽기 전용 요청입니다.
요청은 구문을 쉽게 읽고 작성하고 자동화할 수 있는 데이터 흐름 모델을 사용하여 일반 텍스트로 작성됩니다. 쿼리는 SQL의 데이터베이스, 테이블 및 열과 비슷한 계층 구조로 구성된 스키마 엔터티를 사용합니다.

쿼리는 세미콜론(`;`)으로 구분된 일련의 쿼리 문으로 구성됩니다. 하나 이상의 명령문은 테이블 형식의 열/행 메시로 정렬된 데이터를 생성하는 [테이블 형식 식 명령문](tabularexpressionstatements.md)입니다. 쿼리의 테이블 형식 식 문이 쿼리 결과를 생성합니다.

테이블 형식 식 문의 구문에는 테이블 형식 쿼리 연산자 간의 테이블 형식 데이터 흐름이 있습니다. 이 흐름은 데이터 원본(예: 데이터베이스의 테이블 또는 데이터를 생성하는 연산자)에서 시작한 다음, 파이프(`|`) 구분 기호를 사용하여 함께 바인딩된 데이터 변환 연산자 세트를 통해 이동합니다.

예를 들어 다음 Kusto 쿼리에는 테이블 형식 식 명령문인 단일 명령문이 포함되어 있습니다. 이 문은 `StormEvents` 테이블에 대한 참조로 시작됩니다. 이 테이블을 호스트하는 데이터베이스는 연결 문자열의 일부인 암시적 항목입니다. 해당 테이블의 데이터(행)는 `StartTime` 열의 값과 `State` 열의 값을 기준으로 하여 차례로 필터링됩니다. 그리고 나면 쿼리가 필터링 후에 남은 행의 개수를 반환합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where StartTime >= datetime(2007-11-01) and StartTime < datetime(2007-12-01)
| where State == "FLORIDA"  
| count 
```

이 쿼리를 실행하려면 [여기](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAwsuyS/KdS1LzSspVuDlqlEoz0gtSlUILkksKgnJzE1VsLNVSEksSS0BsjWMDAzMdQ0NdQ0MNRUS81KQVNmgKzICKUIxryRVwdZWQcnNxz/I08VRSQFsW3J+aV6JAgAwMx4+hAAAAA==)를 클릭합니다.
이 경우 결과는 다음과 같습니다.

|개수|
|-----|
|   23|
