---
title: Versionshistorik för Azure AD Connect Health
description: I det här dokumentet beskrivs versioner för Azure AD Connect Health och vad som har inkluderats i dessa versioner.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/10/2020
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 408e972eb5950baa75fa1bdf33fb2cd507154bcb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612848"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Versionshistorik för Azure AD Connect Health
Azure Active Directorys teamet uppdaterar regelbundet Azure AD Connect Health med nya funktioner. Den här artikeln innehåller de versioner och funktioner som har släppts.  

> [!NOTE]
> Anslut hälso agenter uppdateras automatiskt när en ny version släpps. Kontrol lera att inställningarna för automatisk uppgradering är aktiverade från Azure Portal.
>

Azure AD Connect Health för synkronisering är integrerat med Azure AD Connect installationen. Läs mer om [Azure AD Connect versions historik](./reference-connect-version-history.md) för feedback, rösta på [Connect Health User Voice Channel](https://feedback.azure.com/forums/169401-azure-active-directory/filters/new?category_id=165591)

## <a name="march-2021"></a>Mars 2021
**Agent uppdatering**

- Azure AD Connect Health Agent för AD FS (version 3.1.95.0)

  - Korrigera för att lösa NT4-formaterade användar namn till ett UPN vid inloggnings händelser.
  - Korrigera för att identifiera fel scenarier för program identifierare med en särskild felkod.
  - Ändringar för att lägga till en ny egenskap för OAuth-klient-ID.
  - Korrigera för att Visa korrekta värden i fälten **protokoll** och **AUTENTISERINGSTYP** i Azure AD Sign-In rapport för vissa inloggnings scenarier.
  - Korrigera för att visa IP-adresser i Azure AD Sign-In rapportens IP-kedje fält i ordning för begäran.
  - Ändringar för att införa ett nytt fält för att särskilja om den sekundära autentiseringen begärdes under en inloggning.
  - Korrigera för egenskapen AD FS-programid som ska visas i Azure AD Sign-In-rapporten.

## <a name="april-2020"></a>April 2020
**Agent uppdatering**

- Azure AD Connect Health Agent för AD FS (version 3.1.77.0)

   - Fel korrigering för "ogiltigt SPN-namn (Service Principal Name) för AD FS tjänst" avisering, för vilken aviseringen rapporterades felaktigt.


## <a name="july-2019"></a>Juli 2019
**Agent uppdatering**
* Azure AD Connect Health Agent för AD FS (version 3.1.59.0) 
   1. Text ändring i TestWindowsTransport
   2. Ändringar för AD FS RP-uppladdning
   
* Azure AD Connect Health Agent för AD FS (version 3.1.56.0) 
   1. Lägg till TestWindowsTransport test och ta bort WsTrust slut punkter checkar in CheckOffice365Endpoints-test
   2. Logga operativ system och .NET-information
   3. Öka överförings storleken för RP-konfigurationsfilen till 1 MB.
   4. Felkorrigeringar
   
* Azure AD Connect Health Agent för AD DS (version 3.1.56.0) 
   1. Logga operativ system och .NET-information 
   2. Felkorrigeringar

## <a name="may-2019"></a>Maj 2019
**Uppdatering av agent:** 
* Azure AD Connect Health Agent för AD FS (version 3.1.51.0) 
   1. Fel korrigering för att skilja mellan flera inloggnings tillägg som delar samma klient-Request-ID.
   2. Fel korrigering för parsning av felaktigt användar namn/lösen ord på språklokaliserade servrar.   

## <a name="april-2019"></a>April 2019
**Uppdatering av agent:** 
* Azure AD Connect Health Agent för AD FS (version 3.1.46.0) 
   1. Åtgärd för att kontrol lera dubbla SPN-aviseringar för ADFS

## <a name="march-2019"></a>Mars 2019
**Uppdatering av agent:** 
* Azure AD Connect Health Agent för AD DS (version 3.1.41.0)  
   1. .NET-versions samling
   2. Förbättra insamling av prestanda räknare när vissa kategorier saknas
   3. Fel korrigering för att förhindra att flera instanser av övervaknings agenten skapas

* Azure AD Connect Health Agent för AD FS (version 3.1.41.0) 
   1. Integrera och uppgradera AD FS test skript med ADFSToolBox
   2. Implementera .NET-versions samling
   3. Förbättra insamling av prestanda räknare när vissa kategorier saknas
   4. Fel korrigering för att förhindra att flera instanser av övervaknings agenten skapas


## <a name="november-2018"></a>November 2018
**Nya GA-funktioner:** 
* Azure AD Connect Health för synkronisering – diagnostisera och åtgärda dubblerade synkroniseringsfel från portalen

**Uppdatering av agent:** 
* Azure AD Connect Health Agent för AD DS (version 3.1.24.0) 
   1. Transport Layer Security (TLS) protokoll version 1,2 efterlevnad och tillämpning
   2. Minska varnings brus i global katalog
   3. Fel korrigeringar för Health Agent-registrering

* Azure AD Connect Health Agent för AD FS (version 3.1.24.0)  
   1. Transport Layer Security (TLS) protokoll version 1,2 efterlevnad och tillämpning
   2. Stöd för Test-ADFSRequestToken för lokaliserat operativ system
   3. Lösa problem med att låsa diagnos agenten
   4. Fel korrigeringar för Health Agent-registrering

## <a name="august-2018"></a>Augusti 2018 
*  Azure AD Connect Health Agent för Sync (version 3.1.7.0) lanserad med Azure AD Connect version 1.1.880.0    
   1. Snabb korrigering för [övervaknings agentens problem med hög CPU med .NET Framework KB-versioner](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)

## <a name="june-2018"></a>Juni 2018 
**Nya för hands versions funktioner:** 
* Azure AD Connect Health för synkronisering – diagnostisera och åtgärda dubblerade synkroniseringsfel från portalen 

**Uppdatering av agent:** 
* Azure AD Connect Health Agent för AD DS (version 3.1.7.0)    
  1. Snabb korrigering för [övervaknings agentens problem med hög CPU med .NET Framework KB-versioner](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
   
* Azure AD Connect Health Agent för AD FS (version 3.1.7.0)  
  1. Snabb korrigering för [övervaknings agentens problem med hög CPU med .NET Framework KB-versioner](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
  2. Korrigeringar av test resultat på sekundär server för ADFS Server 2016
   
* Azure AD Connect Health Agent för AD FS (version 3.1.2.0)  
  1. Snabb korrigering för hantering av agent minne och relaterade aviseringar specifikt för version 3.0.244.0


## <a name="may-2018"></a>Maj 2018
**Uppdatering av agent:**
* Azure AD Connect Health Agent för AD DS (version 3.0.244.0)
  1. Förbättring av agent sekretess  
  2. Fel korrigeringar och allmänna förbättringar

* Azure AD Connect Health Agent för AD FS (version 3.0.244.0)
  1. Förbättringar av agent diagnostik och relaterade PowerShell-moduler
  2. Förbättring av agent sekretess  
  3. Fel korrigeringar och allmänna förbättringar

* Azure AD Connect Health Agent för Sync (version 3.0.164.0) lanserad med Azure AD Connect version 1.1.819.0 
  1. Förbättring av agent sekretess  
  2. Fel korrigeringar och allmänna förbättringar


## <a name="march-2018"></a>Mars 2018
**Nya för hands versions funktioner:**
* Azure AD Connect Health för att AD FS riskfyllda IP-rapporter och aviseringar.

**Uppdatering av agent:**

* Azure AD Connect Health Agent för AD DS (version 3.0.176.0)
  1. Förbättringar av agent tillgänglighet 
  2. Fel korrigeringar och allmänna förbättringar
* Azure AD Connect Health Agent för AD FS (version 3.0.176.0)
  1. Förbättringar av agent tillgänglighet 
  2. Fel korrigeringar och allmänna förbättringar
* Azure AD Connect Health Agent för Sync (version 3.0.129.0) lanserad med Azure AD Connect version 1.1.750.0  
  1. Förbättringar av agent tillgänglighet 
  2. Fel korrigeringar och allmänna förbättringar

## <a name="december-2017"></a>December 2017
**Uppdatering av agent:**

* Azure AD Connect Health Agent för AD DS (version 3.0.145.0)
  1. Förbättringar av agent tillgänglighet 
  2. Nya agent fel söknings kommandon har lagts till
  3. Fel korrigeringar och allmänna förbättringar
* Azure AD Connect Health Agent för AD FS (version 3.0.145.0)
  1. Nya agent fel söknings kommandon har lagts till
  2. Förbättringar av agent tillgänglighet 
  3. Fel korrigeringar och allmänna förbättringar
  
## <a name="october-2017"></a>Oktober 2017
**Uppdatering av agent:**

 * Azure AD Connect Health Agent för Sync (version 3.0.129.0) lanserad med Azure AD Connect version 1.1.649.0
<br></br> Ett problem med kompatibilitet har åtgärd ATS mellan Azure AD Connect och Azure AD Connect Health Agent för synkronisering. Det här problemet påverkar kunder som utför Azure AD Connect uppgradering på plats till version 1.1.647.0, men som för närvarande har 3.0.127.0 för hälso tillstånds agent. Efter uppgraderingen kan hälso agenten inte längre skicka hälso information om Azure AD Connect-synkroniseringstjänsten till Azure AD Hälsotjänst. Med den här korrigeringen installeras Health Agent-version 3.0.129.0 under Azure AD Connect uppgradering på plats. 3.0.129.0 för hälso tillstånds agenten har inte kompatibilitetsproblem med Azure AD Connect version 1.1.649.0.

## <a name="july-2017"></a>Juli 2017
**Uppdatering av agent:**

* Azure AD Connect Health Agent för AD DS (version 3.0.68.0)
  1. Fel korrigeringar och allmänna förbättringar
  2. Support för suveräna moln
* Azure AD Connect Health Agent för AD FS (version 3.0.68.0)
  1. Fel korrigeringar och allmänna förbättringar
  2. Support för suveräna moln
* Azure AD Connect Health Agent för Sync (version 3.0.68.0) lanserad med Azure AD Connect version 1.1.614.0
  1. Stöd för Microsoft Azure Government molnet och Microsoft Cloud Tyskland

## <a name="april-2017"></a>April 2017      
**Uppdatering av agent:**

* Azure AD Connect Health Agent för AD FS (version 3.0.12.0)
  1. Fel korrigeringar och allmänna förbättringar
* Azure AD Connect Health Agent för AD DS (version 3.0.12.0)
  1. Förbättringar av uppladdning av prestanda räknare
  2. Fel korrigeringar och allmänna förbättringar

## <a name="october-2016"></a>Oktober 2016
**Uppdatering av agent:**

* Azure AD Connect Health Agent för AD FS (version 2.6.408.0)
* Förbättringar vid identifiering av klientens IP-adresser i autentiseringsbegäranden
* Fel korrigeringar relaterade till aviseringar
* Azure AD Connect Health Agent för AD DS (version 2.6.408.0)
* Fel korrigeringar relaterade till aviseringar.
* Azure AD Connect Health Agent för Sync (version 2.6.353.0) lanserad med Azure AD Connect version 1.1.281.0
* Ange de data som krävs för fel rapporterna för synkronisering
* Fel korrigeringar relaterade till aviseringar

**Nya för hands versions funktioner:**

* Fel rapporter för synkroniseringsfel för Azure AD Connect

**Nya funktioner:**

* Azure AD Connect Health för fältet AD FS-IP-adress finns i rapporten om de 50 främsta användarna med felaktigt användar namn/lösen ord.

## <a name="july-2016"></a>Juli 2016
**Nya för hands versions funktioner:**

* [Azure AD Connect Health för AD DS](how-to-connect-health-adds.md).

## <a name="january-2016"></a>Januari 2016
**Uppdatering av agent:**

* Azure AD Connect Health Agent för AD FS (version 2.6.91.1512)

**Nya funktioner:**

* [Test anslutnings verktyg för Azure AD Connect Health agenter](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>November 2015
**Nya funktioner:**

* Stöd för [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](how-to-connect-health-operations.md#manage-access-with-azure-rbac)

**Nya för hands versions funktioner:**

* [Azure AD Connect Health för synkronisering](how-to-connect-health-sync.md).

**Korrigerade problem:**

* Fel korrigeringar för fel som visas under agent registreringarna.

## <a name="september-2015"></a>September 2015
**Nya funktioner:**

* Fel lösen ords rapport för användar namn för AD FS
* Stöd för att konfigurera oautentiserad HTTP-proxy
* Stöd för att konfigurera agenten på Server Core
* Förbättringar av aviseringar för AD FS
* Förbättringar i Azure AD Connect Health Agent för AD FS för anslutning och data uppladdning.

**Korrigerade problem:**

* Fel korrigeringar i användnings insikter för AD FS fel typer.

## <a name="june-2015"></a>Juni 2015
**Första versionen av Azure AD Connect Health för AD FS och AD FS proxy.**

**Nya funktioner:**

* Aviseringar för övervakning AD FS-och AD FS-proxyservrar med e-postaviseringar.
* Enkel åtkomst till AD FS topologi och mönster i AD FS prestanda räknare.
* Trenden i lyckade token-begäranden på AD FS servrar grupperade efter program, autentiseringsmetoder, begär nätverks plats osv.
* Trender i misslyckade begär Anden på AD FS servrar grupperade efter program, fel typer osv.
* Enklare agent distribution med autentiseringsuppgifter för global administratör i Azure AD.  

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [att övervaka din lokala identitets infrastruktur och synkroniseringstjänst i molnet](./whatis-azure-ad-connect.md).
