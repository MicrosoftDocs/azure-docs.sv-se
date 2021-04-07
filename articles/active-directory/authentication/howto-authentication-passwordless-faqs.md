---
title: Vanliga frågor och svar om distribution av hybrid FIDO2-säkerhets nycklar – Azure Active Directory
description: Lär dig mer om vanliga frågor och svar om lösen ords skydd med hybrid FIDO2-inloggning med Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca4943293f9474d4089267d05460d6d8766b79e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646392"
---
# <a name="deployment-frequently-asked-questions-faqs-for-hybrid-fido2-security-keys-in-azure-ad"></a>Vanliga frågor och svar om distribution av hybrid FIDO2 säkerhets nycklar i Azure AD 

Den här artikeln beskriver vanliga frågor och svar om distribution av hybrid Azure AD-anslutna enheter och lösen ords lös inloggning till lokal resurser. Med den här funktionen för lösen ords skydd kan du aktivera Azure AD-autentisering på Windows 10-enheter för Hybrid Azure AD-anslutna enheter med hjälp av FIDO2-säkerhetsnycklar. Användare kan logga in på Windows på sina enheter med moderna autentiseringsuppgifter som FIDO2-nycklar och komma åt traditionella Active Directory Domain Services (AD DS) baserade resurser med en sömlös enkel inloggning (SSO) till sina lokal-resurser.

Följande scenarier för användare i en hybrid miljö stöds:

* Logga in på Hybrid Azure AD-anslutna enheter med FIDO2-säkerhetsnycklar och få SSO-åtkomst till lokal-resurser.
* Logga in på Azure AD-anslutna enheter med FIDO2-säkerhetsnycklar och få SSO-åtkomst till lokal-resurser.

Information om hur du kommer igång med FIDO2-säkerhetsnycklar och hybrid åtkomst till lokala resurser finns i följande artiklar:

