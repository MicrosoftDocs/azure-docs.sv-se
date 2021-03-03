---
title: Integrera API Management med Service Fabric i Azure
description: Lär dig hur du snabbt kommer igång med Azure API Management och dirigera trafik till en backend-tjänst i Service Fabric.
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 681a1c5241743a0164d83d73753efa0b6c446109
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735598"
---
# <a name="integrate-api-management-with-service-fabric-in-azure"></a>Integrera API Management med Service Fabric i Azure

Att distribuera Azure API Management med Service Fabric är ett avancerat scenario.  API Management är användbart när du vill publicera API: er med en omfattande uppsättning dirigeringsregler för dina Service Fabric-serverdelstjänster. Molnprogram behöver ofta en klientdelsgateway som enda åtkomstpunkt för ingång för användare, enheter och andra program. I Service Fabric kan en gateway vara valfri tillståndslös tjänst för ingångstrafik, till exempel ett ASP.NET Core-program, Event Hubs, IoT Hub eller Azure API Management.

Den här artikeln visar hur du konfigurerar [Azure API Management](../api-management/api-management-key-concepts.md) med Service Fabric för att dirigera trafik till en backend-tjänst i Service Fabric.  När du är klar kan har du distribuerat API Management till ett virtuellt nätverk och konfigurerat en API-åtgärd som dirigerar trafik till tillståndslösa servicedelstjänster. Mer information om Azure API Management-scenarier med Service Fabric finns i [översiktsartikeln](service-fabric-api-management-overview.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability"></a>Tillgänglighet

> [!IMPORTANT]
> Den här funktionen är tillgänglig på nivån **Premium** och **developer** of API Management på grund av det nödvändiga stödet för virtuellt nätverk.

## <a name="prerequisites"></a>Krav

Innan du börjar:

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installera [Azure PowerShell](/powershell/azure/install-az-ps) eller [Azure CLI](/cli/azure/install-azure-cli).
* Skapa ett säkert [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) i en nätverks säkerhets grupp.
* Om du distribuerar ett Windows-kluster måste du konfigurera en Windows-utvecklingsmiljö. Installera [Visual Studio 2019](https://www.visualstudio.com) och **Azure-utveckling**, **ASP.net och webb utveckling**, och **.net Core plattforms oberoende utvecklings** arbets belastningar.  Konfigurera sedan en [.NET-utvecklingsmiljö](service-fabric-get-started.md).

## <a name="network-topology"></a>Nätverkstopologi

Nu när du har ett säkert [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) på Azure distribuerar du API Management till det virtuella nätverket (VNet) i under nätet och NSG som har angetts för API Management. I den här artikeln har API Management Resource Manager-mallen förkonfigureras för att använda namnen på VNET, undernät och NSG som du har skapat i [guiden Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) den här artikeln distribuerar följande topologi till Azure där API Management och Service Fabric finns i undernät av samma Virtual Network:

 ![Bildrubrik][sf-apim-topology-overview]

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Logga in på Azure och välj din prenumeration

Logga in på ditt Azure-konto och välj din prenumeration innan du kör Azure-kommandon.

```powershell
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Distribuera en servicedelstjänst för Service Fabric

Innan du konfigurerar API Management att dirigera trafik till en servicedelstjänst för Service Fabric måste du ha en service som är igång och kan ta emot begäranden.  

Skapa en grundläggande tillstånds lös ASP.NET Core Reliable service med hjälp av Project-standardmallen för webb-API. Detta skapar en HTTP-slutpunkt för din tjänst, som du exponera via Azure API Management.

Starta Visual Studio som administratör och skapa en ASP.NET Core-tjänst:

 1. I Visual Studio väljer du Arkiv -> Nytt projekt.
 2. Välj Service Fabric-programmallen under Moln och ge den namnet **”ApiApplication”**.
 3. Välj den tillståndslösa ASP.NET Core-tjänstmallen och ge projektet namnet **”WebApiService”**.
 4. Välj projekt mal len webb-API ASP.NET Core 2,1.
 5. När projektet har skapats öppnar du `PackageRoot\ServiceManifest.xml` och ta bort attributet `Port` från slutpunktskonfigurationen för resursen:

    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    Genom att ta bort porten kan Service Fabric ange en port dynamiskt från program port intervallet, som öppnas via nätverks säkerhets gruppen i kluster resurs hanterarens mall, vilket tillåter trafik att flöda till den från API Management.

 6. Tryck på F5 i Visual Studio för att verifiera att ditt webb-API är tillgängligt lokalt.

    Öppna Service Fabric Explorer och öka detaljnivån ned till en specifik instans av ASP.NET Core-tjänsten för att se den basadressen som tjänsten lyssnar på. Lägg till `/api/values` i basadressen och öppna den i en webbläsare. Detta anropar Get-metoden i ValuesController i Web API-mallen. Detta returnerar standardsvaret som tillhandahålls av mallen, en JSON-matris som innehåller två strängar:

    ```json
    ["value1", "value2"]`
    ```

    Det här är den slutpunkt som du exponerar via API Management i Azure.

 7. Till sist distribuerar du programmet till ditt kluster i Azure. Högerklicka på programprojektet i Visual Studio och välj **Publicera**. Ange din klusterslutpunkt (till exempel `mycluster.southcentralus.cloudapp.azure.com:19000`) för att distribuera programmet till ditt Service Fabric-kluster i Azure.

En tillståndslös ASP.NET Core-tjänst med namnet `fabric:/ApiApplication/WebApiService` bör nu köras i ditt Service Fabric-kluster i Azure.

## <a name="download-and-understand-the-resource-manager-templates"></a>Ladda ned och förstå Resource Manager-mallarna

Ladda ned och spara följande mallar och parameterfil för Resource Manager:

* [network-apim.json][network-arm]
* [network-apim.parameters.json][network-parameters-arm]
* [apim.json][apim-arm]
* [apim.parameters.json][apim-parameters-arm]

Mallen *network-apim.json* distribuerar ett nytt undernät och nätverkssäkerhetsgruppen i det virtuella nätverk där Service Fabric-klustret distribueras.

I följande avsnitt beskrivs de resurser som definieras av mallen *apim.json*. Om du vill ha mer information följer du länkarna till mallreferensdokumentationen i de olika avsnitten. De konfigurerbara parametrar som definierats i parameterfilen *apim.parameters.json* anges längre fram i den här artikeln.

### <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

[Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service) beskriver API Management- tjänstinstansen: namn, SKU eller nivå, resursgruppens plats, utgivarinformation och virtuellt nätverk.

### <a name="microsoftapimanagementservicecertificates"></a>Microsoft.ApiManagement/service/certificates

[Microsoft.ApiManagement/service/certificates](/azure/templates/microsoft.apimanagement/service/certificates) konfigurerar API Management-säkerheten. API Management måste autentisera med Service Fabric-klustret för tjänstidentifiering med ett klientcertifikat som har åtkomst till ditt kluster. Den här artikeln använder samma certifikat som angavs tidigare när du skapade [Windows-klustret](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor), vilket som standard kan användas för att komma åt klustret.

I den här artikeln används samma certifikat för klientautentisering och klustrets nod-till-nod-säkerhet. Du kan använda ett separat klientcertifikat om du har ett konfigurerat för att åtkomst till ditt Service Fabric-kluster. Ange **namn**, **lösenord** och **data** (base-64-kodad sträng) för den privata nyckelfilen (.pfx) för det klustercertifikat som du angav när du skapade ditt Service Fabric-kluster.

### <a name="microsoftapimanagementservicebackends"></a>Microsoft.ApiManagement/service/backends

[Microsoft.ApiManagement/service/backends](/azure/templates/microsoft.apimanagement/service/backends) beskriver den serverdelstjänst som trafiken dirigeras till.

För Service Fabric-serverdelar utgör Server Fabric-klustret serverdelen i stället för en specifik Service Fabric-tjänst för Service Fabric serverdelar. På så sätt kan en enda princip dirigera till flera tjänster i klustret. Fältet **URL** här är ett fullständigt kvalificerat namn på en tjänst i klustret som alla begäranden dirigeras till som standard om inget tjänstnamn anges i en serverdelsprincip. Du kan använda falska tjänstnamn, till exempel ”fabric:/fake/service”, om du inte vill ha en reservtjänst. **resourceId** specificerar slutpunkten för klusterhantering.  **clientCertificateThumbprint** och **serverCertificateThumbprints** identifierar certifikaten som används för att autentisera med klustret.

### <a name="microsoftapimanagementserviceproducts"></a>Microsoft.ApiManagement/service/products

[Microsoft.ApiManagement/service/products](/azure/templates/microsoft.apimanagement/service/products) skapar en produkt. En produkt innehåller en eller flera API:er samt en användningskvot och användningsvillkor i Azure API Management. När en produkt har publicerats kan utvecklarna prenumerera på produkten och börja använda produktens API: er.

Ange ett beskrivande **DisplayName** och en **beskrivning** av produkten. I den här artikeln krävs en prenumeration men prenumerations godkännande av en administratör är inte det.  Den här produktens **status** är ”publicerad”, och den är synlig för prenumeranter.

### <a name="microsoftapimanagementserviceapis"></a>Microsoft.ApiManagement/service/apis

[Microsoft.ApiManagement/service/apis](/azure/templates/microsoft.apimanagement/service/apis) skapar ett API. Ett API i API Management representerar en uppsättning åtgärder som kan anropas av klientprogram. När åtgärderna har lagts till läggs API:et till i en produkt och kan publiceras. När ett API har publicerats går det att prenumerera på och kan användas av utvecklare.

* **DisplayName** kan vara valfritt namn för din API. I den här artikeln använder du "Service Fabric app".
* **name** är ett unikt och beskrivande namn för API:et, till exempel ”service-fabric-app”. Det visas i utvecklar- och utgivarportalerna.
* **serviceUrl** refererar till HTTP-tjänsten som implementerar API:et. API-hanteringen vidarebefordrar begäranden till den här adressen. Det här URL-värdet används inte för Service Fabric-serverdelar. Du kan ange ett valfritt värde här. I den här artikeln, till exempel "http: \/ /servicefabric".
* **path** läggs till baswebbadressen för API-hanteringstjänsten. Baswebbadressen är gemensam för alla API:er som har en API Management-tjänstinstans som värd. API Management skiljer API:erna åt med hjälp av deras suffix, och suffixet måste därför vara unikt för alla API:er för en viss utgivare.
* **protocol** anger vilka protokoll som kan användas för åtkomst till API:et. I den här artikeln listar du **http** och **https**.
* **sökväg** är ett suffix för API:et. I den här artikeln använder du "MyApp".

### <a name="microsoftapimanagementserviceapisoperations"></a>Microsoft.ApiManagement/service/apis/operations

[Microsoft.ApiManagement/service/apis/operations](/azure/templates/microsoft.apimanagement/service/apis/operations) Innan en API i API Management kan användas måste åtgärder läggas till API:et.  Externa klienter använder en åtgärd för att kommunicera med den tillståndslösa ASP.NET Core-tjänsten som körs i Service Fabric-klustret.

Fyll i följande värden för att lägga till en API-åtgärd i klientdelen:

* I **displayName** och **description** beskrivs åtgärden. I den här artikeln använder du "values".
* **method** anger HTTP-verbet.  I den här artikeln anger du **Hämta**.
* **urlTemplate** läggs till i baswebbadressen för API:et och identifierar en enskild HTTP-åtgärd.  I den här artikeln använder `/api/values` du om du har lagt till .NET-Server dels tjänsten eller `getMessage` om du har lagt till Java-backend-tjänsten.  Som standard gäller att den webbsökväg som anges här är den webbsökväg som skickas till Service Fabric- serverdelstjänsten. Om du använder samma webbsökväg här som tjänsten använder, till exempel ”/ api/values”, fungerar åtgärden utan ytterligare ändringar. Du kan också ange en annan webbsökväg här än den webbadress som används av Service Fabric-serverdelstjänsten. I så fall måste du också ange en sökvägsomskrivning i din åtgärdsprincip senare.

### <a name="microsoftapimanagementserviceapispolicies"></a>Microsoft.ApiManagement/service/apis/policies

[Microsoft.ApiManagement/service/apis/policies](/azure/templates/microsoft.apimanagement/service/apis/policies) skapar en serverdelsprincip som kopplar samman allt. Det är här du konfigurerar den Service Fabric-serverdelstjänst som begäranden dirigeras till. Du kan använda den här principen för alla API-åtgärder.  Mer information finns i [Principöversikt](../api-management/api-management-howto-policies.md).

[Serverdelskonfigurationen för Service Fabric](../api-management/api-management-transformation-policies.md#SetBackendService) innehåller följande kontroller för dirigering av begäranden:

* Tjänstinstansurval genom att ett Service Fabric-tjänstinstansnamn anges, antingen hårdkodat (till exempel `"fabric:/myapp/myservice"`) eller genererat från HTTP-begäran (till exempel `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
* Partitionsupplösning genom att en partitionsnyckel genereras med hjälp av ett partitioneringsschema i Service Fabric.
* Val av replik för tillståndskänsliga tjänster.
* Villkor för upplösningsförsök som gör att du kan ange villkoren för återupplösning av en tjänstplats och skicka en begäran på nytt.

**policyContent** är det Json-undantagna XML-innehållet i principen.  I den här artikeln skapar du en backend-princip för att dirigera begär Anden direkt till den tillstånds lösa .NET-eller Java-tjänsten som distribuerats tidigare. Lägg till en `set-backend-service`-princip under inkommande principer.  Byt ut värdet *sf-service-instance-name* mot `fabric:/ApiApplication/WebApiService` om du tidigare har distribuerat .NET-serverdelstjänsten eller `fabric:/EchoServerApplication/EchoServerService` om du har distribuerat Java-tjänsten.  *backend-id* refererar till en serverdelsresurs, i det här fallet den `Microsoft.ApiManagement/service/backends`-resurs som definierats i mallen *apim.json*. *backend-id* kan även referera till en annan serverdelsresurs som skapats med hjälp av API Management-API:erna. I den här artikeln anger du *Server dels-ID* till värdet för parametern *service_fabric_backend_name* .

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

En fullständig uppsättning attribut för Service Fabric-serverdelsprincipen finns i [serverdelsdokumentationen för API Management](../api-management/api-management-transformation-policies.md#SetBackendService)

## <a name="set-parameters-and-deploy-api-management"></a>Ange parametrar och distribuera API Management

Fyll i följande tomma parametrar i *apim.parameters.json* för din distribution.

|Parameter|Värde|
|---|---|
|apimInstanceName|sf-apim|
|apimPublisherEmail|myemail@contosos.com|
|apimSku|Utvecklare|
|serviceFabricCertificateName|sfclustertutorialgroup320171031144217|
|certificatePassword|q6D7nN %6ck@6|
|serviceFabricCertificateThumbprint|C4C1E541AD512B8065280292A8BA6079C3F26F10 |
|serviceFabricCertificate|&lt;base64-kodad sträng&gt;|
|url_path|/api/values|
|clusterHttpManagementEndpoint|`https://mysfcluster.southcentralus.cloudapp.azure.com:19080`|
|inbound_policy|&lt;XML-sträng&gt;|

*certificatePassword* och *serviceFabricCertificateThumbprint* måste matcha det klustercertifikat som användes när klustret konfigurerades.

*serviceFabricCertificate* är certifikatet som en base-64-kodad sträng, som kan genereras med hjälp av följande skript:

```powershell
$bytes = [System.IO.File]::ReadAllBytes("C:\mycertificates\sfclustertutorialgroup220171109113527.pfx");
$b64 = [System.Convert]::ToBase64String($bytes);
[System.Io.File]::WriteAllText("C:\mycertificates\sfclustertutorialgroup220171109113527.txt", $b64);
```

I *inbound_policy* byter du ut värdet för *sf-service-instance-name* mot `fabric:/ApiApplication/WebApiService` om du tidigare har distribuerat .NET-serverdelstjänsten eller `fabric:/EchoServerApplication/EchoServerService` om du har distribuerat Java-tjänsten. *backend-id* refererar till en serverdelsresurs, i det här fallet den `Microsoft.ApiManagement/service/backends`-resurs som definierats i mallen *apim.json*. *backend-id* kan även referera till en annan serverdelsresurs som skapats med hjälp av API Management-API:erna. I den här artikeln anger du *Server dels-ID* till värdet för parametern *service_fabric_backend_name* .

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Använd följande skript för att distribuera Resource Manager-mallen och parameterfilerna för API Management:

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"
$templatepath="C:\clustertemplates"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\network-apim.json" -TemplateParameterFile "$templatepath\network-apim.parameters.json" -Verbose

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\apim.json" -TemplateParameterFile "$templatepath\apim.parameters.json" -Verbose
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az deployment group create --name ApiMgmtNetworkDeployment --resource-group $ResourceGroupName --template-file network-apim.json --parameters @network-apim.parameters.json

az deployment group create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json
```

## <a name="test-it"></a>Testa den

Nu kan du testa att skicka en begäran till din serverdelstjänst i Service Fabric via API Management direkt från [Azure-portalen](https://portal.azure.com).

 1. I API Management-tjänsten väljer du **API**.
 2. I det **Service Fabric APP**-API som du skapade i föregående steg väljer du fliken **Test** väljer sedan åtgärden **Values**.
 3. Klicka på knappen **Skicka** för att skicka en testbegäran till serverdelstjänsten.  Du bör se ett HTTP-svar som detta:

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Ocp-Apim-Trace-Location: https://apimgmtstodhwklpry2xgkdj.blob.core.windows.net/apiinspectorcontainer/PWSQOq_FCDjGcaI1rdMn8w2-2?sv=2015-07-08&sr=b&sig=MhQhzk%2FEKzE5odlLXRjyVsgzltWGF8OkNzAKaf0B1P0%3D&se=2018-01-28T01%3A04%3A44Z&sp=r&traceId=9f8f1892121e445ea1ae4d2bc8449ce4

    Date: Sat, 27 Jan 2018 01:04:44 GMT


    ["value1", "value2"]
    ```

## <a name="clean-up-resources"></a>Rensa resurser

Ett kluster består av andra Azure-resurser förutom själva klusterresursen. Det enklaste sättet att ta bort klustret och alla de resurser det använder är att ta bort resursgruppen.

Logga in på Azure och välj det prenumerations-ID som du vill ta bort klustret med.  Du kan hitta ditt prenumerations-ID genom att logga in på [Azure Portal](https://portal.azure.com). Ta bort resurs gruppen och alla kluster resurser med [cmdleten Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du använder [API Management](../api-management/import-and-publish.md).

Du kan också använda [Azure Portal](../api-management/how-to-configure-service-fabric-backend.md) för att skapa och hantera Service Fabric Server delar för API Management.

[azure-powershell]: /powershell/azure/

[apim-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.json
[apim-parameters-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.parameters.json

[network-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.json
[network-parameters-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.parameters.json

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png