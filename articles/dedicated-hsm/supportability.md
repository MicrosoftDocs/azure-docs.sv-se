---
title: Support – Azure Dedicated HSM | Microsoft Docs
description: Support alternativ och ansvars områden för Azure Dedicated HSM i olika scenarier
services: dedicated-hsm
author: johndaw
manager: rkarlin
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seodec18
ms.date: 03/27/2019
ms.author: mbaldwin
ms.openlocfilehash: d83d688707baf6098d63dfde9b4181eb04fb9729
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "70881015"
---
# <a name="azure-dedicated-hsm-supportability"></a>Stöd för Azure Dedicated HSM

Azure Dedicated HSM-tjänsten tillhandahåller en fysisk enhet för den enda kund användningen med fullständig administrativ kontroll och hanterings ansvar. Den tillgängliga enheten är en [Gemalto SafeNet Luna 7 HSM-modell A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Microsoft kommer inte att ha någon administrativ åtkomst efter att ha tillhandahållit en kund, utöver den fysiska seriella port bilagan som en övervaknings roll.  Utan åtkomst kan Microsoft inte ha några kontinuerliga underhålls-eller system administrations ansvar på program nivå. Därför är kunderna ansvariga för vanliga operativa aktiviteter.
Kunder är fullt ansvariga för program som använder HSM: er och bör arbeta med Gemalto för support eller konsultbaserade hjälp åtgärder. På grund av omfattningen av kundens ägande av drifts hygien är det inte möjligt för Microsoft att erbjuda någon typ av garanti för hög tillgänglighet för den här tjänsten. Det är kundens ansvar att se till att deras program är korrekt konfigurerade för att uppnå hög tillgänglighet. Microsoft övervakar och underhåller enhetens hälso tillstånd och nätverks anslutning.

## <a name="getting-support"></a>Få support

Kund support för dedikerad HSM är en gemensam insats mellan Microsoft och Gemalto. Eventuella maskin varu problem eller problem med nätverks Sök vägar kommer att åtgärdas av Microsoft, och allt som du kan göra med den verkliga HSM, till exempel konfiguration, program vara, inbyggd program vara och program utveckling, kommer att åtgärdas av Gemalto. Den här support modellen säkerställer den snabbaste vägen till den mest effektiva supporten. Om du är tveksam med ett visst problem kan du generera en supportbegäran med Microsoft så att vi kan se till att du dirigeras på lämpligt sätt. Microsoft fortsätter att använda alla support scenarier och sträva efter att få bästa möjliga support för våra kunder.

## <a name="gemalto-support"></a>Gemalto-stöd

Kunder som använder sig av dedikerad HSM-tjänst är berättigade till support från Gemalto enligt support avtalet. Detta kräver bara en registrerings process med Gemalto-support portalen. Ett kund-ID och instruktioner kommer att tillhandahållas för detta som en del av det första ärendet med Microsoft för att få åtkomst till den dedikerade HSM-tjänsten. Mekanismen för att få support från Gemalto finns via [kund support portalen](https://supportportal.gemalto.com/csm/).
En viktig anmärkning är att Gemalto tillhandahåller all program vara och dokumentation som krävs för att använda HSM (till exempel klient åtkomst program och SDK: er) via hämtning på kund support portalen.

### <a name="software-components"></a>Program varu komponenter

Olika program varu komponenter används i konfigurationen av HSM-enheter:

* Klient program vara
* SDK
* Verktyg

### <a name="guidance"></a>Vägledning

Gemalto gör den tillgängliga administrations-och konfigurations vägledningen via [kund support portalen](https://supportportal.gemalto.com/csm/). När du har loggat in med ett giltigt kund-ID är dessa dokument tillgängliga för hämtning. Gemalto innehåller också en serie integrerings guider som hjälper kunder med olika scenarier och program varu integreringar. Mer information finns på [webbplatsen för Gemalto-partner för Microsoft](https://safenet.gemalto.com/partners/microsoft/).

### <a name="support"></a>Support

Eventuella problem med program varu nivåer eller frågor i relation till att använda HSM: er som en del av den dedikerade HSM-tjänsten, bör åtgärdas till Gemalto-stöd direkt. Alla program varu komponenter som anges ovan, och en anpassad HSM-konfiguration som är efter etablering, kommer att hanteras av Gemalto. Mer information finns på  [kund support portalen för Gemalto](https://supportportal.gemalto.com/csm/).

### <a name="consulting-services"></a>Konsulttjänster

Kontakta ditt Gemalto-konto om du vill ha hjälp med att utforma, utveckla och distribuera anpassade program som använder HSM.

## <a name="microsoft-support"></a>Microsoft Support

Microsoft ser till att fysiska HSM-enheter är tillgängliga för nätverk och i användnings tillstånd för exklusiv användning av en enskild kund. Kunderna ansvarar för konfiguration, administration och hantering av enheten. Microsoft-ansvar är:

* Kontrol lera att enheten har strömförsörjning och kylning
* Upprätthålla ett drifts tillstånd för HSM (till exempel Break/Fix-scenarier)
* Enheten är tillgänglig över nätverket.

Problem som följande bör rapporteras till Microsoft:

* Komponent haverier
* Fullständigt enhets haveri
* Problem med nätverks åtkomst
* Problem etablering och avetablering.

Microsoft har fysisk seriell port åtkomst till enheten via en övervaknings roll (det vill säga inte administrativ roll) som möjliggör grundläggande hälsotelemetri.  Detta gör att Microsoft kan tillhandahålla proaktiva meddelanden om problem hos kunden om inte kunden väljer att begränsa den här behörigheten. 

### <a name="provisioning-and-decommissioning"></a>Etablering och inaktive ring

När en kund har godkänt registreringen för den dedikerade HSM-tjänsten kan de skapa HSM-resurser (för närvarande via PowerShell eller kommando rads gränssnitt och inte Azure Portal). Resursen går igenom en tilldelnings process som mappar en fysisk enhet i en angiven region till en kunds fördefinierade virtuella nätverk (VNet). När den är synlig i ett virtuellt nätverk kan kunden komma åt enheten och konfigurera den ytterligare enligt kraven. Kunderna får åtkomst till sina dedikerade HSM: er med Gemalto-klientens program vara och verktyg. Processen för att skapa en resurs stöds av Microsoft. Anpassad konfigurations process utöver stöds av Gemalto. (se Gemalto-stöd ovan). När en kund har slutat använda en HSM måste den återställas (eller nollställas) för att säkerställa att inga data beständiges. Processen för att återställa enheten tar bort all anpassad konfiguration och alla data. Microsoft frigör enheten och returnerar den till poolen i ett pristine-tillstånd. Det innebär att när enheten returneras till poolen, finns det inget tecken på tidigare kund aktivitet. 

### <a name="hardware-issues"></a>Maskin varu problem

HSM-enheten har redundanta och utbytbara ström källor och fläkt enheter.  Borttagning av fläkt enheter kommer dock fortfarande att orsaka en Manipulerings händelse. När ett komponent fel uppstår, kommer Microsoft att använda den lämpligaste processen för att åtgärda problem på komponent nivå på ett sätt som orsakar minimalt avbrott och lägsta risk för våra kunders tjänst tillgänglighet.
Ett mer allvarligt haveri på enheten innebär att enheten ersätts av en ny enhet från den kostnads fria poolen. Kunden inkluderar bara den nya enheten i det befintliga HA-paret för att synkronisera och återgå till fullständigt drifts tillstånd. Den felande enheten kommer att ha sina data bär ande enheter borttagna och förstörda på platsen i data centret. Endast chassit kommer att returneras till Gemalto för återvinning.


### <a name="networking-issues"></a>Nätverks problem

Om kunderna får problem med nätverks åtkomst till HSM-enheten bör de kontakta Microsoft support. Ett enkelt test för nätverks åtkomst är att använda SSH för att ansluta till HSM-enheten. Kontakta Microsoft-supporten om detta Miss lyckas.

## <a name="service-level-expectations-for-support"></a>Förväntningar på service nivå för support

Information om Microsofts Support tjänster finns i [support avtalet för Azure](https://azure.microsoft.com/support/plans/).
Gemalto Support Service nivåer finns i [Gemalto support Essentials](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Nästa steg

Vi rekommenderar att viktiga begrepp som hög tillgänglighet och säkerhet är väl förstå före enhets etablering och program design eller distribution.

* [Distributions arkitektur](deployment-architecture.md)
* [Hög tillgänglighet](high-availability.md)
* [Fysisk säkerhet](physical-security.md)
* [Nätverk](networking.md)

