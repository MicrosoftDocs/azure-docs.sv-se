---
title: Diagnostisera och Felsök från kopplingar med Azure IoT Hub DPS
description: Lär dig att diagnostisera och felsöka vanliga fel med enhets anslutning för Azure IoT Hub Device Provisioning Service (DPS)
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: xujing
ms.openlocfilehash: ba81be54592e591f734b05235bb3ce183a07a11c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709894"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>Fel sökning med Azure IoT Hub Device Provisioning Service

Anslutnings problem för IoT-enheter kan vara svåra att felsöka eftersom det finns många möjliga fel punkter, till exempel attesterings fel, registrerings fel osv. Den här artikeln innehåller vägledning om hur du identifierar och felsöker problem med enhets anslutningar via [Azure Monitor](../azure-monitor/overview.md).

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>Använda Azure Monitor för att visa mått och konfigurera aviseringar

Följande procedur beskriver hur du visar och ställer in aviseringar för IoT Hub Device Provisioning Service mått. 

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Bläddra till IoT Hub Device Provisioning Service.

3. Välj **Mått**.

4. Välj önskat mått. 
   <br />För närvarande finns det tre mått för DPS:

    | Måttnamn | Beskrivning |
    |-------|------------|
    | Attesterings försök | Antal enheter som försökte autentisera med enhets etablerings tjänsten|
    | Registrerings försök | Antal enheter som försökte registrera sig för IoT Hub efter en lyckad autentisering|
    | Enhet tilldelad | Antal enheter som har tilldelats IoT Hub|

5. Välj önskad agg regerings metod för att skapa en visuell vy av måttet. 

6. Om du vill ställa in en varning för ett mått väljer du **nya varnings regler** längst upp till höger på bladet mått, på samma sätt som du kan gå till **varnings** bladet och välja **nya varnings regler**.

7. Välj **Lägg till villkor** och välj sedan önskat mått och tröskelvärde genom att följa instruktionerna nedan.

Mer information finns i [varningar i Azure Monitor](../azure-monitor/platform/alerts-overview.md).

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>Använda logg analys för att visa och lösa fel

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Bläddra till din IoT-hubb.

3. Välj **diagnostikinställningar**.

4. Välj **Aktivera diagnostik**.

5. Aktivera önskade loggar som ska samlas in.

    | Loggnamn | Beskrivning |
    |-------|------------|
    | DeviceOperations | Loggar relaterade till anslutnings händelser för enheter |
    | ServiceOperations | Händelse loggar som är relaterade till användningen av service SDK (t. ex. för att skapa eller uppdatera registrerings grupper)|

6. Aktivera **Skicka till Log Analytics** ([Se prissättning](https://azure.microsoft.com/pricing/details/log-analytics/)). 

7. Gå till fliken **loggar** i Azure Portal under enhets Provisioning service-resursen.

8. Klicka på **Kör** för att visa de senaste händelserna.

9. Om det finns resultat kan du söka efter `OperationName` , `ResultType` , `ResultSignature` och `ResultDescription` (fel meddelande) för att få mer information om felet.


## <a name="common-error-codes"></a>Vanliga felkoder
Använd den här tabellen för att förstå och lösa vanliga fel.

| Felkod| Beskrivning | HTTP-statuskod |
|-------|------------|------------|
| 400 | Bröd texten i begäran är inte giltig. Det går till exempel inte att parsa den, eller så går det inte att verifiera objektet.| 400 felaktigt format |
| 401 | Det går inte att verifiera autentiseringstoken. till exempel har den upphört att gälla eller gäller inte för frågans URI. Den här felkoden returneras även till enheter som en del av flödet för TPM-attestering. | 401 – Ej behörig|
| 404 | Enhets etablerings tjänstens instans eller en resurs (t. ex. en registrering) finns inte. |404 – Hittades inte |
| 412 | ETag i begäran matchar inte den befintliga resursens ETag, som per RFC7232. | 412-villkoret misslyckades |
| 429 | Åtgärder begränsas av tjänsten. Information om begränsningar för vissa tjänster finns i [IoT Hub Device Provisioning service gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-device-provisioning-service-limits). | 429 för många begär Anden |
| 500 | Ett internt fel har inträffat. | 500 internt serverfel|