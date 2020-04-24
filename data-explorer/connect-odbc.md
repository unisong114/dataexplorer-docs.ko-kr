---
title: ODBC를 사용하여 Azure 데이터 탐색기에 연결
description: 이 문서에서는 Azure 데이터 탐색기에 대한 ODBC(개방형 데이터베이스 연결) 연결을 설정하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 156257613195cb53730273ac3a654908feac9601
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496616"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>ODBC를 사용하여 Azure 데이터 탐색기에 연결

개방형 데이터베이스[연결(ODBC)은](/sql/odbc/reference/odbc-overview)데이터베이스 액세스를 위해 널리 사용되는 응용 프로그램 프로그래밍 인터페이스(API)입니다. ODBC를 사용하여 전용 커넥터가 없는 응용 프로그램에서 Azure 데이터 탐색기에 연결합니다.

백그라운드에서 응용 프로그램은 ODBC 인터페이스에서 함수를 호출하며, 이 함수는 *드라이버라는*데이터베이스 별 모듈에서 구현됩니다. Azure Data Explorer는 SQL Server 통신[프로토콜(MS-TDS)의](kusto/api/tds/index.md)하위 집합을 지원하므로 SQL Server에 대한 ODBC 드라이버를 사용할 수 있습니다.

다음 비디오를 사용하여 ODBC 연결을 만드는 방법을 배울 수 있습니다. 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

또는 아래에 자세히 설명된 대로 [ODBC 데이터 원본을 구성할](#configure-the-odbc-data-source) 수 있습니다. 

이 문서에서는 SQL Server ODBC 드라이버를 사용하는 방법을 알아서 ODBC를 지원하는 모든 응용 프로그램에서 Azure 데이터 탐색기에 연결할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

다음이 필요합니다.

* [SQL Server 버전 17.2.0.1 이상운영](/sql/connect/odbc/download-odbc-driver-for-sql-server) 체제용 Microsoft ODBC 드라이버입니다.

## <a name="configure-the-odbc-data-source"></a>ODBC 데이터 원본 구성

다음 단계에 따라 SQL Server용 ODBC 드라이버를 사용하여 ODBC 데이터 원본을 구성합니다.

1. Windows에서 *ODBC 데이터 원본을*검색하고 ODBC 데이터 원본 데스크톱 앱을 엽니다.

1. **추가**를 선택합니다.

    ![데이터 원본 추가](media/connect-odbc/add-data-source.png)

1. **SQL Server의 ODBC 드라이버 17을** 선택한 다음 **완료합니다.**

    ![드라이버 선택](media/connect-odbc/select-driver.png)

1. 연결 및 연결할 클러스터에 대한 이름과 설명을 입력한 다음 **다음을 선택합니다.** 클러스터 URL은 * \<ClusterName\>형식이어야\< 합니다. 지역\>.kusto.windows.net*.

    ![서버 선택](media/connect-odbc/select-server.png)

1. **다음**에 **통합 된 활성 디렉터리를** 선택합니다.

    ![Active Directory 통합](media/connect-odbc/active-directory-integrated.png)

1. 다음 다음 샘플 데이터로 **Next**데이터베이스를 선택합니다.

    ![기본 데이터베이스 변경](media/connect-odbc/change-default-database.png)

1. 다음 화면에서 모든 옵션을 기본값으로 그대로 두고 **완료를**선택합니다.

1. **테스트 데이터 원본을 선택합니다.**

    ![테스트 데이터 원본](media/connect-odbc/test-data-source.png)

1. 테스트가 성공했는지 확인한 다음 **확인을**선택합니다. 테스트가 성공하지 못한 경우 이전 단계에서 지정한 값을 확인하고 클러스터에 연결할 수 있는 충분한 권한이 있는지 확인합니다.

    ![테스트 성공](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>다음 단계

* [Tableau에서 Azure 데이터 탐색기에 연결](tableau.md)