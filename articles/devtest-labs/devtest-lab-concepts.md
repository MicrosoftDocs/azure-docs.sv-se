---
title: DevTest Labs-koncept | Microsoft Docs
description: Lär dig de grundläggande begreppen i DevTest Labs och hur det kan göra det enkelt att skapa, hantera och övervaka virtuella Azure-datorer
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b184f835ee99d47ddb843e5b02136299c26bfcdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92327866"
---
# <a name="devtest-labs-concepts"></a>DevTest Labs-koncept
## <a name="overview"></a>Översikt
Följande lista innehåller viktiga begrepp och definitioner för DevTest Labs:

## <a name="labs"></a>Labbar
Ett labb är en infrastruktur som omfattar en grupp resurser, t. ex. Virtual Machines (VM), som gör att du kan hantera dessa resurser bättre genom att ange gränser och kvoter.

## <a name="virtual-machine"></a>Virtuell dator
En virtuell Azure-dator är en av flera olika typer av [skalbara dator resurser](/azure/architecture/guide/technology-choices/compute-decision-tree) som Azure erbjuder. Virtuella Azure-datorer ger dig flexibiliteten i virtualisering utan att behöva köpa och underhålla den fysiska maskin vara som kör den, även om du fortfarande behöver underhålla den virtuella datorn genom att utföra vissa uppgifter, till exempel konfigurera, korrigera och installera program varan som körs på den.

[Översikt över virtuella Windows-datorer i Azure](../virtual-machines/windows/overview.md) ger dig information om vad du bör tänka på innan du skapar en virtuell dator, hur du skapar den och hur du hanterar den.

## <a name="claimable-vm"></a>Virtuell dator som går att frigöra
En virtuell dator med Azure-anspråk är en virtuell dator som kan användas av alla labb användare med behörigheter. En labb administratör kan förbereda virtuella datorer med vissa bas avbildningar och artefakter och spara dem i en delad pool. En labb användare kan sedan begära en fungerande virtuell dator från poolen när de behöver en med den angivna konfigurationen.

En virtuell dator som kan krävas är inte inlednings vis tilldelad en viss användare, men visas i varje användares lista under "anspråk bara virtuella datorer". När en virtuell dator har ansökts av en användare flyttas den till "mina virtuella datorer" och kan inte längre krävas av någon annan användare.

## <a name="environment"></a>Miljö
I DevTest Labs refererar en miljö till en samling Azure-resurser i ett labb. [Det här blogg inlägget](./devtest-lab-faq.md#blog-post) beskriver hur du skapar miljöer för flera virtuella datorer från Azure Resource Manager mallar.

## <a name="base-images"></a>Basavbildningar
Bas avbildningar är VM-avbildningar med alla verktyg och inställningar förinstallerade och konfigurerade för att snabbt skapa en virtuell dator. Du kan etablera en virtuell dator genom att välja en befintlig bas och lägga till en artefakt för att installera test agenten. Du kan sedan spara den etablerade virtuella datorn som en bas så att basen kan användas utan att behöva installera om test agenten för varje etablering av den virtuella datorn.

## <a name="artifacts"></a>Artifacts
Artefakter används för att distribuera och konfigurera ditt program när en virtuell dator har tillhandahållits. Artefakter kan vara:

* Verktyg som du vill installera på den virtuella datorn, till exempel agenter, Fiddler och Visual Studio.
* Åtgärder som du vill köra på den virtuella datorn, till exempel kloning av en lagrings platsen.
* Program som du vill testa.

Artefakter är [Azure Resource Manager](../azure-resource-manager/management/overview.md) JSON-filer som innehåller instruktioner för att utföra distribution och tillämpa konfiguration.

## <a name="artifact-repositories"></a>Artefakt databaser
Artefakt databaser är git-databaser där artefakter checkas in. Artefakt databaser kan läggas till i flera labb i din organisation, vilket möjliggör åter användning och delning.

## <a name="formulas"></a>Formler
Formler, förutom bas avbildningar, tillhandahåller en mekanism för snabb VM-etablering. En formel i DevTest Labs är en lista över standard egenskaps värden som används för att skapa en virtuell labb dator.
Med formler kan virtuella datorer med samma uppsättning egenskaper, till exempel bas avbildning, storlek på virtuell dator, virtuellt nätverk och artefakter, skapas utan att du behöver ange dessa egenskaper varje gång. När du skapar en virtuell dator från en formel kan du använda standardvärdena som-är eller ändra.

## <a name="policies"></a>Principer
Med hjälp av principer kan du kontrol lera kostnaderna i labbet. Du kan till exempel skapa en princip för att automatiskt stänga av virtuella datorer baserat på ett definierat schema.

## <a name="caps"></a>Kepsar
Caps är en mekanism för att minimera spill i labbet. Du kan till exempel ange en Cap för att begränsa antalet virtuella datorer som kan skapas per användare eller i ett labb.

## <a name="security-levels"></a>Säkerhetsnivåer
Säkerhets åtkomst bestäms av Azure-rollbaserad åtkomst kontroll (Azure RBAC). För att förstå hur Access fungerar, hjälper det att förstå skillnaderna mellan en behörighet, en roll och ett omfång som definieras av Azure RBAC.

* Behörighet – en behörighet är en definierad åtkomst till en viss åtgärd (t. ex. Läs åtkomst till alla virtuella datorer).
* Roll – en roll är en uppsättning behörigheter som kan grupperas och tilldelas till en användare. Rollen *prenumerations ägare* har till exempel åtkomst till alla resurser i en prenumeration.
* Omfattning – ett omfång är en nivå i hierarkin för en Azure-resurs, till exempel en resurs grupp, ett enda labb eller hela prenumerationen.

Inom ramen för DevTest Labs finns det två typer av roller för att definiera användar behörigheter: labb ägare och labb användare.

* Labb ägare – en labb ägare har åtkomst till alla resurser i labbet. En labb ägare kan därför ändra principer, läsa och skriva virtuella datorer, ändra det virtuella nätverket och så vidare.
* Labb användare – en labb användare kan visa alla labb resurser, till exempel virtuella datorer, principer och virtuella nätverk, men inte ändra principer eller virtuella datorer som har skapats av andra användare.

Information om hur du skapar anpassade roller i DevTest Labs finns i artikeln [bevilja användar behörighet till vissa labb principer](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Eftersom omfattningar är hierarkiska, beviljas de behörigheterna automatiskt vid varje lågnivå omfång när en användare har behörighet för ett visst omfång. Om en användare till exempel har tilldelats rollen som prenumerations ägare, har de åtkomst till alla resurser i en prenumeration, som omfattar alla virtuella datorer, alla virtuella nätverk och alla labb. En prenumerations ägare ärver därför automatiskt rollen som labb ägare. Motsatt är dock inte sant. En labb ägare har åtkomst till ett labb, vilket är ett lägre omfång än prenumerations nivån. Därför kommer en labb ägare inte att kunna se virtuella datorer eller virtuella nätverk eller några resurser som ligger utanför labbet.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar
Alla begrepp som diskuteras i den här artikeln kan konfigureras med hjälp av Azure Resource Manager mallar, som gör att du kan definiera infrastrukturen/konfigurationen för din Azure-lösning och distribuera den på ett konsekvent tillstånd.

[Förstå strukturen och syntaxen för Azure Resource Manager mallar](../azure-resource-manager/templates/template-syntax.md#template-format) beskriver strukturen för en Azure Resource Manager-mall och de egenskaper som är tillgängliga i de olika avsnitten i en mall.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
[Skapa ett labb i DevTest Labs](devtest-lab-create-lab.md)
