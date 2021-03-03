---
title: Använd Azure Private Link för att ansluta nätverk till Azure Monitor på ett säkert sätt
description: Använd Azure Private Link för att ansluta nätverk till Azure Monitor på ett säkert sätt
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.subservice: ''
ms.openlocfilehash: e9431aac203b831a0ffe22b835acf4677061780c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101707853"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Använd Azure Private Link för att ansluta nätverk till Azure Monitor på ett säkert sätt

Med [Azures privata länk](../../private-link/private-link-overview.md) kan du på ett säkert sätt länka Azure PaaS-tjänster till ditt virtuella nätverk med hjälp av privata slut punkter. För många tjänster skapar du bara en slut punkt per resurs. Azure Monitor är dock en Constellation av olika sammankopplade tjänster som arbetar tillsammans för att övervaka dina arbets belastningar. Därför har vi skapat en resurs som kallas en Azure Monitor Private Link-omfånget (AMPLS). Med AMPLS kan du definiera gränserna för ditt övervaknings nätverk och ansluta till ditt virtuella nätverk. Den här artikeln beskriver när du ska använda och hur du konfigurerar ett Azure Monitor privat länk omfång.

## <a name="advantages"></a>Fördelar

Med privat länk kan du:

- Anslut privat till Azure Monitor utan att öppna någon offentlig nätverks åtkomst
- Se till att dina övervaknings data endast nås via auktoriserade privata nätverk
- Förhindra data exfiltrering från dina privata nätverk genom att definiera vissa Azure Monitor resurser som ansluter via din privata slut punkt
- Anslut ett privat lokalt nätverk till Azure Monitor med ExpressRoute och privat länk
- Behåll all trafik i Microsoft Azure stamnät nätverket

