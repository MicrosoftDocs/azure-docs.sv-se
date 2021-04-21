---
title: 'Självstudie: Mappa befintligt anpassat DNS-namn'
description: Lär dig hur du lägger till ett befintligt anpassat DNS-domännamn (anpassad domän) i en webbapp, serverdel för mobilappar eller EN API-app i Azure App Service.
keywords: app service, azure app service, domain mapping, domain name, existing domain, hostname, vanity domain
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18, devx-track-azurepowershell
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 1fcf8c681f4fad65209c27663045d4974be633f7
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833264"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Självstudie: Mappa ett befintligt anpassat DNS-namn till Azure App Service

Den här självstudien visar hur du mappar befintliga <abbr title="Ett domännamn som du har köpt från en domänregistrator, till exempel GoDaddy, eller en underdomän till din köpta domän.">anpassat DNS-domännamn</abbr> på <abbr title="En HTTP-baserad tjänst som är värd för webbprogram, REST API:er och mobila serverdelsprogram.">Azure App Service</abbr>.

I den här självstudien får du lära dig att:

> [!div class="checklist"]
> * Mappa en underdomän med hjälp av en <abbr title="En DNS-kanonisk namnpost mappar ett domännamn till ett annat.">CNAME-post</abbr>.
> * Mappa en rotdomän med hjälp av en <abbr title="En adresspost i DNS mappar ett värdnamn till en IP-adress.">A-post</abbr>.
> * Mappa en domän med jokertecken med hjälp av en CNAME-post.
> * Omdirigera standard-URL:en till en anpassad katalog.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Förbered din miljö

* [Skapa en App Service-app](./index.yml), eller använd en app som du har skapat för en annan kurs.
* Se till att du kan redigera DNS-poster för din anpassade domän. Om du inte har en anpassad domän än kan du köpa [en App Service domän](manage-custom-dns-buy-domain.md).

    <details>
        <summary>Vad behöver jag för att redigera DNS-poster?</summary>
        Kräver åtkomst till DNS-registret för din domänleverantör, till exempel GoDaddy. För att till exempel lägga till DNS-poster för <code>contoso.com</code> och <code>www.contoso.com</code> måste du kunna konfigurera DNS-inställningarna för rotdomänen <code>contoso.com</code>.
    </details>

<hr/> 

## <a name="2-prepare-the-app"></a>2. Förbered appen

Om du vill mappa ett anpassat DNS-namn till en app mappar du appens <abbr title="Anger plats, storlek och funktioner för webbservergruppen som är värd för din app.">App Service-plan</abbr> måste vara en betald nivå (inte <abbr title="En Azure App Service nivå där din app körs på samma virtuella datorer som andra appar, inklusive andra kunders appar. Den här nivån är avsedd för utveckling och testning.">**Kostnadsfri (F1)**</abbr>). Mer information finns i [Azure App Service översikt över](overview-hosting-plans.md).

#### <a name="sign-in-to-azure"></a>Logga in på Azure

