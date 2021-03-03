---
title: Primary Refresh token (PRT) och Azure AD – Azure Active Directory
description: Vad är rollen och hur hanterar vi den primära uppdateringstoken (PRT) i Azure Active Directory?
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46cc8ef1158c02190f905cbe8eb1d12ea7be50a2
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644943"
---
# <a name="what-is-a-primary-refresh-token"></a>Vad är en primär uppdateringstoken?

En Primary Refresh token (PRT) är en viktig artefakt i Azure AD-autentisering i Windows 10, Windows Server 2016 och senare versioner, iOS och Android-enheter. Det är en JSON Web Token (JWT) som utfärdats särskilt för token från Microsoft för att aktivera enkel inloggning (SSO) över de program som används på dessa enheter. I den här artikeln får du information om hur en PRT utfärdas, används och skyddas på Windows 10-enheter.

Den här artikeln förutsätter att du redan förstår de olika enhets tillstånd som är tillgängliga i Azure AD och hur enkel inloggning fungerar i Windows 10. Mer information om enheter i Azure AD finns i artikeln [Vad är enhets hantering i Azure Active Directory?](overview.md)

## <a name="key-terminology-and-components"></a>Nyckel terminologi och komponenter

Följande Windows-komponenter spelar en viktig roll när de begär och använder en PRT:

* CloudAP ( **Cloud Authentication provider** ): CloudAP är modern autentiseringsprovider för Windows-inloggning som verifierar användare som loggar in på en Windows 10-enhet. CloudAP tillhandahåller ett plugin-ramverk som identitets leverantörer kan bygga på för att aktivera autentisering för Windows som använder identitets leverantörens autentiseringsuppgifter.
* **Webb konto hanterare** (WAM): WAM är standard-token Broker på Windows 10-enheter. WAM tillhandahåller också ett plugin-ramverk som identitets leverantörer kan bygga på och aktivera SSO till sina program som förlitar sig på identitets leverantören.
* **Azure AD CloudAP-plugin**: ett Azure AD-särskilt plugin-program som bygger på CloudAP Framework, som verifierar användarautentiseringsuppgifter med Azure AD under Windows-inloggning.
* **Azure AD WAM-plugin**: ett Azure AD-speciellt plugin-program som bygger på WAM-ramverket, som möjliggör enkel inloggning till program som förlitar sig på Azure AD för autentisering.
* **Dsreg**: en särskild Azure AD-komponent i Windows 10 som hanterar enhets registrerings processen för alla enhets tillstånd.
* **Trusted Platform Module** (TPM): en TPM är en maskin varu komponent som är inbyggd i en enhet, som tillhandahåller maskinvarubaserade säkerhetsfunktioner för användar-och enhets hemligheter. Mer information finns i artikeln [Trusted Platform Module Technology-översikt](/windows/security/information-protection/tpm/trusted-platform-module-overview).

## <a name="what-does-the-prt-contain"></a>Vad innehåller PRT?

En PRT innehåller anspråk som vanligt vis finns i valfri Azure AD-uppdateringstoken. Dessutom finns det vissa enhetsspecifika anspråk som ingår i PRT. Det här är skillnaderna:

* **Enhets-ID**: en PRT utfärdas till en användare på en speciell enhet. Anspråket för enhets-ID `deviceID` avgör vilken enhet som PRT har utfärdats till användaren. Detta påstående utfärdas senare till token som erhållits via PRT. Enhets-ID-anspråket används för att fastställa auktorisering för villkorlig åtkomst baserat på enhetens tillstånd eller efterlevnad.
* **Sessionsnyckel**: sessionsnyckeln är en krypterad symmetrisk nyckel som genereras av Azure AD-Autentiseringstjänsten som utfärdats som en del av PRT. Sessionsnyckeln fungerar som bevis på innehavet när en PRT används för att hämta token för andra program.

### <a name="can-i-see-whats-in-a-prt"></a>Kan jag se vad som finns i en PRT?

