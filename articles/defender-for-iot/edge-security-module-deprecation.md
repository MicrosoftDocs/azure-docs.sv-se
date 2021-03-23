---
title: Funktions support och pensionering
description: Defender för IoT fortsätter att stödja C, C# och Edge fram till den 1 mars 2022.
ms.date: 1/21/2021
ms.topic: how-to
ms.openlocfilehash: 782e2e8ab0c54e21da643ca73f647a7ea21e4223
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784534"
---
# <a name="feature-support-and-retirement"></a>Funktions support och pensionering

I den här artikeln beskrivs Azure Defender för IoT-funktioner och stöd för olika funktioner i Defender för IoT.

## <a name="defender-for-iot-c-c-and-edge-defender-iot-micro-agent-deprecation"></a>Defender för IoT C, C# och Edge Defender-IoT-Micro-agent-utfasning

Den nya Micro-agenten ersätter den aktuella C, C# och Edge Defender-IoT-Micro-agenten.  

Den nya Micro-agenten baseras på kunskapen och erfarenheten från att avsluta Defender-IoT-Micro-agent-utveckling, kunder och samarbets partners med fyra viktiga förbättringar: 

- **Säkerhets värde för djup**: den nya agenten kommer att köras på värdnivå, vilket ger bättre insyn i de underliggande åtgärderna för enheten och för att möjliggöra bättre säkerhets täckning.

- **Förbättrad enhets prestanda och minskad** storlek: uppnås av ett litet RAM-minne och minnes utrymme för ROM-minnet samt låg CPU-förbrukning.  

- **Plug and Play**: den nya Micro-agenten har inga beroenden på kernel-nivå längre och alla dess program varu beroenden tillhandahålls som en del av paketet. Micro Agent stöder gemensam CPU-arkitektur.

- **Lätt att distribuera**: Micro agent har stöd för olika distributions modeller, via käll kod och som ett binärt paket. 

### <a name="timeline"></a>Tidslinje 

Defender för IoT fortsätter att stödja C, C# och Edge fram till den 1 mars 2022. 

## <a name="micro-agent-preview-support"></a>Stöd för för hands version av Micro agent

I för hands versionen kan Micro-agenten uppleva ändringar utan föregående meddelande.

## <a name="next-steps"></a>Nästa steg

Kolla [av API: er för IoT-sensor och Management Console](references-work-with-defender-for-iot-apis.md).