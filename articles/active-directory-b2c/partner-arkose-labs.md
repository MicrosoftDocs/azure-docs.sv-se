---
title: Självstudie för att konfigurera Azure Active Directory B2C med Arkose Labs
titleSuffix: Azure AD B2C
description: Självstudie för att konfigurera Azure Active Directory B2C med Arkose Labs för att identifiera riskfyllda och bedrägliga användare
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/18/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 46f117b13909c2d9624b88e9f5d9a62c4c646e51
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500300"
---
# <a name="tutorial-configure-arkose-labs-with-azure-active-directory-b2c"></a>Självstudie: Konfigurera Arkose Labs med Azure Active Directory B2C

I den här exempel självstudien lär du dig att integrera Azure Active Directory (AD) B2C-autentisering med [Arkose Labs](https://www.arkoselabs.com/). Arkose Labs hjälper organisationer mot bot-attacker, konto upptagnings attacker och falska konto öppningar.  

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du:

- En Azure-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).

- [En Azure AD B2C klient](tutorial-create-tenant.md) som är länkad till din Azure-prenumeration.

- Ett [Arkose Labs](https://www.arkoselabs.com/book-a-demo/) -konto.

## <a name="scenario-description"></a>Scenariobeskrivning

Arkose Labs-integrering innehåller följande komponenter:

- **Arkose Labs** – en tjänst för bedrägeri och missbruk för att skydda mot robotar och annan automatiserad missbruk.

- **Azure AD B2C-registrerat användar flöde** – den registrerings upplevelse som används av Arkose Labs-tjänsten. Använder anpassad HTML och Java Script och API-kopplingar för att integrera med Arkose Labs-tjänsten.

- **Azure Functions** – API-slutpunkt som är värd för dig som fungerar med funktionen API-kopplingar. Detta API ansvarar för att utföra verifiering på Server sidan av Arkose Labs-sessionstoken.

I följande diagram beskrivs hur Arkose Labs integreras med Azure AD B2C.

![Bild som visar arkitektur diagram för Arkose Labs](media/partner-arkose-labs/arkose-labs-architecture-diagram.png)

| Steg  | Beskrivning |
|---|---|
|1     | En användare registrerar sig och skapar ett konto. När användaren väljer Skicka, visas en tvingande Arkose Labs-utmaning.         |
|2     |  När användaren har slutfört utmaningen skickar Azure AD B2C statusen till Arkose Labs för att generera en token. |
|3     |  Arkose Labs genererar en token och skickar tillbaka den till Azure AD B2C.   |
|4     |  Azure AD B2C anropar ett mellanliggande webb-API för att skicka registrerings formuläret.      |
|5     |  Det mellanliggande webb-API: t skickar registrerings formuläret till Arkose Lab för verifiering av token.    |
|6     | Arkose Lab bearbetar och skickar verifierings resultaten tillbaka till mellanliggande webb-API.|
|7     | Det mellanliggande webb-API: et skickar ett lyckat eller misslyckat resultat från utmaningen till Azure AD B2C. |
|8     | Om utmaningen har slutförts skickas ett registrerings formulär till Azure AD B2C och Azure AD B2C Slutför autentiseringen.|

## <a name="onboard-with-arkose-labs"></a>Publicera med Arkose Labs

1. Kontakta [Arkose](https://www.arkoselabs.com/book-a-demo/) och skapa ett konto.

2. När kontot har skapats går du till https://dashboard.arkoselabs.com/login  

3. I instrument panelen navigerar du till plats inställningar för att hitta din offentliga nyckel och privata nyckel. Den här informationen kommer att behövas senare för att konfigurera Azure AD B2C. Värdena för offentliga och privata nycklar kallas `ARKOSE_PUBLIC_KEY` och `ARKOSE_PRIVATE_KEY` i [exempel koden](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose).

## <a name="integrate-with-azure-ad-b2c"></a>Integrera med Azure AD B2C

### <a name="part-1--create-a-arkosesessiontoken-custom-attribute"></a>Del 1 – Skapa ett anpassat ArkoseSessionToken-attribut

Följ dessa steg om du vill skapa ett anpassat attribut:  

1. Gå till **Azure Portal**  >  **Azure AD B2C**

2. Välj **användarattribut**

3. Välj **Lägg till**

4. Ange **ArkoseSessionToken** som attributnamn

5. Välj **Skapa**

Läs mer om [anpassade attribut](https://docs.microsoft.com/azure/active-directory-b2c/user-flow-custom-attributes?pivots=b2c-user-flow).

### <a name="part-2---create-a-user-flow"></a>Del 2 – Skapa ett användar flöde

Användar flödet kan vara antingen för **registrering** och **inloggning** eller bara **registrering**. Användar flödet Arkose Labs visas bara vid registrering.

1. Se [anvisningarna](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) för att skapa ett användar flöde. Om du använder ett befintligt användar flöde måste det vara av den rekommenderade versions typen **(nästa generations förhands granskning)** .

2. I inställningarna för användar flödet går du till **användarattribut** och väljer **ArkoseSessionToken** -anspråket.

![Bild som visar hur du väljer anpassade attribut](media/partner-arkose-labs/select-custom-attribute.png)

### <a name="part-3---configure-custom-html-javascript-and-page-layouts"></a>Del 3 – Konfigurera anpassad HTML, Java Script och sidlayouter

Gå till det angivna [HTML-skriptet](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html). Filen innehåller en HTML-mall med JavaScript- `<script>` taggar som gör tre saker:

1. Läs in Arkose Labs-skriptet, som återger widgeten för Arkose Labs och utför verifiering av Arkose Labs på klient sidan.

2. Dölj `extension_ArkoseSessionToken` indata-elementet och etiketten, som motsvarar det `ArkoseSessionToken` anpassade attributet, från användar gränssnittet som visas för användaren.

3. När en användare slutför Arkose Labs-utmaningen verifierar Arkose Labs användarens svar och genererar en token. Återanropet `arkoseCallback` i det anpassade JavaScript-kommandot anger värdet `extension_ArkoseSessionToken` till det genererade värdet för token. Det här värdet skickas till API-slutpunkten enligt beskrivningen i nästa avsnitt.

    Läs [den här artikeln](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/214176229/Standard+Setup) om du vill veta mer om verifiering på klient sidan i Arkose Labs.

Följ de steg som nämns för att använda anpassad HTML och Java Script för ditt användar flöde.

1. Ändra [selfAsserted.html](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html) -filen så att `<ARKOSE_PUBLIC_KEY>` matchar det värde som du skapade för verifiering på klient sidan och används för att läsa in Arkose Labs-skriptet för ditt konto.

2. Placera HTML-sidan på en CORS-aktiverad webb slut punkt (Cross-Origin Resource Sharing). [Skapa ett Azure Blob Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal) och [Konfigurera CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services).

  >[!NOTE]
  >Om du har en egen anpassad HTML-kod kopierar du och klistrar in `<script>` elementen på HTML-sidan.

3. Följ dessa steg om du vill konfigurera sidlayouter

   a. Från Azure Portal går du till **Azure AD B2C**

   b. Navigera till **användar flöden** och välj ditt användar flöde

   c. Välj **sidlayouter**

   d. Välj **Sidlayout för lokalt konto**

   e. Växla **Använd anpassat sid innehåll** till **Ja**

   f. Klistra in URI: n där din anpassade HTML-kod är i **användning av anpassat sid innehåll**

   ex. Om du använder sociala identitets leverantörer upprepar du **steg e** och **f** för **registrerings sidan för sociala konton** .

   ![bild som visar sidlayouter](media/partner-arkose-labs/page-layouts.png)

4. Från ditt användar flöde går du till **Egenskaper** och väljer **Aktivera Java Script** -Tvingad Sidlayout (för hands version). Läs den här [artikeln](https://docs.microsoft.com/azure/active-directory-b2c/javascript-and-page-layout?pivots=b2c-user-flow) om du vill veta mer.

### <a name="part-4---create-and-deploy-your-api"></a>Del 4 – Skapa och distribuera ditt API

Installera [Azure Functions-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för Visual Studio Code.

>[!Note]
>De steg som beskrivs i det här avsnittet förutsätter att du använder Visual Studio Code för att distribuera Azure-funktionen. Du kan också använda Azure Portal, Terminal eller kommando tolk eller någon annan kod redigerare för att distribuera.

#### <a name="run-the-api-locally"></a>Kör API lokalt

1. Navigera till Azure-tillägget i Visual Studio Code i det vänstra navigerings fältet. Välj **lokal projektmapp** som representerar din lokala Azure-funktion.

2. Tryck på **F5** eller Använd **fel** söknings  >  menyn starta fel **sökning** för att starta fel söknings programmet och koppla till den Azure Functions värden. Det här kommandot använder automatiskt den enskilda fel söknings konfigurationen som Azure Function skapade.

3. Azure Function-tillägget genererar automatiskt några filer för lokal utveckling, installerar beroenden och installerar funktionen Core-verktyg om den inte redan finns. Dessa verktyg hjälper till med fel söknings upplevelsen.

4. Utdata från funktionen Core-verktyget visas i Visual Studio Codes **Terminal** -panel. När värden har startat, **Alt + klicka på** den lokala URL som visas i utdata för att öppna webbläsaren och köra funktionen. I Azure Functions Explorer högerklickar du på funktionen för att se URL: en för den lokalt värdbaserade funktionen.

Upprepa steg 1 till 4 för att distribuera om den lokala instansen under testningen.

#### <a name="add-environment-variables"></a>Lägg till miljövariabler

Det här exemplet skyddar webb-API-slutpunkten med hjälp av [http Basic-autentisering](https://tools.ietf.org/html/rfc7617).

Användar namn och lösen ord lagras som miljövariabler och inte som en del av lagrings platsen. Mer information finns i [local.settings.jspå](https://docs.microsoft.com/azure/azure-functions/functions-run-local?tabs=macos%2Ccsharp%2Cbash#local-settings-file) fil.

1. Skapa en local.settings.jspå en fil i rotmappen

2. Kopiera och klistra in koden nedan till filen:

```
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "BASIC_AUTH_USERNAME": "<USERNAME>",
    "BASIC_AUTH_PASSWORD": "<PASSWORD>",
    "ARKOSE_PRIVATE_KEY": "<ARKOSE_PRIVATE_KEY>",
    "B2C_EXTENSIONS_APP_ID": "<B2C_EXTENSIONS_APP_ID>"
  }
}
```
Värdena för **BASIC_AUTH_USERNAME** och **BASIC_AUTH_PASSWORD** ska vara de autentiseringsuppgifter som används för att autentisera API-anropet till Azure-funktionen. Välj önskade värden.

`<ARKOSE_PRIVATE_KEY>`Är hemligheten på Server sidan som du skapade i Arkose Labs-tjänsten. Den används för att anropa [API för Arkose Labs på Server sidan](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/266214758/Server-Side+Instructions) för att verifiera värdet för den `ArkoseSessionToken` som genereras av klient delen.

`<B2C_EXTENSIONS_APP_ID>`Är program-ID: t för appen som används av Azure AD B2C för att lagra anpassade attribut i katalogen. Du hittar det här program-ID: t genom att gå till Appregistreringar, söka efter B2C-Extensions-app och kopiera program-ID: t (klient) från **översikts** fönstret. Ta bort `-` tecknen.

![Bild som visar Sök efter app-ID](media/partner-arkose-labs/search-app-id.png)

#### <a name="deploy-the-application-to-the-web"></a>Distribuera programmet till webben

1. Följ de steg som beskrivs i [den här](https://docs.microsoft.com/azure/javascript/tutorial-vscode-serverless-node-04) guiden för att distribuera Azure-funktionen till molnet. Kopiera slut punktens webb-URL för din Azure-funktion.

2. När du har distribuerat väljer du alternativet **överför inställningar** . De kommer att överföra miljövariablerna till [program inställningarna](https://docs.microsoft.com/azure/azure-functions/functions-develop-vs-code?tabs=csharp#application-settings-in-azure) för App Service. Dessa program inställningar kan också konfigureras eller [hanteras via Azure Portal.](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings)

I [den här artikeln](https://docs.microsoft.com/azure/azure-functions/functions-develop-vs-code?tabs=csharp#republish-project-files) får du lära dig mer om Visual Studio Code development för Azure Functions.

#### <a name="configure-and-enable-the-api-connector"></a>Konfigurera och aktivera API-anslutningen

[Skapa en API-anslutning](https://docs.microsoft.com/azure/active-directory-b2c/add-api-connector) och aktivera den för ditt användar flöde. Din API Connector-konfiguration bör se ut så här:

![Bild som visar hur du konfigurerar API-koppling](media/partner-arkose-labs/configure-api-connector.png)

- **Slut punkts-URL** – är funktionen URL som du kopierade tidigare medan du distribuerade Azure function.

- **Användar namn och lösen ord** – är det användar namn och lösen ord som du definierade som miljövariabler tidigare.

Om du vill aktivera API-anslutningen går du till inställningarna för **API-anslutaren** för ditt användar flöde och väljer den API-anslutning som ska anropas på **innan du skapar användar** steget. Detta kommer att anropa API: et när en användare väljer **skapa** i registrerings flödet. API: n kommer att utföra en verifiering på Server sidan av `ArkoseSessionToken` värdet, vilket angavs av Arkose-widgeten `arkoseCallback` .

![Bild som visar aktivering av API-koppling](media/partner-arkose-labs/enable-api-connector.png)

## <a name="test-the-user-flow"></a>Testa användar flödet

1. Öppna Azure AD B2C-klienten och under principer väljer du **användar flöden**.

2. Välj ditt tidigare skapade användar flöde.

3. Välj **Kör användar flöde** och välj inställningarna:

   a. Program: Välj den registrerade appen (exemplet är JWT)

   b. Svars-URL: Välj omdirigerings-URL

   c. Välj **Kör användar flöde**.

4. Gå igenom registrerings flödet och skapa ett konto

5. Logga ut

6. Gå igenom inloggnings flödet  

7. Ett Arkose Labs-pussel visas när du har valt **Fortsätt**.

## <a name="additional-resources"></a>Ytterligare resurser

- [Exempel koder](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose) för Azure AD B2C registrering av användar flöde

- [Anpassade principer i Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Kom igång med anpassade principer i Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
