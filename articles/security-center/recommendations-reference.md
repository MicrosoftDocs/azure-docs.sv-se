---
title: Referens tabell för alla Azure Security Center rekommendationer
description: Den här artikeln innehåller Azure Security Center säkerhets rekommendationer som hjälper dig att skärpa och skydda dina resurser.
author: memildin
ms.service: security-center
ms.topic: reference
ms.date: 04/06/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: a44a5e4e715238c42e51e65dfe16d4f70dfcf54d
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504828"
---
# <a name="security-recommendations---a-reference-guide"></a>Säkerhetsrekommendationer – en referensguide

Den här artikeln innehåller de rekommendationer som du kan se i Azure Security Center. Rekommendationerna som visas i din miljö beror på vilka resurser du skyddar och din anpassade konfiguration.

Security Centers rekommendationer baseras på [Azures säkerhets benchmark](../security/benchmarks/introduction.md). Azures säkerhets prestanda är Microsofts-skapade, Azure-/regionsspecifika uppsättning rikt linjer för säkerhets-och efterlevnads metod tips baserade på vanliga ramverk för efterlevnad. Detta respekterade riktmärken bygger på kontrollerna från [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) och [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) med fokus på Cloud-inriktad säkerhet.

Information om hur du svarar på dessa rekommendationer finns i [åtgärda rekommendationer i Azure Security Center](security-center-remediate-recommendations.md).

Dina säkra Poäng baseras på antalet Security Center rekommendationer som du har slutfört. För att bestämma vilka rekommendationer som ska lösas först kan du titta på allvarlighets graden för var och en av de potentiella effekterna på dina säkra poäng.

> [!TIP]
> Om beskrivningen i rekommendationen säger "ingen relaterad princip" är det vanligt vis på grund av att rekommendationen är beroende av en annan rekommendation och _dess_ princip. Rekommendationen "slut punkts skydd bör därför inte åtgärdas...", förlitar sig på rekommendationen som kontrollerar om en Endpoint Protection-lösning är till och med _installerad_ ("Endpoint Protection-lösning ska installeras..."). Den _underliggande rekommendationen har en_ princip.
> Att begränsa principerna till enbart den grundläggande rekommendationen fören klar princip hanteringen.

## <a name="appservices-recommendations"></a><a name='recs-appservices'></a>AppServices-rekommendationer

[!INCLUDE [asc-recs-appservices](../../includes/asc-recs-appservices.md)]

## <a name="compute-recommendations"></a><a name='recs-compute'></a>Beräknings rekommendationer

[!INCLUDE [asc-recs-compute](../../includes/asc-recs-compute.md)]

## <a name="container-recommendations"></a><a name='recs-container'></a>Rekommendationer för containers

[!INCLUDE [asc-recs-container](../../includes/asc-recs-container.md)]

## <a name="data-recommendations"></a><a name='recs-data'></a>Data rekommendationer

[!INCLUDE [asc-recs-data](../../includes/asc-recs-data.md)]

## <a name="identityandaccess-recommendations"></a><a name='recs-identityandaccess'></a>IdentityAndAccess-rekommendationer

[!INCLUDE [asc-recs-identityandaccess](../../includes/asc-recs-identityandaccess.md)]

## <a name="iot-recommendations"></a><a name='recs-iot'></a>IoT-rekommendationer

[!INCLUDE [asc-recs-iot](../../includes/asc-recs-iot.md)]

## <a name="networking-recommendations"></a><a name='recs-networking'></a>Nätverksrekommendationer

[!INCLUDE [asc-recs-networking](../../includes/asc-recs-networking.md)]

## <a name="deprecated-recommendations"></a>Föråldrade rekommendationer

|Rekommendation|Beskrivning & relaterad princip|Allvarlighetsgrad|
|----|----|----|
|Åtkomst till App Services bör vara begränsad|Begränsa åtkomsten till din App Services genom att ändra nätverks konfigurationen för att neka inkommande trafik från intervall som är för breda.<br>(Relaterad princip: [för hands version]: åtkomst till App Services ska vara begränsad)|Högt|
|Reglerna för webb program på IaaS NSG: er bör vara härdade|Skärp nätverks säkerhets gruppen (NSG) för dina virtuella datorer som kör webb program, med NSG-regler som kan tillåtas för webb program portar.<br>(Relaterad princip: NSG: er-reglerna för webb program på IaaS bör vara härdade)|Högt|
|Pod säkerhets principer bör definieras för att minska angrepps vektorn genom att ta bort onödiga program behörigheter (förhands granskning)|Definiera Pod säkerhets principer för att minska angrepps vektorn genom att ta bort onödiga program privilegier. Vi rekommenderar att du konfigurerar Pod säkerhets principer så att poddar endast kan komma åt resurser som de har åtkomst till.<br>(Relaterad princip: [för hands version]: Pod säkerhets principer bör definieras på Kubernetes-tjänster)|Medel|
|Installera Azure Security Center för IoT-säkerhetsmodulen för att få mer insyn i IoT-enheterna|Installera Azure Security Center för IoT-säkerhetsmodulen för att få mer insyn i IoT-enheterna.|Låg|
|Datorerna måste startas om för att tillämpa system uppdateringar|Starta om datorerna för att tillämpa system uppdateringar och skydda datorn från sårbarheter. (Relaterad princip: system uppdateringar bör installeras på dina datorer)|Medel|
|Övervaknings agenten ska installeras på dina datorer|Den här åtgärden installerar en övervaknings agent på de valda virtuella datorerna. Välj en arbets yta som agenten ska rapportera till. (Ingen relaterad princip)|Högt|
||||

## <a name="next-steps"></a>Nästa steg

Mer information om rekommendationer finns i följande avsnitt:

- [Vad är säkerhets principer, initiativ och rekommendationer?](security-policy-concept.md)
- [Granska dina säkerhets rekommendationer](security-center-recommendations.md)
