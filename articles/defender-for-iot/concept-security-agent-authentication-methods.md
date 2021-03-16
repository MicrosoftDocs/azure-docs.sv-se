---
title: Autentiseringsmetoder för säkerhets agent
description: Lär dig mer om de olika autentiseringsmetoder som är tillgängliga när du använder tjänsten Defender för IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: 4aeaa703b5e3203eeb7bc355051e0e8f48f898ac
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103494129"
---
# <a name="security-agent-authentication-methods"></a>Autentiseringsmetoder för säkerhets agent

I den här artikeln beskrivs de olika autentiseringsmetoderna som du kan använda med AzureIoTSecurity-agenten för att autentisera med IoT Hub.

För varje enhet som har registrerats till Defender för IoT i IoT Hub krävs en Defender-IoT-Micro-agent. För att autentisera enheten kan Defender för IoT använda en av två metoder. Välj den metod som passar bäst för din befintliga IoT-lösning.

- Alternativet SecurityModule
- Enhets alternativ

## <a name="authentication-methods"></a>Autentiseringsmetoder

De två metoderna för AzureIoTSecurity-agenten Defender for IoT för att utföra autentisering:

- **Defender-IoT-Micro-agent-** autentiseringsläge<br>
Agenten autentiseras med hjälp av-identiteten Defender-IoT-Micro-agent oberoende av enhets identiteten.
Använd den här typen av autentisering om du vill att säkerhets agenten ska använda en dedikerad autentiseringsmetod via Defender-IoT-Micro-agent (endast symmetrisk nyckel).

- Autentiseringsläge för **enhet**<br>
I den här metoden autentiserar säkerhets agenten först med enhets identiteten. Efter den inledande autentiseringen utför Defender för IoT-agenten ett **rest** -anrop till IoT Hub med hjälp av REST API med enhetens autentiseringsdata. Defender for IoT-agenten begär sedan autentiseringsmetoden Defender-IoT-Micro-agent och data från IoT Hub. I det sista steget utför Defender för IoT-agenten en autentisering mot modulen Defender för IoT.

Använd den här autentiseringstypen om du vill att säkerhets agenten ska återanvända en befintlig autentiseringsmetod för enheten (självsignerat certifikat eller symmetrisk nyckel).

Se [installations parametrar för säkerhets agent](#security-agent-installation-parameters) för att lära dig hur du konfigurerar.

## <a name="authentication-methods-known-limitations"></a>Kända begränsningar för autentiseringsmetoder

- **SecurityModule** autentiseringsläget stöder endast symmetrisk nyckel autentisering.
- CA-Signed certifikat stöds inte av autentiseringsläge för **enhet** .

## <a name="security-agent-installation-parameters"></a>Installations parametrar för säkerhets agent

När du [distribuerar en säkerhets agent](how-to-deploy-agent.md)måste autentiserings information tillhandahållas som argument.
Dessa argument beskrivs i följande tabell.

|Linux-parameter namn | Windows-parameter namn | Stenografisk parameter |Beskrivning|Alternativ|
|---------------------|---------------|---------|---------------|---------------|
|autentisering-identitet|AuthenticationIdentity|aui|Autentiseringsidentitet| **SecurityModule** eller **enhet**|
|autentisering-metod|AuthenticationMethod|aum|Autentiseringsmetod|**SymmetricKey** eller **SelfSignedCertificate**|
|fil Sök väg|FilePath|f|Absolut fullständig sökväg för filen som innehåller certifikatet eller den symmetriska nyckeln| |
|värd namn|HostName|HN|FQDN för IoT Hub|Exempel: ContosoIotHub.azure-devices.net|
|enhets-ID|DeviceId|di|Enhets-ID|Exempel: MyDevice1|
|certifikat – plats-typ|CertificateLocationKind|cl|Lagrings plats för certifikat|**Lokalfil** eller **butik**|
|

När du använder skriptet installera säkerhets agent utförs följande konfiguration automatiskt. Redigera konfigurations filen manuellt om du vill redigera säkerhets agentens autentisering manuellt.

## <a name="change-authentication-method-after-deployment"></a>Ändra autentiseringsmetod efter distribution

När du distribuerar en säkerhets agent med ett installations skript skapas en konfigurations fil automatiskt.

Om du vill ändra autentiseringsmetoder efter distributionen krävs manuell redigering av konfigurations filen.

### <a name="c-based-security-agent"></a>C#-baserad säkerhets agent

Redigera _Authentication.config_ med följande parametrar:

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>C-baserad säkerhets agent

Redigera _LocalConfiguration.js_ med följande parametrar:

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>Se även

- [Översikt över säkerhets agenter](security-agent-architecture.md)
- [Distribuera säkerhets agent](how-to-deploy-agent.md)
- [Få åtkomst till råsäkerhetsdata](how-to-security-data-access.md)
