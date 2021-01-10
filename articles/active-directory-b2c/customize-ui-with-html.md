---
title: Anpassa användargränssnittet
titleSuffix: Azure AD B2C
description: Lär dig hur du anpassar användar gränssnittet för dina program som använder Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 4a789574b736eb22bd8d13fcf1a9facec5e241c9
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2021
ms.locfileid: "98058675"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Anpassa användar gränssnittet i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Anpassning och anpassning av användar gränssnittet som Azure Active Directory B2C (Azure AD B2C) visar för dina kunder och ger en smidig användar upplevelse i ditt program. Dessa upplevelser omfattar registrering, inloggning, profil redigering och återställning av lösen ord. I den här artikeln beskrivs metoder för anpassning av användar gränssnitt (UI). 

> [!TIP]
> Om du bara vill ändra banderollens logo typ, bakgrunds bild och bakgrunds färg för dina användar flödes sidor kan du prova funktionen för [företags anpassning](company-branding.md) .

## <a name="custom-html-and-css-overview"></a>Översikt över anpassad HTML och CSS

Azure AD B2C kör kod i kundens webbläsare genom att använda [resurs delning mellan ursprung (CORS)](https://www.w3.org/TR/cors/). Vid körning läses innehållet in från en URL som du anger i ditt användar flöde eller en anpassad princip. Varje sida i användar gränssnittet läser in innehållet från den URL som du anger för sidan. När innehållet har lästs in från din URL sammanfogas det med ett HTML-fragment som infogats av Azure AD B2C och sedan visas sidan för kunden.

![Anpassad marginal för sid innehåll](./media/customize-ui-with-html/html-content-merging.png)

### <a name="custom-html-page-content"></a>Innehåll för anpassad HTML-sida

Skapa en HTML-sida med ditt eget varumärke för att hantera ditt anpassade sid innehåll. Den här sidan kan vara en statisk `*.html` sida eller en dynamisk sida som .net, Node.js eller php.

Ditt anpassade sid innehåll kan innehålla alla HTML-element, inklusive CSS och Java Script, men kan inte innehålla osäkra element som iframes. Det enda obligatoriska elementet är ett div-element med `id` inställt på `api` , till exempel det här `<div id="api"></div>` i din HTML-sida.

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

#### <a name="customize-the-default-azure-ad-b2c-pages"></a>Anpassa standard Azure AD B2C sidor

I stället för att skapa ett anpassat sid innehåll från grunden kan du anpassa Azure AD-B2C's standard sid innehåll.

I följande tabell visas standard sid innehållet som tillhandahålls av Azure AD B2C. Hämta filerna och Använd dem som utgångs punkt för att skapa egna anpassade sidor.

| Standard sida | Beskrivning | ID för innehålls definition<br/>(endast anpassad princip) |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Felsida**. Den här sidan visas när ett undantag eller ett fel påträffas. | *API. error* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Själv kontrollerad sida**. Använd den här filen som ett anpassat sid innehåll för registrerings sidan för ett socialt konto, en registrerings sida för lokalt konto, en inloggnings sida för lokalt konto, lösen ords återställning med mera. Formuläret kan innehålla olika inmatnings kontroller, t. ex. en text inmatnings ruta, en ruta för lösen ords inmatning, en alternativ knapp, en listruta med flera val och kryss rutor med flera val. | *API. localaccountsignin*, *API. localaccountsignup*, *API. localaccountpasswordreset*, *API. selfasserted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Sidan Multi-Factor Authentication**. På den här sidan kan användarna verifiera sina telefonnummer (med hjälp av text eller röst) under registreringen eller inloggningen. | *API. phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Sidan profil uppdatering**. Den här sidan innehåller ett formulär som användarna kan använda för att uppdatera sin profil. Den här sidan liknar registrerings sidan för sociala konton, förutom fälten för lösen ords inmatning. | *API. selfasserted. profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Enhetlig registrerings-eller inloggnings sida**. Den här sidan hanterar användarens registrerings-och inloggnings process. Användare kan använda företags identitets leverantörer, sociala identitets leverantörer som Facebook, Google + eller lokala konton. | *API. signuporsignin* |

## <a name="hosting-the-page-content"></a>Värd för sid innehållet

När du använder egna HTML-och CSS-filer för att anpassa användar gränssnittet, ska du vara värd för ditt GRÄNSSNITTs innehåll på en offentligt tillgänglig HTTPS-slutpunkt som stöder CORS. Till exempel [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md), [Azure App tjänster](../app-service/index.yml), webb servrar, CDN, AWS S3 eller fildelnings system.

## <a name="guidelines-for-using-custom-page-content"></a>Rikt linjer för att använda anpassat sid innehåll

- Använd en absolut URL när du inkluderar externa resurser som media, CSS och JavaScript-filer i HTML-filen.
- Med hjälp av 1.2.0 [och senare kan](page-layout.md) du lägga till `data-preload="true"` attributet i dina HTML-taggar för att kontrol lera inläsnings ordningen för CSS och Java Script. Med `data-preload="true"` konstrueras sidan innan den visas för användaren. Det här attributet förhindrar sidan från att flimra genom att i förväg läsa in CSS-filen utan att den icke-formaterade HTML-koden visas för användaren. Följande HTML-kodfragment visar hur `data-preload` taggen används.
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- Vi rekommenderar att du börjar med standard innehållet på sidan och bygger ovanpå det.
- Du kan [Inkludera Java Script](javascript-and-page-layout.md) i ditt anpassade innehåll.
- Webb läsar versioner som stöds är:
  - Internet Explorer 11, 10 och Microsoft Edge
  - Begränsat stöd för Internet Explorer 9 och 8
  - Google Chrome 42,0 och senare
  - Mozilla Firefox 38,0 och senare
  - Safari för iOS och macOS, version 12 och senare
- På grund av säkerhets begränsningar stöder Azure AD B2C inte `frame` , `iframe` eller `form` HTML-element.

## <a name="localize-content"></a>Lokalisera innehåll

Du lokaliserar ditt HTML-innehåll genom att aktivera [språk anpassning](language-customization.md) i Azure AD B2C klient organisationen. När du aktiverar den här funktionen kan Azure AD B2C vidarebefordra parametern OpenID Connect `ui_locales` till din slut punkt. Din innehålls Server kan använda den här parametern för att tillhandahålla språkspecifika HTML-sidor.

Innehållet kan hämtas från olika platser baserat på det språk som används. I din CORS-aktiverade slut punkt ställer du in en mappstruktur som värd för innehåll för vissa språk. Du anropar rätt ett om du använder jokertecknet `{Culture:RFC5646}` .

Din anpassade sid-URI kan till exempel se ut så här:

```http
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

Du kan läsa in sidan på franska genom att dra innehåll från:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="custom-page-content-walkthrough"></a>Genom gång av anpassat sid innehåll

Här är en översikt över processen:

1. Förbered en plats som ska vara värd för ditt anpassade sid innehåll (en offentligt tillgänglig, CORS-aktiverad HTTPS-slutpunkt).
1. Hämta och anpassa en standard innehålls fil för sidor, till exempel `unified.html` .
1. Publicera ditt anpassade sid innehåll till en offentligt tillgänglig HTTPS-slutpunkt.
1. Ange resurs delning mellan ursprung (CORS) för din webbapp.
1. Peka din princip till din anpassade princip innehålls-URI.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

### <a name="1-create-your-html-content"></a>1. skapa ditt HTML-innehåll

Skapa ett anpassat sid innehåll med produktens märkes namn i rubriken.

1. Kopiera följande HTML-kodfragment. Det är väl formulerad HTML5 med ett tomt element som kallas *\<div id="api"\>\</div\>* i *\<body\>* taggarna. Det här elementet anger var Azure AD B2C innehåll ska infogas.

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

1. Klistra in det kopierade kodfragmentet i en text redigerare
1. Använd CSS för att formatera de GRÄNSSNITTs element som Azure AD B2C infogar på sidan. I följande exempel visas en enkel CSS-fil som även innehåller inställningar för inmatade HTML-element för registrering:

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
> HTML-formulärets element tas bort på grund av säkerhets begränsningar om du använder login.microsoftonline.com. [Använd b2clogin.com](b2clogin.md)om du vill använda HTML-formulär element i ditt anpassade HTML-innehåll.

### <a name="2-create-an-azure-blob-storage-account"></a>2. skapa ett Azure Blob Storage-konto

I den här artikeln använder vi Azure Blob Storage för att vara värd för vårt innehåll. Du kan välja att vara värd för ditt innehåll på en webb server, men du måste [Aktivera CORS på din webb server](https://enable-cors.org/server.html).

Utför följande steg för att vara värd för HTML-innehåll i Blob Storage:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. På menyn **hubb** väljer du **nytt**  >  **lagrings**  >  **lagrings konto**.
1. Välj en **prenumeration** för ditt lagrings konto.
1. Skapa en **resurs grupp** eller Välj en befintlig.
1. Ange ett unikt **namn** för ditt lagrings konto.
1. Välj den **geografiska platsen** för ditt lagrings konto.
1. **Distributions modellen** kan vara **Resource Manager**.
1. **Prestanda** kan vara **standard**.
1. Ändra **konto typ** till **Blob Storage**.
1. **Replikeringen** kan förbli **RA-GRS**.
1. **Åtkomst nivån** kan vara **aktiv**.
1. Välj **Granska + skapa** för att skapa lagrings kontot.
    När distributionen är klar öppnas sidan **lagrings konto** automatiskt.

#### <a name="21-create-a-container"></a>2,1 Skapa en behållare

Utför följande steg för att skapa en offentlig behållare i Blob Storage:

1. Under **BLOB service** på menyn till vänster väljer du **blobbar**.
1. Välj **+ behållare**.
1. I **namn** anger du *root*. Namnet kan vara ett namn som du väljer, till exempel *contoso*, men vi använder *roten* i det här exemplet för enkelhetens skull.
1. För **offentlig åtkomst nivå** väljer du **BLOB** och sedan **OK**.
1. Välj **rot** för att öppna den nya behållaren.

#### <a name="22-upload-your-custom-page-content-files"></a>2,2 Ladda upp dina anpassade innehållsfiler för sidor

1. Välj **Överför**.
1. Välj mappikonen bredvid **Välj en fil**.
1. Navigera till och välj **customize-ui.html**, som du skapade tidigare i avsnittet sid UI anpassning.
1. Om du vill överföra till en undermapp expanderar du **Avancerat** och anger ett mappnamn i **mappen överför till**.
1. Välj **Överför**.
1. Välj den **customize-ui.html** -blob som du överförde.
1. Till höger om text rutan **URL** väljer du ikonen **Kopiera till Urklipp** för att kopiera webb adressen till Urklipp.
1. I webbläsaren navigerar du till den URL som du kopierade för att verifiera att blobben du överförde är tillgänglig. Om det inte går att komma åt, till exempel om du stöter `ResourceNotFound` på ett fel, se till att behållarens åtkomst typ är inställd på **BLOB**.

### <a name="3-configure-cors"></a>3. Konfigurera CORS

Konfigurera Blob Storage för resurs delning mellan ursprung genom att utföra följande steg:

1. I menyn väljer du **CORS**.
1. För **tillåtna ursprung** anger du `https://your-tenant-name.b2clogin.com` . Ersätt `your-tenant-name` med namnet på din Azure AD B2C-klient. Ett exempel är `https://fabrikam.b2clogin.com`. Använd små bokstäver när du anger ditt klient namn.
1. För **tillåtna metoder** väljer du både `GET` och `OPTIONS` .
1. För **tillåtna huvuden** anger du en asterisk (*).
1. För **exponerade rubriker** anger du en asterisk (*).
1. Ange 200 för **högsta ålder**.
1. Välj **Spara**.

#### <a name="31-test-cors"></a>3,1-testcors

Verifiera att du är redo genom att utföra följande steg:

1. Upprepa steget Konfigurera CORS. För **tillåtna ursprung** anger du `https://www.test-cors.org`
1. Navigera till [www.test-CORS.org](https://www.test-cors.org/) 
1. I rutan **Fjärradress** klistrar du in URL: en för HTML-filen. Till exempel `https://your-account.blob.core.windows.net/root/azure-ad-b2c/unified.html`
1. Välj **skicka begäran**.
    Resultatet bör vara `XHR status: 200` . 
    Kontrol lera att CORS-inställningarna är korrekta om du får ett fel meddelande. Du kan också behöva rensa webbläsarens cacheminne eller öppna en privat webbläsarsession genom att trycka på CTRL + SKIFT + P.

::: zone pivot="b2c-user-flow"

### <a name="4-update-the-user-flow"></a>4. uppdatera användar flödet

1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **användar flöden** och välj sedan *B2C_1_signupsignin1* användar flödet.
1. Välj **sidlayouter** och klicka sedan på **Ja** under **enhetlig registrering eller inloggnings sida** för att **använda anpassat sid innehåll**.
1. Ange URI: n för den *custom-ui.html* -fil som du tidigare spelat in i **URI för anpassad sida**.
1. Välj **Spara** längst upp på sidan.

### <a name="5-test-the-user-flow"></a>5. testa användar flödet

1. I Azure AD B2C klient väljer du **användar flöden** och väljer *B2C_1_signupsignin1* användar flödet.
1. Klicka på **Kör användar flöde** längst upp på sidan.
1. Klicka på knappen **Kör användar flöde** .

Du bör se en sida som liknar följande exempel med elementen centrerade baserat på CSS-filen som du skapade:

![Webbläsare som visar registrerings-eller inloggnings sida med anpassade GRÄNSSNITTs element](./media/customize-ui-with-html/run-now.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="4-modify-the-extensions-file"></a>4. ändra tilläggs filen

Om du vill konfigurera UI-anpassning kopierar du **ContentDefinition** och dess underordnade element från bas filen till tilläggs filen.

1. Öppna bas filen för din princip. Till exempel <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em> . Den här bas filen är en av de principfiler som ingår i den anpassade principens start paket, som du borde ha skaffat i förutsättningen, [Kom igång med anpassade principer](./custom-policy-get-started.md).
1. Sök efter och kopiera hela innehållet i **ContentDefinitions** -elementet.
1. Öppna tilläggs filen. Till exempel *TrustFrameworkExtensions.xml*. Sök efter **BuildingBlocks** -elementet. Om elementet inte finns lägger du till det.
1. Klistra in hela innehållet i **ContentDefinitions** -elementet som du kopierade som ett underordnat objekt till **BuildingBlocks** -elementet.
1. Sök efter det **ContentDefinition** -element som innehåller `Id="api.signuporsignin"` i XML-filen som du kopierade.
1. Ändra värdet för **LoadUri** till URL: en för HTML-filen som du laddade upp till lagringen. Ett exempel är `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Den anpassade principen bör se ut som följande kodfragment:

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

1. Spara tilläggs filen.

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. Ladda upp och testa din uppdaterade anpassade princip

#### <a name="51-upload-the-custom-policy"></a>5,1 Ladda upp den anpassade principen

1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Sök efter och välj **Azure AD B2C**.
1. Under **principer** väljer du **Identity Experience Framework**.
1. Välj **överför anpassad princip**.
1. Ladda upp tilläggs filen som du har ändrat tidigare.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5,2 testa den anpassade principen med hjälp av **Kör nu**

1. Välj den princip som du överförde och välj sedan **Kör nu**.
1. Du bör kunna registrera dig med hjälp av en e-postadress.

## <a name="configure-dynamic-custom-page-content-uri"></a>Konfigurera innehålls-URI för dynamiskt anpassad sida

Genom att använda Azure AD B2C anpassade principer kan du skicka en parameter i URL-sökvägen eller en frågesträng. Genom att skicka parametern till HTML-slutpunkten kan du dynamiskt ändra sidinnehållet. Du kan till exempel ändra bakgrundsbilden på registrerings- eller inloggningssidan för Azure AD B2C baserat på en parameter som du skickar från ditt webb- eller mobilprogram. Parametern kan vara alla [anspråks lösare](claim-resolver-overview.md), till exempel program-ID, språk-ID eller anpassad frågesträngparametern, till exempel `campaignId` .

### <a name="sending-query-string-parameters"></a>Parametrar för frågesträng skickas

Om du vill skicka frågesträngs parametrar i [principen för förlitande part](relyingparty.md), lägger du till ett- `ContentDefinitionParameters` element som visas nedan.

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

Ändra värdet för till i din innehålls definition `LoadUri` `https://<app_name>.azurewebsites.net/home/unified` . Den anpassade principen `ContentDefinition` bör se ut som följande kodfragment:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

När Azure AD B2C läser in sidan, gör den ett anrop till din webb server slut punkt:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>URI för dynamiskt sid innehåll

Innehållet kan hämtas från olika platser baserat på de parametrar som används. I den CORS-aktiverade slut punkten ställer du in en mappstruktur som värd för innehåll. Du kan till exempel organisera innehållet i följande struktur. Rotmapp */mapp per språk/dina HTML-filer*. Din anpassade sid-URI kan till exempel se ut så här:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C skickar ISO-koden med två bokstäver för språket `fr` franska:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

::: zone-end

## <a name="sample-templates"></a>Exempelmallar

Du hittar exempel på mallar för anpassning av användar gränssnittet här:

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

Det här projektet innehåller följande mallar:
- [Havet, blå](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [Platta, grå](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

Så här använder du exemplet:

1. Klona lagrings platsen på den lokala datorn. Välj en mall-mapp `/ocean_blue` eller `/slate_gray` .
1. Ladda upp alla filer under mappen mallar och `/assets` mappen till Blob Storage enligt beskrivningen i föregående avsnitt.
1. Sedan öppnar du varje `\*.html` fil i roten för antingen `/ocean_blue` eller `/slate_gray` , ersätter alla instanser av relativa URL: er med URL: er för de CSS-, bilder-och fonts-filer som du laddade upp i steg 2. Exempel:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    Om du vill
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Spara `\*.html` filerna och överför dem till Blob Storage.
1. Ändra principen genom att peka på HTML-filen som tidigare nämnts.
1. Om du ser saknade teckensnitt, avbildningar eller CSS, kontrollerar du dina referenser i tillägg-principen och \* HTML-filerna.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du aktiverar [JavaScript-kod på klient sidan](javascript-and-page-layout.md).

