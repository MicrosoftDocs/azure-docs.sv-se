---
title: Azure HDInsight SDK för go
description: Referens material för att använda Azure HDInsight SDK för go och Apache Hadoop kluster
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18, devx-track-azurecli
ms.date: 01/03/2020
ms.openlocfilehash: 09564bd149488d956586c8a0e349cd79eec358a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100582761"
---
# <a name="hdinsight-sdk-for-go-preview"></a>HDInsight SDK för Go (för hands version)

## <a name="overview"></a>Översikt
HDInsight SDK för Go tillhandahåller klasser och funktioner som gör att du kan hantera dina HDInsight-kluster. Den innehåller åtgärder för att skapa, ta bort, uppdatera, lista, ändra storlek, köra skript åtgärder, övervaka, Hämta egenskaper för HDInsight-kluster med mera.

> [!NOTE]  
>GoDoc Reference material för denna SDK finns också [här](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Ett [ `go get` verktyg](https://github.com/golang/go/wiki/GoGetTools).
* [Gå](https://golang.org/dl/).

## <a name="sdk-installation"></a>SDK-installation

Från din GOPATH-plats kör du `go get github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight`

## <a name="authentication"></a>Autentisering

SDK måste först autentiseras med din Azure-prenumeration.  Följ exemplet nedan för att skapa ett huvud namn för tjänsten och använda det för att autentisera. När detta är gjort har du en instans av en `ClustersClient` , som innehåller många funktioner (beskrivs i avsnitten nedan) som kan användas för att utföra hanterings åtgärder.

> [!NOTE]  
> Det finns andra sätt att autentisera förutom nedanstående exempel som kan vara bättre lämpade för dina behov. Alla funktioner beskrivs här: [autentiserings funktioner i Azure SDK för go](/azure/go/azure-sdk-go-authorization)

### <a name="authentication-example-using-a-service-principal"></a>Autentiserings exempel med ett huvud namn för tjänsten

Först loggar du in på [Azure Cloud Shell](https://shell.azure.com/bash). Kontrol lera att du för närvarande använder den prenumeration där du vill att tjänstens huvud namn ska skapas.

```azurecli-interactive
az account show
```

Din prenumerations information visas som JSON.

```json
{
  "environmentName": "AzureCloud",
  "id": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "isDefault": true,
  "name": "XXXXXXX",
  "state": "Enabled",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "user": {
    "cloudShellID": true,
    "name": "XXX@XXX.XXX",
    "type": "user"
  }
}
```

Om du inte är inloggad på rätt prenumeration väljer du rätt en genom att köra: 
```azurecli-interactive
az account set -s <name or ID of subscription>
```

> [!IMPORTANT]  
> Om du inte redan har registrerat HDInsight-resurs leverantören av en annan funktion (till exempel genom att skapa ett HDInsight-kluster via Azure Portal), måste du göra detta en gång innan du kan autentisera dig. Detta kan göras från [Azure Cloud Shell](https://shell.azure.com/bash) genom att köra följande kommando:
>```azurecli-interactive
>az provider register --namespace Microsoft.HDInsight
>```

Välj sedan ett namn för tjänstens huvud namn och skapa det med följande kommando:

```azurecli-interactive
az ad sp create-for-rbac --name <Service Principal Name> --sdk-auth
```

Tjänstens huvud information visas som JSON.

```json
{
  "clientId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "clientSecret": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "subscriptionId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Kopiera nedanstående kodfragment och fyll i `TENANT_ID` ,, `CLIENT_ID` `CLIENT_SECRET` och `SUBSCRIPTION_ID` med strängarna från JSON-filen som returnerades efter att du kört kommandot för att skapa tjänstens huvud namn.

```golang
package main

import (
    "context"
    "github.com/Azure/go-autorest/autorest/azure/auth"
    hdi "github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight"
    "github.com/Azure/go-autorest/autorest/to"
)

func main() {
    var err error

    // Tenant ID for your Azure Subscription
    var TENANT_ID = ""
    // Your Service Principal App Client ID
    var CLIENT_ID = ""
    // Your Service Principal Client Secret
    var CLIENT_SECRET = ""
    // Azure Subscription ID
    var SUBSCRIPTION_ID = ""

    var credentials = auth.NewClientCredentialsConfig(CLIENT_ID, CLIENT_SECRET, TENANT_ID)
    var client = hdi.NewClustersClient(SUBSCRIPTION_ID)

    client.Authorizer, err = credentials.Authorizer()
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
```

## <a name="cluster-management"></a>Klusterhantering

> [!NOTE]  
> Det här avsnittet förutsätter att du redan har autentiserat och konstruerat en `ClusterClient` instans och lagra det i en variabel som kallas `client` . Instruktioner för att autentisera och hämta en `ClusterClient` finns i avsnittet autentisering ovan.

### <a name="create-a-cluster"></a>Skapa ett kluster

Du kan skapa ett nytt kluster genom att anropa `client.Create()` . 

#### <a name="example"></a>Exempel

Det här exemplet visar hur du skapar ett [Apache Spark](https://spark.apache.org/) kluster med två huvudnoder och en arbetsnod.

> [!NOTE]  
> Du måste först skapa en resurs grupp och ett lagrings konto enligt beskrivningen nedan. Om du redan har skapat dessa kan du hoppa över de här stegen.

##### <a name="creating-a-resource-group"></a>Skapa en resursgrupp

Du kan skapa en resurs grupp med hjälp av [Azure Cloud Shell](https://shell.azure.com/bash) genom att köra

```azurecli-interactive
az group create -l <Region Name (i.e. eastus)> --n <Resource Group Name>
```

##### <a name="creating-a-storage-account"></a>Skapa ett lagringskonto

Du kan skapa ett lagrings konto med hjälp av [Azure Cloud Shell](https://shell.azure.com/bash) genom att köra:

```azurecli-interactive
az storage account create -n <Storage Account Name> -g <Existing Resource Group Name> -l <Region Name (i.e. eastus)> --sku <SKU i.e. Standard_LRS>
```

Kör nu följande kommando för att hämta nyckeln till ditt lagrings konto (du behöver det för att skapa ett kluster):

```azurecli-interactive
az storage account keys list -n <Storage Account Name>
```

---
I avsnittet go-kodfragmentet nedan skapas ett Spark-kluster med två huvudnoder och en arbetsnod. Fyll i de tomma variablerna som förklaras i kommentarerna och ändra andra parametrar så att de passar just dina behov.

```golang
// The name for the cluster you are creating
var clusterName = "";
// The name of your existing Resource Group
var resourceGroupName = "";
// Choose a username
var username = "";
// Choose a password
var password = "";
// Replace <> with the name of your storage account
var storageAccount = "<>.blob.core.windows.net";
// Storage account key you obtained above
var storageAccountKey = "";
// Choose a region
var location = "";
var container = "default";

var parameters = hdi.ClusterCreateParametersExtended {
    Location: to.StringPtr(location),
    Tags: make(map[string]*string),
    Properties: &hdi.ClusterCreateProperties {
        ClusterVersion: to.StringPtr("3.6"),
        OsType: hdi.Linux,
        ClusterDefinition: &hdi.ClusterDefinition {
            Kind: to.StringPtr("spark"),
            Configurations: map[string]map[string]interface{}{
                "gateway": {
                    "restAuthCredential.isEnabled": "True",
                    "restAuthCredential.username":  username,
                    "restAuthCredential.password":  password,
                },
            },
        },
        Tier: hdi.Standard,
        ComputeProfile: &hdi.ComputeProfile {
            Roles: &[]hdi.Role {
                hdi.Role {
                    Name: to.StringPtr("headnode"),
                    TargetInstanceCount: to.Int32Ptr(2),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
                hdi.Role {
                    Name: to.StringPtr("workernode"),
                    TargetInstanceCount: to.Int32Ptr(1),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
            },
        },
        StorageProfile: &hdi.StorageProfile {
            Storageaccounts: &[]hdi.StorageAccount {
                hdi.StorageAccount {
                    Name: to.StringPtr(storageAccount),
                    Key: to.StringPtr(storageAccountKey),
                    Container: to.StringPtr(container),
                    IsDefault: to.BoolPtr(true),
                },
            },
        },
    },
}
client.Create(context.Background(), resourceGroupName, clusterName, parameters)
```

### <a name="get-cluster-details"></a>Hämta kluster information

Så här hämtar du egenskaper för ett angivet kluster:

```golang
client.Get(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Exempel

Du kan använda `get` för att bekräfta att du har skapat klustret.

```golang
cluster, err := client.Get(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
fmt.Println(*cluster.Name)
fmt.Println(*cluster.ID
```

Utdata bör se ut så här:

```
<Cluster Name>
/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<Resource Group Name>/providers/Microsoft.HDInsight/clusters/<Cluster Name>
```

### <a name="list-clusters"></a>Lista kluster

#### <a name="list-clusters-under-the-subscription"></a>Lista kluster under prenumerationen

```golang
client.List()
```

#### <a name="list-clusters-by-resource-group"></a>Lista kluster efter resurs grupp

```golang
client.ListByResourceGroup("<Resource Group Name>")
```

> [!NOTE]  
> Både `List()` och `ListByResourceGroup()` returnerar en `ClusterListResultPage` struktur. För att hämta nästa sida kan du anropa `Next()` . Detta kan upprepas tills `ClusterListResultPage.NotDone()` returneras `false` , som du ser i exemplet nedan.

#### <a name="example"></a>Exempel

I följande exempel skrivs egenskaperna för alla kluster i den aktuella prenumerationen ut:

```golang
page, err := client.List(context.Background())
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, cluster := range page.Values() {
        fmt.Println(*cluster.Name)
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="delete-a-cluster"></a>Ta bort ett kluster

Så här tar du bort ett kluster:

```golang
client.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

### <a name="update-cluster-tags"></a>Uppdatera kluster Taggar

Du kan uppdatera taggarna för ett angivet kluster så här:

```golang
client.Update(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterPatchParameters{<map[string]*string} of Tags>)
```

#### <a name="example"></a>Exempel

```golang
client.Update(context.Background(), "SDKTestRG", "SDKTest", hdi.ClusterPatchParameters{map[string]*string{"tag1Name" : to.StringPtr("tag1Value"), "tag2Name" : to.StringPtr("tag2Value")}})
```

### <a name="resize-cluster"></a>Ändra storlek på kluster

Du kan ändra storlek på ett angivet kluster antal arbetsnoder genom att ange en ny storlek så här:

```golang
client.Resize(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterResizeParameters{<Num of Worker Nodes (int)>})
```

## <a name="cluster-monitoring"></a>Klusterövervakning

Hanterings-SDK: t för HDInsight kan också användas för att hantera övervakningen i klustren via Operations Management Suite (OMS).

På samma sätt som du har skapat för `ClusterClient` hanterings åtgärder måste du skapa en `ExtensionClient` som ska användas för övervaknings åtgärder. När du har slutfört autentiseringen ovan kan du skapa en så här `ExtensionClient` :

```golang
extClient := hdi.NewExtensionsClient(SUBSCRIPTION_ID)
extClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> I exemplen nedan ovan förutsätts det att du redan har initierat ett `ExtensionClient` anropat `extClient` och angett det `Authorizer` som visas ovan.

### <a name="enable-oms-monitoring"></a>Aktivera OMS-övervakning

> [!NOTE]  
> Om du vill aktivera OMS-övervakning måste du ha en befintlig Log Analytics-arbetsyta. Om du inte redan har skapat en, kan du lära dig hur du gör det här: [skapa en Log Analytics arbets yta i Azure Portal](../azure-monitor/logs/quick-create-workspace.md).

Aktivera OMS-övervakning i klustret:

```golang
extClient.EnableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name", hdi.ClusterMonitoringRequest {WorkspaceID: to.StringPtr("<Workspace Id>")})
```

### <a name="view-status-of-oms-monitoring"></a>Visa status för OMS-övervakning

Så här hämtar du status för OMS i klustret:

```golang
extClient.GetMonitoringStatus(context.Background(), "<Resource Group Name", "Cluster Name")
```

### <a name="disable-oms-monitoring"></a>Inaktivera OMS-övervakning

Så här inaktiverar du OMS i klustret:

```golang
extClient.DisableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name")
```

## <a name="script-actions"></a>Skript åtgärder

HDInsight innehåller en konfigurations funktion som kallas skript åtgärder som anropar anpassade skript för att anpassa klustret.

> [!NOTE]  
> Mer information om hur du använder skript åtgärder hittar du här: [Anpassa Linux-baserade HDInsight-kluster med skript åtgärder](./hdinsight-hadoop-customize-cluster-linux.md)

### <a name="execute-script-actions"></a>Köra skript åtgärder

Du kan köra skript åtgärder på ett angivet kluster så här:

```golang
var scriptAction1 = hdi.RuntimeScriptAction{Name: to.StringPtr("<Script Name>"), URI: to.StringPtr("<URL To Script>"), Roles: <&[]string of roles>} //valid roles are "headnode", "workernode", "zookeepernode", and "edgenode"
client.ExecuteScriptActions(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ExecuteScriptActionParameters{PersistOnSuccess: to.BoolPtr(true), ScriptActions: &[]hdi.RuntimeScriptAction{scriptAction1}}) //add more RuntimeScriptActions to the list to execute multiple scripts
```

För åtgärderna "ta bort skript åtgärd" och "lista beständiga skript åtgärder", måste du skapa en `ScriptActionsClient` , på samma sätt som du har skapat `ClusterClient` för hanterings åtgärder. När du har slutfört autentiseringen ovan kan du skapa en så här `ScriptActionsClient` :

```golang
scriptActionsClient := hdi.NewScriptActionsClient(SUBSCRIPTION_ID)
scriptActionsClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> I exemplen nedan skript åtgärder förutsätter vi att du redan har initierat ett `ScriptActionsClient` anropat `scriptActionsClient` och angett det `Authorizer` som visas ovan.

### <a name="delete-script-action"></a>Ta bort skript åtgärd

Ta bort en angiven Sparad skript åtgärd på ett visst kluster:

```golang
scriptActionsClient.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>", "<Script Name>")
```

### <a name="list-persisted-script-actions"></a>Lista bestående skript åtgärder

> [!NOTE]  
> Båda `ListByCluster()` returnerar en `ScriptActionsListPage` struct. För att hämta nästa sida kan du anropa `Next()` . Detta kan upprepas tills `ClusterListResultPage.NotDone()` returneras `false` , som du ser i exemplet nedan.

För att visa en lista över alla beständiga skript åtgärder för det angivna klustret:
```golang
scriptActionsClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Exempel

```golang
page, err := scriptActionsClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="list-all-scripts-execution-history"></a>Visa lista över alla skript körnings historik

För den här åtgärden måste du skapa en på `ScriptExecutionHistoryClient` samma sätt som du har skapat `ClusterClient` för hanterings åtgärder. När du har slutfört autentiseringen ovan kan du skapa en så här `ScriptActionsClient` :

```golang
scriptExecutionHistoryClient := hdi.NewScriptExecutionHistoryClient(SUBSCRIPTION_ID)
scriptExecutionHistoryClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Nedan förutsätter vi att du redan har initierat ett `ScriptExecutionHistoryClient` anropat `scriptExecutionHistoryClient` och angett det `Authorizer` som visas ovan.

Visa alla skript körnings historik för det angivna klustret:

```golang
scriptExecutionHistoryClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Exempel

I det här exemplet skrivs all information om alla tidigare skript körningar ut.

```golang
page, err := scriptExecutionHistoryClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

## <a name="next-steps"></a>Nästa steg

Utforska [referens materialet för GoDoc](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight). GoDocs innehåller referens dokumentation för alla funktioner i SDK: n.
