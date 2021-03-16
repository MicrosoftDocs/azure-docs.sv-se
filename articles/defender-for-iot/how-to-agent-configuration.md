---
title: Konfigurera säkerhetsagenter
description: Lär dig hur du konfigurerar Defender för IoT-säkerhetsagenter för användning med tjänsten Defender for IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: b59d9079b6d0fe0ab3fe61eacf43f3a99eb46686
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495784"
---
# <a name="tutorial-configure-security-agents"></a>Självstudie: Konfigurera säkerhets agenter

Den här artikeln beskriver Defender för IoT-säkerhetsagenter och information om hur du ändrar och konfigurerar dem.

> [!div class="checklist"]
> * Konfigurera säkerhetsagenter
> * Ändra agent beteende genom att redigera dubbla egenskaper
> * Identifiera standard konfiguration

## <a name="agents"></a>Agenter

Defender för IoT-säkerhetsagenter samlar in data från IoT-enheter och utför säkerhets åtgärder för att minimera identifierade sårbarheter. Konfigurationen av säkerhets agenten kan kontrol leras med hjälp av en uppsättning av modulens dubbla egenskaper som du kan anpassa. I allmänhet är sekundära uppdateringar av dessa egenskaper ovanliga.

Defender för IoT: s säkerhets agent är ett JSON-format-objekt. Konfigurationsobjektet är en uppsättning kontrollerbara egenskaper som du kan definiera för att styra agentens beteende.

Med de här konfigurationerna kan du anpassa agenten för varje scenario som krävs. Du kan till exempel automatiskt utesluta vissa händelser eller behålla energi förbrukningen till en minimal nivå genom att konfigurera dessa egenskaper.

