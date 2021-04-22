---
title: Använd Azure Private Link för att ansluta nätverk till Azure Monitor på ett säkert sätt
description: Använd Azure Private Link för att ansluta nätverk till Azure Monitor på ett säkert sätt
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: b3bbd8510c47831c9568bb10c62e2a5ca751902a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863037"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Använd Azure Private Link för att ansluta nätverk till Azure Monitor på ett säkert sätt

[Azure Private Link](../../private-link/private-link-overview.md) kan du länka Azure PaaS-tjänster på ett säkert sätt till ditt virtuella nätverk med hjälp av privata slutpunkter. För många tjänster har du bara ställt in en slutpunkt per resurs. Men Azure Monitor av olika sammankopplade tjänster som fungerar tillsammans för att övervaka dina arbetsbelastningar. Därför har vi skapat en resurs som kallas AMPLS (Azure Monitor Private Link Scope). Med AMPLS kan du definiera gränserna för ditt övervakningsnätverk och ansluta till det virtuella nätverket. Den här artikeln beskriver när du ska använda och hur du ställer in Azure Monitor Private Link omfång.

## <a name="advantages"></a>Fördelar

Med Private Link kan du:

- Ansluta privat till Azure Monitor utan att öppna någon offentlig nätverksåtkomst
- Se till att dina övervakningsdata endast nås via auktoriserade privata nätverk
- Förhindra data exfiltrering från dina privata nätverk genom att definiera specifika Azure Monitor som ansluter via din privata slutpunkt
- Anslut ditt privata lokala nätverk på ett säkert sätt till Azure Monitor expressroute och Private Link
- Behåll all trafik i Microsoft Azure stamnätverk

