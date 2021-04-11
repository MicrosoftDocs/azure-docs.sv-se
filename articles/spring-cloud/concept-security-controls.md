---
title: Säkerhetskontroller för Azure Spring Cloud-tjänsten
description: Använd säkerhets kontroller som är inbyggda i Azure våren Cloud service.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 62a0bd19f6b10bbe6561f5587ed85d4d1e5880b3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878622"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Säkerhetskontroller för Azure Spring Cloud-tjänsten

**Den här artikeln gäller för:** ✔️ Java ✔️ C #

Säkerhets kontroller är inbyggda i moln tjänsten Azure våren.

En säkerhets kontroll är en kvalitet eller funktion i en Azure-tjänst som bidrar till tjänstens möjlighet att förhindra, identifiera och reagera på säkerhets risker.  För varje kontroll använder vi *Ja* eller *Nej* för att ange om den finns för närvarande för tjänsten.  Vi använder *saknas* för en kontroll som inte är tillämplig för tjänsten. 

**Säkerhets kontroller för data skydd**

| Säkerhets kontroll | Ja/nej | Kommentarer | Dokumentation |
|:-------------|:-------|:-------------------------------|:----------------------|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar | Ja | Användare som överfört källa och artefakter, konfigurations Server inställningar, appinställningar och data i beständig lagring lagras i Azure Storage, som automatiskt krypterar innehållet i vila.<br><br>Config server cache, binärfiler för körbara filer som skapats utifrån Uppladdad källa och program loggar under programmets livs längd sparas till Azure Managed disk, som automatiskt krypterar innehållet i vila.<br><br>Behållar avbildningar som skapats från användarens överförda källa sparas i Azure Container Registry som automatiskt krypterar avbildnings innehållet i vila. | [Azure Storage-kryptering av vilande data](../storage/common/storage-service-encryption.md)<br><br>[Kryptering på Server sidan av Azure Managed disks](../virtual-machines/disk-encryption.md)<br><br>[Lagrings avbildnings lagring i Azure Container Registry](../container-registry/container-registry-storage.md) |
| Kryptering i tillfällig | Ja | Användar programmets offentliga slut punkter använder HTTPS för inkommande trafik som standard. |  |
| Krypterade API-anrop | Ja | Hanterings anrop för att konfigurera Azure våren Cloud service sker via Azure Resource Manager-anrop via HTTPS. | [Azure Resource Manager](../azure-resource-manager/index.yml) |

**Säkerhets kontroller för nätverks åtkomst**

| Säkerhets kontroll | Ja/nej | Kommentarer | Dokumentation |
|:-------------|:-------|:-------------------------------|:----------------------|
| Service tag | Ja | Använd **AzureSpringCloud** service tag för att definiera utgående nätverks åtkomst kontroller i [nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md#security-rules) eller [Azure-brandvägg](../firewall/service-tags.md)för att tillåta trafik till Azure våren Cloud-program.<br><br>*Obs:* För närvarande är det bara ny Azure våren Cloud Service-instans som skapats efter 2020/07/14 som stöder **AzureSpringCloud** service tag. | [Tjänsttaggar](../virtual-network/service-tags-overview.md) |

## <a name="next-steps"></a>Nästa steg

* [Snabb start: Distribuera ditt första Azure våren Cloud-program](spring-cloud-quickstart.md)