---
title: 저장소 연결 문자열-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 저장소 연결 문자열을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: 0456ad7115c51bcdc51b0db82bc9f9b88953be32
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226213"
---
# <a name="storage-connection-strings"></a>스토리지 연결 문자열

몇 가지 Kusto 명령은 외부 저장소 서비스와 상호 작용 하도록 Kusto 명령을 지시 합니다. 예를 들어 Kusto는 데이터를 Azure Storage Blob 내보내도록 지시 하 여 저장소 계정 이름 또는 Blob 컨테이너 등의 특정 매개 변수를 제공 해야 합니다.

Kusto는 다음 저장소 공급자를 지원 합니다.


* 저장소 공급자 Azure Storage Blob
* 저장소 공급자 Azure Data Lake Storage

저장소 공급자의 각 종류는 저장소 리소스를 설명 하는 데 사용 되는 연결 문자열 형식 및 액세스 하는 방법을 정의 합니다.
Kusto는 URI 형식을 사용 하 여 이러한 저장소 리소스 및 이러한 저장소 리소스에 액세스 하는 데 필요한 속성 (예: 보안 자격 증명)을 설명 합니다.


|공급자                   |구성표    |URI 템플릿                          |
|---------------------------|----------|--------------------------------------|
|Azure Storage Blob         |`https://`|`https://`*계정* `.blob.core.windows.net/` *컨테이너*[ `/` *blobname*] [ `?` *SasKey* \| `;` *AccountKey*]|
|Azure Data Lake Store Gen 2|`abfss://`|`abfss://`*파일 시스템* `@` *계정* `.dfs.core.windows.net/` *PathToDirectoryOrFile*[ `;` *callercredentials*]|
|Azure Data Lake Store Gen 1|`adl://`  |`adl://`*Account*Azuredatalakestore.net/*PathToDirectoryOrFile*[ `;` *callercredentials*]|

## <a name="azure-storage-blob"></a>Azure Storage Blob

이 공급자는 가장 일반적으로 사용 되며 모든 시나리오에서 지원 됩니다.
리소스에 액세스할 때 공급자에 게 자격 증명을 제공 해야 합니다. 자격 증명을 제공 하는 데 지원 되는 두 가지 메커니즘이 있습니다.

* Azure Storage Blob의 표준 쿼리 ()를 사용 하 여 공유 액세스 (SAS) 키를 제공 `?sig=...` 합니다. Kusto가 제한 된 시간 동안 리소스에 액세스 해야 하는 경우이 방법을 사용 합니다.
* 저장소 계정 키 ()를 제공 `;ljkAkl...==` 합니다. Kusto가 지속적으로 리소스에 액세스 해야 하는 경우이 방법을 사용 합니다.

예 (계정 키 또는 SAS를 노출 하지 않도록 난독 처리 된 문자열 리터럴이 표시 됨):

`h"https://fabrikam.blob.core.windows.net/container/path/to/file.csv;<storage_account_key_text, ends with '=='>"`
`h"https://fabrikam.blob.core.windows.net/container/path/to/file.csv?sv=...&sp=rwd"` 

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="azure-data-lake-store-gen-2"></a>Azure Data Lake Store Gen 2

이 공급자는 Azure Data Lake Store Gen 2의 데이터에 액세스할 수 있도록 지원 합니다.

URI의 형식은 다음과 같습니다.

`abfss://`*파일 시스템* `@` *Storageaccountname* `.dfs.core.windows.net/` *경로* `;` *Callercredentials*

위치:

* _Filesystem_ 은 ADLS Filesystem 개체의 이름입니다 (약 Blob 컨테이너와 동일).
* _Storageaccountname_ 은 저장소 계정의 이름입니다.
* _Path_ 는 슬래시 () 문자를 액세스 하는 디렉터리 또는 파일에 대 한 경로를 `/` 구분 기호로 사용 합니다.
* _Callercredentials_ 는 아래에 설명 된 대로 서비스에 액세스 하는 데 사용 되는 자격 증명을 나타냅니다.

Gen 2 Azure Data Lake Store에 액세스할 때 호출자는 서비스에 액세스 하기 위한 유효한 자격 증명을 제공 해야 합니다. 자격 증명을 제공 하는 다음과 같은 방법이 지원 됩니다.

* `;sharedkey=` _AccountKey_ 을 저장소 계정 키로 사용 하 여 *AccountKey* 를 URI에 추가 합니다.
* `;impersonate`URI에를 추가 합니다. Kusto는 요청자의 보안 주체 id를 사용 하 고 가장 하 여 리소스에 액세스 합니다. 보안 주체는 [여기](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)에 설명 된 대로 읽기/쓰기 작업을 수행할 수 있도록 적절 한 RBAC 역할을 할당 해야 합니다. 예를 들어 읽기 작업의 최소 역할은 `Storage Blob Data Reader` 역할입니다.
* `;token=` _AadToken_ 를 base-64로 인코딩된 AAD 액세스 토큰으로 사용 하 여 *AadToken* 를 URI에 추가 합니다 (토큰은 리소스에 대 한 토큰 인지 확인 `https://storage.azure.com/` ).
* `;prompt`URI에를 추가 합니다. Kusto는 리소스에 액세스 해야 하는 경우 사용자 자격 증명을 요청 합니다. (사용자에 게 클라우드 배포를 사용 하지 않도록 설정 되어 있으며 테스트 환경 에서만 사용 됨)
* Azure Data Lake Storage Gen 2의 표준 쿼리 ()를 사용 하 여 공유 액세스 (SAS) 키를 제공 `?sig=...` 합니다. Kusto가 제한 된 시간 동안 리소스에 액세스 해야 하는 경우이 방법을 사용 합니다.



### <a name="azure-data-lake-store-gen-1"></a>Azure Data Lake Store Gen 1

이 공급자는 Azure Data Lake Store의 파일 및 디렉터리에 대 한 액세스를 지원 합니다.
자격 증명을 제공 해야 합니다 (Kusto는 자체 AAD 보안 주체를 사용 하 여 Azure Data Lake 액세스 하지 않음). 자격 증명을 제공 하는 다음과 같은 방법이 지원 됩니다.

* `;impersonate`URI에를 추가 합니다. Kusto는 요청자의 보안 주체 id를 사용 하 여 리소스에 액세스 하기 위해 가장 합니다.
* `;token=` *AadToken* 를 base-64로 인코딩된 AAD 액세스 토큰으로 사용 하 여 *AadToken* 를 URI에 추가 합니다 (토큰은 리소스에 대 한 토큰 인지 확인 `https://management.azure.com/` ).
* `;prompt`URI에를 추가 합니다. Kusto는 리소스에 액세스 해야 하는 경우 사용자 자격 증명을 요청 합니다. (사용자에 게 클라우드 배포를 사용 하지 않도록 설정 되어 있으며 테스트 환경 에서만 사용 됨)



