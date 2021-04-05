---
title: Konfigurera regler och åtgärder i Azure IoT Central | Microsoft Docs
description: Den här artikeln visar dig som ett verktyg för att konfigurera telemetri-baserade regler och åtgärder i ditt Azure IoT Central-program.
author: vavilla
ms.author: vavilla
ms.date: 12/23/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 378a1dbcfbc89cdf9b24dc2490db583f1135b9a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97796796"
---
# <a name="configure-rules"></a>Konfigurera regler

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

Regler i IoT Central fungerar som ett anpassningsbart svars verktyg som utlöser aktivt övervakade händelser från anslutna enheter. I följande avsnitt beskrivs hur regler utvärderas.

## <a name="select-target-devices"></a>Välj mål enheter

Använd avsnittet mål enheter för att välja vilken typ av enheter som regeln ska tillämpas på. Med filter kan du ytterligare förfina vilka enheter som ska ingå. Filtren använder egenskaper för enhets mal len för att filtrera uppsättningen med enheter. Själva filtren utlöser inte en åtgärd. I följande skärm bild är de enheter som används som mål för enhetens Malltyp **kyl skåp**. Filtret anger att regeln endast ska innehålla **kyl skåp** där egenskapen **tillverkat tillstånd** är lika med **Washington**.

![Villkor](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>Använd flera villkor

Villkoren är vilka regler som utlöses på. När du lägger till flera villkor i en regel är de logiskt och sammantaget. Med andra ord måste alla villkor vara uppfyllda för att regeln ska utvärderas som sant.  

I följande skärm bild kontrollerar villkoren när temperaturen är större än 70 &deg; F och att fuktigheten är mindre än 10. När båda dessa instruktioner är uppfyllda utvärderas regeln till sant och utlöser en åtgärd.

![Skärm bild som visar en kyl skåps övervakare med villkor som anges för temperatur och fuktighet.](media/howto-configure-rules/conditions.png)

### <a name="use-a-cloud-property-in-a-value-field"></a>Använd en moln egenskap i ett värde fält

Du kan referera till en moln egenskap från enhets mal len i fältet **värde** för ett villkor. Moln egenskapen och telemetri-värdet måste ha liknande typer. Om **temperaturen** t. ex. är en dubbel, visas endast moln egenskaper av typen dubbelt som alternativ i list rutan **värde** .

Om du väljer ett telemetri värde för händelse typ innehåller **värdet** List rutan värde **alla**. **Alla** alternativ innebär att regeln utlöses när ditt program får en händelse av den typen, oavsett nytto Last.

## <a name="use-aggregate-windowing"></a>Använd agg regerings fönster

Regler utvärderar sammanställd tid i Windows som rullande Windows. I skärm bilden nedan är tids perioden fem minuter. Var femte minut utvärderar regeln de senaste fem minuterna data. Data utvärderas bara en gång i fönstret som den motsvarar.

![Rullande Windows](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>Använda regler med IoT Edge moduler

En begränsning gäller regler som tillämpas på IoT Edge moduler. Regler för telemetri från olika moduler utvärderas inte som giltiga regler. Ta med följande som exempel. Det första villkoret för regeln är på en temperatur telemetri från modul A. Regelns andra villkor är en fuktighets-telemetri på modul B. Eftersom de två villkoren är från olika moduler är detta en ogiltig uppsättning villkor. Regeln är inte giltig och kommer att utlösa ett fel vid försök att spara regeln.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du konfigurerar en regel i ditt Azure IoT Central-program, kan du lära dig hur du [konfigurerar avancerade regler](howto-configure-rules-advanced.md) med hjälp av energi automatisering eller Azure Logic Apps.
