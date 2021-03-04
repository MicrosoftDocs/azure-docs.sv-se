---
title: Självstudie – konfigurera Azure Active Directory B2C med Zscaler
titleSuffix: Azure AD B2C
description: Lär dig hur du integrerar Azure AD B2C-autentisering med Zscaler.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: dbeb3cd4fccf80f434e6c7ac08c658632f64b135
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096861"
---
# <a name="tutorial-configure-zscaler-private-access-with-azure-active-directory-b2c"></a>Självstudie: Konfigurera Zscaler privat åtkomst med Azure Active Directory B2C

I den här självstudien får du lära dig att integrera Azure Active Directory B2C (Azure AD B2C)-autentisering med [Zscaler Private Access (ZPA)](https://www.zscaler.com/products/zscaler-private-access). ZPA levererar principbaserad, säker åtkomst till privata program och till gångar utan kostnad, krångel eller säkerhets risker i ett virtuellt privat nätverk (VPN). Zscaler Secure hybrid Access-erbjudandet möjliggör en noll-attack-yta för klientbaserade program när den kombineras med Azure AD B2C.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar behöver du:

- En Azure-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).  
- [En Azure AD B2C klient](./tutorial-create-tenant.md) som är länkad till din Azure-prenumeration.  
- [En ZPA-prenumeration](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview).

## <a name="scenario-description"></a>Scenariobeskrivning

ZPA-integrering innehåller följande komponenter:

- **Azure AD B2C**: identitets leverantören (IdP) som ansvarar för att verifiera användarens autentiseringsuppgifter. Den ansvarar också för att registrera en ny användare.  
- **ZPA**: tjänsten som ansvarar för att skydda webb programmet genom [att tvinga åtkomst med noll-förtroende](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.).  
- **Webb programmet**: är värd för den tjänst som användaren försöker få åtkomst till.

Följande diagram visar hur ZPA integreras med Azure AD B2C.

![Diagram över Zscaler-arkitekturen, som visar hur ZPA integreras med Azure AD B2C.](media/partner-zscaler/zscaler-architecture-diagram.png)

Sekvensen beskrivs i följande tabell:

|Steg | Beskrivning |
| :-----:| :-----------|
| 1 | En användare kommer till en användar portal i ZPA eller ett ZPA program för webb läsar åtkomst.
| 2 | ZPA kräver användar kontext information innan den kan avgöra om användaren ska få åtkomst till webb programmet. För att autentisera användaren utför ZPA en SAML-omdirigering till sidan Azure AD B2C inloggning.  
| 3 | Användaren kommer till inloggnings sidan för Azure AB-B2C. Nya användare registrerar sig för att skapa ett konto och befintliga användare loggar in med sina befintliga autentiseringsuppgifter. Azure AD B2C verifierar användarens identitet.
| 4 | Vid lyckad autentisering dirigerar Azure AD B2C tillbaka användaren till ZPA tillsammans med SAML-kontrollen. ZPA verifierar SAML Assertion och anger användar kontexten.
| 5 | ZPA utvärderar åtkomst principer för användaren. Om användaren har behörighet att komma åt webb programmet, tillåts anslutningen att passera.

## <a name="onboard-to-zpa"></a>Publicera till ZPA

Den här självstudien förutsätter att du redan har en fungerande ZPA-installation. Om du kommer igång med ZPA, se [steg-för-steg-konfigurations guiden för ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa).

## <a name="integrate-zpa-with-azure-ad-b2c"></a>Integrera ZPA med Azure AD B2C

### <a name="step-1-configure-azure-ad-b2c-as-an-idp-on-zpa"></a>Steg 1: Konfigurera Azure AD B2C som en IdP på ZPA

Om du vill konfigurera Azure AD B2C som en [IDP på ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign)gör du följande:

