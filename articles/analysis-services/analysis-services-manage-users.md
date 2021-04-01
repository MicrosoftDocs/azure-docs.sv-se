---
title: Azure Analysis Services autentisering och användar behörigheter | Microsoft Docs
description: I den här artikeln beskrivs hur Azure Analysis Services använder Azure Active Directory (Azure AD) för identitets hantering och användarautentisering.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 551bae56565140da3754e74a23b1cc18087f1171
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96487447"
---
# <a name="authentication-and-user-permissions"></a>Autentisering och användarbehörigheter

Azure Analysis Services använder Azure Active Directory (Azure AD) för identitets hantering och användarautentisering. Alla användare som skapar, hanterar eller ansluter till en Azure Analysis Services-server måste ha en giltig användar identitet i en [Azure AD-klient](../active-directory/fundamentals/active-directory-whatis.md) i samma prenumeration.

Azure Analysis Services stöder [Azure AD B2B-samarbete](../active-directory/external-identities/what-is-b2b.md). Med B2B kan användare utanför en organisation bjudas in som gäst användare i en Azure AD-katalog. Gäster kan vara från en annan Azure AD-innehavaradministratör eller en giltig e-postadress. När du har bjudit in och användaren accepterar den inbjudan som skickas via e-post från Azure läggs användar identiteten till i klient katalogen. Dessa identiteter kan läggas till i säkerhets grupper eller som medlemmar i en Server administratör eller databas roll.

