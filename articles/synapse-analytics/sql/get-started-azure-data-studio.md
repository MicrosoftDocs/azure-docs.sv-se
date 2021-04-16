---
title: Ansluta till Synapse SQL med Azure Data Studio
description: Använd Azure Data Studio för att ansluta till och fråga Synapse SQL i Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 1529e45bd7f799a727a29c8c2e26f7ed77c4e2a0
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565601"
---
# <a name="connect-to-synapse-sql-with-azure-data-studio"></a>Ansluta till Synapse SQL med Azure Data Studio

> [!div class="op_single_selector"]
>
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [Sqlcmd](get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

Du kan använda [Azure Data Studio för](/sql/azure-data-studio/download-azure-data-studio?view=azure-sqldw-latest&preserve-view=true) att ansluta till och fråga Synapse SQL i Azure Synapse Analytics. 

## <a name="connect"></a>Ansluta

Om du vill ansluta Synapse SQL du Azure Data Studio och väljer **Ny anslutning.**

![Öppna Azure Data Studio](./media/get-started-azure-data-studio/1-start.png)

Välj **Microsoft SQL Server** som **Anslutningstyp.**

Anslutningen kräver följande parametrar:

* **Server:** Server i formuläret `<Azure Synapse workspace name>` -ondemand.sql.azuresynapse.net
* **Databas:** Databasnamn

> [!NOTE]
> Om du vill använda en **serverlös SQL-pool bör** URL:en se ut så här:
>
> - `<Azure Synapse workspace name>`-ondemand.sql.azuresynapse.net.
>
> Om du vill använda dedikerad **SQL-pool bör** URL:en se ut så här:
>
> - `<Azure Synapse workspace name>`.sql.azuresynapse.net

Välj **Windows-autentisering,** **Azure Active Directory** eller **SQL-inloggning** som **Autentiseringstyp.**

Om du **vill använda SQL-inloggning** som autentiseringstyp lägger du till parametrarna för användarnamn/lösenord:

* **Användare:** Serveranvändare i formuläret `<User>`
* **Lösenord:** Lösenord som är associerat med användaren

Om du Azure Active Directory autentiseringstyp måste du välja den autentiseringstyp som krävs.

![AAD-autentisering](./media/get-started-azure-data-studio/3-aad-auth.png)

Följande skärmbild visar **anslutningsinformationen för** **Windows-autentisering:**

![Windows-autentisering](./media/get-started-azure-data-studio/3-windows-auth.png)

Följande skärmbild visar **anslutningsinformationen med** **SQL-inloggning:**

![SQL-inloggning](./media/get-started-azure-data-studio/2-database-details.png)

Efter lyckad inloggning bör du se en instrumentpanel som den här: ![ Instrumentpanel](./media/get-started-azure-data-studio/4-dashboard.png)

## <a name="query"></a>Fråga

När du är ansluten kan du fråga Synapse SQL med [hjälp av Transact-SQL-instruktioner (T-SQL)](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true) som stöds mot instansen. Välj **Ny fråga i** instrumentpanelsvyn för att komma igång.

![Ny fråga](./media/get-started-azure-data-studio/5-new-query.png)

Du kan till exempel använda följande Transact-SQL-instruktion för att fråga [Parquet-filer med](query-parquet-files.md) hjälp av en serverlös SQL-pool:

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
- [Sqlcmd](get-started-connect-sqlcmd.md)

Besök Använd Azure Data Studio för att ansluta och fråga efter data med [hjälp av en dedikerad SQL-pool Azure Synapse Analytics](/sql/azure-data-studio/quickstart-sql-dw), för mer information.
