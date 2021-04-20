---
title: Kontinuerlig integrering och leverans för Synapse-arbetsyta
description: Lär dig hur du använder kontinuerlig integrering och leverans för att distribuera ändringar i arbetsytan från en miljö (utveckling, testning, produktion) till en annan.
author: liudan66
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 833478d956560c981bd6cc3ba03b48bb602f563c
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739682"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Kontinuerlig integrering och leverans för Azure Synapse arbetsyta

## <a name="overview"></a>Översikt

Kontinuerlig integrering (CI) är en process för att automatisera bygget och testningen av kod varje gång en teammedlem genomför ändringar i versionskontroll. Kontinuerlig distribution (CD) är en process för att skapa, testa, konfigurera och distribuera från flera test- eller mellanlagringsmiljöer till en produktionsmiljö.

I en Azure Synapse Analytics flyttar kontinuerlig integrering och leverans (CI/CD) alla entiteter från en miljö (utveckling, testning, produktion) till en annan. Det finns två delar för att höja upp din arbetsyta till en annan arbetsyta. Börja med att använda en [Azure Resource Manager (ARM-mall) för](../../azure-resource-manager/templates/overview.md) att skapa eller uppdatera arbetsyteresurser (pooler och arbetsytor). Migrera sedan artefakter (SQL-skript, notebook-fil, Spark-jobbdefinition, pipelines, datauppsättningar, dataflöden och så vidare) med Azure Synapse Analytics CI/CD-verktyg i Azure DevOps. 

Den här artikeln beskriver hur du använder en Azure DevOps-lanseringspipeline för att automatisera distributionen av en Azure Synapse-arbetsyta till flera miljöer.

## <a name="prerequisites"></a>Förutsättningar

Dessa krav och konfigurationer måste finnas på plats för att automatisera distributionen av en Azure Synapse arbetsyta till flera miljöer.

### <a name="azure-devops"></a>Azure DevOps

- Ett Azure DevOps-projekt har förberetts för att köra lanseringspipelinen.
- [Bevilja alla användare som checkar in kodåtkomsten "Basic" på organisationsnivå](/azure/devops/organizations/accounts/add-organization-users?view=azure-devops&tabs=preview-page)så att de kan se lagringsplatsen.
- Bevilja ägarrättigheter till Azure Synapse lagringsplatsen.
- Kontrollera att du har skapat en azure DevOps VM-agent med egen värd eller att du använder en Azure DevOps-värdbaserade agent.
- Behörighet att [skapa en Azure Resource Manager-tjänstanslutning för resursgruppen](/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml).
- En Azure Active Directory (Azure AD)-administratör måste installera [tillägget Azure DevOps Synapse Workspace Deployment Agent i Azure DevOps-organisationen](/azure/devops/marketplace/install-extension).
- Skapa eller nominera ett befintligt tjänstkonto som pipelinen ska köras som. Du kan använda en personlig åtkomsttoken i stället för ett tjänstkonto, men dina pipelines fungerar inte när användarkontot har tagits bort.

### <a name="azure-active-directory"></a>Azure Active Directory

- I Azure AD skapar du ett huvudnamn för tjänsten som ska användas för distribution. Uppgiften Distribution av Synapse-arbetsyta stöder inte användning av en hanterad identitet i version 1* och tidigare.
- Azure AD-administratörsrättigheter krävs för den här åtgärden.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

> [!NOTE]
> Du kan automatisera och distribuera dessa krav med hjälp av samma pipeline, en ARM-mall eller Azure CLI, men processen beskrivs inte i den här artikeln.

