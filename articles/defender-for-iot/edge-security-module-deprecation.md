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
ms.openlocfilehash: 2779a73d3a5f77e3a3b144309baf6d2788565443
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809971"
---
# <a name="feature-support-and-retirement"></a>Funktions support och pensionering

I den här artikeln beskrivs Azure Defender för IoT-funktioner och stöd för olika funktioner i Defender för IoT.

## <a name="defender-for-iot-c-c-and-edge-security-module-deprecation"></a>Defender för infasningen av säkerhetsmodulen i IoT C, C# och Edge

Den nya Micro-agenten kommer att ersätta den aktuella säkerhetsmodulen C, C# och Edge.  

Den nya Micro-agenten är baserad på kunskapen och erfarenhet som samlas in från den utlämnande säkerhetsmodulen, kunder och partner feedback med fyra viktiga förbättringar: 

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