Mer information finns i  [viktiga fördelar med privat länk](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Så här fungerar det

Azure Monitor Private Link omfånget (AMPLS) ansluter privata slut punkter (och de virtuella nätverk de finns i) till en eller flera Azure Monitor resurser-Log Analytics arbets ytor och Application Insights komponenter.

![Diagram över grundläggande resurs sto pol Ogin](./media/private-link-security/private-link-basic-topology.png)

* Den privata slut punkten på ditt VNet gör att den når Azure Monitor slut punkter via privata IP-adresser från nätverkets pool, i stället för att använda till offentliga IP-adresser för dessa slut punkter. Det gör att du kan fortsätta att använda dina Azure Monitor resurser utan att öppna ditt VNet för att kräva utgående trafik. 
* Trafik från den privata slut punkten till Azure Monitor resurser går över Microsoft Azure stamnätet och dirigeras inte till offentliga nätverk. 
* Du kan konfigurera var och en av dina arbets ytor eller komponenter för att tillåta eller neka inmatning och frågor från offentliga nätverk. Det ger ett skydd på resurs nivå, så att du kan styra trafiken till vissa resurser.

> [!NOTE]
> En enskild Azure Monitor resurs kan tillhöra flera AMPLSs, men du kan inte ansluta ett enskilt VNet till fler än en AMPLS. 

## <a name="planning-your-private-link-setup"></a>Planera konfigurationen av den privata länken

Innan du konfigurerar installationen av Azure Monitor privat länk bör du tänka på nätverk sto pol Ogin och särskilt din DNS-routning. 

### <a name="the-issue-of-dns-overrides"></a>Frågan om DNS-åsidosättningar
Vissa Azure Monitor tjänster använder globala slut punkter, vilket innebär att de hanterar begär Anden som riktar sig mot alla arbets ytor/komponenter. Ett par exempel är den Application Insights inmatnings slut punkten och slut punkten för både Application Insights och Log Analytics.

När du konfigurerar en anslutning till en privat länk uppdateras din DNS för att mappa Azure Monitor slut punkter till privata IP-adresser från ditt VNet IP-intervall. Den här ändringen åsidosätter alla tidigare mappningar av dessa slut punkter, vilket kan ha meningsfulla följder, som granskas nedan. 

### <a name="azure-monitor-private-link-applies-to-all-azure-monitor-resources---its-all-or-nothing"></a>Azure Monitor privat länk gäller för alla Azure Monitor resurser – allt det är eller inget
Eftersom vissa Azure Monitor slut punkter är globala är det omöjligt att skapa en privat länk anslutning för en viss komponent eller arbets yta. I stället uppdateras dina DNS-poster för **alla** Application Insights-komponenter när du konfigurerar en privat länk till en enda Application Insights-komponent. Försök att mata in eller fråga en komponent går via den privata länken och kan eventuellt Miss lyckas. Om du ställer in en privat länk till en enskild arbets yta så kommer alla Log Analyticss frågor att gå igenom slut punkten för den privata länken (men inte inmatnings begär Anden som har arbets ytans angivna slut punkter).

![Diagram över DNS-åsidosättningar i ett enda VNet](./media/private-link-security/dns-overrides-single-vnet.png)

Det stämmer inte bara för ett särskilt VNet, men för alla virtuella nätverk som delar samma DNS-server (se [frågan om DNS-åsidosättningar](#the-issue-of-dns-overrides)). Det innebär att till exempel begäran om att mata in loggar till en Application Insights komponent alltid skickas via den privata länk vägen. Komponenter som inte är länkade till AMPLS kommer att Miss klar med verifieringen av den privata länken och inte gå igenom.

> [!NOTE]
> Att ingå: när du har konfigurerat en privat länk anslutning till en enda resurs gäller den för alla Azure Monitor resurser i nätverket – allt det är eller inget. Det innebär att du bör lägga till alla Azure Monitor resurser i nätverket till din AMPLS, eller ingen av dem.

### <a name="azure-monitor-private-link-applies-to-your-entire-network"></a>Azure Monitor privat länk gäller hela nätverket
Vissa nätverk består av flera virtuella nätverk. Om virtuella nätverk använder samma DNS-Server åsidosätter de var and ras DNS-mappningar och kan eventuellt dela var and ras kommunikation med Azure Monitor (se [frågan om DNS-åsidosättningar](#the-issue-of-dns-overrides)). Slutligen kommer endast det sista virtuella nätverket kunna kommunicera med Azure Monitor, eftersom DNS kommer att mappa Azure Monitor slut punkter till privata IP-adresser från det här virtuella nätverk-intervallet (som kanske inte kan nås från andra virtuella nätverk).

![Diagram över DNS-åsidosättningar i flera virtuella nätverk](./media/private-link-security/dns-overrides-multiple-vnets.png)

I diagrammet ovan ansluter VNet 10.0.1. x först till AMPLS1 och mappar Azure Monitor globala slut punkter till IP-adresser från intervallet. Senare ansluter VNet 10.0.2. x till AMPLS2 och åsidosätter DNS-mappningen av *samma globala slut punkter* med IP-adresser från intervallet. Eftersom dessa virtuella nätverk inte är peer-kopplade, kan det första VNet nu inte komma åt dessa slut punkter.

> [!NOTE]
> Att ingå: AMPLS-installationen påverkar alla nätverk som delar samma DNS-zoner. För att undvika att åsidosätta var and ras DNS-slutpunkts mappningar, är det bäst att konfigurera en enskild privat slut punkt i ett peer-nätverk (till exempel ett hubb-VNet) eller separera nätverken på DNS-nivå (Foe exempel genom att använda DNS-vidarebefordrare eller separata DNS-servrar helt).

### <a name="hub-spoke-networks"></a>Nav-eker-nätverk
Topologier för NAV-ekrar kan undvika DNS-åsidosättningar genom att ange en privat länk i hubben (huvud-VNet) i stället för att skapa en privat länk för varje VNet separat. Den här inställningen är särskilt användbar om Azure Monitor resurser som används av eker-virtuella nätverk delas. 

![Hubb-och-eker-Single-PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> Du kan avsiktligt föredra att skapa separata privata Länkar för din eker-virtuella nätverk, till exempel för att tillåta varje VNet att få åtkomst till en begränsad uppsättning övervaknings resurser. I sådana fall kan du skapa en dedikerad privat slut punkt och AMPLS för varje VNet, men du måste också kontrol lera att de inte delar samma DNS-zoner för att undvika åsidosättning av DNS.


### <a name="consider-limits"></a>Överväg begränsningar

Som listas i [begränsningar och begränsningar](#restrictions-and-limitations)har AMPLS-objektet ett antal gränser, som visas i nedanstående topologi:
* Varje VNet ansluter till endast **ett** AMPLS-objekt.
* AMPLS B är anslutet till privata slut punkter för två virtuella nätverk (VNet2 och VNet3) med 2 av de 10 möjliga privata slut punkts anslutningarna.
* AMPLS A ansluter till två arbets ytor och en komponent för program insikter med tre av de 50 möjliga Azure Monitor resurs anslutningarna.
* Workspace2 ansluter till AMPLS A och AMPLS B med 2 av 5 möjliga AMPLS-anslutningar.

![Diagram över AMPLS-gränser](./media/private-link-security/ampls-limits.png)


## <a name="example-connection"></a>Exempel anslutning

Börja med att skapa en Azure Monitor privat länk omfångs resurs.

1. Gå till **skapa en resurs** i Azure Portal och sök efter **Azure Monitor privat länk omfång**.

   ![Sök omfång för Azure Monitor privat länk](./media/private-link-security/ampls-find-1c.png)

2. Välj **Skapa**.
3. Välj en prenumeration och en resurs grupp.
4. Ge AMPLS ett namn. Det är bäst att använda ett meningsfullt och tydligt namn, till exempel "AppServerProdTelem".
5. Välj **Granska + skapa**. 

   ![Skapa Azure Monitor privat länk omfång](./media/private-link-security/ampls-create-1d.png)

6. Låt validerings passet gå till och välj sedan **skapa**.

### <a name="connect-azure-monitor-resources"></a>Anslut Azure Monitor resurser

Anslut Azure Monitor resurser (Log Analytics arbets ytor och Application Insights-komponenter) till din AMPLS.

1. I sökområdet Azure Monitor privat länk väljer du **Azure Monitor resurser** i den vänstra menyn. Välj knappen **Lägg till**.
2. Lägg till arbets ytan eller komponenten. Om du väljer knappen **Lägg till** visas en dialog ruta där du kan välja Azure Monitor resurser. Du kan bläddra igenom dina prenumerationer och resurs grupper, eller så kan du skriva in namnet för att filtrera ned dem. Välj arbets ytan eller komponenten och välj **tillämpa** för att lägga till dem i ditt omfång.

    ![Skärm bild av Välj ett omfångs-UX](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> Om du tar bort Azure Monitor resurser måste du först koppla bort dem från alla AMPLS-objekt som de är anslutna till. Det går inte att ta bort resurser som är anslutna till en AMPLS.

### <a name="connect-to-a-private-endpoint"></a>Anslut till en privat slut punkt

Nu när du har resurser som är anslutna till din AMPLS skapar du en privat slut punkt för att ansluta vårt nätverk. Du kan göra det här för att utföra den här uppgiften i det [Azure Portal privata länk centret](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)eller inuti Azure Monitor privata länk omfång, som du gjort i det här exemplet.

1. I din omfattnings resurs väljer du **anslutningar för privata slut punkter** i den vänstra resurs menyn. Välj **privat slut punkt** för att starta processen för att skapa slut punkten. Du kan också godkänna anslutningar som startades i det privata länk centret här genom att markera dem och välja **Godkänn**.

    ![Skärm bild av UX för privata slut punkts anslutningar](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Välj prenumeration, resurs grupp och namn för slut punkten och regionen som den ska leva i. Regionen måste vara samma region som det VNet som du ansluter det till.

3. Välj **Nästa: resurs**. 

4. På resurs skärmen

   a. Välj den **prenumeration** som innehåller din Azure Monitor privata scope-resurs. 

   b. För **resurs typ** väljer du **Microsoft. Insights/privateLinkScopes**. 

   c. I list rutan **resurs** väljer du det definitions område för privata länkar som du skapade tidigare. 

   d. Välj **Nästa: konfigurations >**.
      ![Skärm bild av Välj Skapa privat slut punkt](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. I konfigurations fönstret,

   a.    Välj det **virtuella nätverk** och **undernät** som du vill ansluta till dina Azure Monitor-resurser. 
 
   b.    Välj **Ja** för **integrering med privat DNS-zon** och låt den automatiskt skapa en ny privat DNS zon. De faktiska DNS-zonerna kan skilja sig från vad som visas på skärm bilden nedan. 
   > [!NOTE]
   > Om du väljer **Nej** och hellre vill hantera DNS-poster manuellt, slutför först konfigurationen av din privata länk, inklusive den här privata slut punkten och AMPLS-konfigurationen. Konfigurera sedan DNS enligt anvisningarna i [Konfigurera DNS för den privata Azure-slutpunkten](../../private-link/private-endpoint-dns.md). Se till att du inte skapar tomma poster när du förbereder konfigurationen av den privata länken. De DNS-poster som du skapar kan åsidosätta befintliga inställningar och påverka din anslutning till Azure Monitor.
 
   c.    Välj **Granska + skapa**.
 
   d.    Tillåt validerings pass. 
 
   e.    Välj **Skapa**. 

    ![Skärm bild av Välj privat slut punkt information.](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

Nu har du skapat en ny privat slut punkt som är ansluten till den här AMPLS.

## <a name="review-and-validate-your-private-link-setup"></a>Granska och verifiera konfigurationen av en privat länk

### <a name="reviewing-your-endpoints-dns-settings"></a>Granska din slut punkts DNS-inställningar
Den privata slut punkten som du skapade ska nu ha fyra konfigurerade DNS-zoner:

[![Skärm bild av DNS-zoner för privata slut punkter.](./media/private-link-security/private-endpoint-dns-zones.png)](./media/private-link-security/private-endpoint-dns-zones-expanded.png#lightbox)

* privatelink-Monitor-Azure-com
* privatelink-OMS-OpInsights-Azure-com
* privatelink-ODS-OpInsights-Azure-com
* privatelink-agentsvc-Azure-Automation-net

Var och en av dessa zoner mappar vissa Azure Monitor slut punkter till privata IP-adresser från poolen med IP-adresser för den privata slut punktens VNet.

#### <a name="privatelink-monitor-azure-com"></a>Privatelink-Monitor-Azure-com
Den här zonen täcker de globala slut punkter som används av Azure Monitor, vilket innebär att dessa slut punkter betjänar begär Anden som överväger alla resurser, inte en annan. Den här zonen ska ha slut punkter mappade för:
* `in.ai` – (Application Insights slut punkt för inmatningar visas en global och en regional post
* `api` -Application Insights och Log Analytics API-slutpunkt
* `live` – Application Insights Live Metrics-slutpunkt
* `profiler` -Application Insights profiler-slutpunkt
* `snapshot`– Skärm bild av Application Insights ögonblicks bilder [ ![ av privat DNS Zone-övervakaren – Azure-com.](./media/private-link-security/dns-zone-privatelink-monitor-azure-com.png)](./media/private-link-security/dns-zone-privatelink-monitor-azure-com-expanded.png#lightbox)

#### <a name="privatelink-oms-opinsights-azure-com"></a>privatelink-OMS-OpInsights-Azure-com
Den här zonen täcker arbets ytans angivna mappningar till OMS-slutpunkter. Du bör se en post för varje arbets yta som är länkad till den AMPLS som är ansluten till den här privata slut punkten.
[![Skärm bild av Privat DNS Zone OMS-OpInsights – Azure-com.](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-ods-opinsights-azure-com"></a>privatelink-ODS-OpInsights-Azure-com
Den här zonen täcker arbets ytans angivna mappningar till ODS-slutpunkter – inmatnings slut punkten för Log Analytics. Du bör se en post för varje arbets yta som är länkad till den AMPLS som är ansluten till den här privata slut punkten.
[![Skärm bild av Privat DNS Zone ODS-OpInsights-Azure-com.](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-agentsvc-azure-automation-net"></a>privatelink-agentsvc-Azure-Automation-net
Den här zonen täcker arbets ytans angivna mappningar till Agent tjänstens Automation-slutpunkter. Du bör se en post för varje arbets yta som är länkad till den AMPLS som är ansluten till den här privata slut punkten.
[![Skärm bild av Privat DNS Zone agent SVC-Azure-Automation-net.](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net.png)](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net-expanded.png#lightbox)

### <a name="validating-you-are-communicating-over-a-private-link"></a>Verifierar att du kommunicerar via en privat länk
* För att verifiera att dina begär Anden skickas via den privata slut punkten och till de privata IP-kopplade slut punkterna kan du granska dem med en nätverks spårning till verktyg eller till och med din webbläsare. Om du till exempel försöker att fråga din arbets yta eller ditt program, se till att begäran skickas till den privata IP-adressen som är mappad till API-slutpunkten, i det här exemplet är den *172.17.0.9*.

    Obs! vissa webbläsare kan använda andra DNS-inställningar (se [webbläsarens DNS-inställningar](#browser-dns-settings)). Kontrol lera att dina DNS-inställningar gäller.

* För att se till att din arbets yta eller komponent inte tar emot begär Anden från offentliga nätverk (inte anslutna via AMPLS) anger du resursens offentliga inmatnings-och fråge flaggor till *Nej* som förklaras i [Hantera åtkomst från platser utanför privata länkar](#manage-access-from-outside-of-private-links-scopes).

* Från en klient i det skyddade nätverket använder `nslookup` du någon av slut punkterna som anges i dina DNS-zoner. Den bör lösas av DNS-servern till de mappade privata IP-adresserna i stället för de offentliga IP-adresser som används som standard.


## <a name="configure-log-analytics"></a>Konfigurera Log Analytics

Gå till Azure-portalen. I din Log Analytics arbets ytans resurs meny finns ett objekt som kallas **nätverks isolering** på den vänstra sidan. Du kan styra två olika tillstånd från den här menyn.

![LA nätverks isolering](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Anslutna Azure Monitor privata länk omfång
Alla omfattningar som är anslutna till arbets ytan visas på den här skärmen. Genom att ansluta till scope (AMPLSs) tillåts nätverks trafik från det virtuella nätverket som är anslutet till varje AMPLS för att uppnå den här arbets ytan. Att skapa en anslutning med här har samma resultat som när du ställer in det i omfånget, som vi gjorde vid [anslutning Azure Monitor resurser](#connect-azure-monitor-resources). Om du vill lägga till en ny anslutning väljer du **Lägg till** och väljer omfånget Azure Monitor privat länk. Välj **Använd** för att ansluta den. Observera att en arbets yta kan ansluta till 5 AMPLS-objekt, vilket anges i [begränsningar och begränsningar](#restrictions-and-limitations). 

### <a name="manage-access-from-outside-of-private-links-scopes"></a>Hantera åtkomst från utsidan av privata länk omfattningar
Inställningarna på den nedre delen av den här sidan styr åtkomsten från offentliga nätverk, vilket innebär att nätverk som inte är anslutna via de omfång som anges ovan. Inställningen **Tillåt offentligt nätverks åtkomst för** inmatning till **inga** block inmatningar av loggar från datorer utanför de anslutna omfattningarna. Inställningen **Tillåt offentlig nätverks åtkomst för frågor** till **inga** block frågor som kommer från datorer utanför omfånget. Detta inkluderar frågor som körs via arbets böcker, instrument paneler, API-baserade klient upplevelser, insikter i Azure Portal och mycket annat. Upplevelser som körs utanför Azure Portal och som frågar Log Analytics data måste också köras i det privata, länkade VNET.

### <a name="exceptions"></a>Undantag
Att begränsa åtkomsten enligt beskrivningen ovan gäller inte för Azure Resource Manager och har därför följande begränsningar:
* Åtkomst till data – samtidigt som du blockerar/tillåter frågor från offentliga nätverk gäller de flesta Log Analyticss upplevelser, men vissa upplevelser frågar data via Azure Resource Manager och kommer därför inte att kunna fråga efter data, om inte inställningarna för privata länkar tillämpas på Resource Manager också (funktionen kommer snart snart). Detta omfattar exempelvis Azure Monitor lösningar, arbets böcker och insikter och LogicApp-anslutningen.
* Arbets ytans hantering – inställning av arbets yta och konfigurations ändringar (inklusive aktivering av de här åtkomst inställningarna på eller av) hanteras av Azure Resource Manager. Begränsa åtkomsten till hantering av arbets ytor med lämpliga roller, behörigheter, nätverks kontroller och granskning. Mer information finns i [Azure Monitor roller, behörigheter och säkerhet](../roles-permissions-security.md).

> [!NOTE]
> Loggar och mått som överförs till en arbets yta via [diagnostikinställningar](../essentials/diagnostic-settings.md) går via en säker privat Microsoft-kanal och styrs inte av de här inställningarna.

### <a name="log-analytics-solution-packs-download"></a>Hämta Log Analytics lösnings paket

Om du vill tillåta att Log Analytics agent laddar ned lösnings paket lägger du till rätt fullständigt kvalificerade domän namn i listan över tillåtna domän namn för brand väggen. 


| Moln miljö | Agentresurs | Portar | Riktning |
|:--|:--|:--|:--|
|Azure, offentlig     | scadvisorcontent.blob.core.windows.net         | 443 | Utgående
|Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  Utgående
|Azure Kina 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | Utgående

## <a name="configure-application-insights"></a>Konfigurera Application Insights

Gå till Azure-portalen. I Azure Monitor Application Insights komponent resurs är ett meny alternativ för **nätverks isolering** på den vänstra sidan. Du kan styra två olika tillstånd från den här menyn.

![AI-nätverks isolering](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

Först kan du ansluta den här Application Insights resursen till Azure Monitor de privata länk omfattningar som du har åtkomst till. Välj **Lägg till** och välj **omfånget Azure Monitor privat länk**. Välj Använd för att ansluta den. Alla anslutna omfattningar visas på den här skärmen. Genom att göra den här anslutningen kan nätverks trafiken i de anslutna virtuella nätverken komma åt den här komponenten och har samma resultat som att ansluta den från omfånget som vi gjorde när vi [anslöt Azure Monitor-resurser](#connect-azure-monitor-resources). 

För det andra kan du styra hur den här resursen kan nås utanför AMPLS (Private Link scope) som visas ovan. Om du ställer in **Tillåt offentligt nätverks åtkomst för** inmatning till **Nej** kan inte datorer eller SDK: er utanför de anslutna omfattningarna Ladda upp data till den här komponenten. Om du ställer in **Tillåt offentlig nätverks åtkomst för frågor** till **Nej**, kan datorer utanför omfattningarna inte komma åt data i den här Application Insights resursen. Dessa data omfattar till gång till APM-loggar, Mät värden och Live Metrics-dataströmmen, samt erfarenheter som bygger på de flesta arbets böcker, instrument paneler, API-baserade klient upplevelser, insikter i Azure Portal och mycket mer. 

> [!NOTE]
> Användnings upplevelser som inte är portalen måste också köras på det privata, länkade VNET som innehåller de övervakade arbets belastningarna.

Du måste lägga till resurser som är värdar för de övervakade arbets belastningarna till den privata länken. Här är en [dokumentation](../../app-service/networking/private-endpoint.md) om hur du gör detta för app Services.

Att begränsa åtkomsten på det här sättet gäller endast för data i Application Insights-resursen. Konfigurations ändringar, inklusive aktivering av dessa åtkomst inställningar på eller av, hanteras dock av Azure Resource Manager. Därför bör du begränsa åtkomsten till Resource Manager med hjälp av lämpliga roller, behörigheter, nätverks kontroller och granskning. Mer information finns i [Azure Monitor roller, behörigheter och säkerhet](../roles-permissions-security.md).

> [!NOTE]
> För att helt skydda arbets ytans baserade Application Insights måste du låsa både åtkomst till Application Insights resurs och den underliggande Log Analytics arbets ytan.
>
> Kod på kod nivå (profilerings-/fel sökning) behöver du ange ditt eget lagrings konto för att stödja privat länk. Här är en [dokumentation](../app/profiler-bring-your-own-storage.md) om hur du gör detta.

### <a name="handling-the-all-or-nothing-nature-of-private-links"></a>Hantera privata länkars allt-eller-Nothing-natur
Som förklaras i [planera konfigurationen av den privata länken](#planning-your-private-link-setup), ställer in en privat länk även för en enskild resurs, påverkar alla Azure Monitor resurser i nätverken och i andra nätverk som delar samma DNS. Detta kan göra din onboarding-process utmanande. Överväg följande alternativ:

* Allt i den enklaste och säkraste metoden är att lägga till alla Application Insights-komponenter i AMPLS. För komponenter som du vill ha åtkomst till från andra nätverk måste du lämna flaggorna "Tillåt offentlig Internet åtkomst för inmatnings/fråga" inställda till Ja (standard).
* Isolera nätverk – om du är (eller kan justera med) med ekrar virtuella nätverk följer du rikt linjerna i nätverkstopologi [för NAV-ekrar i Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). Konfigurera sedan separata inställningar för privata länkar i relevanta eker-virtuella nätverk. Se även till att avgränsa DNS-zoner, eftersom om du delar DNS-zoner med andra eker-nätverk kommer [DNS att åsidosättas](#the-issue-of-dns-overrides).
* Använd anpassade DNS-zoner för vissa appar – med den här lösningen kan du komma åt Välj Application Insights-komponenter över en privat länk och samtidigt behålla all annan trafik över offentliga vägar.
    - Konfigurera en [anpassad privat DNS-zon](../../private-link/private-endpoint-dns.md)och ge den ett unikt namn, till exempel Internal.Monitor.Azure.com
    - Skapa en AMPLS och en privat slut punkt och välj att **inte** integrera med privat DNS automatiskt
    - Gå till privat slut punkt – > DNS-konfiguration och granska den föreslagna mappningen av fullständigt kvalificerade domän namn.
    - Välj att lägga till konfigurationen och välj den internal.monitor.azure.com-zon som du nyss skapade
    - Lägg till poster för ![ skärm bilden ovan för konfigurerad DNS-zon](./media/private-link-security/private-endpoint-global-dns-zone.png)
    - Gå till Application Insights-komponenten och kopiera dess [anslutnings sträng](../app/sdk-connection-string.md).
    - Appar eller skript som vill anropa den här komponenten via en privat länk bör använda anslutnings strängen med EndpointSuffix = Internal. Monitor. Azure. com
* Mappa slut punkter via Hosts-filer i stället för DNS – så att endast en privat länk får åtkomst från en speciell dator eller virtuell dator i nätverket:
    - Konfigurera en AMPLS och en privat slut punkt och välj att **inte** integrera automatiskt med privat DNS 
    - Konfigurera ovanstående poster på en dator som kör appen i värd filen


## <a name="use-apis-and-command-line"></a>Använda API: er och kommando rad

Du kan automatisera processen som beskrivs tidigare med hjälp av Azure Resource Manager mallar, REST och kommando rads gränssnitt.

Om du vill skapa och hantera privata länk omfattningar använder du [REST API](/rest/api/monitor/private%20link%20scopes%20(preview)) eller [Azure CLI (AZ Monitor Private-Link-scope)](/cli/azure/monitor/private-link-scope).

Om du vill hantera nätverks åtkomst använder du flaggorna `[--ingestion-access {Disabled, Enabled}]` och `[--query-access {Disabled, Enabled}]` på [Log Analytics arbets ytor](/cli/azure/monitor/log-analytics/workspace) eller [Application Insights komponenter](/cli/azure/ext/application-insights/monitor/app-insights/component).

## <a name="collect-custom-logs-and-iis-log-over-private-link"></a>Samla in anpassade loggar och IIS-logg över privat länk

Lagrings konton används i inmatnings processen för anpassade loggar. Som standard används tjänst hanterade lagrings konton. Men för att mata in anpassade loggar på privata länkar måste du använda dina egna lagrings konton och koppla dem till Log Analytics arbets ytor. Se mer information om hur du konfigurerar sådana konton med hjälp av [kommando raden](/cli/azure/monitor/log-analytics/workspace/linked-storage).

Mer information om hur du ansluter ditt eget lagrings konto finns i [kundägda lagrings konton för logg](private-storage.md) inmatning

## <a name="restrictions-and-limitations"></a>Villkor och begränsningar

### <a name="ampls"></a>AMPLS
AMPLS-objektet har ett antal gränser som du bör tänka på när du planerar konfigurationen av den privata länken:

* Ett VNet kan bara ansluta till ett AMPLS-objekt. Det innebär att AMPLS-objektet måste ge åtkomst till alla Azure Monitor-resurser som det virtuella nätverket ska ha åtkomst till.
* En Azure Monitor resurs (arbets yta eller Application Insights komponent) kan ansluta till högst 5 AMPLSs.
* Ett AMPLS-objekt kan bara ansluta till 50 Azure Monitor resurser.
* Ett AMPLS-objekt kan endast ansluta till 10 privata slut punkter.

Se [Överväg begränsningar](#consider-limits) för en djupare granskning av dessa gränser.

### <a name="agents"></a>Agenter

De senaste versionerna av Windows-och Linux-agenterna måste användas för att stödja säker inmatning till Log Analytics arbets ytor. Äldre versioner kan inte överföra övervaknings data över ett privat nätverk.

**Windows-agenten för Log Analytics**

Använd Log Analytics agent version 10.20.18038.0 eller senare.

**Log Analytics-agent för Linux**

Använd agent version 1.12.25 eller senare. Om du inte kan köra kör du följande kommandon på den virtuella datorn.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Azure Portal

Om du vill använda Azure Monitor Portal upplevelser som Application Insights och Log Analytics måste du tillåta att Azure Portal-och Azure Monitor-tillägg kan nås i privata nätverk. Lägg till **AzureActiveDirectory**, **AzureResourceManager**, **AzureFrontDoor. FirstParty** och **AzureFrontDoor. frontend** [service-Taggar](../../firewall/service-tags.md) i nätverks säkerhets gruppen.

### <a name="querying-data"></a>Köra frågor mot data
[ `externaldata` Operatorn](/azure/data-explorer/kusto/query/externaldata-operator?pivots=azuremonitor) stöds inte över en privat länk, eftersom den läser data från lagrings konton, men garanterar inte att lagringen nås privat.

### <a name="programmatic-access"></a>Programmässig åtkomst

Om du vill använda REST API, [CLI](/cli/azure/monitor) eller PowerShell med Azure Monitor i privata nätverk lägger du till [service tag-](../../virtual-network/service-tags-overview.md)  **AzureActiveDirectory** och **AzureResourceManager** i brand väggen.

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Application Insights SDK-nedladdningar från ett Content Delivery Network

Paketera JavaScript-koden i skriptet så att webbläsaren inte försöker hämta kod från ett CDN. Ett exempel finns i [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)

### <a name="browser-dns-settings"></a>Webbläsarens DNS-inställningar

Om du ansluter till Azure Monitor resurser via en privat länk måste trafiken till dessa resurser gå igenom den privata slut punkt som är konfigurerad i nätverket. Om du vill aktivera den privata slut punkten uppdaterar du dina DNS-inställningar enligt beskrivningen i [Anslut till en privat slut punkt](#connect-to-a-private-endpoint). Vissa webbläsare använder sina egna DNS-inställningar i stället för de som du anger. Webbläsaren kan försöka ansluta till Azure Monitor offentliga slut punkter och kringgå den privata länken helt och hållet. Kontrol lera att inställningarna för webbläsaren inte åsidosätter eller cachelagrar gamla DNS-inställningar. 

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [privat lagring](private-storage.md)