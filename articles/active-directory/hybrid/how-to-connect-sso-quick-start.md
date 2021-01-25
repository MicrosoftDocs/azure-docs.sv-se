---
title: 'Azure AD Connect: sömlös Single Sign-On-snabb start | Microsoft Docs'
description: Den här artikeln beskriver hur du kommer igång med Azure Active Directory sömlösa enkla Sign-On
services: active-directory
keywords: Vad är Azure AD Connect, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59cc50274b291c23aeec4620ec7a09312cc0c1fb
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762259"
---
# <a name="azure-active-directory-seamless-single-sign-on-quickstart"></a>Azure Active Directory sömlös enkel inloggning: snabb start

## <a name="deploy-seamless-single-sign-on"></a>Distribuera sömlösa enkla Sign-On

Azure Active Directory (Azure AD) sömlös enkel Sign-On (sömlös SSO) loggar automatiskt in användare när de befinner sig på sina företags datorer som är anslutna till företagets nätverk. Sömlös SSO ger dina användare enkel åtkomst till dina molnbaserade program utan att behöva ytterligare lokala komponenter.

Följ dessa steg om du vill distribuera sömlös SSO.

## <a name="step-1-check-the-prerequisites"></a>Steg 1: kontrol lera kraven

Se till att följande krav är uppfyllda:

* **Konfigurera din Azure AD Connect Server**: om du använder [direktautentisering](how-to-connect-pta.md) som inloggnings metod krävs ingen ytterligare krav kontroll. Om du använder [Password-hash-synkronisering](how-to-connect-password-hash-synchronization.md) som inloggnings metod och om det finns en brand vägg mellan Azure AD Connect och Azure AD, kontrollerar du att:
   - Du använder version 1.1.644.0 eller senare av Azure AD Connect. 
   - Om din brand vägg eller proxy tillåter kan du lägga till anslutningarna till listan över tillåtna **\* Msappproxy.net** -URL: er via port 443. Om du behöver en viss URL i stället för ett jokertecken för proxy-konfigurationen kan du konfigurera **tenantid.registration.msappproxy.net**, där TENANTID är GUID för den klient där du konfigurerar funktionen. Om URL-baserade proxy-undantag inte är möjliga i din organisation, kan du i stället tillåta åtkomst till [Azure Data Center IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653), som uppdateras varje vecka. Den här förutsättningen gäller endast när du aktiverar funktionen. Det krävs inte för faktiska användar inloggningar.

    >[!NOTE]
    >Azure AD Connect-versionerna 1.1.557.0, 1.1.558.0, 1.1.561.0 och 1.1.614.0 har ett problem som rör hash-synkronisering av lösen ord. Om du _inte_ tänker använda Lösenordssynkronisering i samband med direktautentisering kan du läsa mer i [Azure AD Connect viktig information](./reference-connect-version-history.md) .

* **Använd en Azure AD Connect topologi som stöds**: kontrol lera att du använder en av Azure AD Connects topologier som stöds beskrivs [här](plan-connect-topologies.md).

    >[!NOTE]
    >Sömlös SSO stöder flera AD-skogar, oavsett om det finns AD-förtroenden mellan dem eller inte.

* **Konfigurera autentiseringsuppgifter för domän administratör**: du måste ha autentiseringsuppgifter för domän administratör för varje Active Directory skog som:
    * Du synkroniserar till Azure AD via Azure AD Connect.
    * Innehåller användare som du vill aktivera för sömlös SSO.
    
* **Aktivera modern autentisering**: du måste aktivera [modern autentisering](/office365/enterprise/modern-auth-for-office-2013-and-2016) på din klient för att den här funktionen ska fungera.

* **Använd de senaste versionerna av Microsoft 365-klienter**: för att få en tyst inloggnings upplevelse med Microsoft 365 klienter (Outlook, Word, Excel och andra) måste användarna använda versionerna 16.0.8730. xxxx eller senare.

