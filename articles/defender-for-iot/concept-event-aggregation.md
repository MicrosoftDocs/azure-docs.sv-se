---
title: Händelse agg regering (för hands version)
description: Defender för IoT-säkerhetsagenter samlar in data-och system händelser från den lokala enheten och skickar data till Azure-molnet för bearbetning och analys.
ms.date: 1/20/2021
ms.topic: conceptual
ms.openlocfilehash: c0280e97549009a1e4911c072a7a8ec052684b4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779332"
---
# <a name="event-aggregation-preview"></a>Händelse agg regering (för hands version)

Defender för IoT-säkerhetsagenter samlar in data-och system händelser från den lokala enheten och skickar data till Azure-molnet för bearbetning och analys. Defender for IoT Micro agent samlar in många typer av enhets händelser, inklusive nya processer och alla nya anslutnings händelser. Både den nya processen och nya anslutnings händelser kan uppstå ofta på en enhet inom en sekund. Den här funktionen är viktig för omfattande säkerhet, men antalet meddelanden som skickas av säkerhets agenter kan dock uppfyllas snabbt eller överskrida dina IoT Hub kvot-och kostnads gränser. Dessa händelser innehåller dock mycket värdefull säkerhets information som är viktig för att skydda enheten. 

För att minska den extra kvoten och kostnaderna samtidigt som enheterna skyddas, aggregerar Defender för IoT-agenter dessa typer av händelser: 

- ProcessCreate (endast Linux) 

- ConnectionCreate (endast Azure återställnings tider) 

## <a name="how-does-event-aggregation-work"></a>Hur fungerar händelse agg regering? 

Defender för IoT-agenter sammanställer händelser för intervall perioden eller tids perioden. När intervall perioden har passerat skickar agenten de aggregerade händelserna till Azure-molnet för ytterligare analys. De aggregerade händelserna lagras i minnet tills de skickas till Azure-molnet. 

När agenten samlar in en identisk händelse till en som redan finns i minnet, ökar agenten antalet träffar för den här händelsen, för att minska agentens minnes utrymme. När den sammanställnings tids perioden passerar skickar agenten antalet träffar för varje typ av händelse som har inträffat. Händelse agg regering är bara agg regeringen av antalet träffar för varje insamlad typ av händelse. 

## <a name="process-events"></a>Bearbeta händelser 

Process händelser stöds för närvarande bara på Linux-operativsystem. 

Process händelser betraktas som identiska när *kommando raden* och  *UserID*   är identiska. 

Standardbufferten för process händelser är 32 processer, efter vilken bufferten ska Cycle och de äldsta process händelserna tas bort för att göra plats för nya process händelser.  

## <a name="network-connection-events"></a>Nätverks anslutnings händelser 

Nätverks anslutnings händelser stöds för närvarande endast på Azure-återställnings tider. 

Nätverks anslutnings händelser anses identiska när den *lokala porten*, *fjärrporten*, *transport protokollet*, den  *lokala adressen* och  *fjär adressen* är identiska. 

Standardbufferten för nätverks anslutnings händelser är 64. Inga nya nätverks händelser kommer att cachelagras förrän nästa samlings cykel. En varning om att öka cachestorleken kommer att loggas.

## <a name="next-steps"></a>Nästa steg

Kontrol lera dina [säkerhets aviseringar för Defender för IoT](concept-security-alerts.md).
