---
title: Distribuera ett Service Fabric hanterat kluster (för hands version) med hjälp av ARM-mall
description: Distribuera ett program till ett Azure Service Fabric-hanterat kluster (för hands version) med hjälp av en Azure Resource Manager-mall.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: e860c77d77e3aabb70f70defdaa25de14e77e0e1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728019"
---
# <a name="deploy-a-service-fabric-managed-cluster-preview-application-using-arm-template"></a>Distribuera ett Service Fabric hanterat kluster (för hands version) med hjälp av ARM-mall

Du har flera alternativ för att distribuera Azure Service Fabric-program i Service Fabric hanterat kluster. Vi rekommenderar att du använder Azure Resource Manager. Om du använder Resource Manager kan du beskriva program och tjänster i JSON och sedan distribuera dem i samma Resource Manager-mall som klustret. Till skillnad från att använda PowerShell eller Azure CLI för att distribuera och hantera program, behöver du inte vänta tills klustret är klart om du använder Resource Manager. program registrering, etablering och distribution kan alla ske i ett enda steg. Att använda Resource Manager är det bästa sättet att hantera programmets livs cykel i klustret. Mer information finns i [metod tips: infrastruktur som kod](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources).

Att hantera dina program som resurser i Resource Manager kan hjälpa dig att få förbättringar inom följande områden:

* Gransknings logg: Resource Manager granskar varje åtgärd och bevarar en detaljerad aktivitets logg. En aktivitets logg kan hjälpa dig att spåra ändringar som gjorts i programmen och i klustret.
* Rollbaserad åtkomst kontroll: du kan hantera åtkomst till kluster och program som distribueras i klustret med hjälp av samma Resource Manager-mall.
* Hanterings effektivitet: med hjälp av Resource Manager får du en enda plats (Azure Portal) för hantering av klustret och viktiga program distributioner.

I det här dokumentet får du lära dig att:

> [!div class="checklist"]
>
> * Distribuera program resurser med hjälp av Resource Manager.
> * Uppgradera program resurser med hjälp av Resource Manager.
> * Ta bort program resurser.

## <a name="deploy-application-resources"></a>Distribuera program resurser

De övergripande steg som du utför för att distribuera ett program och dess tjänster med Resource Manager-programmets resurs modell är:
1. Paketera program koden.
1. Ladda upp paketet.
1. Referera till paketets plats i en Resource Manager-mall som en program resurs. 

