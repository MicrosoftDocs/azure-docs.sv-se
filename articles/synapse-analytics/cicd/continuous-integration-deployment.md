---
title: Kontinuerlig integrering och leverans för Synapse-arbetsytan
description: Lär dig hur du använder kontinuerlig integrering och leverans för att distribuera ändringar i arbets ytan från en miljö (utveckling, testning, produktion) till en annan.
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: de3738573bb9bb6f045a45d290c74ba9e6902a5e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103561965"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Kontinuerlig integrering och leverans för Azure dataSynapses-arbetsyta

## <a name="overview"></a>Översikt

Kontinuerlig integrering (CI) är en process som automatiserar genereringen och testningen av kod varje gång en grupp medlem genomför ändringar i versions kontrollen. Kontinuerlig distribution (CD) är en process för att bygga, testa, konfigurera och distribuera från flera testnings-eller utvecklings miljöer till en produktions miljö.

För Azure Synapse-arbetsytan, kontinuerlig integrering och leverans (CI/CD) flyttar alla entiteter från en miljö (utveckling, test, produktion) till en annan. För att kunna befordra arbets ytan till en annan arbets yta finns det två delar: Använd [Azure Resource Manager mallar](../../azure-resource-manager/templates/overview.md) för att skapa eller uppdatera arbets ytans resurser (pooler och arbets yta). Migrera artefakter (SQL-skript, Notebook, Spark jobb definition, pipeliner, data uppsättningar, data flöden osv) med Synapse CI/CD-verktyg i Azure DevOps. 

Den här artikeln beskriver hur du kan använda Azure release pipeline för att automatisera distributionen av en Synapse-arbetsyta till flera miljöer.

## <a name="prerequisites"></a>Förutsättningar

-   Arbets ytan som används för utveckling har kon figurer ATS med en git-lagringsplats i Studio, se [käll kontroll i Synapse Studio](source-control.md).
-   Ett Azure DevOps-projekt har förberetts för att köra versions pipelinen.

## <a name="set-up-a-release-pipelines"></a>Konfigurera en versions pipeline

