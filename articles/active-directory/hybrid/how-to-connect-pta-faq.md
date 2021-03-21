---
title: 'Azure AD Connect: direkt autentisering – vanliga frågor och svar | Microsoft Docs'
description: Svar på vanliga frågor om Azure Active Directory direktautentisering
services: active-directory
keywords: Azure AD Connect direktautentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75f797c40a276323cea9983c5340d2d854160c83
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97368485"
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Azure Active Directory direktautentisering: vanliga frågor och svar

Den här artikeln behandlar vanliga frågor om Azure Active Directory (Azure AD) genom strömnings-autentisering. Fortsätt att söka efter uppdaterat innehåll.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Vilken av metoderna för att logga in på Azure AD, direktautentisering, Password-hash-synkronisering och Active Directory Federation Services (AD FS) (AD FS), ska jag välja?

Läs [den här guiden](./choose-ad-authn.md) för en jämförelse av de olika inloggnings metoderna för Azure AD och hur du väljer rätt inloggnings metod för din organisation.

## <a name="is-pass-through-authentication-a-free-feature"></a>Är direkt autentisering en kostnads fri funktion?

Direktautentisering är en kostnads fri funktion. Du behöver inte några betalda versioner av Azure AD för att använda den.

## <a name="does-conditional-access-work-with-pass-through-authentication"></a>Fungerar [villkorlig åtkomst](../conditional-access/overview.md) med direktautentisering?

Ja. Alla funktioner för villkorlig åtkomst, inklusive Azure AD Multi-Factor Authentication, fungerar med direktautentisering.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Stöder direkt autentisering "alternativ-ID" som användar namn, i stället för "userPrincipalName"?
Ja, inloggning med ett icke-UPN-värde, till exempel ett alternativt e-postmeddelande, stöds för både direktautentisering (PTA) och PHS (Password hash Sync). För ytterligare information om [alternativt inloggnings-ID](../authentication/howto-authentication-use-email-signin.md).

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>Fungerar hash-synkronisering av lösen ord som reserv för direktautentisering?

