---
title: 'Självstudie: mappa ett befintligt anpassat DNS-namn'
description: Lär dig hur du lägger till ett befintligt anpassat DNS-domännamn (anpassad Domain) till en webbapp, Server del för mobilapp eller API-app i Azure App Service.
keywords: app service, azure app service, domain mapping, domain name, existing domain, hostname
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./app-service-web-tutorial-custom-domain-uiex
ms.openlocfilehash: 216c96579944661ecf248e4e8676ac45021c53e6
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2021
ms.locfileid: "106293965"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Självstudie: mappa ett befintligt anpassat DNS-namn till Azure App Service

[Azure App Service](overview.md) ger en mycket skalbar och automatisk korrigering av webb värd tjänst. Den här självstudien visar hur du mappar ett befintligt namn på en anpassad Domain Name System (DNS) till App Service.

![Skärm bild som visar Azure Portal navigering till en Azure-App.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Mappa en under domän (till exempel `www.contoso.com` ) med hjälp av en CNAME-post.
> * Mappa en rot domän (till exempel `contoso.com` ) med hjälp av en a-post.
> * Mappa en domän med jokertecken (till exempel `*.contoso.com` ) med hjälp av en CNAME-post.
> * Omdirigera standard-URL: en till en anpassad katalog.
> * Automatisera domän mappning med skript.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* [Skapa en App Service-app](./index.yml), eller använd en app som du har skapat för en annan kurs.
* Köp ett domän namn och se till att du har åtkomst till DNS-registret för din domän leverantör (till exempel GoDaddy).

  För att till exempel lägga till DNS-poster för `contoso.com` och `www.contoso.com` måste du kunna konfigurera DNS-inställningarna för rotdomänen `contoso.com`.

  > [!NOTE]
  > Om du inte har ett befintligt domän namn kan du överväga att [köpa en domän med hjälp av Azure Portal](manage-custom-dns-buy-domain.md).

## <a name="prepare-the-app"></a>Förbereda appen

Om du vill mappa ett anpassat DNS-namn till en webbapp måste webbappens [App Service plan](https://azure.microsoft.com/pricing/details/app-service/) vara en betald nivå (delad, Basic, standard, Premium eller konsumtion för Azure Functions). I det här steget ser du till att App Service-appen har en prisnivå som stöds.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

  > [!NOTE]
  > Om du tar bort eller lägger till en anpassad domän i din webbapp kommer platsen att startas om.
### <a name="sign-in-to-azure"></a>Logga in på Azure

Öppna [Azure Portal](https://portal.azure.com)och logga in med ditt Azure-konto.

### <a name="select-the-app-in-the-azure-portal"></a>Välj appen i Azure Portal

1. Sök efter och välj **app Services**.

   ![Skärm bild som visar val av App Services.](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. På sidan **app Services** väljer du namnet på din Azure-App.

   ![Skärm bild som visar Portal navigering till en Azure-App.](./media/app-service-web-tutorial-custom-domain/select-app.png)

Du ser hanteringssidan för App Service-appen.

<a name="checkpricing" aria-hidden="true"></a>

### <a name="check-the-pricing-tier"></a>Kontrollera prisnivån

1. I det vänstra fönstret på App-sidan, bläddrar du till avsnittet **Inställningar** och väljer **skala upp (App Service plan)**.

   ![Skärm bild som visar menyn skala upp (App Service plan).](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. Appens aktuell nivå markeras med en blå kantlinje. Kontrol lera att appen inte finns på **F1** -nivån. Anpassad DNS stöds inte på **F1** -nivån.

   ![Skärm bild som visar rekommenderade pris nivåer.](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. Om App Service plan inte finns på **F1** -nivån, Stäng sidan **skala upp** och hoppa över för att [Mappa en CNAME-post](#map-a-cname-record).

<a name="scaleup" aria-hidden="true"></a>

### <a name="scale-up-the-app-service-plan"></a>Skala upp App Service-planen

1. Välj någon av betalnivåerna (**D1**, **B1**, **B2**, **B3** eller en nivå i kategorin **Produktion**). Om du vill ha fler alternativ väljer du **Se ytterligare alternativ**.

1. Välj **Använd**.

   ![Skärm bild som visar kontrollerar pris nivån.](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   När du ser följande meddelande har skalningsåtgärden slutförts.

   ![Skärm bild som visar bekräftelse av skalnings åtgärd.](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname" aria-hidden="true"></a>

## <a name="get-a-domain-verification-id"></a>Hämta ett verifierings-ID för domän

Om du vill lägga till en anpassad domän i din app måste du verifiera din ägande av domänen genom att lägga till ett verifierings-ID som en TXT-post med din domän leverantör. Välj **anpassade domäner** i den vänstra rutan på din app-sida. Kopiera ID: t i rutan **anpassad domän verifierings-ID** på sidan **anpassade domäner** för nästa steg.

![Skärm bild som visar ID i rutan anpassad ID för domän verifiering.](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

> [!WARNING]
> Om du lägger till domän verifierings-ID: n i din anpassade domän kan du förhindra Dangling DNS-poster och hjälpa till att undvika under domäner. För anpassade domäner som du tidigare har konfigurerat utan detta verifierings-ID bör du skydda dem från samma risk genom att lägga till verifierings-ID: t i din DNS-post. Mer information om det här vanliga hot med hög allvarlighets grad finns i avsnittet övertag ande av [under domäner](../security/fundamentals/subdomain-takeover.md).

## <a name="map-your-domain"></a>Mappa din domän

Du kan mappa ett anpassat DNS-namn till App Service med antingen en CNAME-post eller en A-post. Följ respektive steg:

- [Mappa en CNAME-post](#map-a-cname-record)
- [Mappa en A-post](#map-an-a-record)
- [Mappa en domän med jokertecken (med en CNAME-post)](#map-a-wildcard-domain)

> [!NOTE]
> Du bör använda CNAME-poster för alla anpassade DNS-namn förutom rotdomäner (till exempel `contoso.com`). För rotdomäner använder du A-poster.

### <a name="map-a-cname-record"></a>Mappa en CNAME-post

I kursexemplet lägger du till en CNAME-post för `www`-underdomänen (till exempel `www.contoso.com`).

Om du har en annan under domän än `www` ersätter du `www` med under domänen (till exempel med `sub` om din anpassade domän är `sub.constoso.com` ).

#### <a name="access-dns-records-with-a-domain-provider"></a>Åtkomst till DNS-poster med en domän leverantör

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Skapa CNAME-posten

Mappa en under domän till appens standard domän namn ( `<app-name>.azurewebsites.net` där `<app-name>` är namnet på din app). Skapa en CNAME-mappning för under `www` domänen genom att skapa två poster:

| Posttyp | Värd | Värde | Kommentarer |
| - | - | - |
| CNAME | `www` | `<app-name>.azurewebsites.net` | Själva domän mappningen. |
| TXT | `asuid.www` | [Verifierings-ID: t som du fick tidigare](#get-a-domain-verification-id) | App Service använder txt- `asuid.<subdomain>` posten för att verifiera din ägande av den anpassade domänen. |

När du har lagt till CNAME-och TXT-posterna ser sidan DNS-poster ut som i följande exempel:

![Skärm bild som visar Portal navigeringen till en Azure-App.](./media/app-service-web-tutorial-custom-domain/cname-record.png)

#### <a name="enable-the-cname-record-mapping-in-azure"></a>Aktivera CNAME-postmappning i Azure

1. I den vänstra rutan på sidan app i Azure Portal väljer du **anpassade domäner**.

    ![Skärm bild som visar menyn anpassade domäner.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. På sidan **anpassade domäner** i appen lägger du till det fullständigt kvalificerade DNS-namnet ( `www.contoso.com` ) i listan.

1. Välj **Lägg till anpassad domän**.

    ![Skärm bild som visar objektet Lägg till värddator namn.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Skriv det fullständigt kvalificerade domännamnet som du lade till en CNAME-post för, till exempel `www.contoso.com`.

1. Välj **Verifiera**. Sidan **Lägg till anpassad domän** visas.

1. Se till att **post typen hostname** är inställd på **CNAME (www- \. example.com eller någon under domän)**. Välj **Lägg till anpassad domän**.

    ![Skärm bild som visar knappen Lägg till anpassad domän.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Det kan ta lite tid innan den nya anpassade domänen visas på sidan **anpassade domäner** för appen. Uppdatera webbläsaren för att uppdatera data.

    ![Skärm bild som visar hur du lägger till CNAME-posten.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > En varnings etikett för din anpassade domän innebär att den ännu inte har bundits till ett TLS/SSL-certifikat. Eventuella HTTPS-förfrågningar från en webbläsare till din anpassade domän får ett fel eller en varning, beroende på webbläsaren. Om du vill lägga till en TLS-bindning, se [skydda ett anpassat DNS-namn med en TLS/SSL-bindning i Azure App Service](configure-ssl-bindings.md).

    Om du missade ett steg eller gjort ett stavfel någonstans tidigare visas ett verifierings fel längst ned på sidan.

    ![Skärm bild som visar ett verifierings fel.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

### <a name="map-an-a-record"></a>Mappa en A-post

I kursexemplet lägger du till en A-post för rotdomänen (till exempel `contoso.com`).

<a name="info"></a>

#### <a name="copy-the-apps-ip-address"></a>Kopiera appens IP-adress

För att kunna mappa A-posten behöver du appens externa IP-adress. Du hittar den här IP-adressen på sidan **anpassade domäner** för appen i Azure Portal.

1. I den vänstra rutan på sidan app i Azure Portal väljer du **anpassade domäner**.

   ![Skärm bild som visar menyn anpassade domäner.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. På sidan **anpassade domäner** kopierar du appens IP-adress.

   ![Skärm bild som visar Portal navigering till en Azure-App.](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

#### <a name="access-dns-records-with-a-domain-provider"></a>Åtkomst till DNS-poster med en domän leverantör

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-a-record"></a>Skapa en A-post

För att mappa en A-post till en app, vanligt vis till rot domänen, skapar du två poster:

| Posttyp | Värd | Värde | Kommentarer |
| - | - | - |
| A | `@` | IP-adress från [Kopiera appens IP-adress](#info) | Själva domän mappningen ( `@` representerar vanligt vis rot domänen). |
| TXT | `asuid` | [Verifierings-ID: t som du fick tidigare](#get-a-domain-verification-id) | App Service använder txt- `asuid.<subdomain>` posten för att verifiera din ägande av den anpassade domänen. Använd för rot domänen `asuid` . |

> [!NOTE]
> Om du vill lägga till en under domän (t. ex. `www.contoso.com` ) med hjälp av en a-post i stället för en rekommenderad [CNAME-post](#map-a-cname-record)bör din post-och TXT-post se ut som i följande tabell:
>
> | Posttyp | Värd | Värde |
> | - | - | - |
> | A | `www` | IP-adress från [Kopiera appens IP-adress](#info) |
> | TXT | `asuid.www` | [Verifierings-ID: t som du fick tidigare](#get-a-domain-verification-id) |
>

När posterna har lagts till ser sidan DNS-poster ut som i följande exempel:

![Skärm bild som visar sidan DNS-poster.](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a" aria-hidden="true"></a>

#### <a name="enable-the-a-record-mapping-in-the-app"></a>Aktivera A-postmappning i appen

Gå tillbaka till sidan **anpassade domäner** i appen i Azure Portal, Lägg till det fullständigt kvalificerade DNS-namnet (till exempel `contoso.com` ) i listan.

1. Välj **Lägg till anpassad domän**.

    ![Skärm bild som visar hur du lägger till ett värdnamn.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Skriv det fullständigt kvalificerade domännamnet som du konfigurerade A-posten för, till exempel `contoso.com`. 

1. Välj **Verifiera**. Sidan **Lägg till anpassad domän** visas.

1. Se till att **Posttyp för värddatornamn** har värdet **A-post (example.com)**. Välj **Lägg till anpassad domän**.

    ![Skärm bild som visar hur du lägger till ett DNS-namn i appen.](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    Det kan ta lite tid innan den nya anpassade domänen visas på sidan **anpassade domäner** för appen. Uppdatera webbläsaren för att uppdatera data.

    ![Skärm bild som visar hur du lägger till en A-post.](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    > [!NOTE]
    > En varnings etikett för din anpassade domän innebär att den ännu inte har bundits till ett TLS/SSL-certifikat. Eventuella HTTPS-förfrågningar från en webbläsare till din anpassade domän får ett fel eller en varning, beroende på webbläsaren. Om du vill lägga till en TLS-bindning, se [skydda ett anpassat DNS-namn med en TLS/SSL-bindning i Azure App Service](configure-ssl-bindings.md).
    
    Om du missade ett steg eller gjort ett stavfel någonstans tidigare visas ett verifierings fel längst ned på sidan.
    
    ![Skärm bild som visar ett verifierings fel.](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

### <a name="map-a-wildcard-domain"></a>Mappa en domän med jokertecken

I kursexemplet mappar du ett [DNS-namn med jokertecken](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (till exempel `*.contoso.com`) till App Service-appen genom att lägga till en CNAME-post.

#### <a name="access-dns-records-with-a-domain-provider"></a>Åtkomst till DNS-poster med en domän leverantör

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Skapa CNAME-posten

Mappa ett jokertecken `*` till appens standard domän namn ( `<app-name>.azurewebsites.net` där `<app-name>` är namnet på din app). Skapa två poster för att mappa namnet på jokertecken:

| Posttyp | Värd | Värde | Kommentarer |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | Själva domän mappningen. |
| TXT | `asuid` | [Verifierings-ID: t som du fick tidigare](#get-a-domain-verification-id) | App Service använder txt- `asuid` posten för att verifiera din ägande av den anpassade domänen. |

För `*.contoso.com`-domänexemplet mappar CNAME-posten namnet `*` till `<app-name>.azurewebsites.net`.

När CNAME har lagts till ser sidan för DNS-poster ut som i följande exempel:

![Skärm bild som visar navigeringen till en Azure-App.](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

#### <a name="enable-the-cname-record-mapping-in-the-app"></a>Aktivera CNAME-postmappning i appen

Nu kan du lägga till alla under domäner som matchar jokertecknets namn i appen (till exempel, `sub1.contoso.com` `sub2.contoso.com` och `*.contoso.com` båda matchningarna `*.contoso.com` ).

1. I den vänstra rutan på sidan app i Azure Portal väljer du **anpassade domäner**.

    ![Skärm bild som visar menyn anpassade domäner.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Välj **Lägg till anpassad domän**.

    ![Skärm bild som visar hur du lägger till ett värdnamn.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Ange ett fullständigt kvalificerat domännamn som matchar domänen med jokertecken (till exempel `sub1.contoso.com`) och välj sedan **Verifiera**.

    Knappen **Lägg till anpassad domän** är aktive rad.

1. Se till att **post typen hostname** är inställd på **CNAME-post (www- \. example.com eller under domän)**. Välj **Lägg till anpassad domän**.

    ![Skärm bild som visar tillägget av ett DNS-namn till appen.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    Det kan ta lite tid innan den nya anpassade domänen visas på sidan **anpassade domäner** för appen. Uppdatera webbläsaren för att uppdatera data.

1. Välj **+** ikonen igen för att lägga till en annan anpassad domän som matchar domänen med jokertecken. Lägg exempelvis till `sub2.contoso.com`.

    ![Skärm bild som visar hur du lägger till en CNAME-post.](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard-2.png)

    > [!NOTE]
    > En varnings etikett för din anpassade domän innebär att den ännu inte har bundits till ett TLS/SSL-certifikat. Eventuella HTTPS-förfrågningar från en webbläsare till din anpassade domän får ett fel eller en varning, beroende på webbläsaren. Om du vill lägga till en TLS-bindning, se [skydda ett anpassat DNS-namn med en TLS/SSL-bindning i Azure App Service](configure-ssl-bindings.md).
    
## <a name="test-in-a-browser"></a>Testa i en webbläsare

Bläddra till de DNS-namn som du konfigurerade tidigare (till exempel,,, `contoso.com` `www.contoso.com` `sub1.contoso.com` och `sub2.contoso.com` ).

![Skärm bild som visar navigering till en Azure-App.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-not-found"></a>Lös 404 "hittades inte"

Om du får ett HTTP 404-fel (hittades inte) när du bläddrar till URL: en för din anpassade domän, kontrollerar du att domänen matchar appens IP-adress med hjälp av <a href="https://www.nslookup.io/" target="_blank">nslookup.io</a>. Om inte kontrollerar du att posterna A och CNAME är korrekt konfigurerade med samma plats. Om den löser in IP-adressen korrekt, men du ändå får en 404, kan din webbläsare ha cachelagrat den gamla IP-adressen för din domän. Rensa cacheminnet och testa DNS-matchningen igen. På en Windows-dator rensar du cachen med `ipconfig /flushdns`.

## <a name="migrate-an-active-domain"></a>Migrera en aktiv domän

Om du vill migrera en live-webbplats och dess DNS-domännamn till App Service utan avbrott kan du läsa [Migrera ett aktivt DNS-namn till Azure App Service](manage-custom-dns-migrate-domain.md).

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>Omdirigera till en anpassad katalog

Som standard dirigerar App Service webbegäranden till rotkatalogen för din appkod. Men vissa webb ramverk startar inte i rot katalogen. [Laravel](https://laravel.com/) startar till exempel i underkatalogen `public`. För att fortsätta med `contoso.com` DNS-exemplet är en sådan app tillgänglig på `http://contoso.com/public` , men du vill direkt `http://contoso.com` till `public` katalogen i stället. Det här steget omfattar inte DNS-matchning, men handlar om att anpassa den virtuella katalogen.

Om du vill anpassa en virtuell katalog för Windows-appar väljer du **program inställningar** i den vänstra rutan på sidan för din webbapp. 

> [!NOTE]
> Linux-appar har inte den här sidan. Om du vill ändra plats roten för Linux-appar, se språkspecifika konfigurations guider ([php](configure-language-php.md?pivots=platform-linux#change-site-root), till exempel).

Längst ned på sidan pekar den virtuella rotkatalogen `/` till `site\wwwroot` som standard, vilket är rotkatalogen för din appkod. Ändra den så att den pekar till exempelvis `site\wwwroot\public` i stället, och spara ändringarna.

![Skärm bild som visar anpassning av en virtuell katalog.](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

När åtgärden har slutförts ska din app returnera rätt sida på rot Sök vägen (till exempel `http://contoso.com` ).

## <a name="automate-with-scripts"></a>Automatisera med skript

Du kan automatisera hanteringen av anpassade domäner med skript med hjälp av [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/).

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
> * Mappa en under domän med hjälp av en CNAME-post.
> * Mappa en rot domän med hjälp av en A-post.
> * Mappa en domän med jokertecken med hjälp av en CNAME-post.
> * Omdirigera standard-URL: en till en anpassad katalog.
> * Automatisera domän mappning med skript.

Fortsätt till nästa självstudie och lär dig hur du binder ett anpassat TLS/SSL-certifikat till en webbapp.

> [!div class="nextstepaction"]
> [Skydda ett anpassat DNS-namn med en TLS/SSL-bindning i Azure App Service](configure-ssl-bindings.md)
