---
title: Konfigurera prestanda för Azure-SSIS Integration Runtime
description: Lär dig hur du konfigurerar egenskaperna för Azure-SSIS Integration Runtime för hög prestanda
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 5d275100124660b901504b7e7f71cf93518fd077
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364400"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Konfigurera Azure-SSIS Integration Runtime för hög prestanda

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


I den här artikeln beskrivs hur du konfigurerar en Azure-SSIS Integration Runtime (IR) för hög prestanda. Med Azure-SSIS IR kan du distribuera och köra SQL Server Integration Services-paket (SSIS) i Azure. Mer information om Azure-SSIS IR finns i artikeln om [integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime) . Information om hur du distribuerar och kör SSIS-paket på Azure finns i [hiss-och shift SQL Server Integration Services-arbetsbelastningar till molnet](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Den här artikeln innehåller prestanda resultat och observationer från interna tester som gjorts av medlemmar i SSIS Development Team. Resultaten kan variera. Utför dina egna tester innan du slutför konfigurations inställningarna, vilket påverkar både kostnader och prestanda.

## <a name="properties-to-configure"></a>Egenskaper som ska konfigureras

Följande del av ett konfigurations skript visar de egenskaper som du kan konfigurera när du skapar en Azure-SSIS Integration Runtime. Fullständig PowerShell-skript och beskrivning finns i [distribuera SQL Server Integration Services-paket till Azure](tutorial-deploy-ssis-packages-azure-powershell.md).

```powershell
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your existing SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to max(2 x number of cores, 8) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/SQL Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your SQL Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your server name or managed instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for SQL Managed Instance]"
```

## <a name="azuressislocation"></a>AzureSSISLocation
**AzureSSISLocation** är platsen för integration runtime Worker-noden. Arbetsnoden underhåller en konstant anslutning till SSIS-SSISDB i Azure SQL Database. Ange **AzureSSISLocation** till samma plats som den [logiska SQL-Server](../azure-sql/database/logical-servers.md) som är värd för SSISDB, vilket gör att integrerings körningen fungerar så effektivt som möjligt.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
Data Factory, inklusive Azure-SSIS IR, stöder följande alternativ:
-   Standard \_ a4 \_ v2
-   Standard \_ a8 \_ v2
-   Standard \_ D1 \_ v2
-   Standard \_ D2 \_ v2
-   Standard \_ D3 \_ v2
-   Standard \_ D4 \_ v2
-   Standard \_ D2 \_ v3
-   Standard \_ D4 \_ v3
-   Standard \_ D8 \_ v3
-   Standard \_ D16 \_ v3
-   Standard \_ D32 \_ v3
-   Standard \_ D64 \_ v3
-   Standard \_ E2 \_ v3
-   Standard \_ E4 \_ v3
-   Standard \_ E8 \_ v3
-   Standard \_ E16 \_ v3
-   Standard \_ E32 \_ v3
-   Standard \_ E64 \_ v3

I det inofficiellt interna testet av SSIS Engineering-teamet verkar D-serien vara lämpligare för körning av SSIS-paket än en serie.

-   Prestanda-/pris förhållandet i D-serien är högre än en serie och prestanda-/pris förhållandet för v3-serien är högre än v2-serien.
-   Data flödet för D-serien är högre än en serie med samma pris och data flödet för v3-serien är högre än v2-serien till samma pris.
-   Noderna i v2-serien i Azure-SSIS IR lämpar sig inte för anpassad installation, så Använd v3-seriens noder i stället. Om du redan använder noderna i v2-serien växlar du till att använda v3-serierna så snart som möjligt.
-   E-serien är minnesoptimerade VM-storlekar som ger ett högre minne-till-CPU-förhållande än andra datorer. Om paketet kräver mycket minne kan du överväga att välja E-serien VM.

### <a name="configure-for-execution-speed"></a>Konfigurera för körnings hastighet
Om du inte har många paket att köra och du vill att paketen ska köras snabbt, använder du informationen i följande diagram för att välja en typ av virtuell dator som passar ditt scenario.

Den här informationen representerar ett enskilt paket som körs på en enskild arbetsnod. Paketet läser in 3 000 000 poster med kolumnerna förnamn och efter namn från Azure Blob Storage, genererar en kolumn med fullständigt namn och skriver poster som har det fullständiga namnet som är längre än 20 tecken till Azure Blob Storage.

Y-axeln är antalet paket som har slutfört körningen inom en timme. Observera att detta bara är ett test resultat av ett minnes krävande paket. Om du vill veta data flödet för ditt paket rekommenderar vi att du utför testet själv.

![Körnings hastighet för SSIS Integration Runtime-paket](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speedV2.png)

### <a name="configure-for-overall-throughput"></a>Konfigurera för övergripande data flöde

Om du har många paket att köra och du bryr dig om det totala data flödet, använder du informationen i följande diagram för att välja en typ av virtuell dator som passar ditt scenario.

Y-axeln är antalet paket som har slutfört körningen inom en timme. Observera att detta bara är ett test resultat av ett minnes krävande paket. Om du vill veta data flödet för ditt paket rekommenderar vi att du utför testet själv.

![SSIS Integration Runtime maximalt totalt data flöde](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughputV2.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** justerar skalbarheten för integration Runtime. Data flödet för integration runtime är proportionellt mot **AzureSSISNodeNumber**. Ange **AzureSSISNodeNumber** till ett litet värde först, övervaka data flödet för integrerings körningen och justera sedan värdet för ditt scenario. Information om hur du konfigurerar om antalet arbets noder finns i [hantera en Azure-SSIS integration runtime](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Om du redan använder en kraftfull arbetsnode för att köra paket kan du öka det totala data flödet för integrerings körningen genom att öka **AzureSSISMaxParallelExecutionsPerNode** . Om du vill öka Max värdet måste du använda Azure PowerShell för att uppdatera **AzureSSISMaxParallelExecutionsPerNode**. Du kan uppskatta lämpligt värde baserat på kostnaden för ditt paket och följande konfigurationer för arbetsnoderna. Mer information finns i [allmänna storlekar för virtuella datorer](../virtual-machines/sizes-general.md).

| Storlek             | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Maximalt antal datadiskar/dataflöde: IOPS | Maximalt antal nätverkskort/förväntade nätverksprestanda (Mbit/s) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standard \_ D1 \_ v2 | 1    | 3.5         | 50                     | 3 000 / 46 / 23                                             | 2 / 2 x 500                         | 2/750                                        |
| Standard \_ D2 \_ v2 | 2    | 7           | 100                    | 6 000 / 93 / 46                                             | 4 / 4 x 500                         | 2/1 500                                       |
| Standard \_ D3 \_ v2 | 4    | 14          | 200                    | 12 000 / 187 / 93                                           | 8 / 8 x 500                         | 4/3 000                                       |
| Standard \_ D4 \_ v2 | 8    | 28          | 400                    | 24 000 / 375 / 187                                          | 16 / 16 x 500                       | 8/6 000                                       |
| Standard \_ a4 \_ v2 | 4    | 8           | 40                     | 4 000 / 80 / 40                                             | 8 / 8 x 500                         | 4/1 000                                       |
| Standard \_ a8 \_ v2 | 8    | 16          | 80                     | 8 000 / 160 / 80                                            | 16 / 16 x 500                       | 8/2 000                                       |
| Standard \_ D2 \_ v3 | 2    | 8           | 50                     | 3 000 / 46 / 23                                             | 4/6x500                         | 2/1 000                                       |
| Standard \_ D4 \_ v3 | 4    | 16          | 100                    | 6 000 / 93 / 46                                             | 8/12x500                        | 2/2 000                                       |
| Standard \_ D8 \_ v3 | 8    | 32          | 200                    | 12 000 / 187 / 93                                           | 16/24x500                       | 4 / 4000                                       |
| Standard \_ D16 \_ v3| 16   | 64          | 400                    | 24 000 / 375 / 187                                          | 32/48x500                        | 8 / 8000                                       |
| Standard \_ D32 \_ v3| 32   | 128         | 800                    | 48 000 / 750 / 375                                          | 32/96x500                       | 8/16 000                                      |
| Standard \_ D64 \_ v3| 64   | 256         | 1600                   | 96000/1000/500                                         | 32/192x500                      | 8 / 30000                                      |
| Standard \_ E2 \_ v3 | 2    | 16          | 50                     | 3 000 / 46 / 23                                             | 4/6x500                         | 2/1 000                                       |
| Standard \_ E4 \_ v3 | 4    | 32          | 100                    | 6 000 / 93 / 46                                             | 8/12x500                        | 2/2 000                                       |
| Standard \_ E8 \_ v3 | 8    | 64          | 200                    | 12 000 / 187 / 93                                           | 16/24x500                       | 4 / 4000                                       |
| Standard \_ E16 \_ v3| 16   | 128         | 400                    | 24 000 / 375 / 187                                          | 32/48x500                       | 8 / 8000                                       |
| Standard \_ E32 \_ v3| 32   | 256         | 800                    | 48 000 / 750 / 375                                          | 32/96x500                       | 8/16 000                                      |
| Standard \_ E64 \_ v3| 64   | 432         | 1600                   | 96000/1000/500                                         | 32/192x500                      | 8 / 30000                                      |

Här följer rikt linjerna för att ange rätt värde för egenskapen **AzureSSISMaxParallelExecutionsPerNode** : 

1. Ange det som ett litet värde först.
2. Öka det med ett litet belopp för att kontrol lera om det totala data flödet har förbättrats.
3. Sluta öka värdet när det totala data flödet når det maximala värdet.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** är pris nivån för SSIS Catalog-databasen (SSISDB) på i Azure SQL Database. Den här inställningen påverkar det maximala antalet arbetare i IR-instansen, hastigheten för att köa en paket körning och hastigheten för att läsa in körnings loggen.

-   Om du inte bryr dig om hastigheten med att köa paket körningen och läsa in körnings loggen kan du välja den lägsta databas pris nivån. Azure SQL Database med Basic-prissättning stöder 8 arbetare i en integration runtime-instans.

-   Välj en kraftfullare databas än Basic om antalet anställda är mer än 8, eller om antalet kärnor är mer än 50. Annars blir databasen Flask halsen för integration runtime-instansen och den övergripande prestandan påverkas negativt.

-   Välj en mer kraftfull databas, till exempel S3, om loggnings nivån är inställd på verbose. Enligt vår inofficiella interna testning kan pris nivån S3 stödja SSIS-paket körning med 2 noder, 128 parallella räknare och utförlig loggnings nivå.

Du kan också justera pris nivån för databasen baserat på användnings informationen för DTU ( [databas transaktions enhet](../azure-sql/database/service-tiers-dtu.md) ) som är tillgänglig på Azure Portal.

## <a name="design-for-high-performance"></a>Design för hög prestanda
Att utforma ett SSIS-paket som ska köras på Azure skiljer sig från att utforma ett paket för lokal körning. I stället för att kombinera flera oberoende uppgifter i samma paket, separera dem till flera paket för mer effektiv körning i Azure-SSIS IR. Skapa en paket körning för varje paket, så att de inte behöver vänta på att de ska slutföras. Den här metoden ger fördelarna med skalbarheten för Azure-SSIS integration Runtime och förbättrar det totala data flödet.

## <a name="next-steps"></a>Nästa steg
Läs mer om Azure-SSIS Integration Runtime. Se [Azure-SSIS integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).