![Arkitektur för Azure Analysis Services-autentisering](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>Autentisering

Alla klient program och verktyg använder ett eller flera av de Analysis Services [klient biblioteken](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true) (AMO, MSOLAP, ADOMD) för att ansluta till en server. 

Alla tre klient biblioteken stöder både det interaktiva flödet i Azure AD och icke-interaktiva autentiseringsmetoder. De två icke-interaktiva metoderna, Active Directory lösen ord och Active Directory integrerade autentiseringsmetoder kan användas i program som använder AMOMD och MSOLAP. Dessa två metoder resulterar aldrig i popup-dialogrutor.

Klient program som Excel och Power BI Desktop och verktyg som SSMS och Analysis Services projekt tillägget för Visual Studio installerar de senaste versionerna av biblioteken när de har uppdaterats till den senaste versionen. Power BI Desktop, SSMS och Analysis Services projekt tillägget uppdateras varje månad. Excel [uppdateras med Microsoft 365](https://support.microsoft.com/office/when-do-i-get-the-newest-features-for-microsoft-365-da36192c-58b9-4bc9-8d51-bb6eed468516). Microsoft 365 uppdateringar är mindre frekventa och vissa organisationer använder den uppskjutna kanalen, vilket innebär att uppdateringar uppskjuts upp till tre månader.

Beroende på vilket klient program eller verktyg du använder kan typen av autentisering och hur du loggar in vara annorlunda. Varje program kan ha stöd för olika funktioner för att ansluta till moln tjänster som Azure Analysis Services.

Power BI Desktop-, Visual Studio-och SSMS-support Active Directory Universal Authentication, en interaktiv metod som också stöder Azure AD Multi-Factor Authentication (MFA). Azure AD MFA hjälper till att skydda åtkomsten till data och program samtidigt som du ger en enkel inloggnings process. Den ger stark autentisering med flera verifierings alternativ (telefonsamtal, textmeddelande, smartkort med PIN-kod eller meddelande om mobilapp). Interaktiv MFA med Azure AD kan resultera i en popup-dialogruta för verifiering. **Universal Authentication rekommenderas**.

Om du loggar in på Azure med hjälp av ett Windows-konto och universell autentisering inte är markerat eller tillgängligt (Excel) krävs [Active Directory Federation Services (AD FS) (AD FS)](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) . Med Federation autentiseras Azure AD-och Microsoft 365-användare med lokala autentiseringsuppgifter och kan komma åt Azure-resurser.

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

Azure Analysis Services-servrar stöder anslutningar från [SSMS v 17.1](/sql/ssms/download-sql-server-management-studio-ssms) och högre genom att använda Windows-autentisering, Active Directory lösenordsautentisering och Active Directory Universal Authentication. I allmänhet rekommenderar vi att du använder Active Directory Universal Authentication eftersom:

*  Stöder interaktiva och icke-interaktiva autentiseringsmetoder.

*  Stöder Azure B2B-gäst användare som bjuds in till Azure som klient organisation. När du ansluter till en server måste gäst användare välja Active Directory Universal-autentisering när de ansluter till servern.

*  Stöder Multi-Factor Authentication (MFA). Azure AD MFA hjälper till att skydda åtkomsten till data och program med ett antal verifierings alternativ: telefonsamtal, textmeddelande, smartkort med PIN-kod eller meddelande om mobilapp. Interaktiv MFA med Azure AD kan resultera i en popup-dialogruta för verifiering.

### <a name="visual-studio"></a>Visual Studio

Visual Studio ansluter till Azure Analysis Services med hjälp av Active Directory Universal Authentication med MFA-stöd. Användarna uppmanas att logga in på Azure vid den första distributionen. Användarna måste logga in på Azure med ett konto med Server administratörs behörighet på den server som de distribuerar till. När du loggar in på Azure första gången tilldelas en token. Token cachelagras i minnet för framtida åter anslutning.

### <a name="power-bi-desktop"></a>Power BI Desktop

Power BI Desktop ansluter till Azure Analysis Services med hjälp av Active Directory Universal Authentication med MFA-stöd. Användarna uppmanas att logga in på Azure på den första anslutningen. Användarna måste logga in på Azure med ett konto som ingår i en Server administratör eller databas roll.

### <a name="excel"></a>Excel

Excel-användare kan ansluta till en server med hjälp av ett Windows-konto, ett organisations-ID (e-postadress) eller en extern e-postadress. Externa e-postidentiteter måste finnas i Azure AD som gäst användare.

## <a name="user-permissions"></a>Användarbehörigheter

**Server administratörer** är bara för en Azure Analysis Services Server instans. De ansluter till verktyg som Azure Portal, SSMS och Visual Studio för att utföra åtgärder som att lägga till databaser och hantera användar roller. Som standard läggs den användare som skapar-servern automatiskt som en Analysis Services Server administratör. Andra administratörer kan läggas till med hjälp av Azure Portal eller SSMS. Server administratörer måste ha ett konto i Azure AD-klienten i samma prenumeration. Mer information finns i [hantera Server administratörer](analysis-services-server-admins.md). 

**Databas användare** ansluter till modell databaser med hjälp av klient program som Excel eller Power BI. Användare måste läggas till i databas roller. Databas roller definierar administratörs-, process-eller Läs behörighet för en databas. Det är viktigt att förstå databas användare i en roll med administratörs behörighet skiljer sig från Server administratörer. Som standard är Server administratörer också databas administratörer. Mer information finns i [Hantera databas roller och användare](analysis-services-database-users.md).

**Azure-resurs ägare**. Resurs ägare hanterar resurser för en Azure-prenumeration. Resurs ägare kan lägga till användar identiteter för Azure AD i ägare eller deltagar roller i en prenumeration med hjälp av **åtkomst kontroll** i Azure Portal eller med Azure Resource Manager mallar. 

![Åtkomst kontroll i Azure Portal](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

Roller på den här nivån gäller för användare eller konton som behöver utföra uppgifter som kan utföras i portalen eller med hjälp av Azure Resource Manager mallar. Läs mer i [Azure rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/overview.md). 

## <a name="database-roles"></a>Databas roller

 Roller som definierats för en tabell modell är databas roller. Det vill säga rollerna innehåller medlemmar som består av Azure AD-användare och säkerhets grupper som har särskilda behörigheter som definierar de åtgärder som medlemmarna kan vidta på en modell databas. En databasroll skapas som ett separat objekt i databasen och gäller endast för databasen som rollen har skapats i.   
  
 När du skapar ett nytt projekt för tabell modeller har modell projektet som standard inga roller. Roller kan definieras med hjälp av dialog rutan roll hanterare i Visual Studio. När roller definieras under modell projektets design, tillämpas de bara på modell arbets ytans databas. När modellen distribueras tillämpas samma roller på den distribuerade modellen. När en modell har distribuerats kan Server-och databas administratörer hantera roller och medlemmar med hjälp av SSMS. Mer information finns i [Hantera databas roller och användare](analysis-services-database-users.md).
  
## <a name="next-steps"></a>Nästa steg

[Hantera åtkomst till resurser med Azure Active Directory grupper](../active-directory/fundamentals/active-directory-manage-groups.md)   
[Hantera databas roller och användare](analysis-services-database-users.md)  
[Hantera serveradministratörer](analysis-services-server-admins.md)  
[Azure RBAC (rollbaserad åtkomstkontroll)](../role-based-access-control/overview.md)