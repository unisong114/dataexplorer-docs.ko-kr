---
title: Kusto-Azure 데이터 탐색기 동기화
description: 이 문서에서는 Azure 데이터 탐색기에서 Kusto 동기화에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: zivc
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/12/2019
ms.openlocfilehash: 6e1cd00cb84ad7b4d62429a932d3ffe298a83766
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875194"
---
# <a name="sync-kusto"></a>Kusto 동기화

Sync Kusto는 사용자가 테이블 스키마 및 저장 된 함수와 같은 다양 한 Kusto 스키마 엔터티를 동기화 할 수 있도록 하는 도구입니다. 이러한 동기화는 로컬 파일 시스템, Azure 데이터 탐색기 데이터베이스 및 Azure Dev Ops 리포지토리 사이에서 수행 됩니다.

Kusto 동기화는 [GitHub에서](https://github.com/microsoft/synckusto)사용할 수 있습니다.