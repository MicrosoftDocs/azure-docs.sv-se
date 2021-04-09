---
title: Distribuera ett behållar program med CI/CD
description: I den här självstudiekursen får du lära dig hur du ställer in kontinuerlig integrering och distribution för ett Azure Service Fabric-containerprogram med hjälp av Visual Studio Azure DevOps.
ms.topic: tutorial
ms.date: 08/29/2018
ms.custom: mvc
ms.openlocfilehash: bb0eb9226a99f139ff10a8da12a1e22017536c67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018859"
---
# <a name="tutorial-deploy-a-container-application-with-cicd-to-a-service-fabric-cluster"></a>Självstudie: Distribuera ett containerprogram med CI/CD till ett Service Fabric-kluster

Den här självstudiekursen är del två i en serie. Du får lära dig hur du konfigurerar kontinuerlig integrering och distribution för ett Azure Service Fabric-containerprogram med hjälp av Visual Studio och Azure DevOps.  Ett befintligt Service Fabric-program krävs. Det som skapas i [Distribuera ett .NET-program i en Windows-container till Azure Service Fabric](service-fabric-host-app-in-a-container.md) används som exempel.

I del två i serien lär du dig hur du:

> [!div class="checklist"]
> * lägga till källkontroll i projektet
> * Skapa en byggesdefinition i Visual Studio Team Explorer
> * Skapa en versionsdefinition i Visual Studio Team Explorer
> * distribuera och uppgradera ett program automatiskt.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien:

* ha ett kluster i Azure, eller [skapa ett via den här självstudiekursen](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Distribuera ett program i en container till det](service-fabric-host-app-in-a-container.md).

## <a name="prepare-a-publish-profile"></a>Förbered publiceringsprofil

Nu när du har [distribuerat ett containerprogram](service-fabric-host-app-in-a-container.md) är du redo att konfigurera kontinuerlig integrering.  Börja med att förbereda en publiceringsprofil i programmet för användning med distributionsprocessen som körs i Azure DevOps Services.  Publiceringsprofilen ska konfigureras med inriktning på det kluster som du skapade tidigare.  Starta Visual Studio och öppna ett befintligt Service Fabric-programprojekt.  I **Solution Explorer** högerklickar du på programmet och väljer **Publicera**.

Välj en målprofil inom programprojektet att använda för arbetsflödet för den kontinuerliga integreringen, till exempel molnet.  Specificera klusteranslutningsslutpunkten.  Markera kryssrutan **Uppgradera programmet** så att programmet uppgraderas för varje distribution i Azure DevOps.  Klicka på hyperlänken **Spara** och spara inställningarna i profilen. Klicka sedan på **Avbryt** och stäng dialogrutan.

