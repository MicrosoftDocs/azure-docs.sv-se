---
title: Azure Monitor för befintliga Operations Manager-kunder
description: Vägledning för befintliga användare av Operations Manager för över gång övervakning av vissa arbets belastningar som ska Azure Monitor som en del av en över gång till molnet.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2021
ms.openlocfilehash: e9f0a285df6013334970b971e46079b9e78b19cb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101728968"
---
# <a name="azure-monitor-for-existing-operations-manager-customers"></a>Azure Monitor för befintliga Operations Manager-kunder
Den här artikeln innehåller vägledning för kunder som för närvarande använder [System Center Operations Manager](/system-center/scom/welcome) och som planerar en över gång till [Azure Monitor](overview.md) när de migrerar affärs program och andra resurser till Azure. Det förutsätter att ditt slutgiltiga mål är en fullständig över gång till molnet, vilket ersätter så mycket Operations Manager funktioner som möjligt med Azure Monitor, utan att kompromissa med dina verksamhets-och drift krav. 

De rekommendationer som görs i den här artikeln kommer att ändras som Azure Monitor och Operations Manager lägga till funktioner. Den grundläggande strategin är även konsekvent.

> [!IMPORTANT]
> Det finns en kostnad för att implementera flera Azure Monitor funktioner som beskrivs här, så du bör utvärdera värdet innan du distribuerar det i hela miljön.

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du redan använder [Operations Manager](/system-center/scom) och minst har en grundläggande förståelse för [Azure Monitor](overview.md). En fullständig jämförelse mellan de två finns i [Cloud Monitoring guide: Monitoring Platforms Overview](/azure/cloud-adoption-framework/manage/monitor/platform-overview). Artikeln innehåller information om olika funktions skillnader mellan till två som hjälper dig att förstå några av de rekommendationer som görs här. 


## <a name="general-strategy"></a>Allmän strategi
Det finns inga Migreringsverktyg för att konvertera till gångar från Operations Manager till Azure Monitor eftersom plattformarna är fundamentalt annorlunda. Din migrering kommer i stället att utgöra en [standard Azure Monitor implementering](deploy.md) medan du fortsätter att använda Operations Manager. När du anpassar Azure Monitor för att uppfylla dina krav för olika program och komponenter och eftersom det får fler funktioner, kan du börja dra tillbaka olika hanterings paket och agenter i Operations Manager.