Öppna [Azure Portal](https://portal.azure.com)och logga in med ditt Azure-konto.

#### <a name="select-the-app-in-the-azure-portal"></a>Välj appen i Azure Portal

1. Sök efter och välj **App Services**.

   ![Skärmbild som visar val av App Services.](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. På sidan **App Services** väljer du namnet på din Azure-app.

   ![Skärmbild som visar portalnavigering till en Azure-app.](./media/app-service-web-tutorial-custom-domain/select-app.png)

    Du ser hanteringssidan för App Service-appen.

<a name="checkpricing" aria-hidden="true"></a>

#### <a name="check-the-pricing-tier"></a>Kontrollera prisnivån

1. I den vänstra rutan på appsidan bläddrar du till **avsnittet Inställningar** och väljer Skala **upp (App Service plan).**

   ![Skärmbild som visar menyn Skala upp (App Service plan).](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. Appens aktuell nivå markeras med en blå kantlinje. Kontrollera att appen inte är på **F1-nivån.** Anpassad DNS stöds inte på **F1-nivån.**

   ![Skärmbild som visar Rekommenderade prisnivåer.](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. Om App Service plan inte finns på **F1-nivån** stänger du sidan **Skala upp** och går vidare till [3. Hämta ett domänverifierings-ID.](#3-get-a-domain-verification-id)

<a name="scaleup" aria-hidden="true"></a>

#### <a name="scale-up-the-app-service-plan"></a>Skala upp App Service-planen

1. Välj någon av betalnivåerna (**D1**, **B1**, **B2**, **B3** eller en nivå i kategorin **Produktion**). Om du vill ha fler alternativ väljer **du Se ytterligare alternativ.**

1. Välj **Använd**.

   ![Skärmbild som visar en kontroll av prisnivån.](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   När du ser följande meddelande har skalningsåtgärden slutförts.

   ![Skärmbild som visar bekräftelsen av skalningsåtgärden.](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<hr/> 

<a name="cname" aria-hidden="true"></a>

## <a name="3-get-a-domain-verification-id"></a>3. Hämta ett domänverifierings-ID

Om du vill lägga till en anpassad domän i din app måste du verifiera ägarskapet för domänen genom att lägga till ett verifierings-ID som en TXT-post hos din domänleverantör. 

1. I den vänstra rutan på appsidan väljer du **Anpassade domäner.** 
1. Kopiera ID:t i **rutan Custom Domain Verifierings-ID** på **sidan Anpassade** domäner för nästa steg.

    ![Skärmbild som visar ID:t i rutan Custom Domain Verifierings-ID.](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

    <details>
        <summary>Varför behöver jag detta?</summary>
        Om du lägger till domänverifierings-ID:n i din anpassade domän kan du förhindra att DNS-poster blir intjänade och att du undviker övertaganden av underdomäner. För anpassade domäner som du tidigare har konfigurerat utan detta verifierings-ID bör du skydda dem från samma risk genom att lägga till verifierings-ID:t i DNS-posten. Mer information om det här vanliga hotet med hög allvarlighetsgrad finns <a href="/azure/security/fundamentals/subdomain-takeover">i Övertagande av underdomäner.</a>
    </details>
    
<a name="info"></a>

3. **(Endast en post)** Mappa en <abbr title="En adresspost i DNS mappar ett värdnamn till en IP-adress.">A-post</abbr>behöver du appens externa IP-adress. På sidan **Anpassade domäner** kopierar du värdet för **IP-adressen**.

   ![Skärmbild som visar portalnavigering till en Azure-app.](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

<hr/> 

## <a name="4-create-the-dns-records"></a>4. Skapa DNS-posterna

1. Logga in på webbplatsen till din domänleverantör.

    <details>
        <summary>Kan jag hantera DNS från min domänleverantör med Azure?</summary>
        Om du vill kan du använda Azure DNS för att hantera DNS-poster för din domän och konfigurera ett anpassat DNS-namn för Azure App Service. Mer information finns i <a href="/azure/dns/dns-delegate-domain-azure-dns">Självstudie: Vara värd för din domän i Azure DNS></a>.
    </details>

1. Sök upp sidan för hantering av DNS-poster. 

    <details>
        <summary>Hur gör jag för att hitta sidan?</summary>
        <p>Leverantören för varje domän har sitt eget DNS-postgränssnitt, så läs leverantörens dokumentation. Leta efter områden på webbplatsen med namnet <strong>Domännamn</strong>, <strong>DNS</strong>, eller <strong>Namnserverhantering</strong>.</p>
        <p>Ofta kan du hitta sidan DNS-poster genom att visa din kontoinformation och sedan söka efter en länk, till exempel <strong>Mina domäner</strong>. Gå till den sidan och leta sedan efter en länk med namnet något som <strong>Zonfil,</strong> <strong>DNS-poster</strong>eller <strong>Avancerad konfiguration.</strong></p>
    </details>

   Skärmbilden nedan är ett exempel på en sida med DNS-poster:

   ![Skärmbild som visar ett exempel på en sida med DNS-poster.](../../includes/media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. Välj **Lägg** till eller lämplig widget för att skapa en post. 

1. Välj vilken typ av post du vill skapa och följ instruktionerna. Du kan använda antingen en <abbr title="En kanonisk namnpost i DNS mappar ett domännamn (ett alias) till ett annat (det kanoniska namnet).">CNAME-post</abbr> eller en <abbr title="En adresspost i DNS mappar ett värdnamn till en IP-adress.">A-post</abbr> för att mappa ett anpassat DNS-namn till App Service. 

    <details>
        <summary>Vilken post ska jag välja?</summary>
        <div>
            <ul>
            <li>Om du vill mappa rotdomänen (till <code>contoso.com</code> exempel ) använder du en A-post. Använd inte CNAME-posten för rotposten (mer information finns i <a href="https://en.wikipedia.org/wiki/CNAME_record">Wikipedia-posten</a>).</li>
            <li>Om du vill mappa en underdomän (till exempel <code>www.contoso.com</code> ) använder du en CNAME-post.</li>
            <li>Du kan mappa en underdomän till appens IP-adress direkt med en A-post, men <a href="/azure/app-service/overview-inbound-outbound-ips#when-inbound-ip-changes">ip-adressen kan ändra</a>. CNAME mappar i stället till appens värdnamn, vilket är mindre känsligt för ändringar.</li>
            <li>Om du vill <a href="https://en.wikipedia.org/wiki/Wildcard_DNS_record">mappa en domän med jokertecken</a> (till <code>*.contoso.com</code> exempel ) använder du en CNAME-post.</li>
            </ul>
        </div>
    </details>
    
# <a name="cname"></a>[CNAME](#tab/cname)

För en underdomän som `www` i skapar du två poster enligt följande `www.contoso.com` tabell:

| Posttyp | Värd | Värde | Kommentarer |
| - | - | - |
| CNAME | `<subdomain>` (till exempel `www` ) | `<app-name>.azurewebsites.net` | Själva domänmappningen. |
| TXT | `asuid.<subdomain>` (till exempel `asuid.www` ) | [Verifierings-ID:t du fick tidigare](#3-get-a-domain-verification-id) | App Service åtkomst till `asuid.<subdomain>` TXT-posten för att verifiera ägarskapet för den anpassade domänen. |

![Skärmbild som visar portalnavigeringen till en Azure-app.](./media/app-service-web-tutorial-custom-domain/cname-record.png)
    
# <a name="a"></a>[A](#tab/a)

För en rotdomän `contoso.com` som skapar du två poster enligt följande tabell:

| Posttyp | Värd | Värde | Kommentarer |
| - | - | - |
| A | `@` | IP-adress från [Kopiera appens IP-adress](#3-get-a-domain-verification-id) | Själva domänmappningen ( `@` representerar vanligtvis rotdomänen). |
| TXT | `asuid` | [Verifierings-ID:t du fick tidigare](#3-get-a-domain-verification-id) | App Service åtkomst till `asuid.<subdomain>` TXT-posten för att verifiera ägarskapet för den anpassade domänen. För rotdomänen använder du `asuid` . |

![Skärmbild som visar en sida med DNS-poster.](./media/app-service-web-tutorial-custom-domain/a-record.png)

<details>
<summary>Vad händer om jag vill mappa en underdomän med en A-post?</summary>
Om du vill mappa en underdomän som med en A-post i stället för en rekommenderad CNAME-post bör A-posten och TXT-posten se ut som `www.contoso.com` i följande tabell i stället:

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
<td><a href="#3-get-a-domain-verification-id" data-linktype="self-bookmark">Verifierings-ID:t du fick tidigare</a></td>
</tr>
</tbody>
</table></div>
</details>

# <a name="wildcard-cname"></a>[Jokertecken (CNAME)](#tab/wildcard)

För ett jokerteckennamn `*` som i skapar du två poster enligt följande `*.contoso.com` tabell:

| Posttyp | Värd | Värde | Kommentarer |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | Själva domänmappningen. |
| TXT | `asuid` | [Verifierings-ID:t som du fick tidigare](#3-get-a-domain-verification-id) | App Service åtkomst till `asuid` TXT-posten för att verifiera ditt ägarskap för den anpassade domänen. |

![Skärmbild som visar navigeringen till en Azure-app.](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)
    
-----

<details>
<summary>Mina ändringar raderas när jag lämnar sidan.</summary>
<p>För vissa leverantörer, till exempel GoDaddy, börjar ändringar i DNS-posterna inte att gälla förrän du väljer en separat <strong>Spara ändringar</strong>-länk.</p>
</details>

<hr/>

## <a name="5-enable-the-mapping-in-your-app"></a>5. Aktivera mappningen i din app

1. I den vänstra rutan på appsidan i Azure Portal väljer du **Anpassade domäner**.

    ![Skärmbild som visar menyn Anpassade domäner.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Välj **Lägg till anpassad domän.**

    ![Skärmbild som visar objektet Lägg till värdnamn.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

# <a name="cname"></a>[CNAME](#tab/cname)

3. Skriv det fullständigt kvalificerade domännamnet som du lade till en CNAME-post för, till exempel `www.contoso.com`.

1. Välj **Verifiera**. Sidan **Lägg till anpassad** domän visas.

1. Kontrollera att **Posttypen Värdnamn har** angetts till **CNAME (www \. example.com eller någon underdomän).** Välj **Lägg till anpassad domän.**

    ![Skärmbild som visar knappen Lägg till anpassad domän.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Det kan ta lite tid innan den nya anpassade domänen visas på appens sida **Anpassade** domäner. Uppdatera webbläsaren för att uppdatera data.

    ![Skärmbild som visar tillägg av CNAME-posten.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    <details>
        <summary>Vad är <strong>varningsetiketten Inte</strong> säker?</summary>
        En varningsetikett för din anpassade domän innebär att den ännu inte är bunden till ett TLS/SSL-certifikat. Alla HTTPS-förfrågningar från en webbläsare till din anpassade domän får ett fel eller en varning, beroende på webbläsaren. Information om hur du lägger till en TLS-bindning finns i Skydda ett anpassat DNS-namn med <a href="/azure/app-service/configure-ssl-bindings">en TLS/SSL-bindning i Azure App Service</a>.
    </details>

    Om du missade ett steg eller stavade fel någonstans tidigare visas ett verifieringsfel längst ned på sidan.

    ![Skärmbild som visar ett verifieringsfel.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

<a name="enable-a" aria-hidden="true"></a>

# <a name="a"></a>[A](#tab/a)

3. Skriv det fullständigt kvalificerade domännamnet som du konfigurerade A-posten för, till exempel `contoso.com`. 

1. Välj **Verifiera**. Sidan **Lägg till anpassad** domän visas.

1. Se till att **Posttyp för värddatornamn** har värdet **A-post (example.com)**. Välj **Lägg till anpassad domän.**

    ![Skärmbild som visar tillägg av ett DNS-namn i appen.](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    Det kan ta lite tid innan den nya anpassade domänen visas på appens sida **Anpassade** domäner. Uppdatera webbläsaren för att uppdatera data.

    ![Skärmbild som visar tillägg av en A-post.](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    <details>
        <summary>Vad är <strong>varningsetiketten Inte</strong> säker?</summary>
        En varningsetikett för din anpassade domän innebär att den ännu inte är bunden till ett TLS/SSL-certifikat. Alla HTTPS-förfrågningar från en webbläsare till din anpassade domän får ett fel eller en varning, beroende på webbläsaren. Information om hur du lägger till en TLS-bindning finns i Skydda ett anpassat DNS-namn med <a href="/azure/app-service/configure-ssl-bindings">en TLS/SSL-bindning i Azure App Service</a>.
    </details>
    
    Om du missade ett steg eller stavade fel någonstans tidigare visas ett verifieringsfel längst ned på sidan.
    
    ![Skärmbild som visar ett verifieringsfel.](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

# <a name="wildcard-cname"></a>[Jokertecken (CNAME)](#tab/wildcard)

3. Ange ett fullständigt kvalificerat domännamn som matchar domänen med jokertecken. Till exempel kan du använda , , eller någon annan sträng som `*.contoso.com` `sub1.contoso.com` matchar `sub2.contoso.com` `*.contoso.com` jokertecknets mönster. Välj sedan **Verifiera**.

    Knappen **Lägg till anpassad** domän aktiveras.

1. Kontrollera att **Posttypen Värdnamn är** inställd på **CNAME-post (www \. example.com eller någon underdomän).** Välj **Lägg till anpassad domän.**

    ![Skärmbild som visar tillägg av ett DNS-namn i appen.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    Det kan ta lite tid innan den nya anpassade domänen visas på appens sida **Anpassade** domäner. Uppdatera webbläsaren för att uppdatera data.

    <details>
        <summary>Vad är <strong>varningsetiketten Inte</strong> säker?</summary>
        En varningsetikett för din anpassade domän innebär att den ännu inte är bunden till ett TLS/SSL-certifikat. Alla HTTPS-förfrågningar från en webbläsare till din anpassade domän får ett fel eller en varning, beroende på webbläsaren. Information om hur du lägger till en TLS-bindning finns i Skydda ett anpassat DNS-namn med <a href="/azure/app-service/configure-ssl-bindings">en TLS/SSL-bindning i Azure App Service</a>.
    </details>

-----

<hr/> 

## <a name="6-test-in-a-browser"></a>6. Testa i en webbläsare

Bläddra till de DNS-namn som du konfigurerade tidigare.

![Skärmbild som visar navigering till en Azure-app.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

<a name="resolve-404-not-found" aria-hidden="true"></a>
<details>
<summary>Jag får ett HTTP 404-fel (hittades inte).</summary>
<ul>
<li>Den konfigurerade anpassade domänen saknar en A-post eller en CNAME-post.</li>
<li>Webbläsarklienten har cachat din domäns gamla IP-adress. Rensa cachen och testa DNS-upplösningen igen. På en Windows-dator rensar du cachen med <code>ipconfig /flushdns</code>.</li>
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
<p>Det beror på din app. Som standard dirigerar App Service webbegäranden till rotkatalogen för din appkod. Men vissa webbramverk startar&#39;inte i rotkatalogen. <a href="https://laravel.com/">Laravel</a> startar till exempel i underkatalogen <code>public</code>. Om du vill <code>contoso.com</code> fortsätta med DNS-exemplet är en sådan app tillgänglig på <code>http://contoso.com/public</code> , men du vill dirigera till katalogen i <code>http://contoso.com</code> <code>public</code> stället. </p>
</details>

Även om det här är ett vanligt scenario omfattar det inte anpassad domänmappning, utan handlar om att anpassa den virtuella katalogen i din app.

1. Välj **Programinställningar** i den vänstra rutan på webbappsidan.

1. Längst ned på sidan pekar den virtuella rotkatalogen `/` till `site\wwwroot` som standard, vilket är rotkatalogen för din appkod. Ändra den så att den pekar till exempelvis `site\wwwroot\public` i stället, och spara ändringarna.

    ![Skärmbild som visar anpassning av en virtuell katalog.](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

1. När åtgärden har avslutats kontrollerar du genom att gå till appens rotsökväg i webbläsaren (till exempel `http://contoso.com` eller `http://<app-name>.azurewebsites.net` ).

<hr/> 

## <a name="automate-with-scripts"></a>Automatisera med skript

Du kan automatisera hanteringen av anpassade domäner med skript med hjälp av [Azure CLI eller](/cli/azure/install-azure-cli) [Azure PowerShell](/powershell/azure/).

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

Fortsätt till nästa självstudie för att lära dig hur du binder ett anpassat TLS/SSL-certifikat till en webbapp.

> [!div class="nextstepaction"]
> [Skydda ett anpassat DNS-namn med en TLS/SSL-bindning i Azure App Service](configure-ssl-bindings.md)
