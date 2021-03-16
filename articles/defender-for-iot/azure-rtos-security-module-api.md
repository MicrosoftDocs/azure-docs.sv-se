---
title: Defender-IoT-Micro-agent för Azure återställnings tider-API
description: Referens-API för Defender-IoT-Micro-agent för Azure återställnings tider.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: 9a285636ba10f7ca0668f597d0e9016cff73494a
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103494299"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-api-preview"></a>Defender-IoT-Micro-agent för Azure återställnings tider-API (för hands version)

Detta API är avsett att användas med endast Defender-IoT-Micro-agenten för Azure återställnings tider. Ytterligare resurser finns i [återställnings tider GitHub-resursen Defender-IoT-Micro-agent för Azure](https://github.com/azure-rtos/azure-iot-preview/releases). 

## <a name="enable-defender-iot-micro-agent-for-azure-rtos"></a>Aktivera Defender-IoT-Micro-agent för Azure återställnings tider

**nx_azure_iot_security_module_enable**

### <a name="prototype"></a>Prototyp

```c
UINT nx_azure_iot_security_module_enable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Beskrivning

Den här rutinen aktiverar under systemet Azure IoT Defender-IoT-Micro-agent. En intern tillstånds dator hanterar insamling av säkerhets händelser och skickar dem till Azure IoT Hub. Endast en NX_AZURE_IOT_SECURITY_MODULE instans krävs och krävs för att hantera data insamling.

### <a name="parameters"></a>Parametrar

| Namn | Beskrivning |
|---------|---------|
| nx_azure_iot_ptr [i]    | En pekare till en `NX_AZURE_IOT` .  |

### <a name="return-values"></a>Returvärden

|Returvärden  |Beskrivning |
|---------|---------|
|NX_AZURE_IOT_SUCCESS|   Azure IoT-säkerhetsmodulen har Aktiver ATS.     |
|NX_AZURE_IOT_FAILURE   |  Det gick inte att aktivera Azure IoT Security-modulen på grund av ett internt fel.    |
|NX_AZURE_IOT_INVALID_PARAMETER   |  Säkerhetsmodulen kräver en giltig #NX_AZURE_IOT instans.      |

### <a name="allowed-from"></a>Tillåten från

Konversation

## <a name="disable-azure-iot-defender-iot-micro-agent"></a>Inaktivera Azure IoT Defender-IoT-Micro-agent

**nx_azure_iot_security_module_disable**


### <a name="prototype"></a>Prototyp

```c
UINT nx_azure_iot_security_module_disable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Beskrivning

Den här rutinen inaktiverar under systemet Azure IoT Defender-IoT-Micro-agent.

### <a name="parameters"></a>Parametrar

| Namn | Beskrivning |
|---------|---------|
| nx_azure_iot_ptr [i]    | En pekare till `NX_AZURE_IOT` . Om NULL är inaktive rad singleton-instansen. |

### <a name="return-values"></a>Returvärden

|Returvärden  |Beskrivning |
|---------|---------|
|NX_AZURE_IOT_SUCCESS     |   Lyckades när modulen Azure IoT Security har inaktiverats.      |
|NX_AZURE_IOT_INVALID_PARAMETER   |  Azure IoT Hub instansen skiljer sig från den sammansatta singleton-instansen.       |
|NX_AZURE_IOT_FAILURE    |  Det gick inte att inaktivera Azure IoT-säkerhetsmodulen på grund av ett internt fel.       |

### <a name="allowed-from"></a>Tillåten från

Konversation


## <a name="next-steps"></a>Nästa steg

Mer information om hur du kommer igång med Azure återställnings tider Defender-IoT-Micro-agent finns i följande artiklar:

- Läs [Översikt över](iot-security-azure-rtos.md)Defender for IoT återställnings tider Defender-IoT-Micro-agent.
