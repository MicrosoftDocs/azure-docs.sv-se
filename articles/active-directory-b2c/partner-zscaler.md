---
title: Självstudie för att konfigurera Azure Active Directory B2C med Zscaler
titleSuffix: Azure AD B2C
description: Lär dig hur du integrerar Azure AD B2C-autentisering med Zscaler
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 223f8acd6aad7aaf4c37e0b2eae2df882ed2ad1d
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629382"
---
# <a name="tutorial-to-configure-zscaler-private-access-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>Självstudie för att konfigurera Zscaler privat åtkomst med Azure Active Directory B2C för säker hybrid åtkomst

I den här självstudien lär du dig att integrera Azure AD B2C-autentisering med [Zscaler Private Access (ZPA)](https://www.zscaler.com/products/zscaler-private-access). ZPA levererar principbaserad, säker åtkomst till privata program och till gångar utan kostnad, krångel eller säkerhets risker i ett virtuellt privat nätverk (VPN). Zscaler är ett Secure hybrid Access-erbjudande som gör det möjligt att använda en tom attack yta för program som riktas mot konsumenter i kombination med Azure AD B2C.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du:

- En Azure-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).

- [En Azure AD B2C klient](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) som är länkad till din Azure-prenumeration.

- [ZPA-prenumeration](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview)

## <a name="scenario-description"></a>Scenariobeskrivning

ZPA-integreringen innehåller följande komponenter:

- Azure AD B2C-identitets leverantören (IdP) som ansvarar för att verifiera användarens autentiseringsuppgifter. Den ansvarar också för att registrera en ny användare.

- ZPA – tjänsten som ansvarar för att skydda webb programmet genom [att tvinga åtkomst från noll-Trust](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.).

- Webb program – är värd för tjänsten som användaren försöker få åtkomst till.

Följande diagram visar hur ZPA integreras med Azure AD B2C.

![Bild visar Zscaler-arkitektur diagrammet](media/partner-zscaler/zscaler-architecture-diagram.png)

|Steg | Beskrivning |
|:-----| :-----------|
| 1. | Användaren kommer till en användar portal i ZPA eller ett ZPA Browser-program.
| 2. | ZPA kräver användar kontext informationen innan den kan avgöra om användaren ska tillåtas att få åtkomst till webb programmet. För att autentisera användaren utför ZPA en SAML-omdirigering till sidan Azure AD B2C inloggning.  
| 3. | Användaren kommer till inloggnings sidan för Azure AB-B2C. Om det är en ny användare, registrerar användaren sig för att skapa ett nytt konto. En befintlig användare loggar in med sina befintliga autentiseringsuppgifter. Azure AD B2C verifierar användarens identitet.
| 4. | Vid lyckad autentisering dirigerar Azure AD B2C tillbaka användaren till ZPA tillsammans med SAML-kontrollen. ZPA verifierar SAML Assertion och anger användar kontexten.
| 5. | ZPA utvärderar åtkomst principer för användaren. Om användaren får åtkomst till webb programmet, tillåts anslutningen att passera.

## <a name="onboard-to-zpa"></a>Publicera till ZPA

Den här självstudien förutsätter att du redan har en fungerande konfiguration av ZPA. Om du kommer igång med ZPA läser du [steg-för-steg-konfigurations guide för ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa).

## <a name="integrate-with-azure-ad-b2c"></a>Integrera med Azure AD B2C

### <a name="part-1---configure-azure-ad-b2c-as-an-idp-on-zpa"></a>Del 1 – Konfigurera Azure AD B2C som en IdP på ZPA

Konfigurera Azure AD B2C som en [identitets leverantör (IdP) på ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign):

