---
title: Distribuera en huvud boks infrastrukturs konsortium i Azure Kubernetes-tjänsten
description: Så här distribuerar och konfigurerar du ett huvud nätverk för ett huvud nätverk i Azure Kubernetes service
ms.date: 03/01/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: contperf-fy21q3
ms.openlocfilehash: 42d16adbc5e6396c8d5d38176ac7681c712f4555
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102101111"
---
# <a name="deploy-hyperledger-fabric-consortium-on-azure-kubernetes-service"></a>Distribuera en huvud boks infrastrukturs konsortium i Azure Kubernetes-tjänsten

Du kan använda huvud strukturen i Kubernetes-mallen (AKS) för att distribuera och konfigurera ett huvud nätverk för ett huvud nätverk i Azure.

När du har läst den här artikeln, kommer du att:

- Ha en fungerande kunskap om huvud struktur och de komponenter som utgör Bygg stenarna i ett blockchain nätverk i ett huvud nätverk.
- Lär dig hur du distribuerar och konfigurerar ett huvud nätverk i Kubernetes i Azure service för dina produktions scenarier.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Välj en Azure blockchain-lösning

Innan du väljer att använda en lösnings mall, jämför ditt scenario med vanliga användnings fall av tillgängliga alternativ för Azure-blockchain:

Alternativ | Tjänst modell | Vanligt användnings fall
-------|---------------|-----------------
Lösningsmallar | IaaS | Solution templates är Azure Resource Manager mallar som du kan använda för att etablera en helt konfigurerad blockchain-nätverkstopologi. Mallarna distribuerar och konfigurerar Microsoft Azure beräknings-, nätverks-och lagrings tjänster för en blockchain-nätverks typ. Solution templates tillhandahålls utan ett service nivå avtal. Använd [Microsoft Q&en sida](/answers/topics/azure-blockchain-workbench.html) för support.
[Azure Blockchain Service](../service/overview.md) | PaaS | För hands versionen av Azure blockchain service fören klar investeringen, hanteringen och styrningen av konsortiet blockchain-nätverk. Använd Azure blockchain-tjänsten för lösningar som kräver PaaS, konsortiets hantering eller kontrakt och transaktions sekretess.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS och PaaS | Azure blockchain Workbench Preview är en samling Azure-tjänster och-funktioner som hjälper dig att skapa och distribuera blockchain-program för att dela affärs processer och data med andra organisationer. Använd Azure blockchain Workbench för prototyp av en blockchain-lösning eller ett koncept bevis för ett blockchain-program. Azure blockchain Workbench tillhandahålls utan ett service nivå avtal. Använd [Microsoft Q&en sida](/answers/topics/azure-blockchain-workbench.html) för support.

## <a name="deploy-the-orderer-and-peer-organization"></a>Distribuera beställnings-och peer-organisationen

För att börja måste du ha en Azure-prenumeration som har stöd för distribution av flera virtuella datorer och standard lagrings konton. Om du inte har någon Azure-prenumeration kan du [skapa ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/).

