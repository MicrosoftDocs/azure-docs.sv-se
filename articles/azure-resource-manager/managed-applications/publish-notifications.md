---
title: Hanterade appar med meddelanden
description: Konfigurera hanterade program med webhook-slutpunkter för att ta emot meddelanden om att skapa, uppdatera, ta bort och fel i de hanterade program instanserna.
ms.topic: conceptual
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: 2a2e9d429d494c35c49a5b0a3e10b291fd8f24a6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100633945"
---
# <a name="azure-managed-applications-with-notifications"></a>Azure-hanterade program med meddelanden

Azure-hanterade program meddelanden låter utgivare automatisera åtgärder baserat på livs cykel händelser för de hanterade program instanserna. Utgivare kan ange anpassade meddelande-webhook-slutpunkter för att ta emot händelse meddelanden om nya och befintliga hanterade program instanser. Utgivare kan konfigurera anpassade arbets flöden vid tidpunkten för program etablering, uppdateringar och borttagning.

## <a name="getting-started"></a>Komma igång
Börja ta emot hanterade program genom att sätta upp en offentlig HTTPS-slutpunkt och ange den när du publicerar tjänst katalogens program definition eller Azure Marketplace-erbjudande.

Här är de rekommenderade stegen för att komma igång snabbt:
1. Skapa en offentlig HTTPS-slutpunkt som loggar in inkommande POST-begäranden och returnerar `200 OK` .
2. Lägg till slut punkten i program definitionen för tjänst katalogen eller Azure Marketplace-erbjudandet enligt beskrivningen längre fram i den här artikeln.
3. Skapa en hanterad program instans som hänvisar till program definitionen eller Azure Marketplace-erbjudandet.
4. Verifiera att meddelandena tas emot.
5. Aktivera auktorisering enligt beskrivningen i avsnittet **Endpoint Authentication** i den här artikeln.
6. Följ anvisningarna i avsnittet **meddelande schema** i den här artikeln för att analysera aviserings förfrågningar och implementera affärs logik baserat på meddelandet.

## <a name="add-service-catalog-application-definition-notifications"></a>Lägg till program definitions meddelanden för tjänst katalog
#### <a name="azure-portal"></a>Azure Portal
Information om hur du kommer igång finns i [publicera ett tjänst katalog program via Azure Portal](./publish-portal.md).

![Aviseringar för program definition i tjänst katalogen i Azure Portal](./media/publish-notifications/service-catalog-notifications.png)

#### <a name="rest-api"></a>REST-API

> [!NOTE]
> För närvarande kan du bara ange en slut punkt i `notificationEndpoints` egenskaperna för program definitionen.

``` JSON
    {
      "properties": {
        "isEnabled": true,
        "lockLevel": "ReadOnly",
        "displayName": "Sample Application Definition",
        "description": "Notification-enabled application definition.",
        "notificationPolicy": {
            "notificationEndpoints": [
                {
                    "uri": "https://isv.azurewebsites.net:1214?sig=unique_token"
                }
            ]
        },
        "authorizations": [
          {
            "principalId": "d6b7fbd3-4d99-43fe-8a7a-f13aef11dc18",
            "roleDefinitionId": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635"
          },
        ...

```
## <a name="add-azure-marketplace-managed-application-notifications"></a>Lägg till aviseringar för hanterade program i Azure Marketplace
Mer information finns i [skapa ett erbjudande för Azure-program](../../marketplace/create-new-azure-apps-offer.md).

