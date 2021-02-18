---
title: Azure Traffic Analytics | Microsoft Docs
description: Lär dig hur du analyserar flödes loggar i Azure Network Security Group med trafik analys.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.reviewer: vinigam
ms.custom: references_regions
ms.openlocfilehash: 42536480a72e8d2160064a82eee7bac11c17746c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585513"
---
# <a name="traffic-analytics"></a>Trafikanalys

Trafikanalys är en molnbaserad lösning som ger insyn i användar-och program aktivitet i moln nätverk. Trafik analys analyserar Network Watcher flödes loggar för nätverks säkerhets gruppen (NSG) för att ge insikter i trafikflödet i Azure-molnet. Med trafik analys kan du:

- Visualisera nätverks aktivitet i dina Azure-prenumerationer och identifiera aktiva punkter.
- Identifiera säkerhetshot till och skydda nätverket, med information som öppna portar, program som försöker ansluta till Internet och virtuella datorer (VM) som ansluter till falska nätverk.
- Förstå trafik flödes mönster i Azure-regioner och på Internet för att optimera nätverks distributionen för prestanda och kapacitet.
- Hitta felaktiga nätverks konfigurationer som leder till misslyckade anslutningar i nätverket.

> [!NOTE]
> Trafikanalys stöder nu insamling av data flödes loggar med en högre frekvens på 10 minuter

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="why-traffic-analytics"></a>Varför trafik analys?

Det är viktigt att övervaka, hantera och känna till ditt eget nätverk för kompromisslös säkerhet, efterlevnad och prestanda. Att känna till din egen miljö är av största vikt för att skydda och optimera den. Du behöver ofta känna till det aktuella läget för nätverket, som ansluter, var de ansluter från, vilka portar som är öppna för Internet, förväntade nätverks beteende, oregelbunden nätverks beteende och plötsliga ökningar i trafiken.

Moln nätverk skiljer sig från lokala företags nätverk, där du har Netflow eller motsvarande protokoll som stöder routrar och växlar, vilket ger möjlighet att samla in IP-nätverkstrafik när den går in eller avslutar ett nätverks gränssnitt. Genom att analysera trafikflödes data kan du bygga en analys av flöde och volym för nätverks trafik.

Virtuella Azure-nätverk har NSG flödes loggar, som ger dig information om inkommande och utgående IP-trafik via en nätverks säkerhets grupp som är kopplad till enskilda nätverks gränssnitt, virtuella datorer eller undernät. Genom att analysera rå NSG flödes loggar och infoga information om säkerhet, topologi och geografi kan trafik analys ge dig insikter i trafikflödet i din miljö. Trafikanalys innehåller information som de flesta kommunicerande värdar, de flesta kommunicerande program protokoll, de flesta konversation-värd par, tillåten/blockerad trafik, inkommande/utgående trafik, öppna Internet portar, de flesta spärrnings regler, trafik distribution per Azure-datacenter, virtuellt nätverk, undernät eller falska nätverk.

## <a name="key-components"></a>Nyckelkomponenter

