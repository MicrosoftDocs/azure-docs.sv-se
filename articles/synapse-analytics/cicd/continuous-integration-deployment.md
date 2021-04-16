---
title: Kontinuerlig integrering och leverans för Synapse-arbetsyta
description: Lär dig hur du använder kontinuerlig integrering och leverans för att distribuera ändringar i arbetsytan från en miljö (utveckling, testning, produktion) till en annan.
author: liud
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 5f68e3698f8616b581d319bc19d2a8c636c79c36
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566094"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Kontinuerlig integrering och leverans för Azure Synapse arbetsyta

## <a name="overview"></a>Översikt

Kontinuerlig integrering (CI) är en process där man automatiserar komningen och testningen av kod varje gång en teammedlem genomför ändringar i versionskontroll. Kontinuerlig distribution (CD) är en process för att skapa, testa, konfigurera och distribuera från flera test- eller mellanlagringsmiljöer till en produktionsmiljö.

För Azure Synapse arbetsyta flyttar kontinuerlig integrering och leverans (CI/CD) alla entiteter från en miljö (utveckling, testning, produktion) till en annan. För att höja upp din arbetsyta till en annan arbetsyta finns det två delar: använda [Azure Resource Manager](../../azure-resource-manager/templates/overview.md) för att skapa eller uppdatera arbetsyteresurser (pooler och arbetsyta); migrera artefakter (SQL-skript, notebook-fil, Spark-jobbdefinition, pipelines, datauppsättningar, dataflöden och så vidare) med Synapse CI/CD-verktyg i Azure DevOps. 

Den här artikeln beskriver hur du använder Azures lanseringspipeline för att automatisera distributionen av en Synapse-arbetsyta till flera miljöer.

## <a name="prerequisites"></a>Förutsättningar

-   Arbetsytan som används för utveckling har konfigurerats med en Git-lagringsplats i Studio. Mer information finns i [Källkontroll i Synapse Studio](source-control.md).
-   Ett Azure DevOps-projekt har förberetts för att köra en lanseringspipeline.

## <a name="set-up-a-release-pipelines"></a>Konfigurera en lanseringspipeline

