---
title: Kryptering med SAML-token i Azure Active Directory
description: Lär dig hur du konfigurerar Azure Active Directory SAML-token-kryptering.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32a6034f407bd81e5d2d2ab8227d08a67f034d4c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258650"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption"></a>Gör så här: Konfigurera Azure AD SAML-token-kryptering

> [!NOTE]
> Token Encryption är en Azure Active Directory (Azure AD) Premium-funktion. Mer information om Azure AD-utgåvor, funktioner och priser finns i [priser för Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

Kryptering med SAML-token möjliggör användning av krypterade SAML-kontroller med ett program som stöder det. När Azure AD har kon figurer ATS för ett program krypteras den SAML-kontroll som den genererar för programmet med hjälp av den offentliga nyckeln som hämtats från ett certifikat som lagras i Azure AD. Programmet måste använda den matchande privata nyckeln för att dekryptera token innan den kan användas som bevis på autentisering för den inloggade användaren.

Kryptering av SAML-kontroller mellan Azure AD och programmet ger ytterligare garantier för att innehållet i token inte kan fångas upp och personliga eller företags data komprometteras.

Även om du inte har kryptering med token, skickas inte Azure AD SAML-tokens i nätverket i klartext. Azure AD kräver att en token-begäran/svar-utbyte sker över krypterade HTTPS/TLS-kanaler så att kommunikation mellan IDP, webbläsare och program sker över krypterade länkar. Överväg värdet för token-kryptering för din situation jämfört med att hantera ytterligare certifikat.   

Om du vill konfigurera token-kryptering måste du ladda upp en X. 509-certifikat fil som innehåller den offentliga nyckeln till det Azure AD-programobjekt som representerar programmet. För att hämta X. 509-certifikat kan du ladda ned det från själva programmet eller hämta det från program leverantören i de fall där program leverantören tillhandahåller krypterings nycklar eller om programmet förväntar dig att tillhandahålla en privat nyckel kan den skapas med kryptografi verktyg, den privata nyckel delen som laddats upp till programmets nyckel lager och det matchande offentliga nyckel certifikat som har överförts till Azure AD.

Azure AD använder AES-256 för att kryptera SAML Assertion-data.

## <a name="configure-saml-token-encryption"></a>Konfigurera SAML-tokenkryptering

Följ dessa steg om du vill konfigurera kryptering av SAML-token:

1. Hämta ett certifikat för en offentlig nyckel som matchar en privat nyckel som har kon figurer ATS i programmet.

    Skapa ett asymmetriskt nyckel par som ska användas för kryptering. Eller, om programmet tillhandahåller en offentlig nyckel som ska användas för kryptering, följer du programmets instruktioner för att ladda ned X. 509-certifikatet.

    Den offentliga nyckeln ska lagras i en X. 509-certifikat fil i. cer-format.

    Om programmet använder en nyckel som du skapar för din instans följer du anvisningarna i programmet för att installera den privata nyckel som programmet ska använda för att dekryptera tokens från din Azure AD-klient.

1. Lägg till certifikatet i program konfigurationen i Azure AD.

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>Konfigurera token-kryptering i Azure Portal

Du kan lägga till det offentliga certifikatet i program konfigurationen i Azure Portal.

1. Gå till [Azure-portalen](https://portal.azure.com).

1. Gå till bladet **Azure Active Directory > företags program** och välj sedan det program som du vill konfigurera token-kryptering för.

1. På programmets sida väljer du **token-kryptering**.

    ![Alternativet för token-kryptering i Azure Portal](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > Alternativet för **token-kryptering** är bara tillgängligt för SAML-program som har kon figurer ATS från bladet **företags program** i Azure Portal, antingen från program galleriet eller en app som inte är en Galleri. Det här meny alternativet är inaktiverat för andra program. För program som registrerats via **Appregistreringars** upplevelse i Azure Portal kan du konfigurera kryptering för SAML-token med hjälp av applikations manifestet, via Microsoft Graph eller via PowerShell.

1. På sidan **token-kryptering** väljer du **Importera certifikat** för att importera. CER-filen som innehåller ditt offentliga X. 509-certifikat.

    ![Importera. CER-filen som innehåller X. 509-certifikatet](./media/howto-saml-token-encryption/import-certificate-small.png)

1. När certifikatet har importer ATS och den privata nyckeln har kon figurer ATS för användning på program Sidan aktiverar du kryptering genom att välja **...** bredvid tumavtrycket och väljer sedan **Aktivera token-kryptering** från alternativen i list menyn.

1. Välj **Ja** för att bekräfta aktiveringen av token Encryption-certifikatet.

1. Bekräfta att SAML-kontroller som har avsänts för programmet är krypterade.

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>Inaktivera token-kryptering i Azure Portal

1. I Azure Portal går du till **Azure Active Directory > företags program** och väljer sedan det program som har SAML token Encryption aktiverat.

1. På sidan program väljer du **token-kryptering**, letar reda på certifikatet och väljer sedan alternativet **...** för att Visa List menyn.

1. Välj **inaktivera token-kryptering**.

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>Konfigurera SAML-token-kryptering med hjälp av Graph API, PowerShell eller app manifest

Krypterings certifikat lagras på programobjektet i Azure AD med en `encrypt` användnings tagg. Du kan konfigurera flera krypterings certifikat och det som är aktivt för kryptering av tokens identifieras av- `tokenEncryptionKeyID` attributet.

Du behöver programmets objekt-ID för att konfigurera token-kryptering med Microsoft Graph API eller PowerShell. Du kan hitta det här värdet program mässigt eller genom att gå till programmets **egenskaps** sida i Azure Portal och notera värdet för **objekt-ID** .

När du konfigurerar en inloggnings information med Graph, PowerShell eller i program manifestet, bör du skapa ett GUID som ska användas för keyId.

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>Konfigurera token-kryptering med Microsoft Graph

1. Uppdatera appen `keyCredentials` med ett X. 509-certifikat för kryptering. I följande exempel visas hur du gör detta.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid>

    { 
       "keyCredentials":[ 
          { 
             "type":"AsymmetricX509Cert","usage":"Encrypt",
             "keyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35",    (Use a GUID generator to obtain a value for the keyId)
             "key": "MIICADCCAW2gAwIBAgIQ5j9/b+n2Q4pDvQUCcy3…"  (Base64Encoded .cer file)
          }
        ]
    }
    ```

1. Identifiera det krypterings certifikat som är aktivt för kryptering av tokens. I följande exempel visas hur du gör detta.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid> 

    { 
       "tokenEncryptionKeyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35" (The keyId of the keyCredentials entry to use)
    }
    ```

### <a name="to-configure-token-encryption-using-powershell"></a>Konfigurera token-kryptering med PowerShell

1. Använd den senaste Azure AD PowerShell-modulen för att ansluta till din klient organisation.

1. Ange krypterings inställningarna för token med kommandot **[set-AzureApplication](/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview)** .

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials "<KeyCredentialsObject>"  -TokenEncryptionKeyId <keyID>
    ```

1. Läs inställningarna för token-kryptering med följande kommandon.

    ```powershell
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>Konfigurera token-kryptering med hjälp av applikations manifestet

1. Från Azure Portal går du till **Azure Active Directory > Appregistreringar**.

1. Välj **alla appar** i list rutan för att visa alla appar och välj sedan det företags program som du vill konfigurera.

1. På sidan program väljer du **manifest** för att redigera [applikations manifestet](../develop/reference-app-manifest.md).

1. Ange värdet för `tokenEncryptionKeyId` attributet.

    I följande exempel visas ett program manifest som kon figurer ATS med två krypterings certifikat och med det andra som är markerat som det aktiva med hjälp av tokenEnryptionKeyId.

    ```json
    { 
      "id": "3cca40e2-367e-45a5-8440-ed94edd6cc35",
      "accessTokenAcceptedVersion": null,
      "allowPublicClient": false,
      "appId": "cb2df8fb-63c4-4c35-bba5-3d659dd81bf1",
      "appRoles": [],
      "oauth2AllowUrlPathMatching": false,
      "createdDateTime": "2017-12-15T02:10:56Z",
      "groupMembershipClaims": "SecurityGroup",
      "informationalUrls": { 
         "termsOfService": null, 
         "support": null, 
         "privacy": null, 
         "marketing": null 
      },
      "identifierUris": [ 
        "https://testapp"
      ],
      "keyCredentials": [ 
        { 
          "customKeyIdentifier": "Tog/O1Hv1LtdsbPU5nPphbMduD=", 
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "8be4cb65-59d9-404a-a6f5-3d3fb4030351", 
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest" 
        }, 
        {
          "customKeyIdentifier": "U5nPphbMduDmr3c9Q3p0msqp6eEI=",
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851",
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest2" 
        } 
      ], 
      "knownClientApplications": [], 
      "logoUrl": null, 
      "logoutUrl": null, 
      "name": "Test SAML Application", 
      "oauth2AllowIdTokenImplicitFlow": true, 
      "oauth2AllowImplicitFlow": false, 
      "oauth2Permissions": [], 
      "oauth2RequirePostResponse": false, 
      "orgRestrictions": [], 
      "parentalControlSettings": { 
         "countriesBlockedForMinors": [], 
         "legalAgeGroupRule": "Allow" 
        }, 
      "passwordCredentials": [], 
      "preAuthorizedApplications": [], 
      "publisherDomain": null, 
      "replyUrlsWithType": [], 
      "requiredResourceAccess": [], 
      "samlMetadataUrl": null, 
      "signInUrl": "https://127.0.0.1:444/applications/default.aspx?metadata=customappsso|ISV9.1|primary|z" 
      "signInAudience": "AzureADMyOrg",
      "tags": [], 
      "tokenEncryptionKeyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851" 
    }  
    ```

## <a name="next-steps"></a>Nästa steg

* Ta reda [på hur Azure AD använder SAML-protokollet](../develop/active-directory-saml-protocol-reference.md)
* Lär dig formatet, säkerhets egenskaperna och innehållet i [SAML-token i Azure AD](../develop/reference-saml-tokens.md)