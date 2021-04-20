---
title: Utvecklarportalen med egen värd
titleSuffix: Azure API Management
description: Lär dig hur du är värd för API Management utvecklarportalen.
author: dlepow
ms.author: apimpm
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: e63a329a10699102802af6d544ab55aa19513f17
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741890"
---
# <a name="self-host-the-api-management-developer-portal"></a>Värd för API Management utvecklarportalen

Den här självstudien beskriver hur du är värd för [API Management utvecklarportalen.](api-management-howto-developer-portal.md) Med egen värd kan du utöka utvecklarportalen med anpassad logik och widgetar som dynamiskt anpassar sidor vid körning. Du kan vara värd för flera portaler för din API Management instans med olika funktioner. När du har en portal som egen värd blir du dess underhållare och du ansvarar för dess uppgraderingar. 

Följande steg visar hur du ställer in din lokala utvecklingsmiljö, utför ändringar i utvecklarportalen och publicerar och distribuerar dem till ett Azure Storage-konto.

Om du redan har laddat upp eller ändrat mediefiler i den hanterade portalen kan du läsa Flytta från hanterad till egen värd [senare](#move-from-managed-to-self-hosted-developer-portal)i den här artikeln.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera en lokal utvecklingsmiljö måste du ha:

- En API Management-tjänstinstans. Om du inte har någon kan du gå till [Snabbstart – Skapa en Azure API Management instans.](get-started-create-service-instance.md)
- Ett Azure-lagringskonto [med funktionen statiska webbplatser](../storage/blobs/storage-blob-static-website.md) aktiverad. Se [Skapa ett lagringskonto.](../storage/common/storage-account-create.md)
- Git på din dator. Installera det genom att följa den [här Git-självstudien](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
- Node.js (LTS-version `v10.15.0` eller senare) och npm på datorn. Se [Ladda ned och installera Node.js och npm.](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)
- Azure CLI. Följ [installationsstegen för Azure CLI.](/cli/azure/install-azure-cli-windows)

## <a name="step-1-set-up-local-environment"></a>Steg 1: Konfigurera lokal miljö

Om du vill konfigurera din lokala miljö måste du klona lagringsplatsen, växla till den senaste versionen av utvecklarportalen och installera npm-paket.

1. Klona [lagringsplatsen api-management-developer-portal](https://github.com/Azure/api-management-developer-portal.git) från GitHub:

    ```console
    git clone https://github.com/Azure/api-management-developer-portal.git
    ```
1. Gå till din lokala kopia av lagringsplatsen:

    ```console
    cd api-management-developer-portal
    ```

1. Kolla in den senaste versionen av portalen.

    Innan du kör följande kod kontrollerar du den aktuella versionstaggen i avsnittet Versioner på [lagringsplatsen](https://github.com/Azure/api-management-developer-portal/releases) och `<current-release-tag>` ersätter värdet med den senaste versionstaggen.
    
    ```console
    git checkout <current-release-tag>
    ```

1. Installera alla tillgängliga npm-paket:

    ```console
    npm install
    ```

> [!TIP]
> Använd alltid [den senaste versionen av](https://github.com/Azure/api-management-developer-portal/releases) portalen och håll din förked portal uppdaterad. Programvarutekniker använder -grenen `master` av den här lagringsplatsen för daglig utveckling. Den har instabila versioner av programvaran.

## <a name="step-2-configure-json-files-static-website-and-cors-settings"></a>Steg 2: Konfigurera JSON-filer, statiska webbplatser och CORS-inställningar

Utvecklarportalen kräver API Management är REST API för att hantera innehållet.

### <a name="configdesignjson-file"></a>config.design.jspå fil

Gå till `src` mappen och öppna `config.design.json` filen.

```json
{
  "environment": "development",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "managementApiAccessToken": "SharedAccessSignature ...",
  "backendUrl": "https://<service-name>.developer.azure-api.net",
  "useHipCaptcha": false
}
```

Konfigurera filen:

1. I värdet `managementApiUrl` ersätter du `<service-name>` med namnet på din API Management instans. Om du har konfigurerat en [anpassad domän](configure-custom-domain.md)använder du den i stället (till exempel `https://management.contoso.com` ).

    ```json
    {
    ...
    "managementApiUrl": "https://contoso-api.management.azure-api.net"
    ...
    ``` 

1. [Skapa en SAS-token manuellt](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication#ManuallyCreateToken) för att aktivera REST API åtkomst till din API Management instans.

1. Kopiera den genererade token och klistra in den som `managementApiAccessToken` värde.

1. I värdet `backendUrl` ersätter du `<service-name>` med namnet på din API Management instans. Om du har konfigurerat en [anpassad domän](configure-custom-domain.md)använder du den i stället (till exempel `https://portal.contoso.com` ).

    ```json
    {
    ...
    "backendUrl": "https://contoso-api.developer.azure-api.net"
    ...
    ```

1. Om du vill aktivera CAPTCHA i utvecklarportalen kan du gå till [Aktivera CAPTCHA.](#enable-captcha)

### <a name="configpublishjson-file"></a>config.publish.jspå fil

Gå till `src` mappen och öppna `config.publish.json` filen.

```json
{
  "environment": "publishing",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "managementApiAccessToken": "SharedAccessSignature...",
  "useHipCaptcha": false
}
```

Konfigurera filen:

1. Kopiera och klistra in `managementApiUrl` värdena och från den tidigare `managementApiAccessToken` konfigurationsfilen.

1. Om du vill aktivera CAPTCHA i utvecklarportalen kan du gå till [Aktivera CAPTCHA.](#enable-captcha)

### <a name="configruntimejson-file"></a>config.runtime.jspå fil

Gå till `src` mappen och öppna `config.runtime.json` filen.

```json
{
  "environment": "runtime",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "backendUrl": "https://<service-name>.developer.azure-api.net"
}
```

Konfigurera filen:

1. Kopiera och klistra in `managementApiUrl` värdet från den tidigare konfigurationsfilen.

1. I värdet `backendUrl` ersätter du `<service-name>` med namnet på din API Management instans. Om du har konfigurerat en [anpassad domän](configure-custom-domain.md)använder du den i stället (till exempel. `https://portal.contoso.com`).

    ```json
    {
    ...
    "backendUrl": "https://contoso-api.developer.azure-api.net"
    ...
    ```

### <a name="configure-the-static-website"></a>Konfigurera den statiska webbplatsen

Konfigurera funktionen **Statisk webbplats** i ditt lagringskonto genom att tillhandahålla vägar till index- och felsidorna:

1. Gå till ditt lagringskonto i Azure Portal och välj **Statisk** webbplats på menyn till vänster.

1. På sidan **Statisk webbplats** väljer du **Aktiverad.**

1. I fältet **Indexdokumentnamn** anger du *index.html*.

1. I fältet **Sökväg till feldokument** anger du *404/index.html*.

1. Välj **Spara**.

### <a name="configure-the-cors-settings"></a>Konfigurera CORS-inställningarna

Konfigurera CORS-inställningarna (Cross-Origin Resource Sharing):

1. Gå till ditt lagringskonto i Azure Portal och välj **CORS** på menyn till vänster.

1. Konfigurera **följande regler på** fliken Blob Service:

    | Regel | Värde |
    | ---- | ----- |
    | Tillåtna ursprung | * |
    | Tillåtna metoder | Markera alla HTTP-verb. |
    | Tillåtna rubriker | * |
    | Exponerade rubriker | * |
    | Högsta ålder | 0 |

1. Välj **Spara**.

## <a name="step-3-run-the-portal"></a>Steg 3: Kör portalen

Nu kan du skapa och köra en lokal portalinstans i utvecklingsläget. I utvecklingsläget är alla optimeringar inaktiverade och källmappningar aktiveras.

Kör följande kommando:

```console
npm start
```

Efter en kort stund öppnas standardwebbläsaren automatiskt med din lokala utvecklarportalinstans. Standardadressen är `http://localhost:8080` , men porten kan ändras om den redan är `8080` upptagen. Ändringar i kodbasen för projektet utlöser återskapande och uppdaterar webbläsarfönstret.

## <a name="step-4-edit-through-the-visual-editor"></a>Steg 4: Redigera via den visuella redigeraren

Använd den visuella redigeraren för att utföra följande uppgifter:

- Anpassa din portal
- Skapa innehåll
- Organisera webbplatsens struktur
- Sizeize its appearance

Se [Självstudie: Få åtkomst till och anpassa utvecklarportalen.](api-management-howto-developer-portal-customize.md) Den beskriver grunderna i det administrativa användargränssnittet och visar rekommenderade ändringar av standardinnehållet. Spara alla ändringar i den lokala miljön och tryck på **Ctrl+C för** att stänga den.

## <a name="step-5-publish-locally"></a>Steg 5: Publicera lokalt

Portaldata har sitt ursprung i form av starkt typade objekt. Följande kommando översätter dem till statiska filer och placerar utdata i `./dist/website` katalogen:

```console
npm run publish
```

## <a name="step-6-upload-static-files-to-a-blob"></a>Steg 6: Ladda upp statiska filer till en blob

Använd Azure CLI för att ladda upp de lokalt genererade statiska filerna till en blob och se till att dina besökare kan nå dem:

1. Öppna Kommandotolken i Windows, PowerShell eller något annat kommandogränssnitt.

1. Kör följande Azure CLI-kommando.
   
    Ersätt `<account-connection-string>` med anslutningssträngen för ditt lagringskonto. Du kan hämta den från avsnittet **Åtkomstnycklar** för ditt lagringskonto.

    ```azurecli
    az storage blob upload-batch --source dist/website \
        --destination '$web' \
        --connection-string <account-connection-string>
    ```


## <a name="step-7-go-to-your-website"></a>Steg 7: Gå till din webbplats

Webbplatsen är nu live under det värdnamn som anges i dina Azure Storage **(Primär slutpunkt i** **Statiska webbplatser).**

## <a name="step-8-change-api-management-notification-templates"></a>Steg 8: Ändra API Management aviseringsmallar

Ersätt URL:en för utvecklarportalen i API Management för att peka på din portal med egen värd. Se [Så här konfigurerar du meddelanden och e-postmallar i Azure API Management](api-management-howto-configure-notifications.md).

Utför i synnerhet följande ändringar i standardmallarna:

> [!NOTE]
> Värdena i följande uppdaterade **avsnitt** förutsätter att du är värd för portalen på **https: \/ /portal.contoso.com/**. 

### <a name="email-change-confirmation"></a>Bekräftelse av e-poständring

Uppdatera URL:en för utvecklarportalen i mallen för bekräftelse **av e-poständring:**

**Ursprungligt innehåll**

```html
<a id="confirmUrl" href="$ConfirmUrl" style="text-decoration:none">
  <strong>$ConfirmUrl</strong></a>
```

**Uppdaterad**

```html
<a id="confirmUrl" href="https://portal.contoso.com/signup?$ConfirmQuery" style="text-decoration:none">
  <strong>https://portal.contoso.com/signup?$ConfirmQuery</strong></a>
```

### <a name="new-developer-account-confirmation"></a>Bekräftelse av nytt utvecklarkonto

Uppdatera url:en för utvecklarportalen i mallen **För bekräftelse av nytt utvecklarkonto:**

**Ursprungligt innehåll**

```html
<a id="confirmUrl" href="$ConfirmUrl" style="text-decoration:none">
  <strong>$ConfirmUrl</strong></a>
```

**Uppdaterad**

```html
<a id="confirmUrl" href="https://portal.contoso.com/signup?$ConfirmQuery" style="text-decoration:none">
  <strong>https://portal.contoso.com/signup?$ConfirmQuery</strong></a>
```

### <a name="invite-user"></a>Bjud in användare

Uppdatera url:en för utvecklarportalen i **mallen Bjud in användarmeddelande:**

**Ursprungligt innehåll**

```html
<a href="$ConfirmUrl">$ConfirmUrl</a>
```

**Uppdaterad**

```html
<a href="https://portal.contoso.com/confirm-v2/identities/basic/invite?$ConfirmQuery">https://portal.contoso.com/confirm-v2/identities/basic/invite?$ConfirmQuery</a>
```

### <a name="new-subscription-activated"></a>Ny prenumeration aktiverad

Uppdatera url:en för utvecklarportalen **i meddelandemallen Ny prenumeration** aktiverad:

**Ursprungligt innehåll**

```html
Thank you for subscribing to the <a href="http://$DevPortalUrl/products/$ProdId"><strong>$ProdName</strong></a> and welcome to the $OrganizationName developer community. We are delighted to have you as part of the team and are looking forward to the amazing applications you will build using our API!
```

**Uppdaterad**

```html
Thank you for subscribing to the <a href="https://portal.contoso.com/product#product=$ProdId"><strong>$ProdName</strong></a> and welcome to the $OrganizationName developer community. We are delighted to have you as part of the team and are looking forward to the amazing applications you will build using our API!
```

**Ursprungligt innehåll**

```html
Visit the developer <a href="http://$DevPortalUrl/developer">profile area</a> to manage your subscription and subscription keys
```

**Uppdaterad**

```html
Visit the developer <a href="https://portal.contoso.com/profile">profile area</a> to manage your subscription and subscription keys
```

**Ursprungligt innehåll**

```html
<a href="http://$DevPortalUrl/docs/services?product=$ProdId">Learn about the API</a>
```

**Uppdaterad**

```html
<a href="https://portal.contoso.com/product#product=$ProdId">Learn about the API</a>
```

**Ursprungligt innehåll**

```html
<p style="font-size:12pt;font-family:'Segoe UI'">
  <strong>
    <a href="http://$DevPortalUrl/applications">Feature your app in the app gallery</a>
  </strong>
</p>
<p style="font-size:12pt;font-family:'Segoe UI'">You can publish your application on our gallery for increased visibility to potential new users.</p>
<p style="font-size:12pt;font-family:'Segoe UI'">
  <strong>
    <a href="http://$DevPortalUrl/issues">Stay in touch</a>
  </strong>
</p>
<p style="font-size:12pt;font-family:'Segoe UI'">
      If you have an issue, a question, a suggestion, a request, or if you just want to tell us something, go to the <a href="http://$DevPortalUrl/issues">Issues</a> page on the developer portal and create a new topic.
</p>
```

**Uppdaterad**

```html
<!--Remove the entire block of HTML code above.-->
```

### <a name="password-change-confirmation"></a>Bekräftelse av lösenordsändring

Uppdatera url:en för utvecklarportalen i mallen **för bekräftelse av lösenordsändring:**

**Ursprungligt innehåll**

```html
<a href="$DevPortalUrl">$DevPortalUrl</a>
```

**Uppdaterad**

```html
<a href="https://portal.contoso.com/confirm-password?$ConfirmQuery">https://portal.contoso.com/confirm-password?$ConfirmQuery</a>
```

### <a name="all-templates"></a>Alla mallar

Uppdatera url:en för utvecklarportalen i valfri mall som har en länk i sidfoten:

**Ursprungligt innehåll**

```html
<a href="$DevPortalUrl">$DevPortalUrl</a>
```

**Uppdaterad**

```html
<a href="https://portal.contoso.com/">https://portal.contoso.com/</a>
```

## <a name="move-from-managed-to-self-hosted-developer-portal"></a>Flytta från en hanterad utvecklarportal till en egen värd

Med tiden kan dina affärsbehov ändras. Du kan hamna i en situation där den hanterade versionen av API Management utvecklarportalen inte längre uppfyller dina behov. Ett nytt krav kan till exempel tvinga dig att skapa en anpassad widget som integreras med en dataleverantör från tredje part. Till skillnad från den manglade versionen ger den lokala versionen av portalen dig fullständig flexibilitet och utökningsbarhet.

### <a name="transition-process"></a>Övergångsprocess

Du kan övergå från den hanterade versionen till en version med egen värd inom samma API Management-tjänstinstans. Processen bevarar de ändringar som du har utfört i den hanterade versionen av portalen. Se till att du hittar innehållet i förväg på portalen. Du hittar säkerhetskopieringsskriptet i mappen `scripts` på API Management [GitHub-lagringsplatsen för utvecklarportalen.](https://github.com/Azure/api-management-developer-portal)

Konverteringsprocessen är nästan identisk med att konfigurera en allmän portal med egen värd, som du ser i föregående steg i den här artikeln. Det finns ett undantag i konfigurationssteget. Lagringskontot i `config.design.json` filen måste vara samma som lagringskontot för den hanterade versionen av portalen. Se Självstudie: Använda en system tilldelad identitet för en virtuell [Linux-dator för att komma åt Azure Storage via en SAS-autentiseringsbehörighet](../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-storage-sas.md#get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls) för instruktioner om hur du hämtar SAS-URL:en.

> [!TIP]
> Vi rekommenderar att du använder ett separat lagringskonto i `config.publish.json` filen. Den här metoden ger dig mer kontroll och förenklar hanteringen av värdtjänsten för din portal.

## <a name="enable-captcha"></a>Aktivera CAPTCHA

När du ställer in den egenvärdbaserade portalen kan du ha inaktiverat CAPTCHA via `useHipCaptcha` inställningen . Kommunikation med CAPTCHA sker via en slutpunkt, vilket gör att resursdelning för korsande ursprung (CORS) endast sker för värdnamnet för den hanterade utvecklarportalen. Om utvecklarportalen har egen värd använder den ett annat värdnamn och CAPTCHA tillåter inte kommunikationen.

### <a name="update-the-json-config-files"></a>Uppdatera JSON-konfigurationsfilerna

Så här aktiverar du CAPTCHA i din portal med egen värd:

1. Tilldela en anpassad domän (till exempel `api.contoso.com` ) till slutpunkten **för utvecklarportalen** för din API Management tjänst.

    Den här domänen gäller för den hanterade versionen av portalen och CAPTCHA-slutpunkten. Anvisningar finns i Konfigurera [ett anpassat domännamn för din Azure API Management instans.](configure-custom-domain.md)

1. Gå till `src` mappen i den lokala [miljön](#step-1-set-up-local-environment) för din lokala portal.

1. Uppdatera `json` konfigurationsfilerna:

    | Fil | Nytt värde | Anteckning |
    | ---- | --------- | ---- |
    | `config.design.json`| `"backendUrl": "https://<custom-domain>"` | Ersätt `<custom-domain>` med den anpassade domän som du konfigurerade i det första steget. |
    |  | `"useHipCaptcha": true` | Ändra värdet till `true` |
    | `config.publish.json`| `"backendUrl": "https://<custom-domain>"` | Ersätt `<custom-domain>` med den anpassade domän som du konfigurerade i det första steget. |
    |  | `"useHipCaptcha": true` | Ändra värdet till `true` |
    | `config.runtime.json` | `"backendUrl": "https://<custom-domain>"` | Ersätt `<custom-domain>` med den anpassade domän som du konfigurerade i det första steget. |

1. [Publicera](#step-5-publish-locally) portalen.

1. [Ladda](#step-6-upload-static-files-to-a-blob) upp och vara värd för den nyligen publicerade portalen.

1. Exponera portalen med egen värd via en anpassad domän.

Portaldomänens första och andra nivåer måste matcha domänen som konfigureras i det första steget. Till exempel `portal.contoso.com`. De exakta stegen beror på vilken värdplattform du väljer. Om du använde ett Azure Storage-konto kan du läsa mer i Mappa en anpassad [domän Azure Blob Storage en slutpunkt.](../storage/blobs/storage-custom-domain-name.md)

## <a name="next-steps"></a>Nästa steg

- Lär dig [mer om alternativa metoder för självvärdtjänster](developer-portal-alternative-processes-self-host.md)
