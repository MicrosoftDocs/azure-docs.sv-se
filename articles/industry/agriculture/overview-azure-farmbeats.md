---
title: Vad är Azure FarmBeats
description: Ger en översikt över Azure-FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: f173dfb055f3d9c75ee63570cd413276ffb6ba37
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182833"
---
# <a name="overview-of-azure-farmbeats-preview"></a>Översikt över Azure-FarmBeats (för hands version)

Azure FarmBeats är ett affärs-till-affärs erbjudande som är tillgängligt på Azure Marketplace. Det möjliggör agg regering av jordbruks data uppsättningar mellan leverantörer. Med Azure FarmBeats kan du bygga artificiell intelligens (AI) eller Machine Learning-modeller (ML) baserat på säkrade data uppsättningar. Med hjälp av Azure FarmBeats kan jordbrukets företag fokusera på kärn värde – lägger till i stället för den avskilda kraftigt avlyftningen av data teknik.

> [!NOTE]
> Azure FarmBeats är för närvarande en offentlig för hands version. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Azure FarmBeats tillhandahålls utan service nivå avtal. Använd support [forum för Azure FarmBeats](/answers/topics/azure-farmbeats.html) för support.

![Taktslag i projekt grupp](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

Med för hands versionen av Azure-FarmBeats kan du:

- Bedöm hälso tillståndet för Server gruppen med vegetations index och vatten index baserat på satellit-bilder.
- Få rekommendationer om hur många jord fuktighets sensorer som ska användas och var de ska placeras.
- Spåra Server grupps villkor genom visualisering av mark data som samlats in av sensorer från olika leverantörer.
- Hämta jord fuktighets kartor baserat på fusion av satellit-och sensor data.
- Få användbara insikter genom att skapa AI/ML-modeller ovanpå aggregerade data uppsättningar.
- Bygg eller utöka din digitala jordbruks lösning genom att tillhandahålla hälso rekommendationer för Server gruppen.

## <a name="datahub"></a>Datahub

Azure FarmBeats-Datahub är ett API-lager som möjliggör agg regerings-, normaliserings-och contextualization för olika jordbruks data uppsättningar i olika leverantörer. Du kan använda Azure-FarmBeats för att få:
- **Sensor data** från två sensor leverantörer [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/), [Teralytic](https://teralytic.com/), [Pessl Instruments](https://metos.at/)
- **Satellit bilder** från Europeiska rums byråens [kontroll 2](https://sentinel.esa.int/web/sentinel/home) satellit uppdrag
- **Drönare bilder** från tre drönare bilder-providers [senseFly](https://www.sensefly.com/) , [SlantRange](https://slantrange.com/) , [DJI](https://dji.com/)

Datahub är utformad som en utöknings bar API-plattform. Vi arbetar med många fler leverantörer för att integrera med Azure FarmBeats, så du har fler valmöjligheter när du skapar din lösning.

## <a name="accelerator"></a>Gas

Azure FarmBeats Accelerator är ett exempel på ett webb program som bygger på Datahub. Accelerator hopp – startar ditt användar gränssnitt och din modell utveckling. Azure FarmBeats-acceleratorn använder Azure FarmBeats-API: er. Den visualiserar inmatade sensor data som diagram och modellens utdata som Maps. Du kan till exempel använda acceleratorn för att skapa en Server grupp snabbt och få en vegetations index karta eller en sensor placerings karta för det Server gruppen enkelt.

## <a name="azure-role-based-access-control-azure-rbac"></a>Azure RBAC (rollbaserad åtkomstkontroll)

En administratör kan definiera åtkomst regler för Azure-FarmBeats med hjälp av en av de fördefinierade rollerna. Roller avgör vilka delar av programmet som en användare har åtkomst till och vilka åtgärder de kan utföra. Det finns två typer av roller i Azure-FarmBeats – för användare och för partner.

### <a name="user-roles"></a>Användarroller

En [administratör kan lägga till och hantera användare](manage-users-in-azure-farmbeats.md) och definiera deras åtkomst nivåer baserat på två användar roller: admin och skrivskyddad.

### <a name="partner-roles"></a>Partner roller

En administratör kan lägga till flera partner som data leverantörer i Azure FarmBeats. Följande sammanfattar de tillgängliga partner rollerna i FarmBeats och deras behörigheter:

| Partner typ    |   Åtgärder  | Omfång |
| ---- | -------- | -------- |
| Sensor partner  |   Skapa, läsa, uppdatera <br/> <br/> Läsa, uppdatera | DeviceModel, enhet, SensorModel, sensor <br/> <br/> ExtendedType |
| Bilder-partner  |   Skapa, läsa, uppdatera <br/> <br/> Läsa, uppdatera <br/> <br/> Läs | Scen, SceneFile <br/> <br/> ExtendedType <br/> <br/> Anläggningen |
| Väder partner * <br/> <br/>  (* Kommer snart) |   Skapa, läsa, uppdatera <br/> <br/> Läsa, uppdatera <br/> <br/> Läs | WeatherDataModel, WeatherDataLocation, JobType <br/> <br/> ExtendedType <br/> <br/> Anläggningen |

## <a name="resources"></a>Resurser

Azure FarmBeats erbjuds utan extra kostnad och du betalar bara för de Azure-resurser som du använder. Du kan använda nedanstående resurser för att få mer information om erbjudandet:

- Håll dig informerad om de senaste Azure FarmBeats-nyheterna genom att gå till vår [Azure FarmBeats-blogg](https://aka.ms/farmbeatsblog).
- Sök efter hjälp genom att publicera en fråga i vårt [Azure FarmBeats support-forum](/answers/topics/azure-farmbeats.html).
- Ge feedback genom att publicera eller rösta på en funktions idé i vårt [Azure FarmBeats feedback-forum](https://aka.ms/farmbeatsfeedback).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Installera Azure FarmBeats](install-azure-farmbeats.md)