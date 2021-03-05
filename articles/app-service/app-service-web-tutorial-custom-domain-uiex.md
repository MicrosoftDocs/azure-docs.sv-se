---
title: 'Självstudie: mappa ett befintligt anpassat DNS-namn'
description: Lär dig hur du lägger till ett befintligt anpassat DNS-domännamn (anpassad Domain) till en webbapp, Server del för mobilapp eller API-app i Azure App Service.
keywords: App Service, Azure App Service, domän mappning, domän namn, befintlig domän, värdnamn, anpassad-domän
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ca1308c969227336bfb4970f7c5c77b9f2e0cc22
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102216538"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Självstudie: mappa ett befintligt anpassat DNS-namn till Azure App Service

Den här självstudien visar hur du mappar befintliga <abbr title="Ett domän namn som du har köpt från en domän registrator, till exempel GoDaddy, eller en under domän till din köpta domän.">anpassat DNS-domännamn</abbr> på <abbr title="En HTTP-baserad tjänst som är värd för webb program, REST API: er och mobila backend-program.">Azure App Service</abbr>.

I den här självstudien får du lära dig att:

> [!div class="checklist"]
> * Mappa en under domän med hjälp av en <abbr title="En DNS-kanoniskt namn post mappar ett domän namn till ett annat.">CNAME-post</abbr>.
> * Mappa en rot domän med hjälp av en <abbr title="En adress post i DNS mappar ett värdnamn till en IP-adress.">A-post</abbr>.
> * Mappa en domän med jokertecken med hjälp av en CNAME-post.
> * Omdirigera standard-URL: en till en anpassad katalog.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Förbered din miljö

* [Skapa en App Service-app](./index.yml), eller använd en app som du har skapat för en annan kurs.
* Se till att du kan redigera DNS-poster för din anpassade domän. Om du inte har en anpassad domän än kan du [köpa en app service-domän](manage-custom-dns-buy-domain.md).

    <details>
        <summary>Vad behöver jag för att redigera DNS-poster?</summary>
        Kräver åtkomst till DNS-registret för din domän leverantör, till exempel GoDaddy. För att till exempel lägga till DNS-poster för <code>contoso.com</code> och <code>www.contoso.com</code> måste du kunna konfigurera DNS-inställningarna för rotdomänen <code>contoso.com</code>.
    </details>

<hr/> 

## <a name="2-prepare-the-app"></a>2. Förbered appen

För att mappa ett anpassat DNS-namn till en app, appens <abbr title="Anger plats, storlek och funktioner för den webb Server grupp som är värd för din app.">App Service-plan</abbr> måste vara en betald nivå (inte <abbr title="En Azure App Service nivå där din app körs på samma virtuella datorer som andra appar, inklusive andra kunders appar. Den här nivån är avsedd för utveckling och testning.">**Kostnads fri (F1)**</abbr>). Mer information finns i [Översikt över Azure App Service plan](overview-hosting-plans.md).

#### <a name="sign-in-to-azure"></a>Logga in på Azure

