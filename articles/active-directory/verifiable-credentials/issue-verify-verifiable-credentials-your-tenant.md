---
title: Självstudie – utfärda och verifiera verifierbara autentiseringsuppgifter med din klient (för hands version)
description: Ändra kod exemplet för verifierbara autentiseringsuppgifter så att det fungerar med din Azure-klient
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 95d3ab19565ed04d9bf7d59ba7262d40b4971d34
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106170183"
---
# <a name="tutorial-issue-and-verify-verifiable-credentials-using-your-tenant-preview"></a>Självstudie: utfärda och verifiera verifierbara autentiseringsuppgifter med din klient (för hands version)

> [!IMPORTANT]
> Azure Active Directory verifierbara autentiseringsuppgifter finns för närvarande i en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Nu när din Azure-klient har kon figurer ATS med tjänsten för verifierbar behörighet går vi igenom de steg som krävs för att aktivera Azure Active Directory (Azure AD) för att utfärda verifierbara autentiseringsuppgifter med hjälp av exempel appen.

I den här artikeln:

> [!div class="checklist"]
> * Registrera exempel appen i din Azure AD
> * Skapa en regel och Visa fil
> * Ladda upp regler och Visa filer
> * Konfigurera din Issuer-tjänst för verifierbara autentiseringsuppgifter för att använda Azure Key Vault
> * Uppdatera exempel kod med din klients information.

Vår exempel kod kräver att användare autentiseras för en identitets leverantör, särskilt Azure AD B2C, innan den verifierade Credential-experten kan utfärdas. Inte alla utfärdare av verifierbara autentiseringsuppgifter kräver autentisering innan autentiseringsuppgifter utfärdas.

Autentisering av ID-token gör att användarna kan bevisa vem de är innan de får sina autentiseringsuppgifter. När användarna har loggat in returnerar identitets leverantören en säkerhetstoken som innehåller anspråk om användaren. Utfärdar-tjänsten omvandlar sedan dessa säkerhetstoken och deras anspråk till verifierbara autentiseringsuppgifter. Den verifierbara autentiseringsuppgiften är signerad med utfärdaren.

Alla identitets leverantörer som stöder OpenID Connect-protokollet stöds. Exempel på identitets leverantörer som stöds är [Azure Active Directory](../fundamentals/active-directory-whatis.md)och [Azure AD B2C](../../active-directory-b2c/overview.md). I den här självstudien använder vi AAD.

## <a name="prerequisites"></a>Förutsättningar

I den här självstudien förutsätter vi att du redan har slutfört stegen i [föregående självstudie](enable-your-tenant-verifiable-credentials.md) och har åtkomst till den miljö som du använde.

## <a name="register-an-app-to-enable-did-wallets-to-sign-in-users"></a>Registrera en app för att aktivera inloggade plån boks användare

Om du vill utfärda en verifierbar autentiseringsuppgift måste du registrera en app så att autentiserare eller andra plån boks referenser för verifierbara autentiseringsuppgifter tillåts logga in användare.  

Registrera ett program med namnet ' VC plån boks app ' i Azure AD och hämta ett klient-ID.

1. Följ anvisningarna för att registrera ett program med [Azure AD](../develop/quickstart-register-app.md) vid registrering, Använd värdena nedan.

   - Namn: "VC för plån boks program"
   - Konto typer som stöds: konton endast i den här organisations katalogen
   - Omdirigerings-URI: vcclient://openid/

   ![registrera ett program](media/issue-verify-verifable-credentials-your-tenant/register-application.png)

2. När du har registrerat programmet skriver du ned program-ID: t (klient). Du behöver det här värdet senare.

   ![programmets klient-ID](media/issue-verify-verifable-credentials-your-tenant/client-id.png)

3. Välj knappen **slut punkter** och kopiera OpenID för att ansluta metadata dokument-URI: n. Du behöver den här informationen för nästa avsnitt. 

   ![utfärdarens slut punkter](media/issue-verify-verifable-credentials-your-tenant/application-endpoints.png)

## <a name="set-up-your-node-app-with-access-to-key-vault"></a>Konfigurera din Node-app med åtkomst till Key Vault

