---
title: Azure 데이터 탐색기 파이썬 SDK - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 Azure 데이터 탐색기 파이썬 SDK에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 5bee1fce1ca76069c34602872b2d4dedeb762ec2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502859"
---
# <a name="azure-data-explorer-python-sdk"></a>Azure 데이터 탐색기 파이썬 SDK

Azure 데이터 탐색기 *Kusto Python 클라이언트* 라이브러리는 파이썬을 사용하여 Azure 데이터 탐색기 클러스터를 쿼리하는 기능을 제공합니다. 파이썬 2.x / 3.x 호환되며 친숙한 파이썬 DB API 인터페이스를 사용하여 모든 데이터 유형을 지원합니다.

예를 들어 Spark 클러스터에 연결된 [Jupyter 노트북에서](https://jupyter.org/) [Azure Databricks](https://azure.microsoft.com/services/databricks/) 인스턴스를 포함하되 단독으로 는 아니지만 라이브러리를 사용할 수 있습니다.

*Kusto 파이썬 인제스트 클라이언트는* Azure 데이터 탐색기 서비스(예: 데이터 수집)로 데이터를 보낼 수 있는 파이썬 라이브러리입니다. 

* [패키지를 설치하는 방법](https://github.com/Azure/azure-kusto-python#install)

* [쿠스토 쿼리 샘플](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py)

* [데이터 수집 샘플](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-ingest/tests/sample.py)

* [GitHub 리포지토리](https://github.com/Azure/azure-kusto-python)

    [![대체 텍스트](https://travis-ci.org/Azure/azure-kusto-python.svg?branch=master "azure-kusto-python")](https://travis-ci.org/Azure/azure-kusto-python)

* 파이 피 패키지:

    * [azure-kusto-데이터](https://pypi.org/project/azure-kusto-data/)
    [![파이피 버전](https://badge.fury.io/py/azure-kusto-data.svg)](https://badge.fury.io/py/azure-kusto-data)
    * [azure-kusto-인제스트](https://pypi.org/project/azure-kusto-ingest/)
    [![파이피 버전](https://badge.fury.io/py/azure-kusto-ingest.svg)](https://badge.fury.io/py/azure-kusto-ingest)
    * [azure-mgmt-kusto](https://pypi.org/project/azure-mgmt-kusto/)
    [![파이피 버전](https://badge.fury.io/py/azure-mgmt-kusto.svg)](https://badge.fury.io/py/azure-mgmt-kusto)