1.  Öppna projektet som skapades för versionen i [Azure DevOps.](https://dev.azure.com/)

1.  Till vänster på sidan väljer du **Pipelines** och sedan **Versioner.**

    ![Välj Pipelines, Versioner](media/create-release-1.png)

1.  Välj **Ny pipeline,** eller, om du har befintliga pipelines, väljer du **Ny** och sedan **Ny lanseringspipeline**.

1.  Välj mallen **Tomt** jobb.

    ![Välj tomt jobb](media/create-release-select-empty.png)

1.  I rutan **Fasnamn** anger du namnet på din miljö.

1.  Välj **Lägg till artefakt** och välj sedan den git-lagringsplats som konfigurerats med Synapse Studio. Välj den git-lagringsplats som du använde för att hantera ARM-mallen för pooler och arbetsytor. Om du använder GitHub som källa måste du skapa en tjänstanslutning för ditt GitHub-konto och hämta lagringsplatsen. Mer information om [tjänstanslutning](/azure/devops/pipelines/library/service-endpoints) 

    ![Lägg till publiceringsgren](media/release-creation-github.png)

1.  Välj grenen av ARM-mallen för resursuppdatering. För **Standardversion väljer** du **Senaste från standardgrenen**.

    ![Lägga till ARM-mall](media/release-creation-arm-branch.png)

1.  Välj [publiceringsgrenen](source-control.md#configure-publishing-settings) för lagringsplatsen för **standardgrenen**. Som standard är den här publiceringsgrenen `workspace_publish` . För **Standardversion väljer** du **Senaste från standardgrenen**.

    ![Lägg till en artefakt](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-arm-resource-create-and-update"></a>Konfigurera en fasuppgift för att skapa och uppdatera ARM-resurser 

Lägg till Azure Resource Manager distributionsaktivitet för att skapa eller uppdatera resurser, inklusive arbetsyta och pooler:

1. I fasvyn väljer du **Visa fasuppgifter.**

    ![Fasvy](media/release-creation-stage-view.png)

1. Skapa en ny uppgift. Sök efter **ARM-malldistribution** och välj sedan Lägg **till**.

1. I uppgiften Distribution väljer du prenumeration, resursgrupp och plats för målarbetsytan. Ange autentiseringsuppgifter om det behövs.

1. I listan **Åtgärd** väljer du **Skapa eller uppdatera resursgruppen**.

1. Välj ellipsknappen (**...**) bredvid **rutan** Mall. Bläddra efter Azure Resource Manager för målarbetsytan

1. Välj **...** bredvid rutan **Mallparametrar** väljer du parameterfilen.

1. Välj **...** bredvid rutan **Åsidosätt mallparametrar** och ange önskade parametervärden för målarbetsytan. 

1. Välj **Inkrementellt** **för Distributionsläge.**
    
    ![distribuera arbetsyta och pooler](media/pools-resource-deploy.png)

1. (Valfritt) Lägg **Azure PowerShell för** tilldelning och uppdatering av rolltilldelning för arbetsyta. Om du använder en lanseringspipeline för att skapa en Synapse-arbetsyta läggs pipelinens huvudnamn för tjänsten till som standardadministratör för arbetsytan. Du kan köra PowerShell för att ge andra konton åtkomst till arbetsytan. 
    
    ![bevilja behörighet](media/release-creation-grant-permission.png)

 > [!WARNING]
> I läget Slutför distribution tas resurser som finns i resursgruppen men som inte anges i den nya Resource Manager mallen **bort.** Mer information finns i Azure Resource Manager [distributionslägen](../../azure-resource-manager/templates/deployment-modes.md)

## <a name="set-up-a-stage-task-for-artifacts-deployment"></a>Konfigurera en fasuppgift för artefaktdistribution 

Använd [distributionstillägget för Synapse-arbetsytan](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy) för att distribuera andra objekt i Synapse-arbetsytan, till exempel datauppsättning, SQL-skript, notebook-fil, spark-jobbdefinition, dataflöde, pipeline, länkad tjänst, autentiseringsuppgifter och IR (Integration Runtime).  

1. Sök efter och hämta tillägget från **Azure DevOps Marketplace**(https://marketplace.visualstudio.com/azuredevops) 

     ![Hämta tillägg](media/get-extension-from-market.png)

1. Välj en organisation för att installera tillägget. 

     ![Installera tillägget](media/install-extension.png)

1. Kontrollera att tjänstens huvudnamn för Azure DevOps-pipelinen har beviljats behörighet för prenumerationen och även tilldelats som arbetsyteadministratör för målarbetsytan. 

1. Skapa en ny uppgift. Sök efter **Distribution av Synapse-arbetsyta** och välj sedan **Lägg till**.

     ![Lägg till tillägg](media/add-extension-task.png)

1.  I uppgiften väljer du **...** bredvid rutan **Mall för** att välja mallfilen.

1. Välj **...** bredvid rutan **Mallparametrar för** att välja parameterfilen.

1. Välj anslutning, resursgrupp och namn på målarbetsytan. 

1. Välj **...** bredvid rutan **Åsidosätt mallparametrar** och ange önskade parametervärden för målarbetsytan. 

    ![Distribuera Synapse-arbetsyta](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> I CI/CD-scenarier måste integration runtime-typen (IR) vara densamma i olika miljöer. Om du till exempel har en IR med egen värd i utvecklingsmiljön måste samma IR också ha en egen värd i andra miljöer, till exempel test och produktion. Om du delar integreringskörningar i flera steg måste du på samma sätt konfigurera integreringskörningarna som länkade lokala i alla miljöer, till exempel utveckling, testning och produktion.

## <a name="create-release-for-deployment"></a>Skapa version för distribution 

När du har sparat alla ändringar kan du välja **Skapa version för** att skapa en version manuellt. Information om hur du automatiserar skapandet av versioner finns i [Azure DevOps-lanseringsutlösare](/azure/devops/pipelines/release/triggers)

   ![Välj Skapa version](media/release-creation-manually.png)

## <a name="use-custom-parameters-of-the-workspace-template"></a>Använda anpassade parametrar för arbetsytans mall 

Du använder automatiserad CI/CD och vill ändra vissa egenskaper under distributionen, men egenskaperna är inte parametriserade som standard. I det här fallet kan du åsidosätta standardparametermallen.

Om du vill **åsidosätta** standardparametermallen måste du skapa en anpassad parametermall, en fil med namnettemplate-parameters-definition.jspå i rotmappen för git-samarbetsgrenen. Du måste använda det exakta filnamnet. När du publicerar från samarbetsgrenen läser Synapse-arbetsytan den här filen och använder dess konfiguration för att generera parametrarna. Om ingen fil hittas används standardparametermallen.

### <a name="custom-parameter-syntax"></a>Anpassad parametersyntax

Följande är några riktlinjer för att skapa den anpassade parameterfilen:

* Ange egenskapssökvägen under relevant entitetstyp.
* Om du anger ett egenskapsnamn till anger du att du vill parameterisera alla egenskaper under det (endast ned till den första `*` nivån, inte rekursivt). Du kan också ange undantag till den här konfigurationen.
* Om du anger värdet för en egenskap som en sträng anger du att du vill parameterisera egenskapen. Använd formatet `<action>:<name>:<stype>`.
   *  `<action>` kan vara något av följande tecken:
      * `=` innebär att det aktuella värdet behålls som standardvärde för parametern .
      * `-` innebär att du inte behåller standardvärdet för parametern .
      * `|` är ett specialfall för hemligheter från Azure Key Vault för anslutningssträngar eller nycklar.
   * `<name>` är namnet på parametern . Om den är tom tar den namnet på egenskapen . Om värdet börjar med `-` ett tecken förkortas namnet. Till exempel `AzureStorage1_properties_typeProperties_connectionString` skulle förkortas till `AzureStorage1_connectionString` .
   * `<stype>` är parametertypen. Om `<stype>` är tomt är standardtypen `string` . Värden som stöds: `string` , , , , och `securestring` `int` `bool` `object` `secureobject` `array` .
* Om du anger en matris i filen betyder det att matchningsegenskapen i mallen är en matris. Synapse itererar genom alla objekt i matrisen med hjälp av definitionen som har angetts. Det andra objektet, en sträng, blir namnet på egenskapen, som används som namn på parametern för varje iteration.
* En definition kan inte vara specifik för en resursinstans. Alla definitioner gäller för alla resurser av den typen.
* Som standard parametriseras alla säkra strängar, som Key Vault hemligheter och säkra strängar som anslutningssträngar, nycklar och token.

### <a name="parameter-template-definition-samples"></a>Exempel på parametermalldefinition 

Här är ett exempel på hur en parametermallsdefinition ser ut:

```json
{
"Microsoft.Synapse/workspaces/notebooks": {
        "properties":{
            "bigDataPool":{
                "referenceName": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/sqlscripts": {
     "properties": {
         "content":{
             "currentConnection":{
                    "*":"-"
                 }
            } 
        }
    },
    "Microsoft.Synapse/workspaces/pipelines": {
        "properties": {
            "activities": [{
                 "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.Synapse/workspaces/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:int",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                     "*": "="
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.Synapse/workspaces/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Här är en förklaring av hur föregående mall konstrueras, uppdelat efter resurstyp.

#### <a name="notebooks"></a>Notebooks 

* Alla -egenskap i `properties/bigDataPool/referenceName` sökvägen parametriseras med dess standardvärde. Du kan parametrisera den anslutna Spark-poolen för varje notebook-fil. 

#### <a name="sql-scripts"></a>SQL-skript 

* Egenskaper (poolName och databaseName) i `properties/content/currentConnection` sökvägen parametriseras som strängar utan standardvärdena i mallen. 

#### <a name="pipelines"></a>Pipelines

* Alla -egenskap i sökvägen `activities/typeProperties/waitTimeInSeconds` parametriseras. Alla aktiviteter i en pipeline som har en kodnivåegenskap med namnet (till exempel aktiviteten) parametriseras som ett `waitTimeInSeconds` `Wait` tal med ett standardnamn. Men den har inget standardvärde i Resource Manager mall. Det är obligatoriska indata under den Resource Manager distributionen.
* På samma sätt parametriseras `headers` en egenskap med namnet (till exempel i en `Web` aktivitet) med typen `object` (Objekt). Den har ett standardvärde, vilket är samma värde som källfabriken.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Alla egenskaper under sökvägen `typeProperties` parametriseras med respektive standardvärden. Det finns till exempel två egenskaper under `IntegrationRuntimes` typegenskaperna: `computeProperties` och `ssisProperties` . Båda egenskapstyperna skapas med respektive standardvärden och standardtyper (Objekt).

#### <a name="triggers"></a>Utlösare

* Under `typeProperties` parametriseras två egenskaper. Den första är `maxConcurrency` , som anges ha ett standardvärde och är av typen `string` . Den har standardparameternamnet `<entityName>_properties_typeProperties_maxConcurrency` .
* Egenskapen `recurrence` är också parametriserad. Under den anges alla egenskaper på den nivån för att parametriseras som strängar, med standardvärden och parameternamn. Ett undantag är egenskapen `interval` , som parametriseras som typen `int` . Parameternamnet har suffixet `<entityName>_properties_typeProperties_recurrence_triggerSuffix` . På samma sätt är `freq` egenskapen en sträng och parametriseras som en sträng. Egenskapen `freq` parametriseras dock utan ett standardvärde. Namnet förkortas och suffixet . Till exempel `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Länkade tjänster är unika. Eftersom länkade tjänster och datauppsättningar har en mängd olika typer kan du ange typspecifik anpassning. I det här exemplet tillämpas en specifik mall för alla länkade tjänster `AzureDataLakeStore` av typen . För alla andra (via `*` ) tillämpas en annan mall.
* Egenskapen `connectionString` parametriseras som ett `securestring` värde. Det har inget standardvärde. Det får ett förkortat parameternamn med suffixet `connectionString` .
* Egenskapen råkar `secretAccessKey` vara en `AzureKeyVaultSecret` (till exempel i en länkad Amazon S3-tjänst). Den parametriseras automatiskt som en Azure Key Vault hemlighet och hämtas från det konfigurerade nyckelvalvet. Du kan också parameterisera själva nyckelvalvet.

#### <a name="datasets"></a>Datauppsättningar

* Även om typspecifik anpassning är tillgänglig för datauppsättningar kan du ange konfiguration utan att uttryckligen ha en \* konfiguration på -nivå. I föregående exempel parametriseras alla `typeProperties` datamängdsegenskaper under .


## <a name="best-practices-for-cicd"></a>Metodtips för CI/CD

Om du använder Git-integrering med synapse-arbetsytan och har en CI/CD-pipeline som flyttar ändringarna från utveckling till testning och sedan till produktion, rekommenderar vi följande metodtips:

-   **Git-integrering**. Konfigurera endast din Synapse-arbetsyta för utveckling med Git-integrering. Ändringar av test- och produktionsarbetsytor distribueras via CI/CD och behöver inte Git-integrering.
-   **Förbered pooler innan artefaktmigrering**. Om du har ETT SQL-skript eller en notebook-fil som är kopplad till pooler på utvecklingsarbetsytan förväntas samma namn på pooler i olika miljöer. 
-   **Infrastruktur som kod (IaC).** Hantering av infrastruktur (nätverk, virtuella datorer, lastbalanserare och anslutningstopologi) i en beskrivande modell använder samma versionshantering som DevOps-teamet använder för källkod. 
-   **Andra**. Se [metodtips för ADF-artefakter](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd)

## <a name="troubleshooting-artifacts-deployment"></a>Felsöka distribution av artefakter 

### <a name="use-the-synapse-workspace-deployment-task"></a>Använda distributionsuppgiften för Synapse-arbetsytan

I Synapse finns det ett antal artefakter som inte är ARM-resurser. Detta skiljer sig från Azure Data Factory. Distributionsuppgiften för ARM-mall fungerar inte korrekt för att distribuera Synapse-artefakter
 
### <a name="unexpected-token-error-in-release"></a>Oväntat tokenfel i versionen

När parameterfilen har parametervärden som inte är ryms kommer lanseringspipelinen inte att parsa filen och genererar felet "oväntad token". Vi rekommenderar att du åsidosätter parametrar eller använder Azure KeyVault för att hämta parametervärden. Du kan också använda dubbla escape-tecken som en lösning.