Den allmänna strategin som rekommenderas i den här artikeln är samma som i [moln övervaknings guiden](/azure/cloud-adoption-framework/manage/monitor/), som rekommenderar en [hybrid moln övervaknings](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview#hybrid-cloud-monitoring) strategi som gör det möjligt att göra en gradvis över gång till molnet. Även om vissa funktioner kan överlappa, kan du använda den här strategin för att underhålla dina befintliga affärs processer när du blir mer bekant med den nya plattformen. Flytta bara bort från Operations Manager-funktioner som du kan ersätta med Azure Monitor. Med hjälp av flera övervaknings verktyg kan du lägga till komplexitet, men du kan dra nytta av Azure Monitors möjlighet att övervaka nästa generations moln arbets belastningar samtidigt som du behåller Operations Managers möjlighet att övervaka komponenter för serverprogram vara och infrastruktur som kan vara lokala eller i andra moln. 


## <a name="components-to-monitor"></a>Komponenter som ska övervakas
Den hjälper till att kategorisera de olika typerna av arbets belastningar som du behöver övervaka för att fastställa en tydlig övervaknings strategi för varje. [Övervaknings guide för molnet: formulera en övervaknings strategi](/azure/cloud-adoption-framework/strategy/monitoring-strategy#high-level-modeling) ger en detaljerad analys av de olika skikt i din miljö som behöver övervakas när du går vidare med äldre företags program till moderna program i molnet.

Innan molnet använde du Operations Manager för att övervaka alla lager. När du startar över gången med IaaS (Infrastructure as a Service) fortsätter du att använda Operations Manager för dina virtuella datorer, men börjar använda Azure Monitor för dina moln resurser. När du senare övergår till moderna program med PaaS (Platform as a Service) kan du fokusera mer på Azure Monitor och börja dra Operations Manager-funktionen.


![Moln modeller](/azure/cloud-adoption-framework/strategy/media/monitoring-strategy/cloud-models.png)

Dessa lager kan för enklas i följande kategorier, som beskrivs i resten av den här artikeln. Alla övervaknings arbets belastningar i din miljö kanske inte passar in i någon av dessa kategorier, var och en bör vara tillräckligt nära en viss kategori för att de allmänna rekommendationerna ska gälla.

**Affärs program.** Program som tillhandahåller funktioner som är specifika för ditt företag. De kan vara interna eller externa och utvecklas ofta internt med anpassad kod. Dina äldre program finns vanligt vis på virtuella eller fysiska datorer som kör Windows eller Linux, medan de nyare programmen kommer att baseras på program tjänster i Azure, till exempel Azure Web Apps och Azure Functions.

**Azure-tjänster.** Resurser i Azure som stöder dina affärs program som har migrerats till molnet. Detta inkluderar tjänster som Azure Storage, Azure SQL och Azure IoT. Detta inkluderar även virtuella Azure-datorer eftersom de övervakas som andra Azure-tjänster, men program och program vara som körs på gäst operativ systemet på de virtuella datorerna kräver mer övervakning utöver värden.

**Serverprogram vara.** Program vara som körs på virtuella och fysiska datorer som har stöd för affärs program eller paketerade program som ger dig allmänna funktioner till din verksamhet. Exempel är Internet Information Server (IIS), SQL Server, Exchange och SharePoint. Detta omfattar även operativ systemet Windows eller Linux på dina virtuella och fysiska datorer.

**Lokal infrastruktur.** Komponenter som är speciella för din lokala miljö som kräver övervakning. Detta inkluderar sådana resurser som fysiska servrar, lagring och nätverks komponenter. Det här är de komponenter som virtualiseras när du flyttar till molnet.

## <a name="sample-walkthrough"></a>Exempelgenomgång
Följande är en hypotetisk genom gång av en migrering från Operations Manager till Azure Monitor. Detta är inte avsett att representera den fullständiga komplexiteten för en faktisk migrering, men det gör minst de grundläggande stegen och sekvensen. I avsnitten nedan beskrivs var och en av de här stegen i detalj.

Miljön innan du flyttar några komponenter till Azure baseras på virtuella och fysiska datorer som finns lokalt eller med en hanterad tjänst leverantör. Den förlitar sig på Operations Manager för att övervaka företags program, serverprogram och andra infrastruktur komponenter i din miljö, till exempel fysiska servrar och nätverk. Du använder standard hanterings paket för serverprogram vara som IIS, SQL Server och olika leverantörs program vara och du finjusterar dessa hanterings paket för dina behov. Du kan skapa anpassade hanterings paket för dina affärs program och andra komponenter som inte kan övervakas med befintliga hanterings paket och konfigurera Operations Manager att stödja dina affärs processer.

Din migrering till Azure börjar med IaaS och flyttar virtuella datorer som stöder affärs program till Azure. Övervaknings kraven för dessa program och serverprogram varan är beroende av ändra inte och du fortsätter att använda Operations Manager på dessa servrar med dina befintliga hanterings paket. 

Azure Monitor aktive ras för dina Azure-tjänster så snart du skapar en Azure-prenumeration. Den samlar automatiskt in plattforms mått och aktivitets loggen och du konfigurerar resurs loggar som ska samlas in så att du kan analysera all tillgänglig telemetri interaktivt med hjälp av logg frågor. Du kan använda VM Insights på dina virtuella datorer för att analysera övervaknings data i hela miljön och identifiera relationer mellan datorer och processer. Du kan utöka din användning av Azure Monitor till dina lokala fysiska och virtuella datorer genom att aktivera Azure Arc-aktiverade servrar. 

Du aktiverar Application Insights för var och en av dina affärs program. Den identifierar de olika komponenterna i varje program, börjar samla in användnings-och prestanda data och identifierar eventuella fel som inträffar i koden. Du skapar tillgänglighets test för att proaktivt testa dina externa program och varna dig för eventuella prestanda-och tillgänglighets problem. Även om Application Insights ger dig kraftfulla funktioner som du inte har i Operations Manager, fortsätter du att förlita dig på anpassade hanterings paket som du har utvecklat för dina affärs program eftersom de innehåller övervaknings scenarier som ännu inte omfattas av Azure Monitor. 

När du får bekanta dig med Azure Monitor börjar du med att skapa aviserings regler som kan ersätta vissa hanterings paket funktioner och börja utveckla dina affärs processer så att de använder den nya övervaknings plattformen. På så sätt kan du börja ta bort datorer och hanterings paket från Operations Manager hanterings gruppen. Du fortsätter att använda hanterings paket för kritisk serverprogram vara och lokal infrastruktur men fortsätter att titta efter nya funktioner i Azure Monitor som gör att du kan dra tillbaka ytterligare funktioner.

## <a name="monitor-azure-services"></a>Övervaka Azure-tjänster
Azure-tjänster kräver faktiskt Azure Monitor för att samla in telemetri, och den är aktive rad så snart du skapar en Azure-prenumeration. [Aktivitets loggen](essentials/activity-log.md) samlas in automatiskt för prenumerationen och [plattforms måtten](essentials/data-platform-metrics.md) samlas in automatiskt från alla Azure-resurser som du skapar. Du kan börja använda [Metrics Explorer](essentials/metrics-getting-started.md)direkt, som liknar prestanda vyer i drift konsolen, men den innehåller interaktiva analyser och [avancerade agg regeringar](essentials/metrics-charts.md) för data. [Skapa en mått avisering](alerts/alerts-metric.md) för att få ett meddelande när ett värde korsar ett tröskelvärde eller [lägga till ett diagram i en Azure-instrumentpanel](essentials/metrics-charts.md#pinning-to-dashboards) för synlighet.

[![Måttutforskare](media/azure-monitor-operations-manager/metrics-explorer.png)](media/azure-monitor-operations-manager/metrics-explorer.png#lightbox)

[Skapa en diagnostisk inställning](essentials/diagnostic-settings.md) för varje Azure-resurs för att skicka Mät värden och [resurs loggar](essentials/resource-logs.md)som innehåller information om den interna åtgärden för varje resurs till en Log Analytics-arbetsyta. Detta ger dig all tillgänglig telemetri för dina resurser och gör att du kan använda [Log Analytics](logs/log-analytics-overview.md) för att interaktivt analysera logg-och prestanda data med hjälp av ett avancerat frågespråk som inte har någon motsvarighet i Operations Manager. Du kan också skapa [logg frågas aviseringar](alerts/alerts-log-query.md)som kan använda komplex logik för att fastställa aviserings villkor och korrelera data över flera resurser.

[![Loggar analys](media/azure-monitor-operations-manager/log-analytics.png)](media/azure-monitor-operations-manager/log-analytics.png#lightbox)

[Insikter](monitor-reference.md) i Azure Monitor liknar hanterings paket på så sätt att de tillhandahåller unik övervakning för en viss Azure-tjänst. Insikter är för närvarande tillgängliga för flera tjänster, inklusive nätverk, lagring och behållare, och andra kommer att läggas till kontinuerligt.

[![Exempel på insikter](media/azure-monitor-operations-manager/insight.png)](media/azure-monitor-operations-manager/insight.png#lightbox)


Insikter baseras på [arbets böcker](visualize/workbooks-overview.md) i Azure Monitor, som kombinerar mått och logg frågor till interaktiva interaktiva rapporter. Skapa egna arbets böcker för att kombinera data från flera tjänster på liknande sätt som du kan skapa anpassade vyer och rapporter i drift konsolen.

### <a name="azure-management-pack"></a>Hanterings paket för Azure
Med [Azures hanterings paket](https://www.microsoft.com/download/details.aspx?id=50013) kan Operations Manager identifiera Azure-resurser och övervaka deras hälsa baserat på en viss uppsättning övervaknings scenarier. Det här hanterings paketet kräver att du utför ytterligare konfiguration för varje resurs i Azure, men det kan vara bra att ge en viss synlighet för dina Azure-resurser i drift konsolen tills du har gjort det möjligt för dina affärs processer att fokusera på Azure Monitor.

[![Hanterings paket för Azure](media/azure-monitor-operations-manager/operations-console.png)](media/azure-monitor-operations-manager/operations-console.png#lightbox)

 Du kan välja att använda Azures hanterings paket om du vill ha synlighet för vissa Azure-resurser i drift konsolen och integrera några grundläggande aviseringar med dina befintliga processer. Den använder faktiskt data som samlas in av Azure Monitor. Du bör titta på Azure Monitor om du vill ha långsiktig fullständig övervakning av dina Azure-resurser. 


## <a name="monitor-server-software-and-local-infrastructure"></a>Övervaka serverprogram vara och lokal infrastruktur
När du flyttar datorer till molnet ändras inte övervaknings kraven för program varan. Du behöver inte längre övervaka deras fysiska komponenter eftersom de är virtualiserade, men gäst operativ systemet och dess arbets belastningar har samma krav oavsett miljö.

[VM Insights](vm/vminsights-overview.md) är den primära funktionen i Azure Monitor för övervakning av virtuella datorer och deras gäst operativ system och arbets belastningar. På samma sätt som Operations Manager, använder VM Insights en agent för att samla in data från gäst operativ systemet för virtuella datorer. Detta är samma prestanda-och händelse data som vanligt vis används av hanterings paket för analys och avisering. Det finns inte befintliga regler, till exempel för att identifiera och Varna vid problem för affärs program och serverprogram som körs på dessa datorer. Du måste skapa egna aviserings regler för att proaktivt meddela eventuella identifierade problem.

[![Prestanda för VM Insights](media/azure-monitor-operations-manager/vm-insights-performance.png)](media/azure-monitor-operations-manager/vm-insights-performance.png#lightbox)

Azure Monitor mäter inte heller hälsan för olika program och tjänster som körs på en virtuell dator. Mått aviseringar kan automatiskt lösas när ett värde sjunker under ett tröskelvärde, men Azure Monitor inte för närvarande har möjlighet att definiera hälso kriterier för program och tjänster som körs på datorn, och inte heller tillhandahålla hälso sammanslagning för att gruppera hälso tillståndet för relaterade komponenter.

> [!NOTE]
> En ny [gäst hälso funktion för VM-insikter](vm/vminsights-health-overview.md) finns nu i en offentlig för hands version och avisering baserat på hälso tillståndet för en uppsättning prestanda mått. Detta är inlednings vis begränsad till en viss uppsättning prestanda räknare som är relaterade till gäst operativ systemet, inte program eller andra arbets belastningar som körs på den virtuella datorn.
> 
> [![Gäst hälsa för VM Insights](media/azure-monitor-operations-manager/vm-insights-guest-health.png)](media/azure-monitor-operations-manager/vm-insights-guest-health.png#lightbox)

Övervakning av program varan på dina datorer i en hybrid miljö använder vanligt vis en kombination av VM-insikter och Operations Manager, beroende på kraven för varje dator och på din förfallo tid för att utveckla operativa processer kring Azure Monitor. Microsofts hanterings agent (kallas Log Analytics agent i Azure Monitor) används av båda plattformarna så att en enda dator kan övervakas samtidigt av båda.

> [!NOTE]
> I framtiden kommer VM Insights att övergå till [Azure Monitor Agent](agents/azure-monitor-agent-overview.md), som för närvarande finns i en offentlig för hands version. Den kommer att vara kompatibel med Microsoft Monitoring Agent, så att samma virtuella dator fortsätter att kunna övervakas av båda plattformarna.

Fortsätt att använda Operations Manager för funktioner som ännu inte kan tillhandahållas av Azure Monitor. Detta inkluderar hanterings paket för kritisk serverprogram vara som IIS, SQL Server eller Exchange. Du kan också ha anpassade hanterings paket som har utvecklats för lokal infrastruktur som inte kan nås med Azure Monitor. Fortsätt också att använda Operations Manager om det är nära integrerat i dina operativa processer tills du kan övergå till att göra dina tjänst åtgärder så att Azure Monitor och andra Azure-tjänster kan utöka eller ersätta. 

Använd Azure Monitor virtuella datorer för att förbättra din aktuella övervakning även om den inte omedelbart ersätter Operations Manager. Exempel på funktioner som är unika för Azure Monitor är följande:

- Identifiera och övervaka relationer mellan virtuella datorer och deras externa beroenden.
- Visa sammanställda prestanda data för flera virtuella datorer i interaktiva diagram och arbets böcker.
- Använd [logg frågor](logs/log-query-overview.md) för att interaktivt analysera telemetri från dina virtuella datorer med data från dina andra Azure-resurser.
- Skapa [logg aviserings regler](alerts/alerts-log-query.md) baserade på komplex logik över flera virtuella datorer.

[![Översikt över VM Insights](media/azure-monitor-operations-manager/vm-insights-map.png)](media/azure-monitor-operations-manager/vm-insights-map.png#lightbox)

Förutom Azure Virtual Machines kan VM Insights övervaka datorer lokalt och i andra moln med hjälp av [Azure Arc-aktiverade servrar](../azure-arc/servers/overview.md). Med ARC-aktiverade servrar kan du hantera dina Windows-och Linux-datorer utanför Azure, i företags nätverket eller annan moln leverantör som stämmer överens med hur du hanterar interna virtuella Azure-datorer.



## <a name="monitor-business-applications"></a>Övervaka företags program
Du behöver vanligt vis anpassade hanterings paket för att övervaka dina affärs program med Operations Manager, med hjälp av agenter som är installerade på varje virtuell dator. Application Insights i Azure Monitor övervakar webbaserade program oavsett om de är i Azure, andra moln eller lokalt, så att de kan användas för alla dina program oavsett om de har migrerats till Azure.

Om övervakningen av ett affärs program är begränsad till funktioner som tillhandahålls av [prestanda mal len för .net-appar]() i Operations Manager, kan du förmodligen migrera till Application Insights utan att några funktioner går förlorade. I själva verket kommer Application Insights att inkludera ett stort antal ytterligare funktioner, inklusive följande:

- Identifiera och övervaka program komponenter automatiskt.
- Samla in detaljerade program användnings-och prestanda data, till exempel svars tid, felpriser och begär ande frekvens.
- Samla in webb läsar data, till exempel sidvyer och belastnings prestanda.
- Identifiera undantag och nedbrytning av stack spårning och relaterade begär Anden.
- Utföra avancerad analys med hjälp av funktioner som [Distributed tracing](app/distributed-tracing.md) och [Smart identifiering](app/proactive-diagnostics.md).
- Använd [Metrics Explorer](essentials/metrics-getting-started.md) för att interaktivt analysera prestanda data.
- Använd [logg frågor](logs/log-query-overview.md) för att interaktivt analysera insamlad telemetri tillsammans med data som samlas in för Azure-tjänster och VM-insikter.

[![Application Insights](media/azure-monitor-operations-manager/application-insights.png)](media/azure-monitor-operations-manager/application-insights.png#lightbox)

Det finns vissa scenarier där du kan behöva fortsätta använda Operations Manager förutom att Application Insights tills du har möjlighet att uppnå de funktioner som krävs. Exempel där du kan behöva fortsätta med Operations Manager innehåller följande:

-  [Tillgänglighets test](app/monitor-web-app-availability.md), som gör att du kan övervaka och varna för dina programs tillgänglighet och svars tider kräver inkommande begär Anden från IP-adresserna för webb test agenter. Om principen inte tillåter sådan åtkomst kan du behöva fortsätta att använda [tillgänglighets Övervakare för webb program](/system-center/scom/web-application-availability-monitoring-template) i Operations Manager.
- I Operations Manager kan du ange ett avsöknings intervall för tillgänglighets test, med många kunder som kontrollerar var 60-120: e sekund. Application Insights har ett minsta avsöknings intervall på 5 minuter som kan vara för långt för vissa kunder.
- En betydande mängd övervakning i Operations Manager utförs genom att samla in händelser som genererats av program och genom att köra skript på den lokala agenten. Dessa är standard alternativ i Application Insights, så du kan behöva anpassad verksamhet för att uppnå dina affärs behov. Detta kan inkludera anpassade aviserings regler med hjälp av händelse data som lagras i en Log Analytics arbets yta och skript som startas i en virtuell dators gäst med [hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md).
- Beroende på vilket språk ditt program har skrivits i, kan du vara begränsad till [Instrumentation som du kan använda med Application Insights](app/platforms.md).

Följ den grundläggande strategin i de andra avsnitten i den här hand boken och fortsätt att använda Operations Manager för dina affärs program, men dra nytta av ytterligare funktioner som tillhandahålls av Application Insights. När du har möjlighet att ersätta kritiska funktioner med Azure Monitor kan du börja dra tillbaka dina anpassade hanterings paket.


## <a name="next-steps"></a>Nästa steg

- I [övervaknings guiden för molnet](/azure/cloud-adoption-framework/manage/monitor/) finns en detaljerad jämförelse av Azure Monitor och System Center Operations Manager och mer information om hur du utformar och implementerar en hybrid övervaknings miljö.
- Läs mer om [övervakning av Azure-resurser i Azure Monitor](essentials/monitor-azure-resource.md).
- Läs mer om [övervakning av virtuella Azure-datorer i Azure Monitor](vm/monitor-vm-azure.md).
- Läs mer om [VM Insights](vm/vminsights-overview.md).
- Läs mer om [Application Insights](app/app-insights-overview.md).