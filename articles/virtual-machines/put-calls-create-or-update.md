---
title: PUT-anrop för åtgärder för att skapa eller uppdatera
description: SKICKA anrop för att skapa eller uppdatera åtgärder på beräknings resurser
author: mimckitt
ms.author: mimckitt
ms.reviewer: cynthn
ms.topic: conceptual
ms.service: virtual-machines
ms.date: 08/4/2020
ms.custom: avverma
ms.openlocfilehash: d6ee4179dce905d637e933743ade7452a2484077
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91978568"
---
# <a name="put-calls-for-creation-or-updates-on-compute-resources"></a>Lägg till anrop för att skapa eller uppdateringar på beräknings resurser

`Microsoft.Compute` resurserna stöder inte den konventionella definitionen av *http-placering* av semantik. Dessa resurser använder i stället PATCH-semantik för både skicka-och PATCH-verben.

**Skapa** -åtgärder tillämpar standardvärden när det är lämpligt. Resurs **uppdateringar** som gjorts genom placering eller korrigerings fil gäller dock inte för några standard egenskaper. Använd strikt KORRIGERINGs semantik för **uppdaterings** åtgärder.

Till exempel `caching` är disk egenskapen för en virtuell dator standardvärdet `ReadWrite` om resursen är en OS-disk.

```json
    "storageProfile": {
      "osDisk": {
        "name": "myVMosdisk",
        "image": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/{existing-generalized-os-image-blob-name}.vhd"
        },
        "osType": "Windows",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "vhd": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/myDisk.vhd"
        }
      }
    },
```

Men för **uppdaterings** åtgärder när en egenskap lämnas ut eller ett *Null* -värde skickas förblir det oförändrat och det finns inga standardvärden.

Detta är viktigt när du skickar uppdaterings åtgärder till en resurs med avsikt att ta bort en Association. Om resursen är en `Microsoft.Compute` resurs måste motsvarande egenskap som du vill ta bort uttryckligen anropas och tilldelas ett värde. För att uppnå detta kan användare skicka en tom sträng, till exempel **""**. Detta instruerar plattformen att ta bort den associationen.

> [!IMPORTANT]
> Det finns inget stöd för "korrigering" av ett mat ris element. Klienten måste i stället göra en begäran om att skicka eller uppdatera med hela innehållet i den uppdaterade matrisen. Om du till exempel vill koppla från en datadisk från en virtuell dator gör du en GET-begäran för att hämta den aktuella VM-modellen, tar bort disken som ska kopplas från `properties.storageProfile.dataDisks` och gör en begäran med den uppdaterade VM-entiteten.

## <a name="examples"></a>Exempel

### <a name="correct-payload-to-remove-a-proximity-placement-groups-association"></a>Korrigera nytto last för att ta bort en närhet av placerings grupper

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": "" } }
`

### <a name="incorrect-payloads-to-remove-a-proximity-placement-groups-association"></a>Felaktiga nytto laster för att ta bort en närhet av placerings grupper

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": null } }
`

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20 } }
`

## <a name="next-steps"></a>Nästa steg
Läs mer om att skapa eller uppdatera samtal för [Virtual Machines](/rest/api/compute/virtualmachines/createorupdate) och [Virtual Machine Scale Sets](/rest/api/compute/virtualmachinescalesets/createorupdate)