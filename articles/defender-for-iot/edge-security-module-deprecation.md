---
title: Funktions support och pensionering
titleSuffix: Azure Defender for IoT
description: Defender för IoT fortsätter att stödja C, C# och Edge fram till den 1 mars 2022.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/21/2021
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: a33e7ff230292d4ceb14610e3cf00935dc1a67a0
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493177"
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