För att autentisera en användares begäran om utfärdande av autentiseringsuppgifter använder webbplatsen för utfärdaren dina kryptografiska nycklar i Azure Key Vault. För att få åtkomst till Azure Key Vault måste webbplatsen ha ett klient-ID och en klient hemlighet som kan användas för att autentisera till Azure Key Vault.

1. När du visar översikts sidan för VC-huvudappen väljer du **certifikat & hemligheter**.
    ![certifikat och hemligheter](media/issue-verify-verifable-credentials-your-tenant/vc-wallet-app-certs-secrets.png)
1. I avsnittet **klient hemligheter** väljer du **ny klient hemlighet**
    1. Lägg till en beskrivning som "nodens VC-klient hemlighet"
    1. Upphör att gälla: på ett år.
  ![Program hemlighet med ett års förfallo datum](media/issue-verify-verifable-credentials-your-tenant/add-client-secret.png)
1. Kopiera HEMLIGHETen. Du behöver den här informationen för att uppdatera din exempel-Node-app.

>[!WARNING]
> Du har en möjlighet att kopiera hemligheten. Hemligheten är ett hash-värde efter detta. Kopiera inte ID: t. 

När du har skapat ditt program och din klient hemlighet i Azure AD måste du ge programmet de behörigheter som krävs för att utföra åtgärder på din Key Vault. Att göra dessa behörighets ändringar krävs för att webbplatsen ska kunna komma åt och använda de privata nycklar som lagras där.

1. Gå till Key Vault.
2. Välj det nyckel valv som vi använder för de här självstudierna.
3. Välj **åtkomst principer** i vänster navigerings fält
4. Välj **+ Lägg till åtkomst princip**.
5. I avsnittet **nyckel behörigheter** väljer du **Hämta** och **signera**.
6. Välj **huvud konto** och Använd program-ID för att söka efter programmet som vi registrerade tidigare. Välj den.
7. Välj **Lägg till**.
8. Välj **Spara**.

Mer information om Key Vault behörigheter och åtkomst kontroll finns i [nyckel valvets RBAC-guide](../../key-vault/general/rbac-guide.md)

![tilldela Key Vault-behörigheter](media/issue-verify-verifable-credentials-your-tenant/key-vault-permissions.png)
## <a name="make-changes-to-match-your-environment"></a>Gör ändringar för att matcha din miljö

Hittills har vi arbetat med vår exempel-App. Appen använder [Azure Active Directory B2C](../../active-directory-b2c/overview.md) och vi växlar nu till att använda Azure AD så vi behöver göra vissa ändringar, inte bara för att matcha din miljö, utan även för att stödja ytterligare anspråk som inte har använts tidigare.

1. Kopiera regel filen nedan och spara den till **modified-expertRules.js**. 

    > [!NOTE]
    > **"scope": "OpenID Profile"** ingår i den här regel filen och inkluderades inte i exempel filens regelfil. I nästa avsnitt beskrivs hur du aktiverar de valfria anspråken i Azure Active Directory-klienten. 
    
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

2. Öppna filen och Ersätt **client_id** -och **konfigurations** värden med de två värdena som vi kopierade i föregående avsnitt.

    ![markering av de två värdena som behöver ändras som en del av det här steget](media/issue-verify-verifable-credentials-your-tenant/rules-file.png)

  Värde **konfigurationen** är OpenID för att ansluta metadata dokument-URI: n.

  Eftersom exempel koden använder Azure Active Directory B2C och vi använder Azure Active Directory, måste vi lägga till valfria anspråk via omfång för att dessa anspråk ska ingå i ID-token som ska skrivas till verifierbara autentiseringsuppgifter. 

3. Öppna Azure Active Directory på Azure Portal.
4. Välj **Appregistreringar**.
5. Öppna appen VC plån bok som vi skapade tidigare.
6. Välj **token-konfiguration**.
7. Välj **+ Lägg till valfritt anspråk**

     ![Lägg till ett valfritt anspråk under pollett-konfiguration](media/issue-verify-verifable-credentials-your-tenant/token-configuration.png)

