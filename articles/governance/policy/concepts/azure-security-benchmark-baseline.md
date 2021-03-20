---
title: Azures säkerhets bas linje för Azure Policy
description: Azure Policy säkerhets bas linje ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: azure-policy
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 9916ad6d3b6cb1a63d34004915666226b7836490
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740365"
---
# <a name="azure-security-baseline-for-azure-policy"></a>Azures säkerhets bas linje för Azure Policy

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark](../../../security/benchmarks/overview.md) till Azure policy. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure. Innehållet grupperas efter **domänerna för regelefterlevnad** och **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Azure policy. **Kontroller** som inte är tillämpliga på Azure policy har uteslutits. Om du vill se hur Azure Policy helt mappar till Azures säkerhets mätning, se den [fullständiga Azure policy mappnings filen för säkerhets bas linjen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

En mappning av Azures säkerhets benchmark-kontroller till inbyggda princip definitioner via det inbyggda initiativet finns i [regler för efterlevnad: Azure Security benchmark](../samples/azure-security-benchmark.md).

Azure Policy använder _ägarskapet_ på ansvar i stället för _ansvar_. Mer information om _ägarskap_ finns i [Azure policy princip definitioner](./definition-structure.md#type) och [delat ansvar i molnet](../../../security/fundamentals/shared-responsibility.md).

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Azure policy använder aktivitets loggar som aktive ras automatiskt för att inkludera händelse källa, datum, användare, tidsstämpel, käll adresser, mål adresser och andra användbara element.

- [Samla in plattforms loggar och mått med Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)

- [Förstå loggning och olika logg typer i Azure](/azure/azure-monitor/platform/platform-logs-overview)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security benchmark: identitet och Access Control](../../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton. Använd Azure Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton. Du kan också aktivera en just-in-Time/bara-tillräcklig-åtkomst-lösning med hjälp av [Azure Active Directory (Azure AD) Privileged Identity Management](../../../active-directory/privileged-identity-management/pim-configure.md) privilegierade roller eller [Azure Resource Manager](../../../azure-resource-manager/management/overview.md).

**Ansvar**: Kund

**Azure Security Center övervakning**: [Azures säkerhets benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) är standard policy initiativ för Security Center och är grunden för [Security Center rekommendationer](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy-definitioner som är relaterade till den här kontrollen aktive ras automatiskt av Security Center. Aviseringar som är relaterade till den här kontrollen kan kräva en [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan för de relaterade tjänsterna.

**Azure policy inbyggda definitioner – Microsoft. GuestConfiguration**:

[!INCLUDE [Resource Policy for Microsoft.GuestConfiguration 3.3](../../../../includes/policy/standards/asb/rp-controls/microsoft.guestconfiguration-3-3.md)]

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Använd säkra, Azure-hanterade arbets stationer för administrativa uppgifter

**Vägledning**: Använd Privileged Access-arbetsstationer (Paw) med multifaktorautentisering konfigurerad för att logga in på och konfigurera Azure-resurser.

- [Lär dig mer om arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Så här aktiverar du multifaktorautentisering i Azure](../../../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../../../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser 

**Vägledning**: Använd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att kontrol lera åtkomsten till Azure policy.

- [Azure RBAC-behörigheter i Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview#azure-rbac-permissions-in-azure-policy)

- [Så här konfigurerar du Azure RBAC](../../../role-based-access-control/role-assignments-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med aktivitets loggar för att skapa aviseringar för när ändringar sker i Azure policy.

- [Så här skapar du aviseringar för Azure aktivitets logg händelser](/azure/azure-monitor/platform/alerts-activity-log)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser som ger metadata till att logiskt organisera dem i en taxonomi. Använd Azure Policy _ändra_ -effekter för att rapportera om och genomdriva efterlevnad och konsekvent etikett styrning.

- [Självstudie: skapa och hantera principer](../tutorials/create-and-manage.md)

- [Självstudie: hantera märkes styrning](../tutorials/govern-tags.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla inventering av godkända Azure-resurser

**Vägledning**: skapa en inventering av godkända princip definitioner och princip tilldelningar enligt organisationens behov.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i dina prenumerationer.

- [Konfigurera och hantera Azure Policy](../tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
