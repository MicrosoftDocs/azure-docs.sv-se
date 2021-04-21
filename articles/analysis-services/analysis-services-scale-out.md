---
title: Azure Analysis Services utskalning| Microsoft Docs
description: Replikera Azure Analysis Services servrar med utskalning. Klientfrågor kan sedan distribueras mellan flera frågerepliker i en skalbar frågepool.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a2385cb811e322bd44daefa03d821b2ae47e0652
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769273"
---
# <a name="azure-analysis-services-scale-out"></a>Utskalning för Azure Analysis Services

Med utskalning kan klientfrågor distribueras  mellan flera frågerepliker i en frågepool, vilket minskar svarstiderna vid höga frågearbetsbelastningar. Du kan också separera bearbetningen från frågepoolen, vilket säkerställer att klientfrågor inte påverkas negativt av bearbetningsåtgärder. Utskalning kan konfigureras i Azure Portal eller med hjälp av Analysis Services REST API.

Utskalning är tillgängligt för servrar på prisnivån Standard. Varje frågereplik debiteras med samma pris som servern. Alla frågerepliker skapas i samma region som servern. Antalet frågerepliker som du kan konfigurera begränsas av den region där servern finns. Mer information finns i [Tillgänglighet efter region.](analysis-services-overview.md#availability-by-region) Utskalning ökar inte mängden tillgängligt minne för servern. Om du vill öka minnet måste du uppgradera din plan. 

## <a name="why-scale-out"></a>Varför skalar du ut?

I en typisk serverdistribution fungerar en server som både bearbetningsserver och frågeserver. Om antalet klientfrågor mot modeller på servern överskrider frågebearbetningsenheterna (QPU) för serverns plan, eller om modellbearbetningen sker samtidigt som höga frågearbetsbelastningar, kan prestandan minska. 

Med utskalning kan du skapa en frågepool med upp till sju ytterligare frågereplikresurser (åtta totalt, inklusive din *primära* server). Du kan skala antalet repliker i frågepoolen för att uppfylla QPU-kraven vid kritiska tidpunkter, och du kan när som helst separera en bearbetningsserver från frågepoolen. 

Oavsett hur många frågerepliker du har i en frågepool distribueras inte bearbetningsarbetsbelastningar mellan frågerepliker. Den primära servern fungerar som bearbetningsserver. Frågerepliker betjänar endast frågor mot modelldatabaserna som synkroniseras mellan den primära servern och varje replik i frågepoolen. 

Vid utskalning kan det ta upp till fem minuter innan nya frågerepliker läggs till stegvis i frågepoolen. När alla nya frågerepliker är igång belastningsutjämnas nya klientanslutningar mellan resurserna i frågepoolen. Befintliga klientanslutningar ändras inte från den resurs som de för närvarande är anslutna till. Vid inskalning avslutas alla befintliga klientanslutningar till en frågepoolresurs som tas bort från frågepoolen. Klienter kan återansluta till en återstående frågepoolresurs.

## <a name="how-it-works"></a>Så här fungerar det

När du konfigurerar utskalning första gången synkroniseras modelldatabaser på den primära servern automatiskt med nya repliker i en ny frågepool.  Automatisk synkronisering sker bara en gång. Under automatisk synkronisering kopieras den primära serverns datafiler (krypterade i vila i bloblagring) till en andra plats, som också krypteras i vila i bloblagring. Repliker i frågepoolen är *sedan sammanfrålade* med data från den andra uppsättningen filer. 

Även om en automatisk synkronisering endast utförs när du skalar ut en server för första gången, kan du också utföra en manuell synkronisering. Synkronisering säkerställer att data på repliker i frågepoolen matchar den primära serverns. När modeller bearbetas (uppdateras) på den primära servern måste en synkronisering utföras *när bearbetningen* har slutförts. Den här synkroniseringen kopierar uppdaterade data från den primära serverns filer i Blob Storage till den andra uppsättningen filer. Repliker i frågepoolen får sedan uppdaterade data från den andra uppsättningen filer i Blob Storage. 

När du utför en efterföljande utskalningsåtgärd, till exempel genom att öka antalet repliker i frågepoolen från två till fem, så kommer de nya replikerna att bli satta med data från den andra uppsättningen filer i bloblagringen. Det finns ingen synkronisering. Om du sedan utför en synkronisering efter utskalning skulle de nya replikerna i frågepoolen få två fel – en redundant extraering. När du utför en efterföljande utskalningsåtgärd är det viktigt att tänka på följande:

* Utför en synkronisering *före utskalningsåtgärden för att* undvika redundant extraering av de tillagda replikerna. Samtidiga synkroniserings- och utskalningsåtgärder som körs samtidigt tillåts inte.

* När du automatiserar *både* bearbetnings- och utskalningsåtgärder är det viktigt att först bearbeta data på den primära servern, sedan utföra en synkronisering och sedan utföra utskalningsåtgärden. Den här sekvensen garanterar minimal påverkan på QPU och minnesresurser.

* Under utskalningsåtgärder är alla servrar i frågepoolen, inklusive den primära servern, tillfälligt offline.

* Synkronisering tillåts även om det inte finns några repliker i frågepoolen. Om du skalar ut från noll till en eller flera repliker med nya data från en bearbetningsåtgärd på den primära servern utför du synkroniseringen först utan repliker i frågepoolen och skalar sedan ut. Om du synkroniserar innan du skalar ut undviker du redundant extra information om de nyligen tillagda replikerna.

* När du tar bort en modelldatabas från den primära servern tas den inte bort automatiskt från repliker i frågepoolen. Du måste utföra en synkroniseringsåtgärd med hjälp av [PowerShell-kommandot Sync-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) som tar bort filen/s för databasen från replikens delade bloblagringsplats och sedan tar bort modelldatabasen på replikerna i frågepoolen. Om du vill ta reda på om en modelldatabas finns på repliker i frågepoolen men inte på den primära servern ser du till att inställningen Separera bearbetningsservern från frågepoolen är **ja.**  Använd sedan SSMS för att ansluta till den primära servern med `:rw` kvalificeraren för att se om databasen finns. Anslut sedan till repliker i frågepoolen genom att ansluta utan kvalificeraren för `:rw` att se om samma databas också finns. Om databasen finns på repliker i frågepoolen men inte på den primära servern kör du en synkroniseringsåtgärd.   

* När du byter namn på en databas på den primära servern krävs ytterligare ett steg för att säkerställa att databasen är korrekt synkroniserad till alla repliker. Efter namnbytet utför du en synkronisering med hjälp av kommandot [Sync-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) och anger `-Database` parametern med det gamla databasnamnet. Den här synkroniseringen tar bort databasen och filerna med det gamla namnet från alla repliker. Utför sedan en annan synkronisering och ange `-Database` parametern med det nya databasnamnet. Den andra synkroniseringen kopierar den nyligen namngivna databasen till den andra uppsättningen filer och replikerar alla repliker. Dessa synkroniseringar kan inte utföras med hjälp av kommandot Synkronisera modell i portalen.

### <a name="synchronization-mode"></a>Synkroniseringsläge

Som standard rehydreras frågerepliker i sin helhet, inte inkrementellt. Återuppvädrning sker i steg. De kopplas från och bifogas två i taget (förutsatt att det finns minst tre repliker) för att se till att minst en replik hålls online för frågor vid en given tidpunkt. I vissa fall kan klienter behöva återansluta till en av onlinereplikerna medan den här processen pågår. Med inställningen **ReplicaSyncMode** kan du nu ange att synkroniseringen av frågerepliken ska ske parallellt. Parallell synkronisering ger följande fördelar: 

- Betydande minskning av synkroniseringstiden. 
- Data mellan repliker är mer sannolika att vara konsekventa under synkroniseringsprocessen. 
- Eftersom databaser hålls online på alla repliker under synkroniseringsprocessen behöver klienterna inte återansluta. 
- Cacheminnet i minnet uppdateras inkrementellt med endast ändrade data, vilket kan gå snabbare än att fullständigt återskapa modellen. 

#### <a name="setting-replicasyncmode"></a>Ställa in ReplicaSyncMode

Använd SSMS för att ange ReplicaSyncMode i Avancerade egenskaper. Möjliga värden är: 

- `1` (standard): Fullständig replikering av databas i steg (inkrementell). 
- `2`: Optimerad synkronisering parallellt. 

![Inställningen RelicaSyncMode](media/analysis-services-scale-out/aas-scale-out-sync-mode.png)

När du **ställer in ReplicaSyncMode=2** kan ytterligare minne förbrukas av frågereplikerna beroende på hur mycket av cacheminnet som behöver uppdateras. För att hålla databasen online och tillgänglig för frågor, beroende på hur mycket  av data som har ändrats, kan åtgärden kräva upp till dubbelt så mycket minne på repliken eftersom både gamla och nya segment lagras i minnet samtidigt. Repliknoder har samma minnesallokering som den primära noden och det finns normalt extra minne på den primära noden för uppdateringsåtgärder, så det kan vara osannolikt att replikerna skulle få slut på minne. Dessutom är det vanliga scenariot att databasen uppdateras inkrementellt på den primära noden, och därför bör kravet på dubbelt minne vara ovanligt. Om synkroniseringsåtgärden stöter på ett fel om att minnet är slut försöker den igen med standardtekniken (koppla/koppla från två i taget). 

### <a name="separate-processing-from-query-pool"></a>Separat bearbetning från frågepool

För maximal prestanda för både bearbetnings- och frågeåtgärder kan du välja att separera bearbetningsservern från frågepoolen. När de är avgränsade tilldelas nya klientanslutningar endast till frågerepliker i frågepoolen. Om bearbetningsåtgärder bara tar en kort stund kan du välja att separera bearbetningsservern från frågepoolen under den tid det tar att utföra bearbetnings- och synkroniseringsåtgärder och sedan inkludera den i frågepoolen igen. När du separerar bearbetningsservern från frågepoolen eller lägger tillbaka den i frågepoolen kan det ta upp till fem minuter för åtgärden att slutföras.

## <a name="monitor-qpu-usage"></a>Övervaka QPU-användning

Du kan avgöra om du behöver skala ut för servern genom [att övervaka servern](analysis-services-monitor.md) i Azure Portal med hjälp av mått. Om din QPU regelbundet överskrider maxgränsen innebär det att antalet frågor mot dina modeller överskrider QPU-gränsen för din plan. Måttet Kölängd för frågepoolsjobb ökar också när antalet frågor i kön för frågetrådpoolen överskrider den tillgängliga QPU:n. 

Ett annat bra mått att titta på är genomsnittlig QPU efter ServerResourceType. Det här måttet jämför genomsnittlig QPU för den primära servern med frågepoolen. 

![Utskalningsmått för frågor](media/analysis-services-scale-out/aas-scale-out-monitor.png)

**Så här konfigurerar du QPU av ServerResourceType**

1. I ett linjediagram för mått klickar du på Lägg **till mått.** 
2. I **RESURS** väljer du din server. I **MÅTTNAMNRYMD** väljer du **Analysis Services standardmått.** I MÅTT väljer du sedan **QPU** och sedan I **SAMMANSÄTTNING** väljer du **Genomsnitt.**  
3. Klicka **på Tillämpa delning.** 
4. I **VÄRDEN** väljer du **ServerResourceType**.  

### <a name="detailed-diagnostic-logging"></a>Detaljerad diagnostisk loggning

Använd Azure Monitor loggar för mer detaljerad diagnostik av utskalade serverresurser. Med loggar kan du använda Log Analytics-frågor för att dela upp QPU och minne per server och replik. Mer information finns i exempelfrågor i Analysis Services [diagnostikloggning](analysis-services-logging.md#example-queries).


## <a name="configure-scale-out"></a>Konfigurera utskalning

### <a name="in-azure-portal"></a>I Azure Portal

1. I portalen klickar du på **Skala ut**. Använd skjutreglaget för att välja antalet frågereplikservrar. Antalet repliker som du väljer är utöver din befintliga server.  

2. I **Separera bearbetningsservern från frågepoolen väljer du** Ja för att undanta bearbetningsservern från frågeservrarna. [Klientanslutningar](#connections) som använder standardanslutningssträngen `:rw` (utan ) omdirigeras till repliker i frågepoolen. 

   ![Skjutreglage för utskalning](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Klicka **på Spara** för att etablera dina nya frågereplikservrar. 

När du konfigurerar utskalning för en server första gången synkroniseras modeller på den primära servern automatiskt med repliker i frågepoolen. Automatisk synkronisering sker bara en gång, när du först konfigurerar utskalning till en eller flera repliker. Efterföljande ändringar av antalet repliker på samma server utlöser *inte någon annan automatisk synkronisering.* Automatisk synkronisering sker inte igen även om du ställer in servern på noll repliker och sedan skalar ut till val annat antal repliker. 

## <a name="synchronize"></a>Synkronisera 

Synkroniseringsåtgärder måste utföras manuellt eller med hjälp av REST API.

### <a name="in-azure-portal"></a>I Azure Portal

I **Översikt** > modell > **Synkronisera modell**.

![Ikonen Synkronisera](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST-API

Använd **synkroniseringsåtgärden.**

#### <a name="synchronize-a-model"></a>Synkronisera en modell   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Hämta synkroniseringsstatus  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Returstatuskoder:


|Kod  |Beskrivning  |
|---------|---------|
|-1     |  Ogiltig       |
|0     | Replikering        |
|1     |  Rehydrating       |
|2     |   Slutförd       |
|3     |   Misslyckad      |
|4     |    Slutföra     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Innan du använder PowerShell [ska du installera eller uppdatera den Azure PowerShell modulen](/powershell/azure/install-az-ps). 

Om du vill köra synkronisering [använder du Sync-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

Om du vill ange antalet frågerepliker använder [du Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver). Ange den valfria `-ReadonlyReplicaCount` parametern.

Om du vill separera bearbetningsservern från frågepoolen använder [du Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver). Ange den valfria `-DefaultConnectionMode` parametern för att använda `Readonly` .

Mer information finns i [Använda ett huvudnamn för tjänsten med modulen Az.AnalysisServices.](analysis-services-service-principal.md#azmodule)

## <a name="connections"></a>Anslutningar

På serverns översiktssida finns det två servernamn. Om du ännu inte har konfigurerat utskalning för en server fungerar båda servernamnen på samma sätt. När du har konfigurerat utskalning för en server måste du ange rätt servernamn beroende på anslutningstypen. 

För klientanslutningar för slutanvändare som Power BI Desktop, Excel och anpassade appar använder du **Servernamn**. 

För SSMS, Visual Studio och anslutningssträngar i PowerShell, Azure Function-appar och AMO använder du **hanteringsservernamnet**. Hanteringsserverns namn innehåller en särskild `:rw` (skrivskyddade) kvalificerare. Alla bearbetningsåtgärder utförs på den (primära) hanteringsservern.

![Servernamn](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="scale-up-scale-down-vs-scale-out"></a>Skala upp, skala ned eller skala ut

Du kan ändra prisnivån på en server med flera repliker. Samma prisnivå gäller för alla repliker. En skalningsåtgärd hämtar först alla repliker samtidigt och sedan alla repliker på den nya prisnivån.

## <a name="troubleshoot"></a>Felsöka

**Problem:** Användare får felet **Det går inte att hitta \<Name of the server> serverns instans i anslutningsläget "ReadOnly".**

**Lösning:** När du väljer **alternativet Separera** bearbetningsservern från frågepoolen omdirigeras klientanslutningar som använder standardanslutningssträngen (utan ) till `:rw` frågepoolrepliker. Om repliker i frågepoolen inte är online än på grund av att synkroniseringen ännu inte har slutförts kan omdirigerade klientanslutningar misslyckas. För att förhindra misslyckade anslutningar måste det finnas minst två servrar i frågepoolen när du utför en synkronisering. Varje server synkroniseras individuellt medan andra förblir online. Om du väljer att inte ha bearbetningsservern i frågepoolen under bearbetningen kan du välja att ta bort den från poolen för bearbetning och sedan lägga till den i poolen igen när bearbetningen är klar, men före synkroniseringen. Använd minnes- och QPU-mått för att övervaka synkroniseringsstatus.



## <a name="related-information"></a>Relaterad information

[Övervaka servermått](analysis-services-monitor.md)   
[Hantera Azure Analysis Services](analysis-services-manage.md)
