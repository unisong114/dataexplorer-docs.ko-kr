---
title: predict_fl ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 predict_fl () 사용자 정의 함수에 대해 설명 합니다.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/09/2020
ms.openlocfilehash: edac4a5bec11f294444f60e1c1a1e5023198fdc6
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321916"
---
# <a name="predict_fl"></a>predict_fl()

함수는 `predict_fl()` 기존의 학습 된 machine learning 모델을 사용 하 여 예측 합니다. 이 모델은 [Scikit](https://scikit-learn.org/stable/)를 사용 하 여 작성 되었으며, 문자열로 직렬화 되 고, 표준 Azure 데이터 탐색기 테이블에 저장 됩니다.

> [!NOTE]
> * `predict_fl()` 는 [UDF (사용자 정의 함수)](../query/functions/user-defined-functions.md)입니다.
> * 이 함수는 인라인 Python을 포함 하며 클러스터에서 [python () 플러그 인을 사용 하도록 설정](../query/pythonplugin.md#enable-the-plugin) 해야 합니다. 자세한 내용은 [사용](#usage)을 참조 하세요.

## <a name="syntax"></a>Syntax

`T | invoke predict_fl(`*models_tbl* `,` *model_name* `,` *features_cols* `,` *pred_col*`)`

## <a name="arguments"></a>인수

* *models_tbl*: serialize 된 모든 모델을 포함 하는 테이블의 이름입니다. 이 테이블에는 다음 열이 포함 되어 있어야 합니다.
    * *이름*: 모델 이름
    * *타임 스탬프*: 모델 학습 시간
    * *model*: serialize 된 모델의 문자열 표현입니다.
* *model_name*: 사용할 특정 모델의 이름입니다.
* *features_cols*: 모델에서 예측에 사용 하는 기능 열의 이름을 포함 하는 동적 배열입니다.
* *pred_col*: 예측을 저장 하는 열의 이름입니다.

## <a name="usage"></a>사용량

`predict_fl()`[invoke 연산자](../query/invokeoperator.md)를 사용 하 여 적용 되는 사용자 정의 [테이블 형식 함수](../query/functions/user-defined-functions.md#tabular-function) 입니다. 쿼리에 해당 코드를 포함 하거나 데이터베이스에 설치할 수 있습니다. Ad hoc 및 영구 사용의 두 가지 사용 옵션이 있습니다. 예제는 아래 탭을 참조 하세요.

# <a name="ad-hoc"></a>[임시](#tab/adhoc)

임시 사용의 경우 [let 문을](../query/letstatement.md)사용 하 여 코드를 포함 합니다. 권한이 필요 하지 않습니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let predict_fl=(samples:(*), models_tbl:(name:string, timestamp:datetime, model:string), model_name:string, features_cols:dynamic, pred_col:string)
{
    let model_str = toscalar(models_tbl | where name == model_name | top 1 by timestamp desc | project model);
    let kwargs = pack('smodel', model_str, 'features_cols', features_cols, 'pred_col', pred_col);
    let code =
    '\n'
    'import pickle\n'
    'import binascii\n'
    '\n'
    'smodel = kargs["smodel"]\n'
    'features_cols = kargs["features_cols"]\n'
    'pred_col = kargs["pred_col"]\n'
    'bmodel = binascii.unhexlify(smodel)\n'
    'clf1 = pickle.loads(bmodel)\n'
    'df1 = df[features_cols]\n'
    'predictions = clf1.predict(df1)\n'
    '\n'
    'result = df\n'
    'result[pred_col] = pd.DataFrame(predictions, columns=[pred_col])'
    '\n'
    ;
    samples
    | evaluate python(typeof(*), code, kwargs)
};
//
// Predicts room occupancy from sensors measurements, and calculates the confusion matrix
//
// Occupancy Detection is an open dataset from UCI Repository at https://archive.ics.uci.edu/ml/datasets/Occupancy+Detection+
// It contains experimental data for binary classification of room occupancy from Temperature,Humidity,Light and CO2.
// Ground-truth labels were obtained from time stamped pictures that were taken every minute
//
OccupancyDetection 
| where Test == 1
| extend pred_Occupancy=false
| invoke predict_fl(ML_Models, 'Occupancy', pack_array('Temperature', 'Humidity', 'Light', 'CO2', 'HumidityRatio'), 'pred_Occupancy')
| summarize n=count() by Occupancy, pred_Occupancy
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

영구적으로 사용 하려면을 사용 [`.create function`](../management/create-function.md) 합니다. 함수를 만들려면 [데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

### <a name="one-time-installation"></a>일회성 설치

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create function with (folder = "Packages\\ML", docstring = "Predict using ML model, build by Scikit-learn")
predict_fl(samples:(*), models_tbl:(name:string, timestamp:datetime, model:string), model_name:string, features_cols:dynamic, pred_col:string)
{
    let model_str = toscalar(models_tbl | where name == model_name | top 1 by timestamp desc | project model);
    let kwargs = pack('smodel', model_str, 'features_cols', features_cols, 'pred_col', pred_col);
    let code =
    '\n'
    'import pickle\n'
    'import binascii\n'
    '\n'
    'smodel = kargs["smodel"]\n'
    'features_cols = kargs["features_cols"]\n'
    'pred_col = kargs["pred_col"]\n'
    'bmodel = binascii.unhexlify(smodel)\n'
    'clf1 = pickle.loads(bmodel)\n'
    'df1 = df[features_cols]\n'
    'predictions = clf1.predict(df1)\n'
    '\n'
    'result = df\n'
    'result[pred_col] = pd.DataFrame(predictions, columns=[pred_col])'
    '\n'
    ;
    samples
    | evaluate python(typeof(*), code, kwargs)
}
```

### <a name="usage"></a>사용량

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
// Predicts room occupancy from sensors measurements, and calculates the confusion matrix
//
// Occupancy Detection is an open dataset from UCI Repository at https://archive.ics.uci.edu/ml/datasets/Occupancy+Detection+
// It contains experimental data for binary classification of room occupancy from Temperature,Humidity,Light and CO2.
// Ground-truth labels were obtained from time stamped pictures that were taken every minute
//
OccupancyDetection 
| where Test == 1
| extend pred_Occupancy=false
| invoke predict_fl(ML_Models, 'Occupancy', pack_array('Temperature', 'Humidity', 'Light', 'CO2', 'HumidityRatio'), 'pred_Occupancy')
| summarize n=count() by Occupancy, pred_Occupancy
```

---

혼동 행렬:
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
Occupancy   pred_Occupancy  n
TRUE        TRUE            3006
FALSE       TRUE            112
TRUE        FALSE           15
FALSE       FALSE           9284
```
