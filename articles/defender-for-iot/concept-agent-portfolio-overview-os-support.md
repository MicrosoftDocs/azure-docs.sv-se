---
title: Översikt över agent portfölj och OS-stöd
description: Azure Defender för IoT tillhandahåller en stor portfölj av agenter baserat på enhets typen.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/20/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: e2897018d1695bde665e1d1aca180e5268851a0b
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120155"
---
# <a name="agent-portfolio-overview-and-os-support"></a>Översikt över agent portfölj och OS-stöd 

Azure Defender för IoT tillhandahåller en stor portfölj av agenter baserat på enhets typen. 

## <a name="standalone-agent"></a>Fristående agent

Den fristående agenten täcker de flesta Linux-operativsystem, som kan distribueras som ett binärt paket eller som en källkod som kan införlivas som en del av den inbyggda program varan och möjliggöra ändring och anpassning baserat på kund behov. Ett exempel på OS-support: 

| Operativsystem | AMD64 | ARM32v7 |
|--|--|--|
| Debian 9 | ✓ | ✓ |
| Ubuntu 18.04 | ✓ |  |
| Ubuntu 20.04 | ✓ |  |

För mer information, stöd för operativ system eller för att begära åtkomst till käll koden så att du kan lägga till den som en del av enhetens inbyggda program vara, kontakta din konto hanterare eller skicka ett e-postmeddelande till <defender_micro_agent@microsoft.com> . 

## <a name="azure-rtos-micro-agent"></a>Azure återställnings tider Micro-agent

Azure Defender for IoT Micro-agenten tillhandahåller en omfattande och lätt säkerhets lösning för enheter som använder Azure återställnings tider. Azure Defender for IoT Micro agent ger täckning för vanliga hot och potentiella skadliga aktiviteter på återställnings tider-enheter (Real Time Opera ting system). Micro agent levereras som en del av Azure återställnings tider NetX Duo-komponenten och övervakar enhetens nätverks aktivitet. 

Azure Defender for IoT Micro agent har skapats som en del av Azure återställnings tider NetX Duo-komponenten och övervakar enhetens nätverks aktivitet. Mikroagenten består av en omfattande och lätt säkerhets lösning som ger täckning för vanliga hot och potentiella skadliga aktiviteter på återställnings tider-enheter (Real Time Opera ting system).

## <a name="next-steps"></a>Nästa steg

Läs mer om [Översikt över fristående mikroagenter ](concept-standalone-micro-agent-overview.md).