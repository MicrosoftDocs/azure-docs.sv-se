---
title: Händelsesammansättning
description: Läs mer om Defender för händelse agg regering i IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/03/2020
ms.author: mlottner
ms.openlocfilehash: c823f0034db7d5fbe1f6b46f6af74e9fa374a6de
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2020
ms.locfileid: "97832377"
---
# <a name="defender-for-iot-event-aggregation"></a>Defender för IoT Event-sammansättning

Defender för IoT-säkerhetsagenter samlar in data-och system händelser från din lokala enhet och skickar dessa data till Azure-molnet för bearbetning och analys. Säkerhets agenten samlar in många typer av enhets händelser, inklusive nya processer och nya anslutnings händelser. Både nya processer och nya anslutnings händelser kan på ett legitimt sätt ske ofta på en enhet inom en sekund, och även om det är viktigt för robust och omfattande säkerhet, tvingas det att skicka säkerhets agenter för meddelanden som snabbt når eller överskrider din IoT Hub kvot och dina kostnads gränser. Dessa händelser innehåller dock mycket värdefull säkerhets information som är viktig för att skydda enheten.

För att minska den ytterligare kvoten och kostnaderna samtidigt som dina enheter skyddas, aggregerar Defender för IoT-agenter dessa typer av händelser.

Händelse agg regering är **aktiverat** som standard och även om det inte rekommenderas, **kan stängas av manuellt när som** helst.

Aggregator är för närvarande tillgängligt för följande typer av händelser:

* ProcessCreate
* ConnectionCreate
* ProcessTerminate (endast Windows)

## <a name="how-does-event-aggregation-work"></a>Hur fungerar händelse agg regering?

När händelse agg regering är kvar **på**, är Defender för IoT-agenter sammanställt händelser för intervall perioden eller tids perioden.
När intervall perioden har passerat skickar agenten de aggregerade händelserna till Azure-molnet för ytterligare analys.
De aggregerade händelserna lagras i minnet tills de skickas till Azure-molnet.

När agenten samlar in en identisk händelse till en som redan finns i minnet, ökar agenten antalet träffar för den här händelsen. När den sammanställnings tids perioden passerar skickar agenten antalet träffar för varje typ av händelse som har inträffat. Händelse agg regering är bara agg regeringen av antalet träffar för varje insamlad typ av händelse.

Händelser anses vara identiska endast när följande villkor uppfylls:

* ProcessCreate-händelser – när **kommandorad**, **körbara filer**, **användar namn** och **UserID** är identiska
* ConnectionCreate-händelser – när **kommando raden**, **userId**, **Direction**, **Local Address**, **Remote Address**, * * Protocol och **målport** är identiska
* ProcessTerminate-händelser – när den **körbara filen** och **avslutnings statusen** är identiska

### <a name="working-with-aggregated-events"></a>Arbeta med sammanställda händelser

Under agg regering ignoreras händelse egenskaper som inte aggregeras och visas i Log Analytics med värdet 0.

* ProcessCreate-händelser – **ProcessID** och **parentProcessId** har angetts till 0
* ConnectionCreate-händelser – **ProcessID** och **käll porten** har angetts till 0

## <a name="event-aggregation-based-alerts"></a>Händelse agg regering baserade aviseringar

Efter analysen skapar Defender för IoT säkerhets aviseringar för misstänkta sammanställda händelser. Aviseringar som skapas från sammanställda händelser visas bara en gång för varje sammanställd händelse.

Samlingens start tid, slut tid och antal träffar för varje händelse loggas i fältet Event **ExtraDetails** i Log Analytics som används vid undersökningar.

Varje sammanställd händelse representerar en 24-timmarsperiod med insamlade aviseringar. Med hjälp av menyn händelse alternativ längst upp till vänster i varje händelse kan du **stänga** av varje enskild sammanställd händelse.

## <a name="event-aggregation-twin-configuration"></a>Dubbel konfiguration av händelse sammansättning

Gör ändringar i konfigurationen av Defender för IoT Event-aggregering i [agent konfigurations objekt](how-to-agent-configuration.md) för modulens dubbla identitet för **azureiotsecurity** -modulen.

| Konfigurations namn | Möjliga värden | Information | Kommentarer |
|:-----------|:---------------|:--------|:--------|
| aggregationEnabledProcessCreate | boolean | Aktivera/inaktivera händelse agg regering för process-skapa händelser |
| aggregationIntervalProcessCreate | ISO8601 TimeSpan-sträng | Samlings intervall för process skapande av händelser |
| aggregationEnabledConnectionCreate | boolean| Aktivera/inaktivera händelse agg regering för skapande av anslutnings händelser |
| aggregationIntervalConnectionCreate | ISO8601 TimeSpan-sträng | Samlings intervall för skapande av anslutnings händelser |
| aggregationEnabledProcessTerminate | boolean | Aktivera/inaktivera händelse agg regering för process-avsluta händelser | Endast Windows|
| aggregationIntervalProcessTerminate | ISO8601 TimeSpan-sträng | Samlings intervall för process-avsluta händelser | Endast Windows|
|

## <a name="default-configurations-settings"></a>Standardinställningar för konfigurering

| Konfigurations namn | Standardvärden |
|:-----------|:---------------|
| aggregationEnabledProcessCreate | true |
| aggregationIntervalProcessCreate | PT1H|
| aggregationEnabledConnectionCreate | true |
| aggregationIntervalConnectionCreate | PT1H|
| aggregationEnabledProcessTerminate | true |
| aggregationIntervalProcessTerminate | PT1H|
|

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om att använda Defender för IoT Security Agent agg regering och de tillgängliga alternativen för händelse konfiguration.

Använd följande artiklar om du vill fortsätta komma igång med Defender för IoT-distribution:

- Förstå [autentiseringsmetoder för säkerhets agenter](concept-security-agent-authentication-methods.md)
- Välj och distribuera en [säkerhets agent](how-to-deploy-agent.md)
- Granska Defender för IoT [system-krav](quickstart-system-prerequisites.md)
- Lär dig hur du [aktiverar Defender för IoT-tjänsten i din IoT Hub](quickstart-onboard-iot-hub.md)
- Läs mer om tjänsten från [vanliga frågor och svar om Defender för IoT](resources-frequently-asked-questions.md)
