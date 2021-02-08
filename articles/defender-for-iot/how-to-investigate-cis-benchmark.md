---
title: Undersök benchmark-rekommendation för CIS
titleSuffix: Azure Defender for IoT
description: Utföra grundläggande och avancerade undersökningar baserade på bas linje rekommendationer för operativ system.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 2f68ebedb229f7295bc9c5dcc3b3349808970e8c
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809947"
---
# <a name="investigate-os-baseline-based-on-cis-benchmark-recommendation"></a>Undersök rekommendationen för OS-baseline (baserad på CIS-benchmark) 

Utföra grundläggande och avancerade undersökningar baserade på bas linje rekommendationer för operativ system.

## <a name="basic-os-baseline-security-recommendation-investigation"></a>Undersökning av grundläggande säkerhets rekommendationer för operativ systemets bas linje  

Du kan undersöka bas linje rekommendationer för operativ system genom att gå till Azure Defender för IoT-portalen under **IoT Hub**. Mer information finns i så här [undersöker du säkerhets rekommendationer](quickstart-investigate-security-recommendations.md).

## <a name="advanced-os-baseline-security-recommendation-investigation"></a>Undersökning av avancerad säkerhets rekommendation för operativ systemets bas linje  

I det här avsnittet beskrivs hur du bättre förstår test resultaten för operativ systemets bas linje och hur du frågar händelser i Azure Log Analytics.  

Undersökningen för avancerad säkerhets rekommendation för operativ systemet stöds bara med Log Analytics. Anslut Defender för IoT till en Log Analytics arbets yta innan du fortsätter. Mer information om avancerade säkerhets rekommendationer för säkerhets bas linjer finns i så här [konfigurerar du Azure Defender för IoT agent-baserad lösning](how-to-configure-agent-based-solution.md).

Så här frågar du efter säkerhets händelser i IoT i Log Analytics för aviseringar:

1. Gå till sidan **aviseringar** .

1. Välj **Undersök rekommendationer i Log Analytics arbets yta**.

Så här frågar du efter säkerhets händelser i IoT i Log Analytics:

1. Gå till sidan **rekommendationer** .

1. Välj **Undersök rekommendationer i Log Analytics arbets yta**.

1. Välj **Visa information om bas linje regler för åtgärds system (OS)** från sidan med **rekommendations information** för att se information om en speciell enhet.

   :::image type="content" source="media/how-to-investigate-cis-benchmark/recommendation-details.png" alt-text="Se information om en speciell enhet."::: 

För att fråga dina IoT-säkerhetshändelser i Log Analytics arbets ytan direkt:

1. Gå till sidan **loggar** .

    :::image type="content" source="media/how-to-investigate-cis-benchmark/logs.png" alt-text="Välj loggar i det vänstra fönstret.":::

1. Välj **Undersök aviseringarna** eller Välj alternativet **Undersök aviseringarna i Log Analytics** från alla säkerhets rekommendationer eller aviseringar.   

## <a name="useful-queries-to-investigate-the-os-baseline-resources"></a>Användbara frågor för att undersöka bas linje resurser för operativ system: 

> [!Note]
> Ersätt `<device-id>` med namnen som du gav enheten i var och en av följande frågor. 


### <a name="retrieve-the-latest-information"></a>Hämta den senaste informationen

- **Enhets flottans fel**: kör följande fråga för att hämta den senaste informationen om kontroller som misslyckats i enhets flottan: 

    ```azurecli
    let lastDates = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    summarize TimeStamp=max(TimeStamp) by DeviceId; 
    
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId  | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    project DeviceId, event.CceId, event.Description 
    ```
 
- **Fel** i en enhet – kör följande fråga för att hämta den senaste informationen om kontroller som misslyckades på en speciell enhet:  

    ```azurecli
    let LastEvents = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    where DeviceId == "<device-id>" | 
    
    top 1 by TimeStamp desc | 
    
    project IoTRawEventId; 
    
    LastEvents | join kind=leftouter SecurityIoTRawEvent on IoTRawEventId | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    project DeviceId, event.CceId, event.Description 
    ```

- **Fel** i en enhet – kör den här frågan för att hämta den senaste informationen om kontroller som har ett fel på en angiven enhet: 

    ```azurecli
    let LastEvents = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    where DeviceId == "<device-id>" | 
    
    top 1 by TimeStamp desc | 
    
    project IoTRawEventId; 
    
    LastEvents | join kind=leftouter SecurityIoTRawEvent on IoTRawEventId | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "ERROR" | 
    
    project DeviceId, event.CceId, event.Description 
    ```
 
- **Uppdatera enhets listan för enhets flottan som misslyckades med en speciell kontroll** – kör den här frågan för att hämta en uppdaterad lista över enheter (i enhets flottan) som inte godkänts i en speciell kontroll:  
 
    ```azurecli
    let lastDates = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    summarize TimeStamp=max(TimeStamp) by DeviceId; 
    
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId  | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    where event.CceId contains "6.2.8" | 
    
    project DeviceId; 
    ```
 
## <a name="next-steps"></a>Nästa steg

[Undersök säkerhets rekommendationer](quickstart-investigate-security-recommendations.md).
 