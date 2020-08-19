---
title: Skala och var värd i Azure Functions
description: Lär dig hur du väljer mellan Azure Functions förbruknings plan och Premium-plan.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 08/17/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 80bb59527f416afd78b992fb12a4ef72956f91b7
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587233"
---
# <a name="azure-functions-scale-and-hosting"></a>Skala och var värd i Azure Functions

När du skapar en Function-app i Azure måste du välja en värd plan för din app. Det finns tre grundläggande värd planer för Azure Functions: [förbruknings plan](#consumption-plan), [Premium plan](#premium-plan)och [dedikerad (App Service) plan](#app-service-plan). Alla värd planer är allmänt tillgängliga (GA) på både virtuella Linux-och Windows-datorer.

Värd planen du väljer styr följande beteenden:

* Hur din Function-app skalas.
* De resurser som är tillgängliga för varje funktions program instans.
* Stöd för avancerade funktioner, till exempel Azure Virtual Network-anslutning.

Både förbruknings-och Premium planer lägger automatiskt till beräknings kraft när koden körs. Din app skalas ut när det behövs för att hantera belastningen och skalas i när kod slutar köras. För förbruknings planen behöver du inte heller betala för inaktiva virtuella datorer eller reserv kapacitet i förväg.  

Premium-prenumerationen innehåller ytterligare funktioner, till exempel Premium Compute-instanser, möjligheten att hålla instanserna varmt under obestämd tid och VNet-anslutning.

Med App Service plan kan du dra nytta av dedikerad infrastruktur som du hanterar. Din Function-app skalar inte baserat på händelser, vilket innebär att aldrig skalas in till noll. (Kräver att [Always on](#always-on) är aktiverat.)

En detaljerad jämförelse mellan de olika värd planerna (inklusive Kubernetes-baserad värd) finns i [jämförelse avsnittet värd planer](#hosting-plans-comparison).

## <a name="consumption-plan"></a>Förbrukningsplan

När du använder förbruknings planen läggs instanser av Azure Functions-värden dynamiskt till och tas bort baserat på antalet inkommande händelser. Den här serverlösa planen skalas automatiskt och du debiteras bara för beräkningsresurser när dina funktioner körs. I en förbrukningsplan får en funktions körningstid timeout efter en konfigurerbar tidsperiod.

Fakturering baseras på antalet körningar, körningstid och använt minne. Fakturering slås samman över alla funktioner inom en funktionsapp. Mer information finns på sidan med [Azure Functions priser](https://azure.microsoft.com/pricing/details/functions/).

Förbruknings planen är standard värd planen och ger följande fördelar:

* Betala endast när funktionerna körs
* Skala ut automatiskt, även under perioder med hög belastning

Function-appar i samma region kan tilldelas samma förbruknings plan. Det finns ingen nack delar eller påverkan på att flera appar körs i samma förbruknings plan. Tilldelning av flera appar till samma förbruknings plan påverkar inte återhämtning, skalbarhet eller tillförlitlighet för varje app.

Mer information om hur du beräknar kostnader när du kör i en förbruknings plan finns i [förstå förbruknings plan kostnader](functions-consumption-costs.md).

## <a name="premium-plan"></a><a name="premium-plan"></a>Premiumplan

När du använder Premium-planen läggs instanser av Azure Functions-värden till och tas bort baserat på antalet inkommande händelser precis som förbruknings planen.  Premium-planen stöder följande funktioner:

* Ständigt varma instanser för att undvika kall start
* VNet-anslutning
* Obegränsad körnings tid (60 minuter garanterad)
* Premium-instans storlekar (en kärna, två kärnor och fyra kärn instanser)
* Mer förutsägbar prissättning
* App-allokering med hög densitet för planer med flera Function-appar

Information om hur du skapar en Function-app i en Premium-plan finns [Azure Functions Premium plan](functions-premium-plan.md).

I stället för fakturering per körning och använt minne baseras faktureringen för Premium-prenumerationen på det antal kärn sekunder och minne som används för alla nödvändiga och förvärmade instanser. Minst en instans måste vara varm vid alla tidpunkter per plan. Det innebär att det finns en lägsta månads kostnad per aktiv plan, oavsett antalet körningar. Tänk på att alla funktions program i en Premium plan delar förvärmade och aktiva instanser.

Överväg Azure Functions Premium-planen i följande situationer:

* Dina Function-appar körs kontinuerligt eller nästan kontinuerligt.
* Du har ett stort antal små körningar och har en hög körnings faktura men låg GB andra fakturor i förbruknings planen.
* Du behöver fler processor-eller minnes alternativ än vad som tillhandahålls av förbruknings planen.
* Din kod måste köras längre än den [maximala körnings tiden som tillåts](#timeout) i förbruknings planen.
* Du behöver funktioner som bara är tillgängliga i en Premium-plan, till exempel virtuell nätverks anslutning. 

## <a name="dedicated-app-service-plan"></a><a name="app-service-plan"></a>Dedikerad (App Service) plan

Dina Function-appar kan också köras på samma dedikerade virtuella datorer som andra App Service appar (Basic, standard, Premium och isolerade SKU: er).

Överväg ett App Service plan i följande situationer:

* Du har befintliga, underutnyttjade virtuella datorer som redan kör andra App Service instanser.
* Du vill ange en anpassad avbildning som dina funktioner ska köras på.

Du betalar samma för functions-appar i en App Service planera precis som för andra App Service resurser, t. ex. Web Apps. Mer information om hur App Service plan fungerar finns [i Översikt över Azure App Service planer](../app-service/overview-hosting-plans.md).

Med en App Service plan kan du manuellt skala ut genom att lägga till fler VM-instanser. Du kan också aktivera autoskalning. Mer information finns i [skala antalet instanser manuellt eller automatiskt](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Du kan också skala upp genom att välja en annan App Service plan. Mer information finns i [skala upp en app i Azure](../app-service/manage-scale-up.md). 

När du kör JavaScript-funktioner på en App Service plan bör du välja en plan som har färre virtuella processorer. Mer information finns i [välj App Service planer med en kärna](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

Genom att köra i en [App Service-miljön](../app-service/environment/intro.md) (ASE) kan du helt isolera dina funktioner och dra nytta av stor skala.

### <a name="always-on"></a><a name="always-on"></a> Always on

Om du kör på en App Service plan bör du aktivera inställningen **Always on** så att din funktions app fungerar som den ska. På en App Service plan aktive ras funktions körningen efter några minuter av inaktivitet, så endast HTTP-utlösare kommer att aktivera dina funktioner. Always On är bara tillgängligt på en App Service plan. I en förbruknings plan aktiverar plattformen automatiskt funktions appar.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Även om Always On är aktiverat, styrs körningens tids gräns för enskilda funktioner av `functionTimeout` inställningen i [host.js](functions-host-json.md#functiontimeout) i projekt filen.

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Fastställa värd planen för ett befintligt program

För att avgöra vilken värd plan som används av din Function-app, se **App Service plan** på fliken **Översikt** för function-appen i [Azure Portal](https://portal.azure.com). Om du vill se pris nivån väljer du namnet på **app Services planen**och väljer sedan **Egenskaper** i det vänstra fönstret.

![Visa skalnings plan i portalen](./media/functions-scale/function-app-overview-portal.png)

Du kan också använda Azure CLI för att fastställa planen enligt följande:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

När utdata från det här kommandot är är `dynamic` din Function-app i förbruknings planen. När utdata från det här kommandot är är `ElasticPremium` din Function-app i Premium-planen. Alla andra värden indikerar olika nivåer för en App Service plan.

## <a name="storage-account-requirements"></a>Krav för lagringskonto

I alla planer kräver en Function-app ett allmänt Azure Storage konto, som stöder Azure Blob, Queue, Files och table Storage. Detta beror på att Azure Functions förlitar sig på Azure Storage för åtgärder som att hantera utlösare och loggning av funktions körningar, men vissa lagrings konton stöder inte köer och tabeller. Dessa konton, som inkluderar BLOB-endast lagrings konton (inklusive Premium Storage) och allmänna lagrings konton med zon-redundant lagrings replikering, filtreras bort från dina befintliga **lagrings konto** val när du skapar en Function-app.

Samma lagrings konto som används av din Function-app kan också användas av utlösare och bindningar för att lagra program data. För lagrings intensiva åtgärder bör du dock använda ett separat lagrings konto.  

Det är möjligt att flera Function-appar delar samma lagrings konto utan problem. (Ett exempel på detta är när du utvecklar flera appar i din lokala miljö med hjälp av Azure Storage emulator, som fungerar som ett lagrings konto.) 

<!-- JH: Does using a Premium Storage account improve perf? -->

Mer information om lagrings konto typer finns i [Introduktion till Azure Storage-tjänsterna](../storage/common/storage-introduction.md#core-storage-services).

## <a name="how-the-consumption-and-premium-plans-work"></a>Så här fungerar förbruknings- och premiumplanerna

I förbruknings-och Premium-planerna skalar Azure Functions-infrastrukturen processor-och minnes resurser genom att lägga till ytterligare instanser av Functions-värden, baserat på antalet händelser som dess funktioner aktive ras på. Varje instans av Functions-värden i förbruknings planen är begränsad till 1,5 GB minne och en processor.  En instans av värden är hela Function-appen, vilket innebär att alla funktioner i en Function-resurs resurs i en instans och skalas på samma gång. Function-appar som delar samma förbruknings plan skalas oberoende av varandra.  I Premium-planen avgör din Plans storlek det tillgängliga minnet och CPU: n för alla appar i planen på den instansen.  

Funktions kod filen lagras på Azure Files resurser på funktionens huvud lagrings konto. När du tar bort huvud lagrings kontot för Function-appen tas funktions kod filerna bort och kan inte återställas.

### <a name="runtime-scaling"></a>Körnings skalning

Azure Functions använder en komponent som kallas för *skalnings styrenheten* för att övervaka händelse frekvensen och bestämma om du vill skala ut eller skala in. Skalnings styrenheten använder heuristik för varje utlösnings typ. Om du till exempel använder en Azure Queue Storage-utlösare, skalas den baserat på köns längd och ålder för det äldsta Queue meddelandet.

Skalnings enheten för Azure Functions är Function-appen. När funktions programmet skalas ut allokeras ytterligare resurser för att köra flera instanser av den Azure Functions värden. I takt med att Compute demand minskas, tar skalnings kontrollen bort funktions värd instanser. Antalet instanser *skalas* slutligen till noll när inga funktioner körs i en Function-app.

![Skala övervakning av kontroll händelser och skapa instanser](./media/functions-scale/central-listener.png)

### <a name="cold-start"></a>Kall start

När din Function-app har varit inaktiv i ett antal minuter kan plattformen skala antalet instanser som din app körs ned till noll. Nästa begäran har lagt till en fördröjning för skalning från noll till en. Den här fördröjningen kallas _kallstart_. Antalet beroenden som måste läsas in av funktions programmet kan påverka kall start tiden. Kall start är ett problem för synkrona åtgärder, till exempel HTTP-utlösare som måste returnera ett svar. Om kall börjar påverka dina funktioner kan du överväga att köra i en Premium-plan eller i en dedikerad plan med Always on-aktiverad.   

### <a name="understanding-scaling-behaviors"></a>Förstå skalnings beteenden

Skalning kan variera beroende på ett antal faktorer och skala på olika sätt beroende på vilken utlösare och vilket språk som valts. Det finns några erna för skalnings beteenden som kan vara medvetna om:

* En enda Function-app skalar bara ut till högst 200 instanser. En enskild instans kan bearbeta mer än ett meddelande eller en begäran i taget, så det finns ingen angiven gräns för antalet samtidiga körningar.  Du kan [Ange ett lägre värde](#limit-scale-out) för att begränsa skalningen efter behov.
* För HTTP-utlösare allokeras nya instanser, högst en gång per sekund.
* För icke-HTTP-utlösare allokeras nya instanser, högst en gång var 30: e sekund. Skalning är snabbare när du kör i en [Premium-plan](#premium-plan).
* För Service Bus utlösare använder du _Hantera_ rättigheter för resurser för den mest effektiva skalningen. Med _avlyssnings_ rättigheter är skalning inte lika tillförlitligt eftersom köns längd inte kan användas för att informera om skalnings beslut. Mer information om hur du ställer in rättigheter i Service Bus åtkomst principer finns i [auktoriseringsprincipen för delad åtkomst](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies).
* För Event Hub-utlösare, se [vägledningen för skalning](functions-bindings-event-hubs-trigger.md#scaling) i referens artikeln. 

### <a name="limit-scale-out"></a>Begränsa skala ut

Du kanske vill begränsa antalet instanser som en app skalar ut till.  Detta är vanligt för fall där en underordnad komponent som en databas har begränsat data flöde.  Som standard skalas förbruknings Plans funktionerna ut till så många som 200 instanser, och Premium plan-funktioner kommer att skalas ut till så många som 100-instanser.  Du kan ange ett lägre Max värde för en speciell app genom att ändra `functionAppScaleLimit` värdet.  `functionAppScaleLimit`Kan anges till 0 eller null för obegränsade, eller ett giltigt värde mellan 1 och appens Max värde.

```azurecli
az resource update --resource-type Microsoft.Web/sites -g <resource_group> -n <function_app_name>/config/web --set properties.functionAppScaleLimit=<scale_limit>
```

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Metod tips och mönster för skalbara appar

Det finns många aspekter av en Function-app som påverkar hur väl den kommer att skalas, inklusive värd konfiguration, körnings miljö och resurs effektivitet.  Mer information finns i [avsnittet om skalbarhet i artikeln om prestanda överväganden](functions-best-practices.md#scalability-best-practices). Du bör också vara medveten om hur anslutningar fungerar när din Function-app skalar. Mer information finns i [hantera anslutningar i Azure Functions](manage-connections.md).

Mer information om skalning i python och Node.js finns i [Azure Functions python Developer Guide-skalning och samtidighet](functions-reference-python.md#scaling-and-concurrency) och [Azure Functions Node.js utvecklare-guide – skalning och samtidighet](functions-reference-node.md#scaling-and-concurrency).

### <a name="billing-model"></a>Faktureringsmodell

Faktureringen för olika planer beskrivs i detalj på [sidan Azure Functions prissättning](https://azure.microsoft.com/pricing/details/functions/). Användningen sammanställs på Function-app-nivå och räknar bara den tid som funktions koden körs. Följande är enheter för fakturering:

* **Resursförbrukning i GB-sekunder (GB-s)**. Beräknad som en kombination av minnes storlek och körnings tid för alla funktioner i en Function-app. 
* **Körningar**. Räknas varje gången en funktion körs som svar på en händelse utlösare.

Användbara frågor och information om hur du förstår din förbruknings faktura finns [i vanliga frågor och svar om fakturering](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="hosting-plans-comparison"></a>Jämförelse av värdplaner

Följande jämförelse tabell visar alla viktiga aspekter som kan hjälpa dig med beslutet att Azure Functions:

### <a name="plan-summary"></a>Plan Sammanfattning
| | |
| --- | --- |  
|**[Förbrukningsplan](#consumption-plan)**| Skala automatiskt och betala bara för beräknings resurser när funktionerna körs. I förbruknings planen läggs instanser av funktions värden dynamiskt till och tas bort baserat på antalet inkommande händelser.<br/> ✔ Standard värd plan.<br/>✔ Endast betala när funktionerna körs.<br/>✔ skala ut automatiskt, även under perioder med hög belastning.|  
|**[Premiumplan](#premium-plan)**|När du skalar automatiskt baserat på efter frågan använder du förvärmade arbetare för att köra program utan fördröjning efter att de varit inaktiva, köra på mer kraftfulla instanser och ansluta till virtuella nätverk. Överväg Azure Functions Premium-planen i följande situationer, förutom alla funktioner i App Service plan: <br/>✔ Dina funktions appar körs kontinuerligt eller nästan kontinuerligt.<br/>✔ Du har ett stort antal små körningar och har en hög körnings faktura men låg GB andra fakturor i förbruknings planen.<br/>✔ Du behöver fler processor-eller minnes alternativ än vad som tillhandahålls av förbruknings planen.<br/>✔ Din kod behöver köra längre än den maximala körnings tiden som tillåts i förbruknings planen.<br/>✔ Du behöver funktioner som bara är tillgängliga i en Premium-prenumeration, till exempel virtuell nätverks anslutning.|  
|**[Dedikerad plan](#app-service-plan)**<sup>1</sup>|Kör dina funktioner inom ett App Service plan med jämna App Service plans taxor. Passar bra för långvariga åtgärder, samt när mer förutsägelse skalning och kostnader krävs. Överväg ett App Service plan i följande situationer:<br/>✔ Du har befintliga, underutnyttjade virtuella datorer som redan kör andra App Service-instanser.<br/>✔ Du vill tillhandahålla en anpassad avbildning som dina funktioner ska köras på.|  
|**[ASE](#app-service-plan)**<sup>1</sup>|App Service-miljön (ASE) är en App Service funktion som ger en helt isolerad och dedikerad miljö för säker körning av App Service appar i hög skala. ASE är lämpliga för program arbets belastningar som kräver: <br/>✔ Mycket hög skala.<br/>✔ Isolering och säker nätverks åtkomst.<br/>✔ Hög minnes användning.|  
| **[Kubernetes](functions-kubernetes-keda.md)** | Kubernetes tillhandahåller en helt isolerad och dedikerad miljö som körs ovanpå Kubernetes-plattformen.  Kubernetes är lämpligt för program arbets belastningar som kräver: <br/>✔ Anpassade maskin varu krav.<br/>✔ Isolering och säker nätverks åtkomst.<br/>✔ Möjlighet att köra i hybrid miljöer eller miljöer med flera moln.<br/>✔ Att köra tillsammans med befintliga Kubernetes-program och-tjänster.|  

<sup>1</sup> vissa gränser för de olika App Service plan alternativen finns i [App Service plan gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="operating-systemruntime"></a>Operativ system/körning

| | Linux<sup>1</sup><br/>Endast kod | Windows<sup>2</sup><br/>Endast kod | Linux<sup>1, 3</sup><br/>Docker-behållare |
| --- | --- | --- | --- |
| **[Förbrukningsplan](#consumption-plan)** | .NET Core<br/>Node.js<br/>Java<br/>Python | .NET Core<br/>Node.js<br/>Java<br/>PowerShell Core | Inget stöd  |
| **[Premiumplan](#premium-plan)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python  | 
| **[Dedikerade plan](#app-service-plan)**<sup>4</sup> | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |
| **[ASE](#app-service-plan)**<sup>4</sup> | .NET Core<br/>Node.js<br/>Java<br/>Python |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core  |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python | 
| **[Kubernetes](functions-kubernetes-keda.md)** | saknas | saknas |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |

<sup>1</sup> Linux är det enda operativ system som stöds för python runtime-stacken.  
<sup>2</sup> Windows är det enda operativ system som stöds för PowerShell runtime-stacken.   
<sup>3</sup> Linux är det enda operativ system som stöds för Docker-behållare.
<sup>4</sup> för vissa gränser för de olika App Service plan alternativen, se [App Service plan gränserna](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="scale"></a>Skala

| | Skala ut | Maximalt antal instanser |
| --- | --- | --- |
| **[Förbrukningsplan](#consumption-plan)** | Händelse driven. Skala ut automatiskt, även vid perioder med hög belastning. Azure Functions-infrastrukturen skalar processor-och minnes resurser genom att lägga till ytterligare instanser av Functions-värden, baserat på antalet händelser som dess funktioner aktive ras på. | 200 |
| **[Premiumplan](#premium-plan)** | Händelse driven. Skala ut automatiskt, även vid perioder med hög belastning. Azure Functions-infrastrukturen skalar processor-och minnes resurser genom att lägga till ytterligare instanser av Functions-värden, baserat på antalet händelser som dess funktioner aktive ras på. |100|
| **[Dedikerad plan](#app-service-plan)**<sup>1</sup> | Manuell/autoskalning |10-20|
| **[ASE](#app-service-plan)**<sup>1</sup> | Manuell/autoskalning |100 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | Händelse driven autoskalning för Kubernetes-kluster med [KEDA](https://keda.sh). | Varierar beroende &nbsp; på &nbsp; kluster.&nbsp;&nbsp;|

<sup>1</sup> vissa gränser för de olika App Service plan alternativen finns i [App Service plan gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="cold-start-behavior"></a>Kall start beteende

|    |    | 
| -- | -- |
| **[Förbruknings &nbsp; plan](#consumption-plan)** | Appar kan skalas till noll om de är inaktiva under en viss tids period, vilket innebär att vissa begär Anden kan ha ytterligare svars tid vid start.  Förbruknings planen har vissa optimeringar som hjälper till att minska kallstart tiden, inklusive att dra från förvärmade placeholder funktioner som redan har funktions värden och språk processer som körs. |
| **[Premiumplan](#premium-plan)** | En permanent varm instans för att undvika kall start. |
| **[Dedikerad plan](#app-service-plan)**<sup>1</sup> | När körs i en dedikerad plan kan funktions värden köras kontinuerligt, vilket innebär att kall start inte egentligen är ett problem. |
| **[ASE](#app-service-plan)**<sup>1</sup> | När körs i en dedikerad plan kan funktions värden köras kontinuerligt, vilket innebär att kall start inte egentligen är ett problem. |
| **[Kubernetes](functions-kubernetes-keda.md)**  | Är beroende av KEDA-konfigurationen. Appar kan konfigureras så att de alltid körs och aldrig har kall start eller kon figurer ATS för att skalas till noll, vilket resulterar i kall start vid nya händelser. 

<sup>1</sup> vissa gränser för de olika App Service plan alternativen finns i [App Service plan gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="service-limits"></a>Tjänstbegränsningar

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]

### <a name="networking-features"></a>Nätverksfunktioner

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

### <a name="billing"></a>Fakturering

| | | 
| --- | --- |
| **[Förbrukningsplan](#consumption-plan)** | Betala endast för den tid som dina funktioner körs. Fakturering baseras på antalet körningar, körningstid och använt minne. |
| **[Premiumplan](#premium-plan)** | Premium-planen baseras på antalet kärn sekunder och minne som används för alla nödvändiga och förvärmade instanser. Minst en instans per plan måste alltid vara varm. Den här planen ger mer förutsägbar prissättning. |
| **[Dedikerad plan](#app-service-plan)**<sup>1</sup> | Du betalar samma för functions-appar i en App Service planera precis som för andra App Service resurser, t. ex. Web Apps.|
| **[ASE](#app-service-plan)**<sup>1</sup> | Det finns en fast månads avgift för en ASE som betalar för infrastrukturen och som inte ändras med ASE storlek. Det finns dessutom en kostnad per App Service plan vCPU. Alla appar som har en ASE som värd finns i SKU med isolerad prissättning. |
| **[Kubernetes](functions-kubernetes-keda.md)**| Du betalar endast kostnaderna för ditt Kubernetes-kluster. ingen ytterligare fakturering för functions. Din Function-App körs som en program arbets belastning ovanpå klustret, precis som en vanlig app. |

<sup>1</sup> vissa gränser för de olika App Service plan alternativen finns i [App Service plan gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

## <a name="next-steps"></a>Nästa steg

+ [Snabb start: skapa ett Azure Functions projekt med Visual Studio Code](functions-create-first-function-vs-code.md)
+ [Distributions tekniker i Azure Functions](functions-deployment-technologies.md) 
+ [Utvecklarguide för Azure Functions](functions-reference.md)
