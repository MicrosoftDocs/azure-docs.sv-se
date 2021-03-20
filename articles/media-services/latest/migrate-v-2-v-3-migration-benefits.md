---
title: Fördelarna med att migrera till Media Services API v3
description: Den här artikeln innehåller fördelarna med att migrera från Media Services v2 till v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: b6d51e05598f60de0e9c8fb85472b7c14bba990e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598396"
---
# <a name="step-1---understand-the-benefits-of-migrating-to-media-services-api-v3"></a>Steg 1 – förstå fördelarna med att migrera till Media Services API v3

![logo typ för migrations guide](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![steg 2 i migreringen](./media/migration-guide/steps-1.svg)

Vi rekommenderar att du börjar använda version 2020-05-01 av Azure Media Services v3 API nu för att få fördelarna eftersom nya funktioner, funktioner och prestanda optimeringar endast är tillgängliga i det aktuella v3-API: et.

Du kan ändra API-versionen i portalen, de senaste SDK: erna, senaste CLI och REST API med rätt versions sträng.

Det har gjorts betydande förbättringar av Media Services med v3.  

## <a name="benefits-of-media-services-v3"></a>Fördelar med Media Services v3

| **V3-funktion** | **Fördelar** |
| --- | --- |
| **Azure-portalen** | |
| Azure Portal uppdateringar | Azure Portal har uppdaterats för att inkludera hantering av v3-API-entiteter. Det gör att kunderna kan använda portalen för att starta Live Streaming, skicka v3-omvandlings jobb, hantera innehålls skydds principer, slut punkter för direkt uppspelning, få API-åtkomst, hantera länkade lagrings konton och utföra övervaknings aktiviteter. |
| **Konton och lagring** | |
| Rollbaserad åtkomst kontroll i Azure (RBAC) | Kunder kan nu definiera sina egna roller och kontrol lera åtkomsten till varje entitet i Media Services ARM-API: et. Detta hjälper till att kontrol lera åtkomsten till resurser av AAD-konton. |
| Hanterade identiteter | Hanterade identiteter eliminerar behovet av utvecklare att hantera autentiseringsuppgifter genom att tillhandahålla en identitet för Azure-resursen i Azure AD. Se information om hanterade identiteter [här](../../active-directory/managed-identities-azure-resources/overview.md). |
| Stöd för privata länkar | Kunderna får åtkomst till Media Services slut punkter för nyckel leverans, LiveEvents och strömnings slut punkter via en PrivateEndpoint på sitt VNet. |
| [Kund – hanterade nycklar](concept-use-customer-managed-keys-byok.md) eller ta med din egen nyckel (BYOK) support | Kunder kan kryptera data i sina Media Services-konton med hjälp av en nyckel i deras Azure Key Vault. |
| **Tillgångar** | |
| En till gång kan ha flera [positioner för strömning](streaming-locators-concept.md) med olika inställningar för [dynamisk paketering](dynamic-packaging-overview.md) och dynamisk kryptering. | Det finns en gräns på 100 strömmande positionerare som tillåts för varje till gång. Kunder kan lagra en enda kopia av medie innehållet i till gången, men dela olika URL: er för strömning med olika strömmande principer eller innehålls skydds principer som baseras på en riktad mål grupp.
| **Jobb bearbetning** ||
| V3 introducerar begreppet [omvandling](transforms-jobs-concept.md)   för filbaserad jobb bearbetning. | En transformering kan användas för att skapa återanvändbara konfigurationer, skapa Azure Resource Manager mallar och isolera bearbetnings inställningar mellan flera kunder eller klienter. |
| För filbaserad jobb bearbetning kan du använda en HTTP (S)-URL som indata. | Du behöver inte ha något innehåll som redan är lagrat i Azure, eller så behöver du inte skapa några inmatade till gångar. |
| **Live-händelser** ||
| Premium 1080p Live-händelser | Nya SKU: er för live event gör det möjligt för kunder att få moln kodning med utdata upp till 1080p i upplösning. |
| Ny support för Live-direktuppspelning med [låg latens](live-event-latency.md) för Live-händelser. | Detta gör det möjligt för användare att se till att Live-händelser närmar sig i real tid än om de inte har aktiverat den här inställningen. |
| För hands versionen av live event stöder [dynamisk paketering](dynamic-packaging-overview.md)   och dynamisk kryptering. | Detta möjliggör innehålls skydd i för hands versionen och bindestreck och HLS-paket. |
| Live-utdata ersätter program | Live-utdata är enklare att använda än entiteten program i v2-API: erna. |
| RTMP-inmatning för Live-händelser har förbättrats med stöd för fler kodare | Ökar stabiliteten och ger käll kodarens flexibilitet. |
| Live-händelser kan strömma dygnet runt | Du kan vara värd för en Live-händelse och låta din publik använda längre perioder. |
| Direkt avskrift för Live-händelser | Med direkt avskrift kan kunder automatiskt skriva talade språk till text i real tid under sändningen av direktsänd händelse. Detta förbättrar tillgängligheten för Live-händelser. |
| [Vänte läge](live-events-outputs-concept.md#standby-mode) för Live-händelser | Live-händelser som är i vänte läge är billigare än att köra Live-händelser. Detta gör det möjligt för kunderna att underhålla en uppsättning direktsända händelser som är redo att starta inom några sekunder till en lägre kostnad än att underhålla en uppsättning aktiva Live-händelser. Minskad prissättning för Live-händelser börjar gälla i februari 2021 för de flesta regioner, med resten att följa i april 2021.
|**Innehålls skydd** ||
| [Innehålls skydd](content-key-policy-concept.md)   stöder flera viktiga funktioner. | Kunder kan nu använda flera innehålls krypterings nycklar på sina strömmande positionerare. |
| **Övervakning** | |
| Support för [Azure EventGrid](monitoring/reacting-to-media-services-events.md) Notification | EventGrid-meddelanden är mer omfattande. Det finns fler typer av meddelanden, bredare SDK-stöd för att ta emot meddelanden i ditt eget program och fler befintliga Azure-tjänster som kan fungera som händelse hanterare. |
| [Azure Monitor support och integrering i Azure Portal](monitoring/monitor-events-portal-how-to.md) | Detta gör det möjligt för kunderna att visualisera Media Services konto kvot användning, real tids statistik över slut punkter för direkt uppspelning och inmatning och Arkiv statistik för live-evenemang. Kunder kan nu ställa in aviseringar och utföra nödvändiga åtgärder baserat på mått data i real tid. |

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]