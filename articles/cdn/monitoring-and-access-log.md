---
title: Övervakning, statistik och råa loggar för Azure CDN
description: I den här artikeln beskrivs hur du konfigurerar och använder Azure CDN övervakning, statistik och råa loggar.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/23/2020
ms.author: yuajia
ms.openlocfilehash: 8a4b48586c564ee5d14a0768156b0477e2935ccf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575445"
---
# <a name="real-time-monitoring-metrics-and-access-logs-for-azure-cdn"></a>Real tids övervakning, Mät värden och åtkomst loggar för Azure CDN
Med Azure CDN från Microsoft kan du övervaka resurser på följande sätt för att hjälpa dig att felsöka, spåra och felsöka problem. 

* Obehandlade loggar innehåller omfattande information om varje begäran som CDN tar emot. Obehandlade loggar skiljer sig från aktivitets loggar. Aktivitets loggarna ger insyn i de åtgärder som utförs på Azure-resurser.
* Mått, som visar fyra nyckel mått för CDN, inklusive antal byte träff, antal förfrågningar, svars storlek och total svars tid. Den innehåller också olika mått för att dela upp mått.
* Avisering, som gör att kunden kan ställa in aviseringar för nyckel mått
* Ytterligare mått, som gör det möjligt för kunder att använda Azure Log Analytics för att aktivera ytterligare Mät värden. Vi tillhandahåller även fråge exempel för några andra mått under Azure Log Analytics.

> [!IMPORTANT]
> Funktionen HTTP RAW-loggar är tillgänglig för Azure CDN från Microsoft.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="configuration---azure-portal"></a>Konfiguration – Azure Portal

Så här konfigurerar du obehandlade loggar för din Azure CDN från Microsoft-profilen: 

1. Välj **alla resurser** på Azure Portal-menyn  >>  **\<your-CDN-profile>** .

2. Under **Övervakning** väljer du **Diagnostikinställningar**.

3. Välj **+ Lägg till diagnostisk inställning**.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-01.png" alt-text="Lägg till diagnostisk inställning för CDN-profil." border="true":::
    
    > [!IMPORTANT]
    > Råa loggar är bara tillgängliga på profil nivå medan sammanställda HTTP-statuskod loggar är tillgängliga på slut punkts nivå.

4. Under **diagnostikinställningar** anger du ett namn för den diagnostiska inställningen under **diagnostiska** inställnings namn.

5. Välj **AzureCdnAccessLog** och ange kvarhållning i dagar.

6. Välj **mål information**. Mål alternativen är:
    * **Skicka till Log Analytics**
        * Välj **prenumerationen** och **Log Analytics arbets ytan**.
    * **Arkivera till ett lagrings konto**
        * Välj **prenumerationen** och **lagrings kontot**.
    * **Strömma till en händelsehubb**
        * Välj **prenumeration**, **Event Hub-namnrymd**, **Event Hub-namn (valfritt)** och **princip namn för Event Hub**.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-02.png" alt-text="Konfigurera målet för logg inställningar." border="true":::

7. Välj **Spara**.

## <a name="configuration---azure-powershell"></a>Konfiguration – Azure PowerShell

Använd [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) för att konfigurera den diagnostiska inställningen för obehandlade loggar.

Kvarhållning av data definieras av alternativet **-RetentionInDays** i kommandot.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>Aktivera diagnostikloggar i ett lagrings konto

1. Logga in på Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. Om du vill aktivera diagnostikloggar i ett lagrings konto anger du dessa kommandon. Ersätt variablerna med dina värden:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Aktivera diagnostikloggar för Log Analytics arbets yta

1. Logga in på Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Om du vill aktivera diagnostikloggar för en arbets yta Log Analytics anger du dessa kommandon. Ersätt variablerna med dina värden:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>Aktivera diagnostikloggar för Event Hub-namnområde

1. Logga in på Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Om du vill aktivera diagnostikloggar för ett Event Hub-namnområde anger du dessa kommandon. Ersätt variablerna med dina värden:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $evthubnamespace = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $eventhub = Get-AzEventHubNamespace -ResourceGroupName $rsg -Name $eventhubname

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhub.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

