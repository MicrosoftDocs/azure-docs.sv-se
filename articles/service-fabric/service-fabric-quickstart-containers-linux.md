---
title: Skapa en Linux container-app på Service Fabric i Azure
description: I den här snabbstarten skapar du en Docker-avbildning med din app, överför avbildningen till ett containerregister och distribuerar sedan containern till ett Service Fabric-kluster.
ms.topic: quickstart
ms.date: 07/22/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: f58b8feda0e88d8a5e7cddaabbc650b0f0ab3973
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98788170"
---
# <a name="quickstart-deploy-linux-containers-to-service-fabric"></a>Snabbstart: Distribuera Linux-containrar till Service Fabric

Azure Service Fabric är en plattform för distribuerade system för distribution och hantering av skalbara och tillförlitliga mikrotjänster och containrar.

Den här snabbstarten visar hur du distribuerar Linux-containrar till ett Service Fabric-kluster i Azure. När du är klar har du ett röstningsprogram som består av en frontwebbtjänst i Python och en Redis-serverdel som körs i ett Service Fabric-kluster. Du lär dig också att redundansväxla ett program och skala program i klustret.

![Webbsida för röstningsappen][quickstartpic]

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabbstarten behöver du:

1. Om du inte har någon prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

2. Installera [Azure CLI](/cli/azure/install-azure-cli-apt)

