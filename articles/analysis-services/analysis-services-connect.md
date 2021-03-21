---
title: Ansluta till Azure Analysis Services servrar | Microsoft Docs
description: Lär dig hur du ansluter till och hämtar data från en Analysis Services-server i Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 4abe1e9c6f9d7b62792936f816b9c46a937be41a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96499432"
---
# <a name="connecting-to-servers"></a>Ansluta till servrar

Den här artikeln beskriver hur du ansluter till en server med hjälp av data modellering och hanterings program som SQL Server Management Studio (SSMS) eller Visual Studio med Analysis Services projekt eller med klient rapporterings program som Microsoft Excel, Power BI Desktop eller anpassade program. Anslutningar till Azure Analysis Services använda HTTPS.

## <a name="client-libraries"></a>Klientbibliotek

[Hämta de senaste klient biblioteken](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true)

Alla anslutningar till en server, oavsett typ, kräver uppdaterade AMO-, ADOMD.NET-och OLEDB-klientcertifikat för att ansluta till och gränssnitt med en Analysis Services-server. För SSMS, Visual Studio, Excel 2016 och senare och Power BI, installeras eller uppdateras de senaste klient biblioteken med månads versioner. Men i vissa fall är det möjligt att ett program kanske inte har det senaste. Till exempel när principer fördröjer uppdateringar eller Microsoft 365 uppdateringar finns på den uppskjutna kanalen.

> [!NOTE]
> Klient biblioteken kan inte ansluta till Azure Analysis Services via proxyservrar som kräver ett användar namn och lösen ord. 

## <a name="server-name"></a>Servernamn

När du skapar en Analysis Services-server i Azure anger du ett unikt namn och den region där-servern ska skapas. När du anger Server namnet i en anslutning är Server namngivnings schema:

```
<protocol>://<region>/<servername>
```
 Om protokollet är en sträng i **Azure** är region den URI där servern skapades (till exempel westus.asazure.Windows.net) och servername är namnet på din unika server i regionen.

### <a name="get-the-server-name"></a>Hämta Server namnet

I **Azure Portal** > Server > **Översikt**  >  **Server namn**, kopierar du hela Server namnet. Om andra användare i din organisation också ansluter till den här servern kan du dela detta Server namn med dem. När du anger ett server namn måste hela sökvägen användas.

![Hämta servernamnet i Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

> [!NOTE]
> Protokollet för regionen USA, östra 2, är **aspaaseastus2**.

## <a name="connection-string"></a>Anslutningssträng

När du ansluter till Azure Analysis Services med hjälp av tabell objekts modellen använder du följande anslutnings sträng format:

###### <a name="integrated-azure-active-directory-authentication"></a>Integrerad Azure Active Directory-autentisering

Integrerad autentisering hämtar cachen för Azure Active Directory-autentiseringsuppgifter om den är tillgänglig. Annars visas fönstret Azure-inloggning.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Azure Active Directory autentisering med användar namn och lösen ord

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Windows-autentisering (integrerad säkerhet)

Använd det Windows-konto som kör den aktuella processen.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>Anslut med hjälp av en. ODC-fil

Med äldre versioner av Excel kan användarna ansluta till en Azure Analysis Services-server med hjälp av en ODC-fil (Office Data Connection). Mer information finns i [skapa en ODC-fil (Office Data Connection)](analysis-services-odc.md).

## <a name="connect-as-a-linked-server-from-sql-server"></a>Anslut som en länkad server från SQL Server

SQL Server kan ansluta till en Azure Analysis Services resurs som en [länkad server](/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine) genom att ange MSOLAP som Provider för data källa. Innan du konfigurerar en länkad server anslutning måste du installera det senaste [MSOLAP-klient biblioteket](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true) (Provider). 

För att länkade Server anslutningar ska Azure Analysis Services, måste MSOLAP-providern instansieras utanför SQL Server processen. När du konfigurerar alternativ för länkad server kontrollerar du att alternativet **Tillåt InProcess** **inte är markerat**.

Om **Tillåt** inaktivitet är markerat och providern instansieras i SQL Server processen returneras följande fel:

```
OLE DB provider "MSOLAP" for linked server "(null)" returned message "The following system error occurred: ".

OLE DB provider "MSOLAP" for linked server "(null)" returned message "The connection failed because user credentials are needed and Sign-In UI is not allowed.".

Msg 7303, Level 16, State 1, Line 2
Cannot initialize the data source object of OLE DB provider "MSOLAP" for linked server "(null)".
```



## <a name="next-steps"></a>Nästa steg

[Anslut till Excel](analysis-services-connect-excel.md)    
[Anslut till Power BI](analysis-services-connect-pbi.md)   
[Hantera servern](analysis-services-manage.md)