## <a name="raw-logs-properties"></a>Egenskaper för RAW-loggar

Azure CDN från Microsoft-tjänsten tillhandahåller för närvarande obehandlade loggar. Obehandlade loggar innehåller enskilda API-begäranden med varje post med följande schema: 

| Egenskap  | Beskrivning |
| ------------- | ------------- |
| BackendHostname | Om begäran vidarebefordras till en server del representerar Server delens värdnamn. Det här fältet är tomt om begäran omdirigeras eller vidarebefordras till ett regionalt cacheminne (när cachelagring är aktiverat för regeln). |
| CacheStatus | För cachelagring av scenarier definierar det här fältet cache-träff/missar på POP |
| ClientIp | IP-adressen för klienten som gjorde begäran. Om det fanns ett X-vidarebefordrat-för-huvud i begäran plockas klientens IP-adress från samma. |
| ClientPort | IP-porten för den klient som gjorde begäran. |
| HttpMethod | HTTP-metod som används av begäran. |
| HttpStatusCode | HTTP-statuskod som returnerades från proxyservern. |
| HttpStatusDetails | Resulterande status för begäran. Innebörd av detta sträng värde finns i en status referens tabell. |
| HttpVersion | Typ av begäran eller anslutning. |
| POP | Kort namn på den kant där begäran landats. |
| RequestBytes | Storleken på HTTP Request-meddelandet i byte, inklusive begärandehuvuden och begär ande texten. |
| RequestUri | URI för den mottagna begäran. |
| ResponseBytes | Byte som skickats av backend-servern som svar.  |
| RoutingRuleName | Namnet på den routningsregler som begäran matchade. |
| RulesEngineMatchNames | Namnen på de regler som begäran matchade. |
| SecurityProtocol | TLS/SSL-protokollets version som används av begäran eller null om ingen kryptering. |
| SentToOriginShield </br> (inaktuell) * **se kommentarer om utfasning i följande avsnitt.**| Om värdet är true innebär det att begäran besvarades från ursprungs sköldens cacheminne i stället för Edge-pop. Ursprungs sköld är ett överordnat cacheminne som används för att förbättra förhållandet mellan cacheträffar. |
| isReceivedFromClient | Om värdet är true innebär det att begäran kom från klienten. Om det här värdet är falskt, är begäran en brist i kanten (underordnad POP) och är besvarad från ursprungs skölden (överordnad POP). |
| TimeTaken | Tiden från första byte av begäran till front dörren till sista mottagna byte, i sekunder. |
| TrackingReference | Den unika referens strängen som identifierar en begäran som betjänas av en front dörr, som också skickas som X-Azure-ref-huvud till klienten. Krävs för att söka efter information i åtkomst loggarna för en speciell begäran. |
| UserAgent | Webbläsarens typ som används av klienten. |
| Mängden | Det här fältet innehåller den specifika typen av fel för att begränsa fel söknings området. </br> Möjliga värden är: </br> **Noerror**: indikerar att inga fel hittades. </br> **CertificateError**: allmänt SSL-certifikat fel.</br> **CertificateNameCheckFailed**: värd namnet i SSL-certifikatet är ogiltigt eller matchar inte. </br> **ClientDisconnected**: det gick inte att utföra begäran på grund av klient nätverks anslutning. </br> **UnspecifiedClientError**: allmänt klient fel. </br> **InvalidRequest**: ogiltig begäran. Det kan bero på en felaktig rubrik, brödtext och URL. </br> **DNSFailure**: DNS-haveri. </br> **DNSNameNotResolved**: det gick inte att matcha Server namnet eller adressen. </br> **OriginConnectionAborted**: anslutningen med ursprunget stoppades plötsligt. </br> **OriginConnectionError**: fel i allmän ursprungs anslutning. </br> **OriginConnectionRefused**: det gick inte att upprätta anslutningen till ursprunget. </br> **OriginError**: allmänt ursprungs fel. </br> **OriginInvalidResponse**: ursprung returnerade ett ogiltigt eller okänt svar. </br> **OriginTimeout**: tids gränsen för ursprungs förfrågan har gått ut. </br> **ResponseHeaderTooBig**: ursprunget returnerade för stort av ett svars huvud. </br> **RestrictedIP**: begäran blockerades på grund av en begränsad IP-adress. </br> **SSLHandshakeError**: det gick inte att upprätta en anslutning till sitt ursprung på grund av ett SYNKRONISERINGSFEL vid SSL-skakning. </br> **UnspecifiedError**: ett fel inträffade som inte fick plats i något av felen i tabellen. |
| TimeToFirstByte | Hur lång tid i millisekunder från när Microsoft CDN tar emot begäran till den tidpunkt då den första byten skickas till klienten. Tiden mäts bara från Microsoft-sidan. Data på klient sidan mäts inte. |
> [!NOTE]
> Loggarna kan visas under din Log Analytics profil genom att köra en fråga. En exempel fråga skulle se ut så här:
    ```
    AzureDiagnostics | where Category == "AzureCdnAccessLog"
    ```

