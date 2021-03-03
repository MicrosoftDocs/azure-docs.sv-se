---
title: Övervaka integration runtime i Azure Data Factory
description: Lär dig hur du övervakar olika typer av integration runtime i Azure Data Factory.
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/11/2020
author: dcstwh
ms.author: weetok
ms.openlocfilehash: 1cb4fcaa51e1a59ee9d09eb178faf9b250173709
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740041"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Övervaka en integreringskörning i Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]
  
**Integration runtime** är den beräknings infrastruktur som används av Azure Data Factory (ADF) för att tillhandahålla olika funktioner för data integrering i olika nätverks miljöer. Det finns tre typer av integrerings körningar som erbjuds av Data Factory:

- Azure Integration Runtime
- Integration Runtime med egen värd
- Integrerings körning för Azure-SQL Server Integration Services (SSIS)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Kör följande PowerShell-kommando för att hämta statusen för en instans av integration Runtime (IR): 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

Cmdleten returnerar annan information för olika typer av integration Runtime. I den här artikeln beskrivs egenskaper och status för varje typ av integration Runtime.  

## <a name="azure-integration-runtime"></a>Azure Integration Runtime

Beräknings resursen för en Azure integration runtime hanteras helt elastiskt i Azure. Följande tabell innehåller beskrivningar av egenskaper som returneras av kommandot **Get-AzDataFactoryV2IntegrationRuntime** :

### <a name="properties"></a>Egenskaper

Följande tabell innehåller beskrivningar av egenskaper som returneras av cmdleten för en Azure integration Runtime:

| Egenskap | Beskrivning |
-------- | ------------- | 
| Namn | Namnet på Azure integration Runtime. |  
| Stat | Status för Azure integration Runtime. | 
| Location | Platsen för Azure integration Runtime. Mer information om platsen för en Azure integration runtime finns i [Introduktion till integration runtime](concepts-integration-runtime.md). |
| DataFactoryName | Namnet på den data fabrik som Azure integration runtime tillhör. | 
| ResourceGroupName | Namnet på den resurs grupp som data fabriken tillhör.  |
| Beskrivning | Beskrivning av integrerings körningen.  |

### <a name="status"></a>Status

Följande tabell innehåller möjliga status värden för en Azure integration Runtime:

| Status | Kommentarer/scenarier | 
| ------ | ------------------ |
| Online | Azure integration runtime är online och redo att användas. | 
| Offline | Azure integration runtime är offline på grund av ett internt fel. |

## <a name="self-hosted-integration-runtime"></a>Integration Runtime med egen värd

Det här avsnittet innehåller beskrivningar av egenskaper som returneras av Get-AzDataFactoryV2IntegrationRuntime-cmdleten. 

> [!NOTE] 
> De returnerade egenskaperna och statusen innehåller information om den övergripande integrerings körningen med egen värd och varje nod i körnings miljön.  

### <a name="properties"></a>Egenskaper

Följande tabell innehåller beskrivningar av övervaknings egenskaper för **varje nod**:

| Egenskap | Beskrivning | 
| -------- | ----------- | 
| Namn | Namnet på den egna värdbaserade integrerings körningen och noder som är kopplade till den. Node är en lokal Windows-dator som har den lokala integrerings körningen installerad. |  
| Status | Status för den övergripande integrerings körningen med egen värd och varje nod. Exempel: online/offline/begränsat/osv. Information om dessa statusar finns i nästa avsnitt. | 
| Version | Versionen av integration runtime med egen värd och varje nod. Versionen av den lokala integration runtime-versionen bestäms baserat på den version av majoriteten av noderna i gruppen. Om det finns noder med olika versioner i installations programmet för lokal installation av integration runtime fungerar bara noderna med samma versions nummer som den logiska integrerings körningen för egen värd. Andra är i begränsat läge och måste uppdateras manuellt (endast om automatisk uppdatering Miss lyckas). | 
| Tillgängligt minne | Tillgängligt minne på en egen värd för integration runtime-noden. Det här värdet är en nära real tids ögonblicks bild. | 
| CPU-användning | CPU-användning för en egen värd för integration runtime-noden. Det här värdet är en nära real tids ögonblicks bild. |
| Nätverk (in/ut) | Nätverks användning av en egen värd för integration runtime-noden. Det här värdet är en nära real tids ögonblicks bild. | 
| Samtidiga jobb (som körs/begränsas) | **Körs**. Antal jobb eller aktiviteter som körs på varje nod. Det här värdet är en nära real tids ögonblicks bild. <br/><br/>**Begränsa**. Gränsen visar maximalt antal samtidiga jobb för varje nod. Det här värdet definieras baserat på datorns storlek. Du kan öka gränsen för att skala upp samtidiga jobb körningar i avancerade scenarier, när aktiviteterna är tids gräns, även om processor, minne eller nätverk används. Den här funktionen är också tillgänglig med en lokal integration runtime med egen värd. |
| Roll | Det finns två typer av roller i en integration runtime med egen värd för flera noder – dispatcher och Worker. Alla noder är arbetare, vilket innebär att de kan användas för att köra jobb. Det finns bara en dispatcher-nod som används för att hämta aktiviteter/jobb från moln tjänster och skicka dem till olika arbetsnoder. Dispatcher-noden är också en arbetsnod. |

