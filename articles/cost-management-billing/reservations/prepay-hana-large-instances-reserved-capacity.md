---
title: Få rabatt på SAP HANA – stora instanser med en Azure-reservation
description: Ta reda på det du behöver veta innan du köper en reservation för HANA – stora instanser och hur du genomför köpet.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 03/19/2021
ms.author: banders
ms.openlocfilehash: 579c7b5f66f52551e2504101406e1f7b2bb69e92
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775535"
---
# <a name="save-on-sap-hana-large-instances-with-an-azure-reservation"></a>Få rabatt på SAP HANA – stora instanser med en Azure-reservation

Du kan spara pengar på dina stora SAP HANA-instanser (HLI) när du förköper Azure-reservationer för ett eller tre år. Reservationsrabatten tillämpas på den etablerade HLI-SKU som matchar den köpta reserverade instansen. I den här artikeln får du lära dig det du behöver veta innan du köper en reservation och hur du genomför köpet.

Genom att köpa en reservation gör du ett åtagande att använda HLI-instansen i ett eller tre år. Köpet av reserverad HLI-kapacitet gäller den beräkningskraft och NFS-lagring som ingår i SKU:n. Kostnader för programvarulicenser som operativsystemet, SAP eller ytterligare lagringskostnader ingår inte i reservationen. Reservationsrabatten gäller automatiskt för den etablerade SAP HLI-instansen. När reservationsperioden är slut börjar du betala per användning för den etablerade resursen.

## <a name="purchase-considerations"></a>Att tänka på innan du köper

Du måste etablera en HLI-SKU innan du köper den reserverade kapaciteten. Du betalar för reservationen i förväg eller via månadsbetalningar. Följande begränsningar gäller för reserverade HLI-kapaciteter:

- Reservationsrabatter gäller endast för prenumerationer av typen Enterprise-avtal och Microsoft-kundavtal. Andra prenumerationer stöds inte.
- Instansstorleken är inte flexibel för reserverade HLI-kapaciteter. En reservation gäller endast den SKU och region du köper den för.
- Du kan inte annullera eller ändra reservationen själv.
- Det reserverade kapacitetsomfånget är ett enskilt omfång, så det gäller för en enda prenumeration och resursgrupp. Den köpta kapaciteten kan inte uppdateras för användning av en annan prenumeration.
- Du kan inte ha ett delat reservationsomfång för reserverad HANA-kapacitet. Du kan inte dela upp, slå samman eller uppdatera reservationsomfånget.
- Du kan köpa en HLI åt gången med API-anrop för reserverade kapaciteter. Gör ytterligare API-anrop om du vill köpa ytterligare kvantiteter.

Du kan köpa reserverad kapacitet i Azure-portalen eller via [REST-API:et](/rest/api/reserved-vm-instances/reservationorder/purchase).

## <a name="buy-a-hana-large-instance-reservation"></a>Köpa en reservation för en stor HANA-instans

Använd följande information när du ska köpa en HLI-reservation med [REST-API:et Reservation Order](/rest/api/reserved-vm-instances/reservationorder/purchase).

### <a name="get-the-reservation-order-and-price"></a>Välj reservationsbeställningen och priset

Börja med att hämta reservationsbeställningen och priset för SKU:n för den etablerade stora HANA-instansen via API:et [Calculate Price](/rest/api/reserved-vm-instances/reservationorder/calculate).

