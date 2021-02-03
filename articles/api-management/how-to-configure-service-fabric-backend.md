---
title: Konfigurera Service Fabric Server del i Azure API Management | Microsoft Docs
description: Så här skapar du en Service Fabric tjänst Server del i Azure API Management med hjälp av Azure Portal
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.openlocfilehash: f6474dbd02c501612b951ddae490385a5d843fbf
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500635"
---
# <a name="set-up-a-service-fabric-backend-in-api-management-using-the-azure-portal"></a>Konfigurera en Service Fabric Server del i API Management med hjälp av Azure Portal

Den här artikeln visar hur du konfigurerar en [Service Fabric](../service-fabric/service-fabric-api-management-overview.md) tjänst som en anpassad API-backend med hjälp av Azure Portal. I demonstrations syfte visas hur du konfigurerar ett grundläggande tillstånds löst ASP.NET Core Reliable service som Service Fabric Server del.

I bakgrunden, se server delar [i API Management](backends.md).

## <a name="prerequisites"></a>Förutsättningar

Krav för att konfigurera en exempel tjänst i ett Service Fabric kluster som kör Windows som en anpassad server del:

* **Windows utvecklings miljö** – installera [Visual Studio 2019](https://www.visualstudio.com) och **Azure-utveckling**, **ASP.net och webb utveckling**, och **.net Core plattforms oberoende utvecklings** arbets belastningar. Konfigurera sedan en [.NET-utvecklingsmiljö](../service-fabric/service-fabric-get-started.md).

* **Service Fabric kluster** – se [självstudie: Distribuera ett Service Fabric kluster som kör Windows till ett virtuellt Azure-nätverk](../service-fabric/service-fabric-tutorial-create-vnet-and-windows-cluster.md). Du kan skapa ett kluster med ett befintligt X. 509-certifikat eller för test syfte skapa ett nytt, självsignerat certifikat. Klustret skapas i ett virtuellt nätverk.

* **Exempel Service Fabric app** – skapa en webb-API-app och distribuera till Service Fabric-klustret enligt beskrivningen i [integrera API Management med Service Fabric i Azure](../service-fabric/service-fabric-tutorial-deploy-api-management.md).

    De här stegen skapar en grundläggande tillstånds lös ASP.NET Core Reliable service med hjälp av projekt mal len standard webb-API. Senare exponerar du HTTP-slutpunkten för tjänsten via Azure API Management.

    Anteckna program namnet, till exempel `fabric:/myApplication/myService` . 

* **API Management instans** – en befintlig eller ny API Management instans på nivån **Premium** eller  **Developer** och i samma region som Service Fabric klustret. Om du behöver ett [skapar du en API Management-instans](get-started-create-service-instance.md).

* **Virtuellt nätverk** – Lägg till din API Management-instans i det virtuella nätverk som du skapade för ditt Service Fabric-kluster. API Management kräver ett dedikerat undernät i det virtuella nätverket.

  Anvisningar för hur du aktiverar virtuell nätverks anslutning för API Management-instansen finns i [så här använder du Azure-API Management med virtuella nätverk](api-management-using-with-vnet.md).

## <a name="create-backend---portal"></a>Skapa Server del – Portal

### <a name="add-service-fabric-cluster-certificate-to-api-management"></a>Lägg till Service Fabric kluster certifikat i API Management

Service Fabric kluster certifikat lagras och hanteras i ett Azure Key Vault som är associerat med klustret. Lägg till det här certifikatet till din API Management-instans som ett klient certifikat.

Anvisningar för hur du lägger till ett certifikat i API Management-instansen finns i [så här skyddar du Server dels tjänster med autentisering av klient certifikat i Azure API Management](api-management-howto-mutual-certificates.md). 

> [!NOTE]   
> Vi rekommenderar att du lägger till certifikatet till API Management genom att referera till Key Vault-certifikatet. 

### <a name="add-service-fabric-backend"></a>Lägg till Service Fabric Server del

1. I [Azure Portal](https://portal.azure.com)navigerar du till API Management-instansen.
1. Under **API**: er **väljer du** Server delar  >  **+ Lägg till**.
1. Ange ett Server dels namn och en valfri beskrivning
1. I **typ** väljer du **Service Fabric**.
1. I **Kör URL för körning** anger du namnet på den Service Fabric backend-tjänst som API Management vidarebefordrar begär anden till. Exempel: `fabric:/myApplication/myService`. 
1. Ange ett tal mellan 0 och 10 i **maximalt antal återförsök för partitions matchning**.
1. Ange hanterings slut punkten för Service Fabric klustret. Den här slut punkten är URL: en för klustret på porten `19080` , till exempel `https://mysfcluster.eastus.cloudapp.azure.com:19080` .
1. I **klient certifikat** väljer du Service Fabric kluster certifikat som du har lagt till i API Management-instansen i föregående avsnitt.
1. I **auktoriseringskod för hanterings slut punkt** anger du ett tumavtryck eller serverns X509-namn för ett certifikat som används av Service Fabric Cluster Management-tjänsten för TLS-kommunikation.
1. Aktivera inställningarna för att **Verifiera certifikat kedjan** och **Verifiera certifikat namn** .
1. I **autentiseringsuppgifter** anger du autentiseringsuppgifter om det behövs för att komma till den konfigurerade Server dels tjänsten i Service Fabric. För den exempel app som används i det här scenariot behövs inga autentiseringsuppgifter.
1. Välj **Skapa**.

:::image type="content" source="media/backends/create-service-fabric-backend.png" alt-text="Skapa en Service Fabric-Server del":::

## <a name="use-the-backend"></a>Använd Server delen

Om du vill använda en anpassad server del refererar du till den med hjälp av [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) principen. Den här principen transformerar standard-URL: en för Server dels tjänsten för en inkommande API-begäran till en viss server del, i det här fallet Service Fabric Server delen. 

`set-backend-service`Principen kan vara användbar med ett befintligt API för att transformera en inkommande begäran till en annan server del än den som anges i API-inställningarna. I demonstrations syfte i den här artikeln skapar du ett test-API och anger principen för att dirigera API-begäranden till Service Fabric Server delen. 

### <a name="create-api"></a>Skapa API

Följ stegen i [lägga till ett API manuellt](add-api-manually.md) för att skapa ett tomt API.

* Lämna **webb tjänstens URL** tom i API-inställningarna.
* Lägg till **URL-suffixet för API**, till exempel *infrastruktur resurs*.

  :::image type="content" source="media/backends/create-blank-api.png" alt-text="Skapa tomt API":::

### <a name="add-get-operation-to-the-api"></a>Lägg till GET-åtgärd i API: et

Som du ser i [distribuera en Service Fabric backend-tjänst](../service-fabric/service-fabric-tutorial-deploy-api-management.md#deploy-a-service-fabric-back-end-service), stöder exempel ASP.net Core tjänsten som distribueras i Service Fabric klustret en enda HTTP Get-åtgärd på URL-sökvägen `/api/values` .

Standardsvaret på den sökvägen är en JSON-matris med två strängar:

```json
["value1", "value2"]
```

Om du vill testa integreringen av API Management med klustret lägger du till motsvarande GET-åtgärd i API: et på sökvägen `/api/values` :

1. Välj det API som du skapade i föregående steg.
1. Välj **+ Lägg till åtgärd**.
1. I fönstret **klient** del anger du följande värden och väljer **Spara**.

     | Inställning             | Värde                             | 
    |---------------------|-----------------------------------|
    | **Visningsnamn**    | *Testa Server del*                       |  
    | **URL** | GET                               | 
    | **URL**             | `/api/values`                           | 
    
    :::image type="content" source="media/backends/configure-get-operation.png" alt-text="Lägg till GET-åtgärd i API":::

### <a name="configure-set-backend-policy"></a>Konfigurera `set-backend` princip

Lägg till [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) principen i test-API: et.

1. På fliken **design** i avsnittet **inkommande bearbetning** väljer du ikonen kod redigerare ( **</>** ). 
1. Placera markören inuti det **&lt; inkommande &gt;** elementet
1. Lägg till följande princip uttryck. I `backend-id` ersätter du namnet på din Service Fabric Server del.

   `sf-resolve-condition`Är ett villkor för återförsök om kluster-partitionen inte är löst. Antalet återförsök angavs när Server delen konfigurerades.

    ```xml
    <set-backend-service backend-id="mysfbackend" sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")"  />
    ```
1. Välj **Spara**.

    :::image type="content" source="media/backends/set-backend-service.png" alt-text="Konfigurera set-backend-service-princip":::

### <a name="test-backend-api"></a>Testa Server dels-API

1. På fliken **test** väljer du åtgärden **Hämta** som du skapade i ett tidigare avsnitt.
1. Välj **Skicka**.

När det är korrekt konfigurerat visar HTTP-svaret en HTTP-lyckad kod och visar den JSON som returnerades från Server delen Service Fabric.

:::image type="content" source="media/backends/test-backend-service.png" alt-text="Testa Service Fabric Server del":::

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [konfigurerar principer](api-management-advanced-policies.md) som vidarebefordrar begär anden till en server del
* Server delar kan också konfigureras med hjälp av API Management [REST API](/rest/api/apimanagement/2020-06-01-preview/backend), [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)eller [Azure Resource Manager mallar](../service-fabric/service-fabric-tutorial-deploy-api-management.md)

