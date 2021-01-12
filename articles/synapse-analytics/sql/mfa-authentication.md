---
title: Använda Multi-Factor AAD-autentisering
description: Synapse SQL-support anslutningar från SQL Server Management Studio (SSMS) med Active Directory Universal Authentication.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.custom: has-adal-ref
ms.openlocfilehash: 511d5841652b1f7b6487a42341ec392cb86d2b3a
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98120384"
---
# <a name="use-multi-factor-aad-authentication-with-synapse-sql-ssms-support-for-mfa"></a>Använd Multi-Factor AAD-autentisering med Synapse SQL (SSMS-stöd för MFA)

Synapse SQL-support anslutningar från SQL Server Management Studio (SSMS) med *Active Directory Universal Authentication*. 

I den här artikeln beskrivs skillnaderna mellan olika autentiseringsalternativ och de begränsningar som är associerade med att använda Universal Authentication. 

**Hämta den senaste SSMS** – på klient datorn laddar du ned den senaste versionen av SSMS, från [nedladdnings SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

För alla funktioner som beskrivs i den här artikeln använder du minst juli 2017, version 17,2.  Dialog rutan senaste anslutning bör se ut ungefär som på följande bild:

![Skärm bild som visar dialog rutan Anslut till server där du kan välja ett server namn och autentiserings alternativ.](./media/mfa-authentication/1mfa-universal-connect.png "Slutför rutan användar namn.")  

## <a name="the-five-authentication-options"></a>De fem alternativen för autentisering  

Active Directory Universal Authentication stöder de två icke-interaktiva autentiseringsmetoderna:
    - `Active Directory - Password` anspråksautentisering
    - `Active Directory - Integrated` anspråksautentisering

Det finns två icke-interaktiva autentiseringsscheman och kan användas i många olika program (ADO.NET, JDCB, ODC osv.). Dessa två metoder resulterar aldrig i popup-dialog rutor:

- `Active Directory - Password`
- `Active Directory - Integrated`

Den interaktiva metoden är att även har stöd för Azure AD Multi-Factor Authentication (MFA):

- `Active Directory - Universal with MFA`

Azure AD MFA bidrar till att skydda åtkomsten till data och program och tillgodoser samtidigt användarens önskemål om en enkel inloggningsprocess. Den ger stark autentisering med en rad enkla verifierings alternativ (telefonsamtal, textmeddelande, smartkort med PIN-kod eller meddelande för mobilapp), så att användarna kan välja den metod de föredrar. Interaktiv MFA med Azure AD kan resultera i en popup-dialogruta för verifiering.

En beskrivning av Multi-Factor Authentication finns i [Multi-Factor Authentication](../../active-directory/authentication//concept-mfa-howitworks.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD-domän namn eller klient-ID-parameter

Från och med [SSMS version 17](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)kan användare som importeras till den aktuella Active Directory från andra Azure Active Directory som gäst användare ange Azure AD-domännamnet eller klient-ID när de ansluter. 

Gäst användare inkluderar användare som har bjudits in från andra Azure-annonser, Microsoft-konton som outlook.com, hotmail.com, live.com eller andra konton som gmail.com. Den här informationen tillåter **Active Directory universell med MFA-autentisering** för att identifiera rätt autentiserings-utfärdare. Det här alternativet krävs också för att stödja Microsoft-konton (MSA) som outlook.com, hotmail.com, live.com eller icke-MSA konton. 

Alla dessa användare som ska autentiseras med Universal Authentication måste ange sitt Azure AD-domännamn eller klient-ID. Den här parametern representerar det aktuella Azure AD-domännamnet/klient-ID som Azure-servern är länkad till. 

Om till exempel Azure Server är associerad med Azure AD-domän `contosotest.onmicrosoft.com` där användaren `joe@contosodev.onmicrosoft.com` är värd för en importerad användare från Azure AD `contosodev.onmicrosoft.com` -domän, är domän namnet som krävs för att autentisera användaren `contosotest.onmicrosoft.com` . 

När användaren är en inbyggd användare av Azure AD som är länkad till Azure Server och inte är ett MSA-konto krävs inget domän namn eller klient-ID. 

Om du vill ange parametern (från och med SSMS version 17,2) går du till dialog rutan **Anslut till databas** , fyller i dialog rutan, väljer **Active Directory-universell med MFA** -autentisering, väljer **alternativ**, fyller i rutan **användar namn** och väljer sedan fliken **anslutnings egenskaper** . 

Kontrol lera rutan **AD-domännamn eller klient-ID** och tillhandahålla autentiserings utfärdare, till exempel domän namnet (**CONTOSOTEST.ONMICROSOFT.com**) eller GUID för klient-ID: t.  

   ![Skärm bild som visar Anslut till server på fliken anslutnings egenskaper med angivna värden.](./media/mfa-authentication/mfa-tenant-ssms.png)

Om du kör SSMS 18. x eller senare behövs inte längre AD-domännamnet eller klient-ID: t för gäst användare eftersom 18. x eller senare identifierar det automatiskt.

   ![MFA-Tenant-SSMS](./media/mfa-authentication/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Support för Azure AD Business till företag   
Azure AD-användare som stöds för Azure AD B2B-scenarier som gäst användare (se [Vad är Azure B2B-samarbete](../../active-directory/external-identities/what-is-b2b.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) kan endast ansluta till SYNAPSE-SQL som en del av medlemmarna i en grupp som skapats i den aktuella Azure AD och mappas manuellt med Transact-SQL- `CREATE USER` uttrycket i en specifik databas. 

Om `steve@gmail.com` till exempel är inbjuden till Azure AD `contosotest` (med Azure AD-domänen `contosotest.onmicrosoft.com` ) måste en Azure AD-grupp, till exempel, `usergroup` skapas i Azure AD som innehåller `steve@gmail.com` medlemmen. Sedan måste den här gruppen skapas för en särskild databas (dvs. databas) av Azure AD SQL-administratören eller Azure AD DBO genom att köra ett Transact-SQL- `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` uttryck. 

När databas användaren har skapats `steve@gmail.com` kan användaren logga in till `MyDatabase` med alternativet SSMS-autentisering `Active Directory – Universal with MFA support` . 

Usergroup har som standard bara Connect-behörigheten och all ytterligare data åtkomst som behöver beviljas på normalt sätt. 

Som gäst användare `steve@gmail.com` måste markera kryss rutan och lägga till AD-domännamnet `contosotest.onmicrosoft.com` i dialog rutan SSMS **Connection Property** . Alternativet **AD-domännamn eller klient-ID** stöds bara för universellt med MFA-anslutnings alternativ, annars är det nedtonad.

## <a name="universal-authentication-limitations-for-synapse-sql"></a>Begränsningar för Universal-autentisering för Synapse SQL

- SSMS och SqlPackage.exe är de enda verktyg som för närvarande är aktiverade för MFA genom Active Directory Universal-autentisering.
- SSMS version 17,2, stöder samtidig åtkomst till flera användare med hjälp av Universal Authentication med MFA. Version 17,0 och 17,1, begränsad en inloggning för en instans av SSMS med hjälp av Universal Authentication till ett enda Azure Active Directory konto. Om du vill logga in som ett annat Azure AD-konto måste du använda en annan instans av SSMS. (Den här begränsningen är begränsad till Active Directory Universal Authentication. du kan logga in på olika servrar med Active Directory lösenordsautentisering, Active Directory integrerad autentisering eller SQL Server autentisering).
- SSMS stöder Active Directory Universal Authentication för Object Explorer, Frågeredigeraren och visualisering av fråge arkiv.
- SSMS version 17,2 ger stöd för DacFx-guiden för export/extrahering/distribution av data databaser. När en enskild användare har autentiserats via dialog rutan inledande autentisering med hjälp av Universal Authentication, fungerar DacFx-guiden på samma sätt som för alla andra autentiseringsmetoder.
- SSMS-Tabelldesigner stöder inte Universal Authentication.
- Det finns inga ytterligare program varu krav för Active Directory Universal Authentication, förutom att du måste använda en version av SSMS som stöds.  
- Active Directory-autentiseringsbibliotek-versionen (ADAL) för Universal Authentication har uppdaterats till den senaste ADAL.dll 3.13.9 tillgänglig version. Se [Active Directory-autentiseringsbibliotek 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>Nästa steg
Mer information finns i avsnittet [ansluta till SYNAPSE SQL med SQL Server Management Studio](get-started-ssms.md) artikeln.