- Den "källarbetsyta" som används för utveckling måste konfigureras med en Git-lagringsplats i Synapse Studio. Mer information finns i [Källkontroll i Synapse Studio](source-control.md#configuration-method-2-manage-hub).

- En tom arbetsyta att distribuera till. Så här ställer du in den tomma arbetsytan:

  1. Skapa en ny Azure Synapse Analytics arbetsyta.
  1. Ge VM-agenten och tjänstens huvudnamn deltagarrättigheter till resursgruppen som den nya arbetsytan finns i.
  1. Konfigurera inte Git-lagringsplatsens anslutning på den nya arbetsytan.
  1. I Azure Portal du den nya Azure Synapse Analytics-arbetsytan och ger dig själv och den som ska köra Azure DevOps-pipelinen Azure Synapse Analytics arbetsytans ägarrättigheter. 
  1. Lägg till Azure DevOps VM-agenten och tjänstens huvudnamn i rollen Deltagare för arbetsytan (detta bör ha ärvts, men kontrollera att det är det).
  1. I arbetsytan Azure Synapse Analytics du till **Studio**  >  **Hantera**  >  **IAM**. Lägg till Azure DevOps VM-agenten och tjänstens huvudnamn i arbetsytans administratörsgrupp.
  1. Öppna lagringskontot som används för arbetsytan. I IAM lägger du till VM-agenten och tjänstens huvudnamn i rollen Välj Storage Blob Data-deltagare.
  1. Skapa ett nyckelvalv i supportprenumerationen och se till att både den befintliga arbetsytan och den nya arbetsytan har minst GET- och LIST-behörigheter till valvet.
  1. För att den automatiserade distributionen ska fungera ser du till att alla anslutningssträngar som anges i dina länkade tjänster finns i nyckelvalvet.

### <a name="additional-prerequisites"></a>Ytterligare krav
 
 - Spark-pooler och integrationskörningar med egen värd skapas inte i en pipeline. Om du har en länkad tjänst som använder en integrationskörning med egen värd skapar du den manuellt på den nya arbetsytan.
 - Om du utvecklar notebook-datorer och ansluter dem till en Spark-pool skapar du om Spark-poolen på arbetsytan.
 - Notebooks som är länkade till en Spark-pool som inte finns i en miljö distribueras inte.
 - Spark-poolnamnen måste vara samma på båda arbetsytorna.
 - Namnge alla databaser, SQL-pooler och andra resurser på samma sätt i båda arbetsytorna.
 - Om dina etablerade SQL-pooler pausas när du försöker distribuera kan distributionen misslyckas.

Mer information finns i [CI CD i Azure Synapse Analytics del 4 – Lanseringspipelinen](https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434). 


## <a name="set-up-a-release-pipeline"></a>Konfigurera en lanseringspipeline

1.  I [Azure DevOps](https://dev.azure.com/)öppnar du projektet som skapades för versionen.

1.  Till vänster på sidan väljer du **Pipelines** och sedan **Versioner.**

    ![Välj Pipelines, Versioner](media/create-release-1.png)

1.  Välj **Ny pipeline**, eller, om du har befintliga pipelines, väljer du **Ny** och sedan **Ny lanseringspipeline**.

1.  Välj mallen **Tomt** jobb.

    ![Välj Tomt jobb](media/create-release-select-empty.png)

1.  I rutan **Stage name (Fasnamn)** anger du namnet på din miljö.

1.  Välj **Lägg till artefakt** och välj sedan den git-lagringsplats som konfigurerats med Synapse Studio. Välj den git-lagringsplats som du använde för att hantera ARM-mallen för pooler och arbetsytor. Om du använder GitHub som källa måste du skapa en tjänstanslutning för ditt GitHub-konto och hämta lagringsplatsen. Mer information om [tjänstanslutning](/azure/devops/pipelines/library/service-endpoints) 

    ![Lägg till publiceringsgren](media/release-creation-github.png)

1.  Välj grenen av ARM-mallen för resursuppdatering. För **Standardversion väljer** du **Senaste från standardgrenen**.

    ![Lägga till ARM-mall](media/release-creation-arm-branch.png)

1.  Välj [publiceringsgrenen](source-control.md#configure-publishing-settings) för lagringsplatsen för **standardgrenen**. Som standard är den här publiceringsgrenen `workspace_publish` . För **Standardversion väljer** du **Senaste från standardgrenen**.

    ![Lägg till en artefakt](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-an-arm-template-to-create-and-update-resource"></a>Konfigurera en fasuppgift för en ARM-mall för att skapa och uppdatera resurs 

Om du har en ARM-mall för att distribuera en resurs, till exempel en Azure Synapse-arbetsyta, Spark- och SQL-pooler eller ett nyckelvalv, lägger du till en Azure Resource Manager Deployment-aktivitet för att skapa eller uppdatera dessa resurser:

1. I fasvyn väljer du **Visa fasuppgifter.**

    ![Fasvy](media/release-creation-stage-view.png)

1. Skapa en ny uppgift. Sök efter **ARM-malldistribution** och välj sedan Lägg **till**.

1. I uppgiften Distribution väljer du prenumeration, resursgrupp och plats för målarbetsytan. Ange autentiseringsuppgifter om det behövs.

1. I listan **Åtgärd** väljer du **Skapa eller uppdatera resursgruppen**.

1. Välj ellipsknappen (**...**) bredvid **rutan** Mall. Bläddra efter Azure Resource Manager för målarbetsytan

1. Välj **...** bredvid rutan **Mallparametrar för** att välja parameterfilen.

1. Välj **...** bredvid rutan **Åsidosätt mallparametrar** och ange önskade parametervärden för målarbetsytan. 

1. Välj **Inkrementellt** **för Distributionsläge.**
    
    ![distribuera arbetsyta och pooler](media/pools-resource-deploy.png)

1. (Valfritt) Lägg **Azure PowerShell** för tilldelning och uppdatering av rolltilldelning för arbetsyta. Om du använder lanseringspipeline för att skapa en Synapse-arbetsyta läggs pipelinens huvudnamn för tjänsten till som standardadministratör för arbetsytan. Du kan köra PowerShell för att ge andra konton åtkomst till arbetsytan. 
    
    ![bevilja behörighet](media/release-creation-grant-permission.png)

 > [!WARNING]
> I läget Slutför distribution tas resurser som finns i resursgruppen men som inte anges i den Resource Manager mallen **bort.** Mer information finns i Azure Resource Manager [distributionslägen](../../azure-resource-manager/templates/deployment-modes.md)

## <a name="set-up-a-stage-task-for-synapse-artifacts-deployment"></a>Konfigurera en fasuppgift för distribution av Synapse-artefakter 

Använd [distributionstillägget för Synapse-arbetsytan](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy) för att distribuera andra objekt i Synapse-arbetsytan, till exempel datauppsättning, SQL-skript, notebook-fil, Spark-jobbdefinition, dataflöde, pipeline, länkad tjänst, autentiseringsuppgifter och IR (Integration Runtime).  

1. Söka efter och hämta tillägget från **Azure DevOps Marketplace**(https://marketplace.visualstudio.com/azuredevops) 

     ![Hämta tillägg](media/get-extension-from-market.png)

1. Välj en organisation för att installera tillägget. 

     ![Installera tillägget](media/install-extension.png)

1. Kontrollera att tjänstens huvudnamn för Azure DevOps-pipelinen har beviljats behörighet för prenumerationen och även tilldelats som arbetsyteadministratör för målarbetsytan. 

1. Skapa en ny uppgift. Sök efter **Distribution av Synapse-arbetsyta** och välj sedan **Lägg till**.

     ![Lägg till tillägg](media/add-extension-task.png)

1.  I uppgiften väljer du **...** bredvid rutan **Mall** för att välja mallfilen.

1. Välj **...** bredvid rutan **Mallparametrar** väljer du parameterfilen.

1. Välj anslutning, resursgrupp och namn på målarbetsytan. 

1. Välj **...** bredvid rutan **Åsidosätt mallparametrar** och ange önskade parametervärden för målarbetsytan, inklusive anslutningssträngar och kontonycklar som används i dina länkade tjänster. [Klicka här om du vill ha mer information] (https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434)

    ![Distribuera Synapse-arbetsyta](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> I CI/CD-scenarier måste integration runtime-typen (IR) vara densamma i olika miljöer. Om du till exempel har en IR med egen värd i utvecklingsmiljön måste samma IR också ha en egen värd i andra miljöer, till exempel test och produktion. Om du delar integreringskörningar i flera steg måste du på samma sätt konfigurera integreringskörningarna som länkade lokala i alla miljöer, till exempel utveckling, testning och produktion.

## <a name="create-release-for-deployment"></a>Skapa en version för distribution 

När du har sparat alla ändringar kan du välja **Skapa version för** att manuellt skapa en version. Information om hur du automatiserar skapandet av versioner finns i [Azure DevOps-lanseringsutlösare](/azure/devops/pipelines/release/triggers)

   ![Välj Skapa version](media/release-creation-manually.png)

## <a name="use-custom-parameters-of-the-workspace-template"></a>Använda anpassade parametrar för arbetsytans mall 

Du använder automatiserad CI/CD och du vill ändra vissa egenskaper under distributionen, men egenskaperna är inte parametriserade som standard. I det här fallet kan du åsidosätta standardparametermallen.

Om du vill **åsidosätta** standardparametermallen måste du skapa en anpassad parametermall, en filtemplate-parameters-definition.jspå i rotmappen för git-samarbetsgrenen. Du måste använda det exakta filnamnet. När du publicerar från samarbetsgrenen läser Synapse-arbetsytan den här filen och använder dess konfiguration för att generera parametrarna. Om ingen fil hittas används standardparametermallen.

### <a name="custom-parameter-syntax"></a>Anpassad parametersyntax

Följande är några riktlinjer för att skapa den anpassade parameterfilen:

* Ange egenskapssökvägen under den relevanta entitetstypen.
* Om du anger ett egenskapsnamn till anger du att du vill parametrisera alla egenskaper under det (endast ned till den första `*` nivån, inte rekursivt). Du kan också ange undantag till den här konfigurationen.
* Om du anger värdet för en egenskap som en sträng anger du att du vill parameterisera egenskapen. Använd formatet `<action>:<name>:<stype>`.
   *  `<action>` kan vara något av följande tecken:
      * `=` innebär att det aktuella värdet behålls som standardvärde för parametern .
      * `-` innebär att du inte behåller standardvärdet för parametern .
      * `|` är ett specialfall för hemligheter från Azure Key Vault för anslutningssträngar eller nycklar.
   * `<name>` är namnet på parametern . Om den är tom tar den namnet på egenskapen . Om värdet börjar med `-` ett tecken förkortas namnet. Till exempel `AzureStorage1_properties_typeProperties_connectionString` skulle förkortas till `AzureStorage1_connectionString` .
   * `<stype>` är parametertypen. Om `<stype>` är tomt är standardtypen `string` . Värden som stöds: `string` , , , , och `securestring` `int` `bool` `object` `secureobject` `array` .
* Om du anger en matris i filen betyder det att matchningsegenskapen i mallen är en matris. Synapse itererar igenom alla objekt i matrisen med hjälp av definitionen som har angetts. Det andra objektet, en sträng, blir namnet på egenskapen, som används som namn på parametern för varje iteration.
* En definition kan inte vara specifik för en resursinstans. Alla definitioner gäller för alla resurser av den typen.
* Som standard är alla säkra strängar, som Key Vault hemligheter och säkra strängar, som anslutningssträngar, nycklar och token, parametriserade.

### <a name="parameter-template-definition-samples"></a>Definitionsexempel för parametermall 

Här är ett exempel på hur en parametermalldefinition ser ut:

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

Här är en förklaring av hur föregående mall skapas, uppdelat efter resurstyp.

#### <a name="notebooks"></a>Notebooks 

* Alla egenskap i sökvägen `properties/bigDataPool/referenceName` parametriseras med dess standardvärde. Du kan parameterisera den bifogade Spark-poolen för varje notebook-fil. 

#### <a name="sql-scripts"></a>SQL-skript 

* Egenskaper (poolName och databaseName) i `properties/content/currentConnection` sökvägen parametriseras som strängar utan standardvärdena i mallen. 

#### <a name="pipelines"></a>Pipelines

* Alla -egenskap i sökvägen `activities/typeProperties/waitTimeInSeconds` parametriseras. Alla aktiviteter i en pipeline som har en kodnivåegenskap med namnet (till exempel aktiviteten) parametriseras som ett `waitTimeInSeconds` `Wait` tal med ett standardnamn. Men den har inget standardvärde i Resource Manager mallen. Det är obligatoriska indata under den Resource Manager distributionen.
* På samma sätt parametriseras en egenskap med namnet `headers` (till exempel `Web` i en aktivitet) med typen `object` (Objekt). Den har ett standardvärde, vilket är samma värde som källfabrikens.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Alla egenskaper under sökvägen `typeProperties` parametriseras med respektive standardvärden. Det finns till exempel två egenskaper under `IntegrationRuntimes` typegenskaperna: `computeProperties` och `ssisProperties` . Båda egenskapstyperna skapas med respektive standardvärden och standardtyper (objekt).

#### <a name="triggers"></a>Utlösare

* Under `typeProperties` parametriseras två egenskaper. Den första är `maxConcurrency` , som anges ha ett standardvärde och är av typen `string` . Den har standardparameternamnet `<entityName>_properties_typeProperties_maxConcurrency` .
* Egenskapen `recurrence` är också parametriserad. Under den anges alla egenskaper på den nivån som parametriserade som strängar, med standardvärden och parameternamn. Ett undantag är egenskapen `interval` , som parametriseras som typen `int` . Parameternamnet har suffixet `<entityName>_properties_typeProperties_recurrence_triggerSuffix` . På samma sätt är `freq` egenskapen en sträng och parametriseras som en sträng. Egenskapen `freq` parametriseras dock utan ett standardvärde. Namnet förkortas och suffixet . Till exempel `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Länkade tjänster är unika. Eftersom länkade tjänster och datauppsättningar har en mängd olika typer kan du tillhandahålla typspecifik anpassning. I det här exemplet tillämpas en specifik mall `AzureDataLakeStore` för alla länkade tjänster av typen . För alla andra (via `*` ) tillämpas en annan mall.
* Egenskapen `connectionString` parametriseras som ett `securestring` värde. Det har inget standardvärde. Den får ett förkortat parameternamn med suffixet `connectionString` .
* Egenskapen råkar `secretAccessKey` vara en `AzureKeyVaultSecret` (till exempel i en länkad Amazon S3-tjänst). Den parametriseras automatiskt som en Azure Key Vault hemlighet och hämtas från det konfigurerade nyckelvalvet. Du kan också parametrisera själva nyckelvalvet.

#### <a name="datasets"></a>Datauppsättningar

* Även om typspecifik anpassning är tillgänglig för datauppsättningar kan du ange konfiguration utan att uttryckligen ha en \* konfiguration på -nivå. I föregående exempel parametriseras alla `typeProperties` datamängdsegenskaper under .


## <a name="best-practices-for-cicd"></a>Metodtips för CI/CD

Om du använder Git-integrering med din Azure Synapse-arbetsyta och har en CI/CD-pipeline som flyttar ändringarna från utveckling till testning och sedan till produktion, rekommenderar vi följande metodtips:

-   **Git-integrering**. Konfigurera endast din Azure Synapse med Git-integrering. Ändringar av test- och produktionsarbetsytor distribueras via CI/CD och behöver inte Git-integrering.
-   **Förbered pooler före artefaktmigrering.** Om du har ETT SQL-skript eller en notebook-fil som är kopplad till pooler på utvecklingsarbetsytan förväntas samma namn på pooler i olika miljöer. 
-   **Infrastruktur som kod (IaC).** Hantering av infrastruktur (nätverk, virtuella datorer, lastbalanserare och anslutningstopologi) i en beskrivande modell använder samma versionshantering som DevOps-teamet använder för källkod. 
-   **Andra**. Se [metodtips för ADF-artefakter](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd)

## <a name="troubleshooting-artifacts-deployment"></a>Felsöka distribution av artefakter 

### <a name="use-the-azure-synapse-analytics-workspace-deployment-task"></a>Använd distributionsuppgiften Azure Synapse Analytics arbetsyta

I Azure Synapse Analytics finns det ett antal artefakter som inte är ARM-resurser. Detta skiljer sig från Azure Data Factory. Distributionsuppgiften för ARM-mall fungerar inte korrekt för att distribuera Azure Synapse Analytics artefakter.
 
### <a name="unexpected-token-error-in-release"></a>Oväntat tokenfel i versionen

När parameterfilen har parametervärden som inte är rymna kommer lanseringspipelinen inte att parsa filen och genererar felet "oväntad token". Vi rekommenderar att du åsidosätter parametrar eller använder Azure Key Vault för att hämta parametervärden. Du kan också använda dubbla escape-tecken som en lösning.
