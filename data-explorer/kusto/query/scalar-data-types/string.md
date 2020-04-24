---
title: 문자열 데이터 형식 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 문자열 데이터 형식에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8c040045ba7146cf56487ca3a8729372084d3bf2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509625"
---
# <a name="the-string-data-type"></a>문자열 데이터 형식

데이터 `string` 형식은 유니코드 문자열을 나타냅니다. Kusto 문자열은 UTF-8로 인코딩되며 기본적으로 1MB로 제한됩니다.

## <a name="string-literals"></a>문자열 리터럴

데이터 형식의 리터럴을 인코딩하는 `string` 방법에는 여러 가지가 있습니다.

* 문자열을 큰따옴표 ()`"`로 둘러싸는 것으로`"This is a string literal. Single quote characters (') do not require escaping. Double quote characters (\") are escaped by a backslash (\\)"`
* 문자열을 따옴표 ()`'`로 둘러싸는 것으로`'Another string literal. Single quote characters (\') require escaping by a backslash (\\). Double quote characters (") do not require escaping.'`

위의 두 표현에서 백슬래시()`\`문자는 이스케이프를 나타냅니다.
그것은 둘러싸는 따옴표 문자, 탭`\t`문자 (),`\n`줄 바래 문자 (), 및 자체 ()를`\\`이스케이프하는 데 사용됩니다.

축어 문자열 리터럴도 지원됩니다. 이 양식에서 백슬래시 문자`\`() 는 이스케이프 문자가 아니라 자체를 의미합니다.

* [사진] 큰따옴표로 묶인 것 ():`"``@"This is a verbatim string literal that ends with a backslash\"`
* 단일 따옴표로`'`동봉됨 ( ):`@'This is a verbatim string literal that ends with a backslash\'`

쿼리 텍스트에 아무 것도 없는 두 개의 문자열 리터럴이 있거나 공백과 주석으로만 구분되어 자동으로 연결되어 새 문자열 리터럴을 형성합니다(이러한 대체가 이루어질 수 없을 때까지).
예를 들어, 다음 식은 `13`모든 수율을 산출합니다.

```kusto
print strlen("Hello"', '@"world!"); // Nothing between them

print strlen("Hello" ', ' @"world!"); // Separated by whitespace only

print strlen("Hello"
  // Comment
  ', '@"world!"); // Separated by whitespace and a comment
```

## <a name="examples"></a>예

```kusto
// Simple string notation
print s1 = 'some string', s2 = "some other string"

// Strings that include single or double-quotes can be defined as follows
print s1 = 'string with " (double quotes)',
          s2 = "string with ' (single quotes)"

// Strings with '\' can be prefixed with '@' (as in c#)
print myPath1 = @'C:\Folder\filename.txt'

// Escaping using '\' notation
print s = '\\n.*(>|\'|=|\")[a-zA-Z0-9/+]{86}=='
```

볼 수 있듯이 문자열이 큰따옴표 (큰따옴표)로`"``'`동봉된 경우, 단일 따옴표 () 문자는 이스케이프가 필요하지 않으며 그 반대의 경우도 마찬가지입니다. 이렇게 하면 컨텍스트에 따라 문자열을 쉽게 인용할 수 있습니다.

## <a name="obfuscated-string-literals"></a>난독 처리된 문자열 리터럴

시스템은 쿼리를 추적하고 원격 분석 및 분석을 위해 쿼리를 저장합니다.
예를 들어 클러스터 소유자가 쿼리 텍스트를 사용할 수 있습니다. 쿼리 텍스트에 암호와 같은 비밀 정보가 포함된 경우 비공개로 유지해야 하는 정보가 유출될 수 있습니다. 이러한 일이 발생하지 않도록 쿼리 작성자는 특정 문자열 리터럴을 **난독 처리된 문자열 리터럴로 표시할**수 있습니다.
쿼리 텍스트의 이러한 리터럴은 자동으로 여러 별자리`*`() 문자로 대체되므로 나중에 분석할 때 사용할 수 없습니다.

> [!IMPORTANT]
> 비밀 정보가 포함된 모든 문자열 리터럴은 난독 처리된 문자열 리터럴로 표시하는 **것이 좋습니다.**

난독 처리 된 문자열 리터럴은 "일반"문자열 리터럴을 취하고 앞에 `h` 있는 `H` 문자 또는 문자를 미리 보류하여 형성 될 수 있습니다. 다음은 그 예입니다.

```kusto
h'hello'
h@'world'
h"hello"
```

> [!NOTE]
> 대부분의 경우 문자열 리터럴의 일부만 비밀입니다. 이러한 경우 리터럴을 비밀이 아닌 부분과 비밀 부분으로 분할한 다음 비밀 부분을 난독 처리된 부분으로만 표시하는 것이 매우 유용합니다. 다음은 그 예입니다.

```kusto
print x="https://contoso.blob.core.windows.net/container/blob.txt?"
  h'sv=2012-02-12&se=2013-04-13T0...'
```