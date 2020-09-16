---
title: Azure 데이터 탐색기를 사용 하 여 Azure Data Lake에서 데이터 쿼리
description: Azure 데이터 탐색기를 사용 하 여 Azure Data Lake에서 데이터를 쿼리 하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/17/2020
ms.openlocfilehash: 058a42cc21c6af9642d91231e6b1620315f94f55
ms.sourcegitcommit: 313a91d2a34383b5a6e39add6c8b7fabb4f8d39a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90680699"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer"></a>Azure 데이터 탐색기를 사용 하 여 Azure Data Lake에서 데이터 쿼리

Azure Data Lake Storage은 빅 데이터 분석을 위한 확장성이 뛰어나고 경제적인 data Lake 솔루션입니다. 고성능 파일 시스템의 강력한 기능을 광범위 하 고 경제적으로 결합 하 여 정보에 대 한 시간을 단축할 수 있습니다. Data Lake Storage Gen2는 Azure Blob Storage 기능을 확장 하며 분석 워크 로드에 최적화 되어 있습니다.
 
Azure 데이터 탐색기는 Azure Blob Storage 및 Azure Data Lake Storage (Gen1 및 Gen2)와 통합 되어 외부 저장소에 저장 된 데이터에 대 한 빠른, 캐시 및 인덱싱된 액세스를 제공 합니다. Azure 데이터 탐색기에 이전에 수집 하지 않고 데이터를 분석 및 쿼리할 수 있습니다. 수집 및 uningested 외부 데이터를 동시에 쿼리할 수도 있습니다.  

