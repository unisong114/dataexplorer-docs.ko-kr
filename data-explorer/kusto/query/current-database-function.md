---
title: current_database ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 current_database ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d68c35547c840cc1e16224c376e90dfabec296d7
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348703"
---
# <a name="current_database"></a>current_database()

범위 (다른 데이터베이스를 지정 하지 않은 경우 모든 쿼리 엔터티가 확인 됨)의 데이터베이스 이름을 반환 합니다.

## <a name="syntax"></a>Syntax

`current_database()`

## <a name="returns"></a>반환

범위에 있는 데이터베이스의 이름 (형식의 값)입니다 `string` .

## <a name="example"></a>예제

```kusto
print strcat("Database in scope: ", current_database())
```