Vissa inställningar för egenskaperna är mer begripliga när det finns två eller fler noder i den egna värdbaserade integrerings körningen (det vill säga i ett scenario med skala ut).

#### <a name="concurrent-jobs-limit"></a>Gräns för samtidiga jobb

Standardvärdet för gränsen för samtidiga jobb anges baserat på datorns storlek. De faktorer som används för att beräkna detta värde beror på mängden RAM-minne och antalet processor kärnor för datorn. Så ju fler kärnor och mer minne, desto högre är standard gränsen för samtidiga jobb.

Du skalar ut genom att öka antalet noder. När du ökar antalet noder är gränsen för samtidiga jobb summan av gräns värdena för samtidiga jobb för alla tillgängliga noder.  Om du till exempel använder en nod för att köra maximalt tolv samtidiga jobb, kan du köra högst 48 samtidiga jobb genom att lägga till tre likartade noder (det vill säga 4 × 12). Vi rekommenderar att du bara ökar gränsen för samtidiga jobb när du ser låg resursanvändning med standardvärdena på varje nod.

Du kan åsidosätta det beräknade standardvärdet i Azure Portal. Välj författare > anslutningar > integrerings körningar > redigera > noder > ändra värdet för samtidiga jobb per nod. Du kan också använda PowerShell [Update-Azdatafactoryv2integrationruntimenode-](/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) kommandot.
  
### <a name="status-per-node"></a>Status (per nod)

Följande tabell innehåller möjliga status värden för en egen värd för integration runtime-noden:

| Status | Beskrivning |
| ------ | ------------------ | 
| Online | Noden är ansluten till Data Factory tjänsten. |
| Offline | Noden är offline. |
| Uppgradera | Noden uppdateras automatiskt. |
| Begränsad | På grund av ett anslutnings problem. Kan bero på HTTP-port 8060 problem, problem med Service Bus-anslutning eller problem med synkronisering av autentiseringsuppgifter. |
| Inaktiv | Noden har en annan konfiguration än konfigurationen av andra majoritets noder. |

En nod kan vara inaktiv när den inte kan ansluta till andra noder.

### <a name="status-overall-self-hosted-integration-runtime"></a>Status (övergripande integrerings körning med egen värd)

Följande tabell innehåller möjliga status värden för en lokal integration Runtime. Den här statusen beror på status för alla noder som tillhör körnings miljön. 

| Status | Beskrivning |
| ------ | ----------- | 
| Behöver registrering | Ingen nod har registrerats för den här integrerings körningen med egen värd. |
| Online | Alla noder är online. |
| Offline | Ingen nod är online. |
| Begränsad | Alla noder i den här integration runtime med egen värd är i felfritt tillstånd. Denna status är en varning om att vissa noder kan vara nere. Den här statusen kan bero på ett problem med synkronisering av autentiseringsuppgifter på dispatcher/arbetsnoden. |

