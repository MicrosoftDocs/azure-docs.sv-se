---
title: Service Fabric hanterade kluster (förhands granskning)
description: Service Fabric hanterade kluster är en utveckling av resurs modellen för Azure Service Fabric-kluster som effektiviserar distribution och kluster hantering.
ms.topic: overview
ms.date: 02/15/2021
ms.openlocfilehash: 271852214097ee96ba6b10de7a94904981cd8ef8
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041237"
---
# <a name="service-fabric-managed-clusters-preview"></a>Service Fabric hanterade kluster (förhands granskning)

Service Fabric hanterade kluster är en utveckling av resurs modellen för Azure Service Fabric-kluster som effektiviserar distributionen och kluster hanterings upplevelsen.

Med mallen för Azure Resource Model (ARM) för traditionella Service Fabric-kluster måste du definiera en kluster resurs tillsammans med ett antal stöd resurser, som alla måste vara "kabelanslutna" (vid distribution och under hela livs cykeln för klustret) för att klustret och dina tjänster ska fungera korrekt. Inkapslings modellen för Service Fabric hanterade kluster består däremot av en enda *Service Fabric hanterad kluster* resurs. Alla underliggande resurser för klustret sammanställs och hanteras av Azure för din räkning.

**Service Fabric traditionell kluster modell** 
 ![ Service Fabric traditionell kluster modell][sf-composition]

**Service Fabric hanterad kluster modell** 
 ![ Service Fabric inkapslad kluster modell][sf-encapsulation]

När det gäller storlek och komplexitet är ARM-mallen för ett Service Fabric hanterat kluster cirka 100 rader med JSON och vissa 1000-rader som krävs för att definiera ett typiskt Service Fabric-kluster:

| Service Fabric-resurser | Service Fabric hanterade kluster resurser |
|----------|-----------|
| Service Fabric-kluster | Service Fabric hanterat kluster |
| Skal uppsättning för virtuell dator (er) | |
| Lastbalanserare | |
| Offentlig IP-adress | |
| Lagringskonto(n) | |
| Virtuellt nätverk | |

Service Fabric hanterade kluster ger ett antal fördelar jämfört med traditionella kluster:

**Förenklad kluster distribution och hantering**
- Distribuera och hantera en enda Azure-resurs
- Certifikat hantering och autorotation
- Förenklade skalnings åtgärder

**Förhindra drifts fel**
- Förhindra konfiguration av matchnings fel med underliggande resurser
- Blockera osäkra åtgärder (till exempel att ta bort en Seed-nod)

**Bästa praxis som standard**
- Förenklade inställningar för tillförlitlighet och hållbarhet

Det kostar inget extra att Service Fabric hanterade kluster utöver kostnaden för de underliggande resurser som krävs för klustret.

## <a name="service-fabric-managed-cluster-skus"></a>Service Fabric hanterade kluster SKU: er

Service Fabric hanterade kluster finns i både Basic-och standard-SKU: er.

| Funktion | Basic | Standard |
| ------- | ----- | -------- |
| Nätverks resurs (SKU för [Load Balancer](../load-balancer/skus.md), [offentlig IP](../virtual-network/public-ip-addresses.md)) | Basic | Standard |
| Antal för min nod (VM-instans) | 3 | 5 |
| Maximalt antal noder per nodtyp | 100 | 100 |
| Maximalt antal nodtyper | 1 | 20 |
| Lägga till/ta bort nodtyper | Inga | Ja |
| Zonredundans | Inga | Ja |

## <a name="whats-new-for-service-fabric-managed-clusters"></a>Vad är nytt för Service Fabric hanterade kluster

De senaste funktionerna för för hands versionen av Service Fabric hanterade kluster inkluderar stöd för:

* [Distribuera program med ARM-mallar](how-to-managed-cluster-app-deployment-template.md)
* [Automatiska uppgraderingar av operativsystem](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades)
* [Disk kryptering](how-to-enable-managed-cluster-disk-encryption.md)
* [Tillämpa NSG-regler](how-to-managed-cluster-networking.md)

Funktioner som ska läggas till i kommande versioner är:

* Distribuera program med Visual Studio
* Stöd för hanterade identiteter
* Tillgänglighetszoner
* Omvänd proxy
* Automatisk skalning

## <a name="next-steps"></a>Nästa steg

Kom igång med Service Fabric hanterade kluster genom att försöka med snabb starten:

> [!div class="nextstepaction"]
> [Skapa ett Service Fabric hanterat kluster (förhands granskning)](quickstart-managed-cluster-template.md)


[sf-composition]: ./media/overview-managed-cluster/sfrp-composition-resource.png
[sf-encapsulation]: ./media/overview-managed-cluster/sfrp-encapsulated-resource.png