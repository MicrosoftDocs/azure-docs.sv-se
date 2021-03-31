---
title: Service Fabric Azure Resource Manager distribution guardrails
description: Den här artikeln innehåller en översikt över vanliga fel som görs när du distribuerar ett Service Fabric kluster via Azure Resource Manager och hur du undviker dem.
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: pepogors
ms.openlocfilehash: aa1499c57ead28bfcee90a2f224ef9c3bb1d7f58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86247832"
---
# <a name="service-fabric-guardrails"></a>Service Fabric guardrails 
När du distribuerar ett Service Fabric-kluster placeras guardrails på plats, vilket kommer att Miss förAzure Resource Manager distribution om en ogiltig kluster konfiguration används. I följande avsnitt får du en översikt över vanliga problem med kluster konfiguration och de steg som krävs för att minimera problemen. 

## <a name="durability-mismatch"></a>Hållbarhets konflikt
### <a name="overview"></a>Översikt
Hållbarhets värdet för en Service Fabric nodtyp definieras i två olika avsnitt i en Azure Resource Manager mall. Avsnittet tillägg för skalnings uppsättning för virtuell dator i den virtuella datorns skalnings uppsättnings resurs och i avsnittet nodtyp i Service Fabric kluster resursen. Det är ett krav att hållbarhets värdet i dessa avsnitt matchar, annars kommer resurs distributionen att Miss förväntas.

Följande avsnitt innehåller ett exempel på en hållbarhets konflikt mellan inställningen hållbarhet för skalnings uppsättning för virtuell dator och inställningen för hållbarhets inställningen Service Fabric nodtyp:  

**Hållbarhets inställning för skalnings uppsättning för virtuell dator**
```json 
{
  "extensions": [
    {
      "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
      "properties": {
        "type": "ServiceFabricNode",
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
          "durabilityLevel": "Silver",
        }
      }
    }
  ]
}
```

**Inställning av hållbarhet för Service Fabric nodtyp** 
```json
{
  "nodeTypes": [
    {
      "name": "[variables('vmNodeType0Name')]",
      "durabilityLevel": "Bronze",
      "isPrimary": true,
      "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    }
  ]
}
```

### <a name="error-messages"></a>Felmeddelanden
* Den virtuella datorns skalnings uppsättnings matchnings fel stämmer inte överens med den aktuella Service Fabrics hållbarhets nivå
* Hållbarhet för skalnings uppsättningen för den virtuella datorn matchar inte mål Service Fabrics hållbarhets nivå
* Hållbarhet för skalnings uppsättning för virtuell dator matchar den aktuella Service Fabric hållbarhets nivå eller mål Service Fabrics hållbarhets nivå 

### <a name="mitigation"></a>Åtgärd
Så här åtgärdar du en hållbarhets konflikt som anges av ovanstående fel meddelanden:
1. Uppdatera hållbarhets nivån i den virtuella datorns skal uppsättnings tillägg eller Service Fabric nodtyp i Azure Resource Manager-mallen för att säkerställa att värdena stämmer överens.
2. Distribuera om Azure Resource Manager-mallen med de uppdaterade värdena.


## <a name="seed-node-deletion"></a>Borttagning av Seed-nod 
### <a name="overview"></a>Översikt
Ett Service Fabric-kluster har en egenskap för [Tillförlitlighets nivå](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) som används för att fastställa antalet repliker av system tjänster som körs på den primära nodtypen i klustret. Antalet nödvändiga repliker avgör det minsta antalet noder som måste underhållas i klustrets primära nodtyp. Om antalet noder i den primära nodtypen går under det minimi krav som krävs för Tillförlitlighets nivån blir klustret instabilt.  

### <a name="error-messages"></a>Felmeddelanden 
Borttagnings åtgärden för startnoden har identifierats och kommer att avvisas. 
* Den här åtgärden skulle leda {0} till att endast potentiella dirigeringsroutrar är kvar i klustret, och {1} det krävs minst.
* Om du tar bort {0} Seed-noderna från {1} kan det leda till att klustret stängs på grund av förlust av kvorum för Seed-nod. Maximalt antal startnoder som kan tas bort i taget är {2} .
 
### <a name="mitigation"></a>Åtgärd 
Kontrol lera att den primära nodtypen har tillräckligt med Virtual Machines för den tillförlitlighet som anges i klustret. Du kommer inte att kunna ta bort en virtuell dator om den här skalnings uppsättningen för den virtuella datorn ska få lägre än det minsta antalet noder för den angivna Tillförlitlighets nivån.
* Om Tillförlitlighets nivån är korrekt angiven kontrollerar du att det finns tillräckligt många noder i den primära nodtypen som behövs för Tillförlitlighets nivån. 
* Om Tillförlitlighets nivån är felaktig initierar du en ändring på Service Fabric resurs för att sänka Tillförlitlighets nivån först innan du påbörjar alla åtgärder för skalnings uppsättningar för virtuella datorer och väntar tills den har slutförts.
* Om Tillförlitlighets nivån är brons följer du dessa [steg](./service-fabric-cluster-scale-in-out.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set) för att skala i klustret på ett smidigt sätt.

## <a name="next-steps"></a>Nästa steg
* Skapa ett kluster på virtuella datorer eller datorer som kör Windows Server: [Service Fabric skapa kluster för Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Skapa ett kluster på virtuella datorer eller datorer som kör Linux: [skapa ett Linux-kluster](service-fabric-cluster-creation-via-portal.md)
* Felsöka Service Fabric: [fel söknings guider](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