En PRT är en ogenomskinlig blob som skickas från Azure AD, vars innehåll inte är känt för några klient komponenter. Det går inte att se vad som finns i en PRT.

## <a name="how-is-a-prt-issued"></a>Hur utfärdas en PRT?

Enhets registrering är en förutsättning för enhets baserad autentisering i Azure AD. En PRT utfärdas till användare endast på registrerade enheter. Mer detaljerad information om enhets registrering finns i artikeln [Windows Hello för företag och enhets registrering](/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration). Vid enhets registrering genererar dsreg-komponenten två uppsättningar av kryptografiska nyckel par:

* Enhets nyckel (dkpub/dkpriv)
* Transport nyckel (tkpub/tkpriv)

De privata nycklarna är kopplade till enhetens TPM om enheten har en giltig och fungerande TPM, medan offentliga nycklar skickas till Azure AD under registrerings processen för enheten. Dessa nycklar används för att validera enhetens tillstånd under PRT-begäranden.

PRT utfärdas vid användarautentisering på en Windows 10-enhet i två situationer:

* **Azure AD-ansluten** eller **hybrid Azure AD-ansluten**: en PRT utfärdas under Windows-inloggning när en användare loggar in med sina autentiseringsuppgifter för organisationen. En PRT utfärdas med alla Windows 10-autentiseringsuppgifter som stöds, till exempel lösen ord och Windows Hello för företag. I det här scenariot är Azure AD CloudAP-plugin-programmet den primära utfärdaren för PRT.
* **Registrerad Azure AD-enhet**: en PRT utfärdas när en användare lägger till ett sekundärt arbets konto till Windows 10-enheten. Användare kan lägga till ett konto i Windows 10 på två olika sätt –  
   * Lägg till ett konto med **hjälp av det här kontot överallt på enheten** när du har loggat in på en app (till exempel Outlook)
   * Lägga till ett konto från **Inställningar**  >  **konton**  >  **åtkomst till arbets-eller skol**  >  **anslutning**

I Azure AD-registrerade enhets scenarier är Azure AD WAM-plugin-programmet den primära utfärdaren för PRT eftersom Windows-inloggning inte sker med det här Azure AD-kontot.

> [!NOTE]
> identitets leverantörer från tredje part måste stödja WS-Trust-protokollet för att aktivera PRT-utfärdande på Windows 10-enheter. Utan WS-Trust kan PRT inte utfärdas till användare på Hybrid Azure AD-anslutna eller Azure AD-anslutna enheter. Endast usernamemixed-slutpunkter i ADFS måste anges. Både ADFS/tjänster/Trust/2005/windowstransport och ADFS/Services/Trust/13/windowstransport ska aktive ras som enbart intranät riktade slut punkter och **får inte visas** som extra näts slut punkter via Webbprogramproxy

## <a name="what-is-the-lifetime-of-a-prt"></a>Vilken är livs längden för en PRT?

När en PRT har utfärdats är den giltig i 14 dagar och förnyas kontinuerligt så länge användaren använder enheten aktivt.  

## <a name="how-is-a-prt-used"></a>Hur används en PRT?

En PRT används av två viktiga komponenter i Windows:

* **Azure AD CloudAP-plugin**: under Windows-inloggning begär Azure AD CloudAP-plugin-programmet en PRT från Azure AD med hjälp av de autentiseringsuppgifter som anges av användaren. Den cachelagrar också PRT för att aktivera cachelagrad inloggning när användaren inte har åtkomst till en Internet anslutning.
* **Azure AD WAM-plugin**: när användare försöker få åtkomst till program använder Azure AD WAM-plugin-programmet PRT för att aktivera SSO på Windows 10. Azure AD WAM-pluginprogrammet använder PRT för att begära uppdaterings-och åtkomsttoken för program som förlitar sig på WAM för token-begäranden. Det aktiverar även SSO i webbläsare genom att mata in PRT i webb läsar begär Anden. Enkel inloggning i webbläsare i Windows 10 stöds på Microsoft Edge (inbyggt) och Chrome (via [Windows 10-konton](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji?hl=en) eller [Office Online](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb?hl=en) -tillägg).

