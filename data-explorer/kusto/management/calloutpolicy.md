---
title: 콜아웃 정책 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 호출 처리 정책에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: df57c4901cef74d574108d2c6e75672d1faba75c
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744505"
---
# <a name="callout-policy"></a>설명선 정책

## <a name="overview"></a>개요

Azure Data Explorer 클러스터는 다양한 시나리오에서 외부 서비스와 통신할 수 있습니다.
클러스터 관리자는 클러스터의 콜아웃 정책을 업데이트하여 외부 호출에 대해 허용된 도메인을 관리할 수 있습니다.

콜아웃 정책은 클러스터 수준에서 관리되고 있으며 다음 유형으로 분류됩니다.
* `kusto`- Azure 데이터 탐색기 교차 클러스터 쿼리를 제어합니다.
* `sql`- [SQL 플러그인을](../query/sqlrequestplugin.md)제어합니다.


* `webapi`- 다른 외부 웹 호출을 제어합니다.
* `sandbox_artifacts`- 샌드 박스 플러그인[(파이썬](../query/pythonplugin.md) | [R)을](../query/rplugin.md)제어합니다.

콜아웃 정책은 다음 구성 요소로 구성됩니다.
* **CalloutType은** 콜아웃의 유형을 정의하며 kusto, sql 또는 webapi 중 하나가 될 수 있습니다.
* **콜아웃UriRegex는** 콜아웃 도메인의 허용된 정규식을 지정합니다.
* **CanCall은** 콜아웃이 외부 호출을 허용하는지 여부를 나타냅니다.

## <a name="predefined-callout-policies"></a>미리 정의된 선외 정책

서비스를 선택하는 콜아웃을 용이하게 하기 위해 모든 Azure Data Explorer 클러스터에 불변으로 미리 정의된 콜아웃 정책 집합이 있습니다.

|서비스      |클라우드        |명칭  |허용된 도메인 |
|-------------|-------------|-------------|-------------|
|Kusto |공용 Azure |클러스터 간 쿼리 |`^[^.]*\.kusto\.windows\.net$` <br> `^[^.]*\.kustomfa\.windows\.net$` |
|Kusto |검은 숲 |클러스터 간 쿼리 |`^[^.]*\.kusto\.cloudapi\.de$` <br> `^[^.]*\.kustomfa\.cloudapi\.de$` |
|Kusto |Fairfax |클러스터 간 쿼리 |`^[^.]*\.kusto\.usgovcloudapi\.net$` <br> `^[^.]*\.kustomfa\.usgovcloudapi\.net$` |
|Kusto |문케이크 |클러스터 간 쿼리 |`^[^.]*\.kusto\.chinacloudapi\.cn$` <br> `^[^.]*\.kustomfa\.chinacloudapi\.cn$` |
|Azure DB |공용 Azure |SQL 요청 |`^[^.]*\.database\.windows\.net$` <br> `^[^.]*\.databasemfa\.windows\.net$` |
|Azure DB |검은 숲 |SQL 요청 |`^[^.]*\.database\.cloudapi\.de$` <br> `^[^.]*\.databasemfa\.cloudapi\.de$` |
|Azure DB |Fairfax |SQL 요청 |`^[^.]*\.database\.usgovcloudapi\.net$` <br> `^[^.]*\.databasemfa\.usgovcloudapi\.net$` |
|Azure DB |문케이크 |SQL 요청 |`^[^.]*\.database\.chinacloudapi\.cn$` <br> `^[^.]*\.databasemfa\.chinacloudapi\.cn$` |
|베이스라이닝 서비스 |공용 Azure |베이스라이닝 요청 |`baseliningsvc-int.azurewebsites.net` <br> `baseliningsvc-ppe.azurewebsites.net` <br> `baseliningsvc-prod.azurewebsites.net` |


## <a name="control-commands"></a>제어 명령

명령에는 [AllDatabasesAdmin](access-control/role-based-authorization.md) 권한이 필요합니다.

**구성된 모든 콜아웃 정책 표시**

```kusto
.show cluster policy callout
```

**종료 콜아웃 정책 변경**

```kusto
.alter cluster policy callout @'[{"CalloutType": "webapi","CalloutUriRegex": "en\\.wikipedia\\.org","CanCall": true}]'
```

**허용된 콜아웃 세트 추가**

```kusto
.alter-merge cluster policy callout @'[{"CalloutType": "webapi","CalloutUriRegex": "en\\.wikipedia\\.org","CanCall": true}, {"CalloutType": "webapi","CalloutUriRegex": "bing\\.com","CanCall": true}]'
```

**변경할 수 없는 모든 콜아웃 정책 삭제**

```kusto
.delete cluster policy callout
```
