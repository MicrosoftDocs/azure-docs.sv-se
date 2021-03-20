---
title: Konfigurera Jenkins för en Java-app på Service Fabric i Azure
description: I den här kursen lär du dig hur du ställer in kontinuerlig integration med Jenkins för att distribuera en Java Service Fabric-tillämpning.
ms.topic: tutorial
ms.date: 08/27/2018
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 4e29104f0586a8e18afb7a640903c57ce988b692
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91531980"
---
# <a name="tutorial-configure-a-jenkins-environment-to-enable-cicd-for-a-java-application-on-service-fabric"></a>Självstudie: Konfigurera en Jenkins-miljö och aktivera CI/CD för en Java-app i Service Fabric

Den här självstudien är del fem i en serie. Den visar hur du använder Jenkins för att distribuera uppgraderingar till din tillämpning. I den här självstudien används plugin-programmet för Service Fabric Jenkins i kombination med en GitHub-databas, som är värd för röstningsprogrammet, för att distribuera tillämpningen till ett kluster.

I del fem i serien lär du dig hur du:
> [!div class="checklist"]
> * Distribuerar Service Fabric Jenkins-containern på din dator
> * Konfigurerar Jenkins-miljön för distribution till Service Fabric
> * Uppgraderar tillämpningen

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Skapa ett Java Service Fabric Reliable Services-program](service-fabric-tutorial-create-java-app.md)
> * [Distribuera och felsöka ditt program på ett lokalt kluster](service-fabric-tutorial-debug-log-local-cluster.md)
> * [Distribuera programmet till ett Azure-kluster](service-fabric-tutorial-java-deploy-azure.md)
> * [Konfigurera övervakning och diagnostik för programmet](service-fabric-tutorial-java-elk.md)
> * Konfigurera CI/CD

## <a name="prerequisites"></a>Förutsättningar

