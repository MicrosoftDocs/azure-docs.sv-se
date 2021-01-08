---
title: Azure Active Directory inloggning med lösen ord (för hands version)
description: Läs om alternativ för lösen ords lös inloggning för att Azure Active Directory med hjälp av FIDO2-säkerhetsnycklar eller Microsoft Authenticator-appen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1b82dbe169e01b68e7d4b8a4c243cb72d3a3e8b
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013013"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Lösen ords verifierings alternativ för Azure Active Directory

Funktioner som Multi-Factor Authentication (MFA) är ett bra sätt att skydda din organisation, men användare kan ofta bli frustrerade med det extra säkerhets skiktet som behöver komma ihåg sina lösen ord. Metoder för snabbare autentisering är mer praktiska eftersom lösen ordet tas bort och ersätts med något som du har, plus något du känner till.

| Autentisering  | Något du har | Något som du är eller vet |
| --- | --- | --- |
| Lösenordsfri | Windows 10-enhet, telefon eller säkerhets nyckel | Bio metrisk eller PIN-kod |

Varje organisation har olika behov när den kommer till autentiseringen. Microsoft erbjuder följande tre lösen ords lösningar som integreras med Azure Active Directory (Azure AD):

- Windows Hello för företag
- Microsoft Authenticator-appen
- FIDO2 säkerhets nycklar

