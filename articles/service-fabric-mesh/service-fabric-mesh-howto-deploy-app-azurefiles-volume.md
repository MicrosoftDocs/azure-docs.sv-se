---
title: Använda en Azure Files-baserad volym i en Service Fabric nätappen
description: Lär dig hur du lagrar tillstånd i ett Azure Service Fabric nät-program genom att montera en Azure Files-baserad volym i en tjänst med hjälp av Azure CLI.
author: georgewallace
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: gwallace
ms.custom: mvc, devcenter , devx-track-azurecli
ms.openlocfilehash: 40d10568e13ad455bc5178821da80e89f4132e93
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625845"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>Montera en Azure Files baserad volym i ett Service Fabric nätprogram 

> [!IMPORTANT]
> Förhands granskningen av Azure Service Fabric-nätet har dragits tillbaka. Nya distributioner kommer inte längre att tillåtas via Service Fabric nät-API. Stöd för befintliga distributioner fortsätter till 28 april 2021.
> 
> Mer information finns i [förhands granskning av Azure Service Fabric nät](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Den här artikeln beskriver hur du monterar en Azure Files-baserad volym i en tjänst av ett Service Fabric nätprogram.  Azure Files volym driv rutinen är en Docker-volym driv rutin som används för att montera en Azure Files resurs till en behållare som du använder för att spara tjänstens tillstånd. -Volymer ger dig generell fil lagring och gör att du kan läsa och skriva filer med hjälp av normal disk-I/O-fil-API: er.  Läs mer om volymer och alternativ för att lagra program data i [lagra tillstånd](service-fabric-mesh-storing-state.md).

Om du vill montera en volym i en tjänst skapar du en volym resurs i Service Fabric nätprogram och refererar sedan till den volymen i din tjänst.  Att deklarera volym resursen och referera till den i tjänst resursen kan göras antingen i [yaml-baserade resursfiler](#declare-a-volume-resource-and-update-the-service-resource-yaml) eller i den [JSON-baserade distributions mal len](#declare-a-volume-resource-and-update-the-service-resource-json). Innan du monterar volymen måste du först skapa ett Azure Storage-konto och en [fil resurs i Azure Files](../storage/files/storage-how-to-create-file-share.md).

## <a name="prerequisites"></a>Förutsättningar
> [!NOTE]
> **Känt problem med distribution på Windows RS5 Development Machine:** Det finns öppen bugg med PowerShell-cmdlet New-SmbGlobalMapping på RS5 Windows-datorer som förhindrar montering av Azurefile-volymer. Nedan visas ett exempel fel som påträffas när AzureFile-baserade volymer monteras på den lokala utvecklings datorn.
```
Error event: SourceId='System.Hosting', Property='CodePackageActivation:counterService:EntryPoint:131884291000691067'.
There was an error during CodePackage activation.System.Fabric.FabricException (-2147017731)
Failed to start Container. ContainerName=sf-2-63fc668f-362d-4220-873d-85abaaacc83e_6d6879cf-dd43-4092-887d-17d23ed9cc78, ApplicationId=SingleInstance_0_App2, ApplicationName=fabric:/counterApp. DockerRequest returned StatusCode=InternalServerError with ResponseBody={"message":"error while mounting volume '': mount failed"}
```
Lösningen för problemet är till 1) kör följande kommando som PowerShell-administratör och 2) starta om datorn.
```powershell
PS C:\WINDOWS\system32> Mofcomp c:\windows\system32\wbem\smbwmiv2.mof
```

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att slutföra den här artikeln. 

Om du vill använda Azure CLI lokalt med den här artikeln ser du till att `az --version` returnerar minst `azure-cli (2.0.43)` .  Installera (eller uppdatera) Azure Service Fabric mask CLI-modulen för tillägg genom att följa dessa [anvisningar](service-fabric-mesh-howto-setup-cli.md).

Logga in på Azure och ange din prenumeration:

```azurecli
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-storage-account-and-file-share-optional"></a>Skapa ett lagrings konto och en fil resurs (valfritt)
Att montera en Azure Files volym kräver ett lagrings konto och en fil resurs.  Du kan använda ett befintligt Azure Storage-konto och en fil resurs, eller skapa resurser:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az storage account create --name myStorageAccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

$current_env_conn_string=$(az storage account show-connection-string -n myStorageAccount -g myResourceGroup --query 'connectionString' -o tsv)

az storage share create --name myshare --quota 2048 --connection-string $current_env_conn_string
```

## <a name="get-the-storage-account-name-and-key-and-the-file-share-name"></a>Hämta lagrings kontots namn och nyckel och fil resurs namnet
Lagrings kontots namn, lagrings konto nyckel och fil resurs namnet refereras till som `<storageAccountName>` , `<storageAccountKey>` och `<fileShareName>` i följande avsnitt. 

Lista dina lagrings konton och hämta namnet på lagrings kontot med den fil resurs som du vill använda:
```azurecli-interactive
az storage account list
```

Hämta namnet på fil resursen:
```azurecli-interactive
az storage share list --account-name <storageAccountName>
```

Hämta lagrings konto nyckeln ("KEY1"):
```azurecli-interactive
az storage account keys list --account-name <storageAccountName> --query "[?keyName=='key1'].value"
```