I följande exempel används [armclient](https://github.com/projectkudu/ARMClient) till att göra REST API-anrop med PowerShell. Reservationsbeställningen, förfrågan i API:et Calculate Price och brödtexten i förfrågan bör likna följande:

```azurepowershell-interactive
armclient post /providers/Microsoft.Capacity/calculatePrice?api-version=2019-04-01  "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
        'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
        'billingplan': 'Monthly'
        'displayName': 'testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported'
    }
}"
```

Mer information om datafälten och deras beskrivningar finns i [reservationsfält för HLI](#hli-reservation-fields).

Det returnerade svaret bör likna följande exempelsvar. Notera `quoteId`-värdet som returneras.

```
{
  "properties": {
    "currencyCode": "USD",
    "netTotal": 313219.0,
    "taxTotal": 0.0,
    "isTaxIncluded": false,
    "grandTotal": 313219.0,
    "purchaseRequest": {
      "sku": {
        "name": "SAP_HANA_On_Azure_S224om"
      },
      "location": "eastus",
      "properties": {
        "billingScopeId": "/subscriptions/11111111-1111-1111-111111111111",
        "term": "P1Y",
        "billingPlan": "Upfront",
        "quantity": 1,
        "displayName": "testreservation_S224om",
        "appliedScopes": [
          "/subscriptions/11111111-1111-1111-111111111111"
        ],
        "appliedScopeType": "Single",
        "reservedResourceType": "SapHana",
        "instanceFlexibility": "NotSupported"
      }
    },
    "quoteId": "d0fd3a890795",
    "isBillingPartnerManaged": true,
    "reservationOrderId": "22222222-2222-2222-2222-222222222222",
    "skuTitle": "SAP HANA on Azure Large Instances - S224om - US East",
    "skuDescription": "SAP HANA on Azure Large Instances, S224om",
    "pricingCurrencyTotal": {
      "currencyCode": "USD",
      "amount": 313219.0
    }
  }
}
```

### <a name="make-your-purchase"></a>Genomför köpet

Genomför köpet med de returnerade värdena för `quoteId` och `reservationOrderId` från avsnittet [Hämta reservationsbeställningen och priset](#get-the-reservation-order-and-price).

Här är en exempelförfrågan:

```azurepowershell-interactive
armclient put /providers/Microsoft.Capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2019-04-01  "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
       'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
               'billingplan': 'Monthly'

        'displayName': ' testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported',
       'renew': true,
       'quoteId': 'd0fd3a890795'
    }
}"
```

Här är ett exempelsvar: Om beställningen går igenom ska `provisioningState` vara `creating`.

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222",
  "etag": 1,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "term": "P1Y",
    "provisioningState": "Creating",
    "reservations": [
      {
        "sku": {
          "name": "SAP_HANA_On_Azure_S224om"
        },
        "id": "/providers/microsoft.capacity/reservationOrders22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333",
        "type": "Microsoft.Capacity/reservationOrders/reservations",
        "name": "22222222-2222-2222-2222-222222222222/33333333-3333-3333-3333-3333333333333",
        "etag": 1,
        "location": "eastus”
        "properties": {
          "appliedScopes": [
            "/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123"
          ],
          "appliedScopeType": "Single",
          "quantity": 1,
          "provisioningState": "Creating",
          "displayName": " testreservation_S224om",
          "effectiveDateTime": "2020-07-14T05:42:34.3528353Z",
          "lastUpdatedDateTime": "2020-07-14T05:42:34.3528353Z",
          "reservedResourceType": "SapHana",
          "instanceFlexibility": "NotSupported",
          "skuDescription": "SAP HANA on Azure Large Instances – S224om - US East",
          "renew": true
        }
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

### <a name="verify-purchase-status-success"></a>Verifiera köpets status

Kör en GET-förfrågan för reservationsbeställningen så att du ser status för köpet. `provisioningState` bör vara `Succeeded`.

```azurepowershell-interactive
armclient get /providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2018-06-01
```

Svaret bör likna nedanstående exempel:

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/44444444-4444-4444-4444-444444444444",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222 ",
  "etag": 8,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "createdDateTime": "2020-07-14T05:44:47.157579Z",
    "expiryDate": "2021-07-14",
    "term": "P1Y",
    "provisioningState": "Succeeded",
    "reservations": [
      {
        "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333"
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

## <a name="hli-reservation-fields"></a>Reservationsfält för HLI

Här förklaras innebörden av de olika reservationsfälten.

  **SKU** namnet på HLI-SKU:n. Det ser ut som `SAP_HANA_On_Azure_<SKUname>`.

  **Plats** tillgängliga HLI-regioner. I [SKU:er för SAP HANA i Azure (stora instanser)](../../virtual-machines/workloads/sap/hana-available-skus.md) kan du läsa om tillgängliga regioner. Använd [API-anropet för att hämta platser](/rest/api/resources/subscriptions/listlocations#locationlistresult) när du ska hämta formatet för platssträngen.

  **Typ av reserverad resurs** `SapHana`

  **Prenumeration** prenumerationen som används för att betala reservationen. Reservationen debiteras via prenumerationens betalningsmetod. Prenumerationstypen måste vara EA (erbjudandenummer: MS-AZR-0017P eller MS-AZR-0148P) eller Microsoft-kundavtal. Avgifterna dras från saldot för Azure-förskottsbetalningen (kallades tidigare ekonomiskt åtagande) om det finns ett sådant eller debiteras som överförbrukning.

  **Omfång** reservationens omfång måste vara ett enskilt omfång.

  **Period** ett år eller tre år. Visas som `P1Y` eller `P3Y`.

  **Kvantitet** antalet instanser som köps i reservationen. Du köper en enda HLI åt gången. Upprepa API-anropet med motsvarande fält om du behöver fler reservationer.

## <a name="troubleshoot-errors"></a>Felsöka fel

Du kan få ett felmeddelande som liknar följande exempel när du köper en reservation. En möjlig orsak är att HLI:n för köpet inte är etablerad. I så fall kontaktar du Microsoft-teamet för ditt konto om att etablera en HLI innan du försöker köpa reservationen.

```
{
  "error": {
    "code": "BadRequest",
    "message": "Capacity check or quota check failed. Please select a different subscription or 
location. You can also go to https://aka.ms/corequotaincrease to learn about quota increase."
  }
} 
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om att [Anropa Azure REST-API:er med Postman och cURL](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman).
- I [SKU:er för SAP HANA i Azure (stora instanser)](../../virtual-machines/workloads/sap/hana-available-skus.md) kan du läsa om tillgängliga SKU:er och regioner.