* Installera Git på den lokala datorn från [sidan för hämtningsbara Git-filer](https://git-scm.com/downloads). Mer information om Git finns i [Git-dokumentationen](https://git-scm.com/docs).
* Ha tidigare erfarenhet av [Jenkins](https://jenkins.io/).
* Skapa ett [GitHub](https://github.com/)-konto och veta hur du använder GitHub.
* Installera [Docker](https://www.docker.com/community-edition) på datorn.

## <a name="pull-and-deploy-service-fabric-jenkins-container-image"></a>Hämta och distribuera Service Fabric Jenkins-containeravbildningen

Du kan konfigurera Jenkins i eller utanför ett Service Fabric-kluster. Följande instruktioner visar hur du konfigurerar det utanför ett kluster med en angiven Docker-avbildning. Det går också att använda en förkonfigurerad kompileringsmiljö för Jenkins. Följande containeravbildning är förinstallerad i plugin-programmet för Service Fabric och är redo för användning med Service Fabric omedelbart.

1. Hämta containeravbildningen för Service Fabric Jenkins:``docker pull rapatchi/jenkins:v10``. Plugin-programmet för Service Fabric Jenkins är förinstallerat i avbildningen.

1. Kör containeravbildningen med den plats där Azure-certifikaten är lagrade på den lokala monterade datorn.

    ```bash
    docker run -itd -p 8080:8080 -v /service-fabric-java-quickstart/AzureCluster rapatchi/jenkins:v10
    ```

1. Hämta ID:t för containeravbildningsinstansen. Du kan visa en lista med alla Docker-containrar med hjälp av kommandot ``docker ps –a``

1. Hämta lösenordet för din Jenkins-instans genom att köra följande kommando:

    ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```

    Om container-ID:t är 2d24a73b5964 ska du använda 2d24.
    * Det här lösenordet krävs för att logga in på Jenkins-instrumentpanelen från portalen som är ``http://<HOST-IP>:8080``
    * När du loggar in för första gången skapar du ett eget användarkonto, eller så använder du administratörskontot.

1. Konfigurera GitHub för Jenkins genom att utföra åtgärderna som nämns i [Generating a new SSH key and adding it to the SSH agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) (Generera en ny SSH-nyckel och lägga till den i SSH-agenten). Eftersom kommandona körs från en Docker-container följer du instruktionerna för Linux-miljön.
   * Skapa SSH-nyckeln med hjälp av instruktionerna från GitHub. Lägg sedan till SSH-nyckeln till GitHub-kontot som är värd för databasen.
   * Kör de kommandon som nämns i länken ovan i Jenkins Docker-gränssnittet (och inte på värden).
   * Om du vill logga in till Jenkins-gränssnittet från värden ska du använda följande kommandon:

     ```sh
     docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
     ```

     Kontrollera att klustret eller datorn där Jenkins-containeravbildningen finns har en offentlig IP-adress. Med en offentlig IP-adress kan Jenkins-instansen ta emot meddelanden från GitHub.

## <a name="create-and-configure-a-jenkins-job"></a>Skapa och konfigurera ett Jenkins-jobb

1. Om du inte har någon databas att använda som värd för röstningsprojektet på GitHub börjar du med att skapa en. Databasen kallas **dev_test** i återstoden av den här självstudien.

1. Skapa ett **nytt objekt** på Jenkins-instrumentpanelen vid ``http://<HOST-IP>:8080``.

1. Ange ett namn (till exempel **MyJob**). Välj **free-style project** (freestyle-projekt) och klicka på **OK**.

1. Gå till jobbsidan och klicka sedan på **Konfigurera**.

   a. Markera kryssrutan och ange URL:en för GitHub-projektet under **GitHub-projekt** i det allmänna avsnittet. Den här URL:en är värd för det Service Fabric Java-program som du vill integrera med Jenkins CI/CD-flödet (t.ex. ``https://github.com/testaccount/dev_test``).

   b. I avsnittet **Source Code Management** (Källkodshantering) väljer du **Git**. Ange den webb adress till databasen som är värd för Service Fabric Java-program som du vill integrera med Jenkins CI/CD-flödet (t. ex. *`https://github.com/testaccount/dev_test.git`* ). Du kan också ange här vilken gren som ska byggas (t.ex. **/master**).

1. Konfigurera din *GitHub* (som är värd för databasen) så att den kan kommunicera med Jenkins. Gör så här:

   a. Gå till GitHub-lagringsplatssidan. Gå till **Inställningar**  >  **integreringar och tjänster**.

   b. Välj **Lägg till tjänst**, skriv **Jenkins** och välj **Jenkins GitHub-plugin-programmet**.

   c. Ange din Jenkins-webhooksadress (som standard ska den vara ``http://<PublicIPorFQDN>:8081/github-webhook/``). Klicka på **Lägg till/Uppdatera tjänsten**.

   d. En testhändelse skickas till Jenkins-instansen. Du bör se en grön bock vid webhooken i GitHub och dina projektversioner.

   ![Service Fabric Jenkins-konfiguration](./media/service-fabric-tutorial-java-jenkins/jenkinsconfiguration.png)

1. I avsnittet om **build-utlösare** väljer du önskat alternativ. I det här exemplet vill du utlösa en build när något skickas till databasen. Därför väljer du **GitHub hook trigger for GITScm polling** (GitHub-hookutlösare för GITScm-avsökning).

1. Under avsnittet **Build** (Bygg) i listrutan **Add build step** (Lägg till byggsteg) väljer du alternativet **Invoke Gradle Script** (Anropa Gradle-skript). I widgeten som visas öppnar du menyn för avancerade alternativ och anger sökvägen till **rotbuildskript** för ditt program. Då hämtas build.gradle från den angivna sökvägen och fungerar på motsvarande sätt.

    ![Service Fabric Jenkins Build-åtgärd](./media/service-fabric-tutorial-java-jenkins/jenkinsbuildscreenshot.png)

1. I listrutan **Post-Build Actions** (Åtgärder efter skapandet) väljer du **Deploy Service Fabric Project** (Distribuera Service Fabric-projekt). Här måste du ange klusterinformation där Jenkins-kompilerade Service Fabric-programmet skulle distribueras. Sökvägen till certifikatet är platsen där volymen monterades (/ tmp/myCerts).

    Du kan även ange ytterligare information som används för att distribuera programmet. Följande skärmbild visar ett exempel på hur informationen om programmet kan se ut:

    ![Dialog rutan åtgärder efter build med ytterligare Service Fabric projekt distributions alternativ](./media/service-fabric-tutorial-java-jenkins/sfjenkins.png)

    > [!NOTE]
    > Det här klustret kan vara detsamma som det kluster som är värd för Jenkins-containerprogrammet om du använder Service Fabric för att distribuera Jenkins-containeravbildningen.
    >

1. Klicka på **Spara**.

## <a name="update-your-existing-application"></a>Distribuera ditt befintliga program

1. Uppdatera HTML-titeln i filen *VotingApplication/VotingWebPkg/Code/wwwroot/index.html* till **Service Fabric röstningsexempel V2**.

    ```html
    <div ng-app="VotingApp" ng-controller="VotingAppController" ng-init="refresh()">
        <div class="container-fluid">
            <div class="row">
                <div class="col-xs-8 col-xs-offset-2 text-center">
                    <h2>Service Fabric Voting Sample V2</h2>
                </div>
            </div>
        </div>
    </div>
    ```

1. Uppdatera versionen för **ApplicationTypeVersion** och **ServiceManifestVersion** till **2.0.0** i filen *Voting/VotingApplication/ApplicationManifest.xml*.

    ```xml
    <?xml version="1.0" encoding="utf-8" standalone="no"?>
    <ApplicationManifest xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingApplicationType" ApplicationTypeVersion="2.0.0">
      <Description>Voting Application</Description>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="2.0.0"/>
      </ServiceManifestImport>
      <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="VotingDataServicePkg" ServiceManifestVersion="1.0.0"/>
        </ServiceManifestImport>
        <DefaultServices>
          <Service Name="VotingWeb">
             <StatelessService InstanceCount="1" ServiceTypeName="VotingWebType">
                <SingletonPartition/>
             </StatelessService>
          </Service>
       <Service Name="VotingDataService">
                <StatefulService MinReplicaSetSize="3" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="3">
                    <UniformInt64Partition HighKey="9223372036854775807" LowKey="-9223372036854775808" PartitionCount="1"/>
                </StatefulService>
            </Service>
        </DefaultServices>
    </ApplicationManifest>
    ```

1. Uppdatera fältet **Version** i **ServiceManifest** och fältet **Version** i taggen **CodePackage** i filen *Voting/VotingApplication/VotingWebPkg/ServiceManifest.xml* till **2.0.0**.

    ```xml
    <CodePackage Name="Code" Version="2.0.0">
    <EntryPoint>
        <ExeHost>
        <Program>entryPoint.sh</Program>
        </ExeHost>
    </EntryPoint>
    </CodePackage>
    ```

1. Skicka dina nya ändringar till GitHub-databasen för att initiera ett Jenkins-jobb som utför en uppgradering av programmet.

1. I Service Fabric Explorer klickar du på listrutan **Program**. Om du vill visa status för uppgraderingen klickar du på fliken **Pågående uppgraderingar**.

    ![Uppgradering pågår](./media/service-fabric-tutorial-create-java-app/upgradejava.png)

1. Om du har åtkomst till **http:// \<Host-IP> : 8080**  röstnings programmet med fullständiga funktioner igång nu.

    ![Lokal röstningsapp](./media/service-fabric-tutorial-java-jenkins/votingv2.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Distribuerar Service Fabric Jenkins-containern på din dator
> * Konfigurerar Jenkins-miljön för distribution till Service Fabric
> * Uppgraderar tillämpningen

* Se andra [Java-exempel](https://github.com/Azure-Samples/service-fabric-java-getting-started)