1. Logga in på [administrations portalen för ZPA](https://admin.private.zscaler.com).

1. Gå till **Administration**  >  **IDP-konfiguration**.

1. Välj **Lägg till IDP-konfiguration**.

   Fönstret **Lägg till konfiguration av IDP** öppnas.

   ![Skärm bild av fliken "IdP information" i fönstret "Lägg till IdP-konfiguration".](media/partner-zscaler/add-idp-configuration.png)

1. Välj fliken **IDP information** och gör sedan följande:

   a. I rutan **namn** anger du **Azure AD B2C**.  
   b. Under **enkel inloggning** väljer du **användare**.  
   c. I list rutan **domäner** väljer du de autentiserings domäner som du vill associera med den här IDP.

1. Välj **Nästa**.

1. Välj fliken **SP-metadata** och gör sedan följande:

   a. Kopiera eller anteckna värdet för senare användning under **tjänst leverantörens URL**.  
   b. Under **tjänste leverantör entitets-ID** kopierar eller noterar du värdet för senare användning.

   ![Skärm bild av fliken "SP metadata" i fönstret "Lägg till IdP-konfiguration".](media/partner-zscaler/sp-metadata.png)

1. Välj **pausa**.

När du har konfigurerat Azure AD B2C återupptas resten av IdP-konfigurationen.

### <a name="step-2-configure-custom-policies-in-azure-ad-b2c"></a>Steg 2: Konfigurera anpassade principer i Azure AD B2C

>[!Note]
>Det här steget krävs bara om du inte redan har konfigurerat anpassade principer. Om du redan har en eller flera anpassade principer kan du hoppa över det här steget.

Information om hur du konfigurerar anpassade principer på Azure AD B2C klient organisationen finns i [komma igång med anpassade principer i Azure Active Directory B2C](./custom-policy-get-started.md).

### <a name="step-3-register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>Steg 3: registrera ZPA som ett SAML-program i Azure AD B2C

Information om hur du konfigurerar ett SAML-program i Azure AD B2C finns i [Registrera ett SAML-program i Azure AD B2C](./saml-service-provider.md). 

I steg ["Ladda upp din princip"](./saml-service-provider.md#upload-your-policy)kopierar eller noterar du IDP SAML metadata URL som används av Azure AD B2C. Du behöver det senare.

Följ anvisningarna i steg ["konfigurera programmet i Azure AD B2C"](./saml-service-provider.md#configure-your-application-in-azure-ad-b2c). I steg 4,2 uppdaterar du appens manifest egenskaper enligt följande:

- För **identifierUris**: Använd tjänste leverantörens ENTITETS-ID som du kopierade eller noterade tidigare i "steg 1.6. b".  
- För **samlMetadataUrl**: hoppa över den här egenskapen eftersom ZPA inte är värd för en SAML-metadata-URL.  
- För **replyUrlsWithType**: Använd tjänste leverantörens URL som du kopierade eller noterade tidigare i "steg 1.6. a".  
- För **logoutUrl**: hoppa över den här egenskapen eftersom ZPA inte har stöd för en utloggnings-URL.

Resten av stegen är inte relevanta för den här självstudien.

### <a name="step-4-extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>Steg 4: extrahera IdP SAML-metadata från Azure AD B2C

Sedan måste du hämta en URL för SAML-metadata i följande format:

```https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata```

Observera att `<tenant-name>` namnet på din Azure AD B2C klient och `<policy-name>` är namnet på den anpassade SAML-princip som du skapade i föregående steg.

URL-adressen kan till exempel vara `https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml//Samlp/metadata` .

Öppna en webbläsare och gå till URL: en för SAML-metadata. Högerklicka var som helst på sidan, Välj **Spara som** och spara sedan filen på din dator för användning i nästa steg.

### <a name="step-5-complete-the-idp-configuration-on-zpa"></a>Steg 5: Slutför IdP-konfigurationen på ZPA

Slutför [IDP-konfigurationen i administrations portalen för ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign) som du delvis konfigurerade tidigare i "steg 1: Konfigurera Azure AD B2C som en IDP på ZPA".

1. I [ZPA administrations Portal](https://admin.private.zscaler.com)går du till **Administration**  >  **IDP Configuration**.

1. Välj den IdP som du konfigurerade i "steg 1" och välj sedan **återuppta**.

1. I fönstret **Lägg till IDP-konfiguration** väljer du fliken **skapa IDP** och gör sedan följande:

   a. Under **IDP-metadatafil** laddar du upp den metadatafil som du sparade tidigare i "steg 4: EXTRAHERA IDP SAML-Metadata från Azure AD B2C".  
   b. Kontrol lera att **statusen** för IDP-konfigurationen är **aktive rad**.  
   c. Välj **Spara**.

   ![Skärm bild av fliken "skapa IdP" i fönstret "Lägg till IdP-konfiguration".](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>Testa lösningen

Gå till en användar Portal för ZPA eller ett program för webb läsar åtkomst och testa registrerings-eller inloggnings processen. Testet bör resultera i en lyckad SAML-autentisering.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- [Kom igång med anpassade principer i Azure AD B2C](./custom-policy-get-started.md)
- [Registrera ett SAML-program i Azure AD B2C](./saml-service-provider.md)
- [Steg-för-steg-konfigurations guide för ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)
- [Konfigurera en IdP för enkel inloggning](https://help.zscaler.com/zpa/configuring-idp-single-sign)