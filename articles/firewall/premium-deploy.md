---
title: Distribuera och konfigurera Azure Firewall Premium Preview
description: Lär dig hur du distribuerar och konfigurerar Azure Firewall Premium.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: fa106fac683619706f4be330ad1c4bff7b56f2dd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721794"
---
# <a name="deploy-and-configure-azure-firewall-premium-preview"></a>Distribuera och konfigurera Azure Firewall Premium Preview

> [!IMPORTANT]
> Azure Firewall Premium är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Azure Firewall Premium Preview är en nästa generations brand vägg med funktioner som krävs för mycket känsliga och reglerade miljöer. Det innehåller följande funktioner:

- **TLS-inspektion** – dekrypterar utgående trafik, bearbetar data och krypterar sedan data och skickar dem till målet.
- **IDP: er** – ett system för identifiering och skydd av nätverks intrång (IDP: er) gör att du kan övervaka nätverks aktiviteter för skadlig aktivitet, Logga information om den här aktiviteten, rapportera den och eventuellt försöka blockera den.
- **URL-filtrering** – utökar Azure-brandväggens funktioner för FQDN-filtrering för att överväga en hel URL. Till exempel i `www.contoso.com/a/c` stället för `www.contoso.com` .
- **Webb kategorier** – administratörer kan tillåta eller neka användar åtkomst till webbplats kategorier som spel webbplatser, sociala media webbplatser och andra.

Mer information finns i [Azure Firewall Premium-funktioner](premium-features.md).

Du använder en mall för att distribuera en test miljö som har ett centralt VNet (10.0.0.0/16) med tre undernät:
- ett arbets under nät (10.0.10.0/24)
- ett Azure skydds-undernät (10.0.20.0/24)
- ett brand Väggs undernät (10.0.100.0/24)

Ett enda centralt VNet används i den här test miljön för enkelhetens skull. I produktions syfte är en [nav-och eker-topologi](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) med peer-virtuella nätverk vanligare.

:::image type="content" source="media/premium-deploy/premium-topology.png" alt-text="Central VNet-topologi":::

Den virtuella datorn är en klient som skickar HTTP/S-begäranden via brand väggen.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="deploy-the-infrastructure"></a>Distribuera infrastrukturen

Mallen distribuerar en komplett test miljö för Azure Firewall Premium som är aktive rad med IDP: er, TLS-granskning, URL-filtrering och webb kategorier:

- en ny Azure Firewall Premium-och brand Väggs princip med fördefinierade inställningar för att möjliggöra enkel validering av dess kärn funktioner (IDP: er, TLS-inspektion, URL-filtrering och webb kategorier)
- distribuerar alla beroenden, inklusive Key Vault och en hanterad identitet. I en produktions miljö kan dessa resurser redan skapas och behövs inte i samma mall.
- genererar självsignerad rot certifikat utfärdare och distribuerar den på den genererade Key Vault
-  genererar en härledd mellanliggande certifikat utfärdare och distribuerar den på en virtuell Windows test-dator (WorkerVM)
- en skydds-värd (BastionHost) distribueras också och kan användas för att ansluta till Windows testing Machine (WorkerVM)



[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-premium%2Fazuredeploy.json)

## <a name="test-the-firewall"></a>testa brandväggen.

Nu kan du testa IDP: er, TLS-inspektion, webb filtrering och webb kategorier.

### <a name="add-firewall-diagnostics-settings"></a>Lägg till inställningar för brand Väggs diagnostik

Om du vill samla in brand Väggs loggar måste du lägga till diagnostikinställningar för att samla in brand Väggs loggar.

1. Välj **DemoFirewall** och under **övervakning** väljer du **diagnostikinställningar**.
2. Välj **Lägg till diagnostikinställning**.
3. För **diagnostiskt inställnings namn** skriver du *VB-diag*.
4. Under **logg** väljer du **AzureFirewallApplicationRule** och **AzureFirewallNetworkRule**.
5. Under **mål information** väljer **du skicka till Log Analytics arbets yta**.
6. Välj **Spara**.

### <a name="idps-tests"></a>IDP: er-tester

Om du vill testa IDP: er måste du distribuera din egen interna webb server med ett lämpligt Server certifikat. Mer information om kraven för för hands versionen av Azure Firewall Premium finns i avsnittet om för [hands versions certifikat för Azure Firewall Premium](premium-certificates.md).

Du kan använda `curl` för att styra olika HTTP-huvuden och simulera skadlig trafik.

#### <a name="to-test-idps-for-http-traffic"></a>Så här testar du IDP: er för HTTP-trafik:

1. Öppna ett kommando tolks fönster för en administratör på den virtuella WorkerVM-datorn.
2. Skriv följande kommando i kommando tolken:

   `curl -A "BlackSun" <your web server address>`