- **Nätverks säkerhets grupp (NSG)**: innehåller en lista över säkerhets regler som tillåter eller nekar nätverks trafik till resurser som är anslutna till en Azure-Virtual Network. Nätverkssäkerhetsgrupper kan kopplas till undernät, enskilda virtuella datorer (klassisk) eller enskilda nätverkskort (NIC) som är anslutna till virtuella datorer (Resource Manager). Mer information finns i [Översikt över nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Flödes loggar för nätverks säkerhets grupper (NSG)**: gör att du kan visa information om inkommande och utgående IP-trafik via en nätverks säkerhets grupp. NSG flödes loggar skrivs i JSON-format och visar utgående och inkommande flöden per regel, vilket nätverkskort flödet gäller för, fem tuple-information om flödet (käll-och mål-IP-adress, käll-och mål Port och protokoll) och om trafiken tillåts eller nekas. Mer information om NSG Flow-loggar finns i [NSG Flow-loggar](network-watcher-nsg-flow-logging-overview.md).
- **Log Analytics**: en Azure-tjänst som samlar in övervaknings data och lagrar data i en central lagrings plats. Dessa data kan omfatta händelser, prestanda data eller anpassade data som tillhandahålls via Azure-API: et. När data har samlats in är de tillgängliga för avisering, analys och export. Övervakning av program, till exempel övervakaren för nätverks prestanda och trafik analys skapas med Azure Monitor loggar som grund. Mer information finns i [Azure Monitor loggar](../azure-monitor/logs/log-query-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Log Analytics arbets yta**: en instans av Azure Monitor loggar där data som hör till ett Azure-konto lagras. Mer information om Log Analytics-arbetsytor finns i [skapa en Log Analytics arbets yta](../azure-monitor/logs/quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Network Watcher**: en regional tjänst som gör att du kan övervaka och diagnostisera villkor på en nätverks scenario nivå i Azure. Du kan aktivera och inaktivera NSG flödes loggar med Network Watcher. Mer information finns i [Network Watcher](network-watcher-monitoring-overview.md).

## <a name="how-traffic-analytics-works"></a>Så här fungerar trafik analys

Trafik analys undersöker rå NSG flödes loggar och avbildar färre loggar genom att aggregera vanliga flöden mellan samma käll-IP-adress, mål-IP-adress, målport och protokoll. Till exempel, värd 1 (IP-adress: 10.10.10.10) som kommunicerar med värd 2 (IP-adress: 10.10.20.10), 100 gånger under en period på 1 timme med port (t. ex. 80) och protokoll (till exempel http). Den reducerade loggen har en post, som är värddator 1 & värd 2, med 100 gånger under en period på 1 timme med port *80* och protokoll http, i stället för att ha 100 *-* poster. Lägre loggar har förbättrats med information om geografi, säkerhet och topologi och lagras sedan i en Log Analytics-arbetsyta. Följande bild visar data flödet:

![Data flöde för bearbetning av NSG flödes loggar](./media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions-nsg"></a>Regioner som stöds: NSG 

Du kan använda Traffic Analytics för NSG: er i någon av följande regioner:
:::row:::
   :::column span="":::
      Australien, centrala  
      Australien, östra  
      Australien, sydöstra  
      Brasilien, södra  
      Kanada, centrala  
      Kanada, östra  
      Indien, centrala  
      Central US  
      Kina, östra 2  
      Kina, norra 2  
   :::column-end:::
   :::column span="":::
      Asien, östra  
      East US  
      USA, östra 2  
      USA, östra 2 EUAP  
      Frankrike, centrala  
      Japan, östra  
      Japan, västra  
      Sydkorea, centrala  
      Sydkorea, södra  
      USA, norra centrala  
   :::column-end:::
   :::column span="":::
      Europa, norra  
      Sydafrika, norra  
      USA, södra centrala  
      Indien, södra  
      Sydostasien  
      Schweiz, norra  
      Schweiz, västra  
      Storbritannien, södra  
      Storbritannien, västra  
      USGov Arizona  
   :::column-end:::
   :::column span="":::
      USGov Texas  
      USGov Virginia  
      USNat, öst  
      USNat väst  
      USSec, öst  
      USSec väst  
      USA, västra centrala  
      Europa, västra  
      USA, västra  
      USA, västra 2  
   :::column-end:::
:::row-end:::

## <a name="supported-regions-log-analytics-workspaces"></a>Regioner som stöds: Log Analytics arbets ytor

Arbets ytan Log Analytics måste finnas i följande regioner:
:::row:::
   :::column span="":::
      Australien, centrala  
      Australien, östra  
      Australien, sydöstra  
      Brasilien, södra  
      Kanada, centrala  
      Indien, centrala  
      Central US  
      Kina, östra 2  
      Asien, östra  
      East US  
   :::column-end:::
   :::column span="":::
      USA, östra 2  
      USA, östra 2 EUAP  
      Frankrike, centrala  
      Tyskland, västra centrala  
      Japan, östra  
      Sydkorea, centrala  
      USA, norra centrala  
      Europa, norra  
      Sydafrika, norra  
      USA, södra centrala  
   :::column-end:::
   :::column span="":::
      Sydostasien  
      Schweiz, norra  
      Schweiz, västra  
      Förenade Arabemiraten Central  
      Storbritannien, södra  
      Storbritannien, västra  
      USGov Arizona  
      USGov Virginia  
      USNat, öst  
      USNat väst  
   :::column-end:::
   :::column span="":::
      USSec, öst  
      USSec väst  
      USA, västra centrala  
      Europa, västra  
      USA, västra  
      USA, västra 2  
   :::column-end:::
:::row-end:::

## <a name="prerequisites"></a>Förutsättningar

### <a name="user-access-requirements"></a>Krav för användar åtkomst

Ditt konto måste vara medlem i någon av följande [inbyggda Azure-roller](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json):

|Distributionsmodell   | Roll                   |
|---------          |---------               |
|Resource Manager   | Ägare                  |
|                   | Deltagare            |
|                   | Läsare                 |
|                   | Nätverksdeltagare    |

Om ditt konto inte har tilldelats någon av de inbyggda rollerna måste det tilldelas en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) som har tilldelats följande åtgärder på prenumerations nivå:

- "Microsoft. Network/applicationGateways/Read"
- "Microsoft. Network/Connections/Read"
- "Microsoft. Network/belastningsutjämnare/Read"
- "Microsoft. Network/localNetworkGateways/Read"
- "Microsoft. Network/networkInterfaces/Read"
- "Microsoft. Network/networkSecurityGroups/Read"
- "Microsoft. Network/publicIPAddresses/Read"
- "Microsoft. Network/routeTables/Read"
- "Microsoft. Network/virtualNetworkGateways/Read"
- "Microsoft. Network/virtualNetworks/Read"
- "Microsoft. Network/expressRouteCircuits/Read"

Information om hur du kontrollerar användar behörighet finns i [vanliga frågor och svar om trafik analys](traffic-analytics-faq.md).

### <a name="enable-network-watcher"></a>Aktivera Network Watcher

För att analysera trafiken måste du ha en befintlig nätverks övervakare eller [Aktivera en nätverks övervakare](network-watcher-create.md) i varje region som du har NSG: er som du vill analysera trafiken för. Trafik analys kan aktive ras för NSG: er som finns i någon av de [regioner som stöds](#supported-regions-nsg).

### <a name="select-a-network-security-group"></a>Välj en nätverks säkerhets grupp

Innan du aktiverar NSG Flow-loggning måste du ha en nätverks säkerhets grupp för att logga flöden för. Om du inte har någon nätverks säkerhets grupp, se [skapa en nätverks säkerhets grupp](../virtual-network/manage-network-security-group.md#create-a-network-security-group) för att skapa en.

I Azure Portal går du till **Network Watcher** och väljer sedan **NSG Flow-loggar**. Välj den nätverks säkerhets grupp som du vill aktivera en NSG flödes logg för, som du ser i följande bild:

![Val av NSG: er som kräver aktivering av NSG Flow-loggen](./media/traffic-analytics/selection-of-nsgs-that-require-enablement-of-nsg-flow-logging.png)

Om du försöker aktivera trafik analys för en NSG som finns i någon annan region än de regioner som [stöds](#supported-regions-nsg)visas fel meddelandet "Det gick inte att hitta".

## <a name="enable-flow-log-settings"></a>Aktivera flödes logg inställningar

Innan du aktiverar flödes logg inställningar måste du utföra följande uppgifter:

Registrera Azure Insights-providern om den inte redan har registrerats för din prenumeration:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

Om du inte redan har ett Azure Storage konto för att lagra NSG Flow-loggar i måste du skapa ett lagrings konto. Du kan skapa ett lagrings konto med kommandot som följer. Innan du kör kommandot ersätter du `<replace-with-your-unique-storage-account-name>` med ett namn som är unikt för alla Azure-platser, mellan 3-24 tecken, med enbart siffror och gemener. Du kan också ändra resurs gruppens namn, om det behövs.

```azurepowershell-interactive
New-AzStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Välj följande alternativ, som du ser på bilden:

1. Välj *på* för **status**
2. Välj *version 2* för **flödes loggar version**. Version 2 innehåller statistik för flödes-session (byte och paket)
3. Välj ett befintligt lagrings konto för att lagra flödes loggarna i. Kontrol lera att lagrings utrymmet för Data Lake Storage Gen2 hierarkiskt namn område är inställt på sant.
4. Ange **kvarhållning** till det antal dagar som du vill lagra data för. Ange värdet till *0* om du vill lagra data permanent. Du debiteras Azure Storage avgifter för lagrings kontot. 
5. Välj *för* **trafikanalys status**.
6. Välj bearbetnings intervall. Baserat på ditt val samlas flödes loggar in från lagrings kontot och bearbetas av Trafikanalys. Du kan välja bearbetnings intervall för varannan timme eller var 10: e minut. 
7. Välj en befintlig Log Analytics (OMS)-arbets yta eller Välj **Skapa ny arbets yta** för att skapa en ny. En Log Analytics arbets yta används av Trafikanalys för att lagra aggregerade och indexerade data som sedan används för att generera analysen. Om du väljer en befintlig arbets yta måste den finnas i någon av de [regioner som stöds](#supported-regions-log-analytics-workspaces) och ha uppgraderats till det nya frågespråket. Om du inte vill uppgradera en befintlig arbets yta eller om du inte har en arbets yta i en region som stöds skapar du en ny. Mer information om frågespråk finns i [Azure Log Analytics uppgradera till ny loggs ökning](../azure-monitor/logs/log-query-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

> [!NOTE]
>Log Analytics-arbetsytan som är värd för Traffic Analytics-lösningen och NSG: er behöver inte vara i samma region. Du kan till exempel ha trafik analys i en arbets yta i regionen Europa, västra, medan du kan ha NSG: er i USA och västra USA. Flera NSG: er kan konfigureras i samma arbets yta.

8. Välj **Spara**.

    ![Val av lagrings konto, Log Analytics arbets yta och Trafikanalys aktivering](./media/traffic-analytics/ta-customprocessinginterval.png)

Upprepa föregående steg för alla andra NSG: er som du vill aktivera trafik analys för. Data från flödes loggar skickas till arbets ytan, så se till att lokala lagar och föreskrifter i ditt land/region tillåter data lagring i den region där arbets ytan finns. Om du har angett olika bearbetnings intervall för olika NSG: er kommer data att samlas in i olika intervall. Exempel: du kan välja att aktivera bearbetnings intervallet på 10 minuter för kritiska virtuella nätverk och 1 timme för icke-kritiska virtuella nätverk.

Du kan också konfigurera trafik analys med hjälp av [set-AzNetworkWatcherConfigFlowLog](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog) PowerShell-cmdleten i Azure PowerShell. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul).

## <a name="view-traffic-analytics"></a>Visa trafik analys

Om du vill visa Trafikanalys söker du efter **Network Watcher** i portalens Sök fält. När du är i Network Watcher kan du utforska trafik analyser och dess funktioner genom att välja **trafikanalys** på den vänstra menyn. 

![Komma åt Trafikanalys-instrumentpanelen](./media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

Det kan ta upp till 30 minuter innan instrument panelen visas första gången eftersom Trafikanalys måste samla in tillräckligt med data för att få meningsfulla insikter innan de kan generera rapporter.

## <a name="usage-scenarios"></a>Användningsscenarier

Några av de insikter du kanske vill få när Trafikanalys har kon figurer ATS fullständigt är följande:

### <a name="find-traffic-hotspots"></a>Hitta trafik-hotspots

**Titta efter**

- Vilka värdar, undernät och virtuella nätverk skickar eller tar emot den högsta trafiken, passerar maximal skadlig trafik och blockerar betydande flöden?
    - Kontrol lera jämför ande diagram för värd, undernät och virtuellt nätverk. Att förstå vilka värdar, undernät och virtuella nätverk som skickar eller tar emot den mesta trafiken kan hjälpa dig att identifiera de värdar som bearbetar den högsta trafiken och huruvida trafik distributionen har utförts på rätt sätt.
    - Du kan utvärdera om trafik volymen är lämplig för en värd. Är volymen av normal trafik eller har den en ytterligare undersökning?
- Hur mycket inkommande/utgående trafik finns det?
    -   Förväntas värden att ta emot mer inkommande trafik än utgående eller vice versa?
- Statistik över blockerad trafik.
    - Varför blockerar en värd en betydande volym av ofarlig trafik? Detta beteende kräver ytterligare undersökning och förmodligen optimering av konfigurationen
- Statistik över skadlig trafik som tillåts/blockeras
  - Varför är en värd som tar emot skadlig trafik och varför är flöden från skadliga källor tillåtna? Det här beteendet kräver ytterligare undersökning och förmodligen optimering av konfigurationen.

    Välj **Visa alla**, under **värd**, som du ser i följande bild:

    ![Instrument panel som demonstrerar värd med mest trafik information](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- Följande bild visar tids trender för de fem främsta samtals värdarna och de flödes relaterade detaljer (tillåtna – inkommande/utgående och nekade inkommande/utgående flöden) för en värd:

    ![Främsta fem mest pratade värd trender](media/traffic-analytics/top-five-most-talking-host-trend.png)

**Titta efter**

- Vilka är de flesta konversation-värd par?
    - Förväntat beteende, t. ex. klient-eller backend-kommunikation eller oregelbundet beteende, t. ex. Internet trafik på Server sidan.
- Statistik över tillåten/blockerad trafik
    - Varför en värd tillåter eller blockerar betydande trafik volym
- Program protokoll som används oftast bland de flesta konversation-värd par:
    - Är dessa program tillåtna i det här nätverket?
    - Är programmen korrekt konfigurerade? Använder de rätt protokoll för kommunikation? Välj **Visa alla** under **vanliga konversationer**, som visas i följande bild:

        ![Instrument panel som demonstrerar de vanligaste konversationerna](./media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- Följande bild visar tids trender för de fem främsta konversationerna och de flödes information som tillåts och nekade inkommande och utgående flöden för ett konversations par:

    ![Information och trend om de fem vanligaste samtalen](./media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**Titta efter**

- Vilket program protokoll används mest i din miljö och vilka konversation-värdnamn använder det program protokoll som är mest?
    - Är dessa program tillåtna i det här nätverket?
    - Är programmen korrekt konfigurerade? Använder de rätt protokoll för kommunikation? Förväntat beteende är vanliga portar som 80 och 443. Om en ovanlig port visas för standard kommunikation kan det krävas en konfigurations ändring. Välj **Visa alla** under **program port** i följande bild:

        ![Instrument panel för demonstration av de främsta program protokollen](./media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- Följande bilder visar tids trender för de fem främsta L7-protokollen och flödes relaterade detaljer (till exempel tillåtna och nekade flöden) för ett L7-protokoll:

    ![5 främsta protokoll information och trend](./media/traffic-analytics/top-five-layer-seven-protocols-details-and-trend.png)

    ![Flödes information för program protokoll i loggs ökning](./media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**Titta efter**

- Kapacitets användnings trender för en VPN-gateway i din miljö.
    - Varje VPN-SKU tillåter en viss mängd bandbredd. Är VPN-gatewayerna underutnyttjade?
    - Når dina gateways kapacitet? Bör du uppgradera till nästa högre SKU?
- Vilka är de flesta konversation-värdar, via vilka VPN-gateway, vars port?
    - Är det här mönstret normal? Välj **Se alla** under **VPN-gateway**, som du ser i följande bild:

        ![Instrument panel med främsta aktiva VPN-anslutningar](./media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- Följande bild visar tids trenden för kapacitets användning av en Azure-VPN Gateway och information om flöde (till exempel tillåtna flöden och portar):

    ![Tendens och flödes information om VPN gateway-användning](./media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>Visualisera trafik distribution efter geografi

**Titta efter**

- Trafik distribution per Data Center, t. ex. främsta trafik källor till ett Data Center, främsta konversation nätverk med data Center och de konversation program protokollen.
  - Om du ser mer belastning i ett Data Center kan du planera för effektiv trafik distribution.
  - Om falska nätverk är konversation i data centret ska du korrigera NSG-reglerna så att de blockeras.

    Välj **Visa karta** under **din miljö**, som du ser i följande bild:

    ![Instrument panels demonstration av trafik distribution](./media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- Geo-kartan visar det övre menyfliksområdet för val av parametrar, t. ex. Data Center (distribuerat/ingen-distribution/aktiv/inaktiv/Trafikanalys aktive rad/Trafikanalys inte aktive rad) och länder/regioner som bidrar till att den aktiva distributionen är oskadlig/skadlig trafik:

    ![Visning av den geografiska kart vyn som demonstrerar aktiv distribution](./media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- Geo-kartan visar trafik distributionen till ett Data Center från länder/regioner och kontinenter som kommunicerar med den i blått (oskadlig trafik) och röda (skadlig trafik) färgade linjer:

    ![Geo Map-vy som demonstrerar trafik distribution till länder/regioner och kontinenter](./media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![Flödes information för trafik distribution i loggs ökning](./media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>Visualisera trafik distribution av virtuella nätverk

**Titta efter**

- Trafik distribution per virtuellt nätverk, topologi, högsta trafik trafik till det virtuella nätverket, de mest falska nätverken konversation till det virtuella nätverket och de främsta konversation-programprotokollen.
  - Att veta vilket virtuellt nätverk som är konversation till det virtuella nätverket. Om konversationen inte förväntas kan du åtgärda det.
  - Om falska nätverk är konversation med ett virtuellt nätverk kan du korrigera NSG-reglerna för att blockera de falska nätverken.
 
    Välj **Visa virtuella nätverk** under **din miljö**, som du ser i följande bild:

    ![Instrument panel som demonstrerar distribution av virtuella nätverk](./media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- Virtual Network sto pol Ogin visar det övre menyfliksområdet för val av parametrar som ett virtuellt nätverk (mellan virtuella nätverks anslutningar/aktiva/inaktiva), externa anslutningar, aktiva flöden och skadliga flöden i det virtuella nätverket.
- Du kan filtrera Virtual Network sto pol Ogin utifrån prenumerationer, arbets ytor, resurs grupper och tidsintervall. Ytterligare filter som hjälper dig att förstå flödet är: flödes typ (anslutningar, IntraVNET och så vidare), flödes riktning (inkommande, utgående), flödes status (tillåts, blockerad), virtuella nätverk (riktad och ansluten), Anslutnings typ (peering eller gateway-P2S och S2S) och NSG. Använd dessa filter för att fokusera på virtuella nätverk som du vill undersöka i detalj.
- Virtual Network sto pol Ogin visar trafik distributionen till ett virtuellt nätverk med avseende på flöden (tillåtna/blockerade/inkommande/utgående/ofarlig/skadlig/skadlig), program protokoll och nätverks säkerhets grupper, till exempel:

    ![Topologi för virtuella nätverk som demonstrerar trafik distribution och flödes information](./media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)
    
    ![Topologi för virtuellt nätverk som visar högsta nivå och fler filter](./media/traffic-analytics/virtual-network-filters.png)

    ![Flödes information för distribution av virtuella nätverks trafik i loggs ökning](./media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**Titta efter**

- Trafik distribution per undernät, topologi, högsta trafik källa till under nätet, de mest falska nätverken konversation till under nätet och de konversation applikations protokollen.
    - Veta vilket undernät som är konversation för vilket undernät. Om du ser oväntade konversationer kan du korrigera konfigurationen.
    - Om falska nätverk är konversation med ett undernät kan du korrigera det genom att konfigurera NSG-regler för att blockera de falska nätverken.
- Topologin för undernät visar det översta menyfliksområdet för val av parametrar, till exempel aktivt/inaktivt undernät, externa anslutningar, aktiva flöden och skadliga flöden i under nätet.
- Under nät sto pol Ogin visar trafik distributionen till ett virtuellt nätverk med avseende på flöden (tillåtna/blockerade/inkommande/utgående/ofarlig/skadlig/skadlig), program protokoll och NSG: er, till exempel:

    ![Under näts topologi som demonstrerar trafik distribution ett virtuellt nätverks under nät med avseende på flöden](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

**Titta efter**

Trafik distribution per Programgateway & Load Balancer, topologi, topp källor för trafik, de främsta falska nätverken konversation till Application Gateway & Load Balancer och Top konversation-programprotokoll. 
    
 - Veta vilket undernät som är konversation till vilken Application Gateway eller Load Balancer. Om du upptäcker oväntade konversationer kan du korrigera konfigurationen.
 - Om falska nätverk är konversation med en Programgateway eller Load Balancer kan du korrigera det genom att konfigurera NSG-regler för att blockera de falska nätverken. 

    ![Skärm bild som visar en under näts topologi med trafik distribution till ett Application Gateway-undernät med avseende på flöden.](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>Visa portar och virtuella datorer som tar emot trafik från Internet

**Titta efter**

- Vilka öppna portar är konversation via Internet?
  - Om oväntade portar har hittats öppna kan du korrigera konfigurationen:

    ![Instrument panels demonstrations portar som tar emot och skickar trafik till Internet](./media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

    ![Information om Azures mål portar och värdar](./media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**Titta efter**

Har du skadlig trafik i din miljö? Var kommer den från? Var är den avsedd för?

![Information om skadliga trafik flöden i loggs ökning](./media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsgnsg-rules-hits"></a>Visualisera trender i NSG/NSG-regel träffar

**Titta efter**

- Vilka NSG/NSG-regler har flest träffar i jämför ande diagram med flödes distribution?
- Vilka är de största käll-och destinations samtals paren per NSG/NSG-regler?

    ![Instrument panel Showcase NSG-träffar statistik](./media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- I följande bilder visas tids trender för träffar av NSG-regler och information om käll destinations flöden för en nätverks säkerhets grupp:

  - Identifiera snabbt vilka NSG: er-och NSG-regler som passerar skadliga flöden och vilka som är de vanligaste skadliga IP-adresserna som används för att komma åt din moln miljö
  - Identifiera vilka NSG/NSG-regler som tillåter/blockerar betydande nätverks trafik
  - Välj Top filter för detaljerad granskning av en NSG-eller NSG-regel

    ![Visa tids trender för NSG regel träffar och de främsta NSG-reglerna](./media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![Statistik information om främsta NSG i loggs ökning](./media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

För att få svar på vanliga frågor, se [vanliga frågor och svar om trafik analys](traffic-analytics-faq.md).

## <a name="next-steps"></a>Nästa steg

- Information om hur du aktiverar flödes loggar finns i [Aktivera NSG flödes loggning](network-watcher-nsg-flow-logging-portal.md).
- Information om schema och bearbetning av Trafikanalys finns i [Traffic Analytics-schemat](traffic-analytics-schema.md).
