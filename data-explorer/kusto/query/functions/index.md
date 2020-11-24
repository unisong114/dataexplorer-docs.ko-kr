---
title: 함수 - Azure Data Explorer | Microsoft Docs
description: 이 문서에서는 Azure Data Explorer의 함수에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.localizationpriority: high
ms.openlocfilehash: 1e0b3f339a755531d8db146ed2dc478ebb5a888d
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513032"
---
# <a name="function-types"></a>함수 형식

**함수** 는 다시 사용할 수 있는 쿼리 또는 쿼리 부분입니다. Kusto에서 지원하는 여러 종류의 함수는 다음과 같습니다.

* **저장 함수** - 한 종류의 데이터베이스 스키마 엔터티를 저장하고 관리하는 사용자 정의 함수입니다.
  [저장 함수](../schema-entities/stored-functions.md)를 참조하세요.
* **쿼리 정의 함수** - 단일 쿼리의 범위 내에서 정의되고 사용되는 사용자 정의 함수입니다. 이러한 함수의 정의는 [let 문](../letstatement.md)을 통해 수행됩니다.
  [사용자 정의 함수](./user-defined-functions.md)를 참조하세요.
* **기본 제공 함수** - 하드 코딩되어 있습니다(Kusto에서 정의하며 사용자가 수정할 수 없음).