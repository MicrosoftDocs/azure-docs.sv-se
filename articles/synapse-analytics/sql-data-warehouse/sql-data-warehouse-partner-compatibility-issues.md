---
title: Kompatibilitetsproblem med program från tredje part och Azure Synapse Analytics
description: Beskriver kända problem som program från tredje part kan hitta med Azure Synapse
services: synapse-analytics
author: periclesrocha
ms.service: synapse-analytics
ms.topic: troubleshooting
ms.subservice: sql
ms.date: 11/18/2020
ms.author: procha
ms.reviewer: jrasnick
ms.openlocfilehash: 3bad9d6464b41ff1b7ad03147d3a48c50c787cb0
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568122"
---
# <a name="compatibility-issues-with-third-party-applications-and-azure-synapse-analytics"></a>Kompatibilitetsproblem med program från tredje part och Azure Synapse Analytics

Program som skapats för SQL Server fungerar sömlöst med Azure Synapse dedikerade SQL-pooler. I vissa fall kanske dock funktioner och språkelement som ofta används i SQL Server inte är tillgängliga i Azure Synapse, eller så beter de sig annorlunda.

Den här artikeln innehåller kända problem som du kan stöta på när du använder program från tredje part med Azure Synapse Analytics. 

## <a name="tableau-error-an-attempt-to-complete-a-transaction-has-failed-no-corresponding-transaction-found"></a>Tableau-fel: "Ett försök att slutföra en transaktion har misslyckats. Ingen motsvarande transaktion hittades"

Från och med Azure Synapse-dedikerad SQL-poolversion 10.0.11038.0 kan vissa Tableau-frågor som gör lagrade procedursamtal misslyckas med följande felmeddelande: "**[Microsoft][ODBC Driver 17 for SQL Server][SQL Server]111214; Ett försök att slutföra en transaktion misslyckades. Ingen motsvarande transaktion hittades.**"

### <a name="cause"></a>Orsak

Det här är ett problem Azure Synapse dedikerad SQL-pool som orsakas av införandet av nya system lagrade procedurer som anropas automatiskt av ODBC- och JDBC-drivrutinerna. En av dessa system lagrade procedurer kan göra att öppna transaktioner avbryts om de misslyckas med körningen. Det här problemet kan inträffa beroende på klientprogramlogiken.

### <a name="solution"></a>Lösning
Kunder som ser det här problemet när de använder Tableau som är Azure Synapse dedikerade SQL-pooler bör ställa in FMTONLY på JA i SQL-anslutningen. För Tableau Desktop och Tableau Server bör du använda en TDC-fil (Tableau Data Source Customization) för att säkerställa att Tableau skickar den här parametern till drivrutinen.  

> [!NOTE] 
> Microsoft tillhandahåller inte stöd för verktyg från tredje part. Vi har testat att den här lösningen fungerar med Tableau Desktop 2020.3.2, men du bör använda den här lösningen på egen kapacitet.
>

* [Information om hur du gör globala anpassningar med en TDC-fil på Tableau Desktop finns i Tableau Desktop-dokumentationen.](https://help.tableau.com/current/pro/desktop/en-us/odbc_customize.htm)
* [Information om hur du gör globala anpassningar med en TDC-fil på Tableau Server finns i Använda en . TDC-fil med Tableau Server.](https://kb.tableau.com/articles/howto/using-a-tdc-file-with-tableau-server)

Exemplet nedan visar en Tableau TDC-fil som skickar parametern FMTONLY=YES till SQL-anslutningssträngen:

```json
<connection-customization class='azure_sql_dw' enabled='true' version='18.1'>
    <vendor name='azure_sql_dw' />
    <driver name='azure_sql_dw' />
    <customizations>        
        <customization name='odbc-connect-string-extras' value='UseFMTONLY=yes' />
    </customizations>
</connection-customization>
```
Kontakta Tableau-supporten om du vill ha mer information om hur du använder TDC-filer. 

## <a name="see-also"></a>Se även

* [T-SQL-språkelement för dedikerad SQL-pool i Azure Synapse Analytics.](./sql-data-warehouse-reference-tsql-language-elements.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
* [T-SQL-instruktioner som stöds för dedikerad SQL-pool i Azure Synapse Analytics.](./sql-data-warehouse-reference-tsql-statements.md)