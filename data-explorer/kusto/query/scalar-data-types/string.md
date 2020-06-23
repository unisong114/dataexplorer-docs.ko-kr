---
title: 문자열 데이터 형식-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 문자열 데이터 형식에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e7c043a9b4d8b141d2dc45e88022e191e6483c35
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264813"
---
# <a name="the-string-data-type"></a>문자열 데이터 형식입니다.

`string`데이터 형식은 유니코드 문자열을 나타냅니다. Kusto 문자열은 u t f-8로 인코딩되고 기본적으로 1MB로 제한 됩니다.

## <a name="string-literals"></a>문자열 리터럴

데이터 형식의 리터럴을 인코딩하는 방법에는 여러 가지가 있습니다 `string` .

* 문자열을 큰따옴표 ()로 묶습니다 `"` .`"This is a string literal. Single quote characters (') don't require escaping. Double quote characters (\") are escaped by a backslash (\\)"`
* 문자열을 작은따옴표 ()로 묶습니다 `'` .`'Another string literal. Single quote characters (\') require escaping by a backslash (\\). Double quote characters (") do not require escaping.'`

위의 두 표현에서 백슬래시 ( `\` ) 문자는 이스케이프를 나타냅니다.
묶는 따옴표 문자, 탭 문자 ( `\t` ), 줄 바꿈 문자 ( `\n` ) 및 자체 ()를 이스케이프 하는 데 사용 `\\` 됩니다.

축 자 문자열 리터럴도 지원 됩니다. 이 형식에서 백슬래시 문자 ()는 `\` 이스케이프 문자가 아니라 자체를 나타냅니다.

* 큰따옴표 ()로 묶습니다 `"` .`@"This is a verbatim string literal that ends with a backslash\"`
* 작은따옴표 ()로 묶기 `'` :`@'This is a verbatim string literal that ends with a backslash\'`

쿼리에서 두 개의 문자열 리터럴을 포함 하거나 공백과 메모만만 분리 하 여 자동으로 조인 하면 새 문자열 리터럴이 형성 됩니다.
예를 들어 다음 식은 모두 길이를 산출 합니다 `13` .

```kusto
print strlen("Hello"', '@"world!"); // Nothing between them

print strlen("Hello" ', ' @"world!"); // Separated by whitespace only

print strlen("Hello"
  // Comment
  ', '@"world!"); // Separated by whitespace and a comment
```

## <a name="examples"></a>예제

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

볼 수 있듯이 문자열이 큰따옴표 ()로 묶여 있는 경우 `"` 작은따옴표 ( `'` ) 문자에는 이스케이프가 필요 하지 않으며 그 밖의 방법도 있습니다. 이 메서드를 사용 하면 컨텍스트에 따라 문자열이 더 쉽게 따옴표를 사용할 수 있습니다.

## <a name="obfuscated-string-literals"></a>난독 처리 되지 않은 문자열 리터럴

시스템은 쿼리를 추적 하 고 원격 분석 및 분석을 위해 저장 합니다.
예를 들어 쿼리 텍스트를 클러스터 소유자가 사용할 수 있도록 설정할 수 있습니다. 쿼리 텍스트에 암호와 같은 비밀 정보가 포함 되어 있으면 비공개로 유지 되어야 하는 정보가 누출 될 수 있습니다. 이러한 누출 발생을 방지 하기 위해 쿼리 작성자는 특정 문자열 리터럴을 난독 처리 된 **문자열 리터럴로**표시할 수 있습니다.
쿼리 텍스트의 이러한 리터럴은 자동으로 여러 개의 별표 ( `*` ) 문자로 대체 되므로 나중에 분석할 때 사용할 수 없습니다.

> [!IMPORTANT]
> 비밀 정보를 포함 하는 모든 문자열 리터럴을 난독 처리 된 문자열 리터럴로 표시 합니다.

난독 처리 된 문자열 리터럴은 "일반" 문자열 리터럴을 가져오고 `h` `H` 앞에 또는 문자 앞에 문자를 추가할 수 있습니다. 

예를 들어:

```kusto
h'hello'
h@'world'
h"hello"
```

> [!NOTE]
> 대부분의 경우 문자열 리터럴의 일부만 비밀입니다. 이러한 경우에는 리터럴을 비밀 없는 부분과 비밀 파트로 분할 합니다. 그런 다음 비밀 부분만 난독 처리 된 것으로 표시 합니다.

예를 들어:

```kusto
print x="https://contoso.blob.core.windows.net/container/blob.txt?"
  h'sv=2012-02-12&se=2013-04-13T0...'
```