![Azure Marketplace-hanterade program meddelanden i Azure Portal](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Händelseutlösare
I följande tabell beskrivs alla möjliga kombinationer av EventType och ProvisioningState och deras utlösare:

Typ | ProvisioningState | Utlösare för avisering
---|---|---
PUT | Har godkänts | En hanterad resurs grupp har skapats och projicerats efter att program har lagts till (innan distributionen i den hanterade resurs gruppen har startats).
PUT | Lyckades | Fullständig etablering av det hanterade programmet lyckades efter en placering.
PUT | Misslyckad | Det gick inte att ställa in program instansens etablerings plats.
9.0a | Lyckades | Efter en lyckad korrigering på den hanterade program instansen för att uppdatera taggar, JIT-åtkomstkontroll eller hanterad identitet.
DELETE | Tas bort | Så snart användaren initierar en borttagning av en hanterad App-instans.
DELETE | Borttagen | Efter en fullständig och lyckad borttagning av det hanterade programmet.
DELETE | Misslyckad | Efter ett fel under avetablerings processen som blockerar borttagningen.
## <a name="notification-schema"></a>Meddelande schema
När du skapar en webhook-slutpunkt för att hantera meddelanden måste du parsa nytto lasten för att få viktiga egenskaper för att sedan agera på aviseringen. Tjänst katalogen och hanterade program meddelanden i Azure Marketplace innehåller många av samma egenskaper. Två små skillnader beskrivs i tabellen som följer exemplen.

#### <a name="service-catalog-application-notification-schema"></a>Tjänst katalog program aviserings schema
Här är ett exempel på en tjänst katalog meddelande efter lyckad etablering av en hanterad program instans:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "applicationDefinitionId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>"    
}

```

Om etableringen Miss lyckas skickas ett meddelande med fel information till den angivna slut punkten.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "applicationDefinitionId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>",
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

#### <a name="azure-marketplace-application-notification-schema"></a>Aviserings schema för Azure Marketplace-program

Här är ett exempel på en tjänst katalog meddelande efter lyckad etablering av en hanterad program instans:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    }
}

```

Om etableringen Miss lyckas skickas ett meddelande med fel information till den angivna slut punkten.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    },
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

Parameter | Beskrivning
---|---
Händelsetyp | Typ av händelse som utlöste meddelandet. (Till exempel, skicka, korrigera, ta bort.)
applicationId | Det fullständigt kvalificerade resurs-ID: t för det hanterade program som meddelandet utlöstes för.
Händelsetid | Tidsstämpeln för händelsen som utlöste meddelandet. (Datum och tid i formatet UTC-ISO 8601.)
provisioningState | Etablerings statusen för den hanterade program instansen. (Till exempel lyckad, misslyckad, borttagning, borttagen.)
fel | *Anges bara när ProvisioningState misslyckades*. Innehåller felkod, meddelande och information om problemet som orsakade felet.
applicationDefinitionId | *Anges endast för hanterade program i tjänst katalog*. Representerar det fullständigt kvalificerade resurs-ID: t för den program definition som den hanterade program instansen etablerades för.
planera | *Anges endast för hanterade program i Azure Marketplace*. Representerar utgivare, erbjudande, SKU och version för den hanterade program instansen.
billingDetails | *Anges endast för hanterade program i Azure Marketplace.* Fakturerings information för den hanterade program instansen. Innehåller resourceUsageId som du kan använda för att söka efter användnings information i Azure Marketplace.

## <a name="endpoint-authentication"></a>Endpoint Authentication
För att skydda webhook-slutpunkten och se till att aviseringen är äkta:
1. Ange en frågeparameter ovanpå webhook-URI: n, så här: https \: //Your-Endpoint.com? sig = GUID. Kontrol lera att Frågeparametern `sig` har det förväntade värdet för varje meddelande `Guid` .
2. Utfärda en GET på den hanterade program instansen med hjälp av applicationId. Kontrol lera att provisioningState matchar provisioningState för meddelandet för att säkerställa konsekvens.

## <a name="notification-retries"></a>Aviserings försök

I meddelande tjänsten för hanterade program förväntas ett `200 OK` svar från webhook-slutpunkten till meddelandet. Meddelande tjänsten kommer att försöka igen om webhook-slutpunkten returnerar en HTTP-felkod som är större än eller lika med 500, om den returnerar felkoden 429 eller om slut punkten är tillfälligt otillgänglig. Om webhook-slutpunkten inte blir tillgänglig inom 10 timmar tas aviserings meddelandet bort och Återförsöken stoppas.
