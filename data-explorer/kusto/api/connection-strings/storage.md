---
title: 저장소 연결 문자열 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 저장소 연결 문자열에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: 909198e42786666d3a26874e18b5cfd57b27ab1f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502961"
---
# <a name="storage-connection-strings"></a>스토리지 연결 문자열

몇 가지 Kusto 명령은 Kusto에게 외부 저장소 서비스와 상호 작용하도록 지시합니다. 예를 들어 Kusto는 Azure Storage Blob으로 데이터를 내보내도록 요청받을 수 있으며, 이 경우 특정 매개 변수(예: 저장소 계정 이름 또는 Blob 컨테이너)를 제공해야 합니다.

Kusto는 다음과 같은 스토리지 공급자를 지원합니다.


* Azure 저장소 Blob 저장소 공급자
* Azure 데이터 레이크 스토리지 공급자

각 종류의 저장소 공급자는 저장소 리소스및 액세스 방법을 설명하는 데 사용되는 연결 문자열 형식을 정의합니다.
Kusto는 URI 형식을 사용하여 이러한 저장소 리소스와 이러한 저장소 리소스에 액세스하는 데 필요한 속성(예: 보안 자격 증명)을 설명합니다.


|공급자                   |구성표    |URI 템플릿                          |
|---------------------------|----------|--------------------------------------|
|Azure Storage Blob         |`https://`|`https://`*계정*`.blob.core.windows.net/`*Container*컨테이너`/`[*BlobName][*`?`*SasKey* \| `;` *계정키*]|
|Azure Data Lake Store Gen 2|`abfss://`|`abfss://`*파일 시스템*`@`*계정*`.dfs.core.windows.net/`*경로ToDirectoryOrFile*[`;`발신자 자격*증명*]|
|Azure Data Lake Store Gen 1|`adl://`  |`adl://`*계정*.azuredatalakestore.net/*PathToDirectoryOrFile*[`;`*발신자 자격 증명]*|

## <a name="azure-storage-blob"></a>Azure Storage Blob

이 공급자는 가장 일반적으로 사용되며 모든 시나리오에서 지원됩니다.
리소스에 액세스할 때 공급자에게 자격 증명이 제공되어야 합니다. 자격 증명을 제공하기 위한 두 가지 지원되는 메커니즘이 있습니다.

* Azure 저장소 Blob의 표준 쿼리()를`?sig=...`사용하여 SAS(공유 액세스) 키를 제공합니다. Kusto가 제한된 시간 동안 리소스에 액세스해야 하는 경우 이 방법을 사용합니다.
* 저장소 계정 키`;ljkAkl...==`()를 제공합니다. Kusto가 지속적으로 리소스에 액세스해야 하는 경우 이 방법을 사용합니다.

예제(계정 키 또는 SAS를 노출하지 않도록 난독 처리된 문자열 리터럴이 표시됨).

`h"https://fabrikam.blob.core.windows.net/container/path/to/file.csv;<storage_account_key_text, ends with '=='>"`
`h"https://fabrikam.blob.core.windows.net/container/path/to/file.csv?sv=...&sp=rwd"` 

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="azure-data-lake-store-gen-2"></a>Azure Data Lake Store Gen 2

이 공급자는 Azure Data Lake Store Gen 2의 데이터 액세스를 지원합니다.

URI의 형식은 다음과 입니다.

`abfss://`*파일 시스템* `@` *저장소 계정 이름* `.dfs.core.windows.net/` *경로* `;` *호출 자격 증명*

위치:

* _파일 시스템은_ ADLS 파일 시스템 개체의 이름입니다(Blob 컨테이너와 거의 동일)
* _저장소계정이름_
* _경로는_ 디렉토리 또는 파일에 액세스하는 경로입니다`/`슬래시 () 문자는 구분기호로 사용됩니다.
* _CallerCredential는_ 아래에 설명된 대로 서비스에 액세스하는 데 사용되는 자격 증명을 나타냅니다.

Azure Data Lake Store Gen 2에 액세스할 때 호출자는 서비스에 액세스하기 위한 유효한 자격 증명을 제공해야 합니다. 자격 증명을 제공하는 다음 방법이 지원됩니다.

* 계정 `;sharedkey=`키가 저장소 계정 키인 URI에 _계정 키를_ 더해도 *AccountKey*
* URI에 `;impersonate` 부가합니다. Kusto는 요청자의 보안 주 체증을 사용하고 리소스에 액세스하기 위해 가장합니다. 여기서 [설명된](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)대로 읽기/쓰기 작업을 수행할 수 있도록 적절한 RBAC 역할 할당이 있어야 합니다. 예를 들어 읽기 작업에 대한 최소 `Storage Blob Data Reader` 역할은 역할입니다.
* `;token=` *AadToken을* URI에 부호싸고 _AadToken은_ 기본 64 인코딩된 AAD 액세스 토큰입니다(토큰이 리소스용인지 `https://storage.azure.com/`확인).
* URI에 `;prompt` 부가합니다. Kusto는 리소스에 액세스해야 하는 경우 사용자 자격 증명을 요청합니다. (클라우드 배포에 대해 사용자에게 프롬프트가 비활성화되고 테스트 환경에서만 활성화됩니다.)
* Azure Data Lake Storage Gen 2의 표준 쿼리()를`?sig=...`사용하여 SAS(공유 액세스) 키를 제공합니다. Kusto가 제한된 시간 동안 리소스에 액세스해야 하는 경우 이 방법을 사용합니다.



### <a name="azure-data-lake-store-gen-1"></a>Azure Data Lake Store Gen 1

이 공급자는 Azure Data Lake Store에서 파일 및 디렉터리 액세스를 지원합니다.
자격 증명이 제공되어야 합니다(Kusto는 Azure Data Lake에 액세스하기 위해 자체 AAD 보안 주체를 사용하지 않습니다.) 자격 증명을 제공하는 다음 방법이 지원됩니다.

* URI에 `;impersonate` 부가합니다. Kusto는 요청자의 보안 주체 ID를 사용하여 리소스에 액세스하기 위해 가장합니다.
* uri를 `;token=`_to _AadToken 부호, _AadToken_ 기본-64 인코딩 된 AAD 액세스 토큰 (토큰 리소스에 `https://management.azure.com/`대 한 있는지 확인).
* URI에 `;prompt` 부가합니다. Kusto는 리소스에 액세스해야 할 때 사용자 자격 증명을 요청합니다. (클라우드 배포에 대해 사용자에게 프롬프트가 비활성화되고 테스트 환경에서만 활성화됩니다.)



