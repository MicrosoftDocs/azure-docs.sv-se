---
title: Kontinuerlig integrering med Azure Pipelines
description: Lär dig hur du kontinuerligt skapar, testar och distribuerar Azure Resource Manager mallar (ARM-mallar).
ms.date: 08/24/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 8e9f047497f493752947d8115084dcfe86f5e040
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588139"
---
# <a name="tutorial-continuous-integration-of-arm-templates-with-azure-pipelines"></a>Självstudie: kontinuerlig integrering av ARM-mallar med Azure-pipelines

I den [föregående själv studie kursen](./deployment-tutorial-linked-template.md)distribuerar du en länkad mall.  I den här självstudien får du lära dig hur du använder Azure pipelines för att kontinuerligt bygga och Distribuera Azure Resource Manager Template-projekt (ARM-mall).

Azure DevOps tillhandahåller utvecklartjänster som stöd för team för att planera arbete, samar beta med kod utveckling och bygga och distribuera program. Utvecklare kan arbeta i molnet med Azure DevOps Services. Azure DevOps tillhandahåller en integrerad uppsättning funktioner som du kan komma åt via webbläsaren eller IDE-klienten. Azure pipeline är en av dessa funktioner. Azure-pipelines är en helt aktuell tjänst för kontinuerlig integrering (CI) och kontinuerlig leverans (CD). Det fungerar med din prioriterade git-Provider och kan distribueras till de flesta större moln tjänster. Sedan kan du automatisera bygge, testning och distribution av koden till Microsoft Azure, Google Cloud Platform eller Amazon Web Services.

> [!NOTE]
> Välj ett projekt namn. När du går igenom självstudien ersätter du någon av **AzureRmPipeline** med ditt projekt namn.
> Det här projekt namnet används för att generera resurs namn.  En av resurserna är ett lagrings konto. Lagrings konto namn måste innehålla mellan 3 och 24 tecken och får inte innehålla siffror och gemener. Namnet måste vara unikt. I mallen är lagrings kontots namn det projekt namn där **Store** lagts till och projekt namnet måste innehålla mellan 3 och 11 tecken. Projekt namnet måste uppfylla kraven på lagrings kontots namn och innehålla färre än 11 tecken.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Förbereda en GitHub-lagringsplats
> * Skapa ett Azure DevOps-projekt
> * Skapa en Azure-pipeline
> * Verifiera pipeline-distributionen
> * Uppdatera mallen och distribuera om
> * Rensa resurser

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