## <a name="how-is-a-prt-renewed"></a>Hur förnyas en PRT?

En PRT förnyas på två olika sätt:

* **Azure AD CloudAP-plugin var 4: e timme**: CloudAP-plugin-programmet förnyar PRT var fjärde timme under Windows-inloggning. Om användaren inte har någon Internet anslutning under den tiden kommer CloudAP-plugin-programmet att förnya PRT när enheten är ansluten till Internet.
* **Azure AD WAM-plugin vid begäran om app-token**: WAM-plugin-programmet aktiverar SSO på Windows 10-enheter genom att aktivera obevakade Tokenbegäran för program. WAM-plugin-programmet kan förnya PRT under dessa Tokenbegäran på två olika sätt:
   * En app begär WAM för en åtkomsttoken i bakgrunden men det finns ingen uppdaterad token för den appen. I det här fallet använder WAM PRT för att begära en token för appen och får tillbaka en ny PRT i svaret.
   * En app begär WAM för en åtkomsttoken, men PRT är ogiltig eller så kräver Azure AD ytterligare behörighet (till exempel Azure AD Multi-Factor Authentication). I det här scenariot initierar WAM en interaktiv inloggning som kräver att användaren autentiserar eller ger ytterligare verifiering och att en ny PRT utfärdas vid lyckad autentisering.

I en ADFS-miljö krävs inte direkt insikten för domänkontrollanten för att kunna förnya PRT. PRT-förnyelsen kräver bara/ADFS/Services/Trust/2005/usernamemixed-och/ADFS/Services/Trust/13/usernamemixed-slutpunkter som är aktiverade på proxy genom att använda WS-Trust protokoll.

Windows transport-slutpunkter krävs endast för lösenordsautentisering när ett lösen ord ändras, inte för PRT-förnyelse.

### <a name="key-considerations"></a>Viktiga överväganden

* En PRT utfärdas och förnyas endast under en intern app-autentisering. En PRT förnyas eller utfärdas inte under en webbläsarsession.
* I Azure AD-anslutna och hybrid Azure AD-anslutna enheter är CloudAP-plugin-programmet den primära utfärdaren för en PRT. Om en PRT förnyas under en WAM-baserad Tokenbegäran skickas PRT tillbaka till CloudAP-plugin-programmet, som verifierar giltigheten för PRT med Azure AD innan du godkänner den.

## <a name="how-is-the-prt-protected"></a>Hur skyddas PRT?

En PRT skyddas genom att binda den till enheten som användaren har loggat in på. Azure AD och Windows 10 aktiverar PRT-skydd genom följande metoder:

* **Vid första inloggningen**: under första inloggningen utfärdas en PRT av signerings begär Anden som använder enhets nyckeln kryptografiskt under enhets registreringen. På en enhet med en giltig och fungerande TPM skyddas enhets nyckeln av TPM: en vilket förhindrar skadlig åtkomst. En PRT utfärdas inte om motsvarande enhets nyckel signatur inte kan verifieras.
* **Vid Tokenbegäran och förnyelse**: när en PRT utfärdas utfärdar Azure AD även en krypterad sessionsnyckel till enheten. Den krypteras med den offentliga transport nyckeln (tkpub) som genereras och skickas till Azure AD som en del av enhets registreringen. Den här sessionsnyckeln kan bara dekrypteras av den privata transport nyckeln (tkpriv) som skyddas av TPM: en. Sessionsnyckeln är den nyckel som används för att skicka förfrågningar till Azure AD.  Sessionsnyckeln skyddas också av TPM: en och inga andra OS-komponenter kan komma åt den. Tokenbegäran eller begär Anden om PRT-förnyelse signeras säkert av den här sessionsnyckeln via TPM och kan därför inte manipuleras. Azure AD kommer att ogiltig förklara eventuella förfrågningar från enheten som inte har signerats av motsvarande sessionsnyckel.

