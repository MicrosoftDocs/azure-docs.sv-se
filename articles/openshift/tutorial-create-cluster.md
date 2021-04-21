---
title: Självstudie – Skapa ett Azure Red Hat OpenShift 4-kluster
description: Lär dig hur du skapar ett Microsoft Azure Red Hat OpenShift-kluster med hjälp av Azure CLI
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 10/26/2020
ms.openlocfilehash: dda4fc6a80bbe07977f8d2a5ffcbea895a4e1fe6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771847"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>Självstudie: Skapa ett Azure Red Hat OpenShift 4-kluster

I den här självstudien, som är del ett av tre, förbereder du din miljö för att skapa ett Azure Red Hat OpenShift-kluster som kör OpenShift 4 och skapar ett kluster. Du lär dig följande:
> [!div class="checklist"]
> * Konfigurera förutsättningarna 
> * Skapa det virtuella nätverk och undernät som krävs
> * Distribuera ett kluster

## <a name="before-you-begin"></a>Innan du börjar

Om du väljer att installera och använda CLI lokalt kräver den här självstudien att du kör Azure CLI version 2.6.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Azure Red Hat OpenShift kräver minst 40 kärnor för att skapa och köra ett OpenShift-kluster. Standardkvoten för Azure-resurser för en ny Azure-prenumeration uppfyller inte det här kravet. Information om hur du begär en ökning av resursgränsen finns i [Standardkvot: Öka gränserna med VM-serien](../azure-portal/supportability/per-vm-quota-requests.md).

Pull-hemligheten för ARO ändrar inte kostnaden för RH OpenShift-licensen för ARO.

### <a name="verify-your-permissions"></a>Verifiera dina behörigheter

I den här självstudien skapar du en resursgrupp som innehåller klustrets virtuella nätverk. Du måste antingen ha behörighet som deltagare och administratör för användaråtkomst, eller ägarbehörigheter, antingen direkt i det virtuella nätverket eller i resursgruppen eller prenumerationen som innehåller det.

Du behöver också Azure Active Directory behörighet för verktygen för att skapa ett program och tjänstens huvudnamn åt klustret.

### <a name="register-the-resource-providers"></a>Registrera resursproviders

1. Om du har flera Azure-prenumerationer anger du relevant prenumerations-ID:

    ```azurecli-interactive
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Registrera `Microsoft.RedHatOpenShift` resursprovidern:

    ```azurecli-interactive
    az provider register -n Microsoft.RedHatOpenShift --wait
    ```
    
1. Registrera `Microsoft.Compute` resursprovidern:

    ```azurecli-interactive
    az provider register -n Microsoft.Compute --wait
    ```
    
1. Registrera `Microsoft.Storage` resursprovidern:

    ```azurecli-interactive
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Registrera `Microsoft.Authorization` resursprovidern:

    ```azurecli-interactive
    az provider register -n Microsoft.Authorization --wait
    ```

### <a name="get-a-red-hat-pull-secret-optional"></a>Hämta en Red Hat-pullhemlighet (valfritt)

En Red Hat-pullhemlighet gör att ditt kluster kan komma åt Red Hat-containerregister tillsammans med ytterligare innehåll. Det här steget är valfritt men rekommenderas.

1. [Gå till klusterhanteraren i Red Hat OpenShift och](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) logga in.

   Du måste logga in på ditt Red Hat-konto eller skapa ett nytt Red Hat-konto med din företags-e-postadress och godkänna villkoren.

1. Klicka **på Hämta hämtningshemlighet** och ladda ned en pull-hemlighet som ska användas med ditt ARO-kluster.

    Förvara den sparade `pull-secret.txt` filen på en säker plats. Filen används vid varje klusterskapande om du behöver skapa ett kluster som innehåller exempel eller operatorer för Red Hat eller certifierade partner.

    När du kör `az aro create` kommandot kan du referera till din pull-hemlighet med hjälp av `--pull-secret @pull-secret.txt` parametern . Kör `az aro create` från katalogen där du lagrade `pull-secret.txt` filen. Annars ersätter du `@pull-secret.txt` med `@/path/to/my/pull-secret.txt` .

    Om du kopierar pull-hemligheten eller refererar till den i andra skript ska din pull-hemlighet formateras som en giltig JSON-sträng.

### <a name="prepare-a-custom-domain-for-your-cluster-optional"></a>Förbereda en anpassad domän för klustret (valfritt)

När du kör `az aro create` kommandot kan du ange en anpassad domän för klustret med hjälp av `--domain foo.example.com` parametern .

Observera följande om du anger en anpassad domän för klustret:

* När du har skapat klustret måste du skapa 2 DNS A-poster på DNS-servern för den `--domain` angivna:
    * **api** – pekar på API-serverns IP-adress
    * **\* .apps** – pekar på ingress-IP-adressen
    * Hämta dessa värden genom att köra följande kommando när klustret har skapats: `az aro show -n -g --query '{api:apiserverProfile.ip, ingress:ingressProfiles[0].ip}'` .

* OpenShift-konsolen kommer att vara tillgänglig på en URL, till `https://console-openshift-console.apps.example.com` exempel , i stället för den inbyggda domänen `https://console-openshift-console.apps.<random>.<location>.aroapp.io` .

