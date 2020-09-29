---
title: 구체화 된 뷰 명령 사용 및 사용 안 함-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 구체화 된 뷰 명령을 사용 하거나 사용 하지 않도록 설정 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: bb1fab3f211de4b33ca0dd2cee6a8cfa0cc796a9
ms.sourcegitcommit: 041272af91ebe53a5d573e9902594b09991aedf0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91452683"
---
# <a name="disable--enable-materialized-view"></a>.disable | .enable materialized-view

구체화 된 뷰는 다음과 같은 방법으로 사용 하지 않도록 설정할 수 있습니다.

* **시스템에서 자동으로 사용 안 함:**  구체화 된 뷰는 영구 오류로 인해 구체화가 실패할 경우 자동으로 비활성화 됩니다. 이 프로세스는 다음과 같은 경우에 발생할 수 있습니다. 
    * 뷰 정의와 일치 하지 않는 스키마 변경  
    * 구체화 된 뷰 쿼리가 의미 체계를 초래 하는 원본 테이블에 대 한 변경 내용이 잘못 되었습니다. 
* **구체화 된 뷰를 명시적으로 사용 하지 않도록 설정:**  구체화 된 뷰가 클러스터의 상태에 부정적인 영향을 주는 경우 (예: CPU를 너무 많이 사용 하는 경우) 아래 [명령을](#syntax) 사용 하 여 뷰를 사용 하지 않도록 설정 합니다.

> [!NOTE]
> * 구체화 된 뷰를 사용 하지 않도록 설정 하면 구체화가 일시 중지 되며 클러스터에서 리소스를 사용 하지 않습니다. 사용 하지 않도록 설정한 경우에도 구체화 된 뷰를 쿼리할 수 있지만 성능이 저하 될 수 있습니다. 사용 하지 않도록 설정 된 구체화 된 뷰의 성능은 사용 하지 않도록 설정 된 후에 원본 테이블에 수집 된 레코드의 수에 따라 달라 집니다. 
> * 이전에 사용 하지 않도록 설정 된 구체화 된 뷰를 사용 하도록 설정할 수 있습니다. 다시 사용 하도록 설정 하면 구체화 된 뷰는 중단 된 시점부터 계속 구체화 되며 레코드를 건너뛸 수 없습니다. 뷰가 오랫동안 사용 하지 않도록 설정 된 경우이를 파악 하는 데 시간이 오래 걸릴 수 있습니다.

뷰가 클러스터의 상태에 영향을 주는 것으로 의심 되는 경우에만 보기를 사용 하지 않는 것이 좋습니다.

## <a name="syntax"></a>구문

`.enable` | `disable``materialized-view` *MaterializedViewName*

## <a name="properties"></a>속성

|속성|유형|설명
|----------------|-------|---|
|MaterializedViewName|String|구체화 된 뷰의 이름입니다.|

## <a name="example"></a>예제

```kusto
.enable materialized-view ViewName

.disable materialized-view ViewName
```