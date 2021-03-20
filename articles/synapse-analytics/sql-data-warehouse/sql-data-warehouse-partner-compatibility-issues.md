---
title: Kompatibilitetsproblem med program från tredje part och Azure Synapse Analytics
description: Beskriver kända problem som program från tredje part kan hitta med Azure Synapse
services: synapse-analytics
author: periclesrocha
ms.service: synapse-analytics
ms.topic: troubleshooting
ms.subservice: ''
ms.date: 11/18/2020
ms.author: procha
ms.reviewer: jrasnick
ms.openlocfilehash: a1031656eaa5125d07ae078773379270b26625e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98121387"
---
# <a name="compatibility-issues-with-third-party-applications-and-azure-synapse-analytics"></a>Kompatibilitetsproblem med program från tredje part och Azure Synapse Analytics

Program som skapats för SQL Server fungerar smidigt med Azure Synapse-dedikerade SQL-pooler. I vissa fall kanske funktioner och språk element som ofta används i SQL Server inte är tillgängliga i Azure-Synapse, eller så kan de bete sig annorlunda.

Den här artikeln innehåller kända problem som du kan stöta på när du använder program från tredje part med Azure Synapse Analytics. 

## <a name="tableau-error-an-attempt-to-complete-a-transaction-has-failed-no-corresponding-transaction-found"></a>Tableau-fel: ett försök att slutföra en transaktion misslyckades. Ingen motsvarande transaktion hittades "

Från och med Azure Synapse Dedicated SQL-pool version 10.0.11038.0 kan vissa Tableau-frågor som gör att lagrade procedur anrop Miss lyckas med följande fel meddelande: "**[Microsoft] [ODBC driver 17 för SQL Server] [SQL Server] 111214; Ett försök att slutföra en transaktion misslyckades. Ingen motsvarande transaktion hittades.**"

### <a name="cause"></a>Orsak

Det här är ett problem i Azure Synapse Dedicated SQL-poolen som orsakas av introduktionen av nya system lagrade procedurer som anropas automatiskt av ODBC-och JDBC-drivrutinerna. En av dessa system lagrade procedurer kan orsaka att öppna transaktioner avbryts om de inte körs. Det här problemet kan inträffa beroende på klient program logiken.

### <a name="solution"></a>Lösning
Kunderna ser det här problemet när du använder Tableau som är anslutna till Azure Synapse dedikerade SQL-pooler ska ange FMTONLY till Ja i SQL-anslutningen. För Tableau Desktop och Tableau-servern bör du använda en Tableau (TDC)-fil för data källan för att säkerställa att Tableau skickar den här parametern till driv rutinen.  

> [!NOTE] 
> Microsoft tillhandahåller inte support för verktyg från tredje part. Vi har testat att den här lösningen fungerar med Tableau Desktop 2020.3.2, men du bör använda den här lösningen på din egen kapacitet.
>

* [Information om hur du gör globala anpassningar med en TDC-fil på Tableau Desktop finns i Tableau Desktop-dokumentationen.](https://help.tableau.com/current/pro/desktop/en-us/odbc_customize.htm)
* [Information om hur du gör globala anpassningar med en TDC-fil på Tableau-servern finns i använda en. TDC-fil med Tableau-Server.](https://kb.tableau.com/articles/howto/using-a-tdc-file-with-tableau-server)

Exemplet nedan visar en Tableau TDC-fil som skickar FMTONLY = YES-parametern till SQL-anslutningssträngen:

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
* [T-SQL-uttryck som stöds för dedikerad SQL-pool i Azure Synapse Analytics.](./sql-data-warehouse-reference-tsql-statements.md)