Genom att skydda dessa nycklar med TPM förbättrar vi säkerheten för PRT från skadliga aktörer som försöker stjäla nycklar eller spela upp PRT.  Med hjälp av TPM kan du i stor utsträckning förbättra säkerheten för Azure AD-anslutna, hybrid Azure AD-anslutna och registrerade Azure AD-enheter mot stöld av autentiseringsuppgifter. För prestanda och tillförlitlighet är TPM 2,0 den rekommenderade versionen för alla Azure AD Device Registration-scenarier i Windows 10. Från och med Windows 10, 1903 Update använder Azure AD inte TPM 1,2 för någon av ovanstående nycklar på grund av Tillförlitlighets problem. 

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>Hur skyddas app-token och webbläsarens cookies?

**App-token**: när en app begär token via WAM, utfärdar Azure AD en uppdateringstoken och en åtkomsttoken. WAM returnerar dock bara åtkomsttoken till appen och skyddar uppdateringstoken i dess cacheminne genom att kryptera den med användarens Data Protection application programming interface-nyckel (DPAPI). I WAM används Refresh-token på ett säkert sätt genom att signera förfrågningar med sessionsnyckeln för att utfärda ytterligare åtkomsttoken. DPAPI-nyckeln skyddas av en Azure AD-baserad symmetrisk nyckel i själva Azure AD. När enheten behöver dekryptera användar profilen med DPAPI-nyckeln tillhandahåller Azure AD DPAPI-nyckeln som krypterats av sessionsnyckeln, vilket CloudAP-plugin-programmet begär TPM för dekryptering. Den här funktionen säkerställer konsekvens i att säkra uppdateringstoken och förhindra att program implementerar sina egna skydds mekanismer.  

**Cookies i webbläsaren**: i Windows 10 stöder Azure AD webbläsare SSO i Internet Explorer och Microsoft Edge internt eller i Google Chrome via tillägget Windows 10-konton. Säkerheten skapas inte bara för att skydda cookies, utan även de slut punkter som cookies skickas till. Cookies i webbläsaren skyddas på samma sätt som en PRT, genom att använda sessionsnyckeln för att signera och skydda cookies.

När en användare startar en webb läsar interaktion anropar webbläsaren (eller tillägget) en COM-intern klient värd. Den interna klient värden säkerställer att sidan kommer från en av de tillåtna domänerna. Webbläsaren kan skicka andra parametrar till den interna klient värden, inklusive en nonce, men den interna klient värden garanterar att värd namnet verifieras. Den interna klient värden begär en PRT-cookie från CloudAP-plugin-programmet, som skapar och signerar den med den TPM-skyddade sessionsnyckeln. Eftersom PRT-cookien signeras av sessionsnyckeln är det mycket svårt att manipulera. Denna PRT-cookie ingår i begär ande huvudet för Azure AD för att verifiera den enhet som den härstammar från. Om du använder Chrome-webbläsaren, kan endast tillägget som uttryckligen definierats i den interna klient värdens manifest anropa det för att förhindra godtyckliga tillägg från att göra dessa förfrågningar. När Azure AD validerar PRT cookie, utfärdar den en sessions-cookie till webbläsaren. Cookien för den här sessionen innehåller också samma sessionsnyckel som utfärdats med en PRT. Under efterföljande förfrågningar verifieras sessionsnyckeln effektivt genom att binda cookien till enheten och förhindra uppspelning från andra platser.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>När får en PRT ett MFA-anspråk?

En PRT kan hämta ett MFA-anspråk (Multi-Factor Authentication) i vissa scenarier. När en MFA-baserad PRT används för att begära token för program överförs MFA-anspråk till dessa app-token. Den här funktionen ger användarna en sömlös upplevelse genom att förhindra MFA-utmaning för varje app som kräver det. En PRT kan hämta ett MFA-anspråk på följande sätt:

