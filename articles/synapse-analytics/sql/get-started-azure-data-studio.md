---
title: Ansluta till Synapse SQL med Azure Data Studio
description: Använd Azure Data Studio för att ansluta till och fråga Synapse SQL i Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 6b039d934993d2acee630205c5b5e5d8e0f6145e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667600"
---
# <a name="connect-to-synapse-sql-with-azure-data-studio"></a>Ansluta till Synapse SQL med Azure Data Studio

> [!div class="op_single_selector"]
>
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [SQLCMD](get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

Du kan använda [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio?view=azure-sqldw-latest&preserve-view=true) för att ansluta till och fråga Synapse SQL i Azure Synapse Analytics. 

## <a name="connect"></a>Ansluta

Om du vill ansluta till Synapse SQL öppnar du Azure Data Studio och väljer **ny anslutning**.

![Öppna Azure Data Studio](./media/get-started-azure-data-studio/1-start.png)

Välj **Microsoft SQL Server** som **Anslutnings typ**.

Anslutningen kräver följande parametrar:

* **Server:** Server i formatet `<Azure Synapse workspace name>` -OnDemand.SQL.azuresynapse.net
* **Databas:** Databas namn

> [!NOTE]
> Om du vill använda en **Server utan SQL-pool** bör URL: en se ut så här:
>
> - `<Azure Synapse workspace name>`-ondemand.sql.azuresynapse.net.
>
> Om du vill använda en **särskild SQL-pool** bör URL: en se ut så här:
>
> - `<Azure Synapse workspace name>`. sql.azuresynapse.net

Välj **Windows-autentisering**, **Azure Active Directory** eller **SQL-inloggning** som **Autentiseringstyp**.

Om du vill använda **SQL-inloggning** som autentiseringstyp lägger du till användar namn/lösen ord-parametrar:

* **Användare:** Server användare i formuläret `<User>`
* **Lösen ord:** Lösen ord kopplat till användaren

Om du vill använda Azure Active Directory måste du välja autentiseringstypen som krävs.

![AAD-autentisering](./media/get-started-azure-data-studio/3-aad-auth.png)

Följande skärm bild visar **anslutnings information** för **Windows-autentisering**:

![Windows-autentisering](./media/get-started-azure-data-studio/3-windows-auth.png)

Följande skärm bild visar **anslutnings information** med **SQL-inloggning**:

![SQL-inloggning](./media/get-started-azure-data-studio/2-database-details.png)

Efter en lyckad inloggning bör du se en instrument panel så här: ![ instrument panelen](./media/get-started-azure-data-studio/4-dashboard.png)

## <a name="query"></a>Söka i data

När du har anslutit kan du fråga Synapse SQL med stöd för [Transact-SQL-uttryck (T-SQL)](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true) som stöds mot instansen. Kom igång genom att välja **ny fråga** från vyn instrument panel.

![Ny fråga](./media/get-started-azure-data-studio/5-new-query.png)

Du kan till exempel använda följande Transact-SQL-uttryck för att [fråga Parquet-filer](query-parquet-files.md) med en server lös SQL-pool:

```sql
SELECT COUNT(*)
FROM  
OPENROWSET(
    BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
    FORMAT='PARQUET'
)
```
## <a name="next-steps"></a>Nästa steg 
Utforska andra sätt att ansluta till Synapse SQL: 

- [SSMS](get-started-ssms.md)
- [Power BI](get-started-power-bi-professional.md)
- [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [SQLCMD](get-started-connect-sqlcmd.md)

Mer information finns på [använd Azure Data Studio för att ansluta och fråga efter data med en dedikerad SQL-pool i Azure Synapse Analytics](/sql/azure-data-studio/quickstart-sql-dw).
