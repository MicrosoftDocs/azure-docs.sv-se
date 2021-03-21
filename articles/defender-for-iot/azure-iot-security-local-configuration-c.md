---
title: Lokal konfiguration av säkerhets agent (C)
description: Lär dig mer om Defender för lokala konfigurations agenter för C.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: elazark
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: v-ekrieg
ms.openlocfilehash: 161bc008076de2bba62cd65c6c9bb106f648aa47
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493245"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>Förstå filen LocalConfiguration.json – C-agent

För säkerhets agenten Defender för IoT används konfigurationer från en lokal konfigurations fil.
Säkerhets agenten läser konfigurationen en gång, vid agent start.
Konfigurationen som påträffades i den lokala konfigurations filen innehåller konfiguration av autentisering och andra Agent-relaterade konfigurationer.
Filen innehåller konfigurationer i nyckel värdes par i JSON-format och konfigurationerna fylls i när agenten installeras.

Filen finns som standard på:/var/ASCIoTAgent/LocalConfiguration.jspå

Ändringar i konfigurations filen sker när agenten startas om.

## <a name="security-agent-configurations-for-c"></a>Konfigurationer för säkerhets agent för C

| Konfigurations namn | Möjliga värden | Information |
|:-----------|:---------------|:--------|
| AgentId | GUID | Agentens unika identifierare |
| TriggerdEventsInterval | ISO8601 sträng | Scheduler-intervall för insamling av utlöst händelser |
| ConnectionTimeout | ISO8601 sträng | Tids gränsen för tids gränsen uppnåddes för anslutningen till IoThub |
| Autentisering | JsonObject | Konfiguration av autentisering. Det här objektet innehåller all information som behövs för autentisering mot IoTHub |
| Identitet | "DPS", "SecurityModule", "Device" | Autentiseringsidentitet – DPS om autentisering görs via DPS, SecurityModule om autentisering görs via Defender-IoT-Micro-agentcredentials eller Device om autentisering görs med autentiseringsuppgifter för enhet |
| AuthenticationMethod | "SasToken", "SelfSignedCertificate" | användar hemlighet för autentisering – Välj SasToken om användnings hemligheten är en symmetrisk nyckel väljer du självsignerat certifikat om hemligheten är ett självsignerat certifikat  |
| FilePath | Sökväg till fil (sträng) | Sökväg till filen som innehåller hemligheten för autentisering |
| HostName | sträng | Värd namnet för Azure IoT Hub. <vanligt vis>. azure-devices.net |
| DeviceId | sträng | ID för enheten (som registrerats i Azure IoT Hub) |
| – | JsonObject | DPS-relaterade konfigurationer |
| IDScope | sträng | ID-omfång för DPS |
| RegistrationId | sträng  | Registrerings-ID för DPS-enhet |
| Loggning | JsonObject | Konfigurationer för agent loggning |
| SystemLoggerMinimumSeverity | 0 <= antal <= 4 | logg meddelanden som är lika med eller större än den här allvarlighets graden loggas till/var/log/syslog (0 är den lägsta allvarlighets graden) |
| DiagnosticEventMinimumSeverity | 0 <= antal <= 4 | logg meddelanden som är lika med eller större än den här allvarlighets graden skickas som diagnostiska händelser (0 är den lägsta allvarlighets graden) |

## <a name="security-agent-configurations-code-example"></a>Kod exempel för konfiguration av säkerhets agent

```json
{
    "Configuration" : {
        "AgentId" : "b97faf0a-0f57-471f-9dab-46a8e1764946",
        "TriggerdEventsInterval" : "PT2M",
        "ConnectionTimeout" : "PT30S",
        "Authentication" : {
            "Identity" : "Device",
            "AuthenticationMethod" : "SasToken",
            "FilePath" : "/path/to/my/SymmetricKey",
            "DeviceId" : "my-device",
            "HostName" : "my-iothub.azure-devices.net",
            "DPS" : {
                "IDScope" : "",
                "RegistrationId" : ""
            }
        },
        "Logging": {
            "SystemLoggerMinimumSeverity": 0,
            "DiagnoticEventMinimumSeverity": 2
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

- Läs [översikten över](overview.md) Defender for IoT-tjänsten
- Läs mer om Defender för IoT- [arkitektur](architecture.md)
- Aktivera [tjänsten](quickstart-onboard-iot-hub.md) Defender för IoT
- Läs [vanliga frågor och svar](resources-frequently-asked-questions.md) om Defender för IoT-tjänsten
- Lär dig hur du kommer åt [rå säkerhets data](how-to-security-data-access.md)
- Förstå [rekommendationer](concept-recommendations.md)
- Förstå säkerhets [aviseringar](concept-security-alerts.md)
