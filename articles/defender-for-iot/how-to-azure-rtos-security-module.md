---
title: Konfigurera och anpassa säkerhetsmodulen för Azure återställnings tider
description: Lär dig hur du konfigurerar och anpassar din säkerhetsmodul för Azure återställnings tider.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2021
ms.author: shhazam
ms.openlocfilehash: 524286fa7a923485d0085fb63f3ef9669db1a4d5
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449823"
---
# <a name="configure-and-customize-defender-iot-micro-agent-for-azure-rtos-ga"></a>Konfigurera och anpassa Defender-IoT-Micro-agent för Azure återställnings tider GA

I den här artikeln beskrivs hur du konfigurerar Defender-IoT-Micro-agenten för din Azure återställnings tider-enhet för att uppfylla dina krav på nätverk, bandbredd och minne.

Du måste välja en mål distributions fil som har ett `*.dist` tillägg från `netxduo/addons/azure_iot/azure_iot_security_module/configs` katalogen.  

När du använder en CMake Compilation-miljö måste du ange en kommando rads parameter för `IOT_SECURITY_MODULE_DIST_TARGET` det valda värdet. Till exempel `-DIOT_SECURITY_MODULE_DIST_TARGET=RTOS_BASE`.

I en IAR eller annan icke-CMake, måste du lägga till `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/` sökvägen till eventuella kända sökvägar. Till exempel `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/RTOS_BASE`.

Använd följande fil för att konfigurera enhetens beteende.

**netxduo/addons/azure_iot/azure_iot_security_module/Inc/configs/ \<target distribution> /asc_config. h**

I en CMake Compilation-miljö måste du ändra standard konfigurationen genom att redigera `netxduo/addons/azure_iot/azure_iot_security_module/configs/<target distribution>.dist` filen. Använd följande CMake-format `set(ASC_XXX ON)` eller följande fil `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/asc_config.h` för alla andra miljöer. Till exempel `#define ASC_XXX`.

Standard beteendet för varje konfiguration finns i följande tabeller: 

## <a name="general"></a>Allmänt

| Namn | Typ | Standardvärde | Information |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | Sträng | Defender-IoT-Micro-agent | Enhetens unika identifierare.  |
| SECURITY_MODULE_VERSION_ (STÖRRE) (DEL) (KORRIGERING)  | Antal | 3.2.1 | Versionen. |
| ASC_SECURITY_MODULE_SEND_MESSAGE_RETRY_TIME  | Antal  | 3 | Hur lång tid som Defender-IoT-Micro-agenten ska ta emot säkerhets meddelandet efter ett fel. (i sekunder) |
| ASC_SECURITY_MODULE_PENDING_TIME  | Antal | 300 | Den väntande tiden för Defender-IoT-Micro-agent (i sekunder). Statusen ändras till pausa om tiden överskrids.. |

## <a name="collection"></a>Samling

| Namn | Typ | Standardvärde | Information |
| - | - | - | - |
| ASC_FIRST_COLLECTION_INTERVAL | Antal  | 30  | Insamlarens intervall förskjutning för start samlingen. Under starten kommer värdet att läggas till i system samlingen för att undvika att skicka meddelanden från flera enheter samtidigt.  |
| ASC_HIGH_PRIORITY_INTERVAL | Antal | 10 | Insamlarens grupp intervall med hög prioritet (i sekunder). |
| ASC_MEDIUM_PRIORITY_INTERVAL | Antal | 30 | Insamlarens grupp intervall för medelhög prioritet (i sekunder). |
| ASC_LOW_PRIORITY_INTERVAL | Antal | 145 440  | Insamlarens grupp intervall för låg prioritet (i sekunder). |

#### <a name="collector-network-activity"></a>Insamlarens nätverks aktivitet

Om du vill anpassa konfigurationen för insamlarens nätverks aktivitet använder du följande:

| Namn | Typ | Standardvärde | Information |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Boolesk | falskt | Filtrerar `TCP` nätverks aktiviteten. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Boolesk | falskt | Filtrerar `UDP` nätverks aktivitets händelser. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Boolesk | falskt | Filtrerar `ICMP` nätverks aktivitets händelser. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | Boolesk | true | Fångar endast inkommande unicast-paket. När det är inställt på falskt fångas även både broadcast och multicast. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_SEND_EMPTY_EVENTS  | Boolesk  | falskt  | Skickar ett tomt insamlare. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | Antal | 64 | Det maximala antalet IPv4-nätverks händelser som ska lagras i minnet. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | Antal | 64  | Det maximala antalet IPv6-nätverks händelser att lagra i minnet. |

### <a name="collectors"></a>Insamlare
| Namn | Typ | Standardvärde | Information |
| - | - | - | - |
| ASC_COLLECTOR_HEARTBEAT_ENABLED | Boolesk | ON | Aktiverar pulsslags insamlaren. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ENABLED  | Boolesk | ON | Aktiverar insamlaren av nätverks aktivitet. |
| ASC_COLLECTOR_SYSTEM_INFORMATION_ENABLED | Boolesk | ON | Aktiverar system informations insamlaren.  |

Andra konfigurations flaggor är avancerade och har funktioner som inte stöds. Kontakta supporten om du vill ändra detta eller för mer information.
 
## <a name="supported-security-alerts-and-recommendations"></a>Säkerhets aviseringar och rekommendationer som stöds

Defender-IoT-Micro-agenten för Azure återställnings tider har stöd för vissa säkerhets aviseringar och rekommendationer. Se till att [Granska och anpassa relevanta aviserings-och rekommendations värden](concept-rtos-security-alerts-recommendations.md) för din tjänst.

## <a name="log-analytics-optional"></a>Log Analytics (valfritt)

Du kan aktivera och konfigurera Log Analytics för att undersöka enhets händelser och-aktiviteter. Läs om hur du konfigurerar och använder [Log Analytics med tjänsten Defender for IoT](how-to-security-data-access.md#log-analytics) för mer information. 

## <a name="next-steps"></a>Nästa steg

- Granska och anpassa säkerhetsmodulen för [säkerhets aviseringar och rekommendationer](concept-rtos-security-alerts-recommendations.md) för Azure återställnings tider
- Se [säkerhetsmodulen för Azure återställnings tider API](azure-rtos-security-module-api.md) vid behov.
