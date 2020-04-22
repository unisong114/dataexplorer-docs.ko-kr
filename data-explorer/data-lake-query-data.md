---
title: Azure 데이터 탐색기를 사용하여 Azure 데이터 호수의 쿼리 데이터
description: Azure 데이터 탐색기를 사용하여 Azure Data Lake에서 데이터를 쿼리하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: aa1cdb647190c804c5dc18d1575a6030f166a845
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81498579"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer"></a>Azure 데이터 탐색기를 사용하여 Azure 데이터 호수의 쿼리 데이터

Azure Data Lake Storage는 빅 데이터 분석을 위한 확장성이 뛰어나고 비용 효율적인 데이터 레이크 솔루션입니다. 고성능 파일 시스템의 강력한 성능과 대규모 규모 및 경제성으로 통찰력을 높일 수 있습니다. 데이터 레이크 스토리지 Gen2Azure Blob 저장소 기능을 확장하고 분석 워크로드에 최적화되어 있습니다.
 
Azure 데이터 탐색기는 Azure Blob 저장소 및 Azure 데이터 레이크 저장소(Gen1 및 Gen2)와 통합되어 호수의 데이터에 대한 빠르고 캐시되고 인덱싱된 액세스를 제공합니다. Azure Data Explorer를 사전에 수집하지 않고도 호수의 데이터를 분석하고 쿼리할 수 있습니다. 또한 수집되고 수집되지 않은 네이티브 레이크 데이터를 동시에 쿼리할 수도 있습니다.  