* **Logga in med Windows Hello för företag**: Windows Hello för företag ersätter lösen ord och använder kryptografiska nycklar för att tillhandahålla stark tvåfaktorautentisering. Windows Hello för företag är specifika för en användare på en enhet och kräver själv MFA för att etablera. När en användare loggar in med Windows Hello för företag får användarens PRT ett MFA-anspråk. Det här scenariot gäller även för användare som loggar in med smartkort om autentisering av smartkort skapar ett MFA-anspråk från ADFS.
   * Eftersom Windows Hello för företag betraktas som Multi-Factor Authentication, uppdateras MFA-anspråket när PRT uppdateras, så MFA-varaktigheten kommer kontinuerligt att öka när användarna loggar in med WIndows Hello för företag
* **MFA under WAM-interaktiv inloggning**: under en TOKENBEGÄRAN via WAM, om en användare krävs för att kunna använda MFA för att få åtkomst till appen, skrivs PRT som förnyas under interaktionen med MFA-anspråk.
   * I det här fallet uppdateras MFA-anspråk inte kontinuerligt, så MFA-varaktigheten baseras på den livs längd som anges i katalogen.
   * När en tidigare befintlig PRT och RT används för åtkomst till en app betraktas PRT och RT som det första beviset för autentisering. En ny i krävs med ett andra bevis och ett inskrivet MFA-anspråk. Detta kommer också att utfärda en ny PRT och RT.