Använd cmdleten **Get-AzDataFactoryV2IntegrationRuntimeMetric** för att hämta JSON-nyttolasten som innehåller de detaljerade egenskaperna för den egna värdbaserade integrerings körningen och deras värden för ögonblicks bilder under körningen av cmdleten.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName | ConvertTo-Json 
```

Exempel på utdata (förutsätter att det finns två noder kopplade till den här integrerings körningen med egen värd):

```json
{
    "IntegrationRuntimeName":  "<Name of your integration runtime>",
    "ResourceGroupName":  "<Resource Group Name>",
    "DataFactoryName":  "<Data Factory Name>",
    "Nodes":  [
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        },
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        }

    ]
} 
```

## <a name="azure-ssis-integration-runtime"></a>Azure SSIS Integration Runtime

Azure-SSIS IR är ett fullständigt hanterat kluster av virtuella Azure-datorer (virtuella datorer eller noder) som är dedikerade att köra dina SSIS-paket. Du kan anropa SSIS-paket körningar på Azure-SSIS IR att använda olika metoder, till exempel via Azure-aktiverade SQL Server Data Tools (SSDT), AzureDTExec kommando rads verktyg, T-SQL on SQL Server Management Studio (SSMS)/SQL Server Agent och köra SSIS-paket aktiviteter i ADF-pipeline. Azure-SSIS IR kör inga andra ADF-aktiviteter. När du har upprättat kan du övervaka dess övergripande/Node-/regionsspecifika egenskaper och status via Azure PowerShell, Azure Portal och Azure Monitor.

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-powershell"></a>Övervaka Azure-SSIS integration runtime med Azure PowerShell

Använd följande Azure PowerShell-cmdlet för att övervaka de övergripande/Node-/regionsspecifika egenskaperna och status för Azure-SSIS IR.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

#### <a name="properties"></a>Egenskaper

Följande tabell innehåller beskrivningar av egenskaper som returneras av ovanstående cmdlet för en Azure-SSIS IR.

| Egenskap/status              | Beskrivning                  |
| ---------------------------- | ---------------------------- |
| CreateTime                   | UTC-tiden när Azure-SSIS IR skapades. |
| Noder                        | De allokerade/tillgängliga noderna i din Azure-SSIS IR med Node-/regionsspecifika status (start/tillgänglighet/åter användning/ej tillgänglig) och åtgärds bara fel. |
| OtherErrors                  | De åtgärds bara fel som inte är noder i Azure-SSIS IR. |
| LastOperation                | Resultatet av den senaste start-/stopp åtgärden på Azure-SSIS IR med åtgärds bara fel om det Miss lyckas. |
| Stat                        | Övergripande status (första/starta/starta/stoppa/stoppas) för Azure-SSIS IR. |
| Location                     | Azure-SSIS IR plats. |
| NodeSize                     | Storleken på varje nod i Azure-SSIS IR. |
| NodeCount                    | Antalet noder i Azure-SSIS IR. |
| MaxParallelExecutionsPerNode | Det maximala antalet parallella körningar per nod i Azure-SSIS IR. |
| CatalogServerEndpoint        | Slut punkten för din befintliga Azure SQL Database-Server eller hanterade instans som värd för SSIS-katalogen (SSISDB). |
| CatalogAdminUserName         | Administratörens användar namn för din befintliga Azure SQL Database-Server eller en hanterad instans. ADF använder den här informationen för att förbereda och hantera SSISDB för din räkning. |
| CatalogAdminPassword         | Administratörs lösen ordet för din befintliga Azure SQL Database-Server eller en hanterad instans. |
| CatalogPricingTier           | Pris nivån för SSISDB som finns på Azure SQL Database Server.  Inte tillämpligt på värdbaserade SSISDB för Azure SQL-hanterad instans. |
| VNetId                       | Det virtuella nätverks resurs-ID: t för Azure-SSIS IR att ansluta till. |
| Undernät                       | Under näts namnet för Azure-SSIS IR att ansluta till. |
| ID                           | Resurs-ID för Azure-SSIS IR. |
| Typ                         | IR-typen (hanterad/egen värd) för din Azure-SSIS IR. |
| ResourceGroupName            | Namnet på din Azure-adressresurs där ADF och Azure-SSIS IR skapades. |
| DataFactoryName              | Namnet på din ADF. |
| Namn                         | Namnet på din Azure-SSIS IR. |
| Beskrivning                  | Beskrivning av din Azure-SSIS IR. |
  
#### <a name="status-per-azure-ssis-ir-node"></a>Status (per Azure-SSIS IR nod)

Följande tabell innehåller möjliga status värden för en Azure-SSIS IR-nod:

| Node-/regionsspecifika status | Beskrivning |
| -------------------- | ----------- | 
| Startar             | Den här noden förbereds. |
| Tillgänglig            | Den här noden är redo att distribuera/köra SSIS-paket. |
| Pappers            | Den här noden repareras/startas om. |
| Inte tillgänglig          | Den här noden kan inte användas för att distribuera/köra SSIS-paket och har åtgärds bara fel/problem som du kan lösa. |

#### <a name="status-overall-azure-ssis-ir"></a>Status (övergripande Azure-SSIS IR)

Följande tabell innehåller möjliga övergripande status för en Azure-SSIS IR. Den övergripande statusen i tur är beroende av de kombinerade statusarna för alla noder som tillhör Azure-SSIS IR. 

| Övergripande status | Beskrivning | 
| -------------- | ----------- | 
| Inledande        | Noderna i Azure-SSIS IR inte har allokerats/för berett. | 
| Startar       | Noderna i dina Azure-SSIS IR tilldelas/förbereds och faktureringen har påbörjats. |
| Börjat        | Noderna i Azure-SSIS IR har allokerats/för berett och är redo att distribuera/köra SSIS-paket. |
| Stoppas       | Noderna i dina Azure-SSIS IR håller på att släppas. |
| Stoppad        | Noderna i dina Azure-SSIS IR har släppts och faktureringen har stoppats. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-azure-portal"></a>Övervaka Azure-SSIS integration runtime i Azure Portal

Om du vill övervaka din Azure-SSIS IR i Azure Portal går du till sidan **integration Runtimes** i **Monitor** Hub på ADF UI, där du kan se alla integrerings körningar.

![Övervaka alla integrerings körningar](media/monitor-integration-runtime/monitor-integration-runtimes.png)

Välj sedan namnet på din Azure-SSIS IR för att öppna sidan övervakning där du kan se dess övergripande/Node-/regionsspecifika egenskaper och status. På den här sidan, beroende på hur du konfigurerar inställningarna allmänt, distribution och avancerad för din Azure-SSIS IR, hittar du olika informations-och funktions paneler.

Informations panelerna **typ** och **region** visar Azure-SSIS IRens typ och region.

I informations panelen för **Node-storlek** visas SKU (SSIS edition_VM tier_VM-serien), antal processor kärnor och storleken på RAM-minne per nod för din Azure-SSIS IR. 

Informations panelen för att **köra/begärda noder** jämför antalet noder som för närvarande körs på det totala antalet noder som tidigare begärts för din Azure-SSIS IR.

Informations panelen **med dubbelt växlings-/roll** information visar namnet på ditt dubbla vänte läges Azure-SSIS IR-par som fungerar i sync med Azure SQL Database/hanterade instanser av redundans för affärs kontinuitet och haveri beredskap (BCDR) och den aktuella primära/sekundära rollen för din Azure-SSIS IR. När SSISDB-redundansväxlingen inträffar byter ditt primära och sekundära Azure-SSIS-IRs roller (mer information finns i [Konfigurera din Azure-SSIS IR för BCDR](./configure-bcdr-azure-ssis-integration-runtime.md)).

De funktionella panelerna beskrivs i mer information nedan.

![Övervaka dina Azure-SSIS IR](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime.png)

#### <a name="status-tile"></a>STATUS panel

På **status** panelen på sidan Azure-SSIS IR övervakning kan du se dess övergripande status, till exempel **körs** eller **stoppas**. Om du väljer **körnings** status öppnas ett fönster med knappen för att **stoppa** din Azure-SSIS IR. Om du väljer **stoppad** status öppnas ett fönster med knappen Live **Start** för att starta din Azure-SSIS IR. Popup-fönstret innehåller också knappen **kör SSIS-paket** för att automatiskt generera en ADF-pipeline med kör SSIS-paket-aktivitet som körs på din Azure-SSIS IR (se [köra SSIS-paket som EXECUTE SSIS-paket aktiviteter i ADF-pipeliner](./how-to-invoke-ssis-package-ssis-activity.md)) och en text ruta för **resurs-ID** som du kan använda för att kopiera Azure-SSIS IR resurs-ID ( `/subscriptions/YourAzureSubscripton/resourcegroups/YourResourceGroup/providers/Microsoft.DataFactory/factories/YourADF/integrationruntimes/YourAzureSSISIR` ). Suffixet för Azure-SSIS IR resurs-ID som innehåller dina ADF-och Azure-SSIS IR-namn utgör ett kluster-ID som kan användas för att köpa ytterligare Premium/licensierade SSIS-komponenter från oberoende program varu leverantörer (ISV) och binda dem till din Azure-SSIS IR (se [Installera Premium/licensierade komponenter på din Azure-SSIS IR](./how-to-develop-azure-ssis-ir-licensed-components.md)).

![Övervaka din Azure-SSIS IR-STATUS panel](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-status.png)

#### <a name="ssisdb-server-endpoint-tile"></a>SSISDB SERVER-slutpunkt panel

Om du använder projekt distributions modell där paket lagras i SSISDB som finns på din Azure SQL Database-Server eller en hanterad instans, visas panelen **SSISDB Server Endpoint** på sidan för Azure-SSIS IR övervakning (se [konfigurera inställningarna för Azure-SSIS IR distribution](./tutorial-deploy-ssis-packages-azure.md#deployment-settings-page)). På den här panelen kan du välja en länk som anger din Azure SQL Database Server eller en hanterad instans för att öppna ett fönster, där du kan kopiera Server slut punkten från en text ruta och använda den när du ansluter från SSMS för att distribuera, konfigurera, köra och hantera dina paket. I popup-fönstret kan du också välja länken **Se Inställningar för Azure SQL Database eller hanterad instans** för att konfigurera om/ändra storlek på din SSISDB i Azure Portal.

![Övervaka din Azure-SSIS IR-SSISDB-panel](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-ssisdb.png)

#### <a name="proxy--staging-tile"></a>Panelen PROXY/mellanlagringsplats

Om du laddar ned, installerar och konfigurerar Self-Hosted IR (SHIR) som en proxy för din Azure-SSIS IR för att komma åt data lokalt, visas panelen **proxy/mellanlagringsplats** på sidan för Azure-SSIS IR övervakning (se [Konfigurera SHIR som proxy för dina Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md)). På den här panelen kan du välja en länk som anger din SHIR för att öppna sidan övervakning. Du kan också välja en annan länk som anger Azure-Blob Storage för mellanlagring för att konfigurera om den länkade tjänsten.

#### <a name="validate-vnet--subnet-tile"></a>VERIFIERA panelen VNET/SUBNET

Om du ansluter din Azure-SSIS IR till ett VNet visas panelen **Verifiera VNet-/undernät** på sidan för Azure-SSIS IR övervakning (se [koppla din Azure-SSIS IR till ett VNet](./join-azure-ssis-integration-runtime-virtual-network.md)). På den här panelen kan du välja en länk som anger ditt VNet och undernät för att öppna ett fönster, där du kan kopiera ditt VNet-resurs-ID ( `/subscriptions/YourAzureSubscripton/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworks/YourARMVNet` ) och under nätets namn från text rutor, samt verifiera dina VNet-och Subnet-konfigurationer för att säkerställa att nödvändiga inkommande/utgående nätverks trafik och hantering av Azure-SSIS IR inte blockeras.

![Övervaka din Azure-SSIS IR-VALIDATE-panel](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-validate.png)

#### <a name="diagnose-connectivity-tile"></a>Panelen diagnostisera anslutning

På panelen **diagnostisera anslutning** på sidan Azure-SSIS IR övervakning kan du välja länken **Testa anslutning** för att visa ett fönster, där du kan kontrol lera anslutningarna mellan din Azure-SSIS IR och relevanta paket/konfiguration/data lager, samt hanterings tjänster, via sitt fullständigt kvalificerade domän namn (FQDN)/IP-adress och avsedd port (se [testa anslutningar från din Azure-SSIS IR](./ssis-integration-runtime-diagnose-connectivity-faq.md)).

![Övervaka din Azure-SSIS IR-diagnosticera panel](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-diagnose.png)

#### <a name="static-public-ip-addresses-tile"></a>Panelen statiska offentliga IP-adresser

Om du använder egna statiska offentliga IP-adresser för Azure-SSIS IR visas panelen **statiska offentliga IP-adresser** på sidan för Azure-SSIS IR övervakning (se ta [med egna statiska IP-adresser för Azure-SSIS IR](./join-azure-ssis-integration-runtime-virtual-network.md#publicIP)). På den här panelen kan du välja länkar som anger dina första/andra statiska IP-adresser för Azure-SSIS IR för att öppna ett fönster, där du kan kopiera sitt resurs-ID ( `/subscriptions/YourAzureSubscripton/resourceGroups/YourResourceGroup/providers/Microsoft.Network/publicIPAddresses/YourPublicIPAddress` ) från en text ruta. I popup-fönstret kan du också välja länken **se din första/andra statiska offentliga IP-adress inställningar** för att hantera din första/andra statiska IP-adress i Azure Portal.

![Övervaka Azure-SSIS IR-statisk panel](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-static.png)

#### <a name="package-stores-tile"></a>Panel för paket arkiv

Om du använder paket distributions modell där paket lagras i fil systemet/Azure Files/SQL Server databas (MSDB) som hanteras av din Azure SQL-hanterade instans och hanteras via Azure-SSIS IR paket arkiv, visas panelen **paket arkiv** på din Azure-SSIS IR övervaknings sida (se [konfigurera inställningar för Azure-SSIS IR distribution](./tutorial-deploy-ssis-packages-azure.md#deployment-settings-page)). På den här panelen kan du välja en länk som anger hur många paket lager som är kopplade till din Azure-SSIS IR att popup-fönster visas, där du kan konfigurera om de relevanta länkade tjänsterna för dina Azure-SSIS IR-paket arkiv ovanpå fil systemet/Azure Files/MSDB som hanteras av din Azure SQL-hanterade instans.

![Övervaka din Azure-SSIS IR-paket-panel](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-package.png)

#### <a name="errors-tile"></a>FEL panel

Om det uppstår problem med att starta/stoppa/underhålla/uppgradera Azure-SSIS IR visas en panel med ytterligare **fel** på sidan för Azure-SSIS IR övervakning. På den här panelen kan du välja en länk som anger antalet fel som har genererats av din Azure-SSIS IR att visa ett fönster, där du kan se dessa fel i mer information och kopiera dem för att hitta de rekommenderade lösningarna i vår fel söknings guide (se [fel sökning av Azure-SSIS IR](./ssis-integration-runtime-management-troubleshoot.md)).

![Övervaka din Azure-SSIS IR – fel panel](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-error.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-monitor"></a>Övervaka Azure-SSIS integration runtime med Azure Monitor

Information om hur du övervakar Azure-SSIS IR med Azure Monitor finns i [övervaka SSIS-åtgärder med Azure Monitor](./monitor-using-azure-monitor.md#monitor-ssis-operations-with-azure-monitor).

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Mer information om Azure-SSIS integration runtime

I följande artiklar finns mer information om Azure-SSIS integration Runtime:

- [Azure-SSIS integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln innehåller konceptuell information om integrerings körningar i allmänhet, inklusive Azure-SSIS IR. 
- [Självstudie: distribuera SSIS-paket till Azure](./tutorial-deploy-ssis-packages-azure.md). Den här artikeln innehåller steg-för-steg-instruktioner för att skapa Azure-SSIS IR och använda Azure SQL Database som värd för SSIS-katalogen (SSISDB). 
- [Så här skapar du en Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Den här artikeln expanderar i självstudien och innehåller instruktioner om hur du använder Azure SQL-hanterad instans som värd för SSISDB. 
- [Hantera en Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Den här artikeln visar hur du startar, stoppar eller tar bort Azure-SSIS IR. Det visar också hur du skalar ut det genom att lägga till fler noder. 
- [Anslut Azure-SSIS IR till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md). Den här artikeln innehåller anvisningar om hur du ansluter till Azure-SSIS IR till ett virtuellt nätverk.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar för att övervaka pipelines på olika sätt: 

- [Snabb start: skapa en data fabrik](quickstart-create-data-factory-dot-net.md).
- [Använd Azure Monitor för att övervaka Data Factory pipelines](monitor-using-azure-monitor.md)
