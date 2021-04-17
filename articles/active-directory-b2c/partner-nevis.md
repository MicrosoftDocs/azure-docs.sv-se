---
title: Självstudie för att konfigurera Azure Active Directory B2C med Nevis
titleSuffix: Azure AD B2C
description: Lär dig hur du integrerar Azure AD B2C autentisering med Nevis för lösenordsfri autentisering
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/23/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 42e244249ecb0539637918ae2439bdb4f5da4b38
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588487"
---
# <a name="tutorial-to-configure-nevis-with-azure-active-directory-b2c-for-passwordless-authentication"></a>Självstudie för att konfigurera Nevis Azure Active Directory B2C för lösenordsfri autentisering

I den här exempelsjälvstudien lär du dig att utöka Azure AD B2C  [med Nevis för](https://www.nevis.net/en/solution/authentication-cloud) att aktivera lösenordsfri autentisering. Nevis tillhandahåller en mobil- och helt varumärkesspecifik slutanvändarupplevelse med Nevis Access-appen för att tillhandahålla stark kundautentisering och uppfylla transaktionskraven för Betalningstjänstdirektiv 2 (PSD2).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du:

- Ett [Nevis-utvärderingskonto](https://www.nevis-security.com/aadb2c/)

- En Azure AD-prenumeration. Om du inte har ett konto kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)

- En [Azure AD B2C-klientorganisation](./tutorial-create-tenant.md) som är länkad till din Azure-prenumeration.

- Konfigurerad Azure AD B2C miljö för [användning](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)av anpassade principer om du vill integrera Nevis i ditt flöde för registreringsprinciper.

## <a name="scenario-description"></a>Scenariobeskrivning

I det här scenariot lägger du till en helt varumärkesbaserad åtkomstapp till serverappen för lösenordsfri autentisering. Följande komponenter utgör lösningen:

- En Azure AD B2C klientorganisation med en kombinerad inloggnings- och registreringsprincip till din backend
- Nevis-instansen och dess REST API för att förbättra Azure AD B2C
- Din egen varumärkesmärkta åtkomstapp

Diagrammet visar implementeringen.

![Inloggningsflöde för lösenord på hög nivå med Azure AD B2C och Nevis](./media/partner-nevis/nevis-architecture-diagram.png)

|Steg | Description |
|:-----| :-----------|
| 1. | En användare försöker logga in eller registrera sig för ett program via Azure AD B2C inloggnings- och registreringsprincipen.
| 2. | Under registrering registreras Nevis Access-appen på användarenheten med hjälp av en QR-kod. En privat nyckel genereras på användarenhet och används för att signera användarbegäranden.
| 3. |  Azure AD B2C använder en restful-teknisk profil för att starta inloggningen med Nevis-lösningen.
| 4. | Inloggningsbegäran skickas till åtkomstappen, antingen som ett push-meddelande, QR-kod eller som en djuplänk.
| 5. | Användaren godkänner inloggningsförsöket med sin biometrik. Ett meddelande returneras sedan till Nevis, som verifierar inloggningen med den lagrade offentliga nyckeln.
| 6. | Azure AD B2C skickar en sista begäran till Nevis för att bekräfta att inloggningen har slutförts.
| 7. |Baserat på meddelandet om lyckat/misslyckat Azure AD B2C användaren beviljas/nekas åtkomst till programmet.

## <a name="integrate-your-azure-ad-b2c-tenant"></a>Integrera din Azure AD B2C klient

### <a name="onboard-to-nevis"></a>Publicera till Nevis 

[Registrera dig för ett Nevis-konto.](https://www.nevis-security.com/aadb2c/)
Du får två e-postmeddelanden:

1. Ett meddelande om hanteringskonto

2. En mobilappinbjudan.

### <a name="add-your-azure-ad-b2c-tenant-to-your-nevis-account"></a>Lägga till Azure AD B2C klientorganisation till ditt Nevis-konto

1. Från utvärderingsversionen av Nevis-hanteringskontot kopierar du din hanteringsnyckel till Urklipp.

2. Öppna https://console.nevis.cloud/ i en webbläsare.

3. Logga in på hanteringskonsolen med din nyckel.

4. Välj **Lägg till instans**

5. Välj den nyligen skapade instansen för att öppna den.

6. I sidonavigeringsfältet väljer **du Anpassade integreringar**

7. Välj **Lägg till anpassad integrering.**

8. Som Integrationsnamn anger du Azure AD B2C klientorganisationens namn.

9. För URL/domän anger du `https://yourtenant.onmicrosoft.com`

10. Välj **Nästa**

>[!NOTE]
>Du behöver Åtkomsttoken för Nevis senare.

11. Välj **Klar**.

### <a name="install-the-nevis-access-app-on-your-phone"></a>Installera Nevis Access-appen på din telefon

1. Öppna testappinbjudan från **e-postmeddelandet med utvärderingsversionen av Nevis-mobilappen.**

2. Installera appen.

3. Följ anvisningarna för att installera Nevis Access-appen.

### <a name="integrate-azure-ad-b2c-with-nevis"></a>Integrera Azure AD B2C med Nevis

1. Öppna [Azure-portalen](https://portal.azure.com/).

2. Växla till din Azure AD B2C klientorganisation. Kontrollera att du har valt rätt klient eftersom den Azure AD B2C vanligtvis finns i en separat klientorganisation.

3. I menyn väljer du **Identity Experience Framework (IEF)**

4. Välj **principnycklar**

5. Välj **Lägg** till och skapa en ny nyckel med följande inställningar:

      a. Välj **Manuell** i Alternativ

      b. Ange Namn till **AuthCloudAccessToken**

      c. Klistra in den tidigare lagrade **Nevis-åtkomsttoken** i fältet Hemlighet

      d. För Nyckelanvändning väljer du **Kryptering**

      e. Välj **Skapa**

### <a name="configure-and-upload-the-nevishtml-to-azure-blob-storage"></a>Konfigurera och ladda upp nevis.htmtill Azure Blob Storage

1. I din identitetsmiljö (IDE) går du till [**principmappen.**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy)

2. Öppna [**filennevis.html.**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/nevis.html)

3. Ersätt  **authentication_cloud_url** med URL:en för Nevis-administratörskonsolen – `https://<instance_id>.mauth.nevis.cloud` .

4. **Spara** ändringarna i filen.

5. Följ [instruktionerna](./customize-ui-with-html.md#2-create-an-azure-blob-storage-account) och ladda upp **nevis.html till** Azure Blob Storage.

6. Följ [instruktionerna och](./customize-ui-with-html.md#3-configure-cors) aktivera Resursdelning för korsande ursprung (CORS) för den här filen.

7. När uppladdningen är klar och CORS är aktiverat väljer **dunevis.html-filen** i listan.

8. Välj **kopieringslänkikonen** bredvid **URL:en** **på fliken** Översikt.

9. Öppna länken i en ny webbläsarflik för att se till att den visar en grå ruta.

>[!NOTE]
>Du behöver bloblänken senare.

### <a name="customize-your-trustframeworkbasexml"></a>Anpassa TrustFrameworkBase.xml

1. Gå till principmappen i [**din**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) IDE.

2. Öppna [**TrustFrameworkBase.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkBase.xml) fil.

3. Ersätt **dinklient med namnet** på ditt Azure-klientkonto i **TenantId**.

4. Ersätt **yourtenant med** namnet på ditt Azure-klientkonto i **PublicPolicyURI**.

5. Ersätt alla **authentication_cloud_url** instanser med URL:en för Nevis-administratörskonsolen

6. **Spara** ändringarna i filen.

### <a name="customize-your-trustframeworkextensionsxml"></a>Anpassa TrustFrameworkExtensions.xml

1. Gå till principmappen i [**din**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) IDE.

2. Öppna [**TrustFrameworkExtensions.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkExtensions.xml) fil.

3. Ersätt **dinklient med namnet** på ditt Azure-klientkonto i **TenantId**.

4. Ersätt **yourtenant med** namnet på ditt Azure-klientkonto i **PublicPolicyURI**.

5. Under **BasePolicy** i **TenantId ersätter** du även _dinklient med namnet_ på ditt Azure-klientkonto.

6. Under **BuildingBlocks ersätter** du **LoadUri** med url:en för bloblänken _förnevis.htmi_ ditt bloblagringskonto.

7. **Spara** filen.

### <a name="customize-your-signuporsigninxml"></a>Anpassa SignUpOrSignin.xml

1. Gå till principmappen i [**din**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) IDE.

2. Öppna [**SignUpOrSignin.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/SignUpOrSignin.xml) fil.

3. Ersätt **dinklient med namnet** på ditt Azure-klientkonto i **TenantId**.

4. Ersätt **dinklient med namnet** på ditt Azure-klientkonto i **PublicPolicyUri.**

5. Under **BasePolicy** i **TenantId ersätter** du **även dinklient med namnet** på ditt Azure-klientkonto.

6. **Spara** filen.

### <a name="upload-your-custom-policies-to-azure-ad-b2c"></a>Ladda upp dina anpassade principer till Azure AD B2C

1. Öppna [](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) Azure AD B2C klientorganisationshem.

2. Välj **Identity Experience Framework**.

3. Välj **Ladda upp anpassad princip.**

4. Välj den **TrustFrameworkBase.xml** som du ändrade.

5. Markera **kryssrutan Overwrite the custom policy if it already exists (Skriva över** den anpassade principen om den redan finns).
6. Välj **Överför**.

7. Upprepa steg 5 och 6 för **TrustFrameworkExtensions.xml**.

8. Upprepa steg 5 och 6 för **SignUpOrSignin.xml**.

## <a name="test-the-user-flow"></a>Testa användarflödet

### <a name="test-account-creation-and-nevis-access-app-setup"></a>Testa att skapa konto och konfigurera Nevis Access-appen

1. Öppna [](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) Azure AD B2C klientorganisationshem.

2. Välj **Identity Experience Framework**.

3. Rulla ned till Anpassade principer och välj **B2C_1A_signup_signin**.

4. Välj **Kör nu.**

5. I popup-fönstret väljer du **Registrera dig nu.**

6. Lägg till din e-postadress.

7. Välj **Skicka verifieringskod**.

8. Kopiera verifieringskoden från e-postmeddelandet.

9. Välj **Verifiera**.

10. Fyll i formuläret med ditt nya lösenord och Visningsnamn.

11. Välj **Skapa**.

12. Du kommer till sidan för QR-kodgenomsökning.

13. Öppna **Nevis Access-appen på din telefon.**

14. Välj Face ID ( **Ansikts-ID).**

15. När skärmen visar **att Authenticator-registreringen lyckades** väljer du **Fortsätt.**

16. På telefonen autentiserar du med ansiktet igen.

17. Du kommer till den [](https://jwt.ms) jwt.ms landningssida som visar information om din avkodade token.

### <a name="test-the-pure-passwordless-sign-in"></a>Testa den rena lösenordslösa inloggningen

1. Under **Identity Experience Framework** väljer du **B2C_1A_signup_signin**.

2. Välj **Kör nu.**

3. I popup-fönstret väljer du **Lösenordsfri autentisering.**

4. Ange din e-postadress.

5. Välj **Fortsätt**.

6. I meddelanden på din telefon väljer du **Nevis Access-appmeddelande .**

7. Autentisera med ditt ansikte.

8. Du kommer automatiskt till den [jwt.ms](https://jwt.ms) som visar dina token.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar

- [Anpassade principer i Azure AD B2C](./custom-policy-overview.md)

- [Kom igång med anpassade principer i Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