Mer information finns i  [Viktiga fördelar med att Private Link](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Så här fungerar det

Azure Monitor Private Link Scope (AMPLS) ansluter privata slutpunkter (och de virtuella nätverk som de finns i) till en eller flera Azure Monitor-resurser – Log Analytics-arbetsytor och Application Insights komponenter.

![Diagram över grundläggande resurstopologi](./media/private-link-security/private-link-basic-topology.png)

* Den privata slutpunkten i ditt VNet gör att den kan nå Azure Monitor-slutpunkter via privata IP-adresser från nätverkets pool, i stället för att använda till de offentliga IP-adresser för dessa slutpunkter. Det gör att du kan fortsätta använda Azure Monitor resurser utan att öppna ditt VNet för att obevaka utgående trafik. 
* Trafik från den privata slutpunkten till Azure Monitor resurser går över Microsoft Azure stamnät och dirigeras inte till offentliga nätverk. 
* Du kan konfigurera var och en av dina arbetsytor eller komponenter för att tillåta eller neka inmatning och frågor från offentliga nätverk. Det ger ett skydd på resursnivå så att du kan styra trafiken till specifika resurser.

> [!NOTE]
> En enda Azure Monitor resurs kan tillhöra flera AMPLS,men du kan inte ansluta ett enda VNet till mer än en AMPLS. 

## <a name="planning-your-private-link-setup"></a>Planera konfigurationen Private Link konfiguration

Innan du inställningar för Azure Monitor Private Link bör du tänka på nätverkstopologin och särskilt din DNS-routningstopologi. 

### <a name="the-issue-of-dns-overrides"></a>Problemet med DNS-åsidosättningar
Vissa Azure Monitor tjänster använder globala slutpunkter, vilket innebär att de betjänar begäranden som riktar sig mot en arbetsyta/komponent. Några exempel är slutpunkten Application Insights datainmatning och frågeslutpunkten för både Application Insights och Log Analytics.

När du ställer in en Private Link-anslutning uppdateras din DNS för att mappa Azure Monitor till privata IP-adresser från det virtuella nätverkets IP-intervall. Den här ändringen åsidosätter eventuell tidigare mappning av dessa slutpunkter, vilket kan ha meningsfulla konsekvenser, vilket beskrivs nedan. 

### <a name="azure-monitor-private-link-applies-to-all-azure-monitor-resources---its-all-or-nothing"></a>Azure Monitor Private Link gäller för alla Azure Monitor resurser – allt eller inget
Eftersom vissa Azure Monitor slutpunkter är globala är det omöjligt att skapa en Private Link anslutning för en specifik komponent eller arbetsyta. När du i stället ställer in en Private Link till en enda Application Insights-komponent eller Log Analytics-arbetsyta uppdateras DINA DNS-poster för **alla** Application Insights komponenter. Alla försök att mata in eller fråga en komponent går igenom Private Link och misslyckas eventuellt. När det gäller Log Analytics är inmatnings- och konfigurationsslutpunkter arbetsytespecifika, vilket innebär att konfigurationen för privat länk endast gäller för de angivna arbetsytorna. Inmatning och konfiguration av andra arbetsytor dirigeras till de offentliga Log Analytics-standardslutpunkterna.

![Diagram över DNS-åsidosättningar i ett enda VNet](./media/private-link-security/dns-overrides-single-vnet.png)

Detta gäller inte bara för ett specifikt VNet, utan för alla virtuella nätverk som delar samma DNS-server (se Problemet med [DNS-åsidosättningar).](#the-issue-of-dns-overrides) Begäran om att mata in loggar till en Application Insights komponent skickas därför alltid via den Private Link vägen. Komponenter som inte är länkade till AMPLS misslyckas med den Private Link verifieringen och går inte igenom.

> [!NOTE]
> För att avsluta: När du har Private Link anslutning till en enskild resurs gäller den för Azure Monitor i nätverket. För Application Insights resurser är det "Allt eller Inget". Det innebär i praktiken att du bör lägga Application Insights alla resurser i nätverket till din AMPLS eller ingen av dem.
> 
> För att hantera data exfiltreringsrisker rekommenderar vi att du lägger till alla Application Insights- och Log Analytics-resurser i ampls-nätverket och blockerar utgående nätverkstrafik så mycket som möjligt.

### <a name="azure-monitor-private-link-applies-to-your-entire-network"></a>Azure Monitor Private Link gäller för hela nätverket
Vissa nätverk består av flera virtuella nätverk. Om de virtuella nätverken använder samma DNS-server åsidosätter de varandras DNS-mappningar och kan eventuellt bryta varandras kommunikation med Azure Monitor (se Problemet med [DNS-åsidosättningar).](#the-issue-of-dns-overrides) Slutligen kommer endast det sista virtuella nätverket att kunna kommunicera med Azure Monitor, eftersom DNS mappar Azure Monitor-slutpunkter till privata IP-adresser från det här VNet-intervallet (som kanske inte kan nås från andra virtuella nätverk).

![Diagram över DNS-åsidosättningar i flera virtuella nätverk](./media/private-link-security/dns-overrides-multiple-vnets.png)

I diagrammet ovan ansluter VNet 10.0.1.x först till AMPLS1 och mappar de Azure Monitor globala slutpunkterna till IP-adresser från dess intervall. Senare ansluter VNet 10.0.2.x till AMPLS2 och åsidosätter DNS-mappningen för samma globala *slutpunkter* med IP-adresser från dess intervall. Eftersom dessa virtuella nätverk inte är peer-peer-peerade kan det första virtuella nätverket nu inte nå dessa slutpunkter.

> [!NOTE]
> För att avsluta: AMPLS-konfigurationen påverkar alla nätverk som delar samma DNS-zoner. För att undvika att åsidosätta varandras DNS-slutpunktsmappningar är det bäst att konfigurera en enskild privat slutpunkt i ett peer-baserat nätverk (till exempel ett hubb-VNet) eller separera nätverken på DNS-nivå (exempel på detta med dns-vidarebefordrare eller separata DNS-servrar helt).

### <a name="hub-spoke-networks"></a>Hub-spoke-nätverk
Hub-spoke-topologier kan undvika problem med DNS-åsidosättningar genom att ange en Private Link i det virtuella hubbnätverket (huvudnätverk), i stället för att konfigurera en Private Link för varje VNet separat. Den här konfigurationen är användbar, särskilt om Azure Monitor som används av de virtuella ekernätverken delas. 

![Hub-and-spoke-single-PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> Du kanske avsiktligt föredrar att skapa separata privata länkar för dina ekernätverk, till exempel för att ge varje VNet åtkomst till en begränsad uppsättning övervakningsresurser. I sådana fall kan du skapa en dedikerad privat slutpunkt och AMPLS för varje VNet, men du måste också kontrollera att de inte delar samma DNS-zoner för att undvika DNS-åsidosättningar.


### <a name="consider-limits"></a>Överväg begränsningar

Som du ser [i Begränsningar och](#restrictions-and-limitations)begränsningar har AMPLS-objektet ett antal gränser, som visas i topologin nedan:
* Varje VNet ansluter endast till **1** AMPLS-objekt.
* AMPLS B är anslutet till privata slutpunkter för två virtuella nätverk (VNet2 och VNet3), med hjälp av 2 av de 10 möjliga privata slutpunktsanslutningarna.
* AMPLS A ansluter till två arbetsytor och en Application Insight-komponent, med hjälp av 3 av de 50 Azure Monitor resursanslutningarna.
* Workspace2 ansluter till AMPLS A och AMPLS B med hjälp av 2 av de 5 möjliga AMPLS-anslutningarna.

![Diagram över AMPLS-gränser](./media/private-link-security/ampls-limits.png)


## <a name="example-connection"></a>Exempelanslutning

Börja med att skapa en Azure Monitor Private Link Omfångsresurs.

1. Gå till **Skapa en resurs** i Azure Portal och sök efter Azure Monitor Private Link **Omfång**.

   ![Hitta Azure Monitor Private Link omfång](./media/private-link-security/ampls-find-1c.png)

2. Välj **Skapa**.
3. Välj en prenumeration och resursgrupp.
4. Ge AMPLS ett namn. Det är bäst att använda ett beskrivande och tydligt namn, till exempel "AppServerProdTelem".
5. Välj **Granska + skapa**. 

   ![Skapa Azure Monitor Private Link omfång](./media/private-link-security/ampls-create-1d.png)

6. Låt verifieringen gå igenom och välj sedan **Skapa**.

### <a name="connect-azure-monitor-resources"></a>Ansluta Azure Monitor resurser

Anslut Azure Monitor (Log Analytics-arbetsytor och Application Insights komponenter) till din AMPLS.

1. I Azure Monitor Private Link väljer du **Azure Monitor resurser** på den vänstra menyn. Välj knappen **Lägg till**.
2. Lägg till arbetsytan eller komponenten. Om du **väljer** knappen Lägg till öppnas en dialogruta där du kan välja Azure Monitor resurser. Du kan bläddra igenom dina prenumerationer och resursgrupper, eller så kan du skriva in deras namn för att filtrera ned till dem. Välj arbetsytan eller komponenten och välj Tillämpa **för att** lägga till dem i ditt omfång.

    ![Skärmbild av att välja ett omfångs-UX](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> När Azure Monitor resurser tas bort måste du först koppla bort dem från alla AMPLS-objekt som de är anslutna till. Det går inte att ta bort resurser som är anslutna till en AMPLS.

### <a name="connect-to-a-private-endpoint"></a>Ansluta till en privat slutpunkt

Nu när du har resurser som är anslutna till din AMPLS skapar du en privat slutpunkt för att ansluta nätverket. Du kan utföra den här uppgiften [i Azure Portal Private Link eller](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)i ditt Azure Monitor Private Link, som du gjorde i det här exemplet.

1. I omfångsresursen **väljer du Privata slutpunktsanslutningar** i den vänstra resursmenyn. Välj **Privat slutpunkt för** att starta processen för att skapa slutpunkten. Du kan också godkänna anslutningar som startades i Private Link här genom att välja dem och välja **Godkänn.**

    ![Skärmbild av UX för privata slutpunktsanslutningar](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Välj prenumerationen, resursgruppen och namnet på slutpunkten och den region som den ska finnas i. Regionen måste vara samma region som det virtuella nätverk som du ansluter den till.

3. Välj **Nästa: Resurs**. 

4. På skärmen Resurs

   a. Välj den **prenumeration** som innehåller din Azure Monitor privat omfångsresurs. 

   b. För **resurstyp** väljer du **Microsoft.insights/privateLinkScopes**. 

   c. I **listrutan** resurs väljer du det omfång Private Link som du skapade tidigare. 

   d. Välj **Nästa: Konfiguration >**.
      ![Skärmbild av att välja Skapa privat slutpunkt](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. I konfigurationsfönstret

   a.    Välj det **virtuella nätverk** **och undernät** som du vill ansluta till Azure Monitor resurser. 
 
   b.    Välj **Ja** för **Integrera med privat DNS-zon** och låt den automatiskt skapa en ny Privat DNS zon. De faktiska DNS-zonerna kan vara annorlunda än vad som visas i skärmbilden nedan. 
   > [!NOTE]
   > Om du väljer **Nej** och föredrar att hantera DNS-poster manuellt måste du först konfigurera Private Link , inklusive den här privata slutpunkten och AMPLS-konfigurationen. Konfigurera sedan DNS enligt anvisningarna i [Konfigurera DNS för den privata Azure-slutpunkten](../../private-link/private-endpoint-dns.md). Se till att du inte skapar tomma poster när du förbereder konfigurationen av den privata länken. De DNS-poster som du skapar kan åsidosätta befintliga inställningar och påverka din anslutning till Azure Monitor.
 
   c.    Välj **Granska + skapa**.
 
   d.    Låt valideringen klara. 
 
   e.    Välj **Skapa**. 

    ![Skärmbild av information om att välja privat slutpunkt.](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

Nu har du skapat en ny privat slutpunkt som är ansluten till denna AMPLS.

## <a name="review-and-validate-your-private-link-setup"></a>Granska och verifiera Private Link konfiguration

### <a name="reviewing-your-endpoints-dns-settings"></a>Granska slutpunktens DNS-inställningar
Den privata slutpunkt som du skapade bör nu ha fyra KONFIGURERAde DNS-zoner:

[![Skärmbild av DNS-zoner för privata slutpunkter.](./media/private-link-security/private-endpoint-dns-zones.png)](./media/private-link-security/private-endpoint-dns-zones-expanded.png#lightbox)

* privatelink-monitor-azure-com
* privatelink-oms-opinsights-azure-com
* privatelink-ods-opinsights-azure-com
* privatelink-agentsvc-azure-automation-net

> [!NOTE]
> Var och en av dessa zoner mappar Azure Monitor slutpunkter till privata IP-adresser från det virtuella nätverkets IP-pool. IP-adresserna som visas i bilderna nedan är bara exempel. Konfigurationen bör i stället visa privata IP-adresser från ditt eget nätverk.

#### <a name="privatelink-monitor-azure-com"></a>Privatelink-monitor-azure-com
Den här zonen omfattar de globala slutpunkter som används av Azure Monitor, vilket innebär att dessa slutpunkter betjänar begäranden med hänsyn till alla resurser, inte en specifik. Den här zonen ska ha slutpunkter mappade för:
* `in.ai` – Application Insights inmatningsslutpunkt (både en global och en regional post)
* `api` – Application Insights- och Log Analytics API-slutpunkt
* `live` – Application Insights slutpunkt för livemått
* `profiler` – Application Insights profileringsslutpunkt
* `snapshot`– Application Insights slutpunkten för [ ![ ögonblicksbilder Skärmbild Privat DNS i zonen monitor-azure-com.](./media/private-link-security/dns-zone-privatelink-monitor-azure-com.png)](./media/private-link-security/dns-zone-privatelink-monitor-azure-com-expanded.png#lightbox)

#### <a name="privatelink-oms-opinsights-azure-com"></a>privatelink-oms-opinsights-azure-com
Den här zonen omfattar arbetsytespecifik mappning till OMS-slutpunkter. Du bör se en post för varje arbetsyta som är länkad till den AMPLS som är ansluten till den här privata slutpunkten.
[![Skärmbild av Privat DNS oms-opinsights-azure-com i zonen .](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-ods-opinsights-azure-com"></a>privatelink-ods-opinsights-azure-com
Den här zonen omfattar arbetsytespecifik mappning till ODS-slutpunkter – inmatningsslutpunkten för Log Analytics. Du bör se en post för varje arbetsyta som är länkad till den AMPLS som är ansluten till den här privata slutpunkten.
[![Skärmbild av Privat DNS ods-opinsights-azure-com.](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-agentsvc-azure-automation-net"></a>privatelink-agentsvc-azure-automation-net
Den här zonen omfattar arbetsytespecifik mappning till agenttjänstens automatiseringsslutpunkter. Du bör se en post för varje arbetsyta som är länkad till den AMPLS som är ansluten till den här privata slutpunkten.
[![Skärmbild av Privat DNS zonagent svc-azure-automation-net.](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net.png)](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net-expanded.png#lightbox)

#### <a name="privatelink-blob-core-windows-net"></a>privatelink-blob-core-windows-net
Den här zonen konfigurerar anslutningen till de globala agenternas lösningspaketlagringskonto. Agenterna kan ladda ned nya eller uppdaterade lösningspaket (även kallade hanteringspaket). Endast en post krävs för att hantera Log Analytics-agenter, oavsett hur många arbetsytor som används.
[![Skärmbild av Privat DNS blob-core-windows-net i zonen.](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net.png)](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net-expanded.png#lightbox)
> [!NOTE]
> Den här posten läggs bara till i konfigurationer av privata länkar som skapats den 19 april 2021 eller senare.


### <a name="validating-you-are-communicating-over-a-private-link"></a>Verifiera att du kommunicerar via en Private Link
* För att verifiera att dina begäranden nu skickas via den privata slutpunkten och de privata IP-mappade slutpunkterna kan du granska dem med ett verktyg för nätverksspårning eller till och med din webbläsare. När du till exempel försöker fråga din arbetsyta eller ditt program kontrollerar du att begäran skickas till den privata IP-adress som är mappad till API-slutpunkten. I det här exemplet är den *172.17.0.9.*

    Obs! Vissa webbläsare kan använda andra DNS-inställningar (se [Webbläsarens DNS-inställningar).](#browser-dns-settings) Kontrollera att DNS-inställningarna gäller.

* För att se till att din arbetsyta eller komponent inte tar emot begäranden från offentliga nätverk (inte anslutna via AMPLS), anger du resursens offentliga inmatning och frågeflaggor till *Nej* enligt förklaringen i Hantera åtkomst utanför omfånget för privata [länkar.](#manage-access-from-outside-of-private-links-scopes)

* Från en klient i det skyddade nätverket använder du till `nslookup` någon av de slutpunkter som anges i dina DNS-zoner. Dns-servern bör matcha den med mappade privata IP-adresser i stället för de offentliga IP-adresser som används som standard.


## <a name="configure-log-analytics"></a>Konfigurera Log Analytics

Gå till Azure-portalen. På resursmenyn för Log Analytics-arbetsytan finns det ett objekt som **heter Nätverksisolering** till vänster. Du kan styra två olika tillstånd från den här menyn.

![LA-nätverksisolering](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Omfång Azure Monitor Private Link anslutna enheter
Alla omfång som är anslutna till arbetsytan visas på den här skärmen. Genom att ansluta till omfång (AMPLS) kan nätverkstrafik från det virtuella nätverket som är anslutet till varje AMPLS nå den här arbetsytan. Att skapa en anslutning här har samma effekt som att konfigurera den för omfånget, som vi gjorde i [Ansluta Azure Monitor resurser](#connect-azure-monitor-resources). Om du vill lägga till en ny **anslutning väljer du** Lägg till och Azure Monitor Private Link Omfång. Välj **Tillämpa för** att ansluta den. Observera att en arbetsyta kan ansluta till 5 AMPLS-objekt, som anges i [Begränsningar och begränsningar](#restrictions-and-limitations). 

### <a name="manage-access-from-outside-of-private-links-scopes"></a>Hantera åtkomst utanför omfång för privata länkar
Inställningarna längst ned på den här sidan styr åtkomsten från offentliga nätverk, vilket innebär att nätverk inte är anslutna via angivna omfång (AMPLS). Ange **Tillåt offentlig nätverksåtkomst för inmatning till** **Inga** blockerar inmatning av loggar från datorer utanför de anslutna omfången. Ange **Tillåt offentlig nätverksåtkomst för frågor till** **Nej** blockerar frågor som kommer från datorer utanför omfången. Det omfattar frågor som körs via arbetsböcker, instrumentpaneler, API-baserade klientupplevelser, insikter i Azure Portal med mera. Upplevelser som körs utanför Azure Portal och som frågar Log Analytics-data måste också köras i det privata länkade virtuella nätverket.

### <a name="exceptions"></a>Undantag
Begränsning av åtkomst enligt beskrivningen ovan gäller inte för Azure Resource Manager och har därför följande begränsningar:
* Åtkomst till data – även om blockering/tillåtning av frågor från offentliga nätverk gäller för de flesta Log Analytics-upplevelser, kan vissa använda datafrågor via Azure Resource Manager och kan därför inte fråga efter data om inte Private Link-inställningarna tillämpas även på Resource Manager (funktionen kommer snart). Exempel är Azure Monitor, Arbetsböcker och Insikter och LogicApp-anslutningsappen.
* Arbetsytehantering – Inställningar för arbetsytor och konfigurationsändringar (inklusive att aktivera eller inaktivera dessa åtkomstinställningar) hanteras av Azure Resource Manager. Begränsa åtkomsten till arbetsytehantering med lämpliga roller, behörigheter, nätverkskontroller och granskning. Mer information finns i [Azure Monitor roller, behörigheter och säkerhet](../roles-permissions-security.md).

> [!NOTE]
> Loggar och mått som överförs till en arbetsyta via [diagnostikinställningar](../essentials/diagnostic-settings.md) går via en säker privat Microsoft-kanal och styrs inte av de här inställningarna.

### <a name="log-analytics-solution-packs-download"></a>Ladda ned Log Analytics-lösningspaket
Log Analytics-agenter behöver åtkomst till ett globalt lagringskonto för att kunna ladda ned lösningspaket. Private Link konfigurationer som skapats den 19 april 2021 eller senare kan nå agentens lösningspaketlagring via den privata länken. Detta är möjligt via den nya DNS-zonen som skapats för [blob.core.windows.net](#privatelink-blob-core-windows-net).

Om din Private Link har skapats före den 19 april 2021 kommer den inte att nå lösningspaketens lagring via en privat länk. Du kan hantera detta genom att göra något av följande:
* Skapa om din AMPLS och den privata slutpunkt som är anslutna till den
* Tillåt att dina agenter når lagringskontot via dess offentliga slutpunkt genom att lägga till följande regler i brandväggens lista över tillåtna brandväggar:

    | Molnmiljö | Agentresurs | Portar | Riktning |
    |:--|:--|:--|:--|
    |Azure, offentlig     | scadvisorcontent.blob.core.windows.net         | 443 | Utgående
    |Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  Utgående
    |Azure Kina 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | Utgående


## <a name="configure-application-insights"></a>Konfigurera Application Insights

Gå till Azure-portalen. I Azure Monitor Application Insights-komponentresursen är ett menyalternativ **Nätverksisolering** till vänster. Du kan styra två olika tillstånd från den här menyn.

![AI-nätverksisolering](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

Först kan du ansluta den här Application Insights till Azure Monitor Private Link som du har åtkomst till. Välj **Lägg** till och välj **Azure Monitor Private Link Omfång.** Välj Tillämpa för att ansluta den. Alla anslutna omfång visas på den här skärmen. Genom att upprätta den här anslutningen kan nätverkstrafik i de anslutna virtuella nätverken nå den här komponenten och har samma effekt som att ansluta den från omfånget som vi gjorde i [Ansluta Azure Monitor resurser](#connect-azure-monitor-resources). 

Sedan kan du styra hur den här resursen kan nås utanför de AMPLS-omfång (Private Link Scope) som listades tidigare. Om du anger **Tillåt offentlig nätverksåtkomst** för inmatning till **Nej** kan datorer eller SDK:er utanför de anslutna omfången inte ladda upp data till den här komponenten. Om du anger **Tillåt offentlig nätverksåtkomst** för frågor till **Nej** kan datorer utanför omfången inte komma åt data i den här Application Insights resursen. Dessa data omfattar åtkomst till APM-loggar, mått och live-måttströmmen, samt upplevelser som bygger på arbetsböcker, instrumentpaneler, API-baserade frågeklientupplevelser, insikter i Azure Portal med mera. 

> [!NOTE]
> Förbrukningsupplevelser som inte finns i portalen måste också köras på det privata länkade virtuella nätverket som innehåller de övervakade arbetsbelastningarna.

Du måste lägga till resurser som är värdar för de övervakade arbetsbelastningarna i den privata länken. Se till exempel Använda [privata slutpunkter för Azure Web App](../../app-service/networking/private-endpoint.md).

Begränsning av åtkomst på det här sättet gäller endast för data i Application Insights resurs. Konfigurationsändringar, inklusive att aktivera eller inaktivera dessa åtkomstinställningar, hanteras dock av Azure Resource Manager. Därför bör du begränsa åtkomsten till Resource Manager med lämpliga roller, behörigheter, nätverkskontroller och granskning. Mer information finns i [Azure Monitor, behörigheter och säkerhet.](../roles-permissions-security.md)

> [!NOTE]
> För att skydda arbetsytebaserade Application Insights måste du låsa både åtkomsten till Application Insights och den underliggande Log Analytics-arbetsytan.
>
> Med diagnostik på kodnivå (profilerare/felsökare) måste du ange [ett eget lagringskonto för att](../app/profiler-bring-your-own-storage.md) stödja private link.

### <a name="handling-the-all-or-nothing-nature-of-private-links"></a>Hantering av allt-eller-inget-typ av privata länkar
Som förklaras i [Planera konfigurationen Private Link](#planning-your-private-link-setup)påverkar konfiguration av en Private Link även för en enskild resurs alla Azure Monitor-resurser i dessa nätverk och i andra nätverk som delar samma DNS. Det här beteendet kan göra onboarding-processen utmanande. Överväg följande alternativ:

* Allt i – den enklaste och säkraste metoden är att lägga till alla Application Insights komponenter i AMPLS. För komponenter som du även vill ha åtkomst till från andra nätverk lämnar du flaggorna "Tillåt offentlig Internetåtkomst för inmatning/fråga" inställt på Ja (standard).
* Isolera nätverk – om du är (eller kan anpassa dig till) med hjälp av virtuella ekernätverk följer du riktlinjerna i [Nätverkstopologi av hub-spoke-eker i Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). Konfigurera sedan separata privata länkinställningar i relevanta virtuella ekernätverk. Se även till att separera DNS-zoner eftersom DNS-åsidosättningar kommer att åsidosättas om du delar DNS-zoner [med andra ekernätverk.](#the-issue-of-dns-overrides)
* Använd anpassade DNS-zoner för specifika appar – med den här lösningen kan du komma åt utvalda Application Insights-komponenter över en Private Link, samtidigt som all annan trafik finns kvar på de offentliga vägarna.
    - Konfigurera en anpassad [privat DNS-zon](../../private-link/private-endpoint-dns.md)och ge den ett unikt namn, till exempel internal.monitor.azure.com
    - Skapa en AMPLS och en privat slutpunkt och välj **att inte** integrera automatiskt med privat DNS
    - Gå till Privat slutpunkt – > DNS-konfiguration och granska den föreslagna mappningen av FQDN.
    - Välj lägg till konfiguration och välj den internal.monitor.azure.com zon som du nyss skapade
    - Lägg till poster för ovanstående ![ skärmbild av konfigurerad DNS-zon](./media/private-link-security/private-endpoint-global-dns-zone.png)
    - Gå till Application Insights och kopiera dess [anslutningssträng](../app/sdk-connection-string.md).
    - Appar eller skript som vill anropa den här komponenten via en Private Link bör använda anslutningssträngen med EndpointSuffix=internal.monitor.azure.com
* Mappa slutpunkter via värdfiler i stället för DNS – för att ha en Private Link åtkomst endast från en specifik dator/virtuell dator i nätverket:
    - Konfigurera en AMPLS och en privat slutpunkt och välj **att inte** integrera automatiskt med privat DNS 
    - Konfigurera ovanstående A-poster på en dator som kör appen i värdfilen


## <a name="use-apis-and-command-line"></a>Använda API:er och kommandorad

Du kan automatisera processen som beskrivs tidigare Azure Resource Manager med hjälp av mallar, REST och kommandoradsgränssnitt.

Om du vill skapa och hantera privata länkomfång [använder du REST API](/rest/api/monitor/privatelinkscopes(preview)/private%20link%20scoped%20resources%20(preview)) eller Azure CLI [(az monitor private-link-scope).](/cli/azure/monitor/private-link-scope)

Om du vill hantera nätverksåtkomst använder du flaggor `[--ingestion-access {Disabled, Enabled}]` `[--query-access {Disabled, Enabled}]` och på Log [Analytics-arbetsytor](/cli/azure/monitor/log-analytics/workspace) [eller Application Insights komponenter](/cli/azure/monitor/app-insights/component).

## <a name="collect-custom-logs-and-iis-log-over-private-link"></a>Samla in anpassade loggar och IIS-Private Link

Lagringskonton används i inmatningsprocessen för anpassade loggar. Som standard används tjänst-hanterade lagringskonton. Men om du vill mata in anpassade loggar på privata länkar måste du använda dina egna lagringskonton och associera dem med Log Analytics-arbetsytor. Se mer information om hur du ställer in sådana konton med [hjälp av kommandoraden](/cli/azure/monitor/log-analytics/workspace/linked-storage).

Mer information om hur du tar med ditt eget lagringskonto [finns i Kundägda lagringskonton för logginmatning](private-storage.md)

## <a name="restrictions-and-limitations"></a>Villkor och begränsningar

### <a name="ampls"></a>AMPLS
AMPLS-objektet har ett antal begränsningar som du bör tänka på när du planerar Private Link installation:

* Ett VNet kan bara ansluta till ett AMPLS-objekt. Det innebär att AMPLS-objektet måste ge åtkomst till alla Azure Monitor resurser som det virtuella nätverket ska ha åtkomst till.
* En Azure Monitor (arbetsyta eller Application Insights komponent) kan ansluta till högst 5 AMPL:er.
* Ett AMPLS-objekt kan ansluta till högst 50 Azure Monitor resurser.
* Ett AMPLS-objekt kan som mest ansluta till 10 privata slutpunkter.

Se [Överväg gränser](#consider-limits) för en djupare granskning av dessa gränser.

### <a name="agents"></a>Agenter

De senaste versionerna av Windows- och Linux-agenterna måste användas för säker inmatning till Log Analytics-arbetsytor. Äldre versioner kan inte ladda upp övervakningsdata över ett privat nätverk.

**Windows-agenten för Log Analytics**

Använd Log Analytics-agenten version 10.20.18038.0 eller senare.

**Log Analytics-agent för Linux**

Använd agentversion 1.12.25 eller senare. Om det inte går kör du följande kommandon på den virtuella datorn.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Azure Portal

För att Azure Monitor-portalupplevelser som Application Insights och Log Analytics måste du tillåta att Azure Portal- och Azure Monitor-tilläggen är tillgängliga i de privata nätverken. Lägg **till AzureActiveDirectory,** **AzureResourceManager,** **AzureFrontDoor.FirstParty** och **AzureFrontdoor.Frontend-tjänsttaggar** [](../../firewall/service-tags.md) i nätverkssäkerhetsgruppen.

### <a name="querying-data"></a>Köra frågor mot data
[ `externaldata` Operatorn](/azure/data-explorer/kusto/query/externaldata-operator?pivots=azuremonitor) stöds inte via en Private Link eftersom den läser data från lagringskonton men garanterar inte att lagringen nås privat.

### <a name="programmatic-access"></a>Programmässig åtkomst

Om du vill använda REST API, [CLI](/cli/azure/monitor) eller PowerShell med Azure Monitor på [](../../virtual-network/service-tags-overview.md)privata nätverk lägger du till tjänsttaggarna **AzureActiveDirectory** **och AzureResourceManager** i brandväggen.  

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Application Insights SDK-nedladdningar från ett nätverk för innehållsleverans

Paketera JavaScript-koden i skriptet så att webbläsaren inte försöker ladda ned kod från ett CDN. Ett exempel finns på [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)

### <a name="browser-dns-settings"></a>DNS-inställningar för webbläsare

Om du ansluter till dina Azure Monitor via en Private Link måste trafiken till dessa resurser gå via den privata slutpunkt som är konfigurerad i nätverket. Om du vill aktivera den privata slutpunkten uppdaterar du DNS-inställningarna enligt förklaringen [i Anslut till en privat slutpunkt.](#connect-to-a-private-endpoint) Vissa webbläsare använder sina egna DNS-inställningar i stället för de som du anger. Webbläsaren kan försöka ansluta till Azure Monitor offentliga slutpunkter och kringgå Private Link helt. Kontrollera att webbläsarinställningarna inte åsidosätter eller cachelagrar gamla DNS-inställningar. 

## <a name="next-steps"></a>Nästa steg

- Läs mer om [privat lagring](private-storage.md)
