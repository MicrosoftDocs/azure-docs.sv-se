---
title: Montera Azure Files volym till containergrupp
description: Lär dig hur du monterar en Azure Files-volym för att bevara tillståndet med Azure Container Instances
ms.topic: article
ms.date: 03/24/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: c541d4faa8728d99fd07396bc056a3e69dc93fe8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763747"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Montera en Azure-filresurs i Azure Container Instances

Som standard är Azure Container Instances tillståndslösa. Om containern startas om, kraschar eller stoppas går allt dess tillstånd förlorat. Om du vill bevara tillståndet längre än containerns livslängd måste du montera en volym från en extern lagring. Som du ser i den här artikeln kan Azure Container Instances montera en Azure-filresurs som skapats med [Azure Files](../storage/files/storage-files-introduction.md). Azure Files erbjuder fullständigt hanterade filresurser i Azure Storage som är tillgängliga via SMB-protokollet (industry standard Server Message Block). Om du använder en Azure-filresurs Azure Container Instances fildelningsfunktioner som liknar användningen av en Azure-filresurs med virtuella Azure-datorer.

## <a name="limitations"></a>Begränsningar

* Du kan bara montera Azure Files till Linux-containrar. Läs mer om skillnaderna i funktionsstöd för Linux- och Windows-containergrupper i [översikten.](container-instances-overview.md#linux-and-windows-containers)
* Azure-filresursens volymmontering kräver att Linux-containern körs som *rot.*
* Azure-filresursvolymmonteringar är begränsade till CIFS-stöd.

> [!NOTE]
> Att montera en Azure Files-resurs till en containerinstans liknar en [Docker-bindningsmontering.](https://docs.docker.com/storage/bind-mounts/) Om du monterar en resurs i en containerkatalog där filer eller kataloger finns, döljer monteringen filer eller kataloger, vilket gör dem otillgängliga medan containern körs.
>

> [!IMPORTANT]
> Om du distribuerar containergrupper till en Azure Virtual Network måste du lägga till en [tjänstslutpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) i ditt Azure Storage konto.

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs

Innan du kan använda en Azure-filresurs med Azure Container Instances måste du skapa den. Kör följande skript för att skapa ett lagringskonto som ska vara värd för filresursen och själva resursen. Namnet på lagringskontot måste vara globalt unikt, så skriptet lägger till ett slumpmässigt värde i bassträngen.

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

* **Namn på lagringskonto** – Om du använde föregående skript lagrades lagringskontots namn i `$ACI_PERS_STORAGE_ACCOUNT_NAME` variabeln . Om du vill se kontonamnet skriver du:

  ```console
  echo $ACI_PERS_STORAGE_ACCOUNT_NAME
  ```

* **Resursnamn** – Det här värdet är redan känt (definierat `acishare` som i föregående skript)

* **Lagringskontonyckel** – Det här värdet kan hittas med följande kommando:

  ```azurecli-interactive
  STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
  echo $STORAGE_KEY
  ```

## <a name="deploy-container-and-mount-volume---cli"></a>Distribuera container och montera volym – CLI

Om du vill montera en Azure-filresurs som en volym i en container med hjälp av Azure CLI anger du resursen och volymens monteringspunkt när du skapar containern [med az container create][az-container-create]. Om du har följt föregående steg kan du montera resursen som du skapade tidigare med hjälp av följande kommando för att skapa en container:

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

Värdet `--dns-name-label` måste vara unikt i den Azure-region där du skapar containerinstansen. Uppdatera värdet i föregående kommando om du får **felmeddelandet DNS-namnetikett** när du kör kommandot.

## <a name="manage-files-in-mounted-volume"></a>Hantera filer på monterad volym

När containern startar kan du använda den enkla webbappen som distribuerats via Avbildningen Microsoft [aci-hellofiles][aci-hellofiles] för att skapa små textfiler i Azure-filresursen på den monteringssökväg som du angav. Hämta webbappens fullständiga domännamn (FQDN) med [kommandot az container show:][az-container-show]

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP \
  --name hellofiles --query ipAddress.fqdn --output tsv
```

När du har sparat text med appen kan du [använda Azure Portal][portal] eller ett verktyg som [Microsoft Azure Storage Explorer][storage-explorer] för att hämta och granska filen eller filerna som skrivits till filresursen.

## <a name="deploy-container-and-mount-volume---yaml"></a>Distribuera container och monteringsvolym – YAML

Du kan också distribuera en containergrupp och montera en volym i en container med Azure CLI och en [YAML-mall](container-instances-multi-container-yaml.md). Att distribuera med EN YAML-mall är en bra metod när du distribuerar containergrupper som består av flera containrar.

Följande YAML-mall definierar en containergrupp med en container som skapats med `aci-hellofiles` avbildningen. Containern monterar Azure-filresursen *acishare* som skapats tidigare som en volym. Där det anges anger du namnet och lagringsnyckeln för det lagringskonto som är värd för filresursen. 

Som i CLI-exemplet måste `dnsNameLabel` värdet vara unikt i den Azure-region där du skapar containerinstansen. Uppdatera värdet i YAML-filen om det behövs.

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

Om du vill distribuera med YAML-mallen sparar du föregående YAML i en fil med namnet och kör sedan `deploy-aci.yaml` kommandot az container [create][az-container-create] med `--file` parametern :

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Distribuera container och montera volym – Resource Manager

Förutom CLI- och YAML-distribution kan du distribuera en containergrupp och montera en volym i en container med hjälp av en Azure [Resource Manager mall](/azure/templates/microsoft.containerinstance/containergroups).

Fyll först i `volumes` matrisen i avsnittet `properties` containergrupp i mallen. 

För varje container där du vill montera volymen fyller du sedan i `volumeMounts` matrisen i avsnittet `properties` i containerdefinitionen.

Följande mall Resource Manager en containergrupp med en container som skapats med `aci-hellofiles` avbildningen. Containern monterar Azure-filresursen *acishare som* skapades tidigare som en volym. Där det anges anger du namnet och lagringsnyckeln för det lagringskonto som är värd för filresursen. 

Precis som i föregående exempel måste `dnsNameLabel` värdet vara unikt i den Azure-region där du skapar containerinstansen. Uppdatera värdet i mallen om det behövs.

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

Om du vill distribuera Resource Manager mallen sparar du föregående JSON i en fil med namnet och kör sedan `deploy-aci.json` kommandot az deployment group [create][az-deployment-group-create] med `--template-file` parametern :

```azurecli
# Deploy with Resource Manager template
az deployment group create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Montera flera volymer

Om du vill montera flera volymer i en containerinstans måste du distribuera med en [Azure Resource Manager,](/azure/templates/microsoft.containerinstance/containergroups)en YAML-fil eller en annan programmatisk metod. Om du vill använda en mall eller YAML-fil anger du resursinformationen och definierar volymerna genom att fylla `volumes` i matrisen `properties` i filavsnittet. 

Om du till exempel har skapat två Azure Files resurser med namnet *share1* och *share2* i lagringskontot *myStorageAccount* skulle matrisen i en Resource Manager-mall se ut ungefär så `volumes` här:

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

För varje container i containergruppen där du vill montera volymerna fyller du sedan i matrisen i avsnittet `volumeMounts` `properties` i containerdefinitionen. Detta monterar till exempel de två volymerna *myvolume1* och *myvolume2* som tidigare definierats:

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

Lär dig hur du monterar andra volymtyper i Azure Container Instances:

* [Montera en emptyDir-volym i Azure Container Instances](container-instances-volume-emptydir.md)
* [Montera en gitRepo-volym i Azure Container Instances](container-instances-volume-gitrepo.md)
* [Montera en hemlig volym i Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