* [Lösenordsfria FIDO2-säkerhetsnycklar](howto-authentication-passwordless-security-key.md)
* [Lösenordsfritt Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [Lösenordsfritt lokalt](howto-authentication-passwordless-security-key-on-premises.md)

## <a name="security-keys"></a>Säkerhets nycklar

* [Min organisation kräver två Factor Authentication för att få åtkomst till resurser. Vad kan jag göra för att stödja det här kravet?](#my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement)
* [Var kan jag hitta kompatibla FIDO2 säkerhets nycklar?](#where-can-i-find-compliant-fido2-security-keys)
* [Vad gör jag om jag tappar bort min säkerhets nyckel?](#what-if-i-lose-my-security-key)
* [Hur skyddas data på säkerhets nyckeln FIDO2?](#how-is-the-data-protected-on-the-fido2-security-key)
* [Hur fungerar registreringen av säkerhets nycklar för FIDO2?](#how-does-the-registering-of-fido2-security-keys-work)
* [Finns det ett sätt för administratörer att etablera nycklar för användarna direkt?](#is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly)

### <a name="my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Min organisation kräver Multi-Factor Authentication för att få åtkomst till resurser. Vad kan jag göra för att stödja det här kravet?

FIDO2-säkerhetsnycklarna ingår i olika form faktorer. Kontakta enhets tillverkaren för att diskutera hur deras enheter kan aktive ras med en PIN-kod eller bio metrisk som en andra faktor. En lista över providers som stöds finns i [FIDO2 Security Keys providers](concept-authentication-passwordless.md#fido2-security-key-providers).

### <a name="where-can-i-find-compliant-fido2-security-keys"></a>Var kan jag hitta kompatibla FIDO2 säkerhets nycklar?

En lista över providers som stöds finns i [FIDO2 Security Keys providers](concept-authentication-passwordless.md#fido2-security-key-providers).

### <a name="what-if-i-lose-my-security-key"></a>Vad händer om jag tappar bort min säkerhets nyckel?

Du kan ta bort nycklar i Azure Portal genom att gå till sidan **säkerhets information** och ta bort säkerhets nyckeln FIDO2.

### <a name="how-is-the-data-protected-on-the-fido2-security-key"></a>Hur skyddas data på säkerhets nyckeln FIDO2?

FIDO2 säkerhets nycklar har säker enclaves som skyddar de privata nycklar som lagras på dem. En FIDO2 säkerhets nyckel har också inbyggda egenskaper för Anti-hammering i den, som i Windows Hello, där du inte kan extrahera den privata nyckeln.

### <a name="how-does-the-registering-of-fido2-security-keys-work"></a>Hur fungerar registreringen av säkerhets nycklar för FIDO2?

Mer information om hur du registrerar och använder FIDO2 säkerhets nycklar finns i [Aktivera lösen ords lös säkerhets nyckel inloggning](howto-authentication-passwordless-security-key.md).

### <a name="is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly"></a>Finns det ett sätt för administratörer att etablera nycklar för användarna direkt?

Nej, inte just nu.

## <a name="prerequisites"></a>Förutsättningar

* [Fungerar den här funktionen om det inte finns någon Internet anslutning?](#does-this-feature-work-if-theres-no-internet-connectivity)
* [Vilka är de särskilda slut punkter som krävs för att vara öppna för Azure AD?](#what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad)
* [Hur gör jag för att identifiera domän Anslutnings typ (Azure AD-ansluten eller hybrid Azure AD-ansluten) för min Windows 10-enhet?](#how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device)
* [Vad är rekommendationen för antalet domänkontrollanter som bör korrigeras?](#whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched)
* [Kan jag distribuera FIDO2-providern för autentiseringsuppgifter på en lokal enhet?](#can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device)
* [FIDO2 för säkerhets nycklar fungerar inte för min domän administratör eller andra konton med hög behörighet. Varför?](#fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why)

### <a name="does-this-feature-work-if-theres-no-internet-connectivity"></a>Fungerar den här funktionen om det inte finns någon Internet anslutning?

Internet anslutning är ett krav för att aktivera den här funktionen. Första gången en användare loggar in med FIDO2-säkerhetsnycklar måste de ha Internet anslutning. För efterföljande inloggnings händelser bör cachelagrad inloggning fungera och låta användaren autentiseras utan Internet anslutning.

För en konsekvent upplevelse kontrollerar du att enheterna har till gång till Internet och att det går att upptäcka domänkontrollanter i DCs.

### <a name="what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad"></a>Vilka är de särskilda slut punkter som krävs för att vara öppna för Azure AD?

Följande slut punkter krävs för registrering och autentisering:

* **. microsoftonline.com*
* **. microsoftonline-p.com*
* **. msauth.net*
* **. msauthimages.net*
* **. msecnd.net*
* **. msftauth.net*
* **. msftauthimages.net*
* **. phonefactor.net*
* *enterpriseregistration.windows.net*
* *management.azure.com*
* *policykeyservice.dc.ad.msft.net*
* *secure.aadcdn.microsoftonline-p.com*

En fullständig lista över slut punkter som behövs för att använda Microsoft Online-produkter finns i [Office 365-URL: er och IP-adressintervall](/microsoft-365/enterprise/urls-and-ip-address-ranges).

### <a name="how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device"></a>Hur gör jag för att identifiera domän Anslutnings typ (Azure AD-ansluten eller hybrid Azure AD-ansluten) för min Windows 10-enhet?

Använd följande kommando för att kontrol lera om klient enheten för Windows 10 har rätt domän Anslutnings typ:

```console
Dsregcmd/status
```

Följande exempel på utdata visar att enheten är Azure AD-ansluten eftersom *AzureADJoined* har angetts till *Ja*:

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: NO
```

Följande exempel på utdata visar att enheten är hybrid Azure AD-ansluten eftersom *DomainedJoined* också är inställd på *Ja*. *Domän namn* visas också:

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: YES
DomainName: CONTOSO
```

På en Windows Server 2016-eller 2019-domänkontrollant kontrollerar du att följande korrigeringsfiler har tillämpats. Om det behövs kör Windows Update för att installera dem:

* Windows Server 2016 – [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019 – [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

Kör följande kommando från en klienten het för att kontrol lera anslutningen till en lämplig domänkontrollant med korrigerings filen installerat:

```console
nltest /dsgetdc:<domain> /keylist /kdc
```

### <a name="whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched"></a>Vad är rekommendationen för antalet domänkontrollanter som bör korrigeras?

Vi rekommenderar att du korrigerar en majoritet av domän kontrol Lanterna för Windows Server 2016 eller 2019 med korrigerings filen för att säkerställa att de kan hantera belastningen på din organisations autentiseringsbegäranden.

På en Windows Server 2016-eller 2019-domänkontrollant kontrollerar du att följande korrigeringsfiler har tillämpats. Om det behövs kör Windows Update för att installera dem:

* Windows Server 2016 – [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019 – [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

### <a name="can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device"></a>Kan jag distribuera FIDO2-providern för autentiseringsuppgifter på en lokal enhet?

Nej, den här funktionen stöds inte för den lokala enheten. FIDO2-providern för autentiseringsuppgifter skulle inte visas.

### <a name="fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why"></a>FIDO2 för säkerhets nycklar fungerar inte för min domän administratör eller andra konton med hög behörighet. Varför?

Standard säkerhets principen beviljar inte Azure AD behörighet att signera konton med hög behörighet till lokala resurser.

Om du vill avblockera konton använder **Active Directory användare och datorer** för att ändra egenskapen *msDS-NEVERREVEALGROUP* för *Azure AD Kerberos-datorobjektet (CN = AzureADKerberos, OU = Domain Controllers \<domain-DN> )*.

## <a name="under-the-hood"></a>Under huven

* [Hur länkas Azure AD Kerberos till min lokala Active Directory Domain Servicess miljö?](#how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment)
* [Var kan jag se de här Kerberos-Server objekt som skapas i AD och publiceras i Azure AD?](#where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad)
* [Varför kan jag inte ha den offentliga nyckeln registrerad för lokal AD DS, så det finns inget beroende av Internet?](#why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet)
* [Hur roteras nycklarna på objektet Kerberos-Server?](#how-are-the-keys-rotated-on-the-kerberos-server-object)
* [Varför behöver vi Azure AD Connect? Skrivs all information tillbaka till AD DS från Azure AD?](#why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad)
* [Vad ser HTTP-begäran/-svaret till när du begär PRT + delvis TGT?](#what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt)

### <a name="how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment"></a>Hur länkas Azure AD Kerberos till min lokala Active Directory Domain Servicess miljö?

Det finns två delar – den lokala AD DS-miljön och Azure AD-klienten.

**Active Directory Domain Services (AD DS)**

Azure AD Kerberos-servern representeras i en lokal AD DS-miljö som en domänkontrollant (DC)-objekt. Det här DC-objektet består av flera objekt:

* *CN = AzureADKerberos, OU = domän kontrol Lanterna,\<domain-DN>*
    
    Ett *dator* objekt som representerar en Read-Only DOMÄNKONTROLLANT (RODC) i AD DS. Det finns ingen dator kopplad till det här objektet. I stället är det en logisk representation av en DOMÄNKONTROLLANT.

* *CN = krbtgt_AzureAD, CN = användare,\<domain-DN>*

    Ett *användar* objekt som representerar en RODC-krypterings nyckel (TGT-Ticket Granting Ticket).

* *CN = 900274c4-b7d2-43c8-90ee-00a9f650e335, CN = AzureAD, CN = System,\<domain-DN>*

    Ett *ServiceConnectionPoint* -objekt som lagrar metadata om Azure AD Kerberos-serverobjektet. Administrations verktygen använder det här objektet för att identifiera och hitta Azure AD Kerberos-serverobjektet.

**Azure Active Directory**

Azure AD Kerberos-servern representeras i Azure AD som ett *KerberosDomain* -objekt. Varje lokal AD DS-miljö representeras som ett enda *KerberosDomain* -objekt i Azure AD-klienten.

Du kan till exempel ha en AD DS-skog med två domäner som *contoso.com* och *fabrikam.com*. Om du tillåter att Azure AD utfärdar Kerberos Ticket Granting Tick (TGT) för hela skogen finns det två *KerberosDomain* -objekt i Azure AD – ett-objekt för *contoso.com* och ett för *fabrikam.com*.

Om du har flera AD DS-skogar har du ett *KerberosDomain* -objekt för varje domän i varje skog.

### <a name="where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad"></a>Var kan jag se dessa Kerberos-Server objekt som skapats i AD DS och publiceras i Azure AD?

Om du vill visa alla objekt använder du Azure AD Kerberos-PowerShell-cmdletar som ingår i den senaste versionen av Azure AD Connect.

Mer information, inklusive anvisningar om hur du visar objekten, finns i [skapa Kerberos-Server objekt](howto-authentication-passwordless-security-key-on-premises.md#create-kerberos-server-object).

### <a name="why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet"></a>Varför kan jag inte ha den offentliga nyckeln registrerad för lokal AD DS, så det finns inget beroende av Internet?

Vi har fått feedback kring distributions modellens komplexitet för Windows Hello för företag, så att du enkelt kan förenkla distributions modellen utan att behöva använda certifikat och PKI (FIDO2 använder inte certifikat).

### <a name="how-are-the-keys-rotated-on-the-kerberos-server-object"></a>Hur roteras nycklarna på objektet Kerberos-Server?

Precis som med andra domänkontrollanter bör dina *KRBTGT* -nycklar för Azure AD Kerberos-servern roteras med jämna mellanrum. Vi rekommenderar att du följer samma schema som när du använder för att rotera alla andra AD DS *KRBTGT* -nycklar.

> [!NOTE]
> Även om det finns andra verktyg för att rotera *KRBTGT* -nycklarna måste du [använda PowerShell-cmdletar för att rotera *KRBTGT* -nycklarna](howto-authentication-passwordless-security-key-on-premises.md#rotating-the-azure-ad-kerberos-server-key) för Azure AD Kerberos-servern. Den här metoden säkerställer att nycklarna uppdateras både i den lokala AD DS-miljön och i Azure AD.

### <a name="why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad"></a>Varför behöver vi Azure AD Connect? Skrivs all information tillbaka till AD DS från Azure AD?

Azure AD Connect skriver inte tillbaka information från Azure AD till AD DS. Verktyget inkluderar PowerShell-modulen för att skapa Kerberos-serverobjektet i AD DS och publicera det i Azure AD.

### <a name="what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt"></a>Vad ser HTTP-begäran/-svaret till när du begär PRT + delvis TGT?

HTTP-begäran är en standard-PRT-begäran (Primary Refresh token). Denna PRT-begäran innehåller ett anspråk som anger att en TGT-biljett (Ticket Granting Ticket) krävs.

| Begär | Värde | Beskrivning                             |
|-------|-------|-----------------------------------------|
| Tick   | true  | Anspråk anger att klienten behöver en TGT. |

Azure AD kombinerar den krypterade klient nyckeln och meddelande bufferten till PRT-svaret som ytterligare egenskaper. Nytto lasten krypteras med Azure AD-enhetens sessionsnyckel.

| Fält              | Typ   | Beskrivning  |
|--------------------|--------|--------------|
| tgt_client_key     | sträng | Base64-kodad klient nyckel (hemlighet). Den här nyckeln är den klient hemlighet som används för att skydda TGT. I det här lösen ords scenariot genereras klient hemligheten av servern som en del av varje TGT-begäran och returneras sedan till klienten i svaret. |
| tgt_key_type       | int    | Den lokala AD DS-nyckel typen som används för både klient nyckeln och Kerberos-sessionsnyckeln som ingår i KERB_MESSAGE_BUFFER. |
| tgt_message_buffer | sträng | Base64-kodade KERB_MESSAGE_BUFFER. |

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång med FIDO2-säkerhetsnycklar och hybrid åtkomst till lokala resurser finns i följande artiklar:

* [Lösenordsfria FIDO2-säkerhetsnycklar](howto-authentication-passwordless-security-key.md)
* [Lösenordsfritt Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [Lösenordsfritt lokalt](howto-authentication-passwordless-security-key-on-premises.md)