1.  Öppna projektet som skapats för versionen i [Azure-DevOps](https://dev.azure.com/).

1.  Välj **pipelines** på vänster sida av sidan och välj sedan **versioner**.

    ![Välj pipeliner, versioner](media/create-release-1.png)

1.  Välj **ny pipeline** eller, om du har befintliga pipeliner, väljer du **ny** och sedan **ny versions pipeline**.

1.  Välj den **tomma jobb** mal len.

    ![Välj tomt jobb](media/create-release-select-empty.png)

1.  I rutan **scen namn** anger du namnet på din miljö.

1.  Välj **Lägg till artefakt** och välj sedan den git-lagringsplats som kon figurer ATS med din utvecklings Synapse Studio. Välj den git-lagringsplats som du använde för att hantera ARM-mall för pooler och arbets yta. Om du använder GitHub som källa måste du skapa en tjänst anslutning för ditt GitHub-konto och pull-databaser. Mer information om [tjänst anslutning](/azure/devops/pipelines/library/service-endpoints) 

    ![Lägg till publicerings gren](media/release-creation-github.png)

1.  Välj grenen för ARM-mallen för resurs uppdatering. Välj **senaste från standard gren** för **standard versionen**.

    ![Lägg till ARM-mall](media/release-creation-arm-branch.png)

1.  Välj [publicerings grenen](source-control.md#configure-publishing-settings) för lagrings platsen för **standard grenen**. Som standard är publicerings grenen `workspace_publish` . Välj **senaste från standard gren** för **standard versionen**.

    ![Lägg till en artefakt](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-arm-resource-create-and-update"></a>Konfigurera en fas aktivitet för ARM-resursen skapa och uppdatera 

Lägg till en Azure Resource Manager distributions uppgift för att skapa eller uppdatera resurser, inklusive arbets ytan och pooler:

1. I vyn fas väljer du **Visa fas aktiviteter**.

    ![Vyn fas](media/release-creation-stage-view.png)

1. Skapa en ny uppgift. Sök efter **distribution av arm-mall** och välj sedan **Lägg till**.

1. I distributions aktiviteten väljer du prenumeration, resurs grupp och plats för mål arbets ytan. Ange autentiseringsuppgifter om det behövs.

1. I listan **åtgärd** väljer du **skapa eller uppdatera resurs grupp**.

1. Välj knappen med tre punkter (**...**) bredvid rutan **mall** . Bläddra efter Azure Resource Manager mall för mål arbets ytan

1. Välj **...** bredvid rutan **mallparametrar** för att välja parameter filen.

1. Välj **...** bredvid rutan **Åsidosätt mallparametrar** och ange önskade parameter värden för mål arbets ytan. 

1. Välj **stegvis** för **distributions läget**.
    
    ![distribution av arbets ytor och pooler](media/pools-resource-deploy.png)

1. Valfritt Lägg till **Azure PowerShell** för roll tilldelningen tilldela och uppdatera arbets ytan. Om du använder versions pipeline för att skapa en Synapse-arbetsyta läggs pipelinens tjänst objekt till som standard arbets ytans administratör. Du kan köra PowerShell för att ge andra konton åtkomst till arbets ytan. 
    
    ![bevilja behörighet](media/release-creation-grant-permission.png)

 > [!WARNING]
> I fullständigt distributions läge **raderas** resurser som finns i resurs gruppen men inte har angetts i den nya Resource Manager-mallen. Mer information finns i [Azure Resource Manager distributions lägen](../../azure-resource-manager/templates/deployment-modes.md)

## <a name="set-up-a-stage-task-for-artifacts-deployment"></a>Konfigurera en fas uppgift för distribution av artefakter 

Använd [distributions tillägget Synapse-arbetsyta](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy) för att distribuera andra objekt i Synapse-arbetsytan, t. ex. data uppsättning, SQL-skript, Notebook, Spark jobb definition, data flöde, pipeline, länkad tjänst, AUTENTISERINGSUPPGIFTER och IR (integration Runtime).  

1. Sök och hämta tillägget från **Azure DevOps Marketplace**(https://marketplace.visualstudio.com/azuredevops) 

     ![Hämta tillägg](media/get-extension-from-market.png)

1. Välj en organisation för att installera tillägget. 

     ![Installera tillägget](media/install-extension.png)

1. Se till att Azure DevOps pipeline: s tjänst huvud namn har beviljats behörigheten för prenumerationen och även tilldelats som arbets ytans administratör för mål arbets ytan. 

1. Skapa en ny uppgift. Sök efter **distributionen av Synapse-arbetsytan** och välj sedan **Lägg till**.

     ![Lägg till tillägg](media/add-extension-task.png)

1.  I uppgiften väljer du **...** bredvid rutan **mall** för att välja mallfilen.

1. Välj **...** bredvid rutan **mallparametrar** för att välja parameter filen.

1. Välj anslutning, resurs grupp och namn på mål arbets ytan. 

1. Välj **...** bredvid rutan **Åsidosätt mallparametrar** och ange önskade parameter värden för mål arbets ytan. 

    ![Distribuera Synapse-arbetsyta](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> I CI/CD-scenarier måste integrerings körnings typen (IR) i olika miljöer vara densamma. Om du till exempel har en lokal IR-anslutning i utvecklings miljön, måste samma IR också vara av typen egen värd i andra miljöer, till exempel test och produktion. Om du däremot delar integrerings körningar över flera steg, måste du konfigurera integration runtime som länkad egen värd i alla miljöer, till exempel utveckling, testning och produktion.

## <a name="create-release-for-deployment"></a>Skapa version för distribution 

När du har sparat alla ändringar kan du välja **Skapa version** för att skapa en version manuellt. För att automatisera skapandet av versioner, se [Azure DevOps release triggers](/azure/devops/pipelines/release/triggers)

   ![Välj Skapa version](media/release-creation-manually.png)

## <a name="use-custom-parameters-of-the-workspace-template"></a>Använd anpassade parametrar för arbets ytans mall 

Du använder automatiserad CI/CD och du vill ändra vissa egenskaper under distributionen, men egenskaperna är inte parameterstyrda som standard. I det här fallet kan du åsidosätta standard parameter mal len.

Om du vill åsidosätta standard parameter mal len måste du skapa en anpassad parametriserad mall, en fil med namnet **template-parameters-definition.js** i rotmappen i din git Collaboration-gren. Du måste använda det exakta fil namnet. När du publicerar från samarbets grenen kommer Synapse-arbetsytan att läsa den här filen och använda dess konfiguration för att generera parametrarna. Om ingen fil hittas används standard parameter mal len.

### <a name="custom-parameter-syntax"></a>Anpassad parameter-syntax

Här följer några rikt linjer för att skapa filen med anpassade parametrar:

* Ange sökvägen till egenskapen under den relevanta entitetstypen.
* Om du anger ett egenskaps namn för att ange att `*` du vill Parameterisera alla egenskaper under den (enbart till den första nivån, inte rekursivt). Du kan också ange undantag för den här konfigurationen.
* Att ange värdet för en egenskap som en sträng anger att du vill Parameterisera egenskapen. Använd formatet `<action>:<name>:<stype>`.
   *  `<action>` kan vara något av följande tecken:
      * `=` betyder att det aktuella värdet ska vara standardvärdet för parametern.
      * `-` innebär att inte behålla standardvärdet för parametern.
      * `|` är ett specialfall för hemligheter från Azure Key Vault för anslutnings strängar eller nycklar.
   * `<name>` är namnet på parametern. Om det är tomt tar det med namnet på egenskapen. Om värdet börjar med ett `-` Character förkortas namnet. Till exempel `AzureStorage1_properties_typeProperties_connectionString` skulle kortas till `AzureStorage1_connectionString` .
   * `<stype>` är typen av parameter. Om `<stype>` är tomt är standard typen `string` . Värden som stöds:,,,, `string` `securestring` `int` `bool` `object` `secureobject` och `array` .
* Att ange en matris i filen anger att den matchande egenskapen i mallen är en matris. Synapse itererar igenom alla objekt i matrisen med hjälp av definitionen som anges. Det andra objektet, en sträng, blir namnet på egenskapen, som används som namn för parametern för varje iteration.
* En definition kan inte vara unik för en resurs instans. Alla definitioner gäller för alla resurser av den typen.
* Som standard är alla säkra strängar, som Key Vault hemligheter och säkra strängar, som anslutnings strängar, nycklar och tokens, parameterstyrda.

### <a name="parameter-template-definition-samples"></a>Definitions exempel för parameter mal len 

Här är ett exempel på hur en definition av en parametriserad mall ser ut så här:

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
Här är en förklaring av hur föregående mall skapas, uppdelat efter resurs typ.

#### <a name="notebooks"></a>Notebooks 

* Alla egenskaper i sökvägen `properties/bigDataPool/referenceName` är parameterstyrda med standardvärdet. Du kan Parameterisera anslutna Spark-pool för varje Notebook-fil. 

#### <a name="sql-scripts"></a>SQL-skript 

* Egenskaperna (poolName och databaseName) i sökvägen `properties/content/currentConnection` är parameterstyrda som strängar utan standardvärdena i mallen. 

#### <a name="pipelines"></a>Pipelines

* Alla egenskaper i sökvägen `activities/typeProperties/waitTimeInSeconds` är parameterstyrda. Alla aktiviteter i en pipeline som har en kod nivå egenskap med namnet `waitTimeInSeconds` (till exempel `Wait` aktiviteten) är parameterstyrda som ett tal med ett standard namn. Men det finns inget standardvärde i Resource Manager-mallen. Det är en obligatorisk Indatatyp under distributionen av Resource Manager.
* På samma sätt är en egenskap `headers` som kallas (t. ex. i en `Web` aktivitet) parameterstyrda med typ `object` (objekt). Det har ett standardvärde, vilket är samma värde som käll fabriken.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Alla egenskaper under sökvägen `typeProperties` är parameterstyrda med respektive standardvärden. Det finns till exempel två egenskaper under `IntegrationRuntimes` typ egenskaper: `computeProperties` och `ssisProperties` . Båda egenskaps typerna skapas med deras respektive standardvärden och typer (objekt).

#### <a name="triggers"></a>Utlösare

* Under `typeProperties` , har två egenskaper parametriserade. Det första är `maxConcurrency` , som har angetts att ha ett standardvärde och är av typen `string` . Den har standard parameter namnet `<entityName>_properties_typeProperties_maxConcurrency` .
* `recurrence`Egenskapen är också parametriserad. Under den här nivån anges alla egenskaper på den nivån som parameterstyrda som strängar, med standardvärden och parameter namn. Ett undantag är `interval` egenskapen, som är parameterstyrda som typ `int` . Parameter namnet har suffix `<entityName>_properties_typeProperties_recurrence_triggerSuffix` . På samma sätt `freq` är egenskapen en sträng och är parameterstyrda som en sträng. `freq`Egenskapen är dock parameterstyrda utan ett standardvärde. Namnet är kortare och suffixet. Till exempel `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Länkade tjänster är unika. Eftersom länkade tjänster och data uppsättningar har en mängd olika typer, kan du ange en typ bestämd anpassning. I det här exemplet `AzureDataLakeStore` används en speciell mall för alla länkade tjänster av typen. En annan mall används för alla andra (via `*` ).
* `connectionString`Egenskapen är parameterstyrda som ett `securestring` värde. Det har inget standardvärde. Det kommer att ha ett förkortat parameter namn med suffix `connectionString` .
* Egenskapen `secretAccessKey` inträffar som en (till `AzureKeyVaultSecret` exempel i en länkad Amazon S3-tjänst). Den är automatiskt parameterstyrda som en Azure Key Vault hemlighet och hämtas från det konfigurerade nyckel valvet. Du kan också Parameterisera själva nyckel valvet.

#### <a name="datasets"></a>Datauppsättningar

* Även om typ specifik anpassning är tillgänglig för data uppsättningar kan du ange konfiguration utan att uttryckligen ha en \* -nivå-konfiguration. I föregående exempel är alla data uppsättnings egenskaper under `typeProperties` parameterstyrda.


## <a name="best-practices-for-cicd"></a>Metod tips för CI/CD

Om du använder git-integrering med din Synapse-arbetsyta och har en CI/CD-pipeline som flyttar dina ändringar från utveckling till test och sedan till produktion, rekommenderar vi följande metod tips:

-   **Git-integrering**. Konfigurera endast din utvecklings Synapse-arbetsyta med git-integrering. Ändringar av test-och produktions arbets ytor distribueras via CI/CD och kräver inte git-integrering.
-   **Förbered pooler innan artefakter migreras**. Om du har SQL-skript eller antecknings bok ansluten till pooler i arbets ytan utveckling förväntas samma namn på pooler i olika miljöer. 
-   **Infrastruktur som kod (IaC)**. Hantering av infrastruktur (nätverk, virtuella datorer, belastningsutjämnare och nätverkstopologi) i en beskrivande modell använder samma version som DevOps-teamet för käll koden. 
-   **Andra**. Se [metod tips för ADF-artefakter](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd)

## <a name="troubleshooting-artifacts-deployment"></a>Felsöka artefakt distribution 

### <a name="use-the-synapse-workspace-deployment-task"></a>Använda distributions uppgiften Synapse-arbetsyta

I Synapse finns det ett antal artefakter som inte är ARM-resurser. Detta skiljer sig från Azure Data Factory. Distributions uppgiften för ARM-mallen fungerar inte korrekt för att distribuera Synapse-artefakter
 
### <a name="unexpected-token-error-in-release"></a>Oväntat token-fel i version

När parameter filen har parameter värden som inte är undantagna, kan inte versions pipelinen parsa filen och generera felet "oväntad token". Vi föreslår att du åsidosätter parametrar eller använder Azure-nyckel valvet för att hämta parameter värden. Du kan också använda dubbla escape-tecken som en lösning.
