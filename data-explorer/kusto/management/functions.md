---
title: 저장 된 함수 관리 개요-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 저장 된 함수 관리 개요에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: 4354538b206ee86e34941718bcf6d74130647fb6
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321355"
---
# <a name="stored-functions-management-overview"></a>저장된 함수 관리 개요
이 섹션에서는 다음 [사용자 정의 함수](../query/functions/user-defined-functions.md)를 만들고 변경 하는 데 사용 되는 제어 명령을 설명 합니다.

|기능 |설명|
|---------|-----------|
|[`.alter function`](alter-function.md) |기존 함수를 변경 하 여 데이터베이스 메타 데이터에 저장 합니다. |
|[`.alter function docstring`](alter-docstring-function.md) |기존 함수의 DocString 값을 변경 합니다. |
|[`.alter function folder`](alter-folder-function.md) |기존 함수의 폴더 값을 변경 합니다. |
|[`.create function`](create-function.md) |저장 된 함수를 만듭니다. |
|[`.create-or-alter function`](create-alter-function.md) |저장 된 함수를 만들거나 기존 함수를 변경 하 여 데이터베이스 메타 데이터에 저장 합니다. |
|[`.drop function` 하거나 `.drop functions`](drop-function.md) |데이터베이스에서 함수 (또는 함수)를 삭제 합니다. |
|[`.show functions` 하거나 `.show function`](show-function.md) |현재 선택 된 데이터베이스에 있는 모든 저장 된 함수 또는 특정 함수를 나열 합니다. |