![Push-profil][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-azure-devops-git-repo"></a>Dela Visual Studio-lösningen till en ny Azure DevOps Git-lagringsplats

Dela programkällfilerna till ett teamprojekt i Azure DevOps så att du kan generera byggen.

Skapa en ny lokal git-lagrings platsen för projektet genom att välja **Lägg till i käll kontroll**  ->  **git** i statusfältet i det nedre högra hörnet av Visual Studio.

I vyn för **Push-överföring** i **Team Explorer** väljer du knappen för att **publicera Git-lagringsplatsen** under alternativet för att **push-överföra till Azure DevOps**.

![Skärm bild av fönstret team Explorer-synkronisering i Visual Studio. Under push to Azure DevOps markeras knappen publicera till git lagrings platsen.][push-git-repo]

Verifiera din e-postadress och välj din organisation i listrutan **Konto**. Du kan behöva konfigurera en organisation om du inte redan har en. Skriv in lagringsplatsens namn och välj **Publicera lagringsplats**.

![Skärm bild av fönstret för att skicka till Azure-DevOps. Inställningarna för e-post, konto, namn på lagrings plats och knappen publicera databas är markerade.][publish-code]

När du publicerar lagringsplatsen skapas ett nytt teamprojekt i kontot med samma namn som den lokala lagringsplatsen. Om du vill skapa lagringsplatsen i ett befintlig teamprojekt klickar du på **Avancerat** bredvid **lagringsplatsens namn** och väljer ett teamprojekt. Du kan visa koden på webben genom att välja alternativet för att **visa på webben**.

## <a name="configure-continuous-delivery-with-azure-pipelines"></a>Konfigurera kontinuerlig leverans med Azure Pipelines

En byggesdefinition för Azure DevOps beskriver ett arbetsflöde som består av en uppsättning byggesåtgärder som körs sekventiellt. Skapa en byggesdefinition som producerar ett Service Fabric-programpaket och andra artefakter att distribuera till ett Service Fabric-kluster. Läs mer om [byggesdefinitioner](https://www.visualstudio.com/docs/build/define/create) för Azure DevOps. 

En versionsdefinition för Azure DevOps beskriver ett arbetsflöde som distribuerar ett programpaket till ett kluster. När de används tillsammans utför byggesdefinitionen och versionsdefinitionen hela arbetsflödet med början på källfiler för att sluta med ett program som körs i klustret. Läs mer om [versionsdefinitioner](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition) för Azure DevOps.

### <a name="create-a-build-definition"></a>Skapa en byggesdefinition

Öppna det nya teamprojektet genom att gå till https://dev.azure.com i en webbläsare och välja din organisation, följt av det nya projektet. 

Välj alternativet **Pipelines** i den vänstra panelen och klicka sedan på **Ny pipeline**.

>[!NOTE]
>Om du inte ser mallen för build-definition ska du se till att **funktionen för att skapa en ny YAML-pipeline** är avstängd. Den här funktionen har konfigurerats i avsnittet **Förhandsversionsfunktioner** i ditt DevOps-konto.

![Ny pipeline][new-pipeline]

Välj **Azure Repos Git** som källa, teamprojektnamnet, projektets lagringsplats samt standardgrenen **master** eller manuella och schemalagda byggen.  Klicka sedan på **Fortsätt**.

I **Select a template** (Välj en mall) väljer du mallen **Azure Service Fabric application with Docker support** (Azure Service Fabric-program med Docker-support) och klickar på **Apply** (Använd).

![Välj byggesmall][select-build-template]

I **Tasks** (Uppgifter) väljer du **Hosted VS2017** som **Agent pool** (Agentpool).

![Välj uppgifter][task-agent-pool]

Klicka på **Tagga avbildningar**.

I **Container Registry Type** (Containerregistertyp) väljer du **Azure Container Registry** (Azure-containerregister). Välj en **Azure-prenumeration** och klicka sedan på **Authorize** (Auktorisera). Välj ett **Azure-containerregister**.

![Välj Docker Tag images (Docker Tagga avbildningar)][select-tag-images]

Klicka på **Push images** (Push-överför avbildningar).

I **Container Registry Type** (Containerregistertyp) väljer du **Azure Container Registry** (Azure-containerregister). Välj en **Azure-prenumeration** och klicka sedan på **Authorize** (Auktorisera). Välj ett **Azure-containerregister**.

![Välj Docker Push images (Docker Push-överför avbildningar)][select-push-images]

På fliken **Utlösare** aktiverar du kontinuerlig integrering genom att markera **Aktivera kontinuerlig integrering**. I **Branch filters** (Grenfilter) klickar du på **+ Lägg till** så får **Branch specification** (Grenspecifikation) standardvärdet **master**.

I **dialogrutan för att spara bygg-pipeline och kö** klickar du på **Save & queue** (Spara och köa) för att manuellt påbörja en version.

![Välj utlösare][save-and-queue]

Byggen utlöser också vid push och incheckning. Om du vill kontrol lera Bygg förloppet växlar du till fliken **versioner** .  När du har kontrollerat att genereringen körs korrekt definierar du en versions definition som distribuerar ditt program till ett kluster.

### <a name="create-a-release-definition"></a>Skapa en versionsdefinition

Välj alternativet **Pipelines** i den vänstra panelen och välj sedan **Versioner** följt av **+ Ny pipeline**.  I **Välj en mall** väljer du mallen **Azure Service Fabric Deployment** på listan och sedan **Använd**.

![Välj versionsmall][select-release-template]

Välj **Tasks** (Uppgifter), **Environment 1** (Miljö 1) och sedan **+New** (+Ny) för att lägga till en ny klusteranslutning.

![Lägga till klusteranslutning][add-cluster-connection]

I vyn för att **lägga till nya Service Fabric-anslutningar** väljer du autentisering av typerna **Certifikatbaserad** eller **Azure Active Directory**.  Ange ett anslutningsnamn för mysftestcluster och en klusterslutpunkt för tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000 (eller slutpunkten i klustret som du distribuerar till).

Om du använder certifikatbaserad autentisering lägger du till **servercertifikatets tumavtryck** för det servercertifikat som används för att skapa klustret.  I **klientcertifikat** lägger du till base-64-kodningen för klientcertifikatfilen. Det finns ett hjälppopupfönster för fältet om du behöver information om hur du hittar den base-64-kodade representationen för certifikatet. Lägg också till **lösenordet** för certifikatet.  Du kan använda certifikatet för klustret eller servern om du inte har något separat klientcertifikat.

För Azure Active Directory-autentiseringsuppgifter lägger du till **servercertifikatets tumavtryck** för det servercertifikatet som användes för att skapa klustret och de autentiseringsuppgifter du vill använda för att ansluta till klustret i fälten **Användarnamn** och **Lösenord**.

Klicka på **Lägg till** och spara klusteranslutningen.



Under Agent Phase (Agentfas) klickar du på **Deploy Service Fabric Application** (Distribuera Service Fabric-program).
Klicka på **Docker Settings** (Docker-inställningar) och klicka sedan på **Configure Docker settings** (Konfigurera Docker-inställningar). I **Registry Credentials Source** (Källa för registerautentiseringsuppgifter) väljer du **Azure Resource Manager Service Connection** (Anslutning för Azure Resource Manager-tjänst). Välj sedan din **Azure-prenumeration**.

![Agent för versionspipeline][release-pipeline-agent]

Lägg sedan till en byggesartefakt i pipelinen så versionsdefinitionen kan hitta utdata från bygget. Välj **Pipeline** och **artefakter**->**+ Lägg till**.  I **Source (Build definition)** (Källa (byggesdefinition)) väljer du den byggesdefinition som du skapade tidigare.  Klicka på **Lägg till** för att spara byggesartefakten.

![Lägg till artefakt][add-artifact]

Aktivera en kontinuerlig distributionsutlösare så att versionen automatiskt skapas när bygget har slutförts. Klicka på blixtikonen i artefakten, aktivera utlösaren och klicka på **Spara** för att spara versionsdefinitionen.

![Aktivera utlösare][enable-trigger]

Välj **+ släpp**  ->  **skapa en version**  ->  **skapa** för att skapa en version manuellt. Du kan övervaka versionsförloppet på fliken **Releases** (Versioner).

Kontrollera att distributionen har slutförts och programmet körs i klustret.  Öppna en webbläsare och gå till `http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/`.  Observera programversionen. I det här exemplet är den 1.0.0.20170616.3.

## <a name="commit-and-push-changes-trigger-a-release"></a>Genomför och push-överföring av ändringar utlöser en släppning

För att kontrollera att pipelinen för den kontinuerliga integreringen fungerar genom att kontrollera kodändringar i Azure DevOps.

När du skriver koden spåras dina ändringar automatiskt av Visual Studio. Genomför ändringar av den lokala Git-lagringsplatsen genom att välja ikonen väntande ändringar (![Ikonen för väntande ändringar visar en penna och en siffra.][pending]) från statusfältet i nederkant högra hörnet.

I vyn **Ändringar** i Team Explorer lägger du till ett meddelande som beskriver uppdateringen. Spara sedan ändringarna.

![Genomför alla][changes]

Välj statusfältikonen för de opublicerade ändringarna (![Opublicerade ändringar][unpublished-changes]) eller vyn Synkronisera i Team Explorer. Välj **Push** (Push-överföring) för att uppdatera koden i Azure DevOps.

![Push-överföring av ändringar][push]

När du skickar ändringar till Azure DevOps via push-överföring utlöses ett bygge automatiskt.  När byggesdefinition har slutförts skapas en version automatiskt och börjar uppgradera programmet i klustret.

Om du vill kontrollera förloppet för bygget kan du byta till fliken **Builds** (Byggen) i **Team Explorer** i Visual Studio.  När du har kontrollerat att bygget körs rätt kan du definiera en versionsdefinition som distribuerar programmet till ett kluster.

Kontrollera att distributionen har slutförts och programmet körs i klustret.  Öppna en webbläsare och gå till `http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/`.  Observera programversionen. I det här exemplet är den 1.0.0.20170815.3.

![Skärm bild av röstnings appen i Service Fabric Explorer. På fliken Essentials är app-versionen "1.0.0.20170815.3" markerad.][sfx1]

## <a name="update-the-application"></a>Uppdatera programmet

Göra kodändringar i programmet.  Spara och genomföra ändringarna via de här stegen.

När programuppgraderingen påbörjas kan du visa uppgraderingsförloppet i Service Fabric Explorer:

![Skärm bild av röstnings appen i Service Fabric Explorer. Meddelandet "uppgradering pågår" är markerat och appens status är "uppgradering".][sfx2]

Programuppgraderingen kan ta flera minuter. När uppgraderingen har slutförts kör programmet nästa version.  I det här exemplet 1.0.0.20170815.4.

![Skärm bild av röstnings appen i Service Fabric Explorer. På fliken Essentials är den uppdaterade program versionen "1.0.0.20170815.4" markerad.][sfx3]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * lägga till källkontroll i projektet
> * Skapa en byggesdefinition
> * Skapa en versionsdefinition
> * distribuera och uppgradera ett program automatiskt.

I nästa del av kursen lär du dig att konfigurera [övervakning för containern](service-fabric-tutorial-monitoring-wincontainers.md).

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishCode.png
[new-pipeline]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewPipeline.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SelectBuildTemplate.png
[task-agent-pool]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/TaskAgentPool.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SaveAndQueue.png
[select-tag-images]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/DockerTagImages.png
[select-push-images]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/DockerPushImages.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/AddClusterConnection.png
[release-pipeline-agent]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/ReleasePipelineAgent.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Push.png
[continuous-delivery-with-VSTS]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewServiceEndpointDialog.png
