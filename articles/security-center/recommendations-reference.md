---
title: Referens tabell för alla Azure Security Center rekommendationer
description: Den här artikeln innehåller Azure Security Center säkerhets rekommendationer som hjälper dig att skydda dina resurser.
author: memildin
ms.service: security-center
ms.topic: reference
ms.date: 01/24/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: cd72e904f659b7bd9c7e2df86b46fe20bd26ec33
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98757105"
---
# <a name="security-recommendations---a-reference-guide"></a>Säkerhetsrekommendationer – en referensguide

Den här artikeln innehåller de rekommendationer som du kan se i Azure Security Center. Rekommendationerna som visas i din miljö beror på vilka resurser du skyddar och din anpassade konfiguration.

Security Centers rekommendationer baseras på [Azures säkerhets benchmark](../security/benchmarks/introduction.md). Azures säkerhets prestanda är Microsofts-skapade, Azure-/regionsspecifika uppsättning rikt linjer för säkerhets-och efterlevnads metod tips baserade på vanliga ramverk för efterlevnad. Detta respekterade riktmärken bygger på kontrollerna från [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) och [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) med fokus på Cloud-inriktad säkerhet.

Information om hur du svarar på dessa rekommendationer finns i [åtgärda rekommendationer i Azure Security Center](security-center-remediate-recommendations.md).

Dina säkra Poäng baseras på antalet Security Center rekommendationer som du har slutfört. För att bestämma vilka rekommendationer som ska lösas först kan du titta på allvarlighets graden för var och en av de potentiella effekterna på dina säkra poäng.

> [!TIP]
> Om beskrivningen i rekommendationen säger "ingen relaterad princip" är det vanligt vis på grund av att rekommendationen är beroende av en annan rekommendation och _dess_ princip. Rekommendationen "slut punkts skydd bör därför inte åtgärdas...", förlitar sig på rekommendationen som kontrollerar om en Endpoint Protection-lösning är till och med _installerad_ ("Endpoint Protection-lösning ska installeras..."). Den _underliggande rekommendationen har en_ princip.
> Att begränsa principerna till enbart den grundläggande rekommendationen fören klar princip hanteringen.

## <a name="compute-recommendations"></a><a name='recs-compute'></a>Beräknings rekommendationer

[!INCLUDE [asc-recs-compute](../../includes/asc-recs-compute.md)]

## <a name="data-recommendations"></a><a name='recs-data'></a>Data rekommendationer

[!INCLUDE [asc-recs-data](../../includes/asc-recs-data.md)]

## <a name="identityandaccess-recommendations"></a><a name='recs-identityandaccess'></a>IdentityAndAccess-rekommendationer

[!INCLUDE [asc-recs-identityandaccess](../../includes/asc-recs-identityandaccess.md)]

## <a name="networking-recommendations"></a><a name='recs-networking'></a>Nätverksrekommendationer

[!INCLUDE [asc-recs-networking](../../includes/asc-recs-networking.md)]

## <a name="deprecated-recommendations"></a>Föråldrade rekommendationer

|Rekommendation|Beskrivning & relaterad princip|Allvarlighetsgrad|Snabb korrigering aktive rad? ([Läs mer](security-center-remediate-recommendations.md#quick-fix-remediation))|Resurstyp|
|----|----|----|----|----|
|**Åtkomst till App Services bör vara begränsad**|Begränsa åtkomsten till din App Services genom att ändra nätverks konfigurationen för att neka inkommande trafik från intervall som är för breda.<br>(Relaterad princip: [för hands version]: åtkomst till App Services ska vara begränsad)|Högt|N|App Service|
|**Reglerna för webb program på IaaS NSG: er bör vara härdade**|Skärp nätverks säkerhets gruppen (NSG) för dina virtuella datorer som kör webb program, med NSG-regler som kan tillåtas med avseende på webb program portar.<br>(Relaterad princip: NSG: er-reglerna för webb program på IaaS bör vara härdade)|Högt|N|Virtuell dator|
|**Pod säkerhets principer bör definieras för att minska angrepps vektorn genom att ta bort onödiga program behörigheter (förhands granskning)**|Definiera Pod säkerhets principer för att minska angrepps vektorn genom att ta bort onödiga program privilegier. Vi rekommenderar att du konfigurerar Pod säkerhets principer så att poddar endast kan komma åt resurser som de har åtkomst till.<br>(Relaterad princip: [för hands version]: Pod säkerhets principer bör definieras på Kubernetes-tjänster)|Medium|N|Beräknings resurser (behållare)|
|**Installera Azure Security Center för IoT-säkerhetsmodulen för att få mer insyn i IoT-enheterna**|Installera Azure Security Center för IoT-säkerhetsmodulen för att få mer insyn i IoT-enheterna.|Låg|N|IoT-enhet|

## <a name="next-steps"></a>Nästa steg

Mer information om rekommendationer finns i följande avsnitt:

- [Säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md)
- [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)
