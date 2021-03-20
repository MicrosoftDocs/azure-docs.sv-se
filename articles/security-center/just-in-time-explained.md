---
title: Förstå åtkomst till virtuella datorer just-in-Time i Azure Security Center
description: Det här dokumentet beskriver hur just-in-Time VM Access i Azure Security Center hjälper dig att kontrol lera åtkomsten till dina virtuella Azure-datorer
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 9a52596aa0dd5fa7b9a7226d2ae57259dab08d37
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93285731"
---
# <a name="understanding-just-in-time-jit-vm-access"></a>Förstå VM-åtkomst med just-in-time (JIT)

På den här sidan förklaras principerna bakom Azure Security Center just-in-Time (JIT)-funktionen för VM-åtkomst och logiken bakom rekommendationen.

Information om hur du använder JIT på dina virtuella datorer med hjälp av Azure Portal (antingen Security Center eller Azure Virtual Machines) eller program mässigt finns i [så här skyddar du hanterings portarna med JIT](security-center-just-in-time.md).


## <a name="the-risk-of-open-management-ports-on-a-virtual-machine"></a>Risken för öppna hanterings portar på en virtuell dator

Hot aktörerna aktivt kan komma åt datorer med öppna hanterings portar som RDP eller SSH. Alla dina virtuella datorer är potentiella mål för ett angrepp. När en virtuell dator har komprometterats används den som start punkt för att angripa ytterligare resurser i din miljö.



## <a name="why-jit-vm-access-is-the-solution"></a>Varför den virtuella datorn för JIT-åtkomst är lösningen 

Precis som med alla cybersäkerhet skydds tekniker bör målet vara att minska risken för attacker. I det här fallet innebär det att ha färre öppna portar, särskilt hanterings portar.

Dina legitima användare använder också dessa portar, så det är inte praktiskt att hålla dem stängda.

Azure Security Center erbjuder JIT för att lösa den här dilemma. Med JIT kan du låsa inkommande trafik till dina virtuella datorer, vilket minskar exponeringen för attacker och ger enkel åtkomst till virtuella datorer vid behov.



## <a name="how-jit-operates-with-network-security-groups-and-azure-firewall"></a>Hur JIT fungerar med nätverks säkerhets grupper och Azure-brandvägg

När du aktiverar just-in-Time VM-åtkomst kan du välja portarna på den virtuella datorn som inkommande trafik ska blockeras för. Security Center garanterar att regler för "neka all inkommande trafik" finns för de valda portarna i [nätverks säkerhets gruppen](../virtual-network/network-security-groups-overview.md#security-rules) (NSG) och [Azure brand Väggs regler](../firewall/rule-processing.md). Dessa regler begränsar åtkomsten till dina virtuella Azure-datorers hanterings portar och skyddar dem från angrepp. 

Om det redan finns andra regler för de valda portarna prioriteras dessa befintliga regler för den nya regeln "neka all inkommande trafik". Om det inte finns några befintliga regler på de valda portarna, prioriteras de nya reglerna först i NSG och Azure-brandväggen.

När en användare begär åtkomst till en virtuell dator kontrollerar Security Center att användaren har [Azure-rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) behörigheter för den virtuella datorn. Om begäran har godkänts konfigurerar Security Center NSG: er och Azure-brandväggen för att tillåta inkommande trafik till de valda portarna från den relevanta IP-adressen (eller intervallet) för den angivna tiden. När tiden har gått ut Security Center återställer NSG: er till sina tidigare tillstånd. Anslutningar som redan har upprättats avbryts inte.

> [!NOTE]
> JIT stöder inte virtuella datorer som skyddas av Azure-brandväggar som kontrol leras av [Azure Firewall Manager](../firewall-manager/overview.md).




## <a name="how-security-center-identifies-which-vms-should-have-jit-applied"></a>Hur Security Center identifierar vilka virtuella datorer som ska använda JIT

Diagrammet nedan visar den logik som Security Center gäller när du bestämmer dig för att kategorisera de virtuella datorer som stöds: 

[![JIT-logik (just-in-Time) för virtuell dator (VM)](media/just-in-time-explained/jit-logic-flow.png)](media/just-in-time-explained/jit-logic-flow.png#lightbox)

När Security Center hittar en dator som kan dra nytta av JIT lägger den till den datorn på fliken för rekommenderade **resurser** på fliken. 

![Åtkomst rekommendation för just-in-Time (JIT) virtuell dator (VM)](./media/just-in-time-explained/unhealthy-resources.png)


## <a name="faq---questions-about-just-in-time-virtual-machine-access"></a>Vanliga frågor och svar om just-in-Time-åtkomst till virtuella datorer

### <a name="what-permissions-are-needed-to-configure-and-use-jit"></a>Vilka behörigheter krävs för att konfigurera och använda JIT?

JIT kräver att [Azure Defender för servrar](defender-for-servers-introduction.md) aktive ras i prenumerationen. 

**Reader** -och **SecurityReader** -roller kan båda Visa JIT-status och parametrar.

Om du vill skapa anpassade roller som kan fungera med JIT behöver du information från tabellen nedan.

> [!TIP]
> Om du vill skapa en roll med minst privilegier för användare som behöver begära JIT-åtkomst till en virtuell dator och inte utföra några andra JIT-åtgärder, använder du [set-JitLeastPrivilegedRole-skriptet](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role) från community-sidorna för Security Center GitHub.

| Så här gör du så att en användare kan: | Behörigheter att ange|
| --- | --- |
|Konfigurera eller redigera en JIT-princip för en virtuell dator | *Tilldela följande åtgärder till rollen:*  <ul><li>I omfånget för en prenumeration eller resurs grupp som är associerad med den virtuella datorn:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> I omfånget för en prenumeration eller resurs grupp av virtuell dator: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|Begär JIT-åtkomst till en virtuell dator | *Tilldela följande åtgärder till användaren:*  <ul><li>I omfånget för en prenumeration eller resurs grupp som är associerad med den virtuella datorn:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>I omfånget för en prenumeration eller resurs grupp som är associerad med den virtuella datorn:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  I omfånget för en prenumeration eller resurs grupp eller virtuell dator:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  I omfånget för en prenumeration eller resurs grupp eller virtuell dator:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|
|Läs JIT-principer| *Tilldela följande åtgärder till användaren:*  <ul><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/read`</li><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action`</li><li>`Microsoft.Security/policies/read`</li><li>`Microsoft.Security/pricings/read`</li><li>`Microsoft.Compute/virtualMachines/read`</li><li>`Microsoft.Network/*/read`</li>|
|||





## <a name="next-steps"></a>Nästa steg

Den här sidan förklaras _varför_ just-in-Time (JIT)-åtkomst till virtuell dator (VM) ska användas. 

Gå vidare till instruktions artikeln om du vill veta mer om hur du aktiverar JIT och begär åtkomst till dina JIT-aktiverade virtuella datorer:

> [!div class="nextstepaction"]
> [Så här skyddar du hanterings portar med JIT](security-center-just-in-time.md)