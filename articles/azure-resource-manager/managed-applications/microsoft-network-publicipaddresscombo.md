---
title: PublicIpAddressCombo-GRÄNSSNITTs element
description: Beskriver elementet Microsoft. Network. PublicIpAddressCombo UI för Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 5def6db9d551b3882204c9f997f164a0df7ac223
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87063293"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>GRÄNSSNITTs element för Microsoft. Network. PublicIpAddressCombo

En grupp kontroller för att välja en ny eller befintlig offentlig IP-adress.

## <a name="ui-sample"></a>UI-exempel

![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft-network-publicipaddresscombo.png)

- Om användaren väljer Ingen för offentlig IP-adress är text rutan domän namns etikett dold.
- Om användaren väljer en befintlig offentlig IP-adress är text rutan domän namns etikett inaktive rad. Dess värde är domän namns etiketten för den valda IP-adressen.
- Domän namnets suffix (till exempel westus.cloudapp.azure.com) uppdateras automatiskt baserat på den valda platsen.

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Network.PublicIpAddressCombo",
  "label": {
    "publicIpAddress": "Public IP address",
    "domainNameLabel": "Domain name label"
  },
  "toolTip": {
    "publicIpAddress": "",
    "domainNameLabel": ""
  },
  "defaultValue": {
    "publicIpAddressName": "ip01",
    "domainNameLabel": "mydomain"
  },
  "constraints": {
    "required": {
      "domainNameLabel": true
    }
  },
  "options": {
    "hideNone": false,
    "hideDomainNameLabel": false,
    "hideExisting": false,
    "zone": 3
  },
  "visible": true
}
```

## <a name="sample-output"></a>Exempelutdata

Om användaren väljer ingen offentlig IP-adress, returnerar kontrollen följande utdata:

```json
{
  "newOrExistingOrNone": "none"
}
```

Om användaren väljer en ny eller befintlig IP-adress returnerar kontrollen följande utdata:

```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "mydomain",
  "publicIPAllocationMethod": "Dynamic",
  "sku": "Basic",
  "newOrExistingOrNone": "new"
}
```

- När `options.hideNone` har angetts som **Sant** får `newOrExistingOrNone` bara värdet **New** eller **existing**.
- När `options.hideDomainNameLabel` har angetts som **Sant** `domainNameLabel` är odeklarerat.

## <a name="remarks"></a>Kommentarer

- Om `constraints.required.domainNameLabel` är inställt på **Sant** måste användaren ange en domän namns etikett när de skapar en ny offentlig IP-adress. Befintliga offentliga IP-adresser utan en etikett kan inte väljas.
- Om `options.hideNone` är inställt på **Sant** döljs alternativet för att välja **ingen** för den offentliga IP-adressen. Standardvärdet är **falskt**.
- Om `options.hideDomainNameLabel` är inställt på **Sant** döljs text rutan för domän namns etiketten. Standardvärdet är **falskt**.
- Om `options.hideExisting` är sant kan användaren inte välja en befintlig offentlig IP-adress. Standardvärdet är **falskt**.
- För är `zone` det bara offentliga IP-adresser för den angivna zonen eller zonens elastiska offentliga IP-adresser tillgängliga.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