> [!TIP]
> 최고의 쿼리 성능을 위해서는 Azure 데이터 탐색기에 데이터를 수집 해야 합니다. 이전 수집 없이 외부 데이터를 쿼리 하는 기능은 기록 데이터 또는 거의 쿼리하지 않는 데이터에만 사용 해야 합니다. 최상의 결과를 위해 [외부 데이터 쿼리 성능을 최적화](#optimize-your-query-performance) 합니다.
 
## <a name="create-an-external-table"></a>외부 테이블 만들기

웨어하우스에 저장 된 제품에 대 한 기록 정보가 포함 된 CSV 파일이 많고 작년에서 가장 인기 있는 5 개 제품을 찾기 위해 빠른 분석을 수행 하려는 경우를 가정해 보겠습니다. 이 예제에서 CSV 파일은 다음과 같습니다.

| 타임스탬프 | ProductId   | ProductDescription |
|-----------|-------------|--------------------|
| 2019-01-01 11:21:00 | TO6050 | 3.5 DS/HD 플로피 디스크 |
| 2019-01-01 11:30:55 | YDX1   | Yamaha DX1 신시사이저  |
| ...                 | ...    | ...                     |

이 파일은 Azure Blob storage에 저장 되며 `mycompanystorage` `archivedproducts` , 날짜별로 분할 된 이라는 컨테이너에 저장 됩니다.

```
https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/01/part-00000-7e967c99-cf2b-4dbb-8c53-ce388389470d.csv.gz
https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/01/part-00001-ba356fa4-f85f-430a-8b5a-afd64f128ca4.csv.gz
https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/01/part-00002-acb644dc-2fc6-467c-ab80-d1590b23fc31.csv.gz
https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/01/part-00003-cd5fad16-a45e-4f8c-a2d0-5ea5de2f4e02.csv.gz
https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/02/part-00000-ffc72d50-ff98-423c-913b-75482ba9ec86.csv.gz
...
```

이러한 CSV 파일에서 직접 KQL 쿼리를 실행 하려면 명령을 사용 하 여 `.create external table` Azure 데이터 탐색기에서 외부 테이블을 정의 합니다. 외부 테이블 만들기 명령 옵션에 대 한 자세한 내용은 [외부 테이블 명령](kusto/management/external-tables-azurestorage-azuredatalake.md)을 참조 하세요.

```Kusto
.create external table ArchivedProducts(Timestamp:datetime, ProductId:string, ProductDescription:string)   
kind=blob            
partition by (Date:datetime = bin(Timestamp, 1d))   
dataformat=csv   
(   
  h@'https://mycompanystorage.blob.core.windows.net/archivedproducts;StorageSecretKey'
)    
```

이제 외부 테이블이 웹 UI의 왼쪽 창에 표시 됩니다.

:::image type="content" source="media/data-lake-query-data/external-tables-web-ui.png" alt-text="웹 UI의 외부 테이블":::
 
### <a name="external-table-permissions"></a>외부 테이블 사용 권한
 
* 데이터베이스 사용자는 외부 테이블을 만들 수 있습니다. 테이블 작성자가 자동으로 테이블 관리자가 됩니다.
* 클러스터, 데이터베이스 또는 테이블 관리자는 기존 테이블을 편집할 수 있습니다.
* 모든 데이터베이스 사용자 또는 읽기 권한자는 외부 테이블을 쿼리할 수 있습니다.

## <a name="querying-an-external-table"></a>외부 테이블 쿼리
 
외부 테이블을 정의한 후에 `external_table()` 는 함수를 사용 하 여 해당 테이블을 참조할 수 있습니다. 쿼리의 나머지 부분은 표준 Kusto 쿼리 언어입니다.

```Kusto
external_table("ArchivedProducts")   
| where Timestamp > ago(365d)   
| summarize Count=count() by ProductId,   
| top 5 by Count
```

## <a name="querying-external-and-ingested-data-together"></a>외부 및 수집 데이터를 함께 쿼리

동일한 쿼리 내에서 외부 테이블과 수집 데이터 테이블을 모두 쿼리할 수 있습니다. [`join`](kusto/query/joinoperator.md) [`union`](kusto/query/unionoperator.md) Azure 데이터 탐색기, SQL server 또는 기타 원본의 추가 데이터를 사용 하 여 외부 테이블을 만들 수 있습니다. 를 사용 [`let( ) statement`](kusto/query/letstatement.md) 하 여 외부 테이블 참조에 약식 이름을 할당 합니다.

아래 예제에서 *제품은* 수집 데이터 테이블이 고 *ArchivedProducts* 은 이전에 정의한 외부 테이블입니다.

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);   
let T = Products; //T is an internal table   
T1 | join T on ProductId | take 10
```

## <a name="querying-hierarchical-data-formats"></a>계층적 데이터 형식 쿼리

Azure 데이터 탐색기는,, 및과 같은 계층적 형식을 쿼리할 수 있습니다 `JSON` `Parquet` `Avro` `ORC` . 계층적 데이터 스키마를 외부 테이블 스키마 (다른 경우)에 매핑하려면 [외부 테이블 매핑 명령을](kusto/management/external-tables-azurestorage-azuredatalake.md#create-external-table-mapping)사용 합니다. 예를 들어 다음 형식으로 JSON 로그 파일을 쿼리하려면 다음과 같이 합니다.

```JSON
{
  "timestamp": "2019-01-01 10:00:00.238521",   
  "data": {    
    "tenant": "e1ef54a6-c6f2-4389-836e-d289b37bcfe0",   
    "method": "RefreshTableMetadata"   
  }   
}   
{
  "timestamp": "2019-01-01 10:00:01.845423",   
  "data": {   
    "tenant": "9b49d0d7-b3e6-4467-bb35-fa420a25d324",   
    "method": "GetFileList"   
  }   
}
...
```

외부 테이블 정의는 다음과 같습니다.

```kusto
.create external table ApiCalls(Timestamp: datetime, TenantId: guid, MethodName: string)
kind=blob
dataformat=multijson
( 
   h@'https://storageaccount.blob.core.windows.net/container1;StorageSecretKey'
)
```

데이터 필드를 외부 테이블 정의 필드에 매핑하는 JSON 매핑을 정의 합니다.

```kusto
.create external table ApiCalls json mapping 'MyMapping' '[{"Column":"Timestamp","Properties":{"Path":"$.timestamp"}},{"Column":"TenantId","Properties":{"Path":"$.data.tenant"}},{"Column":"MethodName","Properties":{"Path":"$.data.method"}}]'
```

외부 테이블을 쿼리하면 매핑이 호출 되 고 관련 데이터가 외부 테이블 열에 매핑됩니다.

```kusto
external_table('ApiCalls') | take 10
```

매핑 구문에 대 한 자세한 내용은 [데이터 매핑](kusto/management/mappings.md)을 참조 하세요.

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>도움말 클러스터의 *TaxiRides* 외부 테이블 쿼리

*Help* 라는 테스트 클러스터를 사용 하 여 다른 Azure 데이터 탐색기 기능을 사용해 보세요. *Help* cluster에는 수십억 개의 taxi 탑승를 포함 하는 [뉴욕 도시 taxi 데이터 집합](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page) 에 대 한 외부 테이블 정의가 포함 되어 있습니다.

### <a name="create-external-table-taxirides"></a>Create external table *TaxiRides* 

이 섹션에서는 *도움말* 클러스터에서 *TaxiRides* 외부 테이블을 만드는 데 사용 되는 쿼리를 보여 줍니다. 이 테이블은 이미 만들었기 때문에이 섹션을 건너뛰고 직접 [ *TaxiRides* 외부 테이블 데이터 쿼리](#query-taxirides-external-table-data)로 이동할 수 있습니다.

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
    h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
```

웹 UI의 왼쪽 창에서 만든 **TaxiRides** 테이블을 찾을 수 있습니다.

:::image type="content" source="media/data-lake-query-data/taxirides-external-table.png" alt-text="Taxi 탑승 external 테이블":::

