---
title: Konfigurera Azure Sentinel för Defender för IoT
description: Förklarar hur du konfigurerar Azure Sentinel för att ta emot data från din Defender for IoT-lösning.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/28/2020
ms.author: shhazam
ms.openlocfilehash: 2d82aaadf158e45cb8faaeee0b9b4e0fc80a3420
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98247343"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel"></a>Anslut dina data från Defender för IoT till Azure Sentinel 

Använd Defender för IoT Connector för att strömma alla dina Defender for IoT-händelser till Azure Sentinel. 

Den här integreringen gör det möjligt för organisationer att snabbt identifiera angrepp i många steg som ofta passerar IT-och-gränser. Dessutom ger Defender för IoT: s integrering med Azure Sentinel-funktionerna för säkerhets dirigering, automatisering och svar (SOAR) automatiserade svar och förebyggande åtgärder med hjälp av inbyggda spel böcker-optimerade. 

## <a name="prerequisites"></a>Förutsättningar

- **Läs** -och **Skriv** behörighet på arbets ytan där Azure Sentinel har distribuerats
- **Defender för IoT** måste vara **aktiverat** på dina relevanta IoT Hub
- Du måste ha **deltagar** behörighet för den **prenumeration** som du vill ansluta

## <a name="connect-to-defender-for-iot"></a>Ansluta till Defender för IoT

1. I Azure Sentinel väljer du **data anslutningar** och väljer sedan **Defender för IoT** (kan fortfarande anropas Azure Security Center för IoT) från galleriet.

1. Klicka på **Öppna kopplings sida** längst ned i den högra rutan.

1. Klicka på **Anslut**, bredvid varje IoT Hub prenumeration vars aviseringar och enhets aviseringar du vill strömma till Azure Sentinel.
    - Du får ett fel meddelande om Defender för IoT inte är aktiverat på minst en IoT Hub i en prenumeration. Aktivera Defender för IoT i IoT Hub för att ta bort felet.

1. Du kan välja om du vill att aviseringar från Defender för IoT automatiskt ska generera incidenter i Azure Sentinel. Under **skapa incidenter** väljer du **Aktivera** för att aktivera standard analys regeln för att automatiskt skapa incidenter från de genererade aviseringarna. Den här regeln kan ändras eller redige ras under **analys**  >  **aktiva regler**.

> [!NOTE]
> Det kan ta 10 sekunder eller mer för **prenumerations** listan att uppdateras när anslutnings ändringarna har gjorts. 

## <a name="log-analytics-alert-view"></a>Log Analytics aviserings vy

Så här använder du det relevanta schemat i Log Analytics för att Visa Defender för IoT-aviseringar:

1. Öppna **loggar**  >  **SecurityInsights**  >  **SecurityAlert** eller Sök efter **SecurityAlert**.

1. Filtrera om du bara vill visa Defender för IoT-genererade aviseringar med följande KQL-filter:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
```

### <a name="service-notes"></a>Service anmärkningar

När du har anslutit en **prenumeration** är Hub-data tillgängliga i Azure Sentinel cirka 15 minuter senare.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter till Defender för IoT till Azure Sentinel. Mer information om hot identifiering och säkerhets data åtkomst finns i följande artiklar:

- Lär dig hur du använder Azure Sentinel för att [få insyn i dina data och potentiella hot](../sentinel/quickstart-get-visibility.md).
- Lär dig hur du [kommer åt dina IoT-säkerhetsdata](how-to-security-data-access.md)