Öppna [Azure Portal](https://portal.azure.com)och logga in med ditt Azure-konto.

#### <a name="select-the-app-in-the-azure-portal"></a>Välj appen i Azure Portal

1. Sök efter och välj **app Services**.

   ![Skärm bild som visar val av App Services.](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. På sidan **app Services** väljer du namnet på din Azure-App.

   ![Skärm bild som visar Portal navigering till en Azure-App.](./media/app-service-web-tutorial-custom-domain/select-app.png)

    Du ser hanteringssidan för App Service-appen.

<a name="checkpricing" aria-hidden="true"></a>

#### <a name="check-the-pricing-tier"></a>Kontrollera prisnivån

1. I det vänstra fönstret på App-sidan, bläddrar du till avsnittet **Inställningar** och väljer **skala upp (App Service plan)**.

   ![Skärm bild som visar menyn skala upp (App Service plan).](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. Appens aktuell nivå markeras med en blå kantlinje. Kontrol lera att appen inte finns på **F1** -nivån. Anpassad DNS stöds inte på **F1** -nivån.

   ![Skärm bild som visar rekommenderade pris nivåer.](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. Om App Service plan inte finns på **F1** -nivån stänger du sidan **skala upp** och hoppar till [3. Hämta ett verifierings-ID för domän](#3-get-a-domain-verification-id).

<a name="scaleup" aria-hidden="true"></a>

#### <a name="scale-up-the-app-service-plan"></a>Skala upp App Service-planen

1. Välj någon av betalnivåerna (**D1**, **B1**, **B2**, **B3** eller en nivå i kategorin **Produktion**). Om du vill ha fler alternativ väljer du **Se ytterligare alternativ**.

1. Välj **Använd**.

   ![Skärm bild som visar kontrollerar pris nivån.](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   När du ser följande meddelande har skalningsåtgärden slutförts.

   ![Skärm bild som visar bekräftelse av skalnings åtgärd.](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<hr/> 

<a name="cname" aria-hidden="true"></a>

## <a name="3-get-a-domain-verification-id"></a>3. Hämta ett domän verifierings-ID

Om du vill lägga till en anpassad domän i din app måste du verifiera din ägande av domänen genom att lägga till ett verifierings-ID som en TXT-post med din domän leverantör. 

1. Välj **anpassade domäner** i den vänstra rutan på din app-sida. 
1. Kopiera ID: t i rutan **anpassad domän verifierings-ID** på sidan **anpassade domäner** för nästa steg.

    ![Skärm bild som visar ID i rutan anpassad ID för domän verifiering.](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

    <details>
        <summary>Varför behöver jag det här?</summary>
        Om du lägger till domän verifierings-ID: n i din anpassade domän kan du förhindra Dangling DNS-poster och hjälpa till att undvika under domäner. För anpassade domäner som du tidigare har konfigurerat utan detta verifierings-ID bör du skydda dem från samma risk genom att lägga till verifierings-ID: t i din DNS-post. Mer information om det här vanliga hot med hög allvarlighets grad finns i avsnittet övertag ande av <a href="/azure/security/fundamentals/subdomain-takeover">under domäner</a>.
    </details>
    
<a name="info"></a>

3. **(Endast en post)** För att mappa en <abbr title="En adress post i DNS mappar ett värdnamn till en IP-adress.">A-post</abbr>behöver du appens externa IP-adress. På sidan **anpassade domäner** kopierar du värdet för **IP-adress**.

   ![Skärm bild som visar Portal navigering till en Azure-App.](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

<hr/> 

## <a name="4-create-the-dns-records"></a>4. Skapa DNS-posterna

1. Logga in på webbplatsen till din domänleverantör.

    <details>
        <summary>Kan jag hantera DNS från min domän leverantör med Azure?</summary>
        Om du vill kan du använda Azure DNS för att hantera DNS-poster för din domän och konfigurera ett anpassat DNS-namn för Azure App Service. Mer information finns i <a href="https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns">Självstudier: vara värd för din domän i Azure DNS></a>.
    </details>

1. Sök upp sidan för hantering av DNS-poster. 

    <details>
        <summary>Hur gör jag för att hittar du sidan?</summary>
        <p>Leverantören för varje domän har sitt eget DNS-postgränssnitt, så läs leverantörens dokumentation. Leta efter områden på webbplatsen med namnet <strong>Domännamn</strong>, <strong>DNS</strong>, eller <strong>Namnserverhantering</strong>.</p>
        <p>Du hittar ofta sidan DNS-poster genom att visa din konto information och sedan söka efter en länk, till exempel <strong>Mina domäner</strong>. Gå till sidan och leta efter en länk som heter något som <strong>zonfilen</strong>, <strong>DNS-poster</strong>eller <strong>Avancerad konfiguration</strong>.</p>
    </details>

   Skärmbilden nedan är ett exempel på en sida med DNS-poster:

   ![Skärm bild som visar en exempel sida med DNS-poster.](../../includes/media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. Välj **Lägg till** eller lämplig widget för att skapa en post. 

1. Välj den typ av post som ska skapas och följ instruktionerna. Du kan antingen använda en <abbr title="En kanonisk namn post i DNS mappar ett domän namn (ett alias) till ett annat (det kanoniska namnet).">CNAME-post</abbr> eller en <abbr title="En adress post i DNS mappar ett värdnamn till en IP-adress.">A-post</abbr> för att mappa ett anpassat DNS-namn till App Service. 

    <details>
        <summary>Vilken post ska jag välja?</summary>
        <div>
            <ul>
            <li>Använd en A-post för att mappa rot domänen (t. ex. <code>contoso.com</code> ). Använd inte CNAME-posten för rot posten (mer information finns i <a href="https://en.wikipedia.org/wiki/CNAME_record">posten Wikipedia</a>).</li>
            <li>Om du vill mappa en under domän (till exempel <code>www.contoso.com</code> ) använder du en CNAME-post.</li>
            <li>Du kan mappa en under domän till appens IP-adress direkt med en A-post, men det är möjligt <a href="https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips#when-inbound-ip-changes">att IP-adressen ändras</a>. CNAME mappar till appens värdnamn istället, vilket är mindre känsligt att ändra.</li>
            <li>Om du vill mappa en <a href="https://en.wikipedia.org/wiki/Wildcard_DNS_record">domän med jokertecken</a> (till exempel <code>*.contoso.com</code> ) använder du en CNAME-post.</li>
            </ul>
        </div>
    </details>
    
# <a name="cname"></a>[CNAME](#tab/cname)

För en under domän som `www` i `www.contoso.com` skapar du två poster enligt följande tabell:

| Posttyp | Värd | Värde | Kommentarer |
| - | - | - |
| CNAME | `<subdomain>` (till exempel `www` ) | `<app-name>.azurewebsites.net` | Själva domän mappningen. |
| TXT | `asuid.<subdomain>` (till exempel `asuid.www` ) | [Verifierings-ID: t som du fick tidigare](#3-get-a-domain-verification-id) | App Service använder txt- `asuid.<subdomain>` posten för att verifiera din ägande av den anpassade domänen. |

![Skärm bild som visar Portal navigeringen till en Azure-App.](./media/app-service-web-tutorial-custom-domain/cname-record.png)
    
# <a name="a"></a>[A](#tab/a)

För en rotdomän som `contoso.com` skapar du två poster enligt följande tabell:

| Posttyp | Värd | Värde | Kommentarer |
| - | - | - |
| A | `@` | IP-adress från [Kopiera appens IP-adress](#3-get-a-domain-verification-id) | Själva domän mappningen ( `@` representerar vanligt vis rot domänen). |
| TXT | `asuid` | [Verifierings-ID: t som du fick tidigare](#3-get-a-domain-verification-id) | App Service använder txt- `asuid.<subdomain>` posten för att verifiera din ägande av den anpassade domänen. Använd för rot domänen `asuid` . |

![Skärm bild som visar sidan DNS-poster.](./media/app-service-web-tutorial-custom-domain/a-record.png)

<details>
<summary>Vad händer om jag vill mappa en under domän med en A-post?</summary>
För att mappa en under domän som `www.contoso.com` med en a-post i stället för en rekommenderad CNAME-post bör din post-och TXT-post se ut som i följande tabell i stället:

<div class="table-scroll-wrapper"><table class="table"><caption class="visually-hidden">Tabell 3</caption>
<thead>
<tr>
<th>Posttyp</th>
<th>Värd</th>
<th>Värde</th>
</tr>
</thead>
<tbody>
<tr>
<td>A</td>
<td><code>&lt;subdomain&gt;</code> (till exempel <code>www</code> )</td>
<td>IP-adress från <a href="#info" data-linktype="self-bookmark">Kopiera appens IP-adress</a></td>
</tr>
<tr>
<td>TXT</td>
<td><code>asuid.&lt;subdomain&gt;</code> (till exempel <code>asuid.www</code> )</td>
<td><a href="#3-get-a-domain-verification-id" data-linktype="self-bookmark">Verifierings-ID: t som du fick tidigare</a></td>
</tr>
</tbody>
</table></div>
</details>

# <a name="wildcard-cname"></a>[Jokertecken (CNAME)](#tab/wildcard)

För ett jokertecken som `*` i `*.contoso.com` skapar du två poster enligt följande tabell:

| Posttyp | Värd | Värde | Kommentarer |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | Själva domän mappningen. |
| TXT | `asuid` | [Verifierings-ID: t som du fick tidigare](#3-get-a-domain-verification-id) | App Service använder txt- `asuid` posten för att verifiera din ägande av den anpassade domänen. |

![Skärm bild som visar navigeringen till en Azure-App.](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)
    
-----

<details>
<summary>Mina ändringar raderas när jag har lämnat sidan.</summary>
<p>För vissa leverantörer, till exempel GoDaddy, börjar ändringar i DNS-posterna inte att gälla förrän du väljer en separat <strong>Spara ändringar</strong>-länk.</p>
</details>

<hr/>

## <a name="5-enable-the-mapping-in-your-app"></a>5. Aktivera mappningen i din app

1. I den vänstra rutan på sidan app i Azure Portal väljer du **anpassade domäner**.

    ![Skärm bild som visar menyn anpassade domäner.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Välj **Lägg till anpassad domän**.

    ![Skärm bild som visar objektet Lägg till värddator namn.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

# <a name="cname"></a>[CNAME](#tab/cname)

3. Skriv det fullständigt kvalificerade domännamnet som du lade till en CNAME-post för, till exempel `www.contoso.com`.

1. Välj **Verifiera**. Sidan **Lägg till anpassad domän** visas.

1. Se till att **post typen hostname** är inställd på **CNAME (www- \. example.com eller någon under domän)**. Välj **Lägg till anpassad domän**.

    ![Skärm bild som visar knappen Lägg till anpassad domän.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Det kan ta lite tid innan den nya anpassade domänen visas på sidan **anpassade domäner** för appen. Uppdatera webbläsaren för att uppdatera data.

    ![Skärm bild som visar hur du lägger till CNAME-posten.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    <details>
        <summary>Vad är med den <strong>osäkra</strong> varnings etiketten?</summary>
        En varnings etikett för din anpassade domän innebär att den ännu inte har bundits till ett TLS/SSL-certifikat. Eventuella HTTPS-förfrågningar från en webbläsare till din anpassade domän får ett fel eller en varning, beroende på webbläsaren. Om du vill lägga till en TLS-bindning, se <a href="https://docs.microsoft.com/azure/app-service/configure-ssl-bindings">skydda ett anpassat DNS-namn med en TLS/SSL-bindning i Azure App Service</a>.
    </details>

    Om du missade ett steg eller gjort ett stavfel någonstans tidigare visas ett verifierings fel längst ned på sidan.

    ![Skärm bild som visar ett verifierings fel.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

<a name="enable-a" aria-hidden="true"></a>

# <a name="a"></a>[A](#tab/a)

3. Skriv det fullständigt kvalificerade domännamnet som du konfigurerade A-posten för, till exempel `contoso.com`. 

1. Välj **Verifiera**. Sidan **Lägg till anpassad domän** visas.

1. Se till att **Posttyp för värddatornamn** har värdet **A-post (example.com)**. Välj **Lägg till anpassad domän**.

    ![Skärm bild som visar hur du lägger till ett DNS-namn i appen.](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    Det kan ta lite tid innan den nya anpassade domänen visas på sidan **anpassade domäner** för appen. Uppdatera webbläsaren för att uppdatera data.

    ![Skärm bild som visar hur du lägger till en A-post.](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    <details>
        <summary>Vad är med den <strong>osäkra</strong> varnings etiketten?</summary>
        En varnings etikett för din anpassade domän innebär att den ännu inte har bundits till ett TLS/SSL-certifikat. Eventuella HTTPS-förfrågningar från en webbläsare till din anpassade domän får ett fel eller en varning, beroende på webbläsaren. Om du vill lägga till en TLS-bindning, se <a href="https://docs.microsoft.com/azure/app-service/configure-ssl-bindings">skydda ett anpassat DNS-namn med en TLS/SSL-bindning i Azure App Service</a>.
    </details>
    
    Om du missade ett steg eller gjort ett stavfel någonstans tidigare visas ett verifierings fel längst ned på sidan.
    
    ![Skärm bild som visar ett verifierings fel.](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

# <a name="wildcard-cname"></a>[Jokertecken (CNAME)](#tab/wildcard)

3. Skriv ett fullständigt kvalificerat domän namn som matchar domänen med jokertecken. Till exempel `*.contoso.com` kan du använda `sub1.contoso.com` , `sub2.contoso.com` , `*.contoso.com` eller någon annan sträng som matchar mönstret jokertecken. Välj sedan **Verifiera**.

    Knappen **Lägg till anpassad domän** är aktive rad.

1. Se till att **post typen hostname** är inställd på **CNAME-post (www- \. example.com eller under domän)**. Välj **Lägg till anpassad domän**.

    ![Skärm bild som visar tillägget av ett DNS-namn till appen.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    Det kan ta lite tid innan den nya anpassade domänen visas på sidan **anpassade domäner** för appen. Uppdatera webbläsaren för att uppdatera data.

    <details>
        <summary>Vad är med den <strong>osäkra</strong> varnings etiketten?</summary>
        En varnings etikett för din anpassade domän innebär att den ännu inte har bundits till ett TLS/SSL-certifikat. Eventuella HTTPS-förfrågningar från en webbläsare till din anpassade domän får ett fel eller en varning, beroende på webbläsaren. Om du vill lägga till en TLS-bindning, se <a href="https://docs.microsoft.com/azure/app-service/configure-ssl-bindings">skydda ett anpassat DNS-namn med en TLS/SSL-bindning i Azure App Service</a>.
    </details>

-----

<hr/> 

## <a name="6-test-in-a-browser"></a>6. testa i en webbläsare

Bläddra till de DNS-namn som du konfigurerade tidigare.

![Skärm bild som visar navigering till en Azure-App.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

<a name="resolve-404-not-found" aria-hidden="true"></a>
<details>
<summary>Jag får ett HTTP 404-fel (hittades inte).</summary>
<ul>
<li>Den anpassade domänen som har kon figurer ATS saknar en A-post eller en CNAME-post.</li>
<li>Webbläsarklienten har cachat din domäns gamla IP-adress. Rensa cacheminnet och testa DNS-matchningen igen. På en Windows-dator rensar du cachen med <code>ipconfig /flushdns</code>.</li>
</ul>
</details>

<hr/> 

## <a name="migrate-an-active-domain"></a>Migrera en aktiv domän

Om du vill migrera en live-webbplats och dess DNS-domännamn till App Service utan avbrott kan du läsa [Migrera ett aktivt DNS-namn till Azure App Service](manage-custom-dns-migrate-domain.md).

<hr/> 

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>Omdirigera till en anpassad katalog

<details>
<summary>Behöver jag det här?</summary>
<p>Det beror på din app. Som standard dirigerar App Service webbegäranden till rotkatalogen för din appkod. Men vissa webb ramverk går&#39;t i rot katalogen. <a href="https://laravel.com/">Laravel</a> startar till exempel i underkatalogen <code>public</code>. För att fortsätta med <code>contoso.com</code> DNS-exemplet är en sådan app tillgänglig på <code>http://contoso.com/public</code> , men du vill direkt <code>http://contoso.com</code> till <code>public</code> katalogen i stället. </p>
</details>

Även om det här är ett vanligt scenario innefattar det inte anpassad domän mappning, utan handlar om att anpassa den virtuella katalogen i din app.

1. Välj **program inställningar** i den vänstra rutan på din webbapp.

1. Längst ned på sidan pekar den virtuella rotkatalogen `/` till `site\wwwroot` som standard, vilket är rotkatalogen för din appkod. Ändra den så att den pekar till exempelvis `site\wwwroot\public` i stället, och spara ändringarna.

    ![Skärm bild som visar anpassning av en virtuell katalog.](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

1. När åtgärden har slutförts kontrollerar du genom att navigera till appens rot Sök väg i webbläsaren (till exempel `http://contoso.com` eller `http://<app-name>.azurewebsites.net` ).

<hr/> 

## <a name="automate-with-scripts"></a>Automatisera med skript

Du kan automatisera hanteringen av anpassade domäner med skript med hjälp av [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/).

#### <a name="azure-cli"></a>Azure CLI

Följande kommando lägger till ett konfigurerat anpassat DNS-namn i en App Service-app.

```bash 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

Mer information finns i [Mappa en anpassad domän till en webbapp](scripts/cli-configure-custom-domain.md).

#### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Följande kommando lägger till ett konfigurerat anpassat DNS-namn i en App Service-app.

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

Mer information finns i [Tilldela en anpassad domän till en webbapp](scripts/powershell-configure-custom-domain.md).

<hr/> 

## <a name="next-steps"></a>Nästa steg

Fortsätt till nästa självstudie och lär dig hur du binder ett anpassat TLS/SSL-certifikat till en webbapp.

> [!div class="nextstepaction"]
> [Skydda ett anpassat DNS-namn med en TLS/SSL-bindning i Azure App Service](configure-ssl-bindings.md)
