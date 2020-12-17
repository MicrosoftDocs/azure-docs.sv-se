---
title: Självstudie för att konfigurera Azure Active Directory B2C med Nevis
titleSuffix: Azure AD B2C
description: Lär dig hur du integrerar Azure AD B2C-autentisering med Nevis för lösen ords lös autentisering
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/23/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 19aa847ce7ce1a6ba727a4733aefcdfad845e8c6
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629399"
---
# <a name="tutorial-to-configure-nevis-with-azure-active-directory-b2c-for-passwordless-authentication"></a>Självstudie för att konfigurera Nevis med Azure Active Directory B2C för lösen ords lös autentisering

I den här självstudien får du lära dig hur du kan utöka Azure AD B2C med  [Nevis](https://www.nevis.net/solution/authentication-cloud) för att aktivera lösen ords lös autentisering. Nevis tillhandahåller en mobil-och helt anpassad slut användar upplevelse med Nevis-appen för att ge stark kundautentisering och överensstämmer med PSD2-transaktions kraven (Payment Services direktiv 2).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du:

- Ett [testprov konto](https://www.nevis-security.com/aadb2c/) för Nevis

- En Azure AD-prenumeration. Om du inte har något kan du skaffa ett [kostnads fritt konto](https://azure.microsoft.com/free/).

- En [Azure AD B2C klient](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) som är länkad till din Azure-prenumeration.

- Konfigurerat Azure AD B2Cs miljö för att använda [anpassade principer](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started), om du vill integrera Nevis i ditt registrerings princip flöde.

## <a name="scenario-description"></a>Scenariobeskrivning

I det här scenariot lägger du till appen med helt anpassad åtkomst till ditt Server dels program för lösen ords lös autentisering. Följande komponenter utgör lösningen:

- En Azure AD B2C klient, med en kombinerad inloggnings-och registrerings princip till Server delen
- Nevis-instansen och dess REST API för att förbättra Azure AD B2C
- Din egen anpassade Access-app

I diagrammet visas implementeringen.

![Inloggnings flöde för lösen ord på hög nivå med Azure AD B2C och Nevis](./media/partner-nevis/nevis-architecture-diagram.png)

|Steg | Beskrivning |
|:-----| :-----------|
| 1. | En användare försöker logga in eller registrera sig för ett program via Azure AD B2C inloggnings-och registrerings princip.
| 2. | Under registreringen registreras Nevis-appen till användar enheten med hjälp av en QR-kod. En privat nyckel genereras på användarens enhet och används för att signera användar förfrågningar.
| 3. |  Azure AD B2C använder en RESTful teknisk profil för att starta inloggningen med Nevis-lösningen.
| 4. | Inloggningsbegäran skickas till Access-appen, antingen som ett push-meddelande, QR-kod eller som en djup länk.
| 5. | Användaren godkänner inloggnings försöket med sina biometrik-enheter. Ett meddelande returneras sedan till Nevis, som verifierar inloggningen med den lagrade offentliga nyckeln.
| 6. | Azure AD B2C skickar en sista begäran till Nevis för att bekräfta att inloggningen har slutförts.
| 7. |Baserat på meddelande om lyckade/misslyckade försök från Azure AD B2C användare beviljas/nekas åtkomst till programmet.

## <a name="integrate-your-azure-ad-b2c-tenant"></a>Integrera din Azure AD B2C-klient

### <a name="onboard-to-nevis"></a>Publicera till Nevis 

[Registrera dig för ett Nevis-konto](https://www.nevis-security.com/aadb2c/).
Du får två e-postmeddelanden:

1. Ett hanterings konto meddelande

2. En inbjudan till mobilappen.

### <a name="add-your-azure-ad-b2c-tenant-to-your-nevis-account"></a>Lägg till din Azure AD B2C-klient till ditt Nevis-konto

1. Från e-postmeddelandet för Nevis Management Account, kopierar du hanterings nyckeln till Urklipp.

2. Öppna https://console.nevis.cloud/ i en webbläsare.

3. Logga in i hanterings konsolen med din nyckel.

4. Välj **Lägg till instans**

5. Välj den nyss skapade instansen för att öppna den.

6. I navigerings fältet på sidan väljer du **anpassade integreringar**

7. Välj **Lägg till anpassad integrering**.

8. Ange Azure AD B2C klient namn för integrations namn.

9. För URL/domän anger du `https://yourtenant.onmicrosoft.com`

10. Välj **Nästa**

>[!NOTE]
>Du behöver åtkomsttoken för Nevis senare.

11. Välj **Klar**.

### <a name="install-the-nevis-access-app-on-your-phone"></a>Installera Nevis-åtkomst-appen på din telefon

1. Från e-postmeddelandet om Nevis-mobilapp öppnar du inbjudan för **testflyg-appen** .

2. Installera appen.

3. Följ anvisningarna för att installera programmet Nevis-åtkomst.

### <a name="integrate-azure-ad-b2c-with-nevis"></a>Integrera Azure AD B2C med Nevis

1. Öppna [Azure-portalen](https://portal.azure.com/).

2. Växla till Azure AD B2C klient. Kontrol lera att du har valt rätt klient organisation, eftersom den Azure AD B2C klienten vanligt vis finns i en separat klient.

3. I menyn väljer du **Identity Experience Framework (IEF)**

4. Välj **princip nycklar**

5. Välj **Lägg till** och skapa en ny nyckel med följande inställningar:

      a. Välj **manuella** i alternativ

      b. Ange namnet till **AuthCloudAccessToken**

      c. Klistra in den tidigare sparade **Nevis** -åtkomsttoken i fältet Hemlig

      d. För nyckel användningen väljer du **kryptering**

      e. Välj **Skapa**

### <a name="configure-and-upload-the-nevishtml-to-azure-blob-storage"></a>Konfigurera och ladda upp nevis.html till Azure Blob Storage

1. Gå till mappen [**princip**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) i din identitets miljö (IDE).

2. Öppna filen  [**nevis.html**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/nevis.html) .

3. Ersätt  **authentication_cloud_url** med URL: en för din Nevis-administratörskonsolen – `https://<instance_id>.mauth.nevis.cloud` .

4. **Spara** ändringarna i filen.

5. Följ [anvisningarna](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-with-html#2-create-an-azure-blob-storage-account) och ladda upp **nevis.html** -filen till Azure Blob Storage.

6. Följ [instruktionerna](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-with-html#3-configure-cors) och aktivera resurs delning mellan ursprung (CORS) för den här filen.

7. När uppladdningen är klar och CORS har Aktiver ATS, väljer du **nevis.html** -filen i listan.

8. Välj ikonen **Kopiera länk** bredvid **URL: en** på fliken **Översikt** .

9. Öppna länken i en ny flik i webbläsaren för att se till att den visar en grå ruta.

>[!NOTE]
>Du behöver BLOB-länken senare.

### <a name="customize-your-trustframeworkbasexml"></a>Anpassa din TrustFrameworkBase.xml

1. I din IDE går du till mappen [**princip**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) .

2. Öppna [**TrustFrameworkBase.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkBase.xml) -filen.

3. Ersätt **yourtenant** med namnet på ditt Azure-klient konto i **TenantId**.

4. Ersätt **yourtenant** med namnet på ditt Azure-klient konto i **PublicPolicyURI**.

5. Ersätt alla **authentication_cloud_url** -instanser med URL: en för din Nevis-administratörskonsolen

6. **Spara** ändringarna i filen.

### <a name="customize-your-trustframeworkextensionsxml"></a>Anpassa din TrustFrameworkExtensions.xml

1. I din IDE går du till mappen [**princip**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) .

2. Öppna [**TrustFrameworkExtensions.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkExtensions.xml) -filen.

3. Ersätt **yourtenant** med namnet på ditt Azure-klient konto i **TenantId**.

4. Ersätt **yourtenant** med namnet på ditt Azure-klient konto i **PublicPolicyURI**.

5. Under **BasePolicy**, i **TenantId**, ersätter du även _Yourtenant_ med namnet på ditt Azure-klient konto.

6. Under **BuildingBlocks** ersätter du **LoadUri** med BLOB-länkens URL för din _nevis.html_ i ditt Blob Storage-konto.

7. **Spara** filen.

### <a name="customize-your-signuporsigninxml"></a>Anpassa din SignUpOrSignin.xml

1. I din IDE går du till mappen [**princip**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) .

2. Öppna [**SignUpOrSignin.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/SignUpOrSignin.xml) -filen.

3. Ersätt **yourtenant** med namnet på ditt Azure-klient konto i **TenantId**.

4. Ersätt **yourtenant** med namnet på ditt Azure-klient konto i **PublicPolicyUri**.

5. Under **BasePolicy**, i **TenantId**, ersätter du även **yourtenant** med ditt namn på ditt Azure-klient konto.

6. **Spara** filen.

### <a name="upload-your-custom-policies-to-azure-ad-b2c"></a>Överför dina anpassade principer till Azure AD B2C

1. Öppna [Azure AD B2C klientens](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) start sida.

2. Välj **ramverk för identitets upplevelse**.

3. Välj **överför anpassad princip**.

4. Välj den **TrustFrameworkBase.xml** -fil som du ändrade.

5. Markera kryss rutan **Skriv över den anpassade principen om den redan finns** .
6. Välj **Överför**.

7. Upprepa steg 5 och 6 för **TrustFrameworkExtensions.xml**.

8. Upprepa steg 5 och 6 för **SignUpOrSignin.xml**.

## <a name="test-the-user-flow"></a>Testa användar flödet

### <a name="test-account-creation-and-nevis-access-app-setup"></a>Installations program för skapande av test konton och Nevis

1. Öppna [Azure AD B2C klientens](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) start sida.

2. Välj **ramverk för identitets upplevelse**.

3. Rulla ned till anpassade principer och välj **B2C_1A_signup_signin**.

4. Välj **Kör nu**.

5. I popup-fönstret väljer du **Registrera dig nu**.

6. Lägg till din e-postadress.

7. Välj **Skicka verifierings kod**.

8. Kopiera över verifierings koden från e-postmeddelandet.

9. Välj **Verifiera**.

10. Fyll i formuläret med ditt nya lösen ord och visnings namn.

11. Välj **Skapa**.

12. Du kommer till genomsöknings sidan för QR-kod.

13. Öppna **appen Nevis-åtkomst** på din telefon.

14. Välj **ansikts-ID**.

15. När skärmen säger att **verifiering av autentiseraren har slutförts** väljer du **Fortsätt**.

16. På din telefon kan du autentisera med ditt ansikte igen.

17. Du kommer till [JWT.MS](https://jwt.ms) -landnings sidan som visar din avkodade information om token.

### <a name="test-the-pure-passwordless-sign-in"></a>Testa den rena inloggningen för lösen ord

1. Under **identifierare för identitets miljö** väljer du **B2C_1A_signup_signin**.

2. Välj **Kör nu**.

3. I popup-fönstret väljer du **lösenordsbaserad autentisering**.

4. Ange din e-postadress.

5. Välj **Fortsätt**.

6. På din telefon, i aviseringar, väljer du **meddelande om Nevis Access-appen**.

7. Autentisera med ditt ansikte.

8. Du kommer automatiskt till [JWT.MS](https://jwt.ms) landnings sida som visar dina tokens.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar

- [Anpassade principer i Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Kom igång med anpassade principer i Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