8. Från **tokentyp** väljer du **ID** och i listan över tillgängliga anspråk väljer du **given_name** och **family_name**

     ![Lägg till valfria anspråk](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim.png)

9. Tryck på **Lägg till**.
10. Om du får varnings meddelandet som visas nedan markerar du kryss rutan och väljer **Lägg till**.

     ![Lägg till behörigheter för valfria anspråk](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim-permissions.png)

Nu när en användare visas med "logga in" för att få till gång till den verifierbara autentiseringsuppgiften vet appen VC plån boks att ta med de angivna anspråken via omfattnings parametern som ska skrivas till verifierbara autentiseringsuppgifter.

## <a name="create-new-vc-with-this-rules-file-and-the-old-display-file"></a>Skapa en ny VC med den här regel filen och den gamla visnings filen

1. Överför den nya regel filen till vår behållare
1. På sidan verifierbara autentiseringsuppgifter skapar du en ny autentiseringsuppgift med namnet **modifiedCredentialExpert** med hjälp av den gamla visnings filen och den nya regel filen (**modified-credentialExpert.jspå**).
1. När processen för att skapa autentiseringsuppgifter är klar på **översikts** sidan kopierar du **webb adressen till ärendets autentiseringsuppgifter** och sparar den eftersom vi behöver den i nästa avsnitt.

## <a name="before-we-continue"></a>Innan vi fortsätter

Vi måste lägga till några värden innan vi kan göra nödvändiga kod ändringar. Vi använder dessa värden i nästa avsnitt för att göra exempel koden att använda dina egna nycklar som lagras i ditt valv. Hittills borde vi ha följande värden klara.

- **Kontrakts-URI** från autentiseringsuppgifterna som vi nyss skapade (URL för utfärdande av autentiseringsuppgifter)
- **Programmets klient-ID** Vi fick detta när vi registrerade Node-appen.
- **Klient hemlighet** Vi skapade det tidigare när vi beviljade din app-åtkomst till Key Vault.

Det finns några andra värden som vi behöver ta innan vi kan göra ändringarna en gång i vår exempel-App. Nu ska vi hämta dem nu!

### <a name="verifiable-credentials-settings"></a>Inställningar för verifierbara autentiseringsuppgifter

1. Gå till sidan autentiseringsuppgifter för verifierbar och välj **Inställningar**.  
1. Kopiera följande värden:

    - Klient-ID 
    - Utfärdar-ID (din)
    - Nyckel valv (URI)

1. Under signerings nyckel identifieraren finns en URI, men vi behöver bara en del av den. Kopiera från den del som säger **issuerSigningKeyION** som är markerad med den röda rektangeln i bilden nedan.

   ![nyckel-ID för inloggning](media/issue-verify-verifable-credentials-your-tenant/issuer-signing-key-ion.png)

### <a name="did-document"></a>GJORDE dokumentet 

