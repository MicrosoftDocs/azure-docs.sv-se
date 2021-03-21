---
title: Rekommenderade principer för Azure-tjänster
description: Beskriver hur du hittar och använder rekommenderade principer för Azure-tjänster, till exempel Azure Virtual Machines.
ms.date: 09/02/2020
ms.topic: conceptual
ms.customer: generated
ms.openlocfilehash: 019541f034ce8f0c3728c38d2ae4425308b4e2a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96350287"
---
# <a name="recommended-policies-for-azure-services"></a>Rekommenderade principer för Azure-tjänster

Kunder som är nya för Azure Policy ofta söker efter vanliga princip definitioner för att hantera och styra sina resurser. Azure Policy **rekommenderade principer** innehåller en fokuserad lista över vanliga princip definitioner som börjar med. De **rekommenderade principerna** för resurser som stöds är inbäddade i Portal upplevelsen för den resursen.

Ytterligare Azure Policy inbyggda program finns i [Azure policy inbyggda definitioner](../samples/built-in-policies.md).

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

De **rekommenderade principerna** för [Azure Virtual Machines](../../../virtual-machines/index.yml) finns på **översikts** sidan för virtuella datorer och under fliken **funktioner** . I _Azure policy_ kortet väljer du texten "inte konfigurerad" eller "# tilldelad" för att öppna ett sido fönster med de rekommenderade principerna. En princip definition som redan är tilldelad en omfattning som den virtuella datorn är en medlem i är nedtonad. Välj de rekommenderade principer som ska tillämpas på den här virtuella datorn och välj **tilldela principer** för att skapa en tilldelning för var och en.

Som en organisation når förfallo tid med att [organisera sina resurser och en resurs-hierarki](/azure/cloud-adoption-framework/ready/azure-best-practices/organize-subscriptions)rekommenderar vi att du överför dessa princip tilldelningar från en per resurs till prenumerations-eller [hanterings grupps](../../management-groups/index.yml) nivån.

### <a name="azure-virtual-machines-recommended-policies"></a>Rekommenderade principer för Azure Virtual Machines

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Granska virtuella datorer utan haveri beredskap har kon figurer ATS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Granska virtuella datorer som inte har någon katastrof återställning konfigurerad. Om du vill veta mer om haveri beredskap går du till [https://aka.ms/asr-doc](../../../site-recovery/index.yml) . |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |
|[Granska virtuella datorer som inte använder hanterade diskar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |Den här principen granskar virtuella datorer som inte använder hanterade diskar |händelse |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |
|[Azure Backup ska vara aktiverat för Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Den här principen hjälper dig att granska om Azure Backup-tjänsten är aktive rad för alla virtuella datorer. Azure Backup är en kostnads effektiv lösning för säkerhets kopiering med enkel klickning som fören klar data återställningen och är enklare att aktivera än andra moln tjänster för säkerhets kopiering. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="next-steps"></a>Nästa steg

- Granska exempel i [Azure policy exempel](../samples/index.md).
- Granska [Förstå policy-effekter](./effects.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).