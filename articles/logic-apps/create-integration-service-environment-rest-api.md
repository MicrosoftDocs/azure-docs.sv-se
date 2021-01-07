---
title: Skapa integrerings tjänst miljöer (ISEs) med Logic Apps REST API
description: Skapa en integrerings tjänst miljö (ISE) med hjälp av Logic Apps REST API så att du kan komma åt Azure Virtual Networks (virtuella nätverk) från Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 12/30/2020
ms.openlocfilehash: ee6c116d02a7be1682d9e8379037ef1b8c92bce8
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97967046"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Skapa en Integration Service Environment (ISE) med hjälp av Logic Apps REST-API

För scenarier där dina Logic Apps och integrations konton behöver åtkomst till ett [virtuellt Azure-nätverk](../virtual-network/virtual-networks-overview.md)kan du skapa en [ *integrerings tjänst miljö* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) med hjälp av Logic Apps REST API. Mer information om ISEs finns i [åtkomst till Azure Virtual Network-resurser från Azure Logic Apps](connect-virtual-network-vnet-isolated-environment-overview.md).

Den här artikeln visar hur du skapar en ISE med hjälp av Logic Apps REST API i allmänhet. Alternativt kan du också aktivera en [systemtilldelad eller användardefinierad hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) på din ISE, men endast genom att använda Logic Apps REST API för tillfället. Med den här identiteten kan du autentisera åtkomst till skyddade resurser, till exempel virtuella datorer och andra system eller tjänster, som finns i eller är anslutna till ett virtuellt Azure-nätverk. På så sätt behöver du inte logga in med dina autentiseringsuppgifter.

Mer information om andra sätt att skapa en ISE finns i följande artiklar:

