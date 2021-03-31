---
title: Ansluta Azure Defender för IoT till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Azure Defender (tidigare Azure Security Center) för IoT-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: 67bc104434dc0db30f5973bec0979afb7480fe4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98621387"
---
# <a name="connect-your-data-from-azure-defender-formerly-azure-security-center-for-iot-to-azure-sentinel"></a>Anslut dina data från Azure Defender (tidigare Azure Security Center) för IoT till Azure Sentinel 

Använd Defender för IoT Connector för att strömma alla dina Defender for IoT-händelser till Azure Sentinel. 

Den här integreringen gör det möjligt för organisationer att snabbt identifiera angrepp i många steg som ofta passerar IT-och-gränser. Dessutom ger Defender för IoT: s integrering med Azure Sentinel-funktionerna för säkerhets dirigering, automatisering och svar (SOAR) automatiserade svar och förebyggande åtgärder med hjälp av inbyggda spel böcker-optimerade. 
## <a name="prerequisites"></a>Förutsättningar

- **Läs** -och **Skriv** behörighet på arbets ytan där Azure Sentinel har distribuerats
- **Defender för IoT** måste vara **aktiverat** på dina relevanta IoT Hub
- Du måste ha **deltagar** behörighet för den **prenumeration** som du vill ansluta

## <a name="connect-to-defender-for-iot"></a>Ansluta till Defender för IoT

1. I Azure Sentinel väljer du **data kopplingar** och väljer sedan **Defender för IoT** (kan fortfarande anropas Azure Security Center för IoT) från galleriet.

1. Klicka på **Öppna kopplings sida** längst ned i den högra rutan. 

1. Klicka på **Anslut**, bredvid varje IoT Hub prenumeration vars aviseringar och enhets aviseringar du vill strömma till Azure Sentinel. 
    - Du får ett fel meddelande om Defender för IoT inte är aktiverat på minst en IoT Hub i en prenumeration. Aktivera Defender för IoT i IoT Hub för att ta bort felet.

1. Du kan välja om du vill att aviseringar från Defender för IoT automatiskt ska generera incidenter i Azure Sentinel. Under **skapa incidenter** väljer du **Aktivera** för att aktivera standard analys regeln för att automatiskt skapa incidenter från de genererade aviseringarna. Den här regeln kan ändras eller redige ras under **analys**  >  **aktiva regler**.

> [!NOTE]
> Det kan ta 10 sekunder eller mer för **prenumerations** listan att uppdateras när anslutnings ändringarna har gjorts. 

## <a name="log-analytics-alert-view"></a>Log Analytics aviserings vy

Så här använder du det relevanta schemat i Log Analytics för att Visa Defender för IoT-aviseringar:

1. Öppna **loggar**  >  **SecurityInsights**  >  **SecurityAlert** eller Sök efter **SecurityAlert**. 

2. Filtrera om du bara vill visa Defender för IoT-genererade aviseringar med följande KQL-filter:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Service anmärkningar

När du har anslutit en **prenumeration** är Hub-data tillgängliga i Azure Sentinel cirka 15 minuter senare.


## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter till Defender för IoT till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