### <a name="sent-to-origin-shield-deprecation"></a>Skickas till ursprungs sköldens utfasning
Den råa logg egenskapen **isSentToOriginShield** har ersatts och ersatts av ett nytt fält **isReceivedFromClient**. Använd det nya fältet om du redan använder det inaktuella fältet. 

Obehandlade loggar innehåller loggar som skapats från både CDN-Edge (underordnad POP) och ursprungs sköld. Ursprungs sköld syftar på överordnade noder som är strategiskt placerade över hela världen. Dessa noder kommunicerar med ursprungs servrar och minskar belastningen på belastningen på ursprunget. 

För varje begäran som går till ursprungs sköld finns det två logg poster:

* En för Edge-noder
* En för ursprungs sköld. 

Om du vill särskilja utgången eller svaren från Edge-noderna eller ursprungs skölden kan du använda fältet **isReceivedFromClient** för att hämta rätt data. 

Om värdet är false innebär det att begäran besvaras från ursprungs sköld till Edge-noder. Den här metoden är effektiv för att jämföra obehandlade loggar med fakturerings data. Avgifter debiteras inte för utgående från ursprungs sköld till Edge-noderna. Avgifter debiteras för utgående från Edge-noderna till klienter. 

**Kusto Query-exempel för att utesluta loggar som skapats på ursprungs sköld i Log Analytics.**

```kusto
AzureDiagnostics 
| where OperationName == "Microsoft.Cdn/Profiles/AccessLog/Write" and Category == "AzureCdnAccessLog"  
| where isReceivedFromClient == true

```

> [!IMPORTANT]
> Funktionen HTTP RAW-loggar är tillgänglig automatiskt för profiler som skapats eller uppdaterats efter **25 februari 2020**. För CDN-profiler som skapats tidigare bör du uppdatera CDN-slutpunkten när du har konfigurerat loggning. Till exempel kan en navigera till geo-filtrering under CDN-slutpunkter och blockera alla länder/regioner som inte är relevanta för deras arbets belastning och tryck på Spara.


## <a name="metrics"></a>Mått
Azure CDN från Microsoft är integrerat med Azure Monitor och publicerar fyra CDN-mått för att kunna spåra, felsöka och felsöka problem. 

Måtten visas i diagram och kan nås via PowerShell, CLI och API. CDN-måtten är kostnads fria.

Azure CDN från Microsoft-mått och skickar sina mått i intervall om 60 sekunder. Måtten kan ta upp till 3 minuter innan de visas i portalen. 

Mer information finns i [Azure Monitor mått](../azure-monitor/essentials/data-platform-metrics.md).

**Mått som stöds av Azure CDN från Microsoft**

