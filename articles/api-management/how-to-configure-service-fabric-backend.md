---
title: Konfigurera Service Fabric i Azure API Management | Microsoft Docs
description: Så här skapar du en Service Fabric-tjänsts backend i Azure API Management med hjälp av Azure Portal
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3dda6f18c2bf92b537c2f4be1c6a0a3b70cdc28a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815890"
---
# <a name="set-up-a-service-fabric-backend-in-api-management-using-the-azure-portal"></a>Konfigurera en Service Fabric i en API Management med hjälp av Azure Portal

Den här artikeln visar hur du konfigurerar [Service Fabric](../service-fabric/service-fabric-api-management-overview.md) en tjänst som en anpassad API-Azure Portal. I demonstrationssyfte visar den hur du ställer in en grundläggande tillståndslös ASP.NET Core Reliable Service som Service Fabric-backend.

Bakgrundsinformation finns i [Backends in API Management](backends.md).

## <a name="prerequisites"></a>Förutsättningar

Krav för att konfigurera en exempeltjänst i ett Service Fabric som kör Windows som en anpassad server:

* **Windows-utvecklingsmiljö** [– Visual Studio arbetsbelastningarna 2019](https://www.visualstudio.com) och **Azure** Development , ASP.NET och **webbutveckling** och **.NET Core plattformsoberoende** utveckling. Konfigurera sedan en [.NET-utvecklingsmiljö](../service-fabric/service-fabric-get-started.md).

* **Service Fabric kluster** – Se [Självstudie: Distribuera ett Service Fabric-kluster som kör Windows till ett virtuellt Azure-nätverk.](../service-fabric/service-fabric-tutorial-create-vnet-and-windows-cluster.md) Du kan skapa ett kluster med ett befintligt X.509-certifikat eller i testsyfte skapa ett nytt, själv signerat certifikat. Klustret skapas i ett virtuellt nätverk.

* **Exempelapp Service Fabric –** Skapa en webb-API-app och distribuera till Service Fabric-klustret enligt beskrivningen [i Integrera API Management med Service Fabric i Azure](../service-fabric/service-fabric-tutorial-deploy-api-management.md).

    De här stegen skapar en grundläggande tillståndslös ASP.NET Core Reliable Service med hjälp av standardmallen för Webb-API-projekt. Senare exponerar du HTTP-slutpunkten för den här tjänsten via Azure API Management.

    Anteckna programnamnet, till exempel `fabric:/myApplication/myService` . 

* **API Management instans** – en befintlig eller API Management instans på **Premium-** eller  **Developer-nivån** och i samma region som Service Fabric klustret. Om du behöver en skapar [du en API Management instans](get-started-create-service-instance.md).

* **Virtuellt nätverk** – Lägg API Management instans i det virtuella nätverk som du skapade för Service Fabric klustret. API Management kräver ett dedikerat undernät i det virtuella nätverket.

  Anvisningar för hur du aktiverar virtuell nätverksanslutning för API Management instansen finns i Så här använder [du Azure API Management med virtuella nätverk.](api-management-using-with-vnet.md)

## <a name="create-backend---portal"></a>Skapa backend – portal

### <a name="add-service-fabric-cluster-certificate-to-api-management"></a>Lägga Service Fabric klustercertifikat till API Management

Certifikatet Service Fabric lagras och hanteras i ett Azure-nyckelvalv som är associerat med klustret. Lägg till det här certifikatet API Management instansen som ett klientcertifikat.

Anvisningar för hur du lägger till ett certifikat till din API Management-instans finns i Skydda [servertjänster](api-management-howto-mutual-certificates.md)med klientcertifikatautentisering i Azure API Management . 

> [!NOTE]   
> Vi rekommenderar att du lägger till certifikatet API Management genom att referera till nyckelvalvscertifikatet. 

### <a name="add-service-fabric-backend"></a>Lägga Service Fabric backend

1. I [Azure Portal](https://portal.azure.com)navigerar du till din API Management instans.
1. Under **API:er** väljer **du Backends**  >  **+ Add**.
1. Ange ett namn på backend och en valfri beskrivning
1. I **Typ** väljer du **Service Fabric**.
1. I **Körnings-URL** anger du namnet på Service Fabric backend-tjänsten som API Management vidarebefordrar begäranden till. Exempel: `fabric:/myApplication/myService`. 
1. I **Maximalt antal partitionsmatchning återförsök** anger du ett tal mellan 0 och 10.
1. Ange hanteringsslutpunkten för Service Fabric klustret. Den här slutpunkten är URL:en för klustret på port `19080` , till exempel `https://mysfcluster.eastus.cloudapp.azure.com:19080` .
1. I **Klientcertifikat** väljer du det Service Fabric klustercertifikat som du lade till API Management instansen i föregående avsnitt.
1. I **Auktoriseringsmetod för** hanteringsslutpunkt anger du ett tumavtryck eller serverns X509-namn på ett certifikat som används av Service Fabric för TLS-kommunikation.
1. Aktivera inställningarna **Verifiera certifikatkedja** och **Verifiera certifikatnamn.**
1. I **Autentiseringsuppgifter för** auktorisering anger du autentiseringsuppgifter, om det behövs, för att nå den konfigurerade backend-tjänsten Service Fabric. Autentiseringsuppgifter behövs inte för exempelappen som används i det här scenariot.
1. Välj **Skapa**.

:::image type="content" source="media/backends/create-service-fabric-backend.png" alt-text="Skapa en Service Fabric-backend":::

## <a name="use-the-backend"></a>Använda backend

Om du vill använda en anpassad backend refererar du till den med hjälp av [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) principen . Den här principen omvandlar bas-URL:en för serverdelstjänsten för en inkommande API-begäran till en angiven serverdel, i det här Service Fabric serverdel. 

Principen kan vara användbar med ett befintligt API för att omvandla en inkommande begäran till en annan server än den som `set-backend-service` anges i API-inställningarna. I demonstrationssyfte i den här artikeln skapar du ett test-API och anger principen för att dirigera API-begäranden till Service Fabric-backend. 

### <a name="create-api"></a>Skapa API

Följ stegen i Lägg [till ett API manuellt för](add-api-manually.md) att skapa ett tomt API.

* Lämna webbtjänstens URL tom **i API-inställningarna.**
* Lägg till **ett API URL-suffix,** till exempel *fabric*.

  :::image type="content" source="media/backends/create-blank-api.png" alt-text="Skapa tomt API":::

### <a name="add-get-operation-to-the-api"></a>Lägga till GET-åtgärd i API:et

Som du ser i Distribuera en Service Fabric-servertjänst stöder exempeltjänsten ASP.NET Core som distribueras i Service Fabric-klustret en enda HTTP [GET-åtgärd](../service-fabric/service-fabric-tutorial-deploy-api-management.md#deploy-a-service-fabric-back-end-service)på URL-sökvägen `/api/values` .

Standardsvaret på den sökvägen är en JSON-matris med två strängar:

```json
["value1", "value2"]
```

Om du vill testa integreringen API Management med klustret lägger du till motsvarande GET-åtgärd i API:et på sökvägen `/api/values` :

1. Välj det API som du skapade i föregående steg.
1. Välj **+ Lägg till åtgärd**.
1. I **fönstret Frontend** anger du följande värden och väljer **Spara**.

     | Inställning             | Värde                             | 
    |---------------------|-----------------------------------|
    | **Visningsnamn**    | *Testa backend*                       |  
    | **URL** | GET                               | 
    | **URL**             | `/api/values`                           | 
    
    :::image type="content" source="media/backends/configure-get-operation.png" alt-text="Lägg till GET-åtgärd till API":::

### <a name="configure-set-backend-policy"></a>Konfigurera `set-backend` princip

Lägg till [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) principen i test-API:et.

1. Välj **kodredigeraren** ( ) **i avsnittet Inkommande** bearbetning på **</>** fliken Design. 
1. Placera markören inuti **&lt; det inkommande &gt;** elementet
1. Lägg till följande principutdrag. I `backend-id` ersätter du namnet på Service Fabric backend.

   `sf-resolve-condition`är ett återförsöksvillkor om klusterpartitionen inte är löst. Antalet återförsök har angetts när serveruppsättningen konfigurerades.

    ```xml
    <set-backend-service backend-id="mysfbackend" sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")"  />
    ```
1. Välj **Spara**.

    :::image type="content" source="media/backends/set-backend-service.png" alt-text="Konfigurera princip för set-backend-service":::

### <a name="test-backend-api"></a>Testa backend-API:et

1. På fliken **Test** väljer du **get-åtgärden** som du skapade i föregående avsnitt.
1. Välj **Skicka**.

När HTTP-svaret är korrekt konfigurerat visar det en HTTP-lyckad kod och visar den JSON som returneras från Service Fabric-tjänsten.

:::image type="content" source="media/backends/test-backend-service.png" alt-text="Testa Service Fabric-backend":::

## <a name="next-steps"></a>Nästa steg

* Lär dig hur [du konfigurerar principer](api-management-advanced-policies.md) för att vidarebefordra begäranden till en backend
* Backends kan också konfigureras med hjälp av API Management [REST API,](/rest/api/apimanagement/2020-06-01-preview/backend) [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)eller [Azure Resource Manager mallar](../service-fabric/service-fabric-tutorial-deploy-api-management.md)