* **MFA vid enhets registrering**: om en administratör har konfigurerat sina enhets inställningar i Azure AD för att [kräva MFA för att registrera enheter](device-management-azure-portal.md#configure-device-settings)måste användaren göra MFA för att slutföra registreringen. Under den här processen har PRT som utfärdas till användaren det MFA-anspråk som hämtats under registreringen. Den här funktionen gäller endast för den användare som gjorde kopplings åtgärden, inte andra användare som loggar in på enheten.
   * I likhet med den interaktiva inloggningen i WAM uppdateras inte MFA-anspråket kontinuerligt, så MFA-varaktigheten baseras på den livs längd som anges i katalogen.

Windows 10 upprätthåller en partitionerad lista över PRTs för varje autentiseringsuppgift. Det finns därför en PRT för var och en av Windows Hello för företag, lösen ord eller smartkort. Med den här partitionering ser du till att MFA-anspråk isoleras baserat på de autentiseringsuppgifter som används och inte blandas under token-begäranden.

## <a name="how-is-a-prt-invalidated"></a>Hur är en PRT ogiltig?

En PRT är ogiltig i följande scenarier:

* **Ogiltig användare**: om en användare tas bort eller inaktive ras i Azure AD är deras PRT ogiltig och kan inte användas för att hämta token för program. Om en borttagen eller inaktive rad användare redan har loggat in på en enhet innan, loggas den cachelagrade inloggningen i, tills CloudAP känner till sitt ogiltiga tillstånd. När CloudAP bestämmer att användaren är ogiltig blockeras efterföljande inloggningar. En ogiltig användare blockeras automatiskt från att logga in på nya enheter som inte har sina autentiseringsuppgifter cachelagrade.
* **Ogiltig enhet**: om en enhet tas bort eller inaktive ras i Azure AD är PRT som hämtats på enheten ogiltig och kan inte användas för att hämta token för andra program. Om en användare redan har loggat in på en ogiltig enhet kan de fortsätta att göra det. Men alla token på enheten är ogiltiga och användaren har inte SSO till några resurser från enheten.
* **Lösen ords ändring**: när en användare har ändrat sitt lösen ord, kommer PRT som hämtats med det tidigare lösen ordet att bli ogiltig i Azure AD. Lösen ords ändring resulterar i att användaren får en ny PRT. Denna ogiltighet kan ske på två olika sätt:
   * Om användaren loggar in i Windows med sitt nya lösen ord, tar CloudAP bort den gamla PRT och begär att Azure AD skickar en ny PRT med sitt nya lösen ord. Om användaren inte har någon Internet anslutning kan det nya lösen ordet inte verifieras, Windows kan kräva att användaren anger sitt gamla lösen ord.
   * Om en användare har loggat in med sitt gamla lösen ord eller ändrat sitt lösen ord efter att ha loggat in i Windows, används den gamla PRT för alla WAM-baserade Tokenbegäran. I det här scenariot uppmanas användaren att autentisera vid begäran om WAM-token och en ny PRT utfärdas.
* **TPM-problem**: ibland kan en enhets TPM Falter eller krascha, vilket leder till att nycklar som skyddas av TPM: en inte är åtkomliga. I det här fallet kan enheten inte hämta en PRT eller begära token med hjälp av en befintlig PRT eftersom det inte kan bevisa att de kryptografiska nycklarna är besittning. Det innebär att alla befintliga PRT ogiltig förklaras av Azure AD. När ett problem upptäcks i Windows 10 initierar det ett återställnings flöde för att omregistrera enheten med nya kryptografiska nycklar. Med hybrid Azure AD Join, precis som den första registreringen, sker återställningen tyst utan användarindata. För Azure AD-anslutna eller Azure AD-registrerade enheter måste återställningen utföras av en användare som har administratörs behörighet på enheten. I det här scenariot initieras återställnings flödet av en Windows-prompt som guidar användaren att återställa enheten.

## <a name="detailed-flows"></a>Detaljerade flöden

Följande diagram illustrerar underliggande information när du utfärdar, förnyar och använder en PRT för att begära en åtkomsttoken för ett program. Dessutom beskriver de här stegen hur de tidigare säkerhetsmekanismerna tillämpas under dessa interaktioner.

### <a name="prt-issuance-during-first-sign-in"></a>PRT utfärdande vid första inloggningen

![PRT utfärdande vid första inloggningen i detaljerat flöde](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> I Azure AD-anslutna enheter sker detta utbyte synkront för att utfärda en PRT innan användaren kan logga in på Windows. I hybrid Azure AD-anslutna enheter är lokala Active Directory den primära utfärdaren. Det innebär att användaren bara väntar tills de kan skaffa en TGT för inloggning, medan PRT-utfärdande sker asynkront. Det här scenariot gäller inte för registrerade Azure AD-enheter eftersom inloggningen inte använder Azure AD-autentiseringsuppgifter.

| Steg | Beskrivning |
| :---: | --- |
| A | Användaren anger sitt lösen ord i inloggnings-användar gränssnittet. LogonUI skickar autentiseringsuppgifterna i en auth-buffert till LSA, som i sin tur skickar den internt till CloudAP. CloudAP vidarebefordrar denna begäran till CloudAP-plugin-programmet. |
| B | CloudAP-plugin-programmet initierar en sfär identifierings förfrågan för att identifiera identitets leverantören för användaren. Om användarens klient organisation har en konfiguration av en Federations leverantör returnerar Azure AD Federations leverantörens metadata Exchange Endpoint (MEX) slut punkt. Annars returnerar Azure AD att användaren är hanterad och anger att användaren kan autentiseras med Azure AD. |
| C | Om användaren hanteras kommer CloudAP att hämta nonce från Azure AD. Om användaren är federerad begär CloudAP-plugin-programmet en SAML-token från Federations leverantören med användarens autentiseringsuppgifter. När den tar emot, begär SAML-token ett nonce-ID från Azure AD. |
| D | CloudAP-plugin-programmet konstruerar autentiseringsbegäran med användarens autentiseringsuppgifter, nonce och en Service Broker-omfattning, signerar begäran med enhets nyckeln (dkpriv) och skickar den till Azure AD. I en federerad miljö använder CloudAP-pluginprogrammet SAML-token som returnerades av Federations leverantören i stället för användarens autentiseringsuppgifter. |
| E | Azure AD validerar användarautentiseringsuppgifter, nonce och disksignatur, verifierar att enheten är giltig i klienten och utfärdar den krypterade PRT. Tillsammans med PRT utfärdar Azure AD även en symmetrisk nyckel som kallas sessionsnyckeln som krypteras av Azure AD med hjälp av transport nyckeln (tkpub). Dessutom är sessionsnyckeln också inbäddad i PRT. Den här sessionsnyckeln fungerar som en nyckel för nyckels innehav (PoP) för efterföljande begär Anden med PRT. |
| F | CloudAP-plugin-programmet skickar den krypterade PRT och sessionsnyckeln till CloudAP. CloudAP begär TPM: en att dekryptera sessionsnyckeln med transport nyckeln (tkpriv) och kryptera om den med hjälp av TPM: s egna nyckel. CloudAP lagrar den krypterade sessionsnyckeln i cacheminnet tillsammans med PRT. |

### <a name="prt-renewal-in-subsequent-logons"></a>PRT förnyelse vid efterföljande inloggningar

![PRT förnyelse vid efterföljande inloggningar](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| Steg | Beskrivning |
| :---: | --- |
| A | Användaren anger sitt lösen ord i inloggnings-användar gränssnittet. LogonUI skickar autentiseringsuppgifterna i en auth-buffert till LSA, som i sin tur skickar den internt till CloudAP. CloudAP vidarebefordrar denna begäran till CloudAP-plugin-programmet. |
| B | Om användaren tidigare har loggat in till användaren initierar Windows cachelagrat inloggning och validerar autentiseringsuppgifter för att logga in användaren i. Var fjärde timme initierar CloudAP-plugin-programmet PRT-förnyelse asynkront. |
| C | CloudAP-plugin-programmet initierar en sfär identifierings förfrågan för att identifiera identitets leverantören för användaren. Om användarens klient organisation har en konfiguration av en Federations leverantör returnerar Azure AD Federations leverantörens metadata Exchange Endpoint (MEX) slut punkt. Annars returnerar Azure AD att användaren är hanterad och anger att användaren kan autentiseras med Azure AD. |
| D | Om användaren är federerad begär CloudAP-plugin-programmet en SAML-token från Federations leverantören med användarens autentiseringsuppgifter. När den tar emot, begär SAML-token ett nonce-ID från Azure AD. Om användaren är hanterad kommer CloudAP direkt att hämta nonce från Azure AD. |
| E | CloudAP-plugin-programmet konstruerar autentiseringsbegäran med användarens autentiseringsuppgifter, nonce och den befintliga PRT, signerar begäran med sessionsnyckeln och skickar den till Azure AD. I en federerad miljö använder CloudAP-pluginprogrammet SAML-token som returnerades av Federations leverantören i stället för användarens autentiseringsuppgifter. |
| F | Azure AD validerar signaturens sessionsnyckel genom att jämföra den mot sessionsnyckeln som är inbäddad i PRT, validerar nonce och verifierar att enheten är giltig i klienten och utfärdar en ny PRT. Som tidigare setts tidigare åtföljs PRT igen med sessionsnyckeln som krypteras av transport nyckeln (tkpub). |
| G | CloudAP-plugin-programmet skickar den krypterade PRT och sessionsnyckeln till CloudAP. CloudAP begär TPM: en att dekryptera sessionsnyckeln med transport nyckeln (tkpriv) och kryptera den igen med hjälp av TPM: s egna nyckel. CloudAP lagrar den krypterade sessionsnyckeln i cacheminnet tillsammans med PRT. |

> [!NOTE]
> En PRT kan förnyas externt utan att en VPN-anslutning behövs när usernamemixed-slutpunkter aktive ras externt.

### <a name="prt-usage-during-app-token-requests"></a>PRT-användning vid begäran om app-token

![PRT-användning vid begäran om app-token](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| Steg | Beskrivning |
| :---: | --- |
| A | Ett program (till exempel Outlook, OneNote osv.) initierar en Tokenbegäran till WAM. WAM, i sin tur, ber Azure AD WAM-plugin-programmet att betjäna token-begäran. |
| B | Om det redan finns en uppdateringstoken för programmet använder Azure AD WAM-plugin-programmet för att begära en åtkomsttoken. För att tillhandahålla bevis på enhets bindning signerar WAM-plugin-programmet begäran med sessionsnyckeln. Azure AD validerar sessionsnyckeln och utfärdar en åtkomsttoken och en ny uppdateringstoken för appen, krypterad av sessionsnyckeln. I WAM-plugin-programmet begärs molnets AP-plugin för att dekryptera tokens, vilket i sin tur begär att TPM: en ska dekryptera med sessionsnyckeln, vilket resulterar i att WAM-plugin-programmet får både tokens. Sedan tillhandahåller WAM-plugin bara åtkomsttoken till programmet, medan den krypterar om uppdateringstoken med DPAPI och lagrar den i sin egen cache  |
| C |  Om en uppdateringstoken för programmet inte är tillgänglig använder Azure AD WAM-plugin-programmet PRT för att begära en åtkomsttoken. För att tillhandahålla bevis på innehav signerar WAM-plugin-programmet begäran som innehåller PRT med sessionsnyckeln. Azure AD validerar signaturens sessionsnyckel genom att jämföra den mot sessionsnyckeln som är inbäddad i PRT, verifierar att enheten är giltig och utfärdar en åtkomsttoken och en uppdateringstoken för programmet. Dessutom kan Azure AD utfärda en ny PRT (baserat på uppdaterings cykel) som alla krypteras av sessionsnyckeln. |
| D | I WAM-plugin-programmet begärs molnets AP-plugin för att dekryptera tokens, vilket i sin tur begär att TPM: en ska dekryptera med sessionsnyckeln, vilket resulterar i att WAM-plugin-programmet får både tokens. Sedan tillhandahåller WAM-plugin bara åtkomsttoken till programmet, medan den krypterar om uppdateringstoken med DPAPI och lagrar den i sin egen cache. I WAM-plugin-programmet används uppdateringstoken som ska vidarebefordras för det här programmet. I WAM-plugin-programmet får du också tillbaka den nya PRT till Cloud AP-plugin, som validerar PRT med Azure AD innan den uppdateras i sin egen cache. Cloud AP-plugin kommer att använda den nya PRT som skickas framåt. |
| E | WAM tillhandahåller den nyligen utfärdade åtkomsttoken till WAM, som i sin tur ger tillbaka till det anropande programmet|

### <a name="browser-sso-using-prt"></a>Enkel inloggning med webbläsare med PRT

![Enkel inloggning med webbläsare med PRT](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| Steg | Beskrivning |
| :---: | --- |
| A | Användaren loggar in i Windows med sina autentiseringsuppgifter för att få en PRT. När användaren öppnar webbläsaren läser webb adresserna från registret. |
| B | När en användare öppnar en inloggnings-URL för Azure AD, verifierar webbläsaren eller tillägget URL: en med de som har hämtats från registret. Om de matchar, anropar webbläsaren den interna klient värden för att hämta en token. |
| C | Den interna klient värden verifierar att URL: erna tillhör Microsoft Identity providers (Microsoft-konto eller Azure AD), extraherar en nonce som skickas från URL: en och anropar CloudAP-plugin-programmet för att hämta en PRT cookie. |
| D | CloudAP-plugin-programmet skapar PRT cookie, loggar in med den TPM-kopplade sessionsnyckeln och skickar tillbaka den till den interna klient värden. |
| E | Den interna klient värden returnerar denna PRT-cookie till webbläsaren, som kommer att inkludera den som en del av begär ande huvudet som kallas x-MS-RefreshTokenCredential och begär token från Azure AD. |
| F | Azure AD validerar signaturen i PRT cookie, validerar nonce, verifierar att enheten är giltig i klienten och utfärdar en ID-token för webb sidan och en krypterad sessions-cookie för webbläsaren. |

> [!NOTE]
> Det SSO-flöde för webbläsare som beskrivs i stegen ovan gäller inte för sessioner i privata lägen som InPrivate i Microsoft Edge eller Incognito i Google Chrome (när du använder tillägget Microsoft-konton).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du felsöker PRT-relaterade problem finns i artikeln [Felsöka hybrid Azure Active Directory anslutna Windows 10-och Windows Server 2016-enheter](troubleshoot-hybrid-join-windows-current.md).
