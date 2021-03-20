---
title: Azure API Management mjuk borttagning (förhands granskning) | Microsoft Docs
description: Med mjuk borttagning kan du återställa borttagna API Management instanser.
ms.service: api-management
ms.topic: conceptual
author: vladvino
ms.author: apimpm
ms.date: 11/27/2020
ms.openlocfilehash: e2842f3e428abb4f0eb628dbb8e446f2714d5d89
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101652393"
---
# <a name="api-management-soft-delete-preview"></a>API Management mjuk borttagning (förhands granskning)

Med API Management mjuk borttagning (för hands version) kan du återställa och återställa nyligen borttagna API Management (APIM) instanser.

> [!IMPORTANT]
> Endast API Management instanser som tas bort med `2020-06-01-preview` och senare API-versioner kommer att tas bort och återskapas med hjälp av stegen som beskrivs i den här artikeln. APIM-instanser som tagits bort med hjälp av tidigare API-versioner fortsätter att vara hårt borttagna. Azure PowerShell och Azure CLI använder för närvarande inte `2020-06-01-preview` versionen och kommer också att leda till beteende för hårda rader.

## <a name="supporting-interfaces"></a>Stöd för gränssnitt

Funktionen mjuk borttagning är tillgänglig via [REST API](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/restore).

> [!TIP]
> Se [azure REST API Reference](/rest/api/azure/) för tips och verktyg för att anropa Azure REST-API: er.

| Åtgärd | Beskrivning | API Management namnrymd | Lägsta API-version |
|--|--|--|--|
| [Skapa eller uppdatera](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) | Skapar eller uppdaterar en API Management-tjänst.  | API Management tjänst | Valfri |
| [Skapa eller uppdatera](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) med `restore` egenskap inställd på **True** | Tar bort API Management-tjänsten om den tidigare varit mjuk – borttagen. Om `restore` har angetts och angetts till `true` alla andra egenskaper kommer att ignoreras.  | API Management tjänst |  2020-06-01 – för hands version |
| [Ta bort](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/delete) | Tar bort en befintlig API Management-tjänst. | API Management tjänst | 2020-06-01 – för hands version|
| [Hämta efter namn](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) | Hämta mjuk-borttagen API Management-tjänst efter namn. | Borttagna tjänster | 2020-06-01 – för hands version |
| [Lista efter prenumeration](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) | Visar en lista över alla borttagnings bara tjänster som är tillgängliga för borttagning för den aktuella prenumerationen. | Borttagna tjänster | 2020-06-01 – för hands version
| [Rensa](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) | Rensar API Management tjänsten (tar bort den utan att välja att ångra borttagningen). | Borttagna tjänster | 2020-06-01 – för hands version

## <a name="soft-delete-behavior"></a>Beteende vid mjuk borttagning

Du kan använda valfri API-version för att skapa din API Management-instans, men du måste använda `2020-06-01-preview` eller senare versioner för att kunna ta bort APIM-instansen (och välja att återställa den).

När du tar bort en API Management instans, kommer tjänsten att finnas i ett borttaget tillstånd, vilket gör den oåtkomlig för alla APIM-åtgärder. I det här läget kan APIM-instansen endast visas, återställas eller rensas (tas bort permanent).

Samtidigt schemalägger Azure borttagningen av underliggande data som motsvarar APIM-instansen för körning efter den fördefinierade (48 timmar) kvarhållningsintervall. DNS-posten som motsvarar instansen behålls även under lagrings periodens varaktighet. Du kan inte återanvända namnet på en API Management-instans som har varit mjuk-borttagen tills kvarhållningsperioden har passerat.

Om din APIM-instans inte återställs inom 48 timmar tas den bort (kan inte återställas). Du kan också välja att [Rensa](#purge-a-soft-deleted-apim-instance) (ta bort permanent) din APIM-instans, forgoing mjuk borttagnings perioden.

## <a name="list-deleted-apim-instances"></a>Lista över borttagna APIM-instanser

Du kan kontrol lera att det finns en mjuk borttagnings instans av APIM som kan återställas (ångra borttagning) med antingen de borttagna tjänsterna [Get efter namn](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) eller [lista efter prenumerations](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) åtgärder.

### <a name="get-a-soft-deleted-instance-by-name"></a>Hämta en mjuk borttagen instans med namn

Använd API Management [Hämta efter namn](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) -åtgärd, ersätta `{subscriptionId}` , `{location}` och `{serviceName}` med din Azure-prenumeration, resurs plats och API Management instans namn:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

Om det är tillgängligt för borttagning returnerar Azure en post av APIM-instansen som visar dess `deletionDate` och `scheduledPurgeDate` , till exempel:

```json
{
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ApiManagement/locations/southcentralus/deletedservices/apimtest",
    "name": "apimtest",
    "type": "Microsoft.ApiManagement/deletedservices",
    "location": "South Central US",
    "properties": {
        "serviceId": "/subscriptions/########-####-####-####-############/resourceGroups/apimtestgroup/providers/Microsoft.ApiManagement/service/apimtest",
        "scheduledPurgeDate": "2020-11-26T19:40:26.3596893Z",
        "deletionDate": "2020-11-24T19:40:50.1013572Z"
    }
}
```

### <a name="list-all-soft-deleted-instances-for-a-given-subscription"></a>Lista alla mjuka borttagna instanser för en specifik prenumeration

Använd prenumerations åtgärden API Managements [lista efter prenumeration](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) och Ersätt `{subscriptionId}` med ditt prenumerations-ID:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/deletedservices?api-version=2020-06-01-preview
```

Om du gör det returneras en lista över alla borttagnings bara tjänster som är tillgängliga för borttagning under den aktuella prenumerationen `deletionDate` `scheduledPurgeDate` .

## <a name="recover-a-deleted-apim-instance"></a>Återställa en borttagen APIM-instans

Använd API Management [skapa eller uppdatera](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) åtgärd, ersätta `{subscriptionId}` , `{resourceGroup}` och `{apimServiceName}` med din Azure-prenumeration, resurs grupp namn och API Management namn:

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ApiManagement/service/{apimServiceName}?api-version=2020-06-01-preview
```

. . . och ange `restore` egenskapen som `true` i begär ande texten. (När den här flaggan har angetts och angetts till *True* ignoreras alla andra egenskaper.) Exempel:

```json
{
  "properties": {
    "publisherEmail": "help@contoso.com",
    "publisherName": "Contoso",
    "restore": true
  },
  "sku": {
    "name": "Developer",
    "capacity": 1
  },
  "location": "South Central US"
}
```

## <a name="purge-a-soft-deleted-apim-instance"></a>Rensa en mjuk borttagning av APIM-instansen

Använd API Management [Rensa](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) -åtgärden, ersätta `{subscriptionId}` , `{location}` och `{serviceName}` med din Azure-prenumeration, resurs plats och API Management namn:

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

Detta tar bort din API Management instans från Azure permanent.

## <a name="next-steps"></a>Nästa steg

Läs mer om långsiktig API Management säkerhets kopierings-och återställnings alternativ:

- [Så här implementerar du haveriberedskap med hjälp av säkerhetskopiering och återställning i Azure API Management](api-management-howto-disaster-recovery-backup-restore.md)