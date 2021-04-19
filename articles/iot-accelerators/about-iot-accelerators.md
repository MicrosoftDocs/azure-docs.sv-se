---
title: Introduktion till IoT-lösningsacceleratorer – Azure | Microsoft Docs
description: Läs mer om Azure IoT-lösningsacceleratorer. IoT-lösningsacceleratorer är kompletta, heltäckande, distributionsklara IoT-lösningar.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: timlt
ms.openlocfilehash: c966051ed5699d408fe83f1e9c862ca78b3282c4
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714550"
---
# <a name="what-are-azure-iot-solution-accelerators"></a>Vad är Azure IoT-lösningsacceleratorer?

En molnbaserad IoT-lösning använder normalt anpassad kod och molntjänster för att hantera anslutningsmöjligheter för enheter, databearbetning och -analys samt presentation.

IoT-lösningsacceleratorer är kompletta och redo att distribuera IoT-lösningar som implementerar vanliga IoT-scenarier. Scenarierna omfattar ansluten fabrik och enhetssimulering. När du distribuerar en lösningsaccelerator omfattar distributionen alla molnbaserade tjänster som krävs tillsammans med programkod som krävs.

Lösningsacceleratorerna är startpunkter för dina egna IoT-lösningar. Källkoden för alla lösningsacceleratorer är öppen källkod och finns i GitHub. Vi rekommenderar att du laddar ned och anpassar lösningsacceleratorerna så att de uppfyller dina krav.

Du kan även använda lösningsacceleratorerna som utbildningsverktyg innan du skapar en anpassad IoT-lösning från grunden. Lösningsacceleratorer implementerar beprövade metoder för molnbaserade IoT-lösningar som du måste följa.

Programkoden i varje lösningsaccelerator innehåller en webbapp som du kan använda för att hantera lösningsacceleratorn.

> [!NOTE]
> Lösningarna för fjärrövervakning och förutsägande underhåll [](https://www.azureiotsolutions.com/Accelerators) har tagits bort från Azure IoT-lösningsacceleratorer platsen. Mer information finns i [Vad är Azure IoT-lösningsacceleratorer? (tidigare version)](/previous-versions/azure/iot-accelerators/about-iot-accelerators).

## <a name="supported-iot-scenarios"></a>IoT-scenarier som stöds

Det finns för närvarande två lösningsacceleratorer som du kan distribuera:

### <a name="connected-factory"></a>Ansluten fabrik

Använd den [Fabriksanslutna lösningsacceleratorn](iot-accelerators-connected-factory-features.md) för att samla in telemetri från industriella tillgångar med ett [OPC Unified Architecture](https://opcfoundation.org/about/opc-technologies/opc-ua/)-gränssnitt och kontrollera dem. Industriella tillgångar kan omfatta sammansättnings- och teststationer vid monteringsbandet på en fabrik.

Du kan använda instrumentpanelen för ansluten fabrik för att övervaka och hantera dina industrienheter:

:::image type="content" source="./media/about-iot-accelerators/cf-dashboard-inline.png" alt-text="Skärmbild som visar instrumentpanelen för lösningen Ansluten fabrik." lightbox="./media/about-iot-accelerators/cf-dashboard-expanded.png":::

### <a name="device-simulation"></a>Enhetssimulering

Använd [Lösningsacceleratorn för enhetssimulering](iot-accelerators-device-simulation-overview.md) för att köra simulerade enheter som genererar realistisk telemetri. Du kan använda den här lösningsacceleratorn till att testa beteendet för andra lösningsacceleratorer eller till att testa dina egna anpassade IoT-lösningar.

Du kan använda webbappen för enhetssimulering för att konfigurera och köra simuleringar:

:::image type="content" source="./media/about-iot-accelerators/ds-dashboard-inline.png" alt-text="Skärmbild som visar instrumentpanelen för enhetssimuleringslösningen." lightbox="./media/about-iot-accelerators/ds-dashboard-expanded.png":::

## <a name="design-principles"></a>Designprinciper

Alla lösningsacceleratorer följer samma principer och mål. De har utformats att vara:

* **skalbara**, så att du kan ansluta och hantera miljontals anslutna enheter
* **utökningsbara**, så att du kan anpassa dem efter dina behov
* **begripliga**, så att du kan förstå hur de fungerar och hur de implementeras.
* **modulära**, så att du kan byta ut tjänster för alternativ
* **säkra**, med en kombination av Azure-säkerhet och inbyggda säkerhetsfunktioner för anslutningar och enheter.

## <a name="architectures-and-languages"></a>Arkitekturer och språk

De ursprungliga lösningsacceleratorerna skrevs med .NET med hjälp av en MVC-arkitektur (model-view-controller). Microsoft uppdaterar lösningsacceleratorerna till en ny arkitektur baserad på mikrotjänster. Följande tabell visar lösningsacceleratorernas aktuella status med länkar till GitHub-lagringsplatserna:

| Lösningsaccelerator   | Arkitektur  | Språk     |
| ---------------------- | ------------- | ------------- |
| Ansluten fabrik      | MVC           | [.NET](https://github.com/Azure/azure-iot-connected-factory)          |
| Enhetssimulering      | Mikrotjänster | [.NET](https://github.com/Azure/azure-iot-pcs-device-simulation)          |

Läs mer om arkitektur för mikrotjänster i [Introduktion till Azure IoT-referensarkitektur](/azure/architecture/reference-architectures/iot/).

## <a name="deployment-options"></a>Distributionsalternativ

Du kan distribuera lösningsacceleratorerna från webbplatsen för [Microsoft Azure IoT-lösningsacceleratorer](https://www.azureiotsolutions.com/Accelerators#) eller från kommandoraden.

Kostnaden för att köra en lösningsaccelerator är den sammanlagda [kostnaden för att köra de underliggande Azure-tjänsterna](https://azure.microsoft.com/pricing). Du ser information om Azure-tjänster som används när du väljer dina distributionsalternativ.

## <a name="next-steps"></a>Nästa steg

Om du vill prova någon av IoT-lösningsacceleratorerna kan du ta en titt på snabbstarten [Prova en ansluten fabrikslösning](quickstart-connected-factory-deploy.md).
