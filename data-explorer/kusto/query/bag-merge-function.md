---
title: bag_merge ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 bag_merge ()에 대해 설명 합니다.
services: data-explorer
author: elgevork
ms.author: elgevork
ms.reviewer: ''
ms.service: data-explorer
ms.topic: reference
ms.date: 06/14/2020
ms.openlocfilehash: f5ca4888b0c3aad976d78c10bbbfa0810483c75b
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84784602"
---
# <a name="bag_merge"></a>bag_merge ()

입력 동적 속성 모음 개체를 동적 속성 모음 개체로 병합 합니다.

**구문**

`bag_merge(`*동적 개체* `,` *동적 개체*`)`

**인수**

* 쉼표로 구분 된 동적 개체를 입력 합니다. 함수는 2 ~ 64 입력 동적 개체를 지원 합니다.

**반환**

`dynamic`속성 모음을 반환 합니다. 모든 입력 속성 모음 개체를 병합 한 결과
키가 두 개 이상의 입력 개체에 표시 되는 경우이 키에 사용할 수 있는 값 중에서 임의의 값이 선택 됩니다.

**예제**

식:

`print result = bag_merge(dynamic({ 'A1':12, 'B1':2, 'C1':3}), dynamic({ 'A2':81, 'B2':82, 'A1':1}))`

다음으로 평가 됩니다.

`{
  "A1": 12,
  "B1": 2,
  "C1": 3,
  "A2": 81,
  "B2": 82
}`
