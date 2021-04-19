---
title: Referenstabell för alla Azure Security Center rekommendationer
description: Den här artikeln Azure Security Center säkerhetsrekommendationer som hjälper dig att härda och skydda dina resurser.
author: memildin
ms.service: security-center
ms.topic: reference
ms.date: 04/06/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: e994aead1840fd3ef9b57e92cf95e94837608d7a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719141"
---
# <a name="security-recommendations---a-reference-guide"></a>Säkerhetsrekommendationer – en referensguide

Den här artikeln innehåller de rekommendationer som du kan se Azure Security Center. Rekommendationerna som visas i din miljö beror på vilka resurser du skyddar och din anpassade konfiguration.

Security Center rekommendationerna baseras på [Azure Security Benchmark.](https://docs.microsoft.com/security/benchmark/azure/introduction)
Azure Security Benchmark är Microsofts microsoft-specifika uppsättning riktlinjer för bästa praxis för säkerhet och efterlevnad baserat på vanliga ramverk för efterlevnad. Det här respekterade benchmark-måttet bygger på kontrollerna från [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) och National Institute of Standards and Technology [(NIST)](https://www.nist.gov/) med fokus på molncentrerad säkerhet.

Mer information om hur du svarar på dessa rekommendationer finns [i Åtgärda rekommendationer i Azure Security Center](security-center-remediate-recommendations.md).

Din säkerhetspoäng baseras på antalet rekommendationer Security Center du har slutfört. När du ska avgöra vilka rekommendationer du ska lösa först kan du titta på allvarlighetsgraden för var och en och dess potentiella inverkan på dina säkerhetspoäng.

> [!TIP]
> Om en rekommendations beskrivning säger "Ingen relaterad princip" beror det vanligtvis på att rekommendationen är beroende av en annan rekommendation och _dess_ princip. Rekommendationen "Hälsofel för slutpunktsskydd bör åtgärdas..." förlitar sig till exempel på rekommendationen som kontrollerar om en Endpoint Protection-lösning ens har installerats _("Endpoint_ Protection-lösningen ska installeras..."). Den underliggande _rekommendationen_ har en princip.
> Genom att begränsa principerna till endast den grundläggande rekommendationen förenklas principhanteringen.

## <a name="appservices-recommendations"></a><a name='recs-appservices'></a>AppServices-rekommendationer

[!INCLUDE [asc-recs-appservices](../../includes/asc-recs-appservices.md)]

## <a name="compute-recommendations"></a><a name='recs-compute'></a>Compute-rekommendationer

[!INCLUDE [asc-recs-compute](../../includes/asc-recs-compute.md)]

## <a name="container-recommendations"></a><a name='recs-container'></a>Rekommendationer för containers

[!INCLUDE [asc-recs-container](../../includes/asc-recs-container.md)]

## <a name="data-recommendations"></a><a name='recs-data'></a>Datarekommendationer

[!INCLUDE [asc-recs-data](../../includes/asc-recs-data.md)]

## <a name="identityandaccess-recommendations"></a><a name='recs-identityandaccess'></a>IdentityAndAccess-rekommendationer

[!INCLUDE [asc-recs-identityandaccess](../../includes/asc-recs-identityandaccess.md)]

## <a name="iot-recommendations"></a><a name='recs-iot'></a>IoT-rekommendationer

[!INCLUDE [asc-recs-iot](../../includes/asc-recs-iot.md)]

## <a name="networking-recommendations"></a><a name='recs-networking'></a>Nätverksrekommendationer

[!INCLUDE [asc-recs-networking](../../includes/asc-recs-networking.md)]

## <a name="deprecated-recommendations"></a>Inaktuella rekommendationer

|Rekommendation|Beskrivning & relaterad princip|Allvarlighetsgrad|
|----|----|----|
|Åtkomst till App Services bör begränsas|Begränsa åtkomsten till App Services genom att ändra nätverkskonfigurationen till att neka inkommande trafik från intervall som är för breda.<br>(Relaterad princip: [Förhandsversion]: Åtkomst till App Services bör begränsas)|Högt|
|Reglerna för webbprogram på IaaS-NSG:er bör härdas|Härda nätverkssäkerhetsgruppen (NSG) för dina virtuella datorer som kör webbprogram med NSG-regler som är övertillåtna när det gäller webbaserade programportar.<br>(Relaterad princip: NSG:erna för webbprogram på IaaS bör härdas)|Högt|
|Säkerhetsprinciper för poddar bör definieras för att minska attackvektorn genom att ta bort onödiga programprivilegier (förhandsversion)|Definiera poddsäkerhetsprinciper för att minska angreppsvektorn genom att ta bort onödiga programprivilegier. Vi rekommenderar att du konfigurerar poddsäkerhetsprinciper så att poddar endast kan komma åt resurser som de har åtkomst till.<br>(Relaterad princip: [Förhandsversion]: Pod Security Policies bör definieras på Kubernetes Services)|Medel|
|Installera Azure Security Center för IoT-säkerhetsmodulen för att få mer insyn i dina IoT-enheter|Installera Azure Security Center för IoT-säkerhetsmodulen för att få mer insyn i dina IoT-enheter.|Låg|
|Datorerna bör startas om för att tillämpa systemuppdateringar|Starta om datorerna för att tillämpa systemuppdateringarna och skydda datorn mot sårbarheter. (Relaterad princip: Systemuppdateringar ska installeras på dina datorer)|Medel|
|Övervakningsagenten ska installeras på dina datorer|Den här åtgärden installerar en övervakningsagent på de valda virtuella datorerna. Välj en arbetsyta som agenten ska rapportera till. (Ingen relaterad princip)|Högt|
||||

## <a name="next-steps"></a>Nästa steg

Mer information om rekommendationer finns i följande:

- [Vad är säkerhetsprinciper, initiativ och rekommendationer?](security-policy-concept.md)
- [Granska dina säkerhetsrekommendationer](security-center-recommendations.md)
