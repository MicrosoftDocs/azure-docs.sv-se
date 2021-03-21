---
title: Översikt över Azure Communication Services UI Framework
titleSuffix: An Azure Communication Services conceptual document
description: Lär dig mer om Azure Communication Services UI Framework
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 47a32815ded5809edfde856a38c69ec7c6fd6fdf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493364"
---
# <a name="azure-communication-services-ui-framework"></a>GRÄNSSNITTs ramverk för Azure Communication Services

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

:::image type="content" source="../media/ui-framework/pre-and-custom-composite.png" alt-text="Jämförelse mellan förskapade och anpassade sammansättningar":::

Azure Communication Services UI Framework gör det enkelt för dig att bygga moderna kommunikations användar upplevelser. Du får ett bibliotek med produktions klara GRÄNSSNITTs komponenter som du kan ta del av i dina program:

- **Sammansatta komponenter** – de här komponenterna är lösningar som gör det möjligt att implementera vanliga kommunikations scenarier. Du kan snabbt lägga till video samtal eller chatta med program. Kompositer är komponenter med öppen källkod som skapats med bas komponenter.
- **Bas komponenter** – de här komponenterna är öppna käll kods Bygg stenar som gör att du kan skapa anpassad kommunikations upplevelse. Komponenter erbjuds både för att anropa och chatta med funktioner som kan kombineras för att utveckla upplevelser. 

Dessa klient bibliotek för användar gränssnittet använder [Microsofts Fluent-design språk](https://developer.microsoft.com/fluentui/) och till gångar. Fluent-ANVÄNDARGRÄNSSNITTET ger ett grundläggande lager för det användar gränssnitts ramverk som har testats av Microsoft-produkter.

## <a name="differentiating-components-and-composites"></a>**Differentiering av komponenter och sammansättningar**

**Bas komponenter** bygger på de viktigaste klient biblioteken för Azure Communication Services och implementerar grundläggande åtgärder som att initiera kärn klient bibliotek, återge video och tillhandahålla användar kontroller för att stänga av, video på/av osv. Du kan använda dessa **bas komponenter** för att skapa dina egna anpassade layout-upplevelser med färdiga, produktions klara kommunikations komponenter.

:::image type="content" source="../media/ui-framework/component-overview.png" alt-text="Översikt över komponenten för UI Framework":::

**Sammansatta komponenter** kombinerar flera **bas komponenter** för att skapa mer kompletta kommunikations upplevelser. Dessa komponenter på högre nivå kan enkelt integreras i en befintlig app för att ta bort en fledge kommunikations upplevelse utan att skapa den från grunden. Utvecklare kan koncentrera sig på att skapa den omgivande upplevelsen och flödet som önskas i sina appar och lämna kommunikations komplexiteten till sammansatta komponenter.

:::image type="content" source="../media/ui-framework/composite-overview.png" alt-text="Översikt över sammansatt för GRÄNSSNITTs ramverk":::

## <a name="what-ui-framework-is-best-for-my-project"></a>Vilket UI-ramverk passar bäst för mitt projekt?

Genom att förstå dessa krav kan du välja rätt klient bibliotek:

- **Hur mycket anpassning vill du ha?** Klient biblioteken för Azure Communication Core har inget UX och är utformade så att du kan bygga vilket UX du vill. UI Framework-komponenter ger användar gränssnitts till gångar till kostnaden för minskad anpassning.
- **Behöver du Mötes funktioner?** Mötes systemet har flera unika funktioner som för närvarande inte är tillgängliga i klient biblioteken för Azure Communication Services, t. ex. suddig bakgrund och upphöjd hand.
- **Vilka plattformar har du mål?** Olika plattformar har olika funktioner.

Information om funktions tillgänglighet i de varierande [SDK: erna för användar gränssnitt finns här](ui-sdk-features.md), men viktiga uppgifter sammanfattas nedan.

|Klient bibliotek/SDK|Implementerings komplexitet|    Anpassnings möjlighet|  Sänder| Chatt| [Team-interop](./../teams-interop.md)
|---|---|---|---|---|---|---|
|Sammansatta komponenter|Låg|Låg|✔|✔|✕
|Bas komponenter|Medel|Medel|✔|✔|✕
|Kärn klient bibliotek|Högt|Högt|✔|✔ |✔

## <a name="cost"></a>Cost

Användningen av Azure UI Framework har ingen extra kostnad eller mätning i Azure. Du betalar bara för användningen av den underliggande tjänsten med samma anrop, chatt och PSTN-mätare.

## <a name="supported-use-cases"></a>Användnings fall som stöds

Sänder

- Delta i Azure Communication Services-anrop med grupp-ID

Funktionen

- Delta i Azure Communication Services chat med tråd-ID

## <a name="supported-identities"></a>Identiteter som stöds:

En Azure Communication Services-identitet krävs för att initiera UI-ramverket och autentisera till tjänsten. Mer information om autentisering finns i [autentisering](../authentication.md) och [åtkomst-token](../../quickstarts/access-tokens.md)


## <a name="recommended-architecture"></a>Rekommenderad arkitektur 

:::image type="content" source="../media/ui-framework/framework-architecture.png" alt-text="UI Framework-rekommenderat arkitektur med klient server arkitektur ":::

Sammansättnings-och bas komponenter initieras med hjälp av en Azure Communication Services-åtkomsttoken. Åtkomsttoken bör delas från Azure Communication Services via en betrodd tjänst som du hanterar. Mer information finns i [snabb start: skapa åtkomsttoken](../../quickstarts/access-tokens.md) och [vägledning för betrodda tjänster](../../tutorials/trusted-service-tutorial.md) .

Dessa klient bibliotek kräver också kontexten för anropet eller chatten som de kommer att ansluta till. I likhet med token för användar åtkomst bör den här kontexten spridas till klienter via din egen betrodda tjänst. I listan nedan sammanfattas de initierings-och resurs hanterings funktioner som du behöver operationalisera.

| Contoso-ansvar                                 | UI Framework-ansvar                         |
|----------------------------------------------------------|-----------------------------------------------------------------|
| Ange åtkomsttoken från Azure                    | Släpp igenom den tilldelade åtkomsttoken för att initiera komponenter        |
| Ange uppdaterings funktion                                 | Uppdatera åtkomsttoken med funktionen för tillhandahållen utvecklare          |
| Hämta/skicka kopplings information för samtal eller chatt          | Släpp igenom samtal och chatta med information för att initiera komponenter |
| Hämta/skicka användar information för en anpassad data modell | Släpp igenom anpassad data modell till komponenter som ska renderas          |