* [Skapa en ISE med hjälp av Azure Portal](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Skapa en ISE med hjälp av exempel mal len Azure Resource Manager snabb start](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment)
* [Skapa en ISE som stöder användning av Kundhanterade nycklar för kryptering av data i vila](customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Krav

* Samma krav [](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) och [åtkomst krav](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) som när du skapar en ISE i Azure Portal

* Eventuella ytterligare resurser som du vill använda med din ISE så att du kan inkludera information i ISE-definitionen, till exempel: 

  * Om du vill aktivera stöd för självsignerade certifikat måste du inkludera information om certifikatet i ISE-definitionen.

  * Om du vill aktivera den tilldelade hanterade identiteten måste du skapa identiteten i förväg och inkludera `objectId` egenskaperna, `principalId` och samt `clientId` deras värden i definitionen för ISE. Mer information finns i [skapa en användare som tilldelats en hanterad identitet i Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

* Ett verktyg som du kan använda för att skapa din ISE genom att anropa Logic Apps REST API med en begäran om HTTPS-begäran. Du kan till exempel använda [Postman](https://www.getpostman.com/downloads/), eller så kan du bygga en logisk app som utför den här uppgiften.

## <a name="create-the-ise"></a>Skapa ISE

Om du vill skapa din ISE genom att anropa Logic Apps REST API, gör du denna HTTPS-begäran:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Logic Apps REST API 2019-05-01-versionen kräver att du gör en egen HTTP-begäran för ISE-anslutningar.

Distributionen tar vanligt vis inom två timmar att slutföra. Ibland kan distributionen ta upp till fyra timmar. Om du vill kontrol lera distributions statusen i [Azure Portal](https://portal.azure.com)går du till Azure-verktygsfältet och väljer aviserings ikonen, som öppnar fönstret meddelanden.

> [!NOTE]
> Om distributionen Miss lyckas eller om du tar bort din ISE kan Azure ta upp till en timme innan du släpper upp dina undernät. Den här fördröjningen innebär att du kan behöva vänta innan du återanvänder dessa undernät i en annan ISE.
>
> Om du tar bort det virtuella nätverket tar Azure vanligt vis upp till två timmar innan du frigör dina undernät, men den här åtgärden kan ta längre tid. 
> Se till att inga resurser fortfarande är anslutna när du tar bort virtuella nätverk. 
> Se [ta bort virtuellt nätverk](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>Begärandehuvud

I begär ande huvudet inkluderar du följande egenskaper:

* `Content-type`: Ange det här egenskap svärdet till `application/json` .

* `Authorization`: Ange det här egenskap svärdet till Bearer-token för den kund som har åtkomst till den Azure-prenumeration eller resurs grupp som du vill använda.

<a name="request-body"></a>

## <a name="request-body"></a>Begärandetext

I begär ande texten anger du resurs definitionen som ska användas för att skapa din ISE, inklusive information om ytterligare funktioner som du vill aktivera på din ISE, till exempel:

* Om du vill skapa en ISE som tillåter att du använder ett självsignerat certifikat och certifikat som utfärdats av företags certifikat utfärdare som är installerade på `TrustedRoot` platsen, inkluderar du `certificates` objektet i definitions avsnittet för ISE `properties` , som den här artikeln beskriver senare.

* Om du vill skapa en ISE som använder en systemtilldelad eller användardefinierad hanterad identitet, inkluderar du `identity` objektet med den hanterade identitets typen och annan obligatorisk information i definitionen för ISE, eftersom den här artikeln senare beskriver.

* Om du vill skapa en ISE som använder Kundhanterade nycklar och Azure Key Vault för att kryptera data i vila, inkluderar du den [information som möjliggör kundhanterad nyckel support](customer-managed-keys-integration-service-environment.md). Du kan bara ställa in Kundhanterade nycklar *när de skapas*, inte senare.

### <a name="request-body-syntax"></a>Syntax för begär ande text

Här är syntaxen för begär ande texten, som beskriver de egenskaper som ska användas när du skapar din ISE:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   // Include the `identity` object to enable the system-assigned identity or user-assigned identity
   "identity": {
      "type": <"SystemAssigned" | "UserAssigned">,
      // When type is "UserAssigned", include the following "userAssignedIdentities" object:
      "userAssignedIdentities": {
         "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{user-assigned-managed-identity-object-ID}": {
            "principalId": "{principal-ID}",
            "clientId": "{client-ID}"
         }
      }
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      // Include `certificates` object to enable self-signed certificate and the certificate issued by Enterprise Certificate Authority
      "certificates": {
         "testCertificate": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

### <a name="request-body-example"></a>Exempel på begär ande text

Den här exempel texten visar exempel värden:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
         "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/*********************************": {
            "principalId": "*********************************",
            "clientId": "*********************************"
         }
      }
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "certificates": {
         "testCertificate": {
            "publicCertificate": "LS0tLS1CRUdJTiBDRV...",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

## <a name="add-custom-root-certificates"></a>Lägg till anpassade rot certifikat

Du använder ofta en ISE för att ansluta till anpassade tjänster i ditt virtuella nätverk eller lokalt. Dessa anpassade tjänster skyddas ofta av ett certifikat som har utfärdats av en anpassad rot certifikat utfärdare, till exempel en företags certifikat utfärdare eller ett självsignerat certifikat. Mer information om hur du använder självsignerade certifikat finns i [säker åtkomst och data åtkomst för utgående anrop till andra tjänster och system](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests). För att din ISE ska kunna ansluta till dessa tjänster via Transport Layer Security (TLS) behöver din ISE åtkomst till dessa rot certifikat. Om du vill uppdatera ISE med ett anpassat betrott rot certifikat, gör du följande HTTPS- `PATCH` begäran:

`PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

Granska följande saker innan du utför den här åtgärden:

* Se till att du laddar upp rot certifikatet *och* alla mellanliggande certifikat. Det maximala antalet certifikat är 20.

* Överföring av rot certifikat är en ersättnings åtgärd där den senaste överföringen skriver över tidigare överföringar. Om du till exempel skickar en begäran som överför ett certifikat och sedan skickar en annan begäran om att ladda upp ett annat certifikat, används endast det andra certifikatet i ISE. Om du behöver använda båda certifikaten lägger du till dem tillsammans i samma begäran.  

* Överföring av rot certifikat är en asynkron åtgärd som kan ta lite tid. Om du vill kontrol lera status eller resultat kan du skicka en `GET` begäran med samma URI. Svarsmeddelandet har ett `provisioningState` fält som returnerar `InProgress` värdet när uppladdnings åtgärden fortfarande fungerar. När `provisioningState` värdet är `Succeeded` slutförs överförings åtgärden.

#### <a name="request-body-syntax-for-adding-custom-root-certificates"></a>Syntax för begär ande brödtext för att lägga till anpassade rot certifikat

Här är syntaxen för begär ande texten, som beskriver de egenskaper som ska användas när du lägger till rot certifikat:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "properties": {
      "certificates": {
         "testCertificate1": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         },
         "testCertificate2": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

## <a name="next-steps"></a>Nästa steg

* [Lägga till resurser i integreringstjänstmiljöer](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Hantera integreringstjänstmiljöer](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
