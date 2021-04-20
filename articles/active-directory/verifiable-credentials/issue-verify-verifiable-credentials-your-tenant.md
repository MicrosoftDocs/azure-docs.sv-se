---
title: Självstudie – Utfärda och verifiera verifierbara autentiseringsuppgifter med din Azure-klientorganisation (förhandsversion)
description: Ändra kodexempel för verifierbara autentiseringsuppgifter så att det fungerar med din Azure-klientorganisation
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 310c821bf102d267d0b5f77dbf206b896ab2f1c7
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739232"
---
# <a name="tutorial---issue-and-verify-verifiable-credentials-using-your-tenant-preview"></a>Självstudie – Utfärda och verifiera verifierbara autentiseringsuppgifter med din klientorganisation (förhandsversion)

> [!IMPORTANT]
> Azure Active Directory verifierbara autentiseringsuppgifter är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Nu när din Azure-klient har ställts in med tjänsten Verifiable Credential går vi igenom de steg som krävs för att göra det möjligt för din Azure Active Directory (Azure AD) att utfärda verifierbara autentiseringsuppgifter med hjälp av exempelappen.

I den här artikeln får du:

> [!div class="checklist"]
> * Registrera exempelappen i Azure AD
> * Skapa regler och visningsfil
> * Ladda upp regler och visa filer
> * Konfigurera tjänsten Utfärdare av verifierbara autentiseringsuppgifter att använda Azure Key Vault
> * Uppdatera exempelkod med din klients information.

Vår exempelkod kräver att användare autentiserar till en identitetsprovider, särskilt Azure AD B2C, innan VC:et för verifierade autentiseringsuppgifter kan utfärdas. Det är inte alla verifierbara utfärdare av autentiseringsuppgifter som kräver autentisering innan autentiseringsuppgifter utfärdas.

Autentisering av ID-token gör det möjligt för användare att bevisa vem de är innan de får sina autentiseringsuppgifter. När användarna har loggat in returnerar identitetsprovidern en säkerhetstoken som innehåller anspråk om användaren. Utfärdartjänsten omvandlar sedan dessa säkerhetstoken och deras anspråk till en verifierbar autentiseringsidentifiering. De verifierbara autentiseringssuppgifter signeras med utfärdarens DID.

Alla identitetsproviders som stöder OpenID Connect-protokollet stöds. Exempel på identitetsproviders [som stöds är Azure Active Directory](../fundamentals/active-directory-whatis.md)och [Azure AD B2C](../../active-directory-b2c/overview.md). I den här självstudien använder vi AAD.

## <a name="prerequisites"></a>Förutsättningar

Den här självstudien förutsätter att du redan har slutfört stegen i [föregående självstudie](enable-your-tenant-verifiable-credentials.md) och har åtkomst till den miljö som du använde.

## <a name="register-an-app-to-enable-did-wallets-to-sign-in-users"></a>Registrera en app för att aktivera DID-plånböcker för att logga in användare

Om du vill utfärda en verifierbar autentiseringsidentifiering måste du registrera en app så att Authenticator, eller andra verifierbara autentiseringsuppgifter, kan logga in användare.  

Registrera ett program med namnet "VC Wallet App" i Azure AD och hämta ett klient-ID.

1. Följ anvisningarna för att registrera ett program [med Azure AD](../develop/quickstart-register-app.md) Vid registrering använder du värdena nedan.

   - Namn: "VC Plånboksapp"
   - Kontotyper som stöds: Endast konton i den här organisationskatalogen
   - Omdirigerings-URI: vcclient://openid/

   ![registrera ett program](media/issue-verify-verifable-credentials-your-tenant/register-application.png)

2. När du har registrerat programmet skriver du ned program-ID:t (klienten). Du behöver det här värdet senare.

   ![programklient-ID](media/issue-verify-verifable-credentials-your-tenant/client-id.png)

3. Välj knappen **Slutpunkter** och kopiera den URI OpenID Connect metadatadokumentet. Du behöver den här informationen för nästa avsnitt. 

   ![issuer endpoints (utfärdarslutpunkter)](media/issue-verify-verifable-credentials-your-tenant/application-endpoints.png)

## <a name="set-up-your-node-app-with-access-to-azure-key-vault"></a>Konfigurera din nodapp med åtkomst till Azure Key Vault

