---
title: Hantera hybrid infrastruktur i stor skala med Azure Arc
description: Lär dig hur du effektivt hanterar kundernas datorer och Kubernetes-kluster utanför Azure.
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: ec32389cd7444405580530a00c8b7c5bc48bcd56
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419344"
---
# <a name="manage-hybrid-infrastructure-at-scale-with-azure-arc"></a>Hantera hybrid infrastruktur i stor skala med Azure Arc

Som tjänst leverantör kan du ha registrerat flera kund klienter i [Azure-Lighthouse](../overview.md). Med Azure Lighthouse kan tjänst leverantörer utföra åtgärder i skala över flera Azure Active Directory (Azure AD)-klienter samtidigt, och göra hanterings uppgifter mer effektiva.

[Azure båg](../../azure-arc/overview.md) hjälper till att förenkla komplexa och distribuerade miljöer i både lokalt och i flera moln, vilket möjliggör distribution av Azure-tjänster var som helst och för att utöka Azure-hanteringen till alla infrastrukturer.

Med [Azure Arc-aktiverade servrar](../../azure-arc/servers/overview.md)kan kunder hantera alla Windows-och Linux-datorer som finns utanför Azure i företagets nätverk, på samma sätt som de hanterar interna virtuella Azure-datorer. När en hybriddator länkas till Azure ansluts den och behandlas som en resurs i Azure. Tjänste leverantörer kan sedan hantera dessa datorer som inte är Azure-datorer tillsammans med sina kunders Azure-resurser.

[Azure Arc Enabled Kubernetes](../../azure-arc/kubernetes/overview.md) låter kunder ansluta och konfigurera Kubernetes-kluster i eller utanför Azure. När ett Kubernetes-kluster är kopplat till Azure-bågen visas det i Azure Portal, med ett Azure Resource Manager-ID och en hanterad identitet. Kluster är anslutna till Azures standard prenumerationer, finns i en resurs grupp och kan ta emot Taggar precis som andra Azure-resurser.

Det här avsnittet innehåller en översikt över hur tjänst leverantörer kan använda Azure Arc-aktiverade servrar och Azure Arc-aktiverade Kubernetes på ett skalbart sätt för att hantera kundernas hybrid miljö, med insyn i alla hanterade kund klienter.

> [!TIP]
> Även om vi refererar till tjänst leverantörer och kunder i det här avsnittet gäller den här vägledningen även för [företag som använder Azure-Lighthouse för att hantera flera klienter](../concepts/enterprise.md).

## <a name="manage-hybrid-servers-at-scale-with-azure-arc-enabled-servers"></a>Hantera hybrid servrar i skala med Azure Arc-aktiverade servrar

Som tjänst leverantör kan du hantera lokala Windows Server-eller Linux-datorer utanför Azure som dina kunder har anslutit till prenumerationen med hjälp av den [Azure-anslutna dator agenten](../../azure-arc/servers/agent-overview.md).

När du visar resurser för en delegerad prenumeration i Azure Portal ser du att de anslutna datorerna är märkta med **Azure Arc**. Du kan hantera dessa anslutna datorer med hjälp av Azure-konstruktioner, till exempel Azure Policy och tagga, på samma sätt som du hanterar kundens Azure-resurser. Du kan också arbeta mellan kund klienter för att hantera alla anslutna hybrid datorer tillsammans.

Du kan till exempel [Se till att samma uppsättning principer tillämpas på kunders hybrid datorer](../../azure-arc/servers/learn/tutorial-assign-policy-portal.md). Du kan också använda Azure Security Center för att övervaka efterlevnad i alla kunders hybrid miljöer eller [använda Azure Monitor för att samla in data direkt från dina hybrid datorer](../../azure-arc/servers/learn/tutorial-enable-vm-insights.md) till en Log Analytics-arbetsyta. [Tillägg för virtuella datorer](../../azure-arc/servers/manage-vm-extensions.md) kan distribueras till virtuella Windows-och Linux-datorer som inte är Azure-datorer, vilket fören klar hanteringen av kundens hybrid datorer.

## <a name="manage-hybrid-kubernetes-clusters-at-scale-with-azure-arc-enabled-kubernetes"></a>Hantera hybrid Kubernetes-kluster i stor skala med Azure Arc-aktiverad Kubernetes

Du kan hantera Kubernetes-kluster som har [anslutits till en kunds prenumeration med Azure Arc](../../azure-arc/kubernetes/connect-cluster.md), precis som om de kördes i Azure.

Om kunden har skapat ett [tjänst objekts konto för att publicera Kubernetes-kluster till Azure Arc](../../azure-arc/kubernetes/create-onboarding-service-principal.md), kan du komma åt det här tjänstens huvud konto för att publicera och hantera kluster. Detta kan göras av användare i den hanterings klient som har beviljats inbyggd Azure-Kubernetes för Azure-båge när prenumerationen som innehåller tjänstens huvud konto har registrerats [i Azure Lighthouse](onboard-customer.md).

Du kan distribuera [konfigurationer](../../azure-arc/kubernetes/use-gitops-connected-cluster.md) och [Helm-diagram](../../azure-arc/kubernetes/use-gitops-with-helm.md) med GitOps för anslutna kluster.

Du kan också övervaka anslutna kluster med Azure Monitor och [använda Azure policy för att tillämpa klusterkonfigurationer i stor skala](../../azure-arc/kubernetes/use-azure-policy.md).

## <a name="next-steps"></a>Nästa steg

- Utforska JumpStarts och exemplen i [Azure Arc GitHub-lagringsplatsen](https://github.com/microsoft/azure_arc).
- Lär dig mer om [scenarier som stöds för Azure Arc-aktiverade servrar](../../azure-arc/servers/overview.md#supported-scenarios).
- Lär dig mer om [Kubernetes-distributioner som stöds av Azure-bågen](../../azure-arc/kubernetes/overview.md#supported-kubernetes-distributions).
- Lär dig hur du [distribuerar en princip i stor skala](policy-at-scale.md).
- Lär dig hur du [använder Azure Monitor loggar i stor skala](monitor-at-scale.md).