* **Ett GitHub-konto** där du använder det för att skapa en lagrings plats för mallarna. Om du inte har något konto kan du [skapa ett utan kostnad](https://github.com). Mer information om hur du använder GitHub-databaser finns i [bygga GitHub-databaser](/azure/devops/pipelines/repos/github).
* **Installera git**. I den här självstudien används *git bash* eller *git-gränssnittet*. Anvisningar finns i [Installera git](https://www.atlassian.com/git/tutorials/install-git).
* **En Azure DevOps-organisation**. Om du inte har något konto kan du skapa ett utan kostnad. Se [skapa en organisation eller en projekt samling](/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* valfritt **Visual Studio Code med Resource Manager Tools-tillägg**. Se [snabb start: skapa arm-mallar med Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="prepare-a-github-repository"></a>Förbereda en GitHub-lagringsplats

GitHub används för att lagra projekt käll koden, inklusive Resource Manager-mallar. För andra databaser som stöds, se [databaser som stöds av Azure-DevOps](/azure/devops/pipelines/repos/?view=azure-devops).

### <a name="create-a-github-repository"></a>Skapa en GitHub-lagringsplats

Om du inte har ett GitHub-konto, se [krav](#prerequisites).

1. Logga in på [GitHub](https://github.com).
1. Välj din konto avbildning i det övre högra hörnet och välj sedan **dina databaser**.

    ![Azure Resource Manager Azure-pipeline för Azure DevOps skapa GitHub-lagringsplats](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-github-repository.png)

1. Välj **nytt**, en grön knapp.
1. I **databas namn** anger du ett namn på databasen.  Till exempel **AzureRmPipeline-lagrings platsen**. Kom ihåg att ersätta **AzureRmPipeline** med ditt projekt namn. Du kan välja antingen **offentlig** eller **privat** för att gå igenom den här självstudien. Och välj sedan **skapa lagrings plats**.
1. Skriv ner URL: en. URL: en för databasen har följande format: `https://github.com/[YourAccountName]/[YourRepositoryName]` .

Den här lagrings platsen kallas för en *fjärrlagringsplats*. Alla utvecklare av samma projekt kan klona sina egna *lokala lagrings* platser och sammanfoga ändringarna till fjärrlagringsplatsen.

### <a name="clone-the-remote-repository"></a>Klona fjärrlagringsplatsen

1. Öppna git-Shell eller git-bash. Se [krav](#prerequisites).
1. Kontrol lera att din aktuella mapp är **GitHub**.
1. Kör följande kommando:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateWebApp
    cd CreateWebApp
    pwd
    ```

    Ersätt `[YourAccountName]` med ditt GitHub-kontonamn och Ersätt `[YourGitHubRepositoryName]` med ditt databas namn som du skapade i föregående procedur.

Mappen _CreateWebApp_ är den mapp där mallen lagras. `pwd`Kommandot visar mappsökvägen. Sökvägen är den plats där du sparar mallen i följande procedur.

### <a name="download-a-quickstart-template"></a>Hämta en snabb starts mall

I stället för att skapa mallarna kan du hämta mallarna och spara dem i mappen _CreateWebApp_ .

* Huvud mal len: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
* Den länkade mallen: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

Både mappnamnet och fil namnen används som de är i pipelinen. Om du ändrar namnen måste du uppdatera namnen som används i pipelinen.

### <a name="push-the-template-to-the-remote-repository"></a>Skicka mallen till fjärrlagringsplatsen

_azuredeploy.jspå_ har lagts till i den lokala lagrings platsen. Sedan laddar du upp mallen till fjärrlagringsplatsen.

1. Öppna *git-Shell* eller *git-bash*, om den inte är öppen.
1. Ändra katalogen till mappen _CreateWebApp_ på din lokala lagrings plats.
1. Kontrol lera att _azuredeploy.js_ filen finns i mappen.
1. Kör följande kommando:

    ```bash
    git add .
    git commit -m "Add web app templates."
    git push origin main
    ```

    Du kan få en varning om LF. Du kan ignorera varningen. **main** är huvud grenen.  Du skapar vanligt vis en gren för varje uppdatering. För att förenkla självstudien använder du huvud grenen direkt.

1. Bläddra till GitHub-lagringsplatsen från en webbläsare. URL: en är `https://github.com/[YourAccountName]/[YourGitHubRepository]` . Du ser mappen _CreateWebApp_ och de tre filerna i mappen.
1. Välj _linkedStorageAccount.jspå_ för att öppna mallen.
1. Välj **RAW** -knappen. URL: en börjar med `https://raw.githubusercontent.com` .
1. Skapa en kopia av URL:en. Du måste ange det här värdet när du konfigurerar pipelinen senare i självstudien.

Hittills har du skapat en GitHub-lagringsplats och laddat upp mallarna till lagrings platsen.

## <a name="create-a-devops-project"></a>Skapa ett DevOps-projekt

En DevOps-organisation krävs innan du kan fortsätta till nästa procedur. Om du inte har en sådan, se [krav](#prerequisites).

1. Logga in på [Azure-DevOps](https://dev.azure.com).
1. Välj en DevOps organisation från vänster.

    ![Azure Resource Manager Azure-pipeline för Azure DevOps skapa Azure DevOps-projekt](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Välj **Nytt projekt**. Om du inte har några projekt öppnas sidan Skapa projekt automatiskt.
1. Ange följande värden:

    * **Projekt namn**: Ange ett projekt namn. Du kan använda det projekt namn som du valde i början av självstudien.
    * **Versions kontroll**: Välj **git**. Du kan behöva expandera **Avancerad** för att se **versions kontrollen**.

    Använd standardvärdet för de andra egenskaperna.
1. Välj **Skapa**.

Skapa en tjänst anslutning som används för att distribuera projekt till Azure.

1. Välj **projekt inställningar** längst ned på den vänstra menyn.
1. Välj **tjänst anslutningar** under **pipeliner**.
1. Välj **ny tjänst anslutning**, Välj **Azure Resource Manager** och välj sedan **Nästa**.
1. Välj **tjänstens huvud namn** och välj sedan **Nästa**.
1. Ange följande värden:

    * **Omfattnings nivå**: Välj **prenumeration**.
    * **Prenumeration**: Välj din prenumeration.
    * **Resurs grupp**: lämna det tomt.
    * **Anslutnings namn**: Ange ett anslutnings namn. Till exempel **AzureRmPipeline-ansluten**. Skriv ned det här namnet. du behöver namnet när du skapar din pipeline.
    * **Bevilja behörighet till alla pipeliner**. välja
1. Välj **Spara**.

## <a name="create-a-pipeline"></a>Skapa en pipeline

Tills nu har du slutfört följande uppgifter.  Om du hoppar över föregående avsnitt eftersom du är van vid GitHub och DevOps måste du slutföra uppgifterna innan du fortsätter.

* Skapa en GitHub-lagringsplats och spara mallarna i mappen _CreateWebApp_ i lagrings platsen.
* Skapa ett DevOps-projekt och skapa en Azure Resource Manager tjänst anslutning.

Så här skapar du en pipeline med ett steg för att distribuera en mall:

1. Välj **pipeliner** på den vänstra menyn.
1. Välj **ny pipeline**.
1. Välj **GitHub** på fliken **Connect** (Anslut). Om du uppmanas anger du dina GitHub-autentiseringsuppgifter och följer sedan anvisningarna. Om du ser följande skärm väljer du **endast Välj databaser** och kontrollerar att lagrings platsen finns i listan innan du väljer **Godkänn & installera**.

    ![Azure Resource Manager Azure DevOps Azure-pipeliner Välj bara databaser](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Välj din lagrings plats på fliken **Välj** . Standard namnet är `[YourAccountName]/[YourGitHubRepositoryName]` .
1. På fliken **Konfigurera** väljer du **starter pipeline**. Den visar pipeline-filen _Azure-pipelines. yml_ med två skript steg.
1. Ta bort de två skript stegen från _. yml_ -filen.
1. Flytta markören till raden efter **steg:**.
1. Välj **Visa assistenten** till höger på skärmen för att öppna fönstret **uppgifter** .
1. Välj **distribution av arm-mall**.
1. Ange följande värden:

    * **deploymentScope**: Välj **resurs grupp**. Mer information om omfattningarna finns i [distributions omfång](deploy-rest.md#deployment-scope).
    * **Azure Resource Manager anslutning**: Välj namnet på tjänst anslutningen som du skapade tidigare.
    * **Prenumeration**: Ange PRENUMERATIONS-ID för mål.
    * **Åtgärd**: Välj åtgärden **skapa eller uppdatera resurs grupp** utför 2 åtgärder-1. skapa en resurs grupp om ett nytt resurs grupps namn har angetts. 11.2. distribuera den angivna mallen.
    * **Resurs grupp**: Ange ett nytt resurs grupps namn. Till exempel **AzureRmPipeline-RG**.
    * **Plats**: Välj en plats för resurs gruppen, till exempel **centrala USA**.
    * **Mallens plats**: Välj **länkad artefakt**, vilket innebär att aktiviteten söker efter mallfilen direkt från den anslutna lagrings platsen.
    * **Mall**: ange _CreateWebApp/azuredeploy.jspå_. Om du har ändrat mappnamnet och fil namnet måste du ändra det här värdet.
    * **Mallparametrar**: lämna fältet tomt. Du kommer att ange parameter värden i **parametrarna för åsidosättning-mallen**.
    * **Åsidosätt mallparametrar**: ange `-projectName [EnterAProjectName] -linkedTemplateUri [EnterTheLinkedTemplateURL]` . Ersätt projekt namnet och den länkade mallens URL. Den länkade mallens URL är det du skrev ned i slutet av [skapa en GitHub-lagringsplats](#create-a-github-repository). Den börjar med `https://raw.githubusercontent.com` .
    * **Distributions läge**: Välj **stegvis**.
    * **Distributions namn**: ange **DeployPipelineTemplate**. Välj **Avancerat** innan du kan se **distributions namnet**.

    ![Skärm bild som visar distributions sidan för ARM-mallen med obligatoriska värden angivna.](./media/deployment-tutorial-pipeline/resource-manager-template-pipeline-configure.png)

1. Välj **Lägg till**.

    Mer information om uppgiften finns i [distributions uppgiften för Azure Resource Group](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)och [Azure Resource Manager för mall distribution](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)

    _. Yml_ -filen ser ut ungefär så här:

    ![Skärm bild som visar sidan Granska med den nya pipelinen granska din pipeline-YAML.](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-yml.png)

1. Välj **Spara och kör**.
1. I fönstret **Spara och köra** väljer du **Spara och kör** igen. En kopia av YAML-filen sparas i den anslutna lagrings platsen. Du kan se YAML-filen genom att bläddra till din lagrings plats.
1. Kontrol lera att pipelinen har körts.

    ![Azure Resource Manager Azure DevOps Azure pipelines yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Verifiera distributionen

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Öppna resurs gruppen. Namnet är det du angav i YAML-filen för pipelinen. Du ska se ett lagrings konto som skapats. Lagrings kontots namn börjar med **Store**.
1. Välj lagrings kontots namn för att öppna det.
1. Välj **Egenskaper**. Observera att **replikeringen** är **lokalt REDUNDANT lagring (LRS)**.

## <a name="update-and-redeploy"></a>Uppdatera och distribuera om

När du uppdaterar mallen och push-överför ändringarna till fjärrlagringsplatsen uppdaterar pipelinen automatiskt resurserna, lagrings kontot i det här fallet.

1. Öppna _linkedStorageAccount.jspå_ från din lokala lagrings plats i Visual Studio Code eller någon text redigerare.
1. Uppdatera **DefaultValue** för **storageAccountType** till **Standard_GRS**. Se följande skärmbild:

    ![Azure Resource Manager Azure DevOps Azure-pipeline uppdatera yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-update-yml.png)

1. Spara ändringarna.
1. Skicka ändringarna till fjärrlagringsplatsen genom att köra följande kommandon från git bash/shell.

    ```bash
    git pull origin main
    git add .
    git commit -m "Update the storage account type."
    git push origin main
    ```

    Det första kommandot ( `pull` ) synkroniserar den lokala lagrings platsen med fjärrlagringsplatsen. Pipelinen YAML-filen lades bara till i fjärrlagringsplatsen. Att köra `pull` kommandot laddar ned en kopia av yaml-filen till den lokala grenen.

    Det fjärde kommandot ( `push` ) laddar upp den ändrade _linkedStorageAccount.jspå_ filen till fjärrlagringsplatsen. När den primära grenen av fjärrlagringsplatsen har uppdaterats, utlöses pipelinen igen.

Du kan kontrol lera ändringarna genom att kontrol lera egenskapen replikering för lagrings kontot. Se [Verifiera distributionen](#verify-the-deployment).

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Från Azure Portal väljer du **resurs grupp** på den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **ta bort resurs grupp** på den översta menyn.

Du kanske också vill ta bort GitHub-lagringsplatsen och Azure DevOps-projektet.

## <a name="next-steps"></a>Nästa steg

Grattis, du har slutfört den här självstudien om distribution av Resource Manager-mallar. Berätta om du har kommentarer och förslag i feedback-avsnittet. Tack!
Du är redo att gå till mer avancerade koncept om mallar. Nästa självstudie innehåller mer information om hur du använder mallens referens dokumentation för att definiera vilka resurser som ska distribueras.

> [!div class="nextstepaction"]
> [Använda mallreferens](./template-tutorial-use-template-reference.md)
