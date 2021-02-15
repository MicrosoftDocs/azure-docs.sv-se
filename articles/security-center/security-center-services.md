---
title: Azure Security Center funktioner baserat på operativ system, maskin typ och moln
description: Lär dig mer om vilka Azure Security Center funktioner som är tillgängliga enligt deras operativ system, typ och moln distribution.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2021
ms.author: memildin
ms.openlocfilehash: 7034d1b5f06fcbcead79d85df10042eee3581b0b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369364"
---
# <a name="feature-coverage-for-machines"></a>Funktions täckning för datorer

De två flikarna nedan visar funktionerna i Azure Security Center som är tillgängliga för virtuella Windows-och Linux-datorer och-servrar.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Funktioner som stöds för virtuella datorer och servrar <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Windows-datorer**](#tab/features-windows)

|**Funktion**|**Azure Virtual Machines**|**Skalningsuppsättningar för virtuella Azure-datorer**|**Azure Arc-aktiverade datorer**|**Azure Defender krävs**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender för slut punkts integrering](security-center-wdatp.md)|✔</br>(i versioner som stöds)|✔</br>(i versioner som stöds)|✔|Ja|
|[Beteende analys av virtuella datorer (och säkerhets aviseringar)](alerts-reference.md)|✔|✔|✔|Ja|
|[Filbaserad säkerhets aviseringar](alerts-reference.md#alerts-windows)|✔|✔|✔|Ja|
|[Nätverksbaserade säkerhets aviseringar](other-threat-protections.md#network-layer)|✔|✔|-|Ja|
|[Just-in-time-åtkomst till virtuella datorer](security-center-just-in-time.md)|✔|-|-|Ja|
|[Ursprunglig sårbarhets bedömning](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|Ja|
|[Övervakning av filintegritet](security-center-file-integrity-monitoring.md)|✔|✔|✔|Ja|
|[Anpassningsbara programkontroller](security-center-adaptive-application.md)|✔|-|✔|Ja|
|[Nätverks karta](security-center-network-recommendations.md#network-map)|✔|✔|-|Ja|
|[Anpassningsbar nätverkshärdning](security-center-adaptive-network-hardening.md)|✔|-|-|Ja|
|[Instrument panel för regelefterlevnad & rapporter](security-center-compliance-dashboard.md)|✔|✔|✔|Ja|
|Rekommendationer och hot skydd på Docker-värdbaserade IaaS-behållare|-|-|-|Ja|
|Utvärdering av OS-korrigeringsfiler som saknas|✔|✔|✔|Azure: Nej<br><br>Arc-aktiverat: Ja|
|Utvärdering av felkonfigurationer för säkerhet|✔|✔|✔|Azure: Nej<br><br>Arc-aktiverat: Ja|
|[Endpoint Protection-utvärdering](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: Nej<br><br>Arc-aktiverat: Ja|
|Disk krypterings bedömning|✔</br>(för [scenarier som stöds](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Inga|
|Sårbarhets bedömning från tredje part|✔|-|✔|Inga|
|[Nätverks säkerhets utvärdering](security-center-network-recommendations.md)|✔|✔|-|Inga|


### <a name="linux-machines"></a>[**Linux-datorer**](#tab/features-linux)

|**Funktion**|**Azure Virtual Machines**|**Skalningsuppsättningar för virtuella Azure-datorer**|**Azure Arc-aktiverade datorer**|**Azure Defender krävs**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender för slut punkts integrering](security-center-wdatp.md)|-|-|-|-|
|[Beteende analys av virtuella datorer (och säkerhets aviseringar)](./azure-defender.md)|✔</br>(i versioner som stöds)|✔</br>(i versioner som stöds)|✔|Ja|
|[Filbaserad säkerhets aviseringar](alerts-reference.md#alerts-windows)|-|-|-|Ja|
|[Nätverksbaserade säkerhets aviseringar](other-threat-protections.md#network-layer)|✔|✔|-|Ja|
|[Just-in-time-åtkomst till virtuella datorer](security-center-just-in-time.md)|✔|-|-|Ja|
|[Ursprunglig sårbarhets bedömning](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|Ja|
|[Övervakning av filintegritet](security-center-file-integrity-monitoring.md)|✔|✔|✔|Ja|
|[Anpassningsbara programkontroller](security-center-adaptive-application.md)|✔|-|✔|Ja|
|[Nätverks karta](security-center-network-recommendations.md#network-map)|✔|✔|-|Ja|
|[Anpassningsbar nätverkshärdning](security-center-adaptive-network-hardening.md)|✔|-|-|Ja|
|[Instrument panel för regelefterlevnad & rapporter](security-center-compliance-dashboard.md)|✔|✔|✔|Ja|
|Rekommendationer och hot skydd på Docker-värdbaserade IaaS-behållare|✔|✔|✔|Ja|
|Utvärdering av OS-korrigeringsfiler som saknas|✔|✔|✔|Azure: Nej<br><br>Arc-aktiverat: Ja|
|Utvärdering av felkonfigurationer för säkerhet|✔|✔|✔|Azure: Nej<br><br>Arc-aktiverat: Ja|
|[Endpoint Protection-utvärdering](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Inga|
|Disk krypterings bedömning|✔</br>(för [scenarier som stöds](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Inga|
|Sårbarhets bedömning från tredje part|✔|-|✔|Inga|
|[Nätverks säkerhets utvärdering](security-center-network-recommendations.md)|✔|✔|-|Inga|

--- 


> [!TIP]
>Om du vill experimentera med funktioner som bara är tillgängliga med Azure Defender kan du registrera dig för en 30-dagars utvärderings version. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Slut punkts skydds lösningar som stöds <a name="endpoint-supported"></a>

Följande tabell innehåller en matris med:

 - Om du kan använda Azure Security Center för att installera varje lösning åt dig.
 - Vilka lösningar för slut punkts skydd Security Center kan identifiera. Om en Endpoint Protection-lösning från den här listan identifieras rekommenderar Security Center inte att installera en.

Information om när rekommendationer genereras för vart och ett av dessa skydd finns i [Endpoint Protection utvärdering och rekommendationer](security-center-endpoint-protection.md).

| Slutpunktsskydd| Plattformar | Installation av Security Center | Security Center Discovery |
|------|------|-----|-----|
| Microsoft Defender Antivirus| Windows Server 2016 eller senare| Nej, inbyggd i OS| Ja |
| System Center Endpoint Protection (Microsoft-programvara mot skadlig kod) | Windows Server 2012 R2, 2012, 2008 R2 (se anmärkning nedan) | Via tillägg | Ja |
| Trend Micro – djup säkerhet | Windows Server-familjen  | Inga | Ja |
| Symantec v12.1.1100+| Windows Server-familjen  | Inga | Ja |
| McAfee v10 + | Windows Server-familjen  | Inga | Ja |
| McAfee v10 + | Linux Server-serien  | Inga | Ja |
| Sophos v9 +| Linux Server-serien  | Inga | Ja |

> [!NOTE]
> Identifiering av System Center Endpoint Protection (SCEP) på en virtuell dator med Windows Server 2008 R2 kräver SCEP för att installeras efter PowerShell (v 3.0 eller senare).



## <a name="feature-support-in-government-clouds"></a>Funktions stöd i offentliga moln

| Tjänst/funktion | US Gov | Kina, gov |
|------|:----:|:----:|
|[Just-in-Time VM-åtkomst](security-center-just-in-time.md) (1)|✔|✔|
|[Övervakning av fil integritet](security-center-file-integrity-monitoring.md) (1)|✔|✔|
|[Anpassningsbara program kontroller](security-center-adaptive-application.md) (1)|✔|✔|
|[Anpassad nätverks härdning](security-center-adaptive-network-hardening.md) (1)|-|-|
|[Docker-värd härdning](harden-docker-hosts.md) (1)|✔|✔|
|[Integrerad sårbarhets bedömning för datorer](deploy-vulnerability-assessment-vm.md) (1)|-|-|
|[Microsoft Defender för slut punkt](harden-docker-hosts.md) (1)|✔|-|
|[Anslut AWS-konto](quickstart-onboard-aws.md) (1)|-|-|
|[Anslut GCP-konto](quickstart-onboard-gcp.md) (1)|-|-|
|[Löpande export](continuous-export.md)|✔|✔ (2)|
|[Automatisering av arbets flöde](workflow-automation.md)|✔|✔|
|[Undantags regler för rekommendation](exempt-resource.md)|-|-|
|[Regler för undertryckning av aviseringar](alerts-suppression-rules.md)|✔|✔|
|[E-postmeddelanden för säkerhets aviseringar](security-center-provide-security-contact-details.md)|✔|✔|
|[Till gångs lager](asset-inventory.md)|✔|✔|
|[Azure Defender för App Service](defender-for-app-service-introduction.md)|-|-|
|[Azure Defender för Storage](defender-for-storage-introduction.md)|✔|-|
|[Azure Defender för SQL](defender-for-sql-introduction.md)|✔|✔ (2)|
|[Azure Defender för Key Vault](defender-for-key-vault-introduction.md)|-|-|
|[Azure Defender för Resource Manager](defender-for-resource-manager-introduction.md)|-|-|
|[Azure Defender för DNS](defender-for-dns-introduction.md)|-|-|
|[Azure Defender för containerregister](defender-for-container-registries-introduction.md)|✔ (2)|✔ (2)|
|[Azure Defender för Kubernetes](defender-for-kubernetes-introduction.md)|✔|✔|
|[Kubernetes arbets belastnings skydd](kubernetes-workload-protections.md)|-|-|
|||

(1) kräver **Azure Defender för servrar**

(2) delvis


## <a name="next-steps"></a>Nästa steg

- Lär dig hur [Security Center samlar in data med hjälp av Log Analytics-agenten](security-center-enable-data-collection.md).
- Lär dig hur [Security Center hanterar och skyddar data](security-center-data-security.md).
- Granska de [plattformar som har stöd för Security Center](security-center-os-coverage.md).