Gå till [Azure Portal](https://portal.azure.com)om du vill komma igång med distributionen av nätverks komponenter för huvud nätverk.

1. Välj **skapa en resurs**  >  **blockchain** och Sök sedan efter **Ledger Fabric på Azure Kubernetes service (för hands version)**.

2. Ange projekt informationen på fliken **grundläggande** .

    ![Skärm bild som visar fliken grundläggande.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Ange följande information:
    - **Prenumeration**: Välj namnet på den prenumeration där du vill distribuera nätverks komponenterna för huvud nätverket.
    - **Resurs grupp**: skapa en ny resurs grupp eller Välj en befintlig tom resurs grupp. Resurs gruppen innehåller alla resurser som distribueras som en del av mallen.
    - **Region**: Välj den Azure-region där du vill distribuera Azure Kubernetes service-klustret för de båda komponenterna i huvud strukturen. Mallen är tillgänglig i alla regioner där AKS är tillgänglig. Välj en region där din prenumeration inte når kvot gränsen för den virtuella datorn (VM).
    - **Resource prefix**: Ange ett prefix för namngivning av resurser som har distribuerats. Det måste innehålla färre än sex tecken och kombinationen av tecken måste innehålla både siffror och bokstäver.
4. Välj fliken **infrastruktur inställningar** om du vill definiera de nätverks komponenter för huvud nätverk som ska distribueras.

    ![Skärm bild som visar fliken infrastruktur inställningar.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Ange följande information:
    - **Organisations namn**: Ange namnet på den huvud organisations organisation som krävs för olika data Plans åtgärder. Organisations namnet måste vara unikt för varje distribution.
    - **Nätverks komponent för infrastruktur resurser**: Välj antingen **beställnings tjänst** eller **peer-noder** baserat på den blockchain nätverks komponent som du vill konfigurera.
    - **Antal noder**: följande två typer av noder:
        - **Beställnings tjänst**: noder som ansvarar för transaktions beställning i redovisningen. Välj antalet noder för att ge fel tolerans till nätverket. Antalet noder som stöds är 3, 5 och 7.
        - **Peer-noder**: noder som är värdar för redovisning och smarta kontrakt. Du kan välja 1 till 10 noder baserat på ditt krav.
    - **Peer-nodens världs tillstånds databas**: världs tillstånds databaser för peer-noderna. LevelDB är standard läges databasen som är inbäddad i peer-noden. Den lagrar chaincode-data som enkla nyckel/värde-par och stöder nyckel-, nyckel intervall och sammansatta nyckel frågor. CouchDB är en valfri alternativ tillstånds databas som stöder omfattande frågor när chaincode-datavärden modelleras som JSON. Det här fältet visas när du väljer **peer-noder** i list rutan för **nätverks komponent för infrastruktur resurser** .
    - **Infrastruktur certifikat utfärdarens användar namn**: infrastruktur certifikat utfärdaren gör det möjligt att initiera och starta en server process som är värd för certifikat utfärdaren. Med den kan du hantera identiteter och certifikat. Varje AKS-kluster som distribueras som en del av mallen kommer att ha en POD för Fabric-certifikat som standard. Ange det användar namn som används för Fabric CA-autentisering.
    - **Lösen ord för infrastruktur certifikat utfärdare**: Ange lösen ordet för Fabric-ca-autentisering.
    - **Bekräfta lösen ord**: bekräfta lösen ordet för infrastruktur certifikat utfärdaren.
    - **Certifikat**: om du vill använda dina egna rot certifikat för att initiera Fabric-ca: n väljer du alternativet **för att ladda upp rot certifikat för infrastruktur certifikat utfärdare** . Annars skapar Fabric CA: n självsignerade certifikat som standard.
    - **Rot certifikat**: Ladda upp rot certifikatet (offentlig nyckel) som Fabric-certifikatutfärdare måste initieras med. Certifikat av. pem-format stöds. Certifikaten ska vara giltiga och i en UTC-tidszon.
    - **Privat nyckel för rot certifikat**: Ladda upp den privata nyckeln för rot certifikatet. Om du har ett. PEM-certifikat, som har en kombinerad offentlig och privat nyckel, laddar du upp det här även.


6. Välj fliken **AKS-kluster inställningar** för att definiera Azure Kubernetes service Cluster-konfigurationen. AKS-klustret har olika poddar som har kon figurer ATS för att köra de olika nätverks komponenterna i huvud boks infrastrukturen. De distribuerade Azure-resurserna är:

    - **Infrastruktur verktyg**: verktyg som ansvarar för att konfigurera de båda komponenterna i huvud boken.
    - **Ordnings-/peer-poddar**: noderna i det mikroredovisningen i Fabric-nätverket.
    - **Proxy**: en ngnix proxy-Pod som klient programmen kan kommunicera med AKS-klustret.
    - **Infrastruktur certifikat utfärdare**: Pod som kör Fabric ca: n.
    - **Postgresql**: databas instans som underhåller Fabric ca-identiteter.
    - **Key Vault**: en instans av den Azure Key Vault tjänst som har distribuerats för att spara autentiseringsuppgifterna för Fabric-certifikat utfärdare och de rot certifikat som tillhandahålls av kunden. Valvet används vid försök att distribuera mallar för att hantera Mechanics för mallen.
    - **Hanterad disk**: instans av Azure Managed disks-tjänsten som tillhandahåller ett beständigt arkiv för redovisningen och för peer-nodens databas för världs tillstånd.
    - **Offentlig IP**: slut punkt för det AKS-kluster som har distribuerats för kommunikation med klustret.

    Ange följande information: 

    ![Skärm bild som visar fliken A K S kluster inställningar.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

    - **Kubernetes-kluster namn**: ändra namnet på AKS-klustret, om det behövs. Det här fältet fylls i automatiskt baserat på det resurs prefix som anges.
    - **Kubernetes-version**: Välj den version av Kubernetes som ska distribueras i klustret. Utifrån den region som du valde på fliken **grundläggande** kan de tillgängliga versionerna ändras.
    - **DNS-prefix**: Ange ett namn för Domain Name System (DNS) för AKS-klustret. Du använder DNS för att ansluta till Kubernetes-API: et när du hanterar behållare när du har skapat klustret.
    - **Node-storlek**: för Kubernetes-nodens storlek kan du välja från listan över virtuella dator enheter (SKU: er) som är tillgängliga i Azure. För bästa prestanda rekommenderar vi standard DS3 v2.
    - **Antal noder**: Ange antalet Kubernetes-noder som ska distribueras i klustret. Vi rekommenderar att du behåller antalet noder som är lika med eller större än antalet noder i den infrastruktur resurs som anges på fliken **infrastruktur inställningar** .
    - **Klient-ID för tjänstens huvud namn**: Ange klient-ID för ett befintligt huvud namn för tjänsten eller skapa ett nytt. Ett huvud namn för tjänsten krävs för AKS-autentisering. Se [stegen för att skapa ett huvud namn för tjänsten](/powershell/azure/create-azure-service-principal-azureps#create-a-service-principal).
    - **Tjänstens huvud namn**: Ange klient hemligheten för tjänstens huvud namn som anges i klient-ID: t för tjänstens huvud namn.
    - **Bekräfta klient hemlighet**: bekräfta klient hemligheten för tjänstens huvud namn.
    - **Aktivera övervakning av behållare**: Välj att aktivera AKS-övervakning, vilket gör att AKS-loggarna kan push-överföras till den angivna Log Analytics-arbetsytan.
    - **Log Analytics arbets yta**: arbets ytan Log Analytics fylls i med standard arbets ytan som skapas om övervakning har Aktiver ATS.

8. Välj fliken **Granska och skapa** . Det här steget utlöser verifieringen för de värden som du har angett.
9. När verifieringen har godkänts väljer du **skapa**.

    Distributionen tar vanligt vis 10 till 12 minuter. Tiden kan variera beroende på de angivna storlekarna och antalet AKS-noder.
10. När distributionen är klar får du ett meddelande via Azure-meddelanden i det övre högra hörnet.
11. Välj **gå till resurs grupp** för att kontrol lera alla resurser som skapats som en del av mall distributionen. Alla resurs namn börjar med det prefix som visas på fliken **grundläggande** .

## <a name="build-the-consortium"></a>Bygg konsortiet

För att bygga blockchain-konsortiet när du har distribuerat beställnings tjänsten och peer-noder, utför du följande steg i följd. Med Azures azhlf (Azures Fabric-skript) kan du konfigurera konsortiet, skapa kanalen och utföra chaincode-åtgärder.

> [!NOTE]
> Azhlf-skriptet (Azures) har uppdaterats för att ge fler funktioner. Om du vill referera till det gamla skriptet går du till [README på GitHub](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md). Det här skriptet är kompatibelt med huvud strukturen i Azure Kubernetes Service Template version 2.0.0 och senare. Följ anvisningarna i [fel sökning](#troubleshoot)för att kontrol lera distributions versionen.

> [!NOTE]
> Skriptet tillhandahålls för att hjälpa till med demonstration, utveckling och testnings scenarier. Den kanal och det konsortium som det här skriptet skapar har grundläggande principer för att förenkla demo-, utvecklings-och test scenarier. För produktions konfigurationen rekommenderar vi att du uppdaterar principer för den virtuella datorn i kanal/konsortiet i enlighet med organisationens krav för att använda inbyggda API: er för huvud boken.


Alla kommandon för att köra Azures Fabric-skript kan köras via Azure bash kommando rads gränssnitt (CLI). Du kan logga in på Azure Cloud Shell via det ![ inbyggda Kubernetes i det ](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) övre högra hörnet i Azure Portal. I kommando tolken skriver du `bash` och väljer nyckeln Enter för att växla till bash CLI eller väljer **bash** i verktygsfältet Cloud Shell.

Se [Azure Cloud Shell](../../cloud-shell/overview.md) för mer information.

![Skärm bild som visar kommandon i Azure Cloud Shell.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Följande bild visar steg för steg-processen för att bygga ett konsortium mellan en ordnings organisation och en peer-organisation. I följande avsnitt visas detaljerade kommandon för att utföra de här stegen.

![Diagram över processen för att bygga ett konsortium.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

När du har slutfört den första installationen använder du klient programmet för att uppnå följande åtgärder:  

- Kanal hantering
- Hantering av konsortiet
- Hantering av Chaincode

### <a name="download-client-application-files"></a>Hämta filer för klient program

Den första installationen är att ladda ned klient program filerna. Kör följande kommandon för att ladda ned alla filer och paket som krävs:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup
```

De här kommandona kommer att klona kod för Azures Fabric-klientprogram från den offentliga GitHub-lagrings platsen och sedan läsa in alla beroende NPM-paket. När kommandot har körts kan du se en node_modules-mapp i den aktuella katalogen. Alla nödvändiga paket läses in i mappen node_modules.

### <a name="set-up-environment-variables"></a>Konfigurera miljövariabler

Alla miljövariabler följer namngivnings konventionen för Azure-resurser.

#### <a name="set-environment-variables-for-the-orderer-organizations-client"></a>Ange miljövariabler för beställnings organisationens klient

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

Baserat på antalet peer-organisationer i konsortiet kan du behöva upprepa peer-kommandona och ställa in miljövariabeln enligt detta.

#### <a name="set-environment-variables-for-an-azure-storage-account"></a>Ange miljövariabler för ett Azure Storage-konto

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

Använd följande kommandon för att skapa ett Azure Storage-konto. Hoppa över det här steget om du redan har ett Azure Storage-konto.

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

Använd följande kommandon för att skapa en fil resurs i Azure Storage-kontot. Hoppa över det här steget om du redan har en fil resurs.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

Använd följande kommandon för att skapa en anslutnings sträng för en Azure-filresurs.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-an-organization-connection-profile-admin-user-identity-and-msp"></a>Importera en organisations anslutnings profil, administratörs användarens identitet och MSP

Använd följande kommandon för att hämta organisationens anslutnings profil, administratörs användarens identitet och hanterad tjänst leverantör (MSP) från Azure Kubernetes service-klustret och lagra dessa identiteter i klient programmets lokala arkiv. Ett exempel på ett lokalt Arkiv är katalogen *azhlfTool/* Store.

För ordnings organisationen:

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

Använd följande kommando från orderns organisations klient för att skapa en kanal som bara innehåller order organisationen.  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

### <a name="add-a-peer-organization-for-consortium-management"></a>Lägg till en peer-organisation för hantering av konsortiet

>[!NOTE]
> Innan du börjar med en konsortiums åtgärd bör du kontrol lera att du har slutfört den första installationen av klient programmet.  

Kör följande kommandon i angiven ordning för att lägga till en peer-organisation i en kanal och konsortiet: 

1.  Ladda upp peer-organisationens MSP på Azure Storage från peer-organisationens klient.

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  Från beställnings organisationens klient hämtar du peer-organisationens MSP från Azure Storage. Utfärda sedan kommandot för att lägga till peer-organisationen i kanalen och konsortiet.

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  Från beställnings organisationens klient laddar du upp orderns anslutnings profil på Azure Storage så att peer-organisationen kan ansluta till beställnings noderna med hjälp av den här anslutnings profilen.

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  Från peer-organisationens klient hämtar du orderns anslutnings profil från Azure Storage. Kör sedan kommandot för att lägga till peer-noder i kanalen.

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

På samma sätt kan du lägga till fler peer-organisationer i kanalen genom att uppdatera peer-miljövariabler enligt den nödvändiga peer-organisationen och göra om steg 1 till 4.

### <a name="set-anchor-peers"></a>Ange Anchor-peers

Från peer-organisationens klient kör du kommandot för att ange Anchor-peers för peer-organisationen i den angivna kanalen.

>[!NOTE]
> Innan du kör det här kommandot måste du se till att peer-organisationen läggs till i kanalen med hjälp av hanterings kommandon för konsortiet.

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY --ordererOrg $ORDERER_ORG_NAME
```

`<anchorPeersList>` är en blankstegsavgränsad lista med peer-noder som ska anges som en Anchor-peer. Exempel:

  - Ange `<anchorPeersList>` som `"peer1"` om du bara vill ange peer1-noden som en Anchor-peer.
  - Ange `<anchorPeersList>` som `"peer1" "peer3"` om du vill ange både peer1-och peer3-noder som Anchor-peer-datorer.

## <a name="chaincode-management-commands"></a>Chaincode hanterings kommandon

>[!NOTE]
> Innan du börjar med en chaincode-åtgärd bör du kontrol lera att du har gjort den första installationen av klient programmet.  

### <a name="set-the-chaincode-specific-environment-variables"></a>Ange de chaincode miljövariablerna

```bash
# Peer organization name where the chaincode operation will be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=“chaincode_example02”
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=“1” for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang', and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is placed. This is the absolute path to the chaincode project root directory.
# If you are using chaincode_example02 to validate then CC_PATH=“/home/<username>/azhlfTool/samples/chaincode/src/chaincode_example02/go”
CC_PATH=<chaincodePath>  
# Channel on which chaincode will be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

### <a name="install-chaincode"></a>Installera chaincode  

Kör följande kommando för att installera chaincode på peer-organisationen.  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
Kommandot installerar chaincode på alla peer-noder i peer-organisationen som anges i `ORGNAME` miljö variabeln. Om två eller flera peer-organisationer finns i din kanal och du vill installera chaincode på alla, kör du kommandot separat för varje peer-organisation.  

Följ de här stegen:  

1.  Ange `ORGNAME` och `USER_IDENTITY` enligt `peerOrg1` och kör `./azhlf chaincode install` kommandot.  
2.  Ange `ORGNAME` och `USER_IDENTITY` enligt `peerOrg2` och kör `./azhlf chaincode install` kommandot.  

### <a name="instantiate-chaincode"></a>Instansiera chaincode  

Kör följande kommando från peer-klient programmet för att instansiera chaincode på kanalen.  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -v $CC_VERSION -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>
```

Överför de Instansierings funktions namn och blankstegsavgränsad listan med argument i respektive `<instantiateFunc>` `<instantiateFuncArgs>` . Om du till exempel vill instansiera chaincode_example02. go-chaincode, ange `<instantiateFunc>` till `init` och `<instantiateFuncArgs>` till `"a" "2000" "b" "1000"` .

Du kan också skicka samlingens konfigurations-JSON-fil med hjälp av `--collections-config` flaggan. Eller ange de tillfälliga argumenten med hjälp av `-t` flaggan när du instansierar chaincode som används för privata transaktioner.

Exempel:

```bash
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath>
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath> -t <transientArgs>
```

`<collectionConfigJSONFilePath>`Delen är sökvägen till JSON-filen som innehåller de samlingar som definierats för instansiering av privata data chaincode. Du kan hitta en exempel samlings konfigurations-JSON-fil i förhållande till *azhlfTool* -katalogen på följande sökväg: `./samples/chaincode/src/private_marbles/collections_config.json` .
Skicka `<transientArgs>` som giltig JSON i sträng format. Undanta specialtecken. Exempelvis: `'{\\\"asset\":{\\\"name\\\":\\\"asset1\\\",\\\"price\\\":99}}'`

> [!NOTE]
> Kör kommandot en gång från en peer-organisation i kanalen. När transaktionen har skickats till ordern distribuerar beställaren transaktionen till alla peer-organisationer i kanalen. Chaincode instansieras sedan på alla peer-noder på alla peer-organisationer i kanalen.  

### <a name="invoke-chaincode"></a>Anropa chaincode  

Kör följande kommando från peer-organisationens klient för att anropa funktionen chaincode:  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

Överför anrops funktions namnet och den blankstegsavgränsad listan med argument i respektive  `<invokeFunction>`    `<invokeFuncArgs>`   . Fortsätt med chaincode_example02. go-chaincode för att utföra en Invoke-åtgärd, ange  `<invokeFunction>`   till  `invoke`   och  `<invokeFuncArgs>`   till `"a" "b" "10"` .  

>[!NOTE]
> Kör kommandot en gång från en peer-organisation i kanalen. När transaktionen har skickats till ordern distribuerar beställaren transaktionen till alla peer-organisationer i kanalen. Världs delstaten uppdateras sedan på alla peer-noder för alla peer-organisationer i kanalen.  


### <a name="query-chaincode"></a>Fråga chaincode  

Kör följande kommando för att fråga chaincode:  

```bash
./azhlf chaincode query -o $ORGNAME -p <endorsingPeers> -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs> 
```
Stödjande-peer-datorer är peer-datorer där chaincode är installerat och kallas för körning av transaktioner. Du måste ange `<endorsingPeers>` att innehåller peer-nodnamn från den aktuella peer-organisationen. Visa en lista över stödjande-peer-datorer för en angiven chaincode och kanal kombination åtskilda med blank steg. Exempel: `-p "peer1" "peer3"`.

Om du använder *azhlfTool* för att installera chaincode skickar du alla peer-nodnamn som ett värde till stödjande-peer-argumentet. Chaincode installeras på alla peer-noder för den organisationen. 

Överför frågans funktions namn och blankstegsavgränsad lista med argument i respektive  `<queryFunction>`    `<queryFuncArgs>`   . Försök att ta chaincode_example02. go-chaincode som referens, för att fråga värdet "a" i världs delstaten, anges  `<queryFunction>`   till  `query` och  `<queryArgs>` till `"a"` .  

## <a name="troubleshoot"></a>Felsöka

### <a name="find-deployed-version"></a>Hitta distribuerad version

Kör följande kommandon för att hitta versionen av mall distributionen. Ange miljövariabler enligt resurs gruppen där mallen har distribuerats.

```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3
```

### <a name="patch-previous-version"></a>Uppdatera tidigare version

Om du har problem med att köra chaincode på alla distributioner av Template-versionen under v 3.0.0 följer du stegen nedan för att korrigera dina peer-noder med en korrigering.

Hämta skriptet för peer-distribution.

```bash
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/scripts/patchPeerDeployment.sh -o patchPeerDeployment.sh; chmod 777 patchPeerDeployment.sh
```

Kör skriptet med följande kommando och ersätt parametrarna för din peer.

```bash
source patchPeerDeployment.sh <peerOrgSubscription> <peerOrgResourceGroup> <peerOrgAKSClusterName>
```

Vänta tills alla peer-noder kommer att installeras. Du kan alltid kontrol lera status för dina peer-noder i olika instanser av gränssnittet med hjälp av följande kommando.

```bash
kubectl get pods -n hlf
```

## <a name="support-and-feedback"></a>Support och feedback

För att hålla dig uppdaterad om blockchain service-erbjudanden och information från Azure blockchain Engineering-teamet går du till [Azure blockchain-bloggen](https://azure.microsoft.com/blog/topics/blockchain/).

För att ge feedback på produkter eller för att begära nya funktioner, post eller rösta för en idé via [Azure feedback-forumet för blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Community-support

Engagera med Microsoft-tekniker och Azure blockchain community-experter:

- [Microsoft Q&en sida](/answers/topics/azure-blockchain-workbench.html) 
   
  Teknisk support för blockchain-mallar är begränsad till distributions problem.
- [Microsofts Tech-community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)
