---
title: 콜아웃 정책-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 콜아웃 정책에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 6e3bb943347e4ea794733451fcf65674e5e23ca7
ms.sourcegitcommit: 041272af91ebe53a5d573e9902594b09991aedf0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91452666"
---
# <a name="callout-policy"></a>설명선 정책

Azure 데이터 탐색기 클러스터는 다양 한 시나리오에서 외부 서비스와 통신할 수 있습니다.
클러스터 관리자는 클러스터의 콜아웃 정책을 업데이트 하 여 외부 호출에 대해 권한 있는 도메인을 관리할 수 있습니다.

콜아웃 정책은 클러스터 수준에서 관리 되며 다음 유형으로 분류 됩니다.
* `kusto` -Azure 데이터 탐색기 클러스터 간 쿼리를 제어 합니다.
* `sql` - [SQL 플러그 인](../query/sqlrequestplugin.md)을 제어 합니다.
* `cosmosdb` - [CosmosDB 플러그 인](../query/cosmosdb-plugin.md)을 제어 합니다.
* `sandbox_artifacts`-샌드박스 플러그 인 ([python](../query/pythonplugin.md)  |  [R](../query/rplugin.md))을 제어 합니다.
* `external_data` - [외부 테이블](../query/schema-entities/externaltables.md) 또는 [externaldata](../query/externaldata-operator.md) 연산자를 통해 외부 데이터에 대 한 액세스를 제어 합니다.

콜아웃 정책은 다음과 같이 구성 됩니다.

* **Callouttype** -설명선의 형식을 정의 하며, 또는 일 수 있습니다 `kusto` `sql` .
* **Callouturiregex** -설명선 도메인의 허용 된 regex를 지정 합니다.
* **Cancall** -설명선이 외부 호출을 허용 하는지 여부를 나타냅니다.

## <a name="predefined-callout-policies"></a>미리 정의 된 콜아웃 정책

다음 표에서는 모든 Azure 데이터 탐색기 클러스터에서 미리 구성 된 미리 정의 된 일련의 콜아웃 정책을 보여 주며,이를 통해 콜아웃에서 서비스를 선택할 수 있습니다.

|서비스      |클라우드        |명칭  |허용 된 도메인 |
|-------------|-------------|-------------|-------------|
|Kusto |`Public Azure` |클러스터 간 쿼리 |`^[^.]*\.kusto\.windows\.net$` <br> `^[^.]*\.kustomfa\.windows\.net$` |
|Kusto |`Black Forest` |클러스터 간 쿼리 |`^[^.]*\.kusto\.cloudapi\.de$` <br> `^[^.]*\.kustomfa\.cloudapi\.de$` |
|Kusto |`Fairfax` |클러스터 간 쿼리 |`^[^.]*\.kusto\.usgovcloudapi\.net$` <br> `^[^.]*\.kustomfa\.usgovcloudapi\.net$` |
|Kusto |`Mooncake` |클러스터 간 쿼리 |`^[^.]*\.kusto\.chinacloudapi\.cn$` <br> `^[^.]*\.kustomfa\.chinacloudapi\.cn$` |
|Azure DB |`Public Azure` |SQL 요청 |`^[^.]*\.database\.windows\.net$` <br> `^[^.]*\.databasemfa\.windows\.net$` |
|Azure DB |`Black Forest` |SQL 요청 |`^[^.]*\.database\.cloudapi\.de$` <br> `^[^.]*\.databasemfa\.cloudapi\.de$` |
|Azure DB |`Fairfax` |SQL 요청 |`^[^.]*\.database\.usgovcloudapi\.net$` <br> `^[^.]*\.databasemfa\.usgovcloudapi\.net$` |
|Azure DB |`Mooncake` |SQL 요청 |`^[^.]*\.database\.chinacloudapi\.cn$` <br> `^[^.]*\.databasemfa\.chinacloudapi\.cn$` |
|기준 기준 서비스 |공용 Azure |기준 기준 요청 |`baseliningsvc-int.azurewebsites.net` <br> `baseliningsvc-ppe.azurewebsites.net` <br> `baseliningsvc-prod.azurewebsites.net` |

## <a name="control-commands"></a>제어 명령

명령에는 [AllDatabasesAdmin](access-control/role-based-authorization.md) 권한이 필요 합니다.

**구성 된 모든 콜아웃 정책 표시**

```kusto
.show cluster policy callout
```

**콜아웃 정책 변경**

```kusto
.alter cluster policy callout @'[{"CalloutType": "sql","CalloutUriRegex": "sqlname.database.azure.com","CanCall": true}]'
```

**허용 되는 설명선 집합 추가**

```kusto
.alter-merge cluster policy callout @'[{"CalloutType": "sql","CalloutUriRegex": "sqlname.database.azure.com","CanCall": true}]'
```

**변경할 수 없는 모든 콜아웃 정책을 삭제 합니다.**

```kusto
.delete cluster policy callout
```
