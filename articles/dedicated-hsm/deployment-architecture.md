---
title: Distributions arkitektur – Azure Dedicated HSM | Microsoft Docs
description: Grundläggande design aspekter när du använder Azure Dedicated HSM som en del av en program arkitektur
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/05/2020
ms.author: mbaldwin
ms.openlocfilehash: d0989c31611b2f42c0219324fa517adc5c216c6c
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586613"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Distributionsarkitektur för Azure Dedicated HSM

Azure Dedicated HSM tillhandahåller kryptografisk nyckel lagring i Azure. Den uppfyller strängare säkerhets krav. Kunderna drar nytta av att använda Azures dedikerad HSM om de:

* Måste uppfylla FIPS 140-2 nivå 3-certifiering
* Kräv att de har exklusiv åtkomst till HSM
* bör ha fullständig kontroll över sina enheter

HSM: er distribueras mellan Microsofts Data Center och kan enkelt etableras som ett enhets par som grund för en lösning med hög tillgänglighet. De kan också distribueras i olika regioner för en katastrof flexibel lösning. Regionerna med dedikerad HSM är för närvarande:

* East US
* USA, östra 2
* USA, västra
* USA, södra centrala
* Sydostasien
* Asien, östra
* Indien, centrala
* Södra Indien
* Japan, östra
* Japan, västra
* Norra Europa
* Europa, västra
* Storbritannien, södra
* Storbritannien, västra
* Kanada, centrala
* Kanada, östra
* Australien, östra
* Australien, sydöstra

Var och en av dessa regioner har HSM-rack som distribueras i antingen två oberoende data Center eller minst två oberoende tillgänglighets zoner. Asien, sydöstra har tre tillgänglighets zoner och östra USA 2 har två. Det finns totalt åtta regioner i Europa, Asien och USA som erbjuder dedikerad HSM-tjänst. Mer information om Azure-regioner finns i informationen om officiella  [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/).
Vissa design faktorer för alla dedikerade HSM-baserade lösningar är plats/latens, hög tillgänglighet och stöd för andra distribuerade program.

## <a name="device-location"></a>Enhetsplats

Den optimala HSM-enhetens plats är nära nära de program som utför kryptografiska åtgärder. Svars tiden i regionen förväntas vara ensiffriga millisekunder. Svars tiden mellan regioner kan vara 5 till 10 gånger högre än detta.

## <a name="high-availability"></a>Hög tillgänglighet

För att uppnå hög tillgänglighet måste kunden använda två HSM-enheter i en region som har kon figurer ATS med Gemalto-programvara som ett högt tillgänglighets par. Den här typen av distribution garanterar tillgängligheten för nycklar om en enskild enhet upplever ett problem som hindrar den från att bearbeta viktiga åtgärder. Det minskar också riskerna när du utför avbrott/åtgärds underhåll, till exempel utbyte av strömförsörjnings material. Det är viktigt att du har en design för att kunna hantera alla typer av regionala nivå problem. Regional nivå problem kan inträffa när det finns natur katastrofer som orkaner, översvämningar eller jord bävningar. Dessa typer av händelser bör begränsas genom att tillhandahålla HSM-enheter i en annan region. Enheter som distribueras i en annan region kan kopplas samman via Gemalto program varu konfiguration. Det innebär att den minsta distributionen för en flexibel lösning med hög tillgänglighet och katastrof är fyra HSM-enheter över två regioner. Lokal redundans och redundans över regioner kan användas som en bas linje för att lägga till ytterligare HSM-enhets distributioner för att stödja latens, kapacitet eller uppfylla andra programspecifika krav.

## <a name="distributed-application-support"></a>Stöd för distribuerade program

Dedikerade HSM-enheter distribueras vanligt vis till stöd för program som behöver utföra nyckel lagring och hämtning av nycklar. Dedikerade HSM-enheter har 10 partitioner för oberoende program support. Enhetens plats bör baseras på en holistisk vy över alla program som behöver använda tjänsten.

## <a name="next-steps"></a>Nästa steg

När distributions arkitekturen har fastställts kommer de flesta konfigurations aktiviteter för att implementera den arkitekturen att tillhandahållas av Gemalto. Detta inkluderar enhets konfiguration och scenarier för program integrering. Om du vill ha mer information kan du använda [Gemalto kund support](https://supportportal.gemalto.com/csm/) Portal och hämta administrations-och konfigurations guider. Microsofts partner webbplats har en rad olika integrations guider.
Vi rekommenderar att alla viktiga begrepp för tjänsten, till exempel hög tillgänglighet och säkerhet till exempel, är bra förstå före enhets etablering eller program design och distribution.
Avsnitt om ytterligare begrepps nivå:

* [Hög tillgänglighet](high-availability.md)
* [Fysisk säkerhet](physical-security.md)
* [Nätverk](networking.md)
* [Support](supportability.md)
* [Övervakning](monitoring.md)
