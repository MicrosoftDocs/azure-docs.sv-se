---
title: 'Lägg till autentisering för att säkra anrop till anpassade API: er'
description: 'Konfigurera autentisering för att förbättra säkerheten för anrop till anpassade API: er från Azure Logic Apps'
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/22/2017
ms.openlocfilehash: 5e0dcd478c6eb6696a0e07d35d4dccddac68ac1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95993176"
---
# <a name="increase-security-for-calls-to-custom-apis-from-azure-logic-apps"></a>Öka säkerheten för anrop till anpassade API: er från Azure Logic Apps

För att förbättra säkerheten för anrop till dina API: er kan du konfigurera Azure Active Directory (Azure AD)-autentisering via Azure Portal så att du inte behöver uppdatera koden. Eller så kan du kräva och ställa in autentisering via API-koden.

## <a name="authentication-options-for-your-api"></a>Autentiseringsalternativ för ditt API

Du kan förbättra säkerheten för anrop till ditt anpassade API på följande sätt:

* [Inga kod ändringar](#no-code): skydda ditt api med [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) via Azure Portal, så du behöver inte uppdatera koden eller distribuera om ditt API.

  > [!NOTE]
  > Som standard ger Azure AD-autentiseringen som du aktiverar i Azure Portal inte detaljerade auktoriseringar. Den här autentiseringen kan till exempel låsa ditt API till bara en speciell klient, inte en speciell användare eller app. 

* [Uppdatera API-koden](#update-code): skydda ditt API genom att framtvinga [certifikatautentisering](#certificate), [grundläggande autentisering](#basic)eller [Azure AD-autentisering](#azure-ad-code) via kod.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>Autentisera anrop till ditt API utan att ändra kod

Här följer de allmänna stegen för den här metoden:

1. Skapa två Azure Active Directory (Azure AD) program identiteter: en för din Logic app och en för din webbapp (eller API-app).

2. Om du vill autentisera anrop till ditt API använder du autentiseringsuppgifterna (klient-ID och hemlighet) för tjänstens huvud namn som är associerat med Azure AD-programidentiteten för din Logic app.

3. Inkludera program-ID: n i din Logic app-definition.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>Del 1: skapa en Azure AD-Programidentitet för din Logic app

Din Logic app använder den här Azure AD-programidentiteten för att autentisera mot Azure AD. Du behöver bara konfigurera den här identiteten en gången för din katalog. Du kan till exempel välja att använda samma identitet för alla dina Logi Kap par, även om du kan skapa unika identiteter för varje Logic app. Du kan ställa in dessa identiteter i Azure Portal eller använda [PowerShell](#powershell).

**Skapa program identiteten för din Logic app i Azure Portal**

1. I [Azure Portal](https://portal.azure.com "https://portal.azure.com")väljer du **Azure Active Directory**. 

2. Bekräfta att du befinner dig i samma katalog som din webbapp eller API-app.

   > [!TIP]
   > Om du vill växla katalog väljer du din profil och väljer en annan katalog. Eller Välj **Översikt över**  >  **växel katalog**.

3. I menyn katalog, under **Hantera**, väljer du **Appregistreringar**  >  **ny program registrering**.

   > [!TIP]
   > Appens registrerings lista visar som standard alla app-registreringar i din katalog. Om du bara vill visa dina program registreringar väljer du **Mina appar** bredvid sökrutan. 

   ![Skapa ny appregistrering](./media/logic-apps-custom-api-authentication/new-app-registration-azure-portal.png)

4. Ge program identiteten ett namn, lämna **program typ** inställd på **webbapp/API**, ange en unik sträng formaterad som en domän för **inloggnings-URL** och välj **skapa**.

   ![Ange namn och inloggnings-URL för program identitet](./media/logic-apps-custom-api-authentication/logic-app-identity-azure-portal.png)

   Program identiteten som du skapade för din Logi Kap par visas nu i listan med registrerings program.

   ![Program identitet för din Logic app](./media/logic-apps-custom-api-authentication/logic-app-identity-created.png)

5. I listan program registreringar väljer du den nya program identiteten. Kopiera och spara det **program-ID** som ska användas som "klient-ID" för din Logic app i del 3.

   ![Kopiera och spara program-ID för Logic app](./media/logic-apps-custom-api-authentication/logic-app-application-id.png)

6. Om dina program identitets inställningar inte visas väljer du **Inställningar** eller **alla inställningar**.

7. Under **API-åtkomst** väljer du **nycklar**. Under **Beskrivning** anger du ett namn för nyckeln. Under **upphör ande** väljer du en varaktighet för nyckeln.

   Den nyckel som du skapar fungerar som program identitetens "hemligt" eller lösen ord för din Logic app.

   ![Skapa nyckel för Logic app-identitet](./media/logic-apps-custom-api-authentication/create-logic-app-identity-key-secret-password.png)

8. Välj **Spara** i verktygsfältet. Under **värde** visas nu din nyckel. 
**Se till att kopiera och spara nyckeln** för senare användning eftersom nyckeln är dold när du lämnar sidan **nycklar** .

   När du konfigurerar din Logic-app i del 3 anger du den här nyckeln som "hemlig" eller lösen ord.

   ![Kopiera och spara nyckel för senare](./media/logic-apps-custom-api-authentication/logic-app-copy-key-secret-password.png)

<a name="powershell"></a>

**Skapa program identiteten för din Logic app i PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan utföra den här uppgiften med hjälp av Azure Resource Manager med PowerShell. I PowerShell kör du följande kommandon:

1. `Add-AzAccount`

1. `$SecurePassword = Read-Host -AsSecureString`

1. Ange ett lösen ord och tryck på RETUR.

1. `New-AzADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password $SecurePassword`

1. Se till att kopiera **klient-ID** : t (GUID för din Azure AD-klient), **program-ID** och lösen ord som du använde.

Mer information finns i så här [skapar du ett huvud namn för tjänsten med PowerShell för att få åtkomst till resurser](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>Del 2: skapa en Azure AD-Programidentitet för din webbapp eller API-app

Om din webbapp eller API-app redan har distribuerats kan du aktivera autentisering och skapa program identiteten i Azure Portal. Annars kan du [aktivera autentisering när du distribuerar med en Azure Resource Manager-mall](#authen-deploy). 

**Skapa program identiteten och aktivera autentisering i Azure Portal för distribuerade appar**

1. Leta upp och välj din webbapp eller API-app i [Azure Portal](https://portal.azure.com "https://portal.azure.com"). 

2. Under **Inställningar** väljer du **autentisering/auktorisering**. Under **App Service autentisering** aktiverar du autentisering **.** Under **autentiseringsproviders** väljer du **Azure Active Directory**.

   ![Aktivera autentisering](./media/logic-apps-custom-api-authentication/custom-web-api-app-authentication.png)

3. Skapa nu en program identitet för webbappen eller API-appen enligt vad som visas här. På sidan **Azure Active Directory inställningar** anger du **hanterings läge** till **Express**. Välj **Skapa ny AD-App**. Ge program identiteten ett namn och välj **OK**. 

   ![Skapa program identitet för webbappen eller API-appen](./media/logic-apps-custom-api-authentication/custom-api-application-identity.png)

4. På sidan **Autentisering/auktorisering** väljer du **Spara**.

Nu måste du hitta klient-ID och klient-ID för program identiteten som är associerad med din webbapp eller API-app. Du använder dessa ID: n i del 3. Fortsätt sedan med de här stegen för Azure Portal.

**Hitta program identitetens klient-ID och klient-ID för din webbapp eller API-app i Azure Portal**

1. Under **autentiseringsproviders** väljer du **Azure Active Directory**. 

   ![Välj ”Azure Active Directory”](./media/logic-apps-custom-api-authentication/custom-api-app-identity-client-id-tenant-id.png)

2. På sidan **Azure Active Directory inställningar** anger du **hanterings läge** till **Avancerat**.

3. Kopiera **klient-ID** och spara det GUID för användning i del 3.

   > [!TIP] 
   > Om **klient-ID** och **utfärdare-URL** inte visas kan du försöka med att uppdatera Azure Portal och upprepa steg 1.

4. Under **utfärdar-URL** kopierar du och sparar bara GUID för del 3. Du kan också använda det här GUID: t i webbappen eller i API-appens distributionsmall, om det behövs.

   Detta GUID är din speciella klients GUID ("klient-ID") och ska visas i denna URL: `https://sts.windows.net/{GUID}`

5. Stäng sidan **Azure Active Directory inställningar** utan att spara ändringarna.

<a name="authen-deploy"></a>

**Aktivera autentisering när du distribuerar med en Azure Resource Manager mall**

Du måste fortfarande skapa en Azure AD-Programidentitet för din webbapp eller API-app som skiljer sig från appens identitet för din Logic app. Om du vill skapa program identiteten följer du föregående steg i del 2 för Azure Portal. 

Du kan också följa stegen i del 1, men se till att använda webbappen eller API-appens faktiska `https://{URL}` för **inloggnings-URL** och **app-ID-URI**. I de här stegen måste du spara både klient-ID och klient-ID för användning i appens distributionsmall och även i del 3.

> [!NOTE]
> När du skapar Azure AD-programidentiteten för din webbapp eller API-app måste du använda Azure Portal, inte PowerShell. PowerShell-kommandot konfigurerar inte de behörigheter som krävs för att logga in användare på en webbplats.

När du har skaffat klient-ID och klient-ID ska du inkludera dessa ID: n som en under resurs till din webbapp eller API-app i distributions mal len:

``` json
"resources": [ 
   {
      "apiVersion": "2015-08-01",
      "name": "web",
      "type": "config",
      "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
      "properties": {
         "siteAuthEnabled": true,
         "siteAuthSettings": {
            "clientId": "<client-ID>",
            "issuer": "https://sts.windows.net/<tenant-ID>/"
         }
      }
   } 
]
```

Om du vill distribuera en tom webbapp och en logisk app automatiskt med Azure Active Directory autentisering, [visar du den fullständiga mallen här](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json)eller klickar på **distribuera till Azure** här:

[![Distribuera till Azure](media/logic-apps-custom-api-authentication/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>Del 3: fylla i avsnittet auktorisering i din Logic app

Det här avsnittet har redan kon figurer ATS för föregående mall, men om du redigerar Logic-appen direkt måste du ta med avsnittet fullständig behörighet.

Öppna din Logic app-definition i kodvyn, gå till definition av **http-** åtgärd, hitta avsnittet **auktorisering** och ta med följande egenskaper:

```json
{
   "tenant": "<tenant-ID>",
   "audience": "<client-ID-from-Part-2-web-app-or-API app>", 
   "clientId": "<client-ID-from-Part-1-logic-app>",
   "secret": "<key-from-Part-1-logic-app>", 
   "type": "ActiveDirectoryOAuth"
}
```

| Egenskap | Krävs | Beskrivning | 
| -------- | -------- | ----------- | 
| tenant | Ja | GUID för Azure AD-klienten | 
| filmen | Ja | GUID för den mål resurs som du vill få åtkomst till, vilket är klient-ID: t från program identiteten för din webbapp eller API-app | 
| ClientID | Ja | GUID för klienten som begär åtkomst, vilket är klient-ID: t från program identiteten för din Logic app | 
| hemlighet | Ja | Nyckeln eller lösen ordet från program identiteten för klienten som begär åtkomsttoken | 
| typ | Ja | Autentiseringstypen. För ActiveDirectoryOAuth-autentisering är värdet `ActiveDirectoryOAuth` . | 
|||| 

Exempel:

``` json
{
   "actions": {
      "HTTP": {
         "inputs": {
            "method": "POST",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         }
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>Säkra API-anrop via kod

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>Certifikatsautentisering

Du kan använda klient certifikat för att validera inkommande begär Anden från din Logic app till din webbapp eller API-app. Om du vill konfigurera din kod lär du dig [hur du konfigurerar ömsesidig TLS-autentisering](../app-service/app-service-web-configure-tls-mutual-auth.md).

I avsnittet **auktorisering** inkluderar du följande egenskaper:

```json
{
   "type": "ClientCertificate",
   "password": "<password>",
   "pfx": "<long-pfx-key>"
} 
```

| Egenskap | Krävs | Beskrivning |
| -------- | -------- | ----------- |
| `type` | Ja | Autentiseringstypen. För TLS/SSL-klientcertifikat måste värdet vara `ClientCertificate` . |
| `password` | Inga | Lösen ordet för att komma åt klient certifikatet (PFX-filen) |
| `pfx` | Ja | Det Base64-kodade innehållet i klient certifikatet (PFX-filen) |
||||

<a name="basic"></a>

#### <a name="basic-authentication"></a>Grundläggande autentisering

Du kan använda grundläggande autentisering, till exempel användar namn och lösen ord för att validera inkommande begär Anden från din Logic app till din webbapp eller API-app. Grundläggande autentisering är ett vanligt mönster, och du kan använda den här autentiseringen på alla språk som används för att bygga webbappen eller API-appen.

I avsnittet **auktorisering** inkluderar du följande egenskaper:

```json
{
   "type": "Basic",
   "username": "<username>",
   "password": "<password>"
}
```

| Egenskap | Krävs | Beskrivning | 
| -------- | -------- | ----------- | 
| typ | Ja | Autentiseringstypen som du vill använda. För grundläggande autentisering måste värdet vara `Basic` . | 
| användarnamn | Ja | Det användar namn som du vill använda för autentisering | 
| password | Ja | Det lösen ord som du vill använda för autentisering | 
|||| 

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>Azure Active Directory autentisering genom kod

Som standard ger Azure AD-autentiseringen som du aktiverar i Azure Portal inte detaljerade auktoriseringar. Den här autentiseringen kan till exempel låsa ditt API till bara en speciell klient, inte en speciell användare eller app. 

Om du vill begränsa API-åtkomst till din Logic app via kod extraherar du rubriken som har JSON-webbtoken (JWT). Kontrol lera anroparens identitet och avvisa begär Anden som inte matchar.

<!-- Going further, to implement this authentication entirely in your own code, 
and not use the Azure portal, learn how to 
[authenticate with on-premises Active Directory in your Azure app](../app-service/overview-authentication-authorization.md).

To create an application identity for your logic app and use that identity to call your API, 
you must follow the previous steps. -->

## <a name="next-steps"></a>Nästa steg

* [Distribuera och anropa anpassade API: er från Logic app-arbetsflöden](../logic-apps/logic-apps-custom-api-host-deploy-call.md)