### <a name="query-taxirides-external-table-data"></a>Query *TaxiRides* 외부 테이블 데이터 

[https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples)에 로그인합니다. 

#### <a name="query-taxirides-external-table-without-partitioning"></a>분할 하지 않고 *TaxiRides* 외부 테이블 쿼리

외부 테이블 *TaxiRides* 에서 [이 쿼리를 실행](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) 하 여 전체 데이터 집합에서 각 요일의 탑승를 표시 합니다. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

이 쿼리는 가장 바쁜 요일을 보여 줍니다. 데이터가 분할 되지 않으므로 쿼리는 결과를 반환 하는 데 최대 몇 분 정도 걸릴 수 있습니다.

:::image type="content" source="media/data-lake-query-data/taxirides-no-partition.png" alt-text="분할 되지 않은 쿼리 렌더링":::

#### <a name="query-taxirides-external-table-with-partitioning"></a>분할이 포함 된 TaxiRides 외부 테이블 쿼리 

외부 테이블 *TaxiRides* 에서 [이 쿼리를 실행](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) 하 여 2017 년 1 월에 사용 되는 taxi cab 형식 (노랑 또는 녹색)을 표시 합니다. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

이 쿼리에서는 쿼리 시간 및 성능을 최적화 하는 분할을 사용 합니다. 분할 된 열 (pickup_datetime)에 대 한 쿼리를 필터링 하 고 몇 초 내에 결과를 반환 합니다.

:::image type="content" source="media/data-lake-query-data/taxirides-with-partition.png" alt-text="분할 된 쿼리 렌더링":::
  
외부 테이블 *TaxiRides* 실행 되는 추가 쿼리를 작성 하 고 데이터에 대해 자세히 알아볼 수 있습니다. 

## <a name="optimize-your-query-performance"></a>쿼리 성능 최적화

외부 데이터를 쿼리 하기 위한 다음 모범 사례를 사용 하 여 lake에서 쿼리 성능을 최적화 하세요. 
 
### <a name="data-format"></a>데이터 형식
 
* 다음과 같은 이유로 분석 쿼리에 대해 칼럼 형식 형식을 사용 합니다.
    * 쿼리와 관련 된 열만 읽을 수 있습니다. 
    * 열 인코딩 기술을 통해 데이터 크기를 크게 줄일 수 있습니다.  
* Azure 데이터 탐색기은 Parquet 및 ORC 칼럼 형식 형식을 지원 합니다. 최적화 된 구현 때문에 Parquet 형식을 제안 합니다. 
 
### <a name="azure-region"></a>Azure 지역 
 
외부 데이터가 Azure 데이터 탐색기 클러스터와 동일한 Azure 지역에 있는지 확인 합니다. 이렇게 설정 하면 비용 및 데이터 인출 시간이 줄어듭니다.
 
### <a name="file-size"></a>파일 크기
 
최적의 파일 크기는 파일당 수백 메가바이트 (최대 1gb)입니다. 느린 파일 열거 프로세스 및 열 형식의 제한 된 사용과 같이 불필요 한 오버 헤드가 필요한 작은 파일은 많이 사용 하지 않습니다. 파일 수는 Azure 데이터 탐색기 클러스터의 CPU 코어 수보다 커야 합니다. 
 
### <a name="compression"></a>압축
 
원격 저장소에서 인출 되는 데이터의 양을 줄이려면 압축을 사용 합니다. Parquet format의 경우 열 그룹을 별도로 압축 하는 내부 Parquet 압축 메커니즘을 사용 하 여 별도로 읽을 수 있습니다. 압축 메커니즘 사용의 유효성을 검사 하려면 파일 이름이 * &lt; &gt; release.tar.gz. parquet* 또는 * &lt; &gt; snappy. parquet* 이 아닌 파일 이름으로 지정 되었는지 확인 * &lt; &gt; 합니다.* 
 
### <a name="partitioning"></a>분할
 
쿼리가 관련이 없는 경로를 건너뛸 수 있도록 하는 "폴더" 파티션을 사용 하 여 데이터를 구성 합니다. 분할을 계획할 때 타임 스탬프 또는 테 넌 트 ID와 같은 쿼리의 파일 크기 및 일반 필터를 고려 합니다.
 
### <a name="vm-size"></a>VM 크기
 
더 많은 코어와 더 높은 네트워크 처리량을 포함 하는 VM Sku를 선택 합니다 (메모리는 중요 하지 않음). 자세한 내용은 [Azure 데이터 탐색기 클러스터에 올바른 VM SKU 선택](manage-cluster-choose-sku.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* Azure 데이터 탐색기를 사용 하 여 Azure Data Lake에서 데이터를 쿼리 합니다. 쿼리를 [작성](write-queries.md) 하 고 데이터에서 추가 정보를 도출 하는 방법에 대해 알아봅니다.
