---
title: Säkerhet för Azure-säkerhet-benchmark v2 – Endpoint Security
description: Säkerhet för Azure Security benchmark v2-slutpunkt
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 937d0b379c4f669e5b57e5053d5e3bffeb091e78
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368961"
---
# <a name="security-control-v2-endpoint-security"></a>Säkerhets kontroll v2: slut punkts säkerhet

Slut punkts säkerhet täcker kontroller i slut punkts identifiering och-svar. Detta inkluderar användning av slut punkts identifiering och-svar (EDR) och skydd mot skadlig kod för slut punkter i Azure-miljöer.

## <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: Använd slut punkts identifiering och-svar (EDR)

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| ES-1 | 8.1 | SI-2, SI-3, SC-3 |

Aktivera funktioner för slut punkts identifiering och-svar (EDR) för servrar och klienter och integrera med SIEM-och säkerhets åtgärds processer.

Microsoft Defender Avancerat skydd tillhandahåller EDR-funktioner som en del av en säkerhets plattform för företags slut punkt för att förhindra, upptäcka, undersöka och svara på avancerade hot. 

- [Översikt över Microsoft Defender Avancerat skydd](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Microsoft Defender ATP-tjänsten för Windows-servrar](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Microsoft Defender ATP-tjänsten för icke-Windows-servrar](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security)

- [Hot information](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Hantering av säkerhetskompatibilitet](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Statushantering](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: Använd centralt hanterade moderna program mot skadlig kod

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| ES-2 | 8.1 | SI-2, SI-3, SC-3 |

Använd en centralt hanterad slut punkt för program mot skadlig kod som kan hantera real tid och regelbunden sökning

Azure Security Center kan automatiskt identifiera användningen av ett antal populära lösningar mot skadlig kod för dina virtuella datorer och rapportera statusen för Endpoint Protection-körningen och göra rekommendationer. 

Microsoft Antimalware för Azure Cloud Services är standard skyddet mot skadlig kod för virtuella Windows-datorer (VM: ar). För virtuella Linux-datorer använder du en lösning från tredje part för program mot skadlig kod.  Du kan också använda Azure Security Center s hot identifiering för data tjänster för att identifiera skadlig kod som laddats upp till Azure Storage-konton. 

- [Så här konfigurerar du Microsoft Antimalware för Cloud Services och Virtual Machines](../fundamentals/antimalware.md)

- [Slut punkts skydds lösningar som stöds](../../security-center/security-center-services.md?tabs=features-windows#supported-endpoint-protection-solutions-)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security)

- [Hot information](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Hantering av säkerhetskompatibilitet](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Statushantering](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3: se till att program och signaturer för skadlig program vara uppdateras

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| ES-3 | 8,2 | SI-2, SI-3 |

Se till att signaturer mot skadlig kod uppdateras snabbt och konsekvent. 

Följ rekommendationerna i Azure Security Center: "Compute &amp; Apps" för att se till att alla slut punkter är uppdaterade med de senaste signaturerna. Microsoft Antimalware kommer automatiskt att installera de senaste signaturerna och motorns uppdateringar som standard. För Linux använder du en lösning mot skadlig kod från tredje part.

- [Så här distribuerar du Microsoft Antimalware för Azure Cloud Services och Virtual Machines](../fundamentals/antimalware.md)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security)

- [Hot information](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Hantering av säkerhetskompatibilitet](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Statushantering](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Endpoint Protection-utvärdering och rekommendationer i Azure Security Center](../../security-center/security-center-endpoint-protection.md)