* Som standard använder OpenShift själv signerade certifikat för alla vägar som skapats på anpassade `*.apps.example.com` domäner.  Om du väljer att använda anpassad DNS när du har anslutit till klustret måste du följa OpenShift-dokumentationen för att konfigurera en anpassad certifikatutfärdare för [din ingress-kontrollant](https://docs.openshift.com/container-platform/4.6/security/certificates/replacing-default-ingress-certificate.html) och en anpassad certifikatutfärdare för [API-servern.](https://docs.openshift.com/container-platform/4.6/security/certificates/api-server.html)

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Skapa ett virtuellt nätverk som innehåller två tomma undernät

Därefter skapar du ett virtuellt nätverk som innehåller två tomma undernät. Om du har ett befintligt virtuellt nätverk som uppfyller dina behov kan du hoppa över det här steget.

1. **Ange följande variabler i gränssnittsmiljön där du ska köra `az` kommandona.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

2. **Skapa en resursgrupp.**

   En Azure-resursgrupp är en logisk grupp där Azure-resurser distribueras och hanteras. När du skapar en resursgrupp uppmanas du att ange en plats. På den här platsen lagras metadata för resursgrupper, och det är också där dina resurser körs i Azure om du inte anger någon annan region när resursen skapas. Skapa en resursgrupp med hjälp av kommandot [az group create](/cli/azure/group#az_group_create).
    
   > [!NOTE] 
   > Azure Red Hat OpenShift är inte tillgängligt i alla regioner där en Azure-resursgrupp kan skapas. Se [Tillgängliga regioner](https://azure.microsoft.com/en-gb/global-infrastructure/services/?products=openshift) för information om var Azure Red Hat OpenShift stöds.

   ```azurecli-interactive
   az group create \
     --name $RESOURCEGROUP \
     --location $LOCATION
   ```

   Följande exempelutdata visar den resursgrupp som skapats:

   ```json
   {
     "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
     "location": "eastus",
     "name": "aro-rg",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "type": "Microsoft.Resources/resourceGroups"
   }
   ```

2. **Skapa ett virtuellt nätverk.**

   Azure Red Hat OpenShift-kluster som kör OpenShift 4 kräver ett virtuellt nätverk med två tomma undernät för huvudnoderna och arbetsnoderna. Du kan antingen skapa ett nytt virtuellt nätverk för detta eller använda ett befintligt virtuellt nätverk.

   Skapa ett nytt virtuellt nätverk i samma resursgrupp som du skapade tidigare:

   ```azurecli-interactive
   az network vnet create \
      --resource-group $RESOURCEGROUP \
      --name aro-vnet \
      --address-prefixes 10.0.0.0/22
   ```

   Följande exempelutdata visar att det virtuella nätverket har skapats:

   ```json
   {
     "newVNet": {
       "addressSpace": {
         "addressPrefixes": [
           "10.0.0.0/22"
         ]
       },
       "dhcpOptions": {
         "dnsServers": []
       },
       "id": "/subscriptions/<guid>/resourceGroups/aro-rg/providers/Microsoft.Network/virtualNetworks/aro-vnet",
       "location": "eastus",
       "name": "aro-vnet",
       "provisioningState": "Succeeded",
       "resourceGroup": "aro-rg",
       "type": "Microsoft.Network/virtualNetworks"
     }
   }
   ```

3. **Lägg till ett tomt undernät för huvudnoderna.**

   ```azurecli-interactive
   az network vnet subnet create \
     --resource-group $RESOURCEGROUP \
     --vnet-name aro-vnet \
     --name master-subnet \
     --address-prefixes 10.0.0.0/23 \
     --service-endpoints Microsoft.ContainerRegistry
   ```

4. **Lägg till ett tomt undernät för arbetsnoderna.**

   ```azurecli-interactive
   az network vnet subnet create \
     --resource-group $RESOURCEGROUP \
     --vnet-name aro-vnet \
     --name worker-subnet \
     --address-prefixes 10.0.2.0/23 \
     --service-endpoints Microsoft.ContainerRegistry
   ```

5. **[Inaktivera principer för privata slutpunkter för undernät](../private-link/disable-private-link-service-network-policy.md) i huvudundernätet.** Detta krävs för att tjänsten ska kunna ansluta till och hantera klustret.

   ```azurecli-interactive
   az network vnet subnet update \
     --name master-subnet \
     --resource-group $RESOURCEGROUP \
     --vnet-name aro-vnet \
     --disable-private-link-service-network-policies true
   ```

## <a name="create-the-cluster"></a>Skapa klustret

Kör följande kommando för att skapa ett kluster. Om du väljer att använda något av följande alternativ ändrar du kommandot på motsvarande sätt:
* Du kan också skicka [din Red Hat-pull-hemlighet](#get-a-red-hat-pull-secret-optional) som gör att ditt kluster kan komma åt Red Hat-containerregister tillsammans med ytterligare innehåll. Lägg till `--pull-secret @pull-secret.txt` argumentet i kommandot.
* Du kan också använda [en anpassad domän.](#prepare-a-custom-domain-for-your-cluster-optional) Lägg till `--domain foo.example.com` argumentet i kommandot och ersätt med din egen anpassade `foo.example.com` domän.

> [!NOTE]
> Om du lägger till valfria argument i kommandot måste du stänga argumentet på föregående rad i kommandot med ett avslutande omslutet snedstreck.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
```

När du har kör `az aro create` kommandot tar det normalt cirka 35 minuter att skapa ett kluster.

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig att:
> [!div class="checklist"]
> * Konfigurera kraven och skapa nödvändiga virtuella nätverk och undernät
> * Distribuera ett kluster

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Ansluta till ett Azure Red Hat OpenShift kluster](tutorial-connect-cluster.md)