Om du vill ha mer information kan du Visa [Paketera ett program](service-fabric-package-apps.md#create-an-sfpkg).

Sedan skapar du en Resource Manager-mall, uppdaterar parameter filen med programinformation och distribuerar mallen på Service Fabric klustret. [Utforska exempel](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/voting-sample-no-reverse-proxy/ARM-Managed-Cluster).

### <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Om du vill distribuera ett program från en Resource Manager-mall måste du ha ett lagrings konto. Lagrings kontot används för att mellanlagra program avbildningen. 

Du kan återanvända ett befintligt lagrings konto eller så kan du skapa ett nytt lagrings konto för att mellanlagra dina program. Om du använder ett befintligt lagrings konto kan du hoppa över det här steget. 

![Skapa ett lagringskonto][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>Konfigurera ditt lagrings konto

När lagrings kontot har skapats skapar du en BLOB-behållare där programmen kan mellanlagras. I Azure Portal går du till det Azure Storage konto där du vill lagra programmen. Välj **blobbar**  >  **Lägg till behållare**. 

Resurser i klustret kan skyddas genom att ange den offentliga åtkomst nivån till **privat**. Du kan bevilja åtkomst på flera sätt:

* Ge åtkomst till blobbar och köer med hjälp av [Azure Active Directory](../storage/common/storage-auth-aad-app.md).
* Bevilja åtkomst till Azure blob-och Queue-data med hjälp av [Azure RBAC i Azure Portal](../storage/common/storage-auth-aad-rbac-portal.md).
* Delegera åtkomst med hjälp av en [signatur för delad åtkomst](/rest/api/storageservices/delegate-access-with-shared-access-signature).

I exemplet på följande skärm bild används anonym Läs åtkomst för blobbar.

![Skapa BLOB][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>Mellanlagra programmet i ditt lagrings konto

Innan du kan distribuera ett program måste du mellanlagra programmet i Blob Storage. I den här självstudien skapar vi programpaketet manuellt. Tänk på att det här steget kan automatiseras. Mer information finns i [Paketera ett program](service-fabric-package-apps.md#create-an-sfpkg). 

I den här självstudien använder vi [röstnings exempel programmet](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart).

1. I Visual Studio högerklickar du på **röstnings** projektet och väljer sedan **paket**.

   ![Paket program][PackageApplication]  
1. Gå till *.\Service-Fabric-dotNet-quickstart\Voting\pkg\Debug* -katalogen. Zippa innehållet till en fil med namnet *Voting.zip*. Den *ApplicationManifest.xml* filen ska finnas i roten i zip-filen.

   ![Zip-program][ZipApplication]  
1. Byt namn på filen för att ändra tillägget från. zip till *. sfpkg*.

1. I Azure Portal, i behållaren **appar** för ditt lagrings konto, väljer du **Ladda upp** och laddar upp **röstning. sfpkg**. 

   ![Ladda upp appaket][UploadAppPkg]

Nu kommer programmet nu att mellanlagras och du kan skapa Resource Manager-mallen för att distribuera programmet.

### <a name="create-the-resource-manager-template"></a>Skapa Resource Manager-mallen

Exempel programmet innehåller [Azure Resource Manager mallar](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) som du kan använda för att distribuera programmet. Mallens fil namn är *UserApp.js* och *UserApp.Parameters.jspå*.

> [!NOTE]
> *UserApp.Parameters.js* filen måste uppdateras med namnet på klustret.
>
>

| Parameter              | Beskrivning                                 | Exempel                                                      | Kommentarer                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | Namnet på det kluster som du distribuerar till | SF-cluster123                                                |                                                              |
| program            | Namnet på programmet                 | Röstning                                                       |
| version    | Resurs-ID, program typ och version av appen.       | /providers/Microsoft.ServiceFabric/managedClusters/sf-cluster-123/applicationTypes/VotingType/versions/1.0.0                                               | Måste matcha ApplicationManifest.xml                 |
| serviceName            | Namnet på tjänsten         | VotingWeb                                             | Måste vara i formatet-ServiceType            |
| serviceTypeName        | Typ namnet för tjänsten                | VotingWebType                                                    | Måste matcha ServiceManifest.xml                 |
| appPackageUrl          | Blob Storage-URL: en för programmet     | https: \/ /servicefabricapps.blob.Core.Windows.net/Apps/Voting.sfpkg | URL: en för programpaketet i Blob Storage (proceduren för att ange URL beskrivs senare i artikeln) |

```json
{
    "apiVersion": "2021-01-01-preview",
    "type": "Microsoft.ServiceFabric/managedclusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2021-01-01-preview",
    "type": "Microsoft.ServiceFabric/managedclusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2021-01-01-preview",
    "type": "Microsoft.ServiceFabric/managedclusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2021-01-01-preview",
    "type": "Microsoft.ServiceFabric/managedclusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-application"></a>Distribuera programmet

Kör cmdleten **New-AzResourceGroupDeployment** för att distribuera programmet till resurs gruppen som innehåller klustret:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>Uppgradera Service Fabric program med hjälp av Resource Manager

> [!IMPORTANT]
> Alla tjänster som distribueras via ARM JSON-definitionen måste tas bort från avsnittet DefaultServices i motsvarande ApplicationManifest.xml-fil.


Du kan uppgradera ett program som redan har distribuerats till ett Service Fabric-kluster av någon av följande anledningar:

* En ny tjänst läggs till i programmet. Du måste lägga till en tjänst definition i *service-manifest.xml* och *application-manifest.xml* filer när en tjänst läggs till i programmet. Om du vill visa en ny version av ett program måste du även ändra program typ versionen från 1.0.0 till 1.0.1 i [UserApp.Parameters.jspå](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json):

    ```json
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```

* En ny version av en befintlig tjänst läggs till i programmet. Exempel på detta är program kod ändringar och uppdateringar till typ version och namn för appen. För den här uppgraderingen uppdaterar du UserApp.Parameters.jsså här:

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```
## <a name="delete-application-resources"></a>Ta bort program resurser

Ta bort ett program som har distribuerats med hjälp av program resurs modellen i Resource Manager:

1. Använd cmdleten [Get-AzResource](/powershell/module/az.resources/get-azresource) för att hämta resurs-ID: t för programmet:

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. Använd cmdleten [Remove-AzResource](/powershell/module/az.resources/remove-azresource) för att ta bort program resurserna:

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Nästa steg

Hämta information om program resurs modellen:

* [Modellera ett program i Service Fabric](service-fabric-application-model.md)
* [Service Fabric program-och tjänst manifest](service-fabric-application-and-service-manifests.md)
* [Metod tips: infrastruktur som kod](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)
* [Hantera program och tjänster som Azure-resurser](service-fabric-best-practices-infrastructure-as-code.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png