---
title: 연속 데이터 내보내기 삭제-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 지속적인 데이터 내보내기를 삭제 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: f08d8c99c242aa63e3847d3965bf9511967b60e1
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149499"
---
# <a name="drop-continuous-export"></a>연속 내보내기 삭제

연속 내보내기 작업을 삭제 합니다.

## <a name="syntax"></a>구문

`.drop``continuous-export` *ContinuousExportName*

## <a name="properties"></a>속성

| 속성             | Type   | Description                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 연속 내보내기 이름 |

## <a name="output"></a>출력

데이터베이스의 남은 연속 내보내기 (사후 삭제)입니다. [연속 내보내기 표시 명령과](show-continuous-export.md)같은 출력 스키마
