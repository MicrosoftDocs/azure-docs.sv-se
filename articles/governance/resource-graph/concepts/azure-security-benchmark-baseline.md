---
title: Azures säkerhets bas linje för Azure Resource Graph
description: Säkerhets bas linjen för Azure Resource Graph ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: resource-graph
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ad8968fdb6548da29a031f0e44bd3671f67b5553
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557725"
---
# <a name="azure-security-baseline-for-azure-resource-graph"></a>Azures säkerhets bas linje för Azure Resource Graph

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark version 1,0](../../../security/benchmarks/overview-v1.md) till Azure Resource Graph. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure.
Innehållet grupperas efter de **säkerhets kontroller** som definierats av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Azure Resource Graph. **Kontroller** som inte gäller Azure Resource Graph har uteslutits.

 
Om du vill se hur Azure Resource Graph fullständigt mappar till Azures säkerhets benchmark, kan du läsa mer i den [fullständiga Azure Resource Graph-filen för säkerhets bas linje mappning](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security benchmark: identitet och Access Control](../../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure Resource Graph ger till gång till resurs typer och egenskaper baserat på Azure-rollbaserade åtkomst kontroller (Azure RBAC). Granska och granska åtkomst beviljad till säkerhets objekt (användare, grupper och tjänst konton) regelbundet för att se till att frågor returnerar resultat för lämpliga resurser.

- [Behörigheter i Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph)

- [Så här använder du granskningar av Azure Identity Access](../../../active-directory/governance/access-reviews-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../../../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser 

**Vägledning**: Använd Azure RBAC för att kontrol lera åtkomsten till data och resurser. Om du vill använda Azure Resource Graph måste du också ha nödvändig åtkomst till de resurser som du vill fråga. Den här åtkomsten bör begränsas till skrivskyddad och beviljas endast för nödvändig personal.

- [Behörigheter i Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph)

- [Så här konfigurerar du Azure RBAC](../../../role-based-access-control/role-assignments-rest.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga efter och identifiera alla resurser som stöds i dina prenumerationer, hanterings grupper och klient organisationer. Se till att du har rätt behörigheter i din klient organisation och att du kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

- [Så här skapar du frågor med Azure Resource Graph](../first-query-portal.md)

- [Förstå Azure RBAC](../../../role-based-access-control/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla inventering av godkända Azure-resurser

**Vägledning**: skapa en inventering av godkända Azure-resurser och godkänd program vara för beräknings resurser enligt organisationens behov. Använd Azure Resource Graph för att fråga efter godkända Azure-resurser och ändrings historik (för hands version) för att granska ögonblicks bilder och se vad som har ändrats.

- [Fråga Azure-resurser med Azure Resource Graph](../first-query-portal.md)

- [Hämta resursändringar](../how-to/get-resource-changes.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure Resource Graph för att fråga efter och identifiera resurser i dina prenumerationer, hanterings grupper och klient organisationer. Se till att alla Azure-resurser i miljön är godkända.

- [Fråga Azure-resurser med Azure Resource Graph](../first-query-portal.md)

- [Exempel: Start frågor för Azure Resource Graph](../samples/starter.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](../../../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../../../security/benchmarks/security-baselines-overview.md)