För att autentisera en användares begäran om utfärdande av autentiseringsuppgifter använder utfärdarwebbplatsen dina kryptografiska nycklar i Azure Key Vault. För att Azure Key Vault åtkomst måste webbplatsen ha ett klient-ID och en klienthemlighet som kan användas för att autentisera Azure Key Vault.

1. När du visar översiktssidan för VC-plånboksappen **väljer du Certifikat & hemligheter**.
    ![certifikat och hemligheter](media/issue-verify-verifable-credentials-your-tenant/vc-wallet-app-certs-secrets.png)
1. I avsnittet **Klienthemligheter** väljer du **Ny klienthemlighet**
    1. Lägg till en beskrivning som "Node VC client secret" (Node VC-klienthemlighet)
    1. Upphör att gälla: om ett år.
  ![Programhemlighet med ett års förfallotid](media/issue-verify-verifable-credentials-your-tenant/add-client-secret.png)
1. Kopiera secret (hemlighet). Du behöver den här informationen för att uppdatera exempelnodappen.

>[!WARNING]
> Du har en möjlighet att kopiera hemligheten. Hemligheten är ett sätt att hash-koda efter detta. Kopiera inte ID:t. 

När du har skapat ditt program och din klienthemlighet i Azure AD måste du ge programmet de behörigheter som krävs för att utföra åtgärder på Key Vault. Dessa behörighetsändringar krävs för att webbplatsen ska kunna komma åt och använda de privata nycklar som lagras där.

1. Gå till Key Vault.
2. Välj det nyckelvalv som vi använder för de här självstudierna.
3. Välj **Åtkomstprinciper** i det vänstra navigeringsfältet
4. Välj **+Lägg till åtkomstprincip.**
5. I avsnittet **Nyckelbehörigheter** väljer **du Hämta** och **Signera**.
6. Välj **Huvudnamn** och använd program-ID:t för att söka efter det program som vi registrerade tidigare. Välj den.
7. Välj **Lägg till**.
8. Välj **SPARA.**

Mer information om hur Key Vault och åtkomstkontroll finns i [RBAC-guiden för nyckelvalv](../../key-vault/general/rbac-guide.md)

![tilldela behörigheter för nyckelvalv](media/issue-verify-verifable-credentials-your-tenant/key-vault-permissions.png)
## <a name="make-changes-to-match-your-environment"></a>Göra ändringar för att matcha din miljö

Hittills har vi arbetat med vår exempelapp. Appen använder [Azure Active Directory B2C](../../active-directory-b2c/overview.md) och vi byter nu till att använda Azure AD, så vi behöver göra vissa ändringar, inte bara för att matcha din miljö, utan även för att stödja ytterligare anspråk som inte har använts tidigare.

1. Kopiera regelfilen nedan och spara den för att **modified-expertRules.jspå**. 

    > [!NOTE]
    > **"scope": "openid profile"** ingår i den här regelfilen och inkluderades inte i exempelappens regelfil. I nästa avsnitt förklaras hur du aktiverar de valfria anspråken i din Azure Active Directory klientorganisation. 
    
    ```json
    {
      "attestations": {
        "idTokens": [
          {
            "mapping": {
              "firstName": { "claim": "given_name" },
              "lastName": { "claim": "family_name" }
            },
            "configuration": "https://dIdPlayground.b2clogin.com/dIdPlayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
            "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
            "redirect_uri": "vcclient://openid/",
             "scope": "openid profile"
          }
        ]
      },
      "validityInterval": 2592000,
      "vc": {
        "type": ["VerifiedCredentialExpert"]
      }
    }
    ```

2. Öppna filen och ersätt **client_id** och **konfigurationsvärdena** med de två värden som vi kopierade i föregående avsnitt.

    ![markera de två värden som behöver ändras som en del av det här steget](media/issue-verify-verifable-credentials-your-tenant/rules-file.png)

  Värdet Konfiguration **är** den URI OpenID Connect metadatadokumentet.

  Eftersom exempelkoden använder Azure Active Directory B2C och vi använder Azure Active Directory måste vi lägga till valfria anspråk via omfång för att dessa anspråk ska tas med i ID-token som ska skrivas till den verifierbara autentiseringskoden. 

3. Öppna Azure Portal i Azure Active Directory.
4. Välj **Appregistreringar**.
5. Öppna VC-plånboksappen som vi skapade tidigare.
6. Välj **Tokenkonfiguration.**
7. Välj **+ Lägg till valfritt anspråk**

     ![under tokenkonfiguration lägger du till ett valfritt anspråk](media/issue-verify-verifable-credentials-your-tenant/token-configuration.png)