1. Logga in på [administrations portalen för ZPA](https://admin.private.zscaler.com)

2. Gå till **Administration**  >  **IDP-konfiguration**

3. Välj **Lägg till IDP-konfiguration**

4. Ange följande för **1 IDP-information**:

   a. **Namn**: Azure AD B2C

   b. **Enkel inloggning**: Välj användare

   c. **Domäner**: Välj de autentiserings domäner som du vill associera till den här IDP och välj sedan **Done**

   ![Bild visar IDP-information](media/partner-zscaler/add-idp-configuration.png)

5. Välj **Nästa**

6. För **2 SP-metadata**:

   a. Kopiera URL: en för tjänste leverantören och anteckna den för senare användning.

   b. Kopiera ID för tjänste leverantörens entitet och anteckna det för senare användning.

   ![Bild som visar information om SP-metadata](media/partner-zscaler/sp-metadata.png)

7. Välj **paus**

Resten av IdP-konfigurationen kommer att återupptas när du har konfigurerat Azure AD B2C.

### <a name="part-2---configure-custom-policy-in-azure-ad-b2c"></a>Del 2 – Konfigurera en anpassad princip i Azure AD B2C

>[!Note]
>Det här steget krävs bara om du inte redan har anpassade principer. Om du redan har en eller flera anpassade principer kan du hoppa över det här steget.

Artikeln [Kom igång med anpassade principer i Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started) ger instruktioner om hur du konfigurerar anpassade principer på din Azure AD B2C-klient.

### <a name="part-3---register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>Del 3 – registrera ZPA som ett SAML-program i Azure AD B2C

Artikeln [Registrera ett SAML-program i Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers) innehåller instruktioner för hur du konfigurerar ett SAML-program i Azure AD B2C. I [steg 3,2](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#32-upload-and-test-your-policy-metadata)kommer du att tillhandahålla IDP SAML metadata URL som används av Azure AD B2C. Du behöver detta för senare användning.

Följ anvisningarna i [steg 4,2](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#42-update-the-app-manifest). I steg 4,2 i instruktionen måste du uppdatera app-manifestet. Uppdatera egenskaperna enligt följande:

- **identifierUris**: Använd tjänste leverantörens ENTITETS-ID som anges i **del 1, steg 6a**.

- **samlMetadataUrl**: hoppa över den här egenskapen eftersom ZPA inte är värd för en SAML-metadata-URL.

- **replyUrlsWithType**: Använd URL: en för tjänst leverantören som anges i **del 1, steg 6b**.

- **logoutUrl**: hoppa över den här egenskapen eftersom ZPA inte har stöd för en utloggnings-URL.

Resten av stegen är inte relevanta för den här självstudien.

### <a name="part-4---extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>Del 4 – extrahera IdP SAML-metadata från Azure AD B2C

I föregående steg måste du hämta en URL för SAML-metadata i följande format:

```https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata```

där `<tenant-name>` är namnet på din Azure AD B2C klient och `<policy-name>` är namnet på den anpassade SAML-princip som du skapade i det senaste steget.

Till exempel " https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml//Samlp/metadata "

Öppna en webbläsare och gå till URL: en för SAML-metadata. När sidan läses in högerklickar du var som helst på sidan. Välj **Spara sida som** och spara filen på din dator. du kommer att använda det i nästa del.

### <a name="part-5---complete-idp-configuration-on-zpa"></a>Del 5 – fullständig IdP-konfiguration på ZPA

Slutför [IDP-konfigurationen i administrations portalen för ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign) som delvis har kon figurer ATS i del 1:

1. Logga in på [administrations portalen för ZPA](https://admin.private.zscaler.com)

2. Gå till **Administration**  >  **IDP-konfiguration**.

3. Välj den IdP som kon figurer ATS i del 1 och välj **återuppta**.

4. För **3 skapa IDP**

   a. Gå till **IDP-metadatafil**  >  **väljer du fil** och laddar upp den metadatafil som du sparade i del 4.

   b. Kontrol lera att **statusen** för IDP-konfigurationen är **aktive rad**.

   c. Välj **Spara**.

      ![Bild som visar information om att skapa IDP](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>Testa lösningen

Gå till en användar portal i ZPA eller ett program för webb läsar åtkomst och testa registrerings-eller inloggnings processen. Det bör resultera i en lyckad SAML-autentisering.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- [Kom igång med anpassade principer i Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)

- [Registrera ett SAML-program i Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers)

- [Steg-för-steg-konfigurations guide för ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)

- [Konfigurera en IdP för enkel inloggning](https://help.zscaler.com/zpa/configuring-idp-single-sign)
