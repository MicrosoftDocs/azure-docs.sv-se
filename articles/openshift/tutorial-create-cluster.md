---
title: Självstudie – Skapa ett Azure Red Hat OpenShift 4-kluster
description: Lär dig hur du skapar ett Microsoft Azure Red Hat OpenShift-kluster med Azure CLI
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: d4938d2e4649d62ab656b6854e8176fd82b59a8f
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587743"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>Självstudie: skapa ett Azure Red Hat OpenShift 4-kluster

I den här självstudien, som är del ett av tre, förbereder du din miljö för att skapa ett Azure Red Hat OpenShift-kluster som kör OpenShift 4 och skapar ett kluster. Du lär dig hur du:
> [!div class="checklist"]
> * Konfigurera förutsättningarna och skapa det virtuella nätverk och undernät som krävs
> * Distribuera ett kluster

## <a name="before-you-begin"></a>Innan du börjar

Om du väljer att installera och använda CLI lokalt kräver den här självstudien att du kör Azure CLI-version 2.6.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="verify-your-permissions"></a>Verifiera dina behörigheter

Om du vill skapa ett Azure Red Hat OpenShift-kluster kontrollerar du följande behörigheter för din Azure-prenumeration, Azure Active Directory användare eller tjänstens huvud namn:

|Behörigheter|Resurs grupp som innehåller VNet|Användaren körs `az aro create`|Tjänstens huvud namn angavs som `–client-id`|
|----|:----:|:----:|:----:|
|**Administratör för användaråtkomst**|X|X| |
|**Deltagare**|X|X|X|

### <a name="register-the-resource-provider"></a>Registrera resursprovidern

Sedan måste du registrera `Microsoft.RedHatOpenShift` resurs leverantören i din prenumeration.

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

### <a name="get-a-red-hat-pull-secret-optional"></a>Hämta en Red Hat pull-hemlighet (valfritt)

Med en Red Hat pull-hemlighet kan ditt kluster få åtkomst till Red Hat container-register tillsammans med ytterligare innehåll. Det här steget är valfritt men rekommenderas.

1. **[Gå till Red Hat OpenShift Cluster Manager-portalen](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) och logga in.**

   Du måste logga in på ditt Red Hat-konto eller skapa ett nytt Red Hat-konto med ditt företags-e-post och godkänna de allmänna villkoren.

