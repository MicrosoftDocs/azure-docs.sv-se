---
title: Använd Azure-DevOps för att skapa en CI/CD-pipeline för ett Stream Analytics jobb
description: Den här artikeln beskriver hur du konfigurerar en pipeline för kontinuerlig integrering och distribution (CI/CD) för ett Azure Stream Analytics jobb i Azure DevOps
services: stream-analytics
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/10/2020
ms.openlocfilehash: b601a3586cfa971b2e8337a914f4e10bb0178ba0
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98014254"
---
# <a name="use-azure-devops-to-create-a-cicd-pipeline-for-a-stream-analytics-job"></a>Använd Azure-DevOps för att skapa en CI/CD-pipeline för ett Stream Analytics jobb

I den här artikeln får du lära dig att skapa en pipeline för att [bygga](/azure/devops/pipelines/get-started/pipelines-get-started) och [lansera](/azure/devops/pipelines/release/define-multistage-release-process) Azure DevOps med hjälp av Azure Stream Analytics CI/CD-verktyg.

## <a name="commit-your-stream-analytics-project"></a>Bekräfta Stream Analytics-projektet

Innan du börjar ska du spara dina fullständiga Stream Analytics-projekt som källfiler till en [Azure DevOps](/azure/devops/user-guide/source-control) -lagringsplats. Du kan referera till den här [exempel lagrings platsen](https://dev.azure.com/wenyzou/azure-streamanalytics-cicd-demo) och [Stream Analytics projekt käll kod](https://dev.azure.com/wenyzou/_git/azure-streamanalytics-cicd-demo?path=%2FmyASAProject) i Azure-pipeliner.

Stegen i den här artikeln använder ett Stream Analytics Visual Studio Code-projekt. Om du använder ett Visual Studio-projekt följer du stegen i [Automatisera versioner, tester och distributioner av ett Azure Stream Analytics jobb med hjälp av CI/CD-verktyg](cicd-tools.md).

## <a name="create-a-build-pipeline"></a>Skapa en bygg-pipeline

I det här avsnittet får du lära dig hur du skapar en pipeline för bygge. Du kan referera till det här exemplet [Auto build and test pipeline](https://dev.azure.com/wenyzou/_git/azure-streamanalytics-cicd-demo?path=%2FmyASAProject) i Azure DevOps.

1. Öppna en webbläsare och navigera till projektet i Azure DevOps.  

1. Under **pipeliner** i den vänstra navigerings menyn väljer du **versioner**. Välj sedan **ny pipeline**.

   :::image type="content" source="media/set-up-cicd-pipeline/new-pipeline.png" alt-text="Skapa ny Azure-pipeline":::

1. Välj **Använd den klassiska redigeraren** för att skapa en pipeline utan yaml.

1. Välj käll typ, team projekt och lagrings plats. Välj sedan **Fortsätt**.

   :::image type="content" source="media/set-up-cicd-pipeline/select-repo.png" alt-text="Välj Azure Stream Analytics projekt":::

1. På sidan **Välj en mall** väljer du **tomt jobb**.

## <a name="install-npm-package"></a>Installera NPM-paket

1. På sidan **aktiviteter** väljer du plus tecknet bredvid **Agent jobb 1**. Ange *NPM* i uppgifts sökningen och välj **NPM**.

   :::image type="content" source="media/set-up-cicd-pipeline/search-npm.png" alt-text="Välj NPM-aktivitet":::

2. Ge uppgiften ett **visnings namn**. Ändra **kommando** alternativet till *Custom* och ange följande kommando i **kommando och argument**. Lämna kvar de återstående standard alternativen.

   ```bash
   install -g azure-streamanalytics-cicd
   ```

   :::image type="content" source="media/set-up-cicd-pipeline/npm-config.png" alt-text="Ange konfigurationer för NPM-aktivitet":::

## <a name="add-a-build-task"></a>Lägg till en build-uppgift

1. På sidan **variabler** väljer du **+ Lägg till** i **pipeline-variabler**. Lägg till följande variabler. Ange följande värden enligt din preferens:

   |Variabelnamn|Värde|
   |-|-|
   |projectRootPath|YourProjectName|
   |outputPath|Resultat|
   |deployPath|Distribuera|

2. På sidan **aktiviteter** väljer du plus tecknet bredvid **Agent jobb 1**. Sök efter **kommando rad**.

3. Ge uppgiften ett **visnings namn** och ange följande skript. Ändra skriptet med ditt databas namn och projekt namn.

   ```bash
   azure-streamanalytics-cicd build -project $(projectRootPath)/asaproj.json -outputpath $(projectRootPath)/$(outputPath)/$(deployPath)
   ```

   I bilden nedan används ett Stream Analytics Visual Studio Code-projekt som exempel.

   :::image type="content" source="media/set-up-cicd-pipeline/command-line-config-build.png" alt-text="Ange konfigurationer för kommando rads aktivitet Visual Studio Code":::

## <a name="add-a-test-task"></a>Lägg till en test aktivitet

1. På sidan **variabler** väljer du **+ Lägg till** i **pipeline-variabler**. Lägg till följande variabler. Ändra värdena med din sökväg för utdata och ditt databas namn.

   |Variabelnamn|Värde|
   |-|-|
   |testPath|Testa|

   :::image type="content" source="media/set-up-cicd-pipeline/pipeline-variables-test.png" alt-text="Lägg till pipeline-variabler":::

2. På sidan **aktiviteter** väljer du plus tecknet bredvid **Agent jobb 1**. Sök efter **kommando rad**.

3. Ge uppgiften ett **visnings namn** och ange följande skript. Ändra skriptet med projekt filens namn och sökvägen till test konfigurations filen. 

   Se [automatiska test instruktioner](cicd-tools.md#automated-test) för mer information om hur du lägger till och konfigurerar test ärenden.

   ```bash
   azure-streamanalytics-cicd test -project $(projectRootPath)/asaproj.json -outputpath $(projectRootPath)/$(outputPath)/$(testPath) -testConfigPath $(projectRootPath)/test/testConfig.json 
   ```

   :::image type="content" source="media/set-up-cicd-pipeline/command-line-config-test.png" alt-text="Ange konfigurationer för kommando rads aktivitet":::

## <a name="add-a-copy-files-task"></a>Lägg till en kopiera filer-aktivitet

Du måste lägga till en kopiera fil-aktivitet för att kopiera test sammanfattnings filen och Azure Resource Manager mallfiler till mappen artefakt. 

1. På sidan **aktiviteter** väljer du **+** bredvid **Agent jobb 1**. Sök efter **Kopiera filer**. Ange sedan följande konfigurationer. Genom `**` att tilldela till **innehåll** kopieras alla filer med test resultaten.

   |Parameter|Indata|
   |-|-|
   |Visningsnamn|Kopiera filer till: $ (build. artifactstagingdirectory)|
   |Källmapp|`$(system.defaultworkingdirectory)/$(outputPath)/`|
   |Innehåll| `**` |
   |Målmapp| `$(build.artifactstagingdirectory)`|

2. Expandera **kontroll alternativ**. Välj **även om en föregående aktivitet har misslyckats, såvida inte versionen har avbrutits** i **kör den här uppgiften**.

   :::image type="content" source="media/set-up-cicd-pipeline/copy-config.png" alt-text="Ange konfigurationer för kopierings uppgift":::

## <a name="add-a-publish-build-artifacts-task"></a>Lägg till aktiviteten publicera Bygg artefakter

1. På sidan **aktiviteter** väljer du plus tecknet bredvid **Agent jobb 1**. Sök efter **publicera versions artefakter** och välj alternativet med den svarta pilens ikon.

2. Expandera **kontroll alternativ**. Välj **även om en föregående aktivitet har misslyckats, såvida inte versionen har avbrutits** i **kör den här uppgiften**.

   :::image type="content" source="media/set-up-cicd-pipeline/publish-config.png" alt-text="Ange konfigurationer för publicerings uppgift":::

## <a name="save-and-run"></a>Spara och kör

När du är färdig med att lägga till NPM-paketet, kommando raden, kopiera filer och publicera Bygg artefakter, väljer du **spara & kö**. När du uppmanas till det anger du en Spara kommentar och väljer **Spara och kör**. Du kan hämta test resultatet från **sammanfattnings** sidan för pipelinen.

## <a name="check-the-build-and-test-results"></a>Kontrol lera build-och test resultaten

Du hittar test sammanfattnings filen och Azure Resource Manager mallfiler i den **publicerade** mappen.

   :::image type="content" source="media/set-up-cicd-pipeline/check-build-test-result.png" alt-text="Kontrol lera build-och test resultat":::

   :::image type="content" source="media/set-up-cicd-pipeline/check-drop-folder.png" alt-text="Kontrol lera artefakter":::

## <a name="release-with-azure-pipelines"></a>Version med Azure-pipeline

I det här avsnittet får du lära dig hur du skapar en versions pipeline. Du kan referera till den här exempel [lanserings pipelinen](https://dev.azure.com/wenyzou/azure-streamanalytics-cicd-demo/_release?_a=releases&view=mine&definitionId=2&preserve-view=true) i Azure DevOps.

Öppna en webbläsare och gå till ditt Azure Stream Analytics Visual Studio Code-projekt.

1. Under **pipeliner** i den vänstra navigerings menyn väljer du **versioner**. Välj sedan **ny pipeline**.

2. Välj **starta med ett tomt jobb**.

3. I rutan **artefakter** väljer du **+ Lägg till en artefakt**. Under **källa** väljer du den versions pipeline som du skapade och väljer **Lägg till**.

   :::image type="content" source="media/set-up-cicd-pipeline/build-artifact.png" alt-text="Ange artefakt för bygg pipeline":::

4. Ändra namnet på **steg 1** för att **distribuera jobb till test miljön**.

5. Lägg till ett nytt stadium och namnge det **för att distribuera jobb till produktions miljön**.

### <a name="add-deploy-tasks"></a>Lägg till distributions uppgifter

1. I list rutan aktiviteter väljer **du distribuera jobb för att testa miljön**.

2. Välj **+** Nästa **Agent jobb** och Sök efter distribution av **arm-mallar**. Ange följande parametrar:

   |Parameter|Värde|
   |-|-|
   |Visningsnamn| *Distribuera myASAProject*|
   |Azure-prenumeration| Välj din prenumeration.|
   |Åtgärd| *Skapa eller uppdatera resursgrupp*|
   |Resursgrupp| Välj ett namn för den test resurs grupp som ska innehålla ditt Stream Analytics jobb.|
   |Plats|Välj platsen för test resurs gruppen.|
   |Mallens plats| Länkad artefakt|
   |Mall| $ (System. DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demo-CI-Deploy/Drop/myASAProject.JobTemplate.jspå |
   |Mallparametrar|$ (System. DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demo-CI-Deploy/Drop/myASAProject.JobTemplate.parameters.jspå |
   |Åsidosätt mallparametrar|-<arm_template_parameter> "ditt värde". Du kan definiera parametrar med hjälp av **variabler**.|
   |Distributions läge|Inkrementellt|

3. I list rutan aktiviteter väljer **du distribuera jobb till produktions miljön**.

4. Välj **+** Nästa **Agent jobb** och Sök efter distribution av *arm-mallar*. Ange följande parametrar:

   |Parameter|Värde|
   |-|-|
   |Visningsnamn| *Distribuera myASAProject*|
   |Azure-prenumeration| Välj din prenumeration.|
   |Åtgärd| *Skapa eller uppdatera resursgrupp*|
   |Resursgrupp| Välj ett namn för den produktions resurs grupp som ska innehålla ditt Stream Analytics jobb.|
   |Plats|Välj platsen för produktions resurs gruppen.|
   |Mallens plats| *Länkad artefakt*|
   |Mall| $ (System. DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demo-CI-Deploy/Drop/myASAProject.JobTemplate.jspå |
   |Mallparametrar|$ (System. DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demo-CI-Deploy/Drop/myASAProject.JobTemplate.parameters.jspå |
   |Åsidosätt mallparametrar|-<arm_template_parameter> "ditt värde"|
   |Distributions läge|Inkrementellt|

### <a name="create-a-release"></a>Skapa en version

Om du vill skapa en version väljer du **Skapa version** i det övre högra hörnet.

:::image type="content" source="media/set-up-cicd-pipeline/create-release.png" alt-text="Skapa en version med hjälp av Azure-pipeliner":::

## <a name="next-steps"></a>Nästa steg

* [Kontinuerlig integrering och kontinuerlig distribution för Azure Stream Analytics](cicd-overview.md)
* [Automatisera skapande, testning och distribution av ett Azure Stream Analytics jobb med CI/CD-verktyg](cicd-tools.md)