| Mått  | Beskrivning | Dimensioner |
| ------------- | ------------- | ------------- |
| Träff grad för byte * | Procent andelen utgående från CDN-cachen, beräknad mot det totala utgående värdet. | Slutpunkt |
| RequestCount | Antalet klient begär Anden som hanteras av CDN. | Slutpunkt </br> Klient land. </br> Klient region. </br> HTTP-status. </br> HTTP-status grupp. |
| ResponseSize | Antalet byte som har skickats som svar från CDN-Edge till klienter. |Slutpunkt </br> Klient land. </br> Klient region. </br> HTTP-status. </br> HTTP-status grupp. |
| TotalLatency | Den totala tiden från klient förfrågan som mottagits av CDN **tills den sista svars bytet skickades från CDN till klienten**. |Slutpunkt </br> Klient land. </br> Klient region. </br> HTTP-status. </br> HTTP-status grupp. |

***Antal byte träff-portioner = (utgående från Edge-utgång från ursprung)/egress från gräns**

Scenarier som exkluderas i beräkningen av byte-träffar:

* Du konfigurerar uttryckligen inget cacheminne antingen via regel motor eller cachelagring av frågesträngar.
* Du konfigurerar inte Cache-Control-direktivet explicit utan lagring eller privat cache.

### <a name="metrics-configuration"></a>Mått konfiguration

1. Välj **alla resurser** på Azure Portal-menyn  >>  **\<your-CDN-profile>** .

2. Under **övervakning** väljer du **mått**:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-03.png" alt-text="Mått för CDN-profilen." border="true":::

3. Välj **Lägg till mått**, Välj måttet som ska läggas till:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-04.png" alt-text="Lägg till och välj mått för CDN-profilen." border="true":::

4. Välj **Lägg till filter** för att lägga till ett filter:
    
    :::image type="content" source="./media/cdn-raw-logs/raw-logs-05.png" alt-text="Använd filter för mått." border="true":::

5. Välj **Använd** delning för att se trenden efter olika dimensioner:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-06.png" alt-text="Använd delning till måttet." border="true":::

6. Välj **nytt diagram** för att lägga till ett nytt diagram:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-07.png" alt-text="Lägg till nytt diagram i vyn mått." border="true":::

### <a name="alerts"></a>Aviseringar

Du kan konfigurera aviseringar i Microsoft CDN genom att välja **övervaknings**  >>  **aviseringar**.

Välj **ny varnings regel** för mått som anges i avsnittet mått:

:::image type="content" source="./media/cdn-raw-logs/raw-logs-08.png" alt-text="Konfigurera aviseringar för CDN-slutpunkten." border="true":::

Aviseringen kommer att debiteras baserat på Azure Monitor. Mer information om aviseringar finns i [Azure Monitor aviseringar](../azure-monitor/alerts/alerts-overview.md).

### <a name="additional-metrics"></a>Ytterligare mått
Du kan aktivera ytterligare mått med hjälp av Azure Log Analytics och råa loggar för ytterligare kostnader.

1. Följ stegen ovan för att aktivera diagnostik för att skicka rå logg till Log Analytics.

2. Välj Log Analytics arbets ytan som du skapade:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-09.png" alt-text="Välj Log Analytics-arbetsyta" border="true":::   

3. Välj **loggar** under **Allmänt** i Log Analytics-arbetsytan.  Välj sedan **Kom igång**:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-10.png" alt-text="Arbets ytan Log Analytics-resurs." border="true":::   
 
4. Välj **CDN-profiler**.  Välj en exempel fråga att köra eller Stäng exempel skärmen för att ange en anpassad fråga:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-11.png" alt-text="Exempel på fråga-skärmen." border="true":::   

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-12.png" alt-text="Frågekörningen." border="true":::   

4. Om du vill visa data efter diagram väljer du **diagram**.  Välj **Fäst på instrument panelen** för att fästa diagrammet på Azure-instrumentpanelen:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-13.png" alt-text="Fäst diagram på instrument panelen." border="true"::: 

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du aktiverat HTTP RAW-loggar för Microsoft CDN-tjänsten.

Mer information om Azure CDN och de andra Azure-tjänsterna som nämns i den här artikeln finns i:

* [Analysera](cdn-log-analysis.md) Azure CDN användnings mönster.

* Läs mer om [Azure Monitor](../azure-monitor/overview.md).

* Konfigurera [Log Analytics i Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md).