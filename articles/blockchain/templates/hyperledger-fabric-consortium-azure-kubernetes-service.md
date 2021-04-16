---
title: Distribuera Hyperledger Fabric konsortium på Azure Kubernetes Service
description: Så här distribuerar och konfigurerar du Hyperledger Fabric ett konsortiumnätverk på Azure Kubernetes Service
ms.date: 03/01/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: contperf-fy21q3, devx-track-azurecli
ms.openlocfilehash: 03f19d1922c011c1b5304b66488e9fa8de703bf9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478326"
---
# <a name="deploy-hyperledger-fabric-consortium-on-azure-kubernetes-service"></a>Distribuera Hyperledger Fabric konsortium på Azure Kubernetes Service

Du kan använda mallen Hyperledger Fabric on Azure Kubernetes Service (AKS) för att distribuera och konfigurera Hyperledger Fabric ett konsortiumnätverk i Azure.

När du har läst den här artikeln, kommer du att:

- Ha kunskaper om Hyperledger Fabric och de komponenter som utgör byggstenarna i ett Hyperledger Fabric ett blockkedjenätverk.
- Veta hur du distribuerar och konfigurerar ett Hyperledger Fabric konsortiumnätverk på Azure Kubernetes Service för dina produktionsscenarier.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Välja en Azure Blockchain lösning

Innan du väljer att använda en lösningsmall bör du jämföra ditt scenario med vanliga användningsfall för tillgängliga Azure Blockchain alternativ:

Alternativ | Tjänstmodell | Vanligt användningsfall
-------|---------------|-----------------
Lösningsmallar | IaaS | Lösningsmallar är Azure Resource Manager som du kan använda för att etablera en fullständigt konfigurerad nätverkstopologi för blockkedjekedje. Mallarna distribuerar och konfigurerar Microsoft Azure tjänster för beräkning, nätverk och lagring för en blockkedjenätverkstyp. Lösningsmallar tillhandahålls utan serviceavtal. Använd [Sidan Microsoft Q&A](/answers/topics/azure-blockchain-workbench.html) för support.
[Azure Blockchain Service](../service/overview.md) | PaaS | Azure Blockchain Service Preview förenklar bildning, hantering och styrning av konsortium-blockkedjenätverk. Använd Azure Blockchain Service lösningar som kräver PaaS, konsortiumhantering eller avtals- och transaktionssekretess.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS och PaaS | Azure Blockchain Workbench Preview är en samling Azure-tjänster och -funktioner som hjälper dig att skapa och distribuera blockkedjeprogram för att dela affärsprocesser och data med andra organisationer. Använd Azure Blockchain Workbench för att skapa prototyper av en blockkedjelösning eller ett konceptbevis för ett blockkedjeprogram. Azure Blockchain Workbench tillhandahålls utan serviceavtal. Använd [Sidan Microsoft Q&A för](/answers/topics/azure-blockchain-workbench.html) support.

## <a name="deploy-the-orderer-and-peer-organization"></a>Distribuera beställaren och peer-organisationen

