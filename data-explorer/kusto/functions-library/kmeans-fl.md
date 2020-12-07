---
title: kmeans_fl ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 kmeans_fl () 사용자 정의 함수에 대해 설명 합니다.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 10/18/2020
ms.openlocfilehash: 7d2482c36c0c55c34adbc664a6c24f64bdadf7a1
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321950"
---
# <a name="kmeans_fl"></a>kmeans_fl()

함수는 `kmeans_fl()` clusterizes [알고리즘](https://en.wikipedia.org/wiki/K-means_clustering)을 사용 하 여 데이터 집합을 만듭니다.

> [!NOTE]
> * `kmeans_fl()` 는 [UDF (사용자 정의 함수)](../query/functions/user-defined-functions.md)입니다.
> * 이 함수는 인라인 Python을 포함 하며 클러스터에서 [python () 플러그 인을 사용 하도록 설정](../query/pythonplugin.md#enable-the-plugin) 해야 합니다. 자세한 내용은 [사용](#usage)을 참조 하세요.

## <a name="syntax"></a>Syntax

`T | invoke kmeans_fl(`*k* `,` *features_cols* `,` *cluster_col*`)`

## <a name="arguments"></a>인수

* *k*: 필요한 클러스터 수입니다.
* *features_cols*: 클러스터링에 사용 되는 기능 열의 이름을 포함 하는 동적 배열입니다.
* *cluster_col*: 각 레코드의 출력 클러스터 ID를 저장 하는 열의 이름입니다.

## <a name="usage"></a>사용량

`kmeans_fl()`[invoke 연산자](../query/invokeoperator.md)를 사용 하 여 적용 되는 사용자 정의 [테이블 형식 함수](../query/functions/user-defined-functions.md#tabular-function) 입니다. 쿼리에 해당 코드를 포함 하거나 데이터베이스에 설치할 수 있습니다. Ad hoc 및 영구 사용의 두 가지 사용 옵션이 있습니다. 예제는 아래 탭을 참조 하세요.

# <a name="ad-hoc"></a>[임시](#tab/adhoc)

임시 사용의 경우 [let 문을](../query/letstatement.md)사용 하 여 코드를 포함 합니다. 권한이 필요 하지 않습니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let kmeans_fl=(tbl:(*), k:int, features:dynamic, cluster_col:string)
{
    let kwargs = pack('k', k, 'features', features, 'cluster_col', cluster_col);
    let code =
        '\n'
        'from sklearn.cluster import KMeans\n'
        '\n'
        'k = kargs["k"]\n'
        'features = kargs["features"]\n'
        'cluster_col = kargs["cluster_col"]\n'
        '\n'
        'km = KMeans(n_clusters=k)\n'
        'df1 = df[features]\n'
        'km.fit(df1)\n'
        'result = df\n'
        'result[cluster_col] = km.labels_\n'
        ;
    tbl
    | evaluate python(typeof(*), code, kwargs)
};
//
// Clusterize room occupancy from sensors measurements.
//
// Occupancy Detection is an open dataset from UCI Repository at https://archive.ics.uci.edu/ml/datasets/Occupancy+Detection+
// It contains experimental data for binary classification of room occupancy from Temperature, Humidity, Light, and CO2.
//
OccupancyDetection
| extend cluster_id=double(null)
| invoke kmeans_fl(5, pack_array("Temperature", "Humidity", "Light", "CO2", "HumidityRatio"), "cluster_id")
| sample 10
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

영구적으로 사용 하려면을 사용 [`.create function`](../management/create-function.md) 합니다. 함수를 만들려면 [데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

### <a name="one-time-installation"></a>일회성 설치

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create function with (folder = "Packages\\ML", docstring = "K-Means clustering")
kmeans_fl(tbl:(*), k:int, features:dynamic, cluster_col:string)
{
    let kwargs = pack('k', k, 'features', features, 'cluster_col', cluster_col);
    let code =
        '\n'
        'from sklearn.cluster import KMeans\n'
        '\n'
        'k = kargs["k"]\n'
        'features = kargs["features"]\n'
        'cluster_col = kargs["cluster_col"]\n'
        '\n'
        'km = KMeans(n_clusters=k)\n'
        'df1 = df[features]\n'
        'km.fit(df1)\n'
        'result = df\n'
        'result[cluster_col] = km.labels_\n'
        ;
    tbl
    | evaluate python(typeof(*), code, kwargs)
}
```

### <a name="usage"></a>사용량

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
// Clusterize room occupancy from sensors measurements.
//
// Occupancy Detection is an open dataset from UCI Repository at https://archive.ics.uci.edu/ml/datasets/Occupancy+Detection+
// It contains experimental data for binary classification of room occupancy from Temperature, Humidity, Light, and CO2.
//
OccupancyDetection
| extend cluster_id=double(null)
| invoke kmeans_fl(5, pack_array("Temperature", "Humidity", "Light", "CO2", "HumidityRatio"), "cluster_id")
| sample 10
```

---

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
Timestamp                   Temperature Humidity    Light  CO2         HumidityRatio Occupancy Test  cluster_id
2015-02-02 14:38:00.0000000 23.64       27.1        473    908.8       0.00489763    TRUE      TRUE  1
2015-02-03 01:47:00.0000000 20.575      22.125      0      446         0.00330878    FALSE     TRUE  0
2015-02-10 08:47:00.0000000 20.42666667 33.56       405    494.3333333 0.004986493   TRUE      FALSE 4
2015-02-10 09:15:00.0000000 20.85666667 35.09666667 433    665.3333333 0.005358055   TRUE      FALSE 4
2015-02-11 16:13:00.0000000 21.89       30.0225     429    771.75      0.004879358   TRUE      TRUE  4
2015-02-13 14:06:00.0000000 23.4175     26.5225     608    599.75      0.004728116   TRUE      TRUE  4
2015-02-13 23:09:00.0000000 20.13333333 32.2        0      502.6666667 0.004696278   FALSE     TRUE  0
2015-02-15 18:30:00.0000000 20.5        32.79       0      666.5       0.004893459   FALSE     TRUE  3
2015-02-17 13:43:00.0000000 21.7        33.9        454    1167        0.005450924   TRUE      TRUE  1
2015-02-17 18:17:00.0000000 22.025      34.2225     0      1538.25     0.005614538   FALSE     TRUE  2
```

함수를 이미 설치한 상태에서 각 클러스터의 중심 및 크기를 추출 합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
OccupancyDetection
| extend cluster_id=double(null)
| invoke kmeans_fl(5, pack_array("Temperature", "Humidity", "Light", "CO2", "HumidityRatio"), "cluster_id")
| summarize Temperature=avg(Temperature), Humidity=avg(Humidity), Light=avg(Light), CO2=avg(CO2), HumidityRatio=avg(HumidityRatio), num=count() by cluster_id
| order by num

```

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
cluster_id  Temperature        Humidity            Light            CO2                HumidityRatio        num
0           20.3507186863278   27.1521395395781    10.1995789883291 486.804272186974   0.00400132147662714  11124
4           20.9247315268427   28.7971160082823    20.7311894656536 748.965771574469   0.00440412568299058  3063
1           22.0284137970445   27.8953334469889    481.872136037748 1020.70779349773   0.00456692559904535  2514
3           22.0344177115763   25.1151053429273    462.358969056434 656.310608696507   0.00411782436443015  2176
2           21.4091216082295   31.8363099552939    174.614816229606 1482.05062388414   0.00504573022875817  1683
```
