---
title: 'Självstudie: Konfigurera Azure Active Directory att utfärda verifierbara autentiseringsuppgifter (för hands version)'
description: I den här självstudien skapar du den miljö som krävs för att distribuera verifierbara autentiseringsuppgifter i din klient organisation
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: tutorial
ms.subservice: verifiable-credentials
ms.date: 03/31/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 08aaa49f73ed437e041ffb93dc9ef5be41e316ec
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106172028"
---
# <a name="tutorial-configure-your-azure-active-directory-to-issue-verifiable-credentials-preview"></a>Självstudie: Konfigurera Azure Active Directory att utfärda verifierbara autentiseringsuppgifter (för hands version)

I den här självstudien bygger vi på arbetet som du utför som en del av [Kom igång](get-started-verifiable-credentials.md) -artikeln och konfigurerar din Azure Active Directory (Azure AD) med sin egen [decentraliserade identifierare](https://www.microsoft.com/security/business/identity-access-management/decentralized-identity-blockchain?rtc=1#:~:text=Decentralized%20identity%20is%20a%20trust,protect%20privacy%20and%20secure%20transactions.) (DID). Vi använder den decentraliserade identifieraren för att utfärda en verifierbar autentiseringsuppgift med hjälp av exempel appen och utfärdaren. men i den här självstudien använder vi fortfarande Azure B2C-klienten för autentisering.  I nästa självstudie kommer vi att vidta ytterligare åtgärder för att få appen konfigurerad att fungera med din Azure AD.

> [!IMPORTANT]
> Azure Active Directory verifierbara autentiseringsuppgifter finns för närvarande i en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I den här artikeln:

> [!div class="checklist"]
> * Du skapar nödvändiga tjänster för att publicera din Azure AD efter verifierbara autentiseringsuppgifter 
> * Vi skapar din
> * Vi anpassar reglerna och visnings filerna
> * Konfigurera verifierbara autentiseringsuppgifter i Azure AD.


## <a name="prerequisites"></a>Förutsättningar

Innan du kan slutföra den här självstudien måste du först:

- Slutför [Kom igång](get-started-verifiable-credentials.md).
- Ha ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure AD med en P2- [licens](https://azure.microsoft.com/pricing/details/active-directory/). Följ anvisningarna [för att skapa ett kostnads fritt utvecklings konto](how-to-create-a-free-developer-account.md) om du inte har något.
- En instans av [Azure Key Vault](../../key-vault/general/overview.md) där du har behörighet att skapa nycklar och hemligheter.

## <a name="azure-active-directory"></a>Azure Active Directory

Innan vi kan starta behöver vi en Azure AD-klient. När din klient är aktive rad för verifierbara autentiseringsuppgifter tilldelas den en decentraliserad identifierare (DID) och den är aktive rad med en Issuer-tjänst för utfärdande av verifierbara autentiseringsuppgifter. Alla verifierbara autentiseringsuppgifter som du utfärdar utfärdas av din klient organisation och dess. Används också vid verifiering av verifierbara autentiseringsuppgifter.
Om du precis har skapat en Azure-prenumeration behöver din klient inte fyllas med användar konton, men du måste ha minst ett användar test konto för att slutföra självstudierna.

## <a name="create-a-key-vault"></a>Skapa en Key Vault-lösning

När du arbetar med verifierbara autentiseringsuppgifter har du fullständig kontroll och hantering av de kryptografiska nycklar som din klient använder för att digitalt signera verifierbara autentiseringsuppgifter. Om du vill utfärda och verifiera autentiseringsuppgifter måste du ge Azure AD åtkomst till din egen instans av Azure Key Vault.

1. På Azure Portal-menyn eller på **Start** sidan väljer du **skapa en resurs**.
2. I rutan Sök anger du **nyckel valv**.
3. Välj **Key Vault** i listan med resultat.
4. Välj **Skapa** i avsnittet Key Vault.
5. Ange följande information i avsnittet **Skapa nyckelvalv** avsnittet Ange följande information:
    - **Prenumeration**: Välj en prenumeration.
    - Under **resurs grupp** väljer du **Skapa ny** och anger ett resurs grupp namn, till exempel **VC-resurs-grupp**. Vi använder samma resurs grupp namn i flera artiklar.
    - **Namn**: Ett unikt namn krävs. Vi använder **woodgroup-VC-kv** , så Ersätt det här värdet med ditt eget unika namn.
    - Välj en plats i listrutan **Plats**.
    - Lämna standardvärdena för de andra alternativen.
6. När du har angett informationen ovan väljer du **åtkomst princip**

    ![Sidan skapa ett nyckel valv](media/enable-your-tenant-verifiable-credentials/create-key-vault.png)

7. På skärmen åtkomst princip väljer du **Lägg till åtkomst princip**

    >[!NOTE]
    > Som standard är det konto som skapar nyckel valvet det enda med åtkomst. Tjänsten verifierbar autentiseringsuppgifter behöver åtkomst till nyckel valvet. Nyckel valvet måste ha en åtkomst princip som gör det möjligt för administratören att **skapa nycklar**, ha möjlighet att **ta bort nycklar** om du avanmäler dig och **signera** för att skapa domän bindningen för verifierbara autentiseringsuppgifter. Om du använder samma konto vid testning ser du till att ändra standard principen för att bevilja konto **inloggningen** utöver de standard behörigheter som har beviljats för valvets skapare.

8. För användar administratören kontrollerar du att avsnittet nyckel behörigheter har **create**, **Delete** och **Sign** aktiverat. Som standard är Create och Delete redan aktiverat och signering ska vara den enda nyckel behörighet som behöver uppdateras. 

    ![Key Vault behörigheter](media/enable-your-tenant-verifiable-credentials/keyvault-access.png)

9. Välj **Granska + skapa**.
10. Välj **Skapa**.
11. Gå till valvet och anteckna valvets namn och URI

Anteckna de två egenskaperna som visas nedan:

- **Valv namn**: i exemplet är värde namnet **Sparbank-VC-kv**. Du använder det här namnet för andra steg.
- **Valv-URI**: i exemplet är det här värdet https://woodgrove-vc-kv.vault.azure.net/ . Program som använder ditt valv via dess REST-API måste använda denna URI.

>[!NOTE]
> Varje Key Vault-transaktion resulterar i ytterligare kostnader för Azure-prenumeration. Mer information hittar du på [sidan med Key Vault priser](https://azure.microsoft.com/pricing/details/key-vault/) .

>[!IMPORTANT]
> Under förhands granskningen Azure Active Directory verifierbara autentiseringsuppgifter ska nycklar och hemligheter som skapats i valvet inte ändras när de har skapats. Borttagning, inaktive ring eller uppdatering av nycklar och hemligheter invaliderar eventuella utfärdade autentiseringsuppgifter. Ändra inte dina nycklar eller hemligheter under för hands versionen.

## <a name="create-a-modified-rules-and-display-file"></a>Skapa en ändrad regel och Visa fil

I det här avsnittet ska vi använda reglerna och Visa filer från exempel appen för certifikat utfärdare och ändra dem något för att skapa din klients första verifierbara autentiseringsuppgift.

1. Kopiera både reglerna och Visa JSON-filer till en tillfällig mapp och Byt namn på dem **MyFirstVC-display.jspå** respektive **MyFirstVC-rules.js** . Du kan hitta båda filerna under **Issuer \ issuer_config**

   ![Visa och regler filer som en del av exempel katalogen för appen](media/enable-your-tenant-verifiable-credentials/sample-app-rules-display.png)

   ![Visa och regler filer i en Temp-mapp](media/enable-your-tenant-verifiable-credentials/display-rules-files-temp.png)

2. Öppna den MyFirstVC-rules.jsfilen i kod redigeraren. 

    ```json
         {
          "attestations": {
            "idTokens": [
              {
                "mapping": {
                  "firstName": { "claim": "given_name" },
                  "lastName": { "claim": "family_name" }
                },
                "configuration": "https://didplayground.b2clogin.com/didplayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
                "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
                "redirect_uri": "vcclient://openid/"
              }
            ]
          },
          "validityInterval": 2592000,
          "vc": {
            "type": ["VerifiedCredentialExpert"]
          }
        }
      
    ```

Nu ska vi ändra fältet typ till "MyFirstVC". 

  ```json
   "type": ["MyFirstVC"]
  
  ```

Spara den här ändringen.

 >[!NOTE]
   > Vi ändrar inte **"konfiguration"** eller **"client_id"** i det här skedet i självstudien. Vi använder fortfarande Microsoft B2C-klienten som vi använde i [Kom igång](get-started-verifiable-credentials.md). Vi kommer att använda Azure AD i nästa självstudie.

3. Öppna den MyFirstVC-display.jsfilen i kod redigeraren.

   ```json
       {
          "default": {
           "locale": "en-US",
           "card": {
             "title": "Verified Credential Expert",
             "issuedBy": "Microsoft",
             "backgroundColor": "#000000",
             "textColor": "#ffffff",
             "logo": {
               "uri": "https://didcustomerplayground.blob.core.windows.net/public/VerifiedCredentialExpert_icon.png",
               "description": "Verified Credential Expert Logo"
             },
             "description": "Use your verified credential to prove to anyone that you know all about verifiable credentials."
           },
           "consent": {
             "title": "Do you want to get your Verified Credential?",
             "instructions": "Sign in with your account to get your card."
           },
           "claims": {
             "vc.credentialSubject.firstName": {
               "type": "String",
               "label": "First name"
             },
             "vc.credentialSubject.lastName": {
               "type": "String",
               "label": "Last name"
             }
           }
         }
      }
   ```

Gör det möjligt att göra några ändringar, så att den här verifierbara autentiseringsuppgiften ser annorlunda ut jämfört med exempel kodens version. 
    
```json
     "card": {
        "title": "My First VC",
        "issuedBy": "Your Issuer Name",
        "backgroundColor": "#ffffff",
        "textColor": "#000000",
```

Spara ändringarna.
## <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Innan du skapar vår första verifierbara autentiseringsuppgift måste vi skapa en Blob Storage behållare som kan innehålla våra filer för konfiguration och regler.

1. Skapa ett lagrings konto med hjälp av alternativen som visas nedan. Detaljerade anvisningar finns i artikeln [skapa ett lagrings konto](../../storage/common/storage-account-create.md?tabs=azure-portal) .

   - **Prenumeration:** Välj den prenumeration som vi använder för de här självstudierna.
   - **Resurs grupp:** Välj samma resurs grupp som vi använde i tidigare Självstudier (**VC-Resource-Group**).
   - **Namn:**  Ett unikt namn.
   - **Plats:** (US) USA, östra.
   - **Prestanda:** Standard.
   - **Konto typ:** Storage v2.
   - **Replikering:** Lokalt redundant.
 
   ![Skapa ett nytt lagringskonto](media/enable-your-tenant-verifiable-credentials/create-storage-account.png)

2. När du har skapat lagrings kontot måste vi skapa en behållare. Välj **behållare** under **Blob Storage** och skapa en behållare med hjälp av de värden som anges nedan:

    - **Namn:** VC-container
    - **Offentlig åtkomst nivå:** Privat (ingen anonym åtkomst)

   ![Skapa en container](media/enable-your-tenant-verifiable-credentials/new-container.png)

3. Välj den nya behållaren och ladda upp både de nya reglerna och visnings filerna **MyFirstVC-display.jspå** och **MyFirstVC-rules.jspå** vi skapade tidigare.

   ![Ladda upp regel fil](media/enable-your-tenant-verifiable-credentials/blob-storage-upload-rules-display-files.png)

## <a name="assign-blob-role"></a>Tilldela BLOB-roll

Innan du skapar autentiseringsuppgifterna måste vi först ge den inloggade användaren rätt roll tilldelning så att de kan komma åt filerna i Storage blob.

1. Gå till **lagrings**  >  **behållare**.
2. Välj **Access Control (IAM)** på menyn till vänster.
3. Välj **roll tilldelningar**.
4. Välj **Lägg till**.
5. I avsnittet **roll** väljer du **Storage BLOB data Reader**.
6. Under **tilldela åtkomst för att** välja **användare, grupp eller tjänst princip**.
7. I **Välj**: väljer du det konto som du använder för att utföra dessa steg.
8. Klicka på **Spara** för att slutföra roll tilldelningen.


   ![Lägg till en rolltilldelning](media/enable-your-tenant-verifiable-credentials/role-assignment.png)

  >[!IMPORTANT]
  >Som standard hämtar behållar skapare den tilldelade **ägar** rollen. **Ägar** rollen är inte tillräckligt egen. Ditt konto måste ha rollen **Storage BLOB data Reader** . Om du vill ha mer information kan du [använda Azure Portal för att tilldela en Azure-roll för åtkomst till blob-och Queue-data](../../storage/common/storage-auth-aad-rbac-portal.md)

## <a name="set-up-verifiable-credentials-preview"></a>Konfigurera verifierbara autentiseringsuppgifter (förhands granskning)

Nu måste vi vidta det sista steget för att ställa in din klient för verifierbara autentiseringsuppgifter.

1. Sök efter **verifierbara autentiseringsuppgifter** från Azure Portal.
2. Välj **verifierbara autentiseringsuppgifter (för hands version)**.
3. Välj **Kom igång**
4. Vi behöver konfigurera din organisation och tillhandahålla organisations namn, domän och nyckel valv. Nu ska vi titta på var och en av värdena.

      - **organisations namn**: det här namnet är hur du refererar till ditt företag i tjänsten för verifierbar behörighet. Det här värdet är inte kund riktat.
      - **Domän:** Den angivna domänen läggs till i en tjänst slut punkt i dokumentet. [Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md) och andra plån bok använder den här informationen för att kontrol lera att din har [länkats till din domän](how-to-dnsbind.md). Om plån boken kan verifiera den visas en verifierad symbol. Om plån boken inte kan verifiera det informeras användaren om att autentiseringsuppgiften utfärdades av en organisation som den inte kunde verifiera. Domänen är vad som är möjligt att binda till något som du kan känna till om din verksamhet.
      - **Nyckel valv:** Ange namnet på Key Vault som vi skapade tidigare.
 
   >[!IMPORTANT]
   > Domänen får inte vara en omdirigering, annars går det inte att länka till domänen. Se till att använda https://www.domain.com format.
    
5. Välj **Spara och skapa autentiseringsuppgift**

    ![Konfigurera din organisations identitet](media/enable-your-tenant-verifiable-credentials/save-create.png)

Grattis, din klient har nu Aktiver ATS för för hands versionen av verifierbara autentiseringsuppgifter!

## <a name="create-your-vc-in-the-portal"></a>Skapa din VC i portalen

Föregående steg är kvar på sidan **skapa en ny autentiseringsuppgift** . 

   ![Kom igång med verifierbara autentiseringsuppgifter](media/enable-your-tenant-verifiable-credentials/create-credential-after-enable-did.png)

1. Lägg till namnet **MyFirstVC** under namn på autentiseringsuppgift. Det här namnet används i portalen för att identifiera dina verifierbara autentiseringsuppgifter och ingår som en del av det verifierbara autentiseringsuppgifterna.
2. I avsnittet Visa fil väljer du **Konfigurera visnings fil**
3. I avsnittet **lagrings konton** väljer du **woodgrovevcstorage**.
4. Välj **VC-container** i listan över tillgängliga behållare.
5. Välj den **MyFirstVC-display.jspå** filen som vi skapade tidigare.
6. I avsnittet **skapa en ny autentiseringsuppgift** i **regel filen** väljer du **Konfigurera regel fil**
7. I avsnittet **lagrings konton** väljer du **woodgrovecstorage**
8. Välj **VC-container**.
9. Välj **MyFirstVC-rules.jspå**
10. På sidan **skapa en ny autentiseringsuppgift** väljer du **skapa**.

   ![skapa en ny autentiseringsuppgift](media/enable-your-tenant-verifiable-credentials/create-my-first-vc.png)

### <a name="credential-url"></a>URL för autentiseringsuppgifter

Nu när du har en ny autentiseringsuppgift kopierar du URL: en för autentiseringsuppgifter.

   ![Utfärdarens autentiseringsuppgifter-URL](media/enable-your-tenant-verifiable-credentials/issue-credential-url.png)

>[!NOTE]
>URL: en för autentiseringsuppgifter är en kombination av reglerna och visnings filerna. Det är den URL som autentiseraren utvärderar innan det visas krav på utfärdande av autentiseringsuppgifter för användarens verifierbara autentiseringsuppgifter.  

## <a name="update-the-sample-app"></a>Uppdatera exempel appen

Nu gör vi ändringar i exempel appens Issuer-kod för att uppdatera den med URL: en för verifierbar inloggning. På så sätt kan du utfärda verifierbara autentiseringsuppgifter med din egen klient.

1. Gå till mappen där du placerade exempel appens filer.
2. Öppna mappen utfärdare och öppna sedan app.js i Visual Studio Code.
3. Uppdatera konstanten autentiseringsuppgift med din nya URL för autentiseringsuppgifter och ange konstanten credentialType till MyFirstVC och spara filen.

    ![bild av Visual Studio-kod som visar de relevanta markerade områdena](media/enable-your-tenant-verifiable-credentials/sample-app-vscode.png)

4. Öppna en kommando tolk och öppna mappen utfärdare.
5. Kör den uppdaterade Node-appen.

    ```terminal
    node app.js
    ```

6. Använda en annan kommando tolk kör ngrok för att konfigurera en URL på 8081

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > Du kan också märka en varning om att den här appen eller webbplatsen kan vara riskfylld. Meddelandet förväntas för tillfället eftersom vi ännu inte har kopplat till din domän. Konfigurera detta genom att följa anvisningarna för [DNS-bindning](how-to-dnsbind.md) .

    
7. Öppna HTTPS-URL: en som genererats av ngrok.

    ![Slut punkter för NGROK-vidarebefordring](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

8. Välj **Hämta autentiseringsuppgift**
9. I autentiseraren genomsöker QR-koden.
10. I **den här appen eller webbplatsen kan ett riskfylldt** varnings meddelande Välj **Avancerat**.

  ![Inledande varning](media/enable-your-tenant-verifiable-credentials/site-warning.png)

11. På den riskfyllda webbplatsen varning väljer **du Fortsätt ändå (osäker)**

  ![Andra varning om utfärdaren](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


12. På skärmen **Lägg till autentiseringsuppgifter** ser du några saker: 
    1. Längst upp på skärmen ser du ett rött **ej verifierat** meddelande
    1. Autentiseringsuppgifterna anpassas baserat på de ändringar som vi gjorde i visnings filen.
    1. Alternativet **Logga in på ditt konto** pekar på **didplayground.b2clogin.com**.
    
   ![Sidan Lägg till autentiseringsuppgift med varning](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

13. Välj **Logga in på ditt konto** och autentisera med hjälp av den autentiseringsinformation du angav i [självstudien kom igång](get-started-verifiable-credentials.md).
14. När du har autentiserat knappen **Lägg till** är det inte längre grått. Välj **Lägg till**.

  ![Sidan Lägg till autentiseringsuppgifter efter autentisering](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

Vi har nu utfärdat en verifierbar autentiseringsuppgift som använder vår klient organisation för att generera VC samtidigt som du fortfarande använder vår B2C-klient för autentisering.

  ![VC utfärdat av din Azure AD och autentiserad av vår Azure B2C-instans](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>Testa att verifiera VC med hjälp av exempel appen

Nu när vi har utfärdat de verifierbara autentiseringsuppgifterna från vår egen klient, ska vi kontrol lera att de använder vår exempel App.

>[!IMPORTANT]
> När du testar använder du samma e-postadress och lösen ord som du använde när du [Kom igång](get-started-verifiable-credentials.md) -artikeln. Medan klienten utfärdar VC kommer InInformationen från en ID-token som utfärdats av Microsoft B2C-klienten. I självstudie två byter vi token-utfärdande till din klient

1. Öppna **Inställningar** på bladet verifierbara autentiseringsuppgifter i Azure Portal. Kopiera den decentraliserade identifieraren (DID).

   ![Kopiera DID](media/enable-your-tenant-verifiable-credentials/issuer-identifier.png)

2. Öppna sedan Verifier-delen av exempelfilerna. Vi måste uppdatera app.js-filen i Verifier-exempel koden och göra följande ändringar:

    - **autentiseringsuppgift**: ändra till din URL för autentiseringsuppgifter
    - **credentialType**: ' MyFirstVC '
    - **issuerDid**: kopiera det här värdet från Azure Portal>verifierbara autentiseringsuppgifter (för hands version) >inställningar>decentraliserad IDENTIFIERARE (DID)
    
   ![uppdatera konstanten issuerDid så att den matchar utfärdarens identifierare](media/enable-your-tenant-verifiable-credentials/editing-verifier.png)

3. Sluta köra din Issuer ngrok-tjänst.

    ```cmd
    control-c
    ```

4. Kör nu ngrok med Verifier-porten 8082.

    ```cmd
    ngrok http 8082
    ```

5. I ett annat terminalfönster navigerar du till appen Verifier och kör den på samma sätt som du körde utfärdarens app.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

6. Öppna ngrok-URL: en i webbläsaren och skanna QR-koden med hjälp av autentiseraren i din mobila enhet.
7. På din mobila enhet väljer du **Tillåt** på skärmen **ny behörighets förfrågan** .

    >[!NOTE]
    > Den signerade denna VC är fortfarande Microsoft-B2C. Verifieraren fanns fortfarande från Microsofts exempel klient för program. Eftersom Microsofts har kopplats till en domän som vi äger, ser du inte den varning som vi fick under utfärdande flödet. Detta kommer att uppdateras i nästa avsnitt.
    
   ![ny behörighets förfrågan](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. Du har inte verifierat dina autentiseringsuppgifter.

## <a name="next-steps"></a>Nästa steg

Nu när du har exempel koden som utfärdar en VC från din utfärdare, kan du fortsätta till nästa avsnitt där du använder din egen identitetsprovider för att autentisera användare som försöker hämta verifierbara autentiseringsuppgifter och använda det du gjorde för att signera presentations förfrågningar.

> [!div class="nextstepaction"]
> [Självstudie – utfärda och verifiera verifierbara autentiseringsuppgifter med din klient](issue-verify-verifiable-credentials-your-tenant.md)