![Autentisering: säkerhet jämfört med bekvämlighet](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello för företag

Windows Hello för företag är idealiskt för informations anställda som har sin egen angivna Windows-dator. Autentiseringsuppgifterna för bio-och PIN-kod är direkt knutna till användarens dator, vilket förhindrar åtkomst från någon annan än ägaren. Med PKI-integrering (Public Key Infrastructure) och inbyggt stöd för enkel inloggning (SSO), är Windows Hello för företag en smidig metod för sömlös åtkomst till företagets resurser lokalt och i molnet.

![Exempel på en användar inloggning med Windows Hello för företag](./media/concept-authentication-passwordless/windows-hellow-sign-in.jpeg)

Följande steg visar hur inloggnings processen fungerar med Azure AD:

![Diagram som beskriver de steg som ingår i användar inloggning med Windows Hello för företag](./media/concept-authentication-passwordless/windows-hello-flow.png)

1. En användare loggar in i Windows med bio metrisk eller PIN-kod. Gesten låser upp den privata nyckeln för Windows Hello för företag och skickas till Cloud authentication-security support provider som kallas för *moln-AP-providern*.
1. Cloud AP-providern begär ett temporärt ID (ett slumpmässigt godtyckligt tal som bara kan användas en gång) från Azure AD.
1. Azure AD returnerar en nonce som är giltig i 5 minuter.
1. Molnets AP-Provider signerar nonce med hjälp av användarens privata nyckel och returnerar signerad nonce till Azure AD.
1. Azure AD verifierar signerad nonce med hjälp av användarens säkert registrerade offentliga nyckel mot signaturen för nonce. När du har validerat signaturen verifierar Azure AD den returnerade signerade nonce. När nonce verifieras skapar Azure AD en Primary Refresh token (PRT) med en sessionsnyckel som är krypterad till enhetens transport nyckel och tillbaka den till molnets AP-Provider.
1. Cloud AP-providern tar emot den krypterade PRT med sessionsnyckeln. Med hjälp av enhetens privata transport nyckel dekrypterar Cloud AP-providern sessionsnyckeln och skyddar sessionsnyckeln med hjälp av enhetens Trusted Platform Module (TPM).
1. Molnets AP-Provider returnerar ett lyckat verifierings svar för Windows. Användaren kan sedan komma åt Windows och molnet och lokala program utan att behöva autentisera igen (SSO).

[Planerings guiden](/windows/security/identity-protection/hello-for-business/hello-planning-guide) för Windows Hello för företag kan användas för att fatta beslut om en typ av Windows Hello för företag-distribution och vilka alternativ du behöver tänka på.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator app

Du kan också låta din personals telefon bli en metod för lösen ords lös autentisering. Du kanske redan använder Microsoft Authenticator-appen som ett bekvämt alternativ för Multi-Factor Authentication förutom ett lösen ord. Du kan också använda Authenticator-appen som ett lösen ords alternativ.

![Logga in på Microsoft Edge med Microsoft Authenticator-appen](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

Authenticator-appen förvandlar en iOS-eller Android-telefon till en stark, lösen ords rik autentiseringsuppgift. Användare kan logga in på vilken plattform eller webbläsare som helst genom att få ett meddelande till sin telefon, matcha ett nummer som visas på skärmen till det som visas på telefonen och sedan använda sina bio mått (touch eller FACET) eller PIN-kod för att bekräfta. Se [Hämta och installera Microsoft Authenticator-appen](../user-help/user-help-auth-app-download-install.md) för installations information.

Lösen ords lös inloggning med Microsoft Authenticator-appen till Azure AD är för närvarande en för hands version. Användning av Microsoft Authenticator-appen för sekundär autentisering för Azure AD Multi-Factor Authentication, självbetjäning för återställning av lösen ord (SSPR) eller OATH-programvaru-token är GA. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.

Lösenordsautentisering som använder Authenticator-appen följer samma grundläggande mönster som Windows Hello för företag. Det är lite mer komplicerat eftersom användaren måste identifieras så att Azure AD kan hitta den Microsoft Authenticator app-version som används:

![Diagram som beskriver de steg som ingår i användar inloggningen med Microsoft Authenticator-appen](./media/concept-authentication-passwordless/authenticator-app-flow.png)

1. Användaren anger sitt användar namn.
1. Azure AD identifierar att användaren har starka autentiseringsuppgifter och startar det starka flödet av autentiseringsuppgifter.
1. Ett meddelande skickas till appen via Apple Push Notification Service (APN) på iOS-enheter eller via Firebase Cloud Messaging (FCM) på Android-enheter.
1. Användaren får push-meddelandet och öppnar appen.
1. Appen anropar Azure AD och tar emot en utmanings-och nonce-begäran.
1. Användaren slutför utmaningen genom att ange sitt bio mått eller PIN-kod för att låsa upp den privata nyckeln.
1. Nonce signeras med den privata nyckeln och skickas tillbaka till Azure AD.
1. Azure AD utför verifiering av offentliga/privata nycklar och returnerar en token.

För att komma igång med lösen ords lös inloggning, slutför du följande anvisningar:

> [!div class="nextstepaction"]
> [Aktivera lösen ords utan lösen ord med Authenticator-appen](howto-authentication-passwordless-phone.md)

## <a name="fido2-security-keys"></a>FIDO2 säkerhets nycklar

Alliansen FIDO (snabb identitet Online) hjälper till att främja Open Authentication-standarder och minska användningen av lösen ord som en form av autentisering. FIDO2 är den senaste standarden som införlivar webbautentiseringen (webauthn)-standarden.

FIDO2-säkerhetsnycklar är en unphishable standard-baserad autentiseringsmetod för lösen ords skydd som kan komma i vilken form som helst. Snabb identitet Online (FIDO) är en öppen standard för lösen ords mässig autentisering. Med FIDO kan användare och organisationer utnyttja standard för att logga in på sina resurser utan användar namn eller lösen ord med hjälp av en extern säkerhets nyckel eller en plattforms nyckel som är inbyggd i en enhet.

Användare kan registrera sig och sedan välja en säkerhets nyckel för FIDO2 i inloggnings gränssnittet som deras huvudsakliga autentiserings sätt. Dessa säkerhets nycklar för FIDO2 är vanligt vis USB-enheter, men kan också använda Bluetooth eller NFC. Med en maskin varu enhet som hanterar autentiseringen ökar säkerheten för ett konto eftersom det inte finns något lösen ord som kan exponeras eller gissas.

FIDO2-säkerhetsnycklar kan användas för att logga in på sina Azure AD-eller hybrid Azure AD-anslutna Windows 10-enheter och få enkel inloggning till deras moln resurser och lokala resurser. Användare kan också logga in i webbläsare som stöds. FIDO2-säkerhetsnycklar är ett bra alternativ för företag som är mycket känsliga eller har scenarier eller anställda som inte är villiga eller kan använda sin telefon som en andra faktor.

Logga in med FIDO2 säkerhets nycklar till Azure AD finns för närvarande i för hands version. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.

![Logga in på Microsoft Edge med en säkerhets nyckel](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

Följande process används när en användare loggar in med en säkerhets nyckel för FIDO2:

![Diagram som beskriver stegen som ingår i användar inloggningen med en säkerhets nyckel för FIDO2](./media/concept-authentication-passwordless/fido2-security-key-flow.png)

1. Användaren ansluter säkerhets nyckeln FIDO2 till sin dator.
2. Windows identifierar säkerhets nyckeln FIDO2.
3. Windows skickar en autentiseringsbegäran.
4. Azure AD skickar tillbaka en nonce.
5. Användaren har slutfört sin gest för att låsa upp den privata nyckel som lagras i säkerhets nyckelns säkra enklaven-FIDO2.
6. Säkerhets nyckeln FIDO2 signerar nonce med den privata nyckeln.
7. PRT-token (Primary Refresh token) med signerat nonce skickas till Azure AD.
8. Azure AD verifierar signerad nonce med hjälp av den offentliga nyckeln FIDO2.
9. Azure AD returnerar PRT för att ge åtkomst till lokala resurser.

Även om det finns många nycklar som är FIDO2-certifierade av FIDO-alliansen, kräver Microsoft vissa valfria tillägg för FIDO2-specifikationen (Client-to-Authenticator Protocol) som ska implementeras av leverantören för att säkerställa maximal säkerhet och bästa möjliga upplevelse.

En säkerhets nyckel **måste** implementera följande funktioner och tillägg från FIDO2 CTAP-protokollet som ska vara Microsoft-kompatibelt:

| # | Funktion/tillägg-förtroende | Varför krävs den här funktionen eller tillägget? |
| --- | --- | --- |
| 1 | Resident nyckel | Den här funktionen gör att säkerhets nyckeln kan vara portabel, där autentiseringsuppgifterna lagras på säkerhets nyckeln. |
| 2 | Klientens PIN-kod | Med den här funktionen kan du skydda dina autentiseringsuppgifter med en andra faktor och gälla säkerhets nycklar som inte har något användar gränssnitt. |
| 3 | HMAC-hemlighet | Det här tillägget garanterar att du kan logga in på enheten när den är offline eller i flyg Plans läge. |
| 4 | Flera konton per RP | Den här funktionen garanterar att du kan använda samma säkerhets nyckel för flera tjänster som Microsoft-konto och Azure Active Directory. |

### <a name="fido2-security-key-providers"></a>FIDO2 säkerhets nyckel leverantörer

Följande leverantörer ger FIDO2 säkerhets nycklar för olika form faktorer som är kända för att vara kompatibla med den lösen ords fri upplevelsen. Vi rekommenderar att du utvärderar säkerhets egenskaperna för dessa nycklar genom att kontakta leverantören och FIDO Alliance.

| Leverantör | Kontakt |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://ftsafe.us/pages/microsoft](https://ftsafe.us/pages/microsoft) |
| Dold | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| TrustKey-lösningar | [https://www.trustkeysolutions.com/security-keys/](https://www.trustkeysolutions.com/security-keys/) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| Gemalto (Thales-grupp) | [https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/](https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/) |
| OneSpan Inc. | [https://www.onespan.com/products/fido](https://www.onespan.com/products/fido) |
| IDmelon Technologies Inc. | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) |
| Hypersecu | [https://www.hypersecu.com/hyperfido](https://www.hypersecu.com/hyperfido) |
| VinCSS | [https://passwordless.vincss.net](https://passwordless.vincss.net) |
| KONA I | [https://konai.com/business/security/fido](https://konai.com/business/security/fido) |
| Excelsecu | [https://www.excelsecu.com/productdetail/esecufido2secu.html](https://www.excelsecu.com/productdetail/esecufido2secu.html) |
| Token2 Schweiz | [https://www.token2.swiss/shop/product/token2-t2f2-alu-fido2-u2f-and-totp-security-key](https://www.token2.swiss/shop/product/token2-t2f2-alu-fido2-u2f-and-totp-security-key) |

> [!NOTE]
> Om du köper och planerar att använda NFC-baserade säkerhets nycklar behöver du en NFC-läsare som stöds för säkerhets nyckeln. NFC-läsaren är inte ett Azure-krav eller en begränsning. Kontakta leverantören av en NFC-baserad säkerhets nyckel för en lista över NFC-läsare som stöds.

Kontakta om du är en leverantör och vill få din enhet på den här listan över enheter som stöds [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com) .

Kom igång med FIDO2-säkerhetsnycklar genom att utföra följande instruktioner:

> [!div class="nextstepaction"]
> [Aktivera lösen ords skydd med FIDO2-säkerhetsnycklar](howto-authentication-passwordless-security-key.md)

## <a name="what-scenarios-work-with-the-preview"></a>Vilka scenarier fungerar med för hands versionen?

Inloggnings funktionerna i Azure AD är för närvarande en för hands version. Följande gäller:

- Administratörer kan aktivera metoder för lösen ords kryptering för sina klienter
- Administratörer kan rikta alla användare eller välja användare/grupper inom sin klient organisation för varje metod
- Slutanvändare kan registrera och hantera dessa metoder för lösen ords kryptering i sin konto Portal
- Slutanvändare kan logga in med dessa metoder för lösen ords kryptering
   - Microsoft Authenticator app: fungerar i scenarier där Azure AD-autentisering används, inklusive i alla webbläsare, under installationen av OOBE-installationen (Windows 10 out of Box) och med integrerade mobilappar på alla operativ system.
   - Säkerhets nycklar: arbeta på Lås skärmen för Windows 10 och webben i webbläsare som stöds, t. ex. Microsoft Edge (både äldre och nya kanter).

## <a name="choose-a-passwordless-method"></a>Välj en metod för lösen ords avtryck

Valet mellan dessa tre lösen ords alternativ beror på företagets krav på säkerhet, plattform och appar.

Här följer några faktorer som du bör tänka på när du väljer Microsoft-lösen ords lös teknik:

||**Windows Hello för företag**|**Lösen ords återsignering med Microsoft Authenticator-appen**|**FIDO2 säkerhets nycklar**|
|:-|:-|:-|:-|
|**Krav**| Windows 10, version 1809 eller senare<br>Azure Active Directory| Microsoft Authenticator-appen<br>Telefon (iOS-och Android-enheter som kör Android 6,0 eller senare)|Windows 10, version 1903 eller senare<br>Azure Active Directory|
|**Läge**|Plattform|Programvara|Maskinvara|
|**System och enheter**|PC med inbyggd Trusted Platform Module (TPM)<br>PIN-kod och biometrik-igenkänning |PIN-kod och biometrik-igenkänning på telefon|FIDO2 säkerhets enheter som är Microsoft-kompatibla|
|**Användarupplevelse**|Logga in med en PIN-kod eller bio metrisk igenkänning (ansikts, iris eller finger avtryck) med Windows-enheter.<br>Windows Hello-autentisering är knuten till enheten. användaren behöver både enheten och en inloggnings komponent som en PIN-kod eller bio metrisk faktor för att få åtkomst till företags resurser.|Logga in med en mobil telefon med finger avtrycks skanning, ansikts-eller iris eller PIN-kod.<br>Användarna loggar in på arbetet eller det personliga kontot från sin dator eller mobil telefon.|Logga in med FIDO2-säkerhetsenheten (biometrik, PIN och NFC)<br>Användaren kan komma åt enheten baserat på organisations kontroller och autentiseras baserat på PIN-kod, biometrik med enheter som USB-säkerhetsnycklar och NFC-aktiverade smartkort, nycklar eller wearables.|
|**Aktiverade scenarier**| Lösen ords lös upplevelse med Windows-enheter.<br>Gäller för dedikerade arbets datorer med möjlighet till enkel inloggning till enhet och program.|Lösen ords lös lösningen överallt med mobil telefon.<br>Gäller för åtkomst till arbets-eller personliga program på webben från vilken enhet som helst.|Lösen ords lös upplevelse för arbetare som använder biometrik, PIN och NFC.<br>Gäller för delade datorer och där en mobil telefon inte är ett lämpligt alternativ (t. ex. för support personal, offentlig kiosk eller sjukhus lag)|

Använd följande tabell för att välja vilken metod som ska ha stöd för dina krav och användare.

|Person|Scenario|Miljö|Teknik med lösen ords teknik|
|:-|:-|:-|:-|
|**Administratör**|Säker åtkomst till en enhet för hanterings uppgifter|Tilldelad Windows 10-enhet|Windows Hello för företag och/eller FIDO2 säkerhets nyckel|
|**Administratör**|Hanterings uppgifter på icke-Windows-enheter| Mobil eller icke-Windows-enhet|Lösen ords återsignering med Microsoft Authenticator-appen|
|**Informationsarbetare**|Produktivitets arbete|Tilldelad Windows 10-enhet|Windows Hello för företag och/eller FIDO2 säkerhets nyckel|
|**Informationsarbetare**|Produktivitets arbete| Mobil eller icke-Windows-enhet|Lösen ords återsignering med Microsoft Authenticator-appen|
|**Frontline Worker**|Kiosker i en fabriks-, anläggnings-, detalj-eller data ingång|Delade Windows 10-enheter|FIDO2 säkerhets nycklar|

## <a name="next-steps"></a>Nästa steg

För att komma igång med lösen ords lös i Azure AD, fyller du i någon av följande instruktioner:

* [Aktivera FIDO2-inloggning med lösen ord för säkerhets nyckel](howto-authentication-passwordless-security-key.md)
* [Aktivera telefonbaserad inloggning via telefon med Authenticator-appen](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Externa länkar

* [FIDO Alliance](https://fidoalliance.org/)
* [FIDO2 CTAP-specifikation](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
