---
title: Självstudie – CI/CD till virtuella Azure-datorer med Azure-pipelines
description: I den här självstudien får du lära dig att ställa in kontinuerlig integrering (CI) och kontinuerlig distribution (CD) för en Node.js-app på virtuella Azure-datorer med YAML-baserad Azure-pipeline.
author: ushan
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/3/2020
ms.author: ushan
ms.custom: devops, devx-track-js
ms.openlocfilehash: 49282bf6cbc7c24b75fbe3f1bbe68bd1fac62ae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552498"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-using-azure-devops-services-and-azure-pipelines"></a>Självstudie: distribuera din app till virtuella Linux-datorer i Azure med Azure DevOps Services och Azure-pipelines

Kontinuerlig integrering (CI) och kontinuerlig distribution (CD) utgör en pipeline som du kan använda för att bygga, lansera och distribuera din kod efter varje kod genomförande. Det här dokumentet innehåller de steg som är kopplade till att konfigurera en CI/CD-pipeline för att utföra distributioner på flera datorer med hjälp av Azure-pipelines.

Azure-pipeliner innehåller en komplett uppsättning CI/CD Automation-verktyg för distributioner till virtuella datorer, både på lokal eller i alla moln.

I den här självstudien ställer du in en YAML-baserad CI/CD-pipeline för att distribuera din app till en Azure pipeline- [miljö](/azure/devops/pipelines/process/environments) med virtuella Linux-datorer som resurser, som var och en fungerar som webb servrar för att köra appen.

Lär dig att:

> [!div class="checklist"]
> * Hämta en exempel App.
> * Skapa en YAML-baserad Azure pipelines CI-pipeline för att skapa exempel appen.
> * Skapa en Azure pipeline-miljö för Azure Virtual Machines
> * Skapa en Azure pipelines CD-pipeline.
> * Kör manuella och CI-utlösta distributioner.

## <a name="before-you-begin"></a>Innan du börjar