8. Från **Tokentyp** väljer **du ID** och i listan över tillgängliga anspråk väljer du **given_name** och **family_name**

     ![lägga till valfria anspråk](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim.png)

9. Tryck på **Lägg till**.
10. Om du får en behörighetsvarning som visas nedan markerar du kryssrutan och väljer Lägg **till**.

     ![lägga till behörigheter för valfria anspråk](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim-permissions.png)

När en användare nu visas med "inloggningen" för att få din verifierbara autentiseringsbok vet VC Wallet-appen att den ska inkludera specifika anspråk via omfångsparametern som ska skrivas till den verifierbara autentiseringsboken.

## <a name="create-new-vc-with-this-rules-file-and-the-old-display-file"></a>Skapa ny VC med den här regelfilen och den gamla visningsfilen

1. Ladda upp den nya regelfilen till vår container
1. På sidan med verifierbara autentiseringsuppgifter skapar du en ny autentiseringsuppgifterna med namnet **modifiedCredentialExpert** med hjälp av den gamla visningsfilen och den nya **regelfilen**(modified-credentialExpert.jspå ).
1. När processen för att skapa autentiseringsuppgifter har slutförts från sidan Översikt kopierar du **URL:en** för autentiseringsuppgifter för problem och sparar den eftersom vi behöver den i nästa avsnitt. 

## <a name="before-we-continue"></a>Innan vi fortsätter

Vi måste sätta ihop några värden innan vi kan göra de nödvändiga kodändringarna. Vi använder dessa värden i nästa avsnitt för att göra så att exempelkoden använder dina egna nycklar som lagras i valvet. Hittills bör vi ha följande värden klara.

- **Kontrakt-URI** från de autentiseringsuppgifter som vi nyss skapade (Utfärda autentiserings-URL)
- **Klient-ID för program** Vi fick detta när vi registrerade Node-appen.
- **Klienthemlighet** Vi skapade detta tidigare när vi gav appen åtkomst till nyckelvalvet.

Det finns några andra värden som vi måste hämta innan vi kan göra ändringarna en gång i vår exempelapp. Nu ska vi hämta dem!

### <a name="verifiable-credentials-settings"></a>Inställningar för verifierbara autentiseringsuppgifter

1. Gå till sidan Kontrollerbara autentiseringsuppgifter och välj **Inställningar.**  
1. Kopiera följande värden:

    - Klientorganisations-ID 
    - Utfärdaridentifierare (din DID)
    - Nyckelvalv (URI)

1. Under Signeringsnyckel-ID finns det en URI, men vi behöver bara en del av den. Kopiera från delen **issuerSigningKeyION** som markerats av den röda rektangeln i bilden nedan.

   ![identifierare för inloggningsnyckel](media/issue-verify-verifable-credentials-your-tenant/issuer-signing-key-ion.png)

### <a name="did-document"></a>DID-dokument

