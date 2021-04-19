---
title: Anpassa användargränssnittet med HTML-mallar
titleSuffix: Azure AD B2C
description: Lär dig hur du anpassar användargränssnittet med HTML-mallar för dina program som använder Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8f9f6dc1abd08c5e53f3d44a8f6ec1b3e20786ed
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717449"
---
# <a name="customize-the-user-interface-with-html-templates-in-azure-active-directory-b2c"></a>Anpassa användargränssnittet med HTML-mallar i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Anpassning och anpassning av användargränssnittet som Azure Active Directory B2C (Azure AD B2C) visar för dina kunder hjälper till att ge en sömlös användarupplevelse i ditt program. Exempel på sådana funktioner är registrering, inloggning, profilredigering och återställning av lösenord. I den här artikeln introduceras metoderna för anpassning av användargränssnitt. 

> [!TIP]
> Om du bara vill ändra banderollslogotypen, bakgrundsbilden och bakgrundsfärgen på dina användarflödessidor kan du prova [företagsprofilfunktionen.](customize-ui.md)

## <a name="custom-html-and-css-overview"></a>Översikt över anpassad HTML och CSS

Azure AD B2C kör kod i kundens webbläsare med hjälp av [Resursdelning för korsande ursprung (CORS).](https://www.w3.org/TR/cors/) Vid körning läses innehåll in från en URL som du anger i ditt användarflöde eller anpassade princip. Varje sida i användarupplevelsen läser in sitt innehåll från den URL som du anger för sidan. När innehållet har lästs in från din URL sammanfogas det med ett HTML-fragment som infogats av Azure AD B2C och sedan visas sidan för kunden.

![Anpassad sidinnehållsmarginal](./media/customize-ui-with-html/html-content-merging.png)

### <a name="custom-html-page-content"></a>Anpassat HTML-sidinnehåll

Skapa en HTML-sida med din egen anpassning för att hantera ditt anpassade sidinnehåll. Den här sidan kan vara en `*.html` statisk sida eller en dynamisk sida som .NET, Node.js eller PHP.

Ditt anpassade sidinnehåll kan innehålla alla HTML-element, inklusive CSS och JavaScript, men kan inte innehålla osäkra element som iframes. Det enda nödvändiga elementet är ett div-element `id` med inställt på `api` , till exempel det här på `<div id="api"></div>` HTML-sidan.

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Product Brand Name</title>
</head>
<body>
    <div id="api"></div>
</body>
</html>
```

#### <a name="customize-the-default-azure-ad-b2c-pages"></a>Anpassa standardsidorna Azure AD B2C sidan

I stället för att skapa ditt anpassade sidinnehåll från grunden kan du anpassa Azure AD B2C standardsidans innehåll.

I följande tabell visas standardsidans innehåll som tillhandahålls av Azure AD B2C. Ladda ned filerna och använd dem som utgångspunkt för att skapa dina egna anpassade sidor.

| Standardsida | Description | Innehållsdefinitions-ID<br/>(endast anpassad princip) |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Felsida**. Den här sidan visas när ett undantag eller ett fel påträffas. | *api.error* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Självinfogade sida**. Använd den här filen som ett anpassat sidinnehåll för en sida för registrering av sociala konton, en lokal kontoindelningssida, en inloggningssida för ett lokalt konto, lösenordsåterställning med mera. Formuläret kan innehålla olika inmatningskontroller, till exempel: en textinmatningsruta, en lösenordsinmatningsruta, en alternativknapp, listrutor med enkelval och flervalskryssrutor. | *api.localaccountsignin*, *api.localaccountsignup*, *api.localaccountpasswordreset*, *api.selfasserted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Sidan multifaktorautentisering**. På den här sidan kan användarna verifiera sina telefonnummer (via sms eller röst) under registrering eller inloggning. | *api.phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Profiluppdateringssidan**. Den här sidan innehåller ett formulär som användarna kan komma åt för att uppdatera sin profil. Den här sidan liknar sidan för registrering av sociala konton, förutom fälten för lösenordsinmatning. | *api.selfasserted.profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Enhetlig sida för registrering eller inloggning.** Den här sidan hanterar användarin- och inloggningsprocessen. Användare kan använda företagsidentitetsproviders, sociala identitetsproviders som Facebook eller Google+, eller lokala konton. | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>Vara värd för sidinnehållet

När du använder egna HTML- och CSS-filer för att anpassa användargränssnittet ska du vara värd för ditt gränssnittsinnehåll på alla offentligt tillgängliga HTTPS-slutpunkter som stöder CORS. Till exempel [Azure Blob Storage,](../storage/blobs/storage-blobs-introduction.md) [Azure App Services,](../app-service/index.yml)webbservrar, CDN,AWS S3 eller fildelningssystem.

## <a name="guidelines-for-using-custom-page-content"></a>Riktlinjer för att använda anpassat sidinnehåll

- Använd en absolut URL när du inkluderar externa resurser som media, CSS och JavaScript-filer i HTML-filen.
- Med [sidlayout version](page-layout.md) 1.2.0 och senare kan du lägga till attributet i HTML-taggarna för att styra `data-preload="true"` inläsningsordningen för CSS och JavaScript. Med `data-preload="true"` skapas sidan innan den visas för användaren. Det här attributet hjälper till att förhindra att sidan "toröjs" genom att css-filen förinstalleras, utan att den icke-formaterade HTML-koden visas för användaren. Följande HTML-kodfragment visar användningen av `data-preload` taggen.
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- Vi rekommenderar att du börjar med standardsidans innehåll och skapar ovanpå det.
- Du kan [inkludera JavaScript](javascript-and-page-layout.md) i ditt anpassade innehåll.
- Webbläsarversioner som stöds är:
  - Internet Explorer 11, 10 och Microsoft Edge
  - Begränsat stöd för Internet Explorer 9 och 8
  - Google Chrome 42.0 och högre
  - Mozilla Firefox 38.0 och högre
  - Safari för iOS och macOS, version 12 och senare
- På grund av säkerhetsbegränsningar Azure AD B2C inte `frame` `iframe` HTML-element, eller `form` .

## <a name="localize-content"></a>Lokalisera innehåll

Du lokaliserar HTML-innehållet genom att aktivera [språkanpassning i](language-customization.md) din Azure AD B2C klientorganisation. Genom att aktivera den här Azure AD B2C kan du vidarebefordra OpenID Connect-parametern `ui_locales` till slutpunkten. Innehållsservern kan använda den här parametern för att tillhandahålla språkspecifika HTML-sidor.

Innehållet kan hämtas från olika platser baserat på de språk som används. I din CORS-aktiverade slutpunkt ställer du in en mappstruktur som är värd för innehåll för specifika språk. Du anropar rätt om du använder jokertecknet `{Culture:RFC5646}` .

Din anpassade sid-URI kan till exempel se ut så här:

```http
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

Du kan läsa in sidan på franska genom att hämta innehåll från:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="custom-page-content-walkthrough"></a>Genomgång av anpassat sidinnehåll

Här är en översikt över processen:

1. Förbered en plats som värd för ditt anpassade sidinnehåll (en offentligt tillgänglig, CORS-aktiverad HTTPS-slutpunkt).
1. Ladda ned och anpassa en standardinnehållsfil för sidor, till exempel `unified.html` .
1. Publicera ditt anpassade sidinnehåll till din offentligt tillgängliga HTTPS-slutpunkt.
1. Ange resursdelning för korsande ursprung (CORS) för din webbapp.
1. Peka principen på din anpassade principinnehålls-URI.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

### <a name="1-create-your-html-content"></a>1. Skapa HTML-innehåll

Skapa ett anpassat sidinnehåll med produktens varumärkesnamn i rubriken.

1. Kopiera följande HTML-kodfragment. Det är välformad HTML5 med ett tomt element som kallas *\<div id="api"\>\</div\>* som finns i *\<body\>* taggarna. Det här elementet anger Azure AD B2C innehåll ska infogas.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. Klistra in det kopierade kodfragmentet i en textredigerare
1. Använd CSS för att formatera de gränssnittselement Azure AD B2C infogar på sidan. I följande exempel visas en enkel CSS-fil som även innehåller inställningar för de INMATADE HTML-elementen:

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

1.  Spara filen som *customize-ui.html*.

> [!NOTE]
> HTML-formulärelement tas bort på grund av säkerhetsbegränsningar om du använder login.microsoftonline.com. Om du vill använda HTML-formulärelement i ditt anpassade HTML-innehåll använder [du b2clogin.com](b2clogin.md).

### <a name="2-create-an-azure-blob-storage-account"></a>2. Skapa ett Azure Blob Storage-konto

I den här artikeln använder vi Azure Blob Storage som värd för vårt innehåll. Du kan välja att vara värd för ditt innehåll på en webbserver, men du [måste aktivera CORS på webbservern](https://enable-cors.org/server.html).

Utför följande steg för att lagra HTML-innehåll i Blob Storage:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. På **navmenyn** väljer du Nytt   >    >  **lagringskonto.**
1. Välj en **prenumeration** för ditt lagringskonto.
1. Skapa en **resursgrupp** eller välj en befintlig.
1. Ange ett unikt **namn för** ditt lagringskonto.
1. Välj geografisk **plats för** ditt lagringskonto.
1. **Distributionsmodellen** kan förbli **Resource Manager**.
1. **Prestanda** kan vara **kvar som Standard.**
1. Ändra **Typ av konto** till Blob **Storage**.
1. **Replikeringen** kan förbli **RA-GRS**.
1. **Åtkomstnivån** kan vara fortsatt **varm.**
1. Välj **Granska + skapa för** att skapa lagringskontot.
    När distributionen är klar öppnas **sidan Lagringskonto** automatiskt.

#### <a name="21-create-a-container"></a>2.1 Skapa en container

Utför följande steg för att skapa en offentlig container i Blob Storage:

1. Under **Blob Service** på den vänstra menyn väljer du **Blobar**.
1. Välj **+Container**.
1. I **Namn** anger du *roten*. Namnet kan vara ett namn som du väljer, till exempel *contoso*, men vi använder *roten* i det här exemplet för enkelhetens skull.
1. För **Offentlig åtkomstnivå** väljer du **Blob** och sedan **OK.**
1. Välj **rot för** att öppna den nya containern.

#### <a name="22-upload-your-custom-page-content-files"></a>2.2 Ladda upp dina anpassade sidinnehållsfiler

1. Välj **Överför**.
1. Välj mappikonen bredvid **Välj en fil**.
1. Gå till och välj **customize-ui.html**, som du skapade tidigare i avsnittet Anpassning av sidgränssnitt.
1. Om du vill ladda upp till en undermapp expanderar du **Avancerat** och anger ett mappnamn i **Ladda upp till mapp**.
1. Välj **Överför**.
1. Välj den **customize-ui.htmsom** du laddade upp.
1. Till höger om **URL-textrutan** väljer du ikonen **Kopiera till Urklipp** för att kopiera URL:en till Urklipp.
1. I webbläsaren navigerar du till den URL som du kopierade för att kontrollera att bloben som du laddade upp är tillgänglig. Om det inte går att komma åt, till exempel om det uppstår ett fel, kontrollerar du `ResourceNotFound` att containeråtkomsttypen är inställd på **blob**.

### <a name="3-configure-cors"></a>3. Konfigurera CORS

Konfigurera Blob Storage för resursdelning mellan ursprung genom att utföra följande steg:

1. I menyn väljer du **CORS**.
1. För **Tillåtna ursprung** anger du `https://your-tenant-name.b2clogin.com` . Ersätt `your-tenant-name` med namnet på din Azure AD B2C klientorganisation. Till exempel `https://fabrikam.b2clogin.com`. Använd alla gemener när du anger ditt klientnamn.
1. För **Tillåtna metoder** väljer du både `GET` och `OPTIONS` .
1. För **Tillåtna rubriker anger** du en asterisk (*).
1. För **Exponerade rubriker** anger du en asterisk (*).
1. För **Maxålder** anger du 200.
1. Välj **Spara**.

#### <a name="31-test-cors"></a>3.1 Testa CORS

Kontrollera att du är redo genom att utföra följande steg:

1. Upprepa steget konfigurera CORS. För **Tillåtna ursprung anger** du `https://www.test-cors.org`
1. Gå till [www.test-cors.org](https://www.test-cors.org/) 
1. I rutan **Fjärr-URL** klistrar du in HTML-filens URL. Till exempel `https://your-account.blob.core.windows.net/root/azure-ad-b2c/unified.html`
1. Välj **Skicka begäran.**
    Resultatet bör vara `XHR status: 200` . 
    Om du får ett felmeddelande kontrollerar du att CORS-inställningarna är korrekta. Du kan också behöva rensa webbläsarens cacheminne eller öppna en privat webbläsarsession genom att trycka på Ctrl+Skift+P.

::: zone pivot="b2c-user-flow"

### <a name="4-update-the-user-flow"></a>4. Uppdatera användarflödet

1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **Användarflöden** och välj sedan det *B2C_1_signupsignin1 användarflödet.*
1. Välj **Sidlayouter** och klicka **sedan** på Ja för Använd  anpassat **sidinnehåll** under sidan Enhetlig registrering eller inloggning.
1. I **Anpassad sid-URI** anger du URI:en *förcustom-ui.html-filen* som du antecknade tidigare.
1. Längst upp på sidan väljer du **Spara**.

### <a name="5-test-the-user-flow"></a>5. Testa användarflödet

1. I din Azure AD B2C väljer du **Användarflöden och** väljer B2C_1_signupsignin1 *användarflöde.*
1. Längst upp på sidan klickar du på **Kör användarflöde**.
1. Klicka på **knappen Kör användarflöde.**

Du bör se en sida som liknar följande exempel med elementen som är centrerade baserat på CSS-filen som du skapade:

![Webbläsare som visar sidan för registrering eller inloggning med anpassade gränssnittselement](./media/customize-ui-with-html/run-now.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="4-modify-the-extensions-file"></a>4. Ändra filtillägget

Om du vill konfigurera anpassning av användargränssnittet **kopierar du ContentDefinition** och dess underordnade element från basfilen till filtillägget.

1. Öppna basfilen för principen. Till exempel <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em> . Den här basfilen är en av de principfiler som ingår i det anpassade principstartpaketet, som du bör ha fått i kravpaketet Kom [igång med anpassade principer.](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)
1. Sök efter och kopiera hela innehållet i **elementet ContentDefinitions.**
1. Öppna tilläggsfilen. Du kan till *exempelTrustFrameworkExtensions.xml*. Sök efter **elementet BuildingBlocks.** Om elementet inte finns lägger du till det.
1. Klistra in hela innehållet i **elementet ContentDefinitions** som du kopierade som underordnad elementet **BuildingBlocks.**
1. Sök efter **contentDefinition-elementet** som innehåller `Id="api.signuporsignin"` den XML som du kopierade.
1. Ändra värdet för **LoadUri till** URL:en för HTML-filen som du laddade upp till lagringen. Till exempel `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Din anpassade princip bör se ut som följande kodfragment:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Spara filtillägget.

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. Ladda upp och testa din uppdaterade anpassade princip

#### <a name="51-upload-the-custom-policy"></a>5.1 Ladda upp den anpassade principen

1. Kontrollera att du använder katalogen som innehåller din Azure AD B2C klient genom att välja filtret **Katalog +** prenumeration på den översta menyn och välja den katalog som innehåller din klientorganisation.
1. Sök efter och välj **Azure AD B2C**.
1. Under **Principer** väljer du **Identity Experience Framework**.
1. Välj **Ladda upp anpassad princip.**
1. Ladda upp filtillägget som du ändrade tidigare.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5.2 Testa den anpassade principen med hjälp av **Kör nu**

1. Välj den princip som du laddade upp och välj sedan **Kör nu.**
1. Du bör kunna registrera dig med en e-postadress.

## <a name="configure-dynamic-custom-page-content-uri"></a>Konfigurera dynamisk URI för anpassat sidinnehåll

Genom att Azure AD B2C anpassade principer kan du skicka en parameter i URL-sökvägen eller en frågesträng. Genom att skicka parametern till HTML-slutpunkten kan du dynamiskt ändra sidinnehållet. Du kan till exempel ändra bakgrundsbilden på registrerings- eller inloggningssidan för Azure AD B2C baserat på en parameter som du skickar från ditt webb- eller mobilprogram. Parametern kan vara vilken [anspråks matchare](claim-resolver-overview.md)som helst, till exempel program-ID, språk-ID eller anpassad frågesträngsparameter, till exempel `campaignId` .

### <a name="sending-query-string-parameters"></a>Skicka frågesträngsparametrar

Om du vill skicka frågesträngsparametrar lägger [du till ett -element i](relyingparty.md)den förlitande `ContentDefinitionParameters` partsprincipen enligt nedan.

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        <Parameter Name="lang">{Culture:LanguageName}</Parameter>
        <Parameter Name="appId">{OIDC:ClientId}</Parameter>
    </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ...
</RelyingParty>
```

I innehållsdefinitionen ändrar du värdet `LoadUri` för till `https://<app_name>.azurewebsites.net/home/unified` . Din anpassade princip `ContentDefinition` bör se ut som följande kodfragment:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

När Azure AD B2C läser in sidan gör den ett anrop till webbserverslutpunkten:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>Dynamisk sidinnehålls-URI

Innehållet kan hämtas från olika platser baserat på de parametrar som används. Konfigurera en mappstruktur som värd för innehåll i din CORS-aktiverade slutpunkt. Du kan till exempel ordna innehållet i följande struktur. *Rotmapp/mapp per språk/html-filer.* Din anpassade sid-URI kan till exempel se ut så här:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C skickar ISO-koden med två bokstäver för språket, `fr` för franska:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

::: zone-end

## <a name="sample-templates"></a>Exempelmallar

Du hittar exempelmallar för anpassning av användargränssnittet här:

```bash
git clone https://github.com/azure-ad-b2c/html-templates
```

Det här projektet innehåller följande mallar:
- [Oceanblå](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/AzureBlue)
- [Slate Gray](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/MSA)
- [Klassiska](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/classic)
- [Mallresurser](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/src)

Så här använder du exemplet:

1. Klona lagringsplatsen på den lokala datorn. Välj en mallmapp `/AzureBlue` `/MSA` , eller `/classic` .
1. Ladda upp alla filer under mallmappen och `/src` mappen till Blob Storage enligt beskrivningen i föregående avsnitt.
1. Öppna sedan varje `\*.html` fil i mallmappen. Ersätt sedan alla instanser av `https://login.microsoftonline.com` URL:er med den URL som du laddade upp i steg 2. Exempel:
    
    Från:
    ```html
    https://login.microsoftonline.com/templates/src/fonts/segoeui.WOFF
    ```

    Till:
    ```html
    https://your-storage-account.blob.core.windows.net/your-container/templates/src/fonts/segoeui.WOFF
    ```
    
1. Spara filerna `\*.html` och ladda upp dem till Blob Storage.
1. Ändra nu principen och peka på HTML-filen, som nämnts tidigare.
1. Om du ser saknade teckensnitt, bilder eller CSS kontrollerar du dina referenser i tilläggsprincipen och `\*.html` filerna.

## <a name="use-company-branding-assets-in-custom-html"></a>Använda företagets varumärkestillgångar i anpassad HTML

Om du [vill använda företagets varumärkestillgångar](customize-ui.md#configure-company-branding) i en anpassad HTML-kod lägger du till följande taggar utanför `<div id="api">` taggen . Bildkällan ersätts med bakgrundsbilden och banderollslogotypen.

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [aktiverar JavaScript-kod på klientsidan.](javascript-and-page-layout.md)
