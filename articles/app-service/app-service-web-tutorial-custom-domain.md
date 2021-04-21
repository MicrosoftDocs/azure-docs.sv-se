---
title: 'Självstudie: Mappa befintligt anpassat DNS-namn'
description: Lär dig hur du lägger till ett befintligt anpassat DNS-domännamn (anpassad domän) i en webbapp, serverdel för mobilappar eller EN API-app i Azure App Service.
keywords: app service, azure app service, domain mapping, domain name, existing domain, hostname
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18, devx-track-azurepowershell
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./app-service-web-tutorial-custom-domain-uiex
ms.openlocfilehash: 8e310cb0507146eb53c7b55c2aaed492baa79521
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833262"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Självstudie: Mappa ett befintligt anpassat DNS-namn till Azure App Service

[Azure App Service](overview.md) en mycket skalbar och självkorrigeringstjänst för webbvärdtjänster. Den här självstudien visar hur du mappar ett befintligt DNS Domain Name System namn (Custom Domain Name System) till App Service.

![Skärmbild som visar Azure Portal till en Azure-app.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Mappa en underdomän (till exempel `www.contoso.com` ) med hjälp av en CNAME-post.
> * Mappa en rotdomän (till exempel `contoso.com` ) med hjälp av en A-post.
> * Mappa en domän med jokertecken (till exempel `*.contoso.com` ) med hjälp av en CNAME-post.
> * Omdirigera standard-URL:en till en anpassad katalog.
> * Automatisera domänmappning med skript.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* [Skapa en App Service-app](./index.yml), eller använd en app som du har skapat för en annan kurs.
* Köp ett domännamn och kontrollera att du har åtkomst till DNS-registret för din domänleverantör (till exempel GoDaddy).

  För att till exempel lägga till DNS-poster för `contoso.com` och `www.contoso.com` måste du kunna konfigurera DNS-inställningarna för rotdomänen `contoso.com`.

  > [!NOTE]
  > Om du inte har ett befintligt domännamn kan du köpa [en domän med hjälp av Azure Portal](manage-custom-dns-buy-domain.md).

## <a name="prepare-the-app"></a>Förbereda appen

Om du vill mappa ett anpassat DNS-namn till en webbapp måste [webbappens App Service plan](https://azure.microsoft.com/pricing/details/app-service/) vara en betalnivå (Delad, Basic, Standard, Premium eller Förbrukning för Azure Functions). I det här steget ser du till att App Service-appen har en prisnivå som stöds.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

  > [!NOTE]
  > Om du tar bort eller lägger till en anpassad domän i webbappen startas webbplatsen om.
### <a name="sign-in-to-azure"></a>Logga in på Azure

Öppna [Azure Portal](https://portal.azure.com)och logga in med ditt Azure-konto.

### <a name="select-the-app-in-the-azure-portal"></a>Välj appen i Azure Portal

1. Sök efter och välj **App Services**.

   ![Skärmbild som visar val av App Services.](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. På **App Services** väljer du namnet på din Azure-app.

   ![Skärmbild som visar portalnavigering till en Azure-app.](./media/app-service-web-tutorial-custom-domain/select-app.png)

Du ser hanteringssidan för App Service-appen.

<a name="checkpricing" aria-hidden="true"></a>

### <a name="check-the-pricing-tier"></a>Kontrollera prisnivån

1. I den vänstra rutan på appsidan bläddrar du till **avsnittet Inställningar** och väljer Skala **upp (App Service plan).**

   ![Skärmbild som visar menyn Skala upp (App Service plan).](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. Appens aktuell nivå markeras med en blå kantlinje. Kontrollera att appen inte är på **F1-nivån.** Anpassad DNS stöds inte på **F1-nivån.**

   ![Skärmbild som visar rekommenderade prisnivåer.](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. Om App Service plan inte finns på **F1-nivån** stänger du sidan **Skala upp** och går vidare till [Mappa en CNAME-post.](#map-a-cname-record)

<a name="scaleup" aria-hidden="true"></a>

### <a name="scale-up-the-app-service-plan"></a>Skala upp App Service-planen

1. Välj någon av betalnivåerna (**D1**, **B1**, **B2**, **B3** eller en nivå i kategorin **Produktion**). Om du vill ha fler alternativ väljer **du Se ytterligare alternativ.**

1. Välj **Använd**.

   ![Skärmbild som visar en kontroll av prisnivån.](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   När du ser följande meddelande har skalningsåtgärden slutförts.

   ![Skärmbild som visar bekräftelse av skalningsåtgärden.](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname" aria-hidden="true"></a>

## <a name="get-a-domain-verification-id"></a>Hämta ett domänverifierings-ID

Om du vill lägga till en anpassad domän i din app måste du verifiera ditt ägarskap för domänen genom att lägga till ett verifierings-ID som en TXT-post hos din domänleverantör. I den vänstra rutan på appsidan väljer du **Anpassade domäner.** Kopiera ID:t i **rutan Custom Domain verifierings-ID** på **sidan Anpassade** domäner för nästa steg.

![Skärmbild som visar ID:t i rutan Custom Domain verifierings-ID.](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

> [!WARNING]
> Om du lägger till domänverifierings-ID:n i din anpassade domän kan du förhindra att DNS-poster dinglar och undviker övertaganden av underdomäner. För anpassade domäner som du tidigare har konfigurerat utan det här verifierings-ID:t bör du skydda dem från samma risk genom att lägga till verifierings-ID:t i DNS-posten. Mer information om det här vanliga hotet med hög allvarlighetsgrad finns [i Övertagande av underdomän.](../security/fundamentals/subdomain-takeover.md)

## <a name="map-your-domain"></a>Mappa din domän

Du kan mappa ett anpassat DNS-namn till App Service med antingen en CNAME-post eller en A-post. Följ respektive steg:

- [Mappa en CNAME-post](#map-a-cname-record)
- [Mappa en A-post](#map-an-a-record)
- [Mappa en domän med jokertecken (med en CNAME-post)](#map-a-wildcard-domain)

> [!NOTE]
> Du bör använda CNAME-poster för alla anpassade DNS-namn förutom rotdomäner (till exempel `contoso.com`). För rotdomäner använder du A-poster.

### <a name="map-a-cname-record"></a>Mappa en CNAME-post

I kursexemplet lägger du till en CNAME-post för `www`-underdomänen (till exempel `www.contoso.com`).

Om du har en annan underdomän än ersätter du med din `www` `www` underdomän (till exempel med `sub` om din anpassade domän är `sub.constoso.com` ).

#### <a name="access-dns-records-with-a-domain-provider"></a>Få åtkomst till DNS-poster med en domänleverantör

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Skapa CNAME-posten

Mappa en underdomän till appens standarddomännamn ( `<app-name>.azurewebsites.net` , där är namnet på din `<app-name>` app). Skapa två poster för att skapa en `www` CNAME-mappning för underdomänen:

| Posttyp | Värd | Värde | Kommentarer |
| - | - | - |
| CNAME | `www` | `<app-name>.azurewebsites.net` | Själva domänmappningen. |
| TXT | `asuid.www` | [Verifierings-ID:t du fick tidigare](#get-a-domain-verification-id) | App Service åtkomst till `asuid.<subdomain>` TXT-posten för att verifiera ägarskapet för den anpassade domänen. |

När du har lagt till CNAME- och TXT-posterna ser sidan för DNS-poster ut som i följande exempel:

![Skärmbild som visar portalnavigeringen till en Azure-app.](./media/app-service-web-tutorial-custom-domain/cname-record.png)

#### <a name="enable-the-cname-record-mapping-in-azure"></a>Aktivera CNAME-postmappning i Azure

1. I den vänstra rutan på appsidan i Azure Portal väljer du **Anpassade domäner**.

    ![Skärmbild som visar menyn anpassade domäner.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. På sidan **Anpassade domäner** i appen lägger du till det fullständigt kvalificerade DNS-namnet ( ) `www.contoso.com` i listan.

1. Välj **Lägg till anpassad domän.**

    ![Skärmbild som visar objektet Lägg till värdnamn.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Skriv det fullständigt kvalificerade domännamnet som du lade till en CNAME-post för, till exempel `www.contoso.com`.

1. Välj **Verifiera**. Sidan **Lägg till anpassad** domän visas.

1. Kontrollera att **Posttypen Värdnamn har** angetts till **CNAME (www \. example.com eller någon underdomän).** Välj **Lägg till anpassad domän.**

    ![Skärmbild som visar knappen Lägg till anpassad domän.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Det kan ta lite tid innan den nya anpassade domänen visas på appens sida **Anpassade** domäner. Uppdatera webbläsaren för att uppdatera data.

    ![Skärmbild som visar tillägg av CNAME-posten.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > En varningsetikett för din anpassade domän innebär att den ännu inte är bunden till ett TLS/SSL-certifikat. Alla HTTPS-förfrågningar från en webbläsare till din anpassade domän får ett fel eller en varning, beroende på webbläsaren. Information om hur du lägger till en TLS-bindning finns i Skydda ett anpassat DNS-namn med [en TLS/SSL-bindning i Azure App Service](configure-ssl-bindings.md).

    Om du missade ett steg eller stavade fel någonstans tidigare visas ett verifieringsfel längst ned på sidan.

    ![Skärmbild som visar ett verifieringsfel.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

### <a name="map-an-a-record"></a>Mappa en A-post

I kursexemplet lägger du till en A-post för rotdomänen (till exempel `contoso.com`).

<a name="info"></a>

#### <a name="copy-the-apps-ip-address"></a>Kopiera appens IP-adress

För att kunna mappa A-posten behöver du appens externa IP-adress. Du hittar den här IP-adressen på appens **sida Anpassade domäner** i Azure Portal.

1. I den vänstra rutan på appsidan i Azure Portal väljer du **Anpassade domäner**.

   ![Skärmbild som visar menyn anpassade domäner.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. På sidan **Anpassade domäner** kopierar du appens IP-adress.

   ![Skärmbild som visar portalnavigering till en Azure-app.](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

#### <a name="access-dns-records-with-a-domain-provider"></a>Få åtkomst till DNS-poster med en domänleverantör

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-a-record"></a>Skapa en A-post

Om du vill mappa en A-post till en app, vanligtvis till rotdomänen, skapar du två poster:

| Posttyp | Värd | Värde | Kommentarer |
| - | - | - |
| A | `@` | IP-adress från [Kopiera appens IP-adress](#info) | Själva domänmappningen ( `@` representerar vanligtvis rotdomänen). |
| TXT | `asuid` | [Verifierings-ID:t du fick tidigare](#get-a-domain-verification-id) | App Service åtkomst till `asuid.<subdomain>` TXT-posten för att verifiera ditt ägarskap för den anpassade domänen. För rotdomänen använder du `asuid` . |

> [!NOTE]
> Om du vill lägga till en underdomän (t.ex. ) med hjälp av en A-post i stället för en rekommenderad `www.contoso.com` [CNAME-post](#map-a-cname-record)bör A-posten och TXT-posten se ut som i följande tabell i stället:
>
> | Posttyp | Värd | Värde |
> | - | - | - |
> | A | `www` | IP-adress från [Kopiera appens IP-adress](#info) |
> | TXT | `asuid.www` | [Verifierings-ID:t som du fick tidigare](#get-a-domain-verification-id) |
>

När posterna har lagts till ser sidan DNS-poster ut som i följande exempel:

![Skärmbild som visar en sida med DNS-poster.](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a" aria-hidden="true"></a>

#### <a name="enable-the-a-record-mapping-in-the-app"></a>Aktivera A-postmappning i appen

När du är tillbaka på **appens** sida Anpassade domäner i Azure Portal lägger du till det fullständigt kvalificerade anpassade DNS-namnet (till `contoso.com` exempel ) i listan.

1. Välj **Lägg till anpassad domän.**

    ![Skärmbild som visar tillägg av ett värdnamn.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Skriv det fullständigt kvalificerade domännamnet som du konfigurerade A-posten för, till exempel `contoso.com`. 

1. Välj **Verifiera**. Sidan **Lägg till anpassad** domän visas.

1. Se till att **Posttyp för värddatornamn** har värdet **A-post (example.com)**. Välj **Lägg till anpassad domän.**

    ![Skärmbild som visar tillägg av ett DNS-namn i appen.](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    Det kan ta lite tid innan den nya anpassade domänen visas på appens sida **Anpassade** domäner. Uppdatera webbläsaren för att uppdatera data.

    ![Skärmbild som visar tillägg av en A-post.](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    > [!NOTE]
    > En varningsetikett för din anpassade domän innebär att den ännu inte är bunden till ett TLS/SSL-certifikat. Alla HTTPS-förfrågningar från en webbläsare till din anpassade domän får ett fel eller en varning, beroende på webbläsaren. Information om hur du lägger till en TLS-bindning finns i Skydda ett anpassat DNS-namn med [en TLS/SSL-bindning i Azure App Service](configure-ssl-bindings.md).
    
    Om du missade ett steg eller stavade fel någonstans tidigare visas ett verifieringsfel längst ned på sidan.
    
    ![Skärmbild som visar ett verifieringsfel.](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

### <a name="map-a-wildcard-domain"></a>Mappa en domän med jokertecken

I kursexemplet mappar du ett [DNS-namn med jokertecken](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (till exempel `*.contoso.com`) till App Service-appen genom att lägga till en CNAME-post.

#### <a name="access-dns-records-with-a-domain-provider"></a>Få åtkomst till DNS-poster med en domänleverantör

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Skapa CNAME-posten

Mappa ett `*` jokerteckennamn till appens standarddomännamn ( `<app-name>.azurewebsites.net` , där är namnet på din `<app-name>` app). Om du vill mappa jokertecknets namn skapar du två poster:

| Posttyp | Värd | Värde | Kommentarer |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | Själva domänmappningen. |
| TXT | `asuid` | [Verifierings-ID:t du fick tidigare](#get-a-domain-verification-id) | App Service åtkomst till `asuid` TXT-posten för att verifiera ägarskapet för den anpassade domänen. |

För `*.contoso.com`-domänexemplet mappar CNAME-posten namnet `*` till `<app-name>.azurewebsites.net`.

När CNAME har lagts till ser sidan för DNS-poster ut som i följande exempel:

![Skärmbild som visar navigeringen till en Azure-app.](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

#### <a name="enable-the-cname-record-mapping-in-the-app"></a>Aktivera CNAME-postmappning i appen

Nu kan du lägga till en underdomän som matchar jokernamnet i appen (till exempel `sub1.contoso.com` , och båda matchar `sub2.contoso.com` `*.contoso.com` `*.contoso.com` ).

1. I den vänstra rutan på appsidan i Azure Portal väljer du **Anpassade domäner**.

    ![Skärmbild som visar menyn anpassade domäner.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Välj **Lägg till anpassad domän.**

    ![Skärmbild som visar tillägg av ett värdnamn.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Ange ett fullständigt kvalificerat domännamn som matchar domänen med jokertecken (till exempel `sub1.contoso.com`) och välj sedan **Verifiera**.

    Knappen **Lägg till anpassad** domän är aktiverad.

1. Kontrollera att **Posttypen Värdnamn är** inställd på **CNAME-post (www \. example.com eller någon underdomän).** Välj **Lägg till anpassad domän.**

    ![Skärmbild som visar tillägg av ett DNS-namn i appen.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    Det kan ta lite tid innan den nya anpassade domänen visas på appens sida **Anpassade** domäner. Uppdatera webbläsaren för att uppdatera data.

1. Välj ikonen **+** igen för att lägga till en annan anpassad domän som matchar domänen med jokertecken. Lägg exempelvis till `sub2.contoso.com`.

    ![Skärmbild som visar tillägg av en CNAME-post.](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard-2.png)

    > [!NOTE]
    > En varningsetikett för din anpassade domän innebär att den ännu inte är bunden till ett TLS/SSL-certifikat. Alla HTTPS-förfrågningar från en webbläsare till din anpassade domän får ett fel eller en varning, beroende på webbläsaren. Information om hur du lägger till en TLS-bindning finns i Skydda ett anpassat DNS-namn med [en TLS/SSL-bindning i Azure App Service](configure-ssl-bindings.md).
    
## <a name="test-in-a-browser"></a>Testa i en webbläsare

Bläddra till de DNS-namn som du konfigurerade tidigare (till exempel `contoso.com` `www.contoso.com` , , och `sub1.contoso.com` `sub2.contoso.com` ).

![Skärmbild som visar navigering till en Azure-app.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-not-found"></a>Lös 404 "Hittades inte"

Om du får ett HTTP 404-fel (hittades inte) när du bläddrar till URL:en för din anpassade domän kontrollerar du att domänen matchar appens IP-adress med <a href="https://www.nslookup.io/" target="_blank">hjälp av nslookup.io</a>. Om inte kontrollerar du att A- och CNAME-posterna är korrekt konfigurerade med samma plats. Om IP-adressen matchas korrekt, men du fortfarande får ett 404-fel, kan webbläsaren ha cachelagrat den gamla IP-adressen för din domän. Rensa cacheminnet och testa DNS-upplösningen igen. På en Windows-dator rensar du cachen med `ipconfig /flushdns`.

## <a name="migrate-an-active-domain"></a>Migrera en aktiv domän

Om du vill migrera en live-webbplats och dess DNS-domännamn till App Service utan avbrott kan du läsa [Migrera ett aktivt DNS-namn till Azure App Service](manage-custom-dns-migrate-domain.md).

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>Omdirigera till en anpassad katalog

Som standard dirigerar App Service webbegäranden till rotkatalogen för din appkod. Men vissa webbramverk startar inte i rotkatalogen. [Laravel](https://laravel.com/) startar till exempel i underkatalogen `public`. För att fortsätta `contoso.com` DNS-exemplet är en sådan app tillgänglig på `http://contoso.com/public` , men du vill dirigera till katalogen i `http://contoso.com` `public` stället. Det här steget omfattar inte DNS-upplösning, utan handlar om att anpassa den virtuella katalogen.

Om du vill anpassa en virtuell katalog för Windows-appar väljer **du Programinställningar** i den vänstra rutan på webbappsidan. 

> [!NOTE]
> Linux-appar har inte den här sidan. Om du vill ändra platsroten för Linux-appar kan du till exempel se språkspecifika konfigurationsguider ([PHP).](configure-language-php.md?pivots=platform-linux#change-site-root)

Längst ned på sidan pekar den virtuella rotkatalogen `/` till `site\wwwroot` som standard, vilket är rotkatalogen för din appkod. Ändra den så att den pekar till exempelvis `site\wwwroot\public` i stället, och spara ändringarna.

![Skärmbild som visar anpassning av en virtuell katalog.](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

När åtgärden har avslutats bör appen returnera rätt sida på rotsökvägen (till exempel `http://contoso.com` ).

## <a name="automate-with-scripts"></a>Automatisera med skript

Du kan automatisera hanteringen av anpassade domäner med skript med hjälp av [Azure CLI eller](/cli/azure/install-azure-cli) [Azure PowerShell](/powershell/azure/).

### <a name="azure-cli"></a>Azure CLI

Följande kommando lägger till ett konfigurerat anpassat DNS-namn i en App Service-app.

```bash 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

Mer information finns i [Mappa en anpassad domän till en webbapp](scripts/cli-configure-custom-domain.md).

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Följande kommando lägger till ett konfigurerat anpassat DNS-namn i en App Service-app.

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

Mer information finns i [Tilldela en anpassad domän till en webbapp](scripts/powershell-configure-custom-domain.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Mappa en underdomän med hjälp av en CNAME-post.
> * Mappa en rotdomän med hjälp av en A-post.
> * Mappa en domän med jokertecken med hjälp av en CNAME-post.
> * Omdirigera standard-URL:en till en anpassad katalog.
> * Automatisera domänmappning med skript.

Fortsätt till nästa självstudie för att lära dig hur du binder ett anpassat TLS/SSL-certifikat till en webbapp.

> [!div class="nextstepaction"]
> [Skydda ett anpassat DNS-namn med en TLS/SSL-bindning i Azure App Service](configure-ssl-bindings.md)