3. Du ser svaret på din webb server.
4. Gå till brand Väggs nätverks regel loggar på Azure Portal för att hitta en avisering som liknar följande meddelande:

   :::image type="content" source="media/premium-deploy/alert-message.png" alt-text="Aviserings meddelande":::

   > [!NOTE]
   > Det kan ta lite tid innan data börjar visas i loggarna. Ange minst 20 minuter för att loggarna ska kunna börja visa data.
5. Lägg till en Signaturrad för signatur 2008983:

   1. Välj **DemoFirewallPolicy** och välj **IDP: er (för hands version)** under **Inställningar** .
   1. Välj fliken **signaturinställningar** .
   1. Under **signatur-ID**, i text rutan som är öppen skriver du *2008983*.
   1. Under **läge** väljer du **neka**.
   1. Välj **Spara**.
   1. Vänta tills distributionen har slutförts innan du fortsätter.



6. Kör kommandot igen på WorkerVM `curl` :

   `curl -A "BlackSun" <your web server address>`

   Eftersom HTTP-begäran nu blockeras av brand väggen visas följande utdata när tids gränsen för anslutningen upphör att gälla:

   `read tcp 10.0.100.5:55734->10.0.20.10:80: read: connection reset by peer`

7. Gå till övervaknings loggarna i Azure Portal och hitta meddelandet för den blockerade begäran.
<!---8. Now you can bypass the IDPS function using the **Bypass list**.

   1. On the **IDPS (preview)** page, select the **Bypass list** tab.
   2. Edit **MyRule** and set **Destination** to *10.0.20.10, which is the ServerVM private IP address.
   3. Select **Save**.
1. Run the test again: `curl -A "BlackSun" http://server.2020-private-preview.com` and now you should get the `Hello World` response and no log alert. --->

#### <a name="to-test-idps-for-https-traffic"></a>Så här testar du IDP: er för HTTPS-trafik

Upprepa dessa sväng test med HTTPS i stället för HTTP. Exempel:

`curl --ssl-no-revoke -A "BlackSun" <your web server address>`

Du bör se samma resultat som du använde för HTTP-testerna.

### <a name="tls-inspection-with-url-filtering"></a>TLS-kontroll med URL-filtrering

Använd följande steg för att testa TLS-granskning med URL-filtrering.

1. Redigera brand Väggs principens program regler och Lägg till en ny regel `AllowURL` som kallas för `AllowWeb` regel samlingen. Konfigurera mål-URL `www.nytimes.com/section/world` : en, källans IP-adress **\* *_, mål typ _* URL (för hands version)**, Välj **TLS-granskning (för hands version)** och protokoll **http, https**.

3. När distributionen är klar öppnar du en webbläsare på WorkerVM och går till `https://www.nytimes.com/section/world` och kontrollerar att HTML-svaret visas som förväntat i webbläsaren.
4. I Azure Portal kan du Visa hela URL: en i övervaknings loggarna för program regeln:

      :::image type="content" source="media/premium-deploy/alert-message-url.png" alt-text="Aviserings meddelande som visar URL":::

Vissa HTML-sidor kan se ofullständiga ut eftersom de refererar till andra URL: er som nekas. För att lösa det här problemet kan du utföra följande metod:

- Om HTML-sidan innehåller länkar till andra domäner kan du lägga till dessa domäner i en ny program regel med Tillåt åtkomst till dessa FQDN: er.
- Om HTML-sidan innehåller länkar till under-URL: er kan du ändra regeln och lägga till en asterisk till URL: en. Exempelvis: `targetURLs=www.nytimes.com/section/world*`

   Du kan också lägga till en ny URL i regeln. Exempel: 

   `www.nytimes.com/section/world, www.nytimes.com/section/world/*`

### <a name="web-categories-testing"></a>Test av webb kategorier

Nu ska vi skapa en program regel för att tillåta åtkomst till idrotts webbplatser.
1. Från portalen öppnar du din resurs grupp och väljer **DemoFirewallPolicy**.
2. Välj **program regler** och Lägg sedan **till en regel samling**.
3. För **namn** skriver du *GeneralWeb*, **Priority** *103*, **regel samlings grupp** väljer **DefaultApplicationRuleCollectionGroup**.
4. Under **regler** för **namn** typ *AllowSports*, **källa** *\** , **protokoll** *http, https*, Välj **TLS-inspektion**, **måltyp** väljer du *webb kategorier (förhands granskning)*, **mål** Välj *sport*.
5. Välj **Lägg till**.

      :::image type="content" source="media/premium-deploy/web-categories.png" alt-text="Webb kategori för sport":::
6. När distributionen är klar går du till  **WorkerVM** och öppnar en webbläsare och bläddrar till `https://www.nfl.com` .

   Du bör se NFL-webbsidan och program regel loggen visar att en **webb kategori: idrotts** regel matchades och att begäran tilläts.

## <a name="next-steps"></a>Nästa steg

- [För hands versionen av Azure Firewall Premium i Azure Portal](premium-portal.md)