> [!TIP]
> 최상의 쿼리 성능은 Azure 데이터 탐색기로 데이터를 수집해야 합니다. 사전 수집 없이 외부 데이터를 쿼리하는 기능은 거의 쿼리되지 않는 기록 데이터 또는 데이터에만 사용해야 합니다. 최상의 결과를 위해 [호수에서 쿼리 성능을 최적화합니다.](#optimize-your-query-performance)
 

## <a name="create-an-external-table"></a>외부 테이블 만들기

 > [!NOTE]
 > 현재 지원되는 저장소 계정은 Azure Blob 저장소 또는 Azure 데이터 레이크 저장소(Gen1 및 Gen2)입니다.

1. `.create external table` 명령을 사용하여 Azure 데이터 탐색기에서 외부 테이블을 만듭니다. 에서와 `.show`같은 `.drop`추가 외부 테이블 `.alter` 명령및 [외부 테이블 명령에 문서화되어 있습니다.](kusto/management/externaltables.md)

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```
    
    > [!NOTE]
    > * 세분화된 분할을 통해 성능이 향상될 것으로 예상됩니다. 예를 들어, 일별 파티션이 있는 외부 테이블에 대한 쿼리는 월별 분할된 테이블이 있는 쿼리보다 성능이 우수합니다.
    > * 파티션이 있는 외부 테이블을 정의할 때 저장소 구조는 동일할 것으로 예상됩니다.
예를 들어 테이블이 yyyy/MM/dd 형식(기본값)의 DateTime 파티션으로 정의된 경우 URI 저장소 파일 경로는 *컨테이너1/yyyy/MM/dd/all_exported_blobs.* 
    > * 외부 테이블이 datetime 열로 분할된 경우 항상 쿼리에 닫힌 범위에 대한 시간 필터를 `ArchivedProducts | where Timestamp between (ago(1h) .. 10m)` 포함합니다(예: 쿼리 - 이(열린 범위) 1개보다 `ArchivedProducts | where Timestamp > ago(1h)` 더 잘 수행해야 합니다. 
    > * 지원되는 모든 [인베이션 형식은](ingest-data-overview.md#supported-data-formats) 외부 테이블을 사용하여 쿼리할 수 있습니다.

1. 외부 테이블이 웹 UI의 왼쪽 창에 표시됩니다.

    ![웹 UI의 외부 테이블](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>json 형식으로 외부 테이블 만들기

json 형식의 외부 테이블을 만들 수 있습니다. 자세한 내용은 [외부 테이블 명령을 참조하십시오.](kusto/management/externaltables.md)

1. `.create external table` 명령을 사용하여 *ExternalTableJson이라는*테이블을 만듭니다.

    ```kusto
    .create external table ExternalTableJson (rownumber:int, rowguid:guid) 
    kind=blob
    dataformat=json
    ( 
       h@'http://storageaccount.blob.core.windows.net/container1;secretKey'
    )
    with 
    (
       docstring = "Docs",
       folder = "ExternalTables",
       namePrefix="Prefix"
    ) 
    ```
 
1. Json 형식은 아래와 같이 열에 매핑을 만드는 두 번째 단계가 필요합니다. 다음 쿼리에서 *매핑 이름이라는*특정 json 매핑을 만듭니다.

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>외부 테이블 사용 권한
 
* 데이터베이스 사용자는 외부 테이블을 만들 수 있습니다. 테이블 작성자가 자동으로 테이블 관리자가 됩니다.
* 클러스터, 데이터베이스 또는 테이블 관리자는 기존 테이블을 편집할 수 있습니다.
* 모든 데이터베이스 사용자 또는 판독기는 외부 테이블을 쿼리할 수 있습니다.
 
## <a name="query-an-external-table"></a>외부 테이블 쿼리
 
외부 테이블을 쿼리하려면 함수를 `external_table()` 사용하고 테이블 이름을 함수 인수로 제공합니다. 나머지 쿼리는 표준 Kusto 쿼리 언어입니다.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> Intellisense는 현재 외부 테이블 쿼리에서 지원되지 않습니다.

### <a name="query-an-external-table-with-json-format"></a>json 형식으로 외부 테이블 쿼리

json 형식으로 외부 테이블을 쿼리하려면 `external_table()` 함수를 사용하고 테이블 이름과 매핑 이름을 함수 인수로 모두 제공합니다. 아래 쿼리에서 *매핑Name을* 지정하지 않으면 이전에 만든 매핑이 사용됩니다.

```kusto
external_table('ExternalTableJson', 'mappingName')
```

## <a name="query-external-and-ingested-data-together"></a>외부 및 수집된 데이터를 함께 쿼리합니다.

동일한 쿼리 내에서 외부 테이블과 수집된 데이터 테이블을 모두 쿼리할 수 있습니다. 사용자 [`join`](kusto/query/joinoperator.md) [`union`](kusto/query/unionoperator.md) 또는 Azure 데이터 탐색기, SQL 서버 또는 기타 소스의 추가 데이터가 있는 외부 테이블입니다. 외부 [`let( ) statement`](kusto/query/letstatement.md) 테이블 참조에 약식 이름을 할당하려면 a를 사용합니다.

아래 예제에서 *Products는* 수집된 데이터 테이블이며 *ArchivedProducts는* Azure Data Lake Storage Gen2의 데이터를 포함하는 외부 테이블입니다.

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>도움말 클러스터의 외부 테이블 쿼리 *TaxiRides*

*TaxiRides* 샘플 데이터 세트에는 뉴욕 시 택시 [및 리무진 위원회의](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)뉴욕시 택시 데이터가 포함되어 있습니다.

### <a name="create-external-table-taxirides"></a>외부 테이블 *택시라이드* 만들기 

> [!NOTE]
> 이 섹션에서는 *도움말* 클러스터에서 *TaxiRides* 외부 테이블을 만드는 데 사용되는 쿼리를 설명합니다. 이 테이블이 이미 만들어졌기 때문에 이 섹션을 건너뛰고 [ *쿼리 TaxiRides* 외부 테이블 데이터를](#query-taxirides-external-table-data)수행할 수 있습니다. 

1. 다음 쿼리는 도움말 클러스터에서 외부 *테이블 TaxiRides를* 만드는 데 사용되었습니다. 

    ```kusto
    .create external table TaxiRides
    (
    trip_id: long,
    vendor_id: string, 
    pickup_datetime: datetime,
    dropoff_datetime: datetime,
    store_and_fwd_flag: string,
    rate_code_id: int,
    pickup_longitude: real,
    pickup_latitude: real,
    dropoff_longitude: real,
    dropoff_latitude: real,
    passenger_count: int,
    trip_distance: real,
    fare_amount: real,
    extra: real,
    mta_tax: real,
    tip_amount: real,
    tolls_amount: real,
    ehail_fee: real,
    improvement_surcharge: real,
    total_amount: real,
    payment_type: string,
    trip_type: int,
    pickup: string,
    dropoff: string,
    cab_type: string,
    precipitation: int,
    snow_depth: int,
    snowfall: int,
    max_temperature: int,
    min_temperature: int,
    average_wind_speed: int,
    pickup_nyct2010_gid: int,
    pickup_ctlabel: string,
    pickup_borocode: int,
    pickup_boroname: string,
    pickup_ct2010: string,
    pickup_boroct2010: string,
    pickup_cdeligibil: string,
    pickup_ntacode: string,
    pickup_ntaname: string,
    pickup_puma: string,
    dropoff_nyct2010_gid: int,
    dropoff_ctlabel: string,
    dropoff_borocode: int,
    dropoff_boroname: string,
    dropoff_ct2010: string,
    dropoff_boroct2010: string,
    dropoff_cdeligibil: string,
    dropoff_ntacode: string,
    dropoff_ntaname: string,
    dropoff_puma: string
    )
    kind=blob 
    partition by bin(pickup_datetime, 1d)
    dataformat=csv
    ( 
        h@'http://storageaccount.blob.core.windows.net/container1;secretKey''
    )
    ```
1. 결과 테이블은 *도움말* 클러스터에서 만들어졌습니다.

    ![택시라이드 외부 테이블](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>쿼리 *TaxiRides* 외부 테이블 데이터 

에 [https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples) 로그인하여 *TaxiRides* 외부 테이블을 쿼리합니다. 

#### <a name="query-taxirides-external-table-without-partitioning"></a>쿼리 *TaxiRides* 분할 없이 외부 테이블

외부 테이블 *TaxiRides에서* [이 쿼리를 실행하여](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) 전체 데이터 집합에서 요일별 라이드를 묘사합니다. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

이 쿼리는 요일 중 가장 바쁜 요일을 보여 주며, 가장 바쁜 요일을 보여 주는 쿼리입니다. 데이터가 분할되지 않으므로 이 쿼리는 결과를 반환하는 데 시간이 오래 걸릴 수 있습니다(최대 몇 분).

![분할되지 않은 쿼리 렌더링](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>쿼리 TaxiRides 분할 외부 테이블 

2017년 1월에 사용된 택시 유형(노란색 또는 녹색)을 표시하는 외부 테이블 *TaxiRides에서* [이 쿼리를 실행합니다.](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

이 쿼리는 쿼리 시간과 성능을 최적화하는 분할을 사용합니다. 쿼리는 분할된 열(pickup_datetime)을 필터링하고 몇 초 안에 결과를 반환합니다.

![분할된 쿼리 렌더링](media/data-lake-query-data/taxirides-with-partition.png)
  
외부 테이블 *TaxiRides에서* 실행하기 위해 추가 쿼리를 작성하고 데이터에 대해 자세히 알아볼 수 있습니다. 

## <a name="optimize-your-query-performance"></a>쿼리 성능 최적화

외부 데이터를 쿼리하기 위한 다음 모범 사례를 사용하여 호수에서 쿼리 성능을 최적화합니다. 
 
### <a name="data-format"></a>데이터 형식
 
다음과 같은 이후 분석 쿼리에 열 형식형식을 사용합니다.
* 쿼리와 관련된 열만 읽을 수 있습니다. 
* 열 인코딩 기술은 데이터 크기를 크게 줄일 수 있습니다.  
Azure 데이터 탐색기는 마루 및 ORC 열 형식입니다. 마루 형식은 최적화 된 구현으로 인해 제안된다. 
 
### <a name="azure-region"></a>Azure 지역 
 
외부 데이터가 Azure 데이터 탐색기 클러스터와 동일한 Azure 지역에 있는지 확인합니다. 이렇게 하면 비용과 데이터 가져오기 시간이 줄어듭니다.
 
### <a name="file-size"></a>파일 크기
 
최적의 파일 크기는 파일당 수백 Mb(최대 1Gb)입니다. 느린 파일 열거 프로세스 및 columnar 형식의 제한된 사용과 같이 불필요한 오버헤드가 필요한 많은 작은 파일을 사용하지 마십시오. 파일 수는 Azure Data Explorer 클러스터의 CPU 코어 수보다 커야 합니다. 
 
### <a name="compression"></a>압축
 
압축을 사용하여 원격 저장소에서 가져오는 데이터의 양을 줄입니다. 마루 형식의 경우 열 그룹을 별도로 압축하는 내부 마루 압축 메커니즘을 사용하여 별도로 읽을 수 있습니다. 압축 메커니즘의 사용을 확인하려면 파일이름이 다음과<filename><filename><filename>같이 지정되어 있는지 확인합니다. 
 
### <a name="partitioning"></a>분할
 
쿼리가 관련없는 경로를 건너뛸 수 있도록 하는 "폴더" 파티션을 사용하여 데이터를 구성합니다. 분할을 계획할 때는 타임스탬프 또는 테넌트 ID와 같은 쿼리의 파일 크기와 일반적인 필터를 고려합니다.
 
### <a name="vm-size"></a>VM 크기
 
더 많은 코어와 더 높은 네트워크 처리량이 있는 VM SCO를 선택합니다(메모리가 덜 중요). 자세한 내용은 [Azure 데이터 탐색기 클러스터에 대한 올바른 VM SKU 선택을](manage-cluster-choose-sku.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

Azure 데이터 탐색기를 사용하여 Azure 데이터 레이크에서 데이터를 쿼리합니다. [쿼리를 작성하고](write-queries.md) 데이터에서 추가 통찰력을 도출하는 방법을 알아봅니다.
