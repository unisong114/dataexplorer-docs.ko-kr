---
title: 클라이언트 라이브러리 개요 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 클라이언트 라이브러리 개요에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 2eb567062402f3c00fce6b12bf81feb507ca6c23
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610168"
---
# <a name="client-libraries-overview"></a>클라이언트 라이브러리 개요

다음 표에는 쿼리, 작업 및 ARM/RP 관리를 위해 제공된 다양한 라이브러리가 나열됩니다. 이러한 라이브러리를 사용하는 것은 Azure API를 사용하고 프로그래밍 방식으로 Azure Data Explorer 기능을 호출하는 데 권장되는 방법입니다. 


|    언어\기능        |    쿼리        |    수집        |    ARM/RP 관리        |
|------------------------------    |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------    |--------------------------------------------------------------------------------------------------------------------------------------------------------------------    |------------------------------------------------------------------------------------------------------------------------------    |
|    .Net        |    [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data/)            |    [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest/)        |    [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/1.0.0)         |
|    .Net 스탠다드        |    [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/)        |    [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest.NETStandard/)        |            |
|    Java        |    [메이븐](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data) [기트허브](https://github.com/Azure/azure-kusto-java/tree/master/data)        |    [메이븐](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest) [기트허브](https://github.com/Azure/azure-kusto-java/tree/master/ingest)        |    [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto.v2019_01_21/azure-mgmt-kusto)        |
|    Javascript        |             |             |    [Npm](https://www.npmjs.com/package/@azure/arm-kusto)         |
|    NodeJS        |    [npm](https://www.npmjs.com/package/azure-kusto-data) [GitHub](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-data)        |    [npm](https://www.npmjs.com/package/azure-kusto-ingest)       [GitHub](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-ingest)        |    [Npm](https://www.npmjs.com/package/azure-arm-kusto/v/2.0.0)        |
|    Python        |    [피피](https://pypi.org/project/azure-kusto-ingest/)    [기트허브](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data)        |    [피피](https://pypi.org/project/azure-kusto-data/)      [기트허브](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-ingest)        |    [피피 ()피피 ((](https://pypi.org/project/azure-mgmt-kusto/0.3.0/)        |
|    R        |    [CRAN](https://cran.r-project.org/web/packages/AzureKusto/index.html)               |             |            |
|    Go        |    [GitHub](https://github.com/Azure/azure-kusto-go)        |    [GitHub](https://github.com/Azure/azure-kusto-go/tree/master/kusto/ingest)        |        [GitHub](https://github.com/Azure/azure-sdk-for-go/tree/master/services/kusto/mgmt/2019-01-21/kusto)        |
|    Ruby        |             |             |    [보석](https://rubygems.org/gems/azure_mgmt_kusto/versions/0.17.1)         |
|    PowerShell        |    [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)        |    [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)        |    [패키지](https://www.powershellgallery.com/packages/Az.Kusto/)         |
|    Azure CLI        |             |             |    [Azure Cli](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)         |
|    나머지 API        |    [REST (영문)](rest/index.md)        |    [REST (영문)](rest/index.md)        |     [GitHub](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/azure-kusto/resource-manager/Microsoft.Kusto)         |
|    TypeScript        |             |             |        [Npm](https://www.npmjs.com/package/@azure/arm-kusto/v/2.0.0)        |
|      |      |      |      |


## <a name="tools-and-integrations"></a>도구 및 통합

* 가장 가벼운: [누겟](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/) 
* 원클릭 섭취 키트: [GitHub](https://github.com/Azure/azure-kusto-ingestion-tools) 
* 카프카: [기트허브](https://github.com/Azure/kafka-sink-azure-kustoLogstash)
* 로그스타쉬: [GitHub](https://github.com/Azure/logstash-output-kusto) 
* 스파크: [메이븐](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)