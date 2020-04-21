---
title: Kusto.WebExplorer - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 Kusto.WebExplorer에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 1f6926df09a207cfea2b9201ef57f36932a63f74
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523871"
---
# <a name="kustowebexplorer"></a>쿠스토.웹익스플로러

Kusto.WebExplorer는 Kusto 서비스에 쿼리 및 제어 명령을 보내는 데 사용할 수 있는 웹 응용 프로그램입니다. 응용 프로그램은 []https://dataexplorer.azure.com/에서 호스팅되고 []로https://aka.ms/kwe짧게 연결됩니다.



Kusto.WebExplorer는 HTML IFRAME의 다른 웹 포털에서 호스팅할 수도 있습니다.
(예를 들어, 이것은 [Azure 포털에](https://portal.azure.com)의해 수행됩니다.) [모나코 IDE를](../api/monaco/monaco-kusto.md) 호스팅하는 방법과 모나코 편집기를 사용하는 방법에 대한 자세한 내용을 참조하십시오.

## <a name="connect-to-multiple-clusters"></a>여러 클러스터에 연결

이제 여러 클러스터를 연결하고 데이터베이스와 클러스터 간에 전환할 수 있습니다.
이 도구는 연결된 클러스터와 데이터베이스를 쉽게 식별할 수 있도록 설계되었습니다.

![대체 텍스트](./Images/KustoTools-WebExplorer/AddingCluster.gif "클러스터 추가")

## <a name="recall-results"></a>리콜 결과

분석 중에 여러 쿼리를 실행하는 경우가 많으며 이전 쿼리의 결과를 다시 검토해야 할 수 있습니다. 이 기능을 사용하여 쿼리를 다시 실행할 필요 없이 결과를 회수할 수 있습니다. 데이터는 로컬 클라이언트 측 캐시에서 제공됩니다.

![대체 텍스트](./Images/KustoTools-WebExplorer/RecallResults.gif "리콜 결과")

## <a name="enhanced-results-grid-control"></a>향상된 결과 그리드 제어

테이블 그리드를 사용하면 여러 행, 열 및 셀을 선택할 수 있습니다. Excel과 같은 여러 셀을 선택하여 집계를 계산하고 데이터를 피벗합니다.

![대체 텍스트](./Images/KustoTools-WebExplorer/EnhancedGrid.gif "향상된 그리드")

## <a name="intellisense--formatting"></a>인텔리센스 & 서식

"Shift + Alt + F" 바로 가기 키, 코드 폴딩(개요) 및 IntelliSense를 사용하여 예쁜 인쇄 형식을 사용할 수 있습니다.

![대체 텍스트](./Images/KustoTools-WebExplorer/Formating.gif "Formating")

## <a name="deep-linking"></a>딥 링크

딥 링크 또는 딥 링크와 쿼리만 복사할 수 있습니다. 다음 템플릿을 사용하여 클러스터, 데이터베이스 및 쿼리를 포함하도록 URL을 포맷할 수도 있습니다.

`https://dataexplorer.azure.com/`[`clusters/` *Cluster* 클러스터`/databases/` [`?` *데이터베이스* [ *옵션*]]

다음 옵션을 지정할 수 있습니다.

* `workspace=empty`: 새 빈 작업 영역을 만들것임을 나타냅니다(이전 클러스터, 탭 및 쿼리에 대한 회수는 수행되지 않습니다).



![대체 텍스트](./Images/KustoTools-WebExplorer/DeepLink.gif "DeepLink")

## <a name="feedback"></a>사용자 의견

도구를 통해 피드백을 제출할 수 있습니다.
![대체 텍스트](./Images/KustoTools-WebExplorer/Feedback.gif "사용자 의견")