---
title: Distribuera en app med CI-och Azure-pipeliner
description: I den här självstudien får du lära dig hur du ställer in kontinuerlig integrering och distribution för ett Service Fabric-program med hjälp av Azure Pipelines.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: a26cfaca466e01b154c65b27895f3004f6320e5d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91326345"
---
# <a name="tutorial-deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>Självstudie: Distribuera ett program med CI/CD till ett Service Fabric-kluster

Den här självstudien är del fyra i en serie. Du får du lära dig hur du ställer in kontinuerlig integrering och distribution för ett Azure Service Fabric-program med hjälp av Azure Pipelines.  En befintlig Service Fabric-tillämpning krävs. Den som skapas i [Bygga ett .NET-program](service-fabric-tutorial-create-dotnet-app.md) används som exempel.

I den tredje delen i serien får du lära dig att:

> [!div class="checklist"]
> * lägga till källkontroll i projektet
> * Skapa en bygg-pipeline i Azure Pipelines
> * Skapa en versionspipeline i Azure Pipelines
> * distribuera och uppgradera ett program automatiskt.

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Bygga ett .NET Service Fabric-program](service-fabric-tutorial-create-dotnet-app.md)
> * [Distribuera programmet till ett fjärran slutet kluster](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Lägga till en HTTPS-slutpunkt i en klienttjänst i ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * Konfigurera CI/CD med hjälp av Azure Pipelines
> * [Konfigurera övervakning och diagnostik för programmet](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien:

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Installera Visual Studio 2019](https://www.visualstudio.com/) och installera arbets belastningarna **Azure Development** och **ASP.net och webb utveckling** .
* [Installera Service Fabric SDK](service-fabric-get-started.md)
* Skapa ett Windows Service Fabric-kluster i Azure, till exempel genom att [följa den här självstudiekursen](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Skapa en [Azure DevOps-organisation](/azure/devops/organizations/accounts/create-organization-msa-or-work-student). På så sätt kan du skapa ett projekt i Azure DevOps och använda Azure Pipelines.

## <a name="download-the-voting-sample-application"></a>Ladda ned exempelprogrammet Röstning

Om du inte byggde exempelprogrammet Röstning i [del ett av självstudiekursen](service-fabric-tutorial-create-dotnet-app.md) kan du ladda ned det. Kör följande kommando i ett kommandofönster för att klona databasen för exempelappen till den lokala datorn.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="prepare-a-publish-profile"></a>Förbered publiceringsprofil

Nu när du har [skapat ett program](service-fabric-tutorial-create-dotnet-app.md) och [distribuerat programmet till Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md) är du redo att konfigurera kontinuerlig integrering.  Börja med att förbereda en publiceringsprofil i programmet för användning med distributionsprocessen som körs i Azure Pipelines.  Publiceringsprofilen ska konfigureras med inriktning på det kluster som du skapade tidigare.  Starta Visual Studio och öppna ett befintligt Service Fabric-programprojekt.  I **Solution Explorer** högerklickar du på programmet och väljer **Publicera**.

Välj en målprofil inom programprojektet att använda för arbetsflödet för den kontinuerliga integreringen, till exempel molnet.  Specificera klusteranslutningsslutpunkten.  Markera kryssrutan **Uppgradera programmet** så att programmet uppgraderas för varje distribution i Azure DevOps.  Klicka på hyperlänken **Spara** och spara inställningarna i profilen. Klicka sedan på **Avbryt** och stäng dialogrutan.

![Push-profil][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-azure-devops-git-repo"></a>Dela Visual Studio-lösningen till en ny Azure DevOps Git-lagringsplats

Dela programkällfilerna till ett projekt i Azure DevOps så att du kan generera byggen.

Skapa en ny lokal git-lagrings platsen för projektet genom att välja **Lägg till i käll kontroll**  ->  **git** i statusfältet i det nedre högra hörnet av Visual Studio.

I vyn för **Push-överföring** i **Team Explorer** väljer du knappen för att **publicera Git-lagringsplatsen** under alternativet för att **push-överföra till Azure DevOps**.

![Skärm bild av fönstret team Explorer-synkronisering i Visual Studio. Knappen publicera till git-lagrings platsen är markerad under push to Azure DevOps.][push-git-repo]

Verifiera din e-postadress och välj ditt konto i listrutan **Azure DevOps-domän**. Skriv in lagringsplatsens namn och välj **Publicera lagringsplats**.

![Skärm bild av inställningarna för att skicka till Azure-DevOps med knappen e-post, konto, lagrings namn och publicerings plats markerad.][publish-code]

När du publicerar lagringsplatsen skapas ett nytt projekt i ditt konto med samma namn som den lokala lagringsplatsen. Om du vill skapa lagringsplatsen i ett befintligt projekt klickar du på **Avancerat** bredvid **namnet på databasen** och väljer ett projekt. Du kan visa koden på webben genom att välja alternativet för att **visa på webben**.

## <a name="configure-continuous-delivery-with-azure-pipelines"></a>Konfigurera kontinuerlig leverans med Azure Pipelines

En pipeline för Azure-pipeliner beskriver ett arbets flöde som består av en uppsättning Bygg steg som körs sekventiellt. Skapa en bygg-pipeline som producerar ett Service Fabric-programpaket och andra artefakter att distribuera till ett Service Fabric-kluster. Läs mer om [Azure Pipelines bygg-pipelines](https://www.visualstudio.com/docs/build/define/create). 

En versionspipeline för Azure Pipelines beskriver ett arbetsflöde som distribuerar ett programpaket till ett kluster. När de används tillsammans kör bygg-pipelinen och versionspipelinen hela arbetsflödet med början på källfiler och slutar med ett program som körs i klustret. Lär dig mer om [Azure pipelines release-pipelines](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition).

### <a name="create-a-build-pipeline"></a>Skapa en bygg-pipeline

Öppna en webbläsare och navigera till det nya projektet på: [https://&lt;myaccount&gt;.visualstudio.com/Voting/Voting%20Team/_git/Voting](https://myaccount.visualstudio.com/Voting/Voting%20Team/_git/Voting).

Välj fliken **Pipelines**, därefter **Builds** (Byggen) och klicka sedan på **Ny pipeline**.

![Ny pipeline][new-pipeline]

Välj **Azure Repos Git** som källa, teamprojektet **Voting** (Röstning), lagringsplatsen **Voting** och standardgrenen **master** eller manuella och schemalagda byggen.  Klicka sedan på **Fortsätt**.

![Välj lagringsplats][select-repo]

I **Välj en mall** väljer du program mal len **Azure Service Fabric** och klickar på **Använd**.

![Välj byggesmall][select-build-template]

I **Uppgifter** anger du ”Hosted VS2017” som **agentpool**.

![Välj uppgifter][save-and-queue]

Under **Utlösare** aktiverar du kontinuerlig integrering genom att markera **Aktivera kontinuerlig integrering**. I **Branch filters** (Grenfilter) får **Branch specification** (Grenspecifikation) standardvärdet **master**. Välj alternativet för att **spara och köa** för att manuellt starta en version.

![Välj utlösare][save-and-queue2]

Byggen utlöser också vid push och incheckning. Om du vill kontrol lera Bygg förloppet växlar du till fliken **versioner** .  När du har kontrollerat att genereringen körs korrekt definierar du en versions pipeline som distribuerar ditt program till ett kluster.

### <a name="create-a-release-pipeline"></a>Skapa en versionspipeline

Välj fliken **Pipelines** och sedan **Versioner** och **+Ny pipeline**.  I **Välj en mall** väljer du mallen **Azure Service Fabric Deployment** på listan och sedan **Använd**.

![Välj versionsmall][select-release-template]

Välj **uppgifter** -> **miljö 1** och sedan **+ ny** för att lägga till en ny kluster anslutning.

![Lägga till klusteranslutning][add-cluster-connection]

I vyn för att **lägga till nya Service Fabric-anslutningar** väljer du autentisering av typerna **Certifikatbaserad** eller **Azure Active Directory**.  Ange ett anslutningsnamn för mysftestcluster och en klusterslutpunkt för tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000 (eller slutpunkten i klustret som du distribuerar till).

För certifikatbaserad autentisering lägger du till **Server certifikatets tumavtryck** för det Server certifikat som användes för att skapa klustret.  I **klientcertifikat** lägger du till base-64-kodningen för klientcertifikatfilen. Det finns ett hjälppopupfönster för fältet om du behöver information om hur du hittar den base-64-kodade representationen för certifikatet. Lägg också till **lösenordet** för certifikatet.  Du kan använda certifikatet för klustret eller servern om du inte har något separat klientcertifikat.

För Azure Active Directory-autentiseringsuppgifter lägger du till **servercertifikatets tumavtryck** för det servercertifikatet som användes för att skapa klustret och de autentiseringsuppgifter du vill använda för att ansluta till klustret i fälten **Användarnamn** och **Lösenord**.

Klicka på **Lägg till** och spara klusteranslutningen.

Lägg sedan till en byggartefakt i pipelinen så versionspipelinen kan hitta utdata från bygget. Välj **Pipeline** och **artefakter**->**+ Lägg till**.  I **Source (Build definition)** (Källa (byggesdefinition)) väljer du den bygg-pipeline som du skapade tidigare.  Klicka på **Lägg till** för att spara byggesartefakten.

![Lägg till artefakt][add-artifact]

Aktivera en kontinuerlig distributionsutlösare så att versionen automatiskt skapas när bygget har slutförts. Klicka på blixtikonen i artefakten, aktivera utlösaren och klicka på **Spara** för att spara versionspipelinen.

![Aktivera utlösare][enable-trigger]

Välj **+ släpp**  ->  **skapa en version**  ->  **skapa** för att skapa en version manuellt. Du kan övervaka versionsförloppet på fliken **Releases** (Versioner).

Kontrollera att distributionen har slutförts och programmet körs i klustret.  Öppna en webbläsare och gå till `http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/`.  Observera programversionen. I det här exemplet är den 1.0.0.20170616.3.

## <a name="commit-and-push-changes-trigger-a-release"></a>Genomför och push-överföring av ändringar utlöser en släppning

För att kontrollera att pipelinen för den kontinuerliga integreringen fungerar genom att kontrollera kodändringar i Azure DevOps.

När du skriver koden spåras dina ändringar automatiskt av Visual Studio. Genomför ändringar av den lokala Git-lagringsplatsen genom att välja ikonen väntande ändringar (![Ikonen för väntande ändringar visar en penna och en siffra.][pending]) från statusfältet i nederkant högra hörnet.

I vyn **Ändringar** i Team Explorer lägger du till ett meddelande som beskriver uppdateringen. Spara sedan ändringarna.

![Genomför alla][changes]

Välj statusfältikonen för de opublicerade ändringarna (![Opublicerade ändringar][unpublished-changes]) eller vyn Synkronisera i Team Explorer. Välj **Push** (Push-överföring) för att uppdatera koden i Azure PIpelines.

![Push-överföring av ändringar][push]

När du skickar ändringar till Azure Pipelines via push-överföring utlöses ett bygge automatiskt.  När bygg-pipelinen har slutförts skapas en version automatiskt och börjar uppgradera programmet i klustret.

Om du vill kontrollera förloppet för bygget kan du byta till fliken **Builds** (Byggen) i **Team Explorer** i Visual Studio.  När du har kontrollerat att bygget körs rätt måste du definiera en versionspipeline som distribuerar programmet till ett kluster.

Kontrollera att distributionen har slutförts och programmet körs i klustret.  Öppna en webbläsare och gå till `http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/`.  Observera programversionen. I det här exemplet är den 1.0.0.20170815.3.

![Skärm bild av röstnings appen i Service Fabric Explorer som körs i ett webbläsarfönster. App-versionen "1.0.0.20170815.3" är markerad.][sfx1]

## <a name="update-the-application"></a>Uppdatera programmet

Göra kodändringar i programmet.  Spara och genomföra ändringarna via de här stegen.

När programuppgraderingen påbörjas kan du visa uppgraderingsförloppet i Service Fabric Explorer:

![Skärm bild av röstnings appen i Service Fabric Explorer. Status meddelandet "uppgradera" och meddelandet "uppgradering pågår" är markerat.][sfx2]

Programuppgraderingen kan ta flera minuter. När uppgraderingen har slutförts kör programmet nästa version.  I det här exemplet 1.0.0.20170815.4.

![Skärm bild av röstnings appen i Service Fabric Explorer som körs i ett webbläsarfönster. Den uppdaterade program versionen "1.0.0.20170815.4" är markerad.][sfx3]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * lägga till källkontroll i projektet
> * Skapa en bygg-pipeline
> * Skapa en versionspipeline
> * distribuera och uppgradera ett program automatiskt.

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Konfigurera övervakning och diagnostik för programmet](service-fabric-tutorial-monitoring-aspnet.md)

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[new-pipeline]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewPipeline.png
[select-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectRepo.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue.png
[save-and-queue2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue2.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Push.png
[continuous-delivery-with-AzureDevOpsServices]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