Använd konfigurations [schemat](https://aka.ms/iot-security-github-module-schema) Defender för IoT Security Agent för att göra ändringar.

## <a name="configuration-objects"></a>Konfigurations objekt

Egenskaper som är relaterade till varje Defender för IoT-Säkerhetsagenten finns i agentens konfigurations objekt i avsnittet önskade egenskaper i **azureiotsecurity** -modulen.

Ändra konfigurationen genom att skapa och ändra det här objektet i **azureiotsecurity** -modulens dubbla identitet.

Om agentens konfigurations objekt inte finns i **azureiotsecurity** -modulen, anges alla egenskaps värden för säkerhets agenten till default.

```json
"desired": {
  "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
  }
}
```

## <a name="configuration-schema-and-validation"></a>Konfigurations schema och verifiering

Se till att verifiera agent konfigurationen mot [schemat](https://aka.ms/iot-security-github-module-schema). En agent startas inte om konfigurationsobjektet inte matchar schemat.

Om agenten körs ändras konfigurationsobjektet till en icke-giltig konfiguration (konfigurationen matchar inte schemat), men agenten ignorerar den ogiltiga konfigurationen och fortsätter att använda den aktuella konfigurationen.

### <a name="configuration-validation"></a>Konfigurations validering

Defender för IoT Security Agent rapporterar den aktuella konfigurationen i avsnittet rapporterade egenskaper i **azureiotsecurity** -modulens dubbla identitet.
Agenten rapporterar alla tillgängliga egenskaper, om en egenskap inte har angetts av användaren, rapporterar agenten standard konfigurationen.

Jämför värdena som anges i det önskade avsnittet med de värden som rapporteras i avsnittet rapportering för att verifiera konfigurationen.

Om det finns ett matchnings fel mellan de önskade och de rapporterade egenskaperna, kunde agenten inte parsa konfigurationen.

Verifiera dina önskade egenskaper mot [schemat](https://aka.ms/iot-security-github-module-schema), korrigera felen och ange önskade egenskaper igen!

> [!NOTE]
> En konfigurations fel avisering kommer att skickas från agenten om agenten inte kunde parsa den önskade konfigurationen.
> Jämför det rapporterade och önskade avsnittet för att förstå om aviseringen fortfarande gäller

## <a name="editing-a-property"></a>Redigera en egenskap

Alla anpassade egenskaper måste anges inuti agentens konfigurations objekt i **azureiotsecurity** -modulen.
Om du vill använda ett standard egenskaps värde tar du bort egenskapen från konfigurationsobjektet.

### <a name="setting-a-property"></a>Ange en egenskap

1. Leta upp och välj den enhet som du vill ändra i IoT Hub.

1. Klicka på enheten och sedan på **azureiotsecurity** -modulen.

1. Klicka på **modulens identitet, delad**.

1. Redigera egenskaperna som du vill ändra i Defender-IoT-Micro-agenten.

   Om du till exempel vill konfigurera anslutnings händelser som hög prioritet och samla in händelser med hög prioritet var 7: e minut, använder du följande konfiguration.

    ```json
    "desired": {
        "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
            "highPriorityMessageFrequency": {
                "value": "PT7M"
            },
            "eventPriorityConnectionCreate": {
                "value": "High"
            }
        }
    }
    ```

1. Klicka på **Spara**.

### <a name="using-a-default-value"></a>Använda ett standardvärde

Om du vill använda ett standard egenskaps värde tar du bort egenskapen från konfigurationsobjektet.

## <a name="default-properties"></a>Standard egenskaper

Följande tabell innehåller de egenskaper som går att kontrol lera för säkerhets agenter i Defender för IoT.

Standardvärden är tillgängliga i rätt schema i [GitHub](https\://aka.ms/iot-security-module-default).

| Namn| Status | Giltiga värden| Standardvärden| Beskrivning |
|----------|--------|--|-------|----|
|highPriorityMessageFrequency|Krävs: falskt |Giltiga värden: varaktighet i ISO 8601-format |Standardvärde: PT7M |Max tidsintervall innan meddelanden med hög prioritet skickas.|
|lowPriorityMessageFrequency |Krävs: falskt|Giltiga värden: varaktighet i ISO 8601-format |Standardvärde: PT5H |Max tid innan meddelanden med låg prioritet skickas.|
|snapshotFrequency |Kräv: falskt|Giltiga värden: varaktighet i ISO 8601-format |Standardvärde PT13H |Tidsintervall för att skapa ögonblicks bilder av enhets status.|
|maxLocalCacheSizeInBytes |Krävs: falskt |Giltiga värden: |Standardvärde: 2560000, större än 8192 | Maximalt lagrings utrymme (i byte) som tillåts för meddelande-cache för en agent. Högsta mängd utrymme som tillåts för att lagra meddelanden på enheten innan meddelanden skickas.|
|maxMessageSizeInBytes |Krävs: falskt |Giltiga värden: ett positivt tal som är större än 8192, mindre än 262144 |Standardvärde: 204800 |Högsta tillåtna storlek på en agent till moln meddelande. Den här inställningen styr mängden maximalt antal data som skickas i varje meddelande. |
|eventPriority $ {EventName} |Krävs: falskt |Giltiga värden: hög, låg, av |Standardvärden: |Prioritet för varje agent-genererad händelse |

### <a name="supported-security-events"></a>Säkerhets händelser som stöds

|Händelse namn| PropertyName | Standardvärde| Ögonblicks bilds händelse| Information status  |
|----------|-|---------|----|----|
|Diagnostisk händelse|eventPriorityDiagnostic| Av| Falskt| Agent relaterade diagnostiska händelser. Använd den här händelsen för utförlig loggning.|
|Konfigurationsfel |eventPriorityConfigurationError |Låg |Falskt |Agenten kunde inte parsa konfigurationen. Verifiera konfigurationen mot schemat.|
|Statistik över ignorerade händelser |eventPriorityDroppedEventsStatistics |Låg |Sant|Agent relaterad händelse statistik. |
|Ansluten maskin vara|eventPriorityConnectedHardware |Låg |Sant |Ögonblicks bild av all maskin vara som är ansluten till enheten.|
|Lyssnande portar|eventPriorityListeningPorts |Högt |Sant |Ögonblicks bild av alla öppna lyssnings portar på enheten.|
|Skapa process |eventPriorityProcessCreate |Låg |Falskt |Granskar process skapande på enheten.|
|Processen avslutas|eventPriorityProcessTerminate |Låg |Falskt |Granskar avslutning av processen på enheten.|
|Systeminformation |eventPrioritySystemInformation |Låg |Sant |En ögonblicks bild av system information (till exempel: OS eller CPU).|
|Lokala användare| eventPriorityLocalUsers |Högt |Sant|En ögonblicks bild av registrerade lokala användare i systemet. |
|Inloggning|  eventPriorityLogin |Högt|Falskt|Granska inloggnings händelser för enheten (lokala och fjärranslutna inloggningar).|
|Skapa anslutning |eventPriorityConnectionCreate|Låg|Falskt|Granskar TCP-anslutningar som skapats till och från enheten. |
|Konfigurering av brandvägg| eventPriorityFirewallConfiguration|Låg|Sant|Ögonblicks bild av enhets brand Väggs konfiguration (brand Väggs regler). |
|OS-bas linje| eventPriorityOSBaseline| Låg|Sant|Ögonblicks bild av bas linje kontroll för enhetens operativ system.|
|

## <a name="next-steps"></a>Nästa steg

- [Förstå rekommendationer från Defender för IoT](concept-recommendations.md)
- [Utforska Defender för IoT-aviseringar](concept-security-alerts.md)
- [Få åtkomst till råsäkerhetsdata](how-to-security-data-access.md)