3. Installera [Service Fabric SDK och CLI](service-fabric-get-started-linux.md#installation-methods)

4. Installera [git](https://git-scm.com/)


## <a name="get-the-application-package"></a>Hämta programpaketet

För att kunna distribuera containrar till Service Fabric behöver du en uppsättning manifestfiler (programdefinitionen), som beskriver de enskilda containrarna samt programmet.

I en konsol använder du Git för att klona en kopia av programmets definition. Ändra sedan katalog till `Voting`-katalogen i din klon.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="create-a-service-fabric-cluster"></a>Skapa ett Service Fabric-kluster

Om du vill distribuera programmet till Azure behöver du ett Service Fabric-kluster som kör programmet. Följande kommandon skapar ett kluster med fem noder i Azure.  Kommandona skapar även ett självsignerat certifikat, lägger till det i ett nyckelvalv och laddar ned certifikatet lokalt. Det nya certifikatet används för att skydda klustret när distribueras och används för att autentisera klienter.

```azurecli
#!/bin/bash

# Variables
ResourceGroupName='containertestcluster' 
ClusterName='containertestcluster' 
Location='eastus' 
Password='q6D7nN%6ck@6' 
Subject='containertestcluster.eastus.cloudapp.azure.com' 
VaultName='containertestvault' 
VmPassword='Mypa$$word!321'
VmUserName='sfadminuser'

# Login to Azure and set the subscription
az login

az account set --subscription <mySubscriptionID>

# Create resource group
az group create --name $ResourceGroupName --location $Location 

# Create secure five node Linux cluster. Creates a key vault in a resource group
# and creates a certficate in the key vault. The certificate's subject name must match 
# the domain that you use to access the Service Fabric cluster.  The certificate is downloaded locally.
az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $ResourceGroupName --vm-password $VmPassword --vm-user-name $VmUserName
```

> [!Note]
> Frontwebbtjänsten är konfigurerad för att lyssna efter inkommande trafik på port 80. Som standard är port 80 öppen på dina virtuella klusterdatorer och Azure-lastbalanseraren.
>

## <a name="configure-your-environment"></a>Konfigurera din miljö

I Service Fabric finns flera verktyg för att hantera kluster och dess program:

- Service Fabric Explorer, ett webbläsarbaserat verktyg.
- Service Fabric CLI (kommandoradsgränssnitt) som körs ovanpå Azure CLI. 
- PowerShell-kommandon.

I den här snabbstarten använder du Service Fabric CLI och Service Fabric Explorer (ett webbaserat verktyg). Om du vill använda Service Fabric Explorer måste du importera certifikatets PFX-fil till webbläsaren. PFX-filen har inget lösenord som standard.

Mozilla Firefox är standardwebbläsaren i Ubuntu 16.04. Klicka på menyknappen i det övre högra hörnet i webbläsaren om du vill importera certifikatet till Firefox. Klicka sedan på **Alternativ**. På sidan **Inställningar** söker du efter ”certifikat” i sökrutan. Klicka på **Visa certifikat** och välj fliken **Dina certifikat**. Klicka på **Importera** och följ anvisningarna för att importera certifikatet.

   ![Installera certifikat på Firefox](./media/service-fabric-quickstart-containers-linux/install-cert-firefox.png)

## <a name="deploy-the-service-fabric-application"></a>Distribuera Service Fabric-programmet

1. Anslut till Service Fabric-klustret i Azure med hjälp av CLI:n. Slutpunkten är hanteringsslutpunkten för klustret. Du skapade PEM-filen i föregående avsnitt. 

    ```bash
    sfctl cluster select --endpoint https://containertestcluster.eastus.cloudapp.azure.com:19080 --pem containertestcluster22019013100.pem --no-verify
    ```

2. Använd installationsskriptet till att kopiera röstningsprogrammets definition till klustret, registrera programtypen och skapa en instans av programmet.  PEM-certifikatfilen måste finnas i samma katalog som *install.sh*-filen.

    ```bash
    ./install.sh
    ```

3. Öppna en webbläsare och gå till Service Fabric Explorer-slutpunkten för klustret. Slut punkten har följande format:  **https:// \<my-azure-service-fabric-cluster-url> : 19080/Explorer**; till exempel `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer` . </br>

4. Expandera noden **Program** för att se att det nu finns en post för röstningsprogramtypen och instansen som du skapade.

    ![Service Fabric Explorer][sfx]

5. Om du vill ansluta till containern som körs öppnar du en webbläsare och går till webbadressen för ditt kluster, till exempel `http://containertestcluster.eastus.cloudapp.azure.com:80`. Nu ska röstningsprogrammet visas i webbläsaren.

    ![Webbsida för röstningsappen][quickstartpic]

> [!NOTE]
> Du kan också distribuera Service Fabric-program med Docker Compose. Till exempel kan följande kommando användas för att distribuera och installera programmet på klustret med Docker Compose.
>  ```bash
> sfctl compose create --deployment-name TestApp --file-path ../docker-compose.yml
> ```

## <a name="fail-over-a-container-in-a-cluster"></a>Redundansväxla en container i ett kluster

Service Fabric säkerställer att dina containerinstanser flyttas automatiskt till andra noder i klustret om ett fel inträffar. Du kan också tömma en nod på containrar och sedan flytta dem till andra noder i klustret. I Service Fabric finns olika sätt att skala dina tjänster på. I följande steg ska du använda Service Fabric Explorer.

Så här redundansväxlar du containern på klientsidan:

1. Öppna Service Fabric Explorer i klustret, till exempel `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer`.
2. Klicka på noden **fabric:/Voting/azurevotefront** i trädvyn och expandera partitionsnoden (visas med en GUID). Lägg märke till nodnamnet i trädvyn som visar de noder som containern körs på, till exempel `_nodetype_1`.
3. Visa noden **Noder** i trädvyn. Klicka på ellipsen (...) bredvid den nod som kör containern.
4. Välj **Starta om** för att starta om noden och bekräfta omstartsåtgärden. Omstarten gör att containern växlar över till en annan nod i klustret.

    ![Nodvy i Service Fabric Explorer][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>Skala program och tjänster i ett kluster

Service Fabric-tjänster kan enkelt skalas över ett kluster beroende på belastningen på tjänsterna. Du kan skala en tjänst genom att ändra antalet instanser som körs i klustret.

Gör så här om du vill skala frontwebbtjänsten:

1. Öppna Service Fabric Explorer i klustret, till exempel`https://containertestcluster.eastus.cloudapp.azure.com:19080`.
2. Klicka på ellipsen (tre punkter) bredvid noden **fabric:/Voting/azurevotefront** i trädvyn och välj alternativet för att **skala tjänsten**.

    ![Skalningstjänsten i Service Fabric Explorer startas][containersquickstartscale]

    Du kan nu välja att skala antalet instanser av frontwebbtjänsten.

3. Ändra antalet till **2** och klicka på **Scale Service** (Skala tjänst).
4. Klicka på noden **fabric:/Voting/azurevotefront** i trädvyn och expandera partitionsnoden (visas med en GUID).

    ![Skalningstjänsten i Service Fabric Explorer har slutförts][containersquickstartscaledone]

    Du kan nu se att tjänsten har två instanser. I trädvyn kan du se vilka noder instanserna körs på.

Med den här enkla hanteringsåtgärden har du dubblerat tillgängliga resurser för bearbetning av användarbelastningen i frontwebbtjänsten. Det är viktigt att förstå att du inte behöver flera instanser av en tjänst för att den ska kunna köras på ett tillförlitligt sätt. Om ett fel uppstår för en tjänst ser Service Fabric till att en ny tjänstinstans körs i klustret.

## <a name="clean-up-resources"></a>Rensa resurser

Använd avinstallationsskriptet (uninstall.sh) som medföljer mallen för att ta bort programinstansen från klustret och avregistrera programtypen. Det tar lite tid för skriptet att rensa instansen, så installationsskriptet bör inte köras omedelbart efter det här skriptet. Du kan använda Service Fabric Explorer till att avgöra när instansen har tagits bort och programtypen har avregistrerats.

```bash
./uninstall.sh
```

Det enklaste sättet att ta bort klustret och alla de resurser det använder är att ta bort resursgruppen.

Logga in på Azure och välj det prenumerations-ID som du vill ta bort klustret med. Du hittar ditt prenumerations-ID när du loggar in på Azure-portalen. Ta bort resursgruppen och alla klusterresurser med [kommandot az group delete](/cli/azure/group).

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="containertestcluster"
az group delete --name $ResourceGroupName
```

Om du är färdig med ditt kluster kan du ta bort certifikatet från certifikatarkivet. Exempel:
- I Windows: Använd [MMC-snapin-modulen Certifikat](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in). Välj **Mitt användarkonto** när du lägger till snapin-modulen. Gå till `Certificates - Current User\Personal\Certificates` och ta bort certifikatet.
- I Mac: Använd nyckelringsappen.
- I Ubuntu: Följ stegen som du använde för att visa certifikat och ta bort certifikatet.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat ett Linux-containerprogram till ett Service Fabric-kluster i Azure, utfört redundansväxling på programmet och skalat programmet i klustret. Om du vill veta mer om hur man arbetar med Linux-containrar i Service Fabric kan du fortsätta till självstudien om Linux-containerappar.

> [!div class="nextstepaction"]
> [Skapa en app för Linux-container](./service-fabric-tutorial-create-container-images.md)

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
