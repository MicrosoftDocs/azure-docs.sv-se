---
title: Azures säkerhets kontroll – inventering och till gångs hantering
description: Hantering av Azure-säkerhetskontroller och till gångs hantering
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: f3079ea475c20e3ae0e78319d6c6b24ee579fd0c
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521313"
---
# <a name="security-control-inventory-and-asset-management"></a>Säkerhets kontroll: inventering och till gångs hantering

Rekommendationer för inventering och till gångs hantering fokuserar på att åtgärda problem som rör aktivt hantering (inventering, spårning och korrigering) av alla Azure-resurser så att endast auktoriserade resurser får åtkomst, och obehöriga och ohanterade resurser identifieras och tas bort.

## <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 6.1 | 1,1, 1,2, 1,3, 1,4, 9,1, 12,1 | Kund |

Använd Azure Resource Graph för att fråga/identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk, portar och protokoll osv.) i din prenumeration (er).  Se till att du har rätt (Läs) behörigheter i din klient organisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via resurs diagram, rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser som går framåt.

- [Så här skapar du frågor med Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription)

- [Förstå Azure RBAC](../../role-based-access-control/overview.md)

## <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 6,2 | 1.5 | Kund |

Använd taggar till Azure-resurser som ger metadata till att logiskt organisera dem i en taxonomi.

- [Skapa och använda Taggar](../../azure-resource-manager/management/tag-resources.md)

## <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 6.3 | 1.6 | Kund |

Använd taggning, hanterings grupper och separata prenumerationer, vid behov, för att organisera och spåra till gångar. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

- [Så här skapar du ytterligare Azure-prenumerationer](../../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](../../azure-resource-manager/management/tag-resources.md)

## <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla en inventering av godkända Azure-resurser

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 6.4 | 2.1 | Kund |

Skapa en inventering av godkända Azure-resurser och godkänd program vara för beräknings resurser enligt våra organisations behov.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 6.5 | 2,3, 2,4 | Kund |

Använd Azure Policy för att ange begränsningar för den typ av resurser som kan skapas i dina prenumerationer.

Använd Azure Resource Graph för att fråga/identifiera resurser i sina prenumerationer.  Se till att alla Azure-resurser som finns i miljön är godkända.

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Graph](../../governance/resource-graph/first-query-portal.md)

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 6.6 | 2,3, 2,4 | Kund |

Använd inventering av virtuella Azure-datorer för att automatisera insamling av information om all program vara på Virtual Machines. Program varu namn, version, utgivare och uppdaterings tid är tillgängliga från Azure Portal. Du kan få åtkomst till installations datum och annan information genom att aktivera diagnostik på gästnivå och ta Windows-händelseloggen till en Log Analytics-arbetsyta.

- [Så här aktiverar du inventering av virtuella Azure-datorer](../../automation/automation-tutorial-installed-software.md)

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 6.7 | 2.5 | Kund |

Använd Azure Security Center fil integritets övervakning (Ändringsspårning) och inventering av virtuella datorer för att identifiera all program vara som är installerad på Virtual Machines. Du kan implementera en egen process för att ta bort otillåten program vara. Du kan också använda en lösning från tredje part för att identifiera program som inte godkänts.

- [Så här använder du övervakning av fil integritet](../../security-center/security-center-file-integrity-monitoring.md)

- [Förstå Azure Ändringsspårning](../../automation/change-tracking/overview.md)

- [Så här aktiverar du inventering av virtuella Azure-datorer](../../automation/automation-tutorial-installed-software.md)

## <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 6.8 | 2,6 | Kund |

Använd Azure Security Center adaptiva program kontroller för att säkerställa att endast auktoriserade program körs och all obehörig program vara blockeras från att köras på Azure Virtual Machines.

- [Använda Azure Security Center adaptiva program kontroller](../../security-center/security-center-adaptive-application.md)

## <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 6,9 | 2,6 | Kund |

Använd Azure Policy för att begränsa vilka tjänster du kan etablera i din miljö.

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](../../governance/policy/samples/index.md)

## <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: underhåll en inventering av godkända program varu titlar

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 6,10 | 2.7 | Kund |

Använd Azure Security Center adaptiva program kontroller för att ange vilka filtyper en regel kan eller kanske inte tillämpas på.

Implementera en lösning från tredje part om detta inte uppfyller kravet.

- [Använda Azure Security Center adaptiva program kontroller](../../security-center/security-center-adaptive-application.md)

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 6,11 | 2.9 | Kund |

Använd villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azures resurser Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azures resurs hanterare](../../role-based-access-control/conditional-access-azure-management.md)

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 6,12 | 2.9 | Kund |

Beroende på typen av skript kan du använda operativ systemets speciella konfigurationer eller resurser från tredje part för att begränsa användarnas möjlighet att köra skript i Azure Compute-resurser.  Du kan också använda Azure Security Center adaptiva program kontroller för att säkerställa att endast auktoriserade program körs och all obehörig program vara blockeras från att köras på Azure Virtual Machines.

- [Så här styr du körning av PowerShell-skript i Windows-miljöer](/powershell/module/microsoft.powershell.security/set-executionpolicy)

- [Använda Azure Security Center adaptiva program kontroller](../../security-center/security-center-adaptive-application.md)

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 6,13 | 2.9 | Kund |

Program vara som krävs för affärs åtgärder, men som kan innebära högre risk för organisationen, bör isoleras inom den egna virtuella datorn och/eller det virtuella nätverket och tillräckligt säkert med antingen en Azure-brandvägg eller en nätverks säkerhets grupp.

- [Så här skapar du ett virtuellt nätverk](../../virtual-network/quick-create-portal.md)

- [Så här skapar du en NSG med en säkerhets konfiguration](../../virtual-network/tutorial-filter-network-traffic.md)


## <a name="next-steps"></a>Nästa steg

- Se nästa säkerhets kontroll: [säker konfiguration](security-control-secure-configuration.md)