För att börja behöver du en Azure-prenumeration som stöder distribution av flera virtuella datorer och standardlagringskonton. Om du inte har någon Azure-prenumeration kan du skapa [ett kostnadsfritt Azure-konto.](https://azure.microsoft.com/free/)

Kom igång med distributionen av Hyperledger Fabric nätverkskomponenter genom att gå till [Azure Portal](https://portal.azure.com).

1. Välj **Skapa en**  >  **blockkedja för resursen** och sök sedan efter Hyperledger Fabric på Azure Kubernetes Service **(förhandsversion)**.

2. Ange projektinformationen på **fliken Grundläggande** inställningar.

    ![Skärmbild som visar fliken Grundläggande inställningar.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Ange följande information:
    - **Prenumeration:** Välj det prenumerationsnamn där du vill distribuera Hyperledger Fabric nätverkskomponenter.
    - **Resursgrupp:** Skapa antingen en ny resursgrupp eller välj en befintlig tom resursgrupp. Resursgruppen innehåller alla resurser som distribueras som en del av mallen.
    - **Region:** Välj den Azure-region där du vill distribuera Azure Kubernetes Service för de Hyperledger Fabric komponenterna. Mallen är tillgänglig i alla regioner där AKS är tillgängligt. Välj en region där din prenumeration inte nå kvotgränsen för den virtuella datorn (VM).
    - **Resursprefix:** Ange ett prefix för namngivning av resurser som distribueras. Det måste innehålla färre än sex tecken och kombinationen av tecken måste innehålla både siffror och bokstäver.
4. Välj fliken **Infrastrukturinställningar** för att definiera Hyperledger Fabric nätverkskomponenter som ska distribueras.

    ![Skärmbild som visar fliken Infrastrukturinställningar.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Ange följande information:
    - **Organisationsnamn:** Ange namnet på den Hyperledger Fabric organisation som krävs för olika dataplansåtgärder. Organisationsnamnet måste vara unikt per distribution.
    - **Infrastrukturnätverkskomponent:** **Välj antingen Beställningstjänst** eller **Peer-noder,** baserat på den blockkedjenätverkskomponent som du vill konfigurera.
    - **Antal noder:** Följande är de två typerna av noder:
        - **Beställningstjänst:** Noder som ansvarar för transaktionsordning i transaktionsregister. Välj antalet noder som ska ge feltolerans till nätverket. Antalet ordernoder som stöds är 3, 5 och 7.
        - **Peer-noder:** Noder som är värdar för redovisningar och smarta kontrakt. Du kan välja mellan 1 och 10 noder baserat på dina behov.
    - **Peernodens världstillståndsdatabas:** World state-databaser för peer-noderna. LevelDB är standardtillståndsdatabasen som är inbäddad i peer-noden. Den lagrar kedjekoddata som enkla nyckel/värde-par och stöder endast nyckel, nyckelintervall och sammansatta nyckelfrågor. CouchDB är en valfri alternativ tillståndsdatabas som stöder omfattande frågor när kedjekodsdatavärden modelleras som JSON. Det här fältet visas när du väljer **Peer-noder** i **listrutan Infrastrukturnätverkskomponent.**
    - **Användarnamn för infrastrukturcertifikatutfärdare:** Med certifikatutfärdaren för infrastruktur kan du initiera och starta en serverprocess som är värd för certifikatutfärdaren. Det gör att du kan hantera identiteter och certifikat. Varje AKS-kluster som distribueras som en del av mallen har som standard en ca-podd för infrastrukturresurs. Ange det användarnamn som används för autentisering med infrastrukturprovutfärdaren.
    - **Lösenord för certifikatutfärdare för** infrastruktur: Ange lösenordet för autentisering av en infrastrukturprovutfärdare.
    - **Bekräfta lösenord:** Bekräfta lösenordet för infrastrukturprovutfärdaren.
    - **Certifikat:** Om du vill använda egna rotcertifikat för att initiera infrastrukturcertifikatutfärdaren väljer du alternativet **Ladda upp rotcertifikat för infrastrukturcertifikatutfärdare.** Annars skapar infrastrukturcertifikatutfärdaren själv signerade certifikat som standard.
    - **Rotcertifikat:** Ladda upp rotcertifikatet (offentlig nyckel) som infrastrukturcertifikatutfärdaren måste initieras med. Certifikat i .pem-format stöds. Certifikaten ska vara giltiga och i en UTC-tidszon.
    - **Privat nyckel för rotcertifikat:** Ladda upp den privata nyckeln för rotcertifikatet. Om du har ett .pem-certifikat, som har en kombinerad offentlig och privat nyckel, laddar du upp det här också.


6. Välj fliken **Inställningar för AKS-kluster** för att definiera Azure Kubernetes Service klusterkonfiguration. AKS-klustret har olika poddar konfigurerade för att köra Hyperledger Fabric nätverkskomponenter. De distribuerade Azure-resurserna är:

    - **Infrastrukturverktyg:** Verktyg som ansvarar för att konfigurera Hyperledger Fabric komponenter.
    - **Orderer-/peer-poddar:** Noderna i Hyperledger Fabric nätverk.
    - **Proxy:** En NGNIX-proxypodd genom vilken klientprogrammen kan kommunicera med AKS-klustret.
    - **Infrastruktur-CA:** Den podd som kör infrastrukturprovutfärdaren.
    - **PostgreSQL:** Databasinstans som underhåller infrastruktur-CA-identiteterna.
    - **Nyckelvalv:** Instansen av Azure Key Vault som distribueras för att spara autentiseringsuppgifterna för infrastrukturcertifikatutfärdaren och de rotcertifikat som tillhandahålls av kunden. Valvet används vid omförsök av malldistribution för att hantera mallens mekanismer.
    - **Hanterad disk:** Instans av Azure Managed Disks-tjänsten som tillhandahåller ett beständigt lager för huvudboken och för peer-nodens världstillståndsdatabas.
    - **Offentlig IP:** Slutpunkt för det AKS-kluster som distribuerats för kommunikation med klustret.

    Ange följande information: 

    ![Skärmbild som visar fliken A K S-klusterinställningar.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

    - **Kubernetes-klusternamn:** Ändra namnet på AKS-klustret om det behövs. Det här fältet är förindelat baserat på det angivna resursprefixet.
    - **Kubernetes-version:** Välj den version av Kubernetes som ska distribueras i klustret. Beroende på den region som du valde på **fliken Grundläggande inställningar** kan de tillgängliga versioner som stöds ändras.
    - **DNS-prefix:** Ange ett Domain Name System (DNS)-namnprefix för AKS-klustret. Du använder DNS för att ansluta till Kubernetes-API:et när du hanterar containrar när du har skapat klustret.
    - **Nodstorlek:** För Kubernetes-nodens storlek kan du välja från listan över VM-lagerhållningsenheter (SKU:er) som är tillgängliga i Azure. För optimala prestanda rekommenderar vi Standard DS3 v2.
    - **Antal noder:** Ange antalet Kubernetes-noder som ska distribueras i klustret. Vi rekommenderar att det här antalet noder är lika med eller större än det antal Hyperledger Fabric noder som anges på **fliken Infrastrukturinställningar.**
    - **Klient-ID för tjänstens** huvudnamn: Ange klient-ID för ett befintligt huvudnamn för tjänsten eller skapa ett nytt. Ett huvudnamn för tjänsten krävs för AKS-autentisering. Se stegen [för att skapa ett huvudnamn för tjänsten.](/powershell/azure/create-azure-service-principal-azureps#create-a-service-principal)
    - **Klienthemlighet för tjänstens** huvudnamn: Ange klienthemligheten för tjänstens huvudnamn som anges i klient-ID:t för tjänstens huvudnamn.
    - **Bekräfta klienthemlighet:** Bekräfta klienthemligheten för tjänstens huvudnamn.
    - **Aktivera övervakning av containrar:** Välj att aktivera AKS-övervakning, vilket gör det möjligt för AKS-loggarna att skicka till den angivna Log Analytics-arbetsytan.
    - **Log Analytics-arbetsyta:** Log Analytics-arbetsytan fylls i med standardarbetsytan som skapas om övervakning har aktiverats.

8. Välj **fliken Granska och** skapa. Det här steget utlöser valideringen för de värden som du har angett.
9. När valideringen har passerat väljer du **Skapa**.

    Distributionen tar vanligtvis 10 till 12 minuter. Tiden kan variera beroende på den angivna storleken och antalet AKS-noder.
10. Efter en lyckad distribution får du ett meddelande via Azure-meddelanden i det övre högra hörnet.
11. Välj **Gå till resursgrupp för** att kontrollera alla resurser som skapats som en del av malldistributionen. Alla resursnamn börjar med prefixet som anges på **fliken Grundläggande** inställningar.

## <a name="build-the-consortium"></a>Skapa konsortium

Utför följande steg i följd för att skapa blockkedjekonsortiumet när du har distribuerat beställningstjänsten och peer-noderna. Azure Hyperledger Fabric (azhlf) hjälper dig att konfigurera konsortium, skapa kanalen och utföra kedjekodsåtgärder.

> [!NOTE]
> Skriptet Azure Hyperledger Fabric (azhlf) har uppdaterats för att ge fler funktioner. Om du vill referera till det gamla skriptet kan du läsa [readme på GitHub.](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md) Det här skriptet är kompatibelt Hyperledger Fabric på Azure Kubernetes Service version 2.0.0 och senare. Om du vill kontrollera versionen av distributionen följer du stegen i [Felsöka](#troubleshoot).

> [!NOTE]
> Skriptet tillhandahålls endast för att hjälpa till med demonstrations-, utvecklings- och testscenarier. Kanalen och konsortium som skriptet skapar har grundläggande principer Hyperledger Fabric för att förenkla demo-, utvecklings- och testscenarier. För produktionskonfiguration rekommenderar vi att du uppdaterar kanal-/konsortium-Hyperledger Fabric principer i enlighet med organisationens efterlevnadsbehov med hjälp av de inbyggda Hyperledger Fabric-API:erna.


Alla kommandon för att köra Azure Hyperledger Fabric-skriptet kan köras via Azure Bash-kommandoradsgränssnittet (CLI). Du kan logga in Azure Cloud Shell via throughâ Hyperledger Fabric på Azure Kubernetes Service mallalternativet i det övre ![ ](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) högra hörnet av Azure Portal. I kommandotolken skriver och väljer du returtangenten för att växla till `bash` Bash CLI eller väljer **Bash** från Cloud Shell verktygsfältet.

Se [Azure Cloud Shell](../../cloud-shell/overview.md) för mer information.

![Skärmbild som visar kommandon i Azure Cloud Shell.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Följande bild visar den stegvisa processen för att skapa ett konsortium mellan en orderorganisation och en peer-organisation. I följande avsnitt visas detaljerade kommandon för att slutföra de här stegen.

![Diagram över processen för att skapa ett konsortium.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

När du är klar med den första installationen använder du klientprogrammet för att uppnå följande åtgärder: â... â

- Kanalhantering
- Konsortiumhantering
- Hantering av kedjekod

### <a name="download-client-application-files"></a>Ladda ned klientprogramfiler

Den första installationen är att ladda ned klientprogramfilerna. Kör följande kommandon för att ladda ned alla nödvändiga filer och paket:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup
```

Dessa kommandon klonar Azure Hyperledger Fabric klientprogramkod från den offentliga GitHub-lagringsplatsen, följt av inläsning av alla beroende npm-paket. När kommandot har körs kan du se en node_modules mapp i den aktuella katalogen. Alla nödvändiga paket läses in i node_modules mappen.

### <a name="set-up-environment-variables"></a>Konfigurera miljövariabler

Alla miljövariabler följer namngivningskonventionen för Azure-resurser.

#### <a name="set-environment-variables-for-the-orderer-organizations-client"></a>Ange miljövariabler för orderorganisationens klient

```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

#### <a name="set-environment-variables-for-the-peer-organizations-client"></a>Ange miljövariabler för peer-organisationens klient

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

Baserat på antalet peer-organisationer i ditt konsortium kan du behöva upprepa peer-kommandona och ange miljövariabeln i enlighet med detta.

#### <a name="set-environment-variables-for-an-azure-storage-account"></a>Ange miljövariabler för ett Azure Storage-konto

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

Använd följande kommandon för att skapa ett Azure Storage-konto. Om du redan har ett Azure Storage-konto hoppar du över det här steget.

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

Använd följande kommandon för att skapa en filresurs i Azure Storage-kontot. Om du redan har en filresurs hoppar du över det här steget.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

Använd följande kommandon för att generera en anslutningssträng för en Azure-filresurs.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-an-organization-connection-profile-admin-user-identity-and-msp"></a>Importera en organisationsanslutningsprofil, administratörsanvändaridentitet och MSP

Använd följande kommandon för att hämta organisationens anslutningsprofil, administratörsanvändaridentitet och hanterad tjänstprovider (MSP) från Azure Kubernetes Service-klustret och lagra dessa identiteter i klientprogrammets lokala arkiv. Ett exempel på ett lokalt arkiv är *katalogen azhlfTool/stores.*

För orderorganisationen:

```bash
./azhlf adminProfile import fromAzure -o $ORDERER_ORG_NAME -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION
./azhlf connectionProfile import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME   
./azhlf msp import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME
```

För peer-organisationen:

```bash
./azhlf adminProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf connectionProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf msp import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
```

### <a name="create-a-channel"></a>Skapa en kanal

Från orderorganisationens klient använder du följande kommando för att skapa en kanal som endast innehåller orderorganisationen.  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

### <a name="add-a-peer-organization-for-consortium-management"></a>Lägga till en peer-organisation för konsortiumhantering

>[!NOTE]
> Innan du börjar med en konsortiumåtgärd måste du se till att du har slutfört den första konfigurationen av klientprogrammet.  

Kör följande kommandon i den angivna ordningen för att lägga till en peer-organisation i en kanal och ett konsortium: 

1.  Från peer-organisationens klient laddar du upp peer-organisationens MSP på Azure Storage.

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  Från orderorganisationens klient laddar du ned peer-organisationens MSP från Azure Storage. Utfärda sedan kommandot för att lägga till peer-organisationen i kanalen och konsortiumet.

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  Ladda upp beställaranslutningsprofilen från orderorganisationens klient på Azure Storage så att peer-organisationen kan ansluta till ordernoderna med hjälp av den här anslutningsprofilen.

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  Från peer-organisationens klient laddar du ned orderns anslutningsprofil från Azure Storage. Kör sedan kommandot för att lägga till peer-noder i kanalen.

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

Om du vill lägga till fler peer-organisationer i kanalen uppdaterar du peer-miljövariablerna enligt den nödvändiga peer-organisationen och gör om steg 1 till 4.

### <a name="set-anchor-peers"></a>Ange fästpunkts-peers

Från peer-organisationens klient kör du kommandot för att ange fästpunkts-peers för peer-organisationen i den angivna kanalen.

>[!NOTE]
> Innan du kör det här kommandot måste du se till att peer-organisationen läggs till i kanalen med hjälp av konsortiumhanteringskommandon.

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY --ordererOrg $ORDERER_ORG_NAME
```

`<anchorPeersList>` är en blankstegsavgränsad lista över peer-noder som ska anges som en fästpunkts-peer. Exempel:

  - Ange `<anchorPeersList>` som om du bara vill ange `"peer1"` peer1-noden som en fästpunkts-peer.
  - Ange `<anchorPeersList>` som om du vill ange både `"peer1" "peer3"` peer1- och peer3-noder som fästpunkts-peers.

## <a name="chaincode-management-commands"></a>Hanteringskommandon för kedjekod

>[!NOTE]
> Innan du börjar med en kedjekodsåtgärd måste du kontrollera att du har gjort den första installationen av klientprogrammet.  

### <a name="set-the-chaincode-specific-environment-variables"></a>Ange kedjekodsspecifika miljövariabler

```bash
# Peer organization name where the chaincode operation will be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=â€œchaincode_example02â€
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=â€œ1â€ for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang', and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is placed. This is the absolute path to the chaincode project root directory.
# If you are using chaincode_example02 to validate then CC_PATH=â€œ/home/<username>/azhlfTool/samples/chaincode/src/chaincode_example02/goâ€
CC_PATH=<chaincodePath>  
# Channel on which chaincode will be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

### <a name="install-chaincode"></a>Installera chaincode  

Kör följande kommando för att installera chaincode på peer-organisationen.  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
Kommandot installerar chaincode på alla peer-noder i peer-organisationen som angetts i `ORGNAME` miljövariabeln. Om två eller flera peer-organisationer finns i din kanal och du vill installera chaincode på dem alla, kör du det här kommandot separat för varje peer-organisation.  

Följ de här stegen:  

1.  Ange `ORGNAME` och enligt och kör kommandot `USER_IDENTITY` `peerOrg1` `./azhlf chaincode install` .  
2.  Ange `ORGNAME` och enligt och kör kommandot `USER_IDENTITY` `peerOrg2` `./azhlf chaincode install` .  

### <a name="instantiate-chaincode"></a>Instansiera chaincode  

Kör följande kommando från peer-klientprogrammet för att instansiera chaincode på kanalen.  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -v $CC_VERSION -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>
```

Skicka instansieringsfunktionens namn och blankstegsavgränsad lista med argument `<instantiateFunc>` `<instantiateFuncArgs>` i respektive . Om du till exempel vill instansiera chaincode_example02.go-kedjekod anger `<instantiateFunc>` du till och till `init` `<instantiateFuncArgs>` `"a" "2000" "b" "1000"` .

Du kan också skicka samlingens JSON-konfigurationsfil med hjälp av `--collections-config` flaggan . Du kan också ange tillfälliga argument med hjälp av `-t` flaggan när du instansierar den kedjekod som används för privata transaktioner.

Exempel:

```bash
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath>
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath> -t <transientArgs>
```

Delen `<collectionConfigJSONFilePath>` är sökvägen till JSON-filen som innehåller samlingarna som definierats för instansiering av privat datakedjekod. Du hittar en exempelsamlings JSON-konfigurationsfil i förhållande till *katalogen azhlfTool* på följande sökväg: `./samples/chaincode/src/private_marbles/collections_config.json` .
Skicka `<transientArgs>` som giltig JSON i strängformat. Undvik eventuella specialtecken. Exempelvis: `'{\\\"asset\":{\\\"name\\\":\\\"asset1\\\",\\\"price\\\":99}}'`

> [!NOTE]
> Kör kommandot en gång från en peer-organisation i kanalen. När transaktionen har skickats till beställaren distribuerar beställaren transaktionen till alla peer-organisationer i kanalen. Chaincode instansieras sedan på alla peer-noder på alla peer-organisationer i kanalen.  

### <a name="invoke-chaincode"></a>Anropa chaincode  

Kör följande kommando från peer-organisationens klient för att anropa funktionen chaincode:  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

Skicka invoke-funktionsnamnet och den blankstegsavgränsade listan med argument inâ° `<invokeFunction>` â â° âandâ° ° `<invokeFuncArgs>` â ârespectively. Vi fortsätter med chaincode_example02.go-kedjekodsexempel för att utföra en invoke-åtgärd, setâ™ â `<invokeFunction>` â° âtoâ... â `invoke` âandâ€ â `<invokeFuncArgs>` â till `"a" "b" "10"` .  

>[!NOTE]
> Kör kommandot en gång från en peer-organisation i kanalen. När transaktionen har skickats till beställaren distribuerar beställaren transaktionen till alla peer-organisationer i kanalen. Världstillståndet uppdateras sedan på alla peer-noder för alla peer-organisationer i kanalen.  


### <a name="query-chaincode"></a>Frågekedjekod  

Kör följande kommando för att fråga chaincode:  

```bash
./azhlf chaincode query -o $ORGNAME -p <endorsingPeers> -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs> 
```
Peer-peers för endors är peer-peers där chaincode är installerat och anropas för körning av transaktioner. Du måste ange `<endorsingPeers>` att ska innehålla peer-nodnamn från den aktuella peer-organisationen. Visa en lista med peer-kodare för en viss kombination av kedjekod och kanal avgränsade med blanksteg. Exempel: `-p "peer1" "peer3"`.

Om du använder *azhlfTool* för att installera chaincode skickar du eventuella peer-nodnamn som ett värde till peer-argumentet för endorsing. Chaincode installeras på varje peer-nod för den organisationen. 

Skicka frågefunktionsnamnet och den blankstegsavgränsade listan med argument inâ° `<queryFunction>` â â° âandâ° ° `<queryFuncArgs>` âspectively. Ta återigen â° â chaincode_example02.goâ€ âchaincode som referens för att fråga värdet för "a" i världstillståndet, setâ™ â `<queryFunction>` â™ âtoâ€ â `query` andâ... â `<queryArgs>` till `"a"` .  

## <a name="troubleshoot"></a>Felsöka

### <a name="find-deployed-version"></a>Hitta distribuerad version

Kör följande kommandon för att hitta versionen av malldistributionen. Ange miljövariabler enligt resursgruppen där mallen har distribuerats.

```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3
```

### <a name="patch-previous-version"></a>Korrigera tidigare version

Om du har problem med att köra chaincode på distributioner av mallversion tidigare än v3.0.0 följer du stegen nedan för att korrigera peer-noderna med en korrigering.

Ladda ned peer-distributionsskriptet.

```bash
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/scripts/patchPeerDeployment.sh -o patchPeerDeployment.sh; chmod 777 patchPeerDeployment.sh
```

Kör skriptet med följande kommando och ersätt parametrarna för din peer.

```bash
source patchPeerDeployment.sh <peerOrgSubscription> <peerOrgResourceGroup> <peerOrgAKSClusterName>
```

Vänta tills alla peer-noder har korrigerats. Du kan alltid kontrollera statusen för dina peer-noder i en annan instans av gränssnittet med hjälp av följande kommando.

```bash
kubectl get pods -n hlf
```

## <a name="support-and-feedback"></a>Support och feedback

Om du vill hålla dig uppdaterad om blockkedjetjänsterbjudanden och information från Azure Blockchain-teknikteamet kan du besöka [Azure Blockchain blogg](https://azure.microsoft.com/blog/topics/blockchain/).

Om du vill ge produktfeedback eller begära nya funktioner kan du publicera eller rösta på en idé via [Azures feedbackforum för blockkedjan](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Community-support

Interagera med Microsofts tekniker och Azure Blockchain experter i communityn:

- [Microsoft Q&A-sida](/answers/topics/azure-blockchain-workbench.html) 
   
  Teknisk support för blockkedjemallar är begränsad till distributionsproblem.
- [Microsofts tekniska community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)
