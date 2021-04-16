---
title: SAML-tokenkryptering i Azure Active Directory
description: Lär dig hur du konfigurerar Azure Active Directory SAML-tokenkryptering.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: iangithinji
ms.reviewer: paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c06a499cccb03e6726ee19542d7eb79e0c99b43
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375737"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption"></a>Gör så här: Konfigurera Azure AD SAML-tokenkryptering

> [!NOTE]
> Tokenkryptering är en premium Azure Active Directory funktion (Azure AD). Mer information om Azure AD-utgåvor, -funktioner och -priser finns i [Priser för Azure AD.](https://azure.microsoft.com/pricing/details/active-directory/)

MED SAML-tokenkryptering kan du använda krypterade SAML-kontroller med ett program som stöder det. När Azure AD har konfigurerats för ett program krypterar det SAML-intyg som det skickar för programmet med hjälp av den offentliga nyckeln som hämtas från ett certifikat som lagras i Azure AD. Programmet måste använda den matchande privata nyckeln för att dekryptera token innan det kan användas som bevis på autentisering för den inloggade användaren.

Kryptering av SAML-försäkran mellan Azure AD och programmet ger ytterligare garantier för att innehållet i token inte kan fångas upp och att personliga data eller företagsdata komprometteras.

Även utan tokenkryptering skickas Azure AD SAML-token aldrig i nätverket i klartexten. Azure AD kräver att tokenbegäran/svar-utbyten sker via krypterade HTTPS/TLS-kanaler så att kommunikationen mellan IDP, webbläsaren och programmet sker via krypterade länkar. Överväg värdet av tokenkryptering för din situation jämfört med omkostnaderna för att hantera ytterligare certifikat.   

För att konfigurera tokenkryptering måste du ladda upp en X.509-certifikatfil som innehåller den offentliga nyckeln till Det Azure AD-programobjekt som representerar programmet. Om du vill hämta X.509-certifikatet kan du ladda ned det från själva programmet eller hämta det från programleverantören i de fall där programleverantören tillhandahåller krypteringsnycklar eller om programmet förväntar sig att du anger en privat nyckel, kan det skapas med hjälp av kryptografiverktyg, den privata nyckeldel som laddas upp till programmets nyckelarkiv och det matchande offentliga nyckelcertifikatet som laddas upp till Azure AD.

Azure AD använder AES-256 för att kryptera SAML-försäkrans data.

## <a name="configure-saml-token-encryption"></a>Konfigurera SAML-tokenkryptering

Följ dessa steg om du vill konfigurera SAML-tokenkryptering:

1. Hämta ett certifikat för offentlig nyckel som matchar en privat nyckel som har konfigurerats i programmet.

    Skapa ett asymmetriskt nyckelpar som ska användas för kryptering. Om programmet tillhandahåller en offentlig nyckel som ska användas för kryptering följer du programmets instruktioner för att ladda ned X.509-certifikatet.

    Den offentliga nyckeln ska lagras i en X.509-certifikatfil i .cer-format.

    Om programmet använder en nyckel som du skapar för din instans följer du anvisningarna i programmet för att installera den privata nyckeln som programmet använder för att dekryptera token från Din Azure AD-klientorganisation.

1. Lägg till certifikatet i programkonfigurationen i Azure AD.

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>Så här konfigurerar du tokenkryptering i Azure Portal

Du kan lägga till det offentliga certifikatet i programkonfigurationen i Azure Portal.

1. Gå till [Azure-portalen](https://portal.azure.com).

1. Gå till **bladet Azure Active Directory > Företagsprogram** och välj sedan det program som du vill konfigurera tokenkryptering för.

1. På programmets sida väljer du **Tokenkryptering.**

    ![Alternativet Tokenkryptering i Azure Portal](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > Alternativet **Tokenkryptering** är endast tillgängligt för SAML-program  som har ställts in från bladet Företagsprogram i Azure Portal, antingen från programgalleriet eller en app som inte är en galleriapp. Det här menyalternativet är inaktiverat för andra program. För program som registrerats **via Appregistreringar-upplevelsen** i Azure Portal kan du konfigurera kryptering för SAML-token med hjälp av programmanifestet, via Microsoft Graph eller via PowerShell.

1. På sidan **Tokenkryptering** väljer du **Importera certifikat för** att importera CER-filen som innehåller ditt offentliga X.509-certifikat.

    ![Importera CER-filen som innehåller X.509-certifikatet](./media/howto-saml-token-encryption/import-certificate-small.png)

1. När certifikatet har importerats och den privata nyckeln har konfigurerats för användning på programsidan aktiverar du kryptering genom att välja **...** bredvid tumavtrycksstatus och väljer sedan **Aktivera tokenkryptering** från alternativen i listrutan.

1. Välj **Ja för** att bekräfta aktiveringen av tokenkrypteringscertifikatet.

1. Bekräfta att DE SAML-intyg som genereras för programmet är krypterade.

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>Så här inaktiverar du tokenkryptering i Azure Portal

1. I Azure Portal du till Azure Active Directory > **Företagsprogram** och väljer sedan det program som har SAML-tokenkryptering aktiverat.

1. På programmets sida väljer du **Tokenkryptering,** hittar certifikatet och väljer sedan **alternativet ...** för att visa den nedrullningsbara menyn.

1. Välj **Inaktivera tokenkryptering.**

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>Konfigurera SAML-tokenkryptering med Graph API, PowerShell eller appmanifest

Krypteringscertifikat lagras på programobjektet i Azure AD med en `encrypt` användningstagg. Du kan konfigurera flera krypteringscertifikat och det som är aktivt för att kryptera token identifieras av `tokenEncryptionKeyID` attributet .

Du behöver programmets objekt-ID för att konfigurera tokenkryptering med hjälp Microsoft Graph API eller PowerShell. Du hittar det här värdet programmatiskt eller genom  att gå till programmets egenskapssida i Azure Portal och lägga till värdet **objekt-ID.**

När du konfigurerar en keyCredential med Graph, PowerShell eller i programmanifestet bör du generera ett GUID som ska användas för keyId.

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>Så här konfigurerar du tokenkryptering med Microsoft Graph

1. Uppdatera programmets med `keyCredentials` ett X.509-certifikat för kryptering. I följande exempel visas hur du gör detta.

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

1. Identifiera krypteringscertifikatet som är aktivt för kryptering av token. I följande exempel visas hur du gör detta.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid> 

    { 
       "tokenEncryptionKeyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35" (The keyId of the keyCredentials entry to use)
    }
    ```

### <a name="to-configure-token-encryption-using-powershell"></a>Så här konfigurerar du tokenkryptering med PowerShell

1. Använd den senaste Azure AD PowerShell-modulen för att ansluta till din klientorganisation.

1. Ange inställningarna för tokenkryptering med kommandot **[Set-AzureApplication.](/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview&preserve-view=true)**

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials "<KeyCredentialsObject>"  -TokenEncryptionKeyId <keyID>
    ```

1. Läs inställningarna för tokenkryptering med hjälp av följande kommandon.

    ```powershell
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>Så här konfigurerar du tokenkryptering med hjälp av programmanifestet

1. Från Azure Portal går du till **Azure Active Directory > Appregistreringar**.

1. Välj **Alla appar** i listrutan för att visa alla appar och välj sedan det företagsprogram som du vill konfigurera.

1. På programmets sida väljer du Manifest **för att** redigera [programmanifestet](../develop/reference-app-manifest.md).

1. Ange värdet för `tokenEncryptionKeyId` attributet .

    I följande exempel visas ett programmanifest som konfigurerats med två krypteringscertifikat och med det andra valt som aktivt med hjälp av tokenEnryptionKeyId.

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
* Lär dig format, säkerhetsegenskaper och innehåll för [SAML-token i Azure AD](../develop/reference-saml-tokens.md)