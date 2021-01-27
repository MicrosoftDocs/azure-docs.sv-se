---
title: Säkerhets kontroller för Azure Relay
description: I den här artikeln finns en check lista över inbyggda säkerhets kontroller för utvärdering av Azure Relay.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: ce5053366ac1d3536a152610d8ed7f76fad62b84
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919586"
---
# <a name="security-controls-for-azure-relay"></a>Säkerhets kontroller för Azure Relay

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure Relay.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Kommentarer | Dokumentation |
|---|---|--|--|
| Stöd för privat slut punkt| Ja |  |   |
| Stöd för nätverks isolering och brand vägg| Ja |  |   |
| Stöd för Tvingad tunnel trafik| Ej tillämpligt | Relä är TLS-tunneln  |   |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Kommentarer| Dokumentation |
|---|---|--|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | |   |
| Loggning och granskning av kontroll-och hanterings plan| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml). |   |
| Loggning och granskning av data planet| Ja | Lyckad/misslyckad anslutning och fel och loggad.  |   |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Kommentarer| Dokumentation |
|---|---|--|--|
| Autentisering| Ja | Via SAS. | [Azure Relay autentisering och auktorisering](relay-authentication-and-authorization.md) |
| Auktorisering|  Ja | Via SAS. | [Azure Relay autentisering och auktorisering](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Kommentarer | Dokumentation |
|---|---|--|--|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar |  Ej tillämpligt | Relay är en webbsocket och bevarar inte data. |   |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Inga | Använder endast Microsoft TLS-certifikat.  |   |
| Kryptering på kolumn nivå (Azure Data Services)| Ej tillämpligt | |   |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet kryptering)| Ja | Tjänsten kräver TLS. |   |
| Krypterade API-anrop| Ja | HTTPS. |


## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Kommentarer| Dokumentation |
|---|---|--|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml).|   |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).