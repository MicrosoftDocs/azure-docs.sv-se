---
title: Montera Azure Files volym till behållar grupp
description: Lär dig hur du monterar en Azure Files volym för att spara tillstånd med Azure Container Instances
ms.topic: article
ms.date: 07/02/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d52ad8ad02735c98b29a83d8ca69cdea8c6af7d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97954982"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Montera en Azure-filresurs i Azure Container Instances

Som standard är Azure Container Instances tillståndslösa. Om behållaren startas om, kraschar eller stoppas försvinner hela sitt tillstånd. Om du vill bevara tillståndet längre än containerns livslängd måste du montera en volym från en extern lagring. Som du ser i den här artikeln kan Azure Container Instances montera en Azure-filresurs som skapats med [Azure Files](../storage/files/storage-files-introduction.md). Azure Files erbjuder fullständigt hanterade fil resurser som finns i Azure Storage som är tillgängliga via SMB-protokollet (Server Message Block) som är bransch standard. Med hjälp av en Azure-filresurs med Azure Container Instances får du fildelnings funktioner som liknar att använda en Azure-filresurs med Azure Virtual Machines.

> [!NOTE]
> Montering av en Azure Files-resurs är för närvarande begränsad till Linux-behållare. Hitta aktuella plattforms skillnader i [översikten](container-instances-overview.md#linux-and-windows-containers).
>
> Montering av en Azure Files resurs till en behållar instans liknar en Docker [BIND-montering](https://docs.docker.com/storage/bind-mounts/). Tänk på att om du monterar en resurs i en behållar katalog där det finns filer eller kataloger döljs dessa filer eller kataloger av monteringen och är inte tillgängliga när behållaren körs.
>

> [!IMPORTANT]
> Om du distribuerar behållar grupper till en Azure-Virtual Network måste du lägga till en [tjänst slut punkt](../virtual-network/virtual-network-service-endpoints-overview.md) till ditt Azure Storage-konto.

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs

Innan du kan använda en Azure-filresurs med Azure Container Instances måste du skapa den. Kör följande skript för att skapa ett lagrings konto som är värd för fil resursen och själva resursen. Namnet på lagringskontot måste vara globalt unikt, så skriptet lägger till ett slumpmässigt värde i bassträngen.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Create the file share
az storage share create \
  --name $ACI_PERS_SHARE_NAME \
  --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Hämta autentiseringsuppgifter för lagringen

När du ska montera en Azure-filresurs som en volym i Azure Container Instances behöver du tre värden: namnet på lagringskontot, resursnamnet och åtkomstnyckeln för lagringen.

* **Lagrings konto namn** – om du använde föregående skript lagras lagrings kontots namn i `$ACI_PERS_STORAGE_ACCOUNT_NAME` variabeln. Om du vill se konto namnet skriver du:

  ```console
  echo $ACI_PERS_STORAGE_ACCOUNT_NAME
  ```

* **Resurs namn** – det här värdet är redan känt (definieras som `acishare` i föregående skript)

* **Lagrings konto nyckel** – det här värdet kan hittas med följande kommando:

  ```azurecli-interactive
  STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
  echo $STORAGE_KEY
  ```

## <a name="deploy-container-and-mount-volume---cli"></a>Distribuera behållare och montera volym – CLI

Om du vill montera en Azure-filresurs som en volym i en behållare med hjälp av Azure CLI anger du resurs-och volym monterings punkt när du skapar behållaren med [AZ container Create][az-container-create]. Om du har följt de föregående stegen kan du montera resursen som du skapade tidigare genom att använda följande kommando för att skapa en behållare:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

`--dns-name-label`Värdet måste vara unikt i den Azure-region där du skapar behållar instansen. Uppdatera värdet i föregående kommando om du får ett fel meddelande om **DNS-namns etikett** när du kör kommandot.

## <a name="manage-files-in-mounted-volume"></a>Hantera filer på monterad volym

När behållaren har startats kan du använda den enkla webbappen som distribueras via Microsoft [ACI-hellofiles-][aci-hellofiles] avbildningen för att skapa små textfiler i Azure-filresursen på den monterings Sök väg du angav. Hämta webbappens fullständigt kvalificerade domän namn (FQDN) med kommandot [AZ container show][az-container-show] :

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP \
  --name hellofiles --query ipAddress.fqdn --output tsv
```

När du har sparat text med appen kan du använda [Azure Portal][portal] eller ett verktyg som [Microsoft Azure Storage Explorer][storage-explorer] för att hämta och inspektera filen eller filerna som skrivs till fil resursen.

## <a name="deploy-container-and-mount-volume---yaml"></a>Distribuera behållare och montera volym – YAML

Du kan också distribuera en behållar grupp och montera en volym i en behållare med Azure CLI och en [yaml-mall](container-instances-multi-container-yaml.md). Att distribuera med YAML-mall är en önskad metod när du distribuerar behållar grupper som består av flera behållare.

Följande YAML-mall definierar en behållar grupp med en behållare som skapats med `aci-hellofiles` avbildningen. Behållaren monterar *acishare* för Azure-filresursen som skapades tidigare som en volym. Ange namnet och lagrings nyckeln för det lagrings konto som är värd för fil resursen. 

Som i CLI-exemplet `dnsNameLabel` måste värdet vara unikt i den Azure-region där du skapar behållar instansen. Uppdatera värdet i YAML-filen om det behövs.

```yaml
apiVersion: '2019-12-01'
location: eastus
name: file-share-demo
properties:
  containers:
  - name: hellofiles
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-hellofiles
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /aci/logs/
        name: filesharevolume
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
      - port: 80
    dnsNameLabel: aci-demo
  volumes:
  - name: filesharevolume
    azureFile:
      sharename: acishare
      storageAccountName: <Storage account name>
      storageAccountKey: <Storage account key>
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

Om du vill distribuera med YAML-mallen sparar du föregående YAML till en fil med namnet `deploy-aci.yaml` och kör sedan kommandot [AZ container Create][az-container-create] med `--file` parametern:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Distribuera behållare och montera volym resurs hanterare

Förutom CLI-och YAML-distribution kan du distribuera en behållar grupp och montera en volym i en behållare med hjälp av en Azure [Resource Manager-mall](/azure/templates/microsoft.containerinstance/containergroups).

Fyll först i `volumes` matrisen i avsnittet behållar grupp i `properties` mallen. 

För varje behållare där du vill montera volymen fyller du sedan `volumeMounts` i matrisen i `properties` avsnittet i behållar definitionen.

Följande Resource Manager-mall definierar en behållar grupp med en behållare som skapats med `aci-hellofiles` avbildningen. Behållaren monterar *acishare* för Azure-filresursen som skapades tidigare som en volym. Ange namnet och lagrings nyckeln för det lagrings konto som är värd för fil resursen. 

Som i föregående exempel `dnsNameLabel` måste värdet vara unikt i den Azure-region där du skapar behållar instansen. Uppdatera värdet i mallen om det behövs.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "container1name": "hellofiles",
    "container1image": "mcr.microsoft.com/azuredocs/aci-hellofiles"
  },
  "resources": [
    {
      "name": "file-share-demo",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "/aci/logs"
                }
              ]
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            }
          ],
          "dnsNameLabel": "aci-demo"
        },
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
                "shareName": "acishare",
                "storageAccountName": "<Storage account name>",
                "storageAccountKey": "<Storage account key>"
            }
          }
        ]
      }
    }
  ]
}
```

Om du vill distribuera med Resource Manager-mallen sparar du föregående JSON till en fil med namnet `deploy-aci.json` och kör sedan kommandot [AZ Deployment Group Create][az-deployment-group-create] med `--template-file` parametern:

```azurecli
# Deploy with Resource Manager template
az deployment group create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Montera flera volymer

Om du vill montera flera volymer i en behållar instans måste du distribuera med hjälp av en [Azure Resource Manager mall](/azure/templates/microsoft.containerinstance/containergroups), en yaml-fil eller en annan programmerings metod. Om du vill använda en mall eller en YAML-fil anger du resurs informationen och definierar volymerna genom att fylla `volumes` i matrisen i `properties` avsnittet i filen. 

Om du till exempel har skapat två Azure Files-resurser med namnet *Share1* och *share2* i lagrings kontot *myStorageAccount*, `volumes` skulle matrisen i en Resource Manager-mall se ut ungefär så här:

```JSON
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

Därefter, för varje behållare i den behållar grupp där du vill montera volymerna, fyller du `volumeMounts` i matrisen i `properties` avsnittet i behållar definitionen. Detta monterar till exempel de två volymerna, *myvolume1* och *myvolume2*, som tidigare definierats:

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du monterar andra volym typer i Azure Container Instances:

* [Montera en emptyDir volym i Azure Container Instances](container-instances-volume-emptydir.md)
* [Montera en gitRepo volym i Azure Container Instances](container-instances-volume-gitrepo.md)
* [Montera en hemlig volym i Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