1. Öppna [DIF-nätverks Utforskaren](https://identity.foundation/ion/explorer/)

2. Klistra in din i Sök fältet.

4. Från det formaterade svaret hittar du avsnittet **verificationMethod**
5. Under "verificationMethod" kopierar du ID: t och etiketter det som kvSigningKeyId
    
    ```json=
    "verificationMethod": [
          {
            "id": "#sig_25e48331",
    ```

Nu har vi allt vi behöver för att göra ändringarna i vår exempel kod.

- **Utfärdare:** app.js uppdatera CONST-autentiseringsuppgifter med din nya kontrakts-URI
- **Verifier:** app.js uppdatera issuerDid med din Issuer-identifierare
- **Utfärdare och Verifier** uppdaterar didconfig.jsmed följande värden:


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
>Detta är ett demo program och du bör normalt aldrig ge ditt program hemlighet direkt.


Nu har du allt på plats för att utfärda och verifiera dina egna verifierbara autentiseringsuppgifter från din Azure Active Directory-klient med dina egna nycklar. 

## <a name="issue-and-verify-the-vc"></a>Utfärda och verifiera VC

Följ samma steg som vi följde i föregående självstudie för att utfärda verifierbara autentiseringsuppgifter och verifiera den med din app. När du har slutfört verifierings processen är du nu redo att fortsätta lära dig om verifierbara autentiseringsuppgifter.

1. Öppna en kommando tolk och öppna mappen utfärdare.
2. Kör den uppdaterade Node-appen.

    ```terminal
    node app.js
    ```

3. Använda en annan kommando tolk kör ngrok för att konfigurera en URL på 8081

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > Du kan också märka en varning om att den här appen eller webbplatsen kan vara riskfylld. Meddelandet förväntas för tillfället eftersom vi ännu inte har kopplat till din domän. Konfigurera detta genom att följa anvisningarna för [DNS-bindning](how-to-dnsbind.md) .

    
4. Öppna HTTPS-URL: en som genererats av ngrok.

    ![Slut punkter för NGROK-vidarebefordring](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

5. Välj **Hämta autentiseringsuppgift**
6. I autentiseraren genomsöker QR-koden.
7. I **den här appen eller webbplatsen kan ett riskfylldt** varnings meddelande Välj **Avancerat**.

  ![Inledande varning](media/enable-your-tenant-verifiable-credentials/site-warning.png)

8. På den riskfyllda webbplatsen varning väljer **du Fortsätt ändå (osäker)**

  ![Andra varning om utfärdaren](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


9. På skärmen **Lägg till autentiseringsuppgifter** ser du några saker: 
    1. Längst upp på skärmen ser du ett rött **ej verifierat** meddelande
    1. Autentiseringsuppgifterna anpassas baserat på de ändringar som vi gjorde i visnings filen.
    1. Alternativet **Logga in på ditt konto** pekar på inloggnings sidan för Azure AD.
    
   ![Sidan Lägg till autentiseringsuppgift med varning](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

10. Välj **Logga in på ditt konto** och autentisera med hjälp av en användare i din Azure AD-klient.
11. När du har autentiserat knappen **Lägg till** är det inte längre grått. Välj **Lägg till**.

  ![Sidan Lägg till autentiseringsuppgifter efter autentisering](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

Vi har nu utfärdat en verifierbar autentiseringsuppgift som använder vår klient organisation för att generera VC samtidigt som du fortfarande använder vår B2C-klient för autentisering.

  ![VC utfärdat av din Azure AD och autentiserad av vår Azure B2C-instans](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>Testa att verifiera VC med hjälp av exempel appen

Nu när vi har utfärdat de verifierbara autentiseringsuppgifterna från vår egen klient med anspråk från din Azure AD, ska vi kontrol lera att vi använder vår exempel App.

1. Sluta köra din Issuer ngrok-tjänst.

    ```cmd
    control-c
    ```

2. Kör nu ngrok med Verifier-porten 8082.

    ```cmd
    ngrok http 8082
    ```

3. I ett annat terminalfönster navigerar du till appen Verifier och kör den på samma sätt som du körde utfärdarens app.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

4. Öppna ngrok-URL: en i webbläsaren och skanna QR-koden med hjälp av autentiseraren i din mobila enhet.
5. På din mobila enhet väljer du **Tillåt** på skärmen **ny behörighets förfrågan** .

   >[!IMPORTANT]
    > Eftersom exempel appen också använder det gjorde du för att signera presentations förfrågan, ser du en varning om att den här appen eller webbplatsen kan vara riskfylld. Meddelandet förväntas för tillfället eftersom vi ännu inte har kopplat till din domän. Konfigurera detta genom att följa anvisningarna för [DNS-bindning](how-to-dnsbind.md) .
    
   ![ny behörighets förfrågan](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. Du har inte verifierat dina autentiseringsuppgifter och webbplatsen ska visa ditt för-och efter namn från Azure AD-användarkontot. 

Du har nu slutfört självstudien och är officiellt en verifierad Credential-expert! Din exempel app använder din gjorde för både utfärdande och verifiering, medan du skriver anspråk till en verifierbar autentiseringsuppgift från din Azure AD. 

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du skapar [anpassade autentiseringsuppgifter](credential-design.md)
- Kommunikations [exempel](issuer-openid.md) för Issuer service