## <a name="step-2-enable-the-feature"></a>Steg 2: aktivera funktionen

Aktivera sömlös SSO via [Azure AD Connect](whatis-hybrid-identity.md).

>[!NOTE]
> Du kan också [Aktivera sömlös enkel inloggning med PowerShell](tshoot-connect-sso.md#manual-reset-of-the-feature) om Azure AD Connect inte uppfyller dina krav. Använd det här alternativet om du har mer än en domän per Active Directory skog och du vill bli mer riktad till den domän som du vill aktivera sömlös SSO för.

Om du har en ny installation av Azure AD Connect väljer du den [anpassade installations Sök vägen](how-to-connect-install-custom.md). På sidan **användar inloggning** väljer du alternativet **aktivera enkel inloggning** .

>[!NOTE]
> Alternativet är bara tillgängligt för markering om metoden Sign on är **Password hash-synkronisering** eller **direktautentisering**.

![Azure AD Connect: användar inloggning](./media/how-to-connect-sso-quick-start/sso8.png)

Om du redan har en installation av Azure AD Connect väljer du **inloggnings sidan Ändra användare** i Azure AD Connect och väljer sedan **Nästa**. Om du använder Azure AD Connect versioner 1.1.880.0 eller senare, är alternativet **aktivera enkel inloggning** markerat som standard. Om du använder äldre versioner av Azure AD Connect väljer du alternativet **aktivera enkel inloggning** .

![Azure AD Connect: ändra användar inloggningen](./media/how-to-connect-sso-quick-start/changeusersignin.png)

Fortsätt med guiden tills du kommer till sidan **aktivera enkel inloggning** . Ange autentiseringsuppgifter för domän administratör för varje Active Directory skog som:

* Du synkroniserar till Azure AD via Azure AD Connect.
* Innehåller användare som du vill aktivera för sömlös SSO.

När guiden har slutförts aktive ras sömlös SSO på din klient.

>[!NOTE]
> Autentiseringsuppgifterna för domän administratören lagras inte i Azure AD Connect eller i Azure AD. De används endast för att aktivera funktionen.

Följ dessa anvisningar för att kontrol lera att du har aktiverat sömlös SSO korrekt:

1. Logga in på [Azure Active Directory administrations Center](https://aad.portal.azure.com) med den globala administratörs behörigheten för din klient organisation.
2. Välj **Azure Active Directory** i det vänstra fönstret.
3. Välj **Azure AD Connect**.
4. Kontrol lera att funktionen **sömlös enkel inloggning** visas som **aktive rad**.

![Azure Portal: Azure AD Connects fönstret](./media/how-to-connect-sso-quick-start/sso10.png)

>[!IMPORTANT]
> Sömlös SSO skapar ett dator konto `AZUREADSSOACC` som heter i din lokala Active Directory (AD) i varje AD-skog. `AZUREADSSOACC`Dator kontot måste vara starkt skyddat av säkerhets skäl. Endast domän administratörer bör kunna hantera dator kontot. Se till att Kerberos-delegering på dator kontot är inaktiverat och att inget annat konto i Active Directory har Delegerings behörighet för `AZUREADSSOACC` dator kontot. Lagra dator kontot i en organisationsenhet (OU) där de är säkra från oavsiktliga borttagningar och där endast domän administratörer har åtkomst.

>[!NOTE]
> Om du använder pass-The-hash och stöld av stöld av autentiseringsuppgifter i din lokala miljö bör du göra lämpliga ändringar för att se till att `AZUREADSSOACC` dator kontot inte upphör i karantän behållaren. 

## <a name="step-3-roll-out-the-feature"></a>Steg 3: Distribuera funktionen

Du kan gradvis distribuera sömlös SSO till dina användare med hjälp av anvisningarna nedan. Du börjar med att lägga till följande Azure AD-URL till alla eller valda användares intranät zons inställningar med hjälp av grupprincip i Active Directory:

- `https://autologon.microsoftazuread-sso.com`

Dessutom måste du aktivera en princip inställning för intranät zon som kallas **Tillåt uppdateringar till statusfältet via skript** genom att Grupprincip. 

>[!NOTE]
> Följande anvisningar fungerar bara för Internet Explorer, Microsoft Edge och Google Chrome i Windows (om det delar en uppsättning betrodda webbplats-URL: er med Internet Explorer). Läs nästa avsnitt för instruktioner om hur du konfigurerar Mozilla Firefox och Google Chrome på macOS.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Varför behöver du ändra användarnas intranät zons inställningar?

Som standard beräknar webbläsaren automatiskt rätt zon, antingen Internet eller intranät, från en viss URL. Mappar till exempel `http://contoso/` till zonen Intranät, och `http://intranet.contoso.com/` mappar till zonen Internet (eftersom URL: en innehåller en punkt). Webbläsare skickar inte Kerberos-biljetter till en moln slut punkt, t. ex. Azure AD-URL, om du inte uttryckligen lägger till URL: en i webbläsarens intranät zon.

Det finns två sätt att ändra användares intranät zon inställningar:

| Alternativ | Administrativa överväganden | Användarupplevelse |
| --- | --- | --- |
| Grupprincip | Admin låser redigerings inställningarna för zonen Intranät | Användare kan inte ändra sina egna inställningar |
| Grup princip inställningar |  Admin tillåter redigering av inställningar för zonen Intranät | Användare kan ändra sina egna inställningar |

### <a name="group-policy-option---detailed-steps"></a>Alternativet "grup princip" – detaljerade steg

1. Öppna Redigeraren Grupprinciphantering-verktyget.
2. Redigera grup principen som används för vissa eller alla dina användare. I det här exemplet används **standard domän principen**.
3. Bläddra till **användar konfigurations**  >  **principer**  >  **administrativa mallar**  >  **Windows-komponenter**  >  **Internet Explorer Internet Explorer**  >  **Internet Control Panel** på  >  **säkerhets sidan**. Välj sedan **plats till zon tilldelnings lista**.
    ![Skärm bild som visar "säkerhets sidan" med "plats till zon tilldelnings lista" vald.](./media/how-to-connect-sso-quick-start/sso6.png)
4. Aktivera principen och ange sedan följande värden i dialog rutan:
   - **Värde namn**: Azure AD-URL: en där Kerberos-biljetterna vidarebefordras.
   - **Värde** (data): **1** anger zonen Intranät.

     Resultatet ser ut så här:

     Värde namn: `https://autologon.microsoftazuread-sso.com`
  
     Värde (data): 1

   >[!NOTE]
   > Om du vill förhindra att vissa användare använder sömlös SSO (t. ex. om dessa användare loggar in på delade kiosker) anger du de föregående värdena till **4**. Den här åtgärden lägger till Azure AD-URL: en i den begränsade zonen och Miss lyckas sömlös SSO hela tiden.
   >

5. Välj **OK** och sedan **OK** igen.

    ![Skärm bild som visar fönstret "Visa innehåll" med en zon tilldelning markerad.](./media/how-to-connect-sso-quick-start/sso7.png)

6. Bläddra till **användar konfigurations**  >  **principer**  >  **administrativa mallar**  >  **Windows-komponenter**  >  **Internet Explorer Internet Explorer**  >  **Internet Control Panel**  >  **säkerhets sida**  >  **zonen Intranät**. Välj sedan **Tillåt uppdateringar av statusfältet via skript**.

    ![Skärm bild som visar sidan "intranät zon" med "Tillåt uppdateringar till statusfält via skript" markerat.](./media/how-to-connect-sso-quick-start/sso11.png)

7. Aktivera princip inställningen och välj sedan **OK**.

    ![Skärm bild som visar fönstret Tillåt uppdateringar av statusfältet via skript och inställningen aktive rad.](./media/how-to-connect-sso-quick-start/sso12.png)

### <a name="group-policy-preference-option---detailed-steps"></a>Alternativet "inställningar för grup princip" – detaljerade steg

1. Öppna Redigeraren Grupprinciphantering-verktyget.
2. Redigera grup principen som används för vissa eller alla dina användare. I det här exemplet används **standard domän principen**.
3. Bläddra till **användar konfigurations**  >  **Inställningar**  >  **Windows-inställningar**  >  **register**  >  **nytt**  >  **register objekt**.

    ![Skärm bild som visar "registret" markerat och "register objekt" valt.](./media/how-to-connect-sso-quick-start/sso15.png)

4. Ange följande värden i lämpliga fält och klicka på **OK**.
   - **Nyckel Sök väg**: **_Software\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap\Domains\microsoftazuread-SSO.com\autologon_* _
   - _* Värde namn * *: **_https_*_
   - _* Värdetyp * *: **_REG_DWORD_*_
   - _* Värde data * *: **_00000001_*_
 
     ![Skärm bild som visar fönstret "nya register egenskaper".](./media/how-to-connect-sso-quick-start/sso16.png)
 
     ![Enkel inloggning](./media/how-to-connect-sso-quick-start/sso17.png)

### <a name="browser-considerations"></a>Webb läsar överväganden

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (alla plattformar)

I Mozilla Firefox används inte Kerberos-autentisering automatiskt. Varje användare måste manuellt lägga till Azure AD-URL: en i sina Firefox-inställningar med hjälp av följande steg:
1. Kör Firefox och ange `about:config` i adress fältet. Stäng alla meddelanden som visas.
2. Sök efter inställningen _ *nätverk. Negotiate-auth. Trusted-URI** Preference. Den här inställningen listar Firefoxs betrodda platser för Kerberos-autentisering.
3. Högerklicka och välj **ändra**.
4. Ange `https://autologon.microsoftazuread-sso.com` i fältet.
5. Välj **OK** och öppna sedan webbläsaren igen.

#### <a name="safari-macos"></a>Safari (macOS)

Se till att datorn som kör macOS är ansluten till AD. Anvisningar för AD-anslutning av din macOS-enhet omfattas inte av den här artikeln.

#### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge baserat på krom (alla plattformar)

Om du har åsidosatt [AuthNegotiateDelegateAllowlist](/DeployEdge/microsoft-edge-policies#authnegotiatedelegateallowlist) -eller [AuthServerAllowlist](/DeployEdge/microsoft-edge-policies#authserverallowlist) -principinställningar i din miljö kontrollerar du att du även lägger till Azure AD-URL: en ( `https://autologon.microsoftazuread-sso.com` ) till dem.

#### <a name="microsoft-edge-based-on-chromium-macos-and-other-non-windows-platforms"></a>Microsoft Edge baserat på krom (macOS och andra plattformar som inte är Windows-plattformar)

För Microsoft Edge som baseras på krom på macOS och andra plattformar som inte är Windows-plattformar kan du läsa mer om hur du lägger till Azure AD-URL: en för integrerad autentisering i din Tillåt-lista i [Microsoft Edge baserad på krom princip listan](/DeployEdge/microsoft-edge-policies#authserverallowlist) .

#### <a name="google-chrome-all-platforms"></a>Google Chrome (alla plattformar)

Om du har åsidosatt [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) -eller [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) -principinställningar i din miljö kontrollerar du att du även lägger till Azure AD-URL: en ( `https://autologon.microsoftazuread-sso.com` ) till dem.

#### <a name="google-chrome-macos-and-other-non-windows-platforms"></a>Google Chrome (macOS och andra plattformar som inte är Windows-plattformar)

För Google Chrome på macOS och andra plattformar som inte är Windows-plattformar går du till [princip listan för krom projekt](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) om du vill ha information om hur du styr listan över tillåtna för Azure AD-URL: en för integrerad autentisering.

Användning av Active Directory grupprincip tillägg från tredje part för att distribuera Azure AD-URL: en till Firefox och Google Chrome på Mac-användare är utanför den här artikeln.

#### <a name="known-browser-limitations"></a>Kända webb läsar begränsningar

Sömlös SSO fungerar inte i privat bläddringsläge i Firefox och Microsoft Edge-webbläsare. Det fungerar inte heller i Internet Explorer om webbläsaren körs i utökat skyddat läge. För nästa version av Microsoft Edge baserat på krom fungerar den inte i InPrivate-och gäst läge genom design.

## <a name="step-4-test-the-feature"></a>Steg 4: testa funktionen

Kontrol lera att alla följande villkor är uppfyllda om du vill testa funktionen för en speciell användare:
  - Användaren loggar in på en företags enhet.
  - Enheten är ansluten till din Active Directorys domän. Enheten behöver _inte_ vara [Azure AD-ansluten](../devices/overview.md).
  - Enheten har en direkt anslutning till domänkontrollanten (DC), antingen i det kabelanslutna eller trådlösa nätverket eller via en fjärråtkomstanslutning, t. ex. en VPN-anslutning.
  - Du har [distribuerat funktionen](#step-3-roll-out-the-feature) till den här användaren via Grupprincip.

Så här testar du scenariot där användaren bara anger användar namnet, men inte lösen ordet:
   - Logga in på `https://myapps.microsoft.com/` i en ny privat webbläsarsession.

Använd något av följande steg för att testa scenariot där användaren inte behöver ange användar namnet eller lösen ordet: 
   - Logga in på `https://myapps.microsoft.com/contoso.onmicrosoft.com` i en ny privat webbläsarsession. Ersätt *contoso* med klient organisationens namn.
   - Logga in på `https://myapps.microsoft.com/contoso.com` i en ny privat webbläsarsession. Ersätt *contoso.com* med en verifierad domän (inte en federerad domän) på din klient.

## <a name="step-5-roll-over-keys"></a>Steg 5: rulla över nycklar

I steg 2 skapar Azure AD Connect dator konton (som representerar Azure AD) i alla Active Directory skogar där du har aktiverat sömlös SSO. Läs mer i [Azure Active Directory sömlös enkel inloggning: teknisk djupgående](how-to-connect-sso-how-it-works.md).

>[!IMPORTANT]
>Krypterings nyckeln för Kerberos på ett dator konto, om den läcker, kan användas för att generera Kerberos-biljetter för alla användare i AD-skogen. Skadliga aktörer kan sedan personifiera Azure AD-inloggningar för komprometterade användare. Vi rekommenderar starkt att du regelbundet rullar över dessa Kerberos-dekrypterings nycklar, minst en gång var 30: e dag.

Instruktioner för hur du rullar över nycklar finns [Azure Active Directory sömlös enkel inloggning: vanliga frågor och svar](how-to-connect-sso-faq.md). Vi arbetar på en möjlighet att införa automatiserade nycklar.

>[!IMPORTANT]
>Du behöver inte göra det här steget _direkt_ efter att du har aktiverat funktionen. Rulla över Kerberos-dekrypterings nycklarna minst en gång var 30: e dag.

## <a name="next-steps"></a>Nästa steg

- [Teknisk djupgående](how-to-connect-sso-how-it-works.md): förstå hur den sömlösa Sign-On funktionen fungerar.
- [Vanliga frågor](how-to-connect-sso-faq.md)och svar: få svar på vanliga frågor om sömlös enkel inloggning.
- [Felsöka](tshoot-connect-sso.md): Lär dig hur du löser vanliga problem med den sömlösa Sign-On funktionen.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Använd Azure Active Directory-forumet för att fil nya funktions begär Anden.