Du kan också hitta de här värdena i [Azure Portal](https://portal.azure.com):
* `<storageAccountName>` – Under **lagrings konton**, namnet på det lagrings konto som användes för att skapa fil resursen.
* `<storageAccountKey>` – Välj ditt lagrings konto under **lagrings konton** och välj sedan **åtkomst nycklar** och Använd värdet under **KEY1**.
* `<fileShareName>` – Välj ditt lagrings konto under  **lagrings konton** och välj sedan **filer**. Namnet som ska användas är namnet på den fil resurs som du har skapat.

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>Deklarera en volym resurs och uppdatera tjänst resursen (JSON)

Lägg till parametrar för `<fileShareName>` `<storageAccountName>` värdena, och som `<storageAccountKey>` du hittade i föregående steg. 

Skapa en volym resurs som en peer för program resursen. Ange ett namn och providern ("SFAzureFile" för att använda Azure Files-baserad volym). I `azureFileParameters` anger du parametrarna för `<fileShareName>` `<storageAccountName>` värdena, och som `<storageAccountKey>` du hittade i föregående steg.

Om du vill montera volymen i tjänsten lägger du till en `volumeRefs` till `codePackages` -elementet i tjänsten.  `name` är resurs-ID: t för volymen (eller en distributions mal len för volym resursen) och namnet på den volym som har deklarerats i resurs filen Volume. yaml.  `destinationPath` är den lokala katalog som volymen ska monteras på.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "defaultValue": "EastUS",
      "type": "String",
      "metadata": {
        "description": "Location of the resources."
      }
    },
    "fileShareName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure Files file share that provides the volume for the container."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure storage account that contains the file share."
      }
    },
    "storageAccountKey": {
      "type": "securestring",
      "metadata": {
        "description": "Access key for the Azure storage account that contains the file share."
      }
    },
    "stateFolderName": {
      "type": "string",
      "defaultValue": "TestVolumeData",
      "metadata": {
        "description": "Folder in which to store the state. Provide an empty value to create a unique folder for each container to store the state. A non-empty value will retain the state across deployments, however if more than one applications are using the same folder, the counter may update more frequently."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "VolumeTest",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/VolumeTestNetwork",
        "Microsoft.ServiceFabricMesh/volumes/testVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "VolumeTestService",
            "properties": {
              "description": "VolumeTestService description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "VolumeTestService",
                  "image": "volumetestservice:dev",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]",
                      "destinationPath": "C:\\app\\data"
                    }
                  ],
                  "environmentVariables": [
                    {
                      "name": "ASPNETCORE_URLS",
                      "value": "http://+:20003"
                    },
                    {
                      "name": "STATE_FOLDER_NAME",
                      "value": "[parameters('stateFolderName')]"
                    }
                  ],
                  ...
                }
              ],
              ...
            }
          }
        ],
        "description": "VolumeTest description."
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "testVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for the test application.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
    ...
  ]
}
```

## <a name="declare-a-volume-resource-and-update-the-service-resource-yaml"></a>Deklarera en volym resurs och uppdatera tjänst resursen (YAML)

Lägg till en ny *volym. yaml* -fil i katalogen för *app-resurser* för ditt program.  Ange ett namn och providern ("SFAzureFile" för att använda Azure Files-baserad volym). `<fileShareName>`, `<storageAccountName>` och `<storageAccountKey>` är de värden som du hittat i föregående steg.

```yaml
volume:
  schemaVersion: 1.0.0-preview2
  name: testVolume
  properties:
    description: Azure Files storage volume for counter App.
    provider: SFAzureFile
    azureFileParameters: 
        shareName: <fileShareName>
        accountName: <storageAccountName>
        accountKey: <storageAccountKey>
```

Uppdatera filen *service. yaml* i katalogen *tjänst resurser* för att montera volymen i din tjänst.  Lägg till `volumeRefs` elementet i `codePackages` elementet.  `name` är resurs-ID: t för volymen (eller en distributions mal len för volym resursen) och namnet på den volym som har deklarerats i resurs filen Volume. yaml.  `destinationPath` är den lokala katalog som volymen ska monteras på.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: VolumeTest
  properties:
    services:
      - name: VolumeTestService
        properties:
          description: VolumeTestService description.
          osType: Windows
          codePackages:
            - name: VolumeTestService
              image: volumetestservice:dev
              volumeRefs:
                - name: "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]"
                  destinationPath: C:\app\data
              endpoints:
                - name: VolumeTestServiceListener
                  port: 20003
              environmentVariables:
                - name: ASPNETCORE_URLS
                  value: http://+:20003
                - name: STATE_FOLDER_NAME
                  value: TestVolumeData
              resources:
                requests:
                  cpu: 0.5
                  memoryInGB: 1
          replicaCount: 1
          networkRefs:
            - name: VolumeTestNetwork
```

## <a name="next-steps"></a>Nästa steg

- Visa Azure Files volym exempel program på [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Mer information om Service Fabric-resursmodellen finns i avsnittet om [Service Fabric Mesh-resursmodellen](service-fabric-mesh-service-fabric-resources.md).
- Mer information om Service Fabric Mesh finns i [översikten över Service Fabric Mesh](service-fabric-mesh-overview.md).