* Logga in på Azure DevOps Services-organisationen ( **https://dev.azure.com/** ). 
  Du kan få en [kostnadsfri Azure DevOps Services-organisation](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Mer information finns i avsnittet om att [ansluta till Azure DevOps Services](/azure/devops/organizations/projects/connect-to-projects).

*  Du behöver en virtuell Linux-dator som distributionsmål.  Mer information finns i [Skapa och hantera virtuella Linux-datorer med Azure CLI](./tutorial-manage-vm.md).

*  Öppna inkommande port 80 för den virtuella datorn. Mer information finns i avsnittet om att [skapa nätverkssäkerhetsgrupper med hjälp av Azure Portal](../../virtual-network/tutorial-filter-network-traffic.md).

## <a name="get-your-sample-app-code"></a>Hämta din exempel kod för appen

Om du redan har en app i GitHub som du vill distribuera kan du prova att skapa en pipeline för koden.

Men om du är en ny användare kan du få en bättre start genom att använda vår exempel kod. I så fall kan du förgrena denna lagrings platsen i GitHub:

#### <a name="java"></a>[Java](#tab/java)

```
https://github.com/spring-projects/spring-petclinic
```

> [!NOTE]
> Petclinic är ett [Java våren Boot](https://spring.io/guides/gs/spring-boot) -program som skapats med [maven](https://spring.io/guides/gs/maven/).

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

```
https://github.com/azure-devops/fabrikam-node
```

> [!NOTE]
> Den här Node.js-appen har skapats med [Yeoman](https://yeoman.io/learning/index.html). Den använder Express, Bower och Grunt. Den har även vissa npm-paket som beroenden.
> Exempelappen innehåller också ett skript som konfigurerar Nginx och distribuerar appen. Det körs på de virtuella datorerna. Vad skriptet gör:
> 1. Installerar Node, Nginx och PM2.
> 2. Konfigurerar Nginx och PM2.
> 3. Startar Node-appen.

* * * 

## <a name="prerequisites-for-the-linux-vm"></a>Krav för den virtuella Linux-datorn

Exempel appar som nämns ovan har testats på Ubuntu 16,04 och vi rekommenderar att du använder samma version av Linux VM för den här snabb starten.
Följ de ytterligare stegen som beskrivs nedan baserat på körnings stacken som används för appen.

#### <a name="java"></a>[Java](#tab/java)

- För att distribuera Java våren boot och våren Cloud based Apps skapar du en virtuell Linux-dator i Azure med hjälp av [den här](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) mallen, som tillhandahåller en openjdk-baserad körning som stöds fullt ut.
- För att distribuera Java-servlets på Tomcat-servern skapar du en virtuell Linux-dator med Java 8 med hjälp av [den här](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) Azure-mallen och [konfigurerar Tomcat 9. x som en tjänst](https://tomcat.apache.org/tomcat-9.0-doc/setup.html).
- För att distribuera Java EE-baserad app använder du en Azure-mall för att skapa en [virtuell Linux-dator + Java + WebSphere 9. x](https://azuremarketplace.microsoft.com/marketplace/apps/midvision.websphere-application-server-nde-90) eller en [Linux VM + Java + WebLogic 12. x](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.20191009-arm-oraclelinux-wls-admin) eller en [Linux VM + Java](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) + WildFly/JBoss 14 


#### <a name="javascript"></a>[JavaScript](#tab/java-script)

Om du vill installera en JavaScript-app eller en Node.js-app behöver du en virtuell Linux-dator med Nginx-webbservern för att distribuera appen.
Om du inte redan har en virtuell Linux-dator med Nginx skapar du en nu i Azure med hjälp av stegen i [det här exemplet](./quick-create-cli.md).

* * * 

## <a name="create-an-azure-pipelines-environment-with-azure-virtual-machines"></a>Skapa en Azure pipeline-miljö med Azure Virtual Machines

Virtuella datorer kan läggas till som resurser i [miljöer](/azure/devops/pipelines/process/environments) och kan riktas mot distributioner på flera datorer. Vyer för distributions historik i miljön ger spårning från virtuell dator till pipelinen och sedan till commit.

Du kan skapa en miljö i hubben "**miljöer**" i avsnittet "**pipeliner**".
1.  Logga in på din Azure DevOps-organisation och navigera till ditt projekt.
2.  Gå till sidan **pipelines** i projektet. Välj sedan **miljöer** och klicka på **skapa miljö**. Ange ett **namn** (obligatoriskt) för miljön och en **Beskrivning**.
3.  Välj **Virtual Machines** som en **resurs** som ska läggas till i miljön och klicka på **Nästa**.
4.  Välj operativ system (Windows/Linux) och **Kopiera PS Registration-skriptet**. 
5.  Kör nu det kopierade skriptet från en administratör PowerShell-kommandotolk på var och en av de virtuella mål datorerna som ska registreras i den här miljön.
    > [!NOTE]
    > - Personlig åtkomsttoken för den inloggade användaren infogas i skriptet som förfaller samma dag, vilket gör att det kopierade skriptet inte kan användas.
    > - Om den virtuella datorn redan har en agent som körs på den, anger du ett unikt namn för "agent" som ska registreras i miljön.
6.  När den virtuella datorn har registrerats kommer den att visas som en miljö resurs under fliken resurser i miljön.

    ![VMcreation](media/tutorial-deploy-vms-azure-pipelines/vm-creation.png)

7.  Om du vill lägga till fler virtuella datorer kan du Visa och kopiera skriptet igen genom att klicka på Lägg till resurs och välja Virtual Machines som resurs. Det här skriptet är detsamma för alla virtuella datorer som ska läggas till i den här miljön. 
8.  Varje dator samverkar med Azure-pipelines för att samordna distributionen av din app.

    ![VMresource_view](media/tutorial-deploy-vms-azure-pipelines/vm-resourceview.png)

9. Du kan lägga till taggar till den virtuella datorn som en del av skriptet för interaktiv PowerShell-registrering (eller) du kan också lägga till/ta bort samma från resursvyn genom att klicka på de tre punkterna i slutet av varje VM-resurs i vyn resurser.

   Taggarna du tilldelar gör att du kan begränsa distributionen till vissa virtuella datorer när miljön används i ett distributions jobb. Taggarna är varje begränsat till 256 tecken, men det finns ingen gräns för antalet taggar som du kan använda.

   ![VMtags](media/tutorial-deploy-vms-azure-pipelines/vm-tags.png)

* * * 

## <a name="define-your-ci-build-pipeline"></a>Definiera din CI build-pipeline

Du behöver en pipeline för kontinuerlig integrering (CI) som publicerar ditt webb program, samt ett distributions skript som kan köras lokalt på Ubuntu-servern. Konfigurera en pipeline för CI-skapande baserat på den körning som du vill använda. 

1. Logga in på din Azure DevOps-organisation och navigera till ditt projekt.

1. Gå till sidan **pipelines** i projektet. Välj sedan åtgärden för att skapa en ny pipeline.

1. Gå igenom stegen i guiden genom att först välja **GitHub** som plats för käll koden.

1. Du kanske omdirigeras till GitHub för att logga in. I så fall anger du dina GitHub-autentiseringsuppgifter.

1. När listan över databaser visas väljer du den önskade exempel App-lagringsplatsen.

1. Azure-pipeliner analyserar din lagrings plats och rekommenderar en lämplig pipeline-mall.

#### <a name="java"></a>[Java](#tab/java)

Välj **Start** mal len och kopiera nedanstående yaml-kodfragment som skapar ditt Java-projekt och kör tester med Apache maven:

```YAML
jobs:
- job: Build
  displayName: Build Maven Project
  steps:
  - task: Maven@3
    displayName: 'Maven Package'
    inputs:
      mavenPomFile: 'pom.xml'
  - task: CopyFiles@2
    displayName: 'Copy Files to artifact staging directory'
    inputs:
      SourceFolder: '$(System.DefaultWorkingDirectory)'
      Contents: '**/target/*.?(war|jar)'
      TargetFolder: $(Build.ArtifactStagingDirectory)
  - upload: $(Build.ArtifactStagingDirectory)
    artifact: drop
```

Om du vill ha mer vägledning följer du stegen i [utveckla din Java-app med maven](/azure/devops/pipelines/ecosystems/java).

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

Välj **Start** mal len och kopiera nedanstående yaml-kodfragment som skapar ett allmänt Node.js-projekt med NPM.

```YAML
- stage: Build
  displayName: Build stage
  jobs:  
  - job: Build
    displayName: Build
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    - script: |
        npm install
        npm run build --if-present
        npm run test --if-present
      displayName: 'npm install, build and test'
    - task: ArchiveFiles@2
      displayName: 'Archive files'
      inputs:
        rootFolderOrFile: '$(System.DefaultWorkingDirectory)'
        includeRootFolder: false
        archiveType: zip
        archiveFile: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
        replaceExistingArchive: true
    - upload: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
      artifact: drop
```

För ytterligare vägledning följer du stegen i [utveckla din Node.js-app med Gulp](/azure/devops/pipelines/ecosystems/javascript).

- Ta en titt på pipelinen för att se vad det gör. Kontrol lera att alla standardvärden är lämpliga för din kod.

- Välj **Spara och kör**, välj sedan **genomför direkt till huvud grenen** och välj sedan **Spara och kör** igen.

- En ny körning har startats. Vänta tills körningen har slutförts.

* * * 

## <a name="define-cd-steps-to-deploy-to-the-linux-vm"></a>Definiera de CD-steg som ska distribueras till den virtuella Linux-datorn

1. Ändra YAML-filen för den ovan pipelinen för att inkludera ett [distributions jobb](/azure/devops/pipelines/process/deployment-jobs) genom att referera till miljön och de VM-resurser som du har tidigare med yaml-syntaxen nedan:

   ```YAML
   jobs:  
   - deployment: VMDeploy
     displayName: web
     environment:
       name:  <environment name>
       resourceType: VirtualMachine
       tags: web
   ```
2. Du kan välja olika uppsättningar av virtuella datorer från miljön för att ta emot distributionen genom att ange de **taggar** som du har definierat för varje virtuell dator i miljön.
[Här](/azure/devops/pipelines/yaml-schema?tabs=schema#deployment-job) är det fullständiga yaml-schemat för distributions jobb.

3. Du kan ange en `runOnce` eller `rolling` som distributions strategi. 

   `runOnce` är den enklaste distributions strategin där alla livs cykel hookar, nämligen `preDeploy` `deploy` `routeTraffic` och `postRouteTraffic` , utförs en gång. Sedan `on:` `success` körs eller utförs `on:` `failure` .

   Nedan visas ett exempel på YAML-kodfragment för `runOnce` :
   ```YAML
   jobs:
   - deployment: VMDeploy
     displayName: web
     pool:
       vmImage: 'Ubuntu-16.04'
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         runOnce:
           deploy:
             steps:
             - script: echo my first deployment
   ```

4. Nedan visas ett exempel på YAML-kodfragmentet som du kan använda för att definiera en rullande strategi för virtuella datorer som uppdaterar upp till 5 mål i varje iteration. `maxParallel` fastställer antalet mål som kan distribueras till parallellt. Urvals kontona för det absoluta antalet eller procent andelen mål som måste vara tillgängliga när som helst exklusive de mål som distribueras till. Den används också för att fastställa lyckade och misslyckade villkor under distributionen.

   ```YAML
   jobs: 
   - deployment: VMDeploy
     displayName: web
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         rolling:
           maxParallel: 5  #for percentages, mention as x%
           preDeploy:
             steps:
             - download: current
               artifact: drop
             - script: echo initialize, cleanup, backup, install certs
           deploy:
             steps:
             - task: Bash@3
               inputs:
                 targetType: 'inline'
                 script: |
                   # Modify deployment script based on the app type
                   echo "Starting deployment script run"
                   sudo java -jar '$(Pipeline.Workspace)/drop/**/target/*.jar'
           routeTraffic:
             steps:
             - script: echo routing traffic
           postRouteTraffic:
             steps:
             - script: echo health check post-route traffic
           on:
             failure:
               steps:
               - script: echo Restore from backup! This is on failure
             success:
               steps:
               - script: echo Notify! This is on success
   ```

   Varje jobb körning registreras distributions historiken mot den `<environment name>` miljö som du har skapat och registrerat de virtuella datorerna.

## <a name="run-your-pipeline-and-get-traceability-views-in-environment"></a>Kör din pipeline och få spårnings bara vyer i miljön
Vyn distributioner av miljön ger fullständig spårning av incheckningar och arbets objekt, och en distributions historik över pipelinen per miljö/resurs.

![VMDeployments_view](media/tutorial-deploy-vms-azure-pipelines/vm-deployments.png)
  
![VMjobs_view](media/tutorial-deploy-vms-azure-pipelines/vm-jobsview.png)

## <a name="next-steps"></a>Nästa steg
- Du kan fortsätta att [Anpassa den pipeline](/azure/devops/pipelines/customize-pipeline) som du nyss skapade.
- Mer information om vad mer du kan göra i YAML-pipelines finns i [yaml schema Reference](/azure/devops/pipelines/yaml-schema).
- Mer information om hur du distribuerar en LAMP-stack (Linux, Apache, MySQL och PHP) får du i nästa kurs.

> [!div class="nextstepaction"]
> [Distribuera LAMP-stack](tutorial-lamp-stack.md)