2. Gå till [**sidan för OpenShift-produkten**](https://developers.redhat.com/products/codeready-containers) om det är första gången du skapar ett kluster. Efter registreringen kan du gå till [**sidan för Red Hat OpenShift-kluster hanteraren**](https://cloud.redhat.com/openshift/)där du kan klicka på **Hämta pull-hemlighet** och ladda ned en pull-hemlighet som ska användas med ditt Aro-kluster.

Behåll den sparade `pull-secret.txt` filen någonstans säker. Filen används i varje kluster som skapas om du behöver skapa ett kluster som innehåller exempel eller operatörer för Red Hat eller certifierade partner.

När du kör `az aro create` kommandot kan du referera till din pull-hemlighet med hjälp av `--pull-secret @pull-secret.txt` parametern. Kör `az aro create` från den katalog där du sparade `pull-secret.txt` filen. Annars ersätter du `@pull-secret.txt` med `@<path-to-my-pull-secret-file>` .

Om du kopierar din pull-hemlighet eller refererar till den i andra skript, ska din pull-hemlighet formateras som en giltig JSON-sträng.

### <a name="prepare-a-custom-domain-for-your-cluster-optional"></a>Förbered en anpassad domän för klustret (valfritt)

När du kör `az aro create` kommandot kan du ange en anpassad domän för klustret med hjälp av- `--domain foo.example.com` parametern.

Om du anger en anpassad domän för klustret noterar du följande punkter:

* När du har skapat klustret måste du skapa två DNS-poster på DNS-servern för de `--domain` angivna:
    * **API** – peka på API-servern
    * ** \* . apps** – peka på ingressen
    * Hämta dessa värden genom att köra följande kommando: `az aro show -n -g --query '{api:apiserverProfile.ip, ingress:ingressProfiles[0].ip}'` .

* OpenShift-konsolen är tillgänglig på en URL, till exempel `https://console-openshift-console.apps.foo.example.com` , i stället för den inbyggda domänen `https://console-openshift-console.apps.<random>.<location>.aroapp.io` .

* Som standard använder OpenShift självsignerade certifikat för alla vägar som skapas på `*.apps.<random>.<location>.aroapp.io` .  Om du väljer att använda anpassad DNS när du har anslutit till klustret måste du följa den OpenShift-dokumentationen för att [Konfigurera en anpassad certifikat utfärdare för din](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) ingångs kontroll och en [anpassad ca för din API-Server](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html).

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Skapa ett virtuellt nätverk som innehåller två tomma undernät

Härnäst ska du skapa ett virtuellt nätverk som innehåller två tomma undernät.

1. **Ange följande variabler i den gränssnitts miljö där du ska köra `az` kommandona.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

1. **Skapa en resurs grupp.**

    En Azure-resursgrupp är en logisk grupp där Azure-resurser distribueras och hanteras. När du skapar en resursgrupp uppmanas du att ange en plats. Den här platsen är den plats där resurs gruppens metadata lagras, men det är även där dina resurser körs i Azure om du inte anger någon annan region när du skapar en resurs. Skapa en resursgrupp med hjälp av kommandot [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create).

    ```azurecli-interactive
    az group create --name $RESOURCEGROUP --location $LOCATION
    ```

    Följande exempelutdata visar den resursgrupp som skapats:

    ```json
    {
    "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
    "location": "eastus",
    "managedBy": null,
    "name": "aro-rg",
    "properties": {
        "provisioningState": "Succeeded"
    },
    "tags": null
    }
    ```

2. **Skapa ett virtuellt nätverk.**

    Azure Red Hat OpenShift-kluster som kör OpenShift 4 kräver ett virtuellt nätverk med två tomma undernät för Master-och Worker-noderna.

    Skapa ett nytt virtuellt nätverk i samma resurs grupp som du skapade tidigare:

    ```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22
    ```

    Följande exempel på utdata visar att det virtuella nätverket har skapats:

    ```json
    {
    "newVNet": {
        "addressSpace": {
        "addressPrefixes": [
            "10.0.0.0/22"
        ]
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

5. **[Inaktivera privata slut punkts principer för undernät](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) i huvud under nätet.** Detta krävs för att kunna ansluta och hantera klustret.

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>Skapa klustret

Kör följande kommando för att skapa ett kluster. Om du väljer att använda något av följande alternativ ändrar du kommandot enligt följande:
* Du kan också [skicka din Red Hat pull-hemlighet](#get-a-red-hat-pull-secret-optional) som gör det möjligt för ditt kluster att komma åt Red Hat container-register tillsammans med ytterligare innehåll. Lägg till `--pull-secret @pull-secret.txt` argumentet i kommandot.
* Du kan också [använda en anpassad domän](#prepare-a-custom-domain-for-your-cluster-optional). Lägg till `--domain foo.example.com` argumentet i kommandot och Ersätt `foo.example.com` med din egen anpassade domän.

> [!NOTE]
> Om du lägger till valfria argument i kommandot måste du stänga argumentet på den föregående raden i kommandot med ett avslutande snedstreck.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
```

När du har kört `az aro create` kommandot tar det vanligt vis cirka 35 minuter att skapa ett kluster.

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig att:
> [!div class="checklist"]
> * Konfigurera förutsättningarna och skapa det virtuella nätverk och undernät som krävs
> * Distribuera ett kluster

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Ansluta till ett Azure Red Hat OpenShift-kluster](tutorial-connect-cluster.md)
