---
title: Kibana를 사용 하 여 Azure 데이터 탐색기에서 데이터 시각화
description: 이 문서에서는 Kibana에 대 한 데이터 원본으로 Azure 데이터 탐색기를 설정 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: bf479a7248033d2aa70a8e09b039814361c78031
ms.sourcegitcommit: bcd0c96b1581e43e33aa35f4d68af6dcb4979d39
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88039236"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>K2Bridge 오픈 소스 커넥터를 사용 하 여 Kibana의 Azure 데이터 탐색기에서 데이터 시각화

K2Bridge (Kibana-Kusto Bridge)를 사용 하면 Azure 데이터 탐색기를 데이터 원본으로 사용 하 고 Kibana에서 데이터를 시각화할 수 있습니다. K2Bridge은 [오픈 소스](https://github.com/microsoft/K2Bridge)컨테이너 화 된 응용 프로그램입니다. Kibana 인스턴스와 Azure 데이터 탐색기 클러스터 간의 프록시로 작동 합니다. 이 문서에서는 K2Bridge를 사용 하 여 해당 연결을 만드는 방법을 설명 합니다.

K2Bridge는 Kibana 쿼리를 KQL (Kusto Query Language)로 변환 하 고 Azure 데이터 탐색기 결과를 다시 Kibana로 보냅니다.

   ![K2Bridge를 통해 Azure 데이터 탐색기와 Kibana 연결](media/k2bridge/k2bridge-chart.png)

K2Bridge는 Kibana의 **검색** 탭을 지원 합니다.

* 데이터를 검색 하 고 탐색 합니다.
* 결과를 필터링 합니다.
* 결과 표에서 필드를 추가 하거나 제거 합니다.
* 레코드 콘텐츠 보기.
* 검색을 저장 하 고 공유 합니다.

다음 이미지는 K2Bridge에서 Azure 데이터 탐색기에 바인딩된 Kibana 인스턴스를 보여 줍니다. Kibana의 사용자 환경은 변경 되지 않습니다.

   [![Kibana 페이지가 Azure 데이터 탐색기에 바인딩되어 있습니다.](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>필수 구성 요소

Kibana의 Azure 데이터 탐색기에서 데이터를 시각화 하려면 다음을 준비 해야 합니다.

* Kubernetes 패키지 관리자 인 [투구 v3](https://github.com/helm/helm#install)

* AKS (Azure Kubernetes Service) 클러스터 또는 기타 Kubernetes 클러스터. 1.14 ~ 1.16 버전을 테스트 하 고 확인 했습니다. AKS 클러스터가 필요한 경우 [Azure CLI를 사용 하 여](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) AKS 클러스터를 배포 하거나 [Azure Portal을 사용](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)하는 방법을 참조 하세요.

* 클러스터의 URL 및 데이터베이스 이름을 포함 하는 [Azure 데이터 탐색기 클러스터](create-cluster-database-portal.md)

* 클라이언트 ID 및 클라이언트 암호를 비롯 하 여 Azure 데이터 탐색기에서 데이터를 볼 수 있는 권한 있는 Azure Active Directory (Azure AD) 서비스 주체입니다.

    뷰어 권한이 있는 서비스 주체를 사용 하 여 더 높은 수준의 사용 권한을 사용 하지 못하도록 하는 것이 좋습니다. [AZURE AD 서비스 주체에 대 한 클러스터의 보기 권한을 설정](manage-database-permissions.md#manage-permissions-in-the-azure-portal)합니다.

    Azure AD 서비스 주체에 대 한 자세한 내용은 [AZURE ad 서비스 주체 만들기](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)를 참조 하세요.

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>AKS (Azure Kubernetes Service)에서 K2Bridge 실행

기본적으로 K2Bridge의 투구 차트는 MCR (Microsoft Container Registry)에 있는 공개적으로 사용 가능한 이미지를 참조 합니다. MCR에는 자격 증명이 필요 하지 않습니다.

1. 필요한 투구 차트를 다운로드 합니다.

1. Elasticsearch 종속성을 투구에 추가 합니다. K2Bridge는 작은 내부 Elasticsearch 인스턴스를 사용 하기 때문에 종속성이 필요 합니다. 인스턴스는 인덱스 패턴 쿼리 및 저장 된 쿼리와 같은 메타 데이터 관련 요청을 서비스 합니다. 이 내부 인스턴스는 비즈니스 데이터를 저장 하지 않습니다. 인스턴스를 구현 세부 정보로 간주할 수 있습니다.

    1. Elasticsearch 종속성을 투구에 추가 하려면 다음 명령을 실행 합니다.

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. GitHub 리포지토리의 차트 디렉터리에서 K2Bridge 차트를 가져오려면 다음을 수행 합니다.

        1. [GitHub](https://github.com/microsoft/K2Bridge)에서 리포지토리를 복제 합니다.
        1. K2Bridges 루트 리포지토리 디렉터리로 이동 합니다.
        1. 다음 명령을 실행합니다.

            ```bash
            helm dependency update charts/k2bridge
            ```

1. K2Bridge를 배포 합니다.

    1. 사용자 환경에 맞는 값으로 변수를 설정 합니다.

        ```bash
        ADX_URL=[YOUR_ADX_CLUSTER_URL] #For example, https://mycluster.westeurope.kusto.windows.net
        ADX_DATABASE=[YOUR_ADX_DATABASE_NAME]
        ADX_CLIENT_ID=[SERVICE_PRINCIPAL_CLIENT_ID]
        ADX_CLIENT_SECRET=[SERVICE_PRINCIPAL_CLIENT_SECRET]
        ADX_TENANT_ID=[SERVICE_PRINCIPAL_TENANT_ID]
        ```

    1. 필요에 따라 Application Insights 원격 분석을 사용 하도록 설정 합니다. Application Insights를 처음으로 사용 하는 경우 [Application Insights 리소스를 만듭니다](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource). [계측 키를 변수에 복사](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key) 합니다.

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a>K2Bridge 차트를 설치 합니다.

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        [구성](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md)에서 전체 구성 옵션 집합을 찾을 수 있습니다.

    1. <a name="install-kibana-service"></a>이전 명령의 출력은 Kibana를 배포 하는 다음 투구 명령을 제안 합니다. 필요에 따라 다음 명령을 실행 합니다.

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```

    1. 포트 전달을 사용 하 여 localhost의 Kibana에 액세스 합니다.

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```

    1. 로 이동 하 여 Kibana에 연결 http://127.0.0.1:5601 합니다.

    1. 사용자에 게 Kibana를 노출 합니다. 여러 가지 방법으로이 작업을 수행할 수 있습니다. 사용 하는 방법은 사용 사례에 따라 크게 달라 집니다.

        예를 들어 서비스를 Load Balancer 서비스로 노출할 수 있습니다. 이렇게 하려면 **--set service. type = LoadBalancer** 매개 변수를 [이전 Kibana 투구 **install** 명령](#install-kibana-service)에 추가 합니다.

        그런 후 다음 명령을 실행 합니다.

        ```bash
        kubectl get service -w -n k2bridge
        ```

        출력은 다음과 같습니다.

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx    <pending>     5601:30128/TCP   4m24s
        ```

        그런 다음 나타나는 생성 된 외부 IP 값을 사용할 수 있습니다. 브라우저를 열고 5601로 이동 하 여 Kibana에 액세스 하는 데 사용 \<EXTERNAL-IP\> 합니다.

1. 인덱스 패턴을 구성 하 여 데이터에 액세스 합니다.

    새 Kibana 인스턴스에서:

    1. Kibana를 엽니다.
    1. **관리**로 이동 합니다.
    1. **인덱스 패턴**을 선택 합니다.
    1. 인덱스 패턴을 만듭니다. 인덱스 이름은 별표 () 없이 테이블 이름 또는 함수 이름과 정확히 일치 해야 합니다 \* . 목록에서 관련 선을 복사할 수 있습니다.

> [!Note]
> 다른 Kubernetes 공급자에서 K2Bridge를 실행 하려면 공급자가 제안한 것과 일치 하도록 값. yaml의 Elasticsearch **storageClassName** 값을 변경 합니다.

## <a name="visualize-data"></a>데이터 시각화

Azure 데이터 탐색기이 Kibana에 대 한 데이터 원본으로 구성 된 경우 Kibana를 사용 하 여 데이터를 탐색할 수 있습니다.

1. Kibana의 맨 왼쪽 메뉴에서 **검색** 탭을 선택 합니다.

1. 가장 왼쪽 드롭다운 목록 상자에서 인덱스 패턴을 선택 합니다. 패턴은 탐색 하려는 데이터 원본을 정의 합니다. 이 경우 인덱스 패턴은 Azure 데이터 탐색기 테이블입니다.

   ![인덱스 패턴 선택](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. 데이터에 시간 필터 필드가 있는 경우 시간 범위를 지정할 수 있습니다. **검색** 페이지의 오른쪽 위에서 시간 필터를 선택 합니다. 기본적으로 페이지는 최근 15 분 동안의 데이터를 표시 합니다.

   ![시간 필터 선택](media/k2bridge/k2bridge-time-filter.png)

1. 결과 테이블에는 처음 500 레코드가 표시 됩니다. 문서를 확장 하 여 JSON 또는 테이블 형식으로 필드 데이터를 검사할 수 있습니다.

   ![확장 된 레코드](media/k2bridge/k2bridge-expand-record.png)

1. 기본적으로 결과 테이블에는 **_source** 열이 포함 되어 있습니다. 시간 필드가 존재 하는 경우에도 **시간** 열도 포함 됩니다. 가장 왼쪽에 있는 창에서 필드 이름 옆에 있는 **추가** 를 선택 하 여 결과 테이블에 특정 열을 추가할 수 있습니다.

   ![추가 단추가 강조 표시 된 특정 열](media/k2bridge/k2bridge-specific-columns.png)

1. 쿼리 표시줄에서 다음을 수행 하 여 데이터를 검색할 수 있습니다.

    * 검색 용어를 입력 합니다.
    * Lucene 쿼리 구문을 사용 합니다. 다음은 그 예입니다.
        * "오류"를 검색 하 여이 값을 포함 하는 모든 레코드를 찾습니다.
        * "상태: 200"을 검색 하 여 상태 값이 200 인 모든 레코드를 가져옵니다.
    * 논리 연산자 **and**, **OR**및 **NOT**을 사용 합니다.
    * 별표 ( \* ) 및 물음표 (?) 와일드 카드 문자를 사용 합니다. 예를 들어 "destination_city: L *" 쿼리는 대상 city 값이 "L" 또는 "l"로 시작 하는 레코드와 일치 합니다. K2Bridge은 대/소문자를 구분 하지 않습니다.

    ![쿼리 실행](media/k2bridge/k2bridge-run-query.png)

    > [!Tip]
    > 검색 [에서 더](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md)많은 검색 규칙과 논리를 찾을 수 있습니다.

1. 검색 결과를 필터링 하려면 페이지의 맨 오른쪽 창에 있는 필드 목록을 사용 합니다. 필드 목록은 다음을 볼 수 있습니다.

    * 필드에 대 한 상위 5 개 값입니다.
    * 필드를 포함 하는 레코드 수입니다.
    * 각 값을 포함 하는 레코드의 백분율입니다.

    >[!Tip]
    > 돋보기를 사용 하 여 특정 값을 가진 레코드를 모두 찾을 수 있습니다.

    ![돋보기를 강조 표시 한 필드 목록](media/k2bridge/k2bridge-field-list.png)

    돋보기를 사용 하 여 결과를 필터링 하 고 결과 테이블의 각 레코드에 대 한 결과 테이블 형식 보기를 볼 수도 있습니다.

     ![돋보기를 강조 표시 한 테이블 목록](media/k2bridge/k2bridge-table-list.png)

1. 검색에 대해 **저장** 또는 **공유** 중 하나를 선택 합니다.

     ![검색을 저장 하거나 공유 하는 강조 표시 된 단추](media/k2bridge/k2bridge-save-search.png)