Nej. Direktautentisering växlar _inte_ automatiskt till hash-synkronisering av lösen ord. För att undvika användar inloggnings problem bör du konfigurera direktautentisering för [hög tillgänglighet](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="what-happens-when-i-switch-from-password-hash-synchronization-to-pass-through-authentication"></a>Vad händer när jag växlar från hash-synkronisering av lösen ord för att passera autentisering?

När du använder Azure AD Connect för att växla inloggnings metoden från hash-synkronisering av lösen ord för att sprida autentisering, blir direktautentisering den primära inloggnings metoden för dina användare i hanterade domäner. Observera att alla användares lösen ords-hashar som tidigare har synkroniserats med hash-synkronisering av lösen ord förblir lagrade i Azure AD.

## <a name="can-i-install-an-azure-ad-application-proxy-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Kan jag installera en [Azure AD-programproxy](../manage-apps/application-proxy.md) -anslutning på samma server som en genom strömnings agent för autentisering?

Ja. De versioner av direkt verifierings agenten, version 1.5.193.0 eller senare, som stöds av den här konfigurationen.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Vilka versioner av Azure AD Connect och vidarekoppling av agenter behöver du?

För att den här funktionen ska fungera måste du ha version 1.1.750.0 eller senare för Azure AD Connect och 1.5.193.0 eller senare för genom strömnings agenten. Installera all program vara på servrar med Windows Server 2012 R2 eller senare.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>Vad händer om min användares lösen ord har upphört att gälla och de försöker logga in med hjälp av direktautentisering?

Om du har konfigurerat [tillbakaskrivning av lösen ord](../authentication/concept-sspr-writeback.md) för en speciell användare, och om användaren loggar in med hjälp av direktautentisering, kan de ändra eller återställa sina lösen ord. Lösen orden skrivs tillbaka till den lokala Active Directory som förväntat.

Om du inte har konfigurerat tillbakaskrivning av lösen ord för en särskild användare eller om användaren inte har någon giltig Azure AD-licens tilldelad, kan användaren inte uppdatera sitt lösen ord i molnet. De kan inte uppdatera sina lösen ord, även om deras lösen ord har upphört att gälla. Användaren ser i stället det här meddelandet: "din organisation tillåter inte att du uppdaterar ditt lösen ord på den här webbplatsen. Uppdatera den enligt den metod som organisationen rekommenderar eller be administratören om hjälp. " Användaren eller administratören måste återställa sina lösen ord i lokala Active Directory.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Hur skyddar direkt autentiseringen mot brute-force-angrepp för lösen ord?

[Läs information om Smart utelåsning](../authentication/howto-password-smart-lockout.md).

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Vad kommunicerar genom strömnings agenter över portarna 80 och 443?

- Autentiserings agenter gör HTTPS-begäranden via port 443 för alla funktions åtgärder.
- Autentiserings agenter gör HTTP-förfrågningar via port 80 för att hämta listor över återkallade TLS/SSL-certifikat (CRL).

     >[!NOTE]
     >De senaste uppdateringarna reducerade antalet portar som krävs av funktionen. Om du har äldre versioner av Azure AD Connect eller Authentication-agenten ska dessa portar vara öppna även: 5671, 8080, 9090, 9091, 9350, 9352 och 10100-10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Kan direktautentisering genom strömning kommunicera via en utgående webbproxyserver?

Ja. Om Web Proxy Auto-Discovery (WPAD) är aktiverat i din lokala miljö försöker Authentication-agenter automatiskt att hitta och använda en webbproxyserver i nätverket.

Om du inte har WPAD i din miljö kan du lägga till proxyinformation (enligt nedan) så att en direktautentisering kan kommunicera med Azure AD:
- Konfigurera proxyinformation i Internet Explorer innan du installerar agenten för direkt autentisering på servern. Detta gör att du kan slutföra installationen av Autentiseringstjänsten, men den visas fortfarande som **inaktiv** på administrations portalen.
- På-servern navigerar du till "C:\Program\Microsoft Azure AD Connect Authentication agent".
- Redigera konfigurations filen "AzureADConnectAuthenticationAgentService" och Lägg till följande rader (Ersätt "http \: //contosoproxy.com:8080" med din faktiska proxyadress):

```
   <system.net>
      <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy
            usesystemdefault="true"
            proxyaddress="http://contosoproxy.com:8080"
            bypassonlocal="true"
         />
     </defaultProxy>
   </system.net>
```

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Kan jag installera två eller flera direktautentisering på samma server?

Nej, du kan bara installera en genom strömnings agent på en enskild server. Om du vill konfigurera direktautentisering för hög tillgänglighet [följer du instruktionerna här](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="do-i-have-to-manually-renew-certificates-used-by-pass-through-authentication-agents"></a>Måste jag manuellt Förnya certifikat som används av genom strömnings agenter?

Kommunikationen mellan varje direktautentisering och Azure AD skyddas med certifikatbaserad autentisering. Dessa [certifikat förnyas automatiskt med några månader av Azure AD](how-to-connect-pta-security-deep-dive.md#operational-security-of-the-authentication-agents). Du behöver inte förnya dessa certifikat manuellt. Du kan rensa äldre utgångna certifikat efter behov.

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>Hur gör jag för att ta bort en direktautentisering för direkt autentisering?

Så länge en direktautentisering körs förblir den aktiv och hanterar ständigt användar inloggnings begär Anden. Om du vill avinstallera en autentiseringsnyckel går du till **kontroll panelen-> program-> program och funktioner** och avinstallerar både **Microsoft Azure AD Connect Authentication agent** och **Microsoft Azure AD Connect agent Updater** -program.

Om du markerar bladet direkt autentisering i [Azure Active Directory administrations Center](https://aad.portal.azure.com) när du har slutfört föregående steg, ser du att Autentiseringstjänsten visas som **inaktiv**. Detta är _förväntat_. Autentiseringstjänsten tas automatiskt bort från listan efter 10 dagar.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Jag använder redan AD FS för att logga in på Azure AD. Hur gör jag för att växlar det till genom strömning?

Om du migrerar från AD FS (eller andra Federations tekniker) till direktautentisering, rekommenderar vi starkt att du följer vår detaljerade distributions guide publicerad [här](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true).

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>Kan jag använda direktautentisering i en Active Directory miljö med flera skogar?

Ja. Miljöer med flera skogar stöds om det finns skogs förtroenden (tvåvägs) mellan dina Active Directory skogar och om routning av namnsuffix har kon figurer ATS korrekt.

## <a name="does-pass-through-authentication-provide-load-balancing-across-multiple-authentication-agents"></a>Tillhandahåller direkt autentisering belastnings utjämning över flera autentiseringspaket?

Nej, om du installerar flera pass-through Authentication-agenter ser du bara [hög tillgänglighet](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability). Den ger ingen deterministisk belastnings utjämning mellan Authentication-agenterna. Alla autentiseringsprinciper (slumpmässigt) kan bearbeta en viss användar inloggnings förfrågan.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Hur många strömnings agenter måste jag installera?

Att installera flera direktautentisering för direktautentisering säkerställer [hög tillgänglighet](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability). Men den ger inte en deterministisk belastnings utjämning mellan Authentication-agenterna.

Överväg den högsta och genomsnittliga belastningen för de inloggnings begär Anden som du förväntar dig att se på din klient. Som riktmärke kan en enda autentiserings-agent hantera 300 till 400-autentiseringar per sekund på en standard processor på 4 kärnor, 16 GB RAM-Server.

Använd följande vägledning för att beräkna nätverks trafik:
- Varje begäran har en nytto Last storlek på (0,5 K + 1 KB * num_of_agents) byte; t. ex. data från Azure AD till Authentication agent. Här anger "num_of_agents" det antal autentiseringspaket som har registrerats för din klient.
- Varje svar har en nytto Last storlek på 1 kB byte; dvs. data från Authentication agent till Azure AD.

För de flesta kunder räcker två eller tre autentiseringsscheman för hög tillgänglighet och kapacitet. Du bör installera autentiseringsprinciper nära domän kontrol Lanterna för att förbättra inloggnings fördröjningen.

>[!NOTE]
>Det finns en system gräns på 40-autentiseringspaket per klient.

## <a name="why-do-i-need-a-cloud-only-global-administrator-account-to-enable-pass-through-authentication"></a>Varför behöver jag ett globalt administratörs konto med enbart moln för att möjliggöra direktautentisering?

Vi rekommenderar att du aktiverar eller inaktiverar direkt autentisering med hjälp av ett globalt administratörs konto för molnet. Lär dig mer om [att lägga till ett globalt administratörs konto för molnet](../fundamentals/add-users-azure-active-directory.md). På så sätt ser du till att du inte blir utelåst från din klient.

## <a name="how-can-i-disable-pass-through-authentication"></a>Hur gör jag för att inaktivera direkt autentisering?

Kör guiden Azure AD Connect igen och ändra användar inloggnings metoden från direktautentisering till en annan metod. Den här ändringen inaktiverar direktautentisering på klienten och avinstallerar Autentiseringstjänsten från servern. Du måste avinstallera autentiseringsinställningarna från de andra servrarna manuellt.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Vad händer när jag avinstallerar en genom strömnings agent?

Om du avinstallerar en direktautentisering från en server kan servern sluta att acceptera inloggnings förfrågningar. För att undvika att dela användar inloggnings funktionen på din klient organisation, se till att du har en annan autentiserings agent som körs innan du avinstallerar en direktautentisering för direkt autentisering.

## <a name="i-have-an-older-tenant-that-was-originally-setup-using-ad-fs--we-recently-migrated-to-pta-but-now-are-not-seeing-our-upn-changes-synchronizing-to-azure-ad--why-are-our-upn-changes-not-being-synchronized"></a>Jag har en äldre klient som ursprungligen konfigurerades med hjälp av AD FS.  Vi migrerade nyligen till PTA, men nu ser vi inte våra ändringar i UPN-ändringar till Azure AD.  Varför synkroniseras inte våra UPN-ändringar?

A: under följande omständigheter kanske dina lokala UPN-ändringar inte synkroniseras om:

- Din Azure AD-klient skapades före den 15 juni 2015
- Du förinstallerades ursprungligen med din Azure AD-klient med hjälp av AD FS för autentisering
- Du har växlat till att hantera användare med PTA som autentisering

Detta beror på att standard beteendet för klienter som skapats före den 15 juni 2015 var att blockera ändringar i UPN.  Om du behöver avinstallera UPN-ändringar måste du köra följande PowerShell-cmdlt:  

`Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $True`

Innehavare som skapats efter den 15 juni 2015 har standard beteende för synkronisering av UPN-ändringar.   



## <a name="next-steps"></a>Nästa steg
- [Aktuella begränsningar](how-to-connect-pta-current-limitations.md): Lär dig vilka scenarier som stöds och vilka som inte är det.
- [Snabb start](how-to-connect-pta-quick-start.md): kom igång med Azure AD-direktautentisering.
- [Migrera från AD FS till vidarekoppling](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) – en detaljerad guide för att migrera från AD FS (eller andra Federations tekniker) till vidarekoppling.
- [Smart utelåsning](../authentication/howto-password-smart-lockout.md): Lär dig hur du konfigurerar funktionen för smart utelåsning på klienten för att skydda användar konton.
- [Teknisk djupgående](how-to-connect-pta-how-it-works.md): förstå hur funktionen för direkt autentisering fungerar.
- [Felsöka](tshoot-connect-pass-through-authentication.md): Lär dig hur du löser vanliga problem med funktionen för direkt autentisering.
- [Djupgående säkerhets](how-to-connect-pta-security-deep-dive.md)information: få djupgående teknisk information om funktionen för direkt autentisering.
- [Azure AD sömlös SSO](how-to-connect-sso.md): Lär dig mer om den här kompletterande funktionen.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Använd Azure Active Directory-forumet för att fil nya funktions begär Anden.