1. Öppna [DIF-Nätverksutforskaren](https://identity.foundation/ion/explorer/)

2. Klistra in DID i sökfältet.

4. I det formaterade svaret hittar du avsnittet **verificationMethod**
5. Under "verificationMethod" kopierar du ID:t och märk det som kvSigningKeyId
    
    ```json=
    "verificationMethod": [
          {
            "id": "#sig_25e48331",
    ```

Nu har vi allt vi behöver för att göra ändringarna i vår exempelkod.

- **Utfärdare:** app.js uppdatera autentiseringsuppgifter med din nya kontrakt-URI
- **Verifierare:** app.js uppdatera issuerDid med din utfärdaridentifierare
- **Issuer och Verifier** uppdaterar didconfig.jspå med följande värden:


```json=
{
    "azTenantId": "Your tenant ID",
    "azClientId": "Your client ID",
    "azClientSecret": "Your client secret",
    "kvVaultUri": "your keyvault uri",
    "kvSigningKeyId": "The verificationMethod ID from your DID Document",
    "kvRemoteSigningKeyId" : "The snippet of the issuerSigningKeyION we copied ",
    "did": "Your DID"
}
```

>[!IMPORTANT]
>Det här är ett demoprogram och du bör normalt aldrig ge ditt program hemligheten direkt.


Nu har du allt på plats för att utfärda och verifiera dina egna verifierbara autentiseringsuppgifter från din Azure Active Directory klient med dina egna nycklar. 

## <a name="issue-and-verify-the-vc"></a>Utfärda och verifiera VC

Följ samma steg som vi följde i föregående självstudie för att utfärda verifierbara autentiseringsuppgifter och verifiera den med din app. När du har slutfört verifieringsprocessen är du nu redo att lära dig mer om verifierbara autentiseringsuppgifter.

1. Öppna en kommandotolk och öppna mappen issuer.
2. Kör den uppdaterade nodappen.

    ```terminal
    node app.js
    ```

3. Använd en annan kommandotolk och kör ngrok för att konfigurera en URL på 8081

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > Du kan också se en varning om att den här appen eller webbplatsen kan vara riskabel. Meddelandet förväntas just nu eftersom vi ännu inte har länkat din DID till din domän. Följ anvisningarna [för DNS-bindning](how-to-dnsbind.md) för att konfigurera detta.

    
4. Öppna DEN HTTPS-URL som genererats av ngrok.

    ![NGROK-slutpunkter för vidarebefordran](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

5. Välj **GET CREDENTIAL (HÄMTA AUTENTISERINGSUPPGIFTER)**
6. Sök igenom QR-koden i Authenticator.
7. På **Den här appen eller webbplatsen kan vara ett riskabelt** varningsmeddelande. Välj **Avancerat**.

  ![Första varningen](media/enable-your-tenant-verifiable-credentials/site-warning.png)

8. Vid varningen om riskfylld webbplats väljer du **Fortsätt ändå (osäker)**

  ![Andra varningen om utfärdaren](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


9. På skärmen **Lägg till en autentiseringsidentifiering** ser du några saker: 
    1. Längst upp på skärmen ser du ett rött meddelande som inte **har verifierats**
    1. Autentiseringssuppgifter anpassas baserat på de ändringar vi har gjort i visningsfilen.
    1. Alternativet **Logga in på ditt konto** pekar på din Azure AD-inloggningssida.
    
   ![skärmen lägg till autentiseringsuppgifter med varning](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

10. Välj **Logga in på ditt konto och** autentisera med en användare i din Azure AD-klientorganisation.
11. När du har autentiserat **knappen** Lägg till är den inte längre nedtonad. Välj **Lägg till.**

  ![skärmen lägg till autentiseringsuppgifter efter autentisering](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

Vi har nu utfärdat en verifierbar autentiseringsautentisering med din klient för att generera vc medan du fortfarande använder den ursprungliga B2C-klienten för autentisering.

  ![vc utfärdat av din azure AD och autentiserad av vår Azure B2C-instans](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>Testa att verifiera VC med hjälp av exempelappen

Nu när vi har utfärdat de verifierbara autentiseringssuppgifter från din egen klientorganisation med anspråk från din Azure AD ska vi verifiera den med hjälp av exempelappen.

1. Sluta köra utfärdarens ngrok-tjänst.

    ```cmd
    control-c
    ```

2. Kör nu ngrok med verifierarporten 8082.

    ```cmd
    ngrok http 8082
    ```

3. I ett annat terminalfönster navigerar du till verifierarappen och kör den på samma sätt som vi körde utfärdarappen.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

4. Öppna ngrok-URL:en i webbläsaren och skanna QR-koden med Authenticator på din mobila enhet.
5. På din mobila enhet väljer du **Tillåt** på skärmen **Ny behörighetsbegäran.**

   >[!IMPORTANT]
    > Eftersom exempelappen också använder din DID för att signera presentationsbegäran visas en varning om att den här appen eller webbplatsen kan vara riskabel. Meddelandet förväntas just nu eftersom vi ännu inte har länkat din DID till din domän. Följ anvisningarna [för DNS-bindning](how-to-dnsbind.md) för att konfigurera detta.
    
   ![ny behörighetsbegäran](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. Nu har du verifierat dina autentiseringsuppgifter och webbplatsen bör visa ditt för- och efternamn från Ditt Azure AD-användarkonto. 

Nu har du slutfört självstudien och är officiellt verifierad autentiseringsexpert! Exempelappen använder din DID för både utfärdande och verifiering, medan anspråk skrivs till en verifierbar autentiseringsidentifiering från Azure AD. 

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du skapar [anpassade autentiseringsuppgifter](credential-design.md)
- Exempel på utfärdare av [tjänstkommunikation](issuer-openid.md)
