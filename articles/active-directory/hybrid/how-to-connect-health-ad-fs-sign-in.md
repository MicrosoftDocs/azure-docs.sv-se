---
title: AD FS inloggningar i Azure AD med Connect Health | Microsoft Docs
description: Det här dokumentet beskriver hur du integrerar AD FS inloggningar med rapporten Azure AD Connect Health inloggningar.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/16/2021
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74769feba1d717a2f1a72d311f85bdfbeac7b7db
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103574800"
---
# <a name="ad-fs-sign-ins-in-azure-ad-with-connect-health---preview"></a>AD FS inloggningar i Azure AD med Connect Health – för hands version

AD FS inloggningar kan nu integreras i Azure Active Directory inloggnings rapporten genom att använda Connect Health. [Rapporten Azure AD-inloggningar](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-all-sign-ins#:~:text=Interactive%20user%20sign-ins%20are%20sign-ins%20where%20a%20user,to%20Azure%20AD%20or%20to%20a%20helper%20app.) innehåller information om när användare, program och hanterade resurser loggar in på Azure AD och åtkomst resurser. 

Connect Health för AD FS agent korrelerar flera händelse-ID: n från AD FS, beroende på Server version, för att ge information om begäran och fel information om begäran Miss lyckas. Den här informationen korreleras med rapport schemat för Azure AD-inloggning och visas i Azure AD Sign-In rapportens gränssnitt. Tillsammans med rapporten är en ny Log Analytics-dataström tillgänglig med AD FS data och en ny mall för Azure Monitor arbets böcker. Mallen kan användas och ändras för en djupgående analys för scenarier som AD FS konto utelåsningar, felaktiga lösen ords försök och toppar av oväntade inloggnings försök.

## <a name="prerequisites"></a>Förutsättningar
* Azure AD Connect Health för AD FS installerat och uppgraderat till den senaste versionen.
* Rollen global administratör eller rapport läsare för att Visa Azure AD-inloggningar

## <a name="what-data-is-displayed-in-the-report"></a>Vilka data visas i rapporten?
De data som är tillgängliga speglar samma data som är tillgängliga för Azure AD-inloggningar. Fem flikar med information kommer att vara tillgängliga baserat på typen av inloggning, antingen Azure AD eller AD FS. Connect Health korrelerar händelser från AD FS, beroende på Server version, och matchar dem i AD FS schemat. 



#### <a name="user-sign-ins"></a>Användar inloggningar 
Varje flik i inloggnings bladet visar standardvärdena nedan:
* Inloggningsdatum
* ID för begäran
* Användar namn eller användar-ID
* Status för inloggningen
* IP-adress för enheten som används för inloggning
* Sign-In identifierare

#### <a name="authentication-method-information"></a>Information om autentiseringsmetod
Följande värden kan visas på fliken autentisering. Autentiseringsmetoden hämtas från AD FS gransknings loggar.

|Autentiseringsmetod|Beskrivning|
|-----|-----|
|Formulär|Autentisering av användar namn/lösen ord|
|Windows|Windows-integrerad autentisering|
|Certifikat|Autentisering med smartkort-/VirtualSmart-certifikat|
|WindowsHelloForBusiness|Det här fältet är för autentisering med Windows Hello för företag. (Microsoft Passport autentisering)|
|Enhet | Visas om enhetsautentisering har valts som "primär" autentisering från intranät/extra nät och enhetsautentisering utförs.  Det finns ingen separat användarautentisering i det här scenariot.| 
|Federerade|AD FS utförde inte autentiseringen, men skickade den till en tredjeparts identitets leverantör|
|Enkel inloggning |Om en token för enkel inloggning användes, visas det här fältet. Om SSO har en MFA visas den som multifaktor|
|Multifaktor|Om en token för enkel inloggning har ett MFA och som användes för autentisering, visas det här fältet som multifaktor|
|Azure MFA|Azure MFA har valts som ytterligare autentiseringsprovider i AD FS och användes för autentisering|
|ADFSExternalAuthenticationProvider|Det här fältet är om en tredjeparts autentiseringsprovider har registrerats och använts för autentisering|


#### <a name="ad-fs-additional-details"></a>AD FS ytterligare information
Följande information är tillgänglig för AD FS inloggningar:
* Servernamn
* IP-kedja
* Protokoll

### <a name="enabling-log-analytics-and-azure-monitor"></a>Aktivera Log Analytics och Azure Monitor
Log Analytics kan aktive ras för AD FS inloggningar och kan användas med andra Log Analytics integrerade komponenter, till exempel Sentinel.

> [!NOTE] 
> AD FS inloggningar kan öka Log Analytics kostnaden avsevärt, beroende på hur mycket inloggnings program som ska AD FS i din organisation. Om du vill aktivera och inaktivera Log Analytics markerar du kryss rutan för data strömmen.

Om du vill aktivera Log Analytics för funktionen går du till bladet Log Analytics och väljer ADFSSignIns-dataström. Det här alternativet gör att AD FS inloggningar kan flöda till Log Analytics.

Om du vill komma åt den uppdaterade Azure Monitor arbetsboksmall går du till "Azure Monitor mallar" och väljer arbets boken "inloggningar".
Mer information om arbets böcker finns i [Azure Monitor-arbetsböcker](https://aka.ms/adfssigninspreview).




### <a name="frequently-asked-questions"></a>Vanliga frågor och svar
***Vilka typer av inloggningar kan jag se?***
Inloggnings rapporten stöder inloggningar via O-auth-, WS-utfodras-, SAML-och WS-Trust protokoll. 

***Hur visas olika typer av inloggningar i inloggnings rapporten?***
Om en sömlös SSO-inloggning utförs, kommer det att finnas en rad för inloggningen med ett korrelations-ID.
Om en enskild Factor Authentication utförs kommer två rader att fyllas i med samma korrelations-ID, men med två olika autentiseringsmetoder (t. ex. formulär, SSO).
I händelse av Multi-Factor Authentication finns det tre rader med ett delat korrelations-ID och tre motsvarande autentiseringsmetoder (t. ex. formulär, AzureMFA, Multiband). I det här exemplet visar multifaktorn i det här fallet att SSO har ett MFA.

***Vilka fel kan jag se i rapporten?***
En fullständig lista över AD FS relaterade fel som är ifyllda i Sign-In rapport och beskrivningar finns [AD FS hjälp om fel koder för hjälp](https://adfshelp.microsoft.com/References/ConnectHealthErrorCodeReference)

***Jag ser "00000000-0000-0000-0000-000000000000" i avsnittet "användare" i en inloggning. Vad betyder det?***
Om inloggningen misslyckades och det försökde UPN-namnet inte matchar ett befintligt UPN, blir "User", "username" och "User ID"-fälten "00000000-0000-0000-0000-000000000000" och "inloggnings identifierare" kommer att fyllas i med det värde som användaren angav. I dessa fall finns inte användaren som försöker logga in.

***Hur kan jag korrelera mina lokala händelser till inloggnings rapporten för Azure AD?***
Azure AD Connect Health agenten för AD FS korrelerar händelse-ID: n från AD FS beroende av Server versionen. Händelserna är tillgängliga i säkerhets loggen för AD FS-servrarna. 

***Varför visas NotSet eller NotApplicable i program-ID/namn för vissa AD FS inloggningar?***
I rapporten AD FS Sign-In visas OAuth-ID: n i fältet program-ID för OAuth-inloggningar. I de WS-utfodras WS-Trust inloggnings scenarierna är program-ID: t NotSet eller NotApplicable, och resurs-ID: n och förlitande part identifierare visas i fältet resurs-ID.

***Finns det några fler kända problem med rapporten i för hands versionen?***
Rapporten har ett känt problem där fältet "autentiseringskrav" på fliken "grundläggande information" fylls i som ett enda faktor värde för AD FS inloggningar oavsett inloggning. Fliken autentiseringsinformation visar dessutom "primär eller sekundär" i fältet krav, där en korrigering pågår för att särskilja primära eller sekundära autentiseringstyper.


## <a name="related-links"></a>Relaterade länkar
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Installation av Azure AD Connect Health Agent](how-to-connect-health-agent-install.md)
* [Riskfylld IP-rapport](how-to-connect-health-adfs-risky-ip.md)





