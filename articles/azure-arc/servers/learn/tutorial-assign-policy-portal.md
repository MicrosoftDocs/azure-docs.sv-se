---
title: Självstudie – Ny principtilldelning med Azure Portal
description: I den här självstudien använder Azure Portal för att skapa en Azure Policy tilldelning för att identifiera icke-kompatibla resurser.
ms.topic: tutorial
ms.date: 04/21/2021
ms.openlocfilehash: 20cb8125a157a3ce97c88278c99f90936b449b65
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831662"
---
# <a name="tutorial-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Självstudie: Skapa en principtilldelning för att identifiera icke-kompatibla resurser

Det första steget mot att förstå kompatibilitet i Azure är att identifiera dina resursers status. Azure Policy stöder granskning av status för din Arc-aktiverade server med gästkonfigurationsprinciper. Gästkonfigurationsprinciper tillämpar inte konfigurationer, de granskar bara inställningar på datorn. I den här självstudien går vi igenom hur du skapar och tilldelar en princip och identifierar vilka Arc-aktiverade servrar som inte har Log Analytics-agenten installerad.

I slutet av den här processen kan du identifiera datorer som inte har Log Analytics-agenten för Windows eller Linux installerad. De är _inkompatibla_ med principtilldelningen.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

I den här självstudien skapar du en principtilldelning och tilldelar _\[ förhandsversionen]: Log Analytics-agenten ska installeras på linux-Azure Arc-datorns_ principdefinition.

1. Starta Azure Policy-tjänsten i Azure Portal genom att klicka på **Alla tjänster** och sedan söka efter och välja **Princip**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/search-policy.png" alt-text="Sök efter princip i Alla tjänster" border="false":::

1. Välj **Tilldelningar** till vänster på sidan Azure Policy. En tilldelning är en princip som tilldelats ett specifikt område.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assignment.png" alt-text="Sidan Välj tilldelningar på sidan Principöversikt" border="false":::

1. Välj **Tilldela princip** längst upp på sidan **Princip – Tilldelningar**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assign-policy.png" alt-text="Tilldela en principdefinition från sidan Tilldelningar" border="false":::

1. På sidan **Tilldela princip** väljer du **Omfång** genom att klicka på ellipsen och antingen välja en hanteringsgrupp eller en prenumeration. Du kan även välja en resursgrupp. Ett omfång avgör vilka resurser eller grupper med resurser som principtilldelningen används i. Klicka sedan på **Välj** längst ned på sidan **Omfång**.

   I det här exemplet används **prenumerationen Parnell Flyg.** Din prenumeration skiljer sig.

1. Resurser som kan uteslutas baserat på **omfång**. **Undantag** startar på en nivå som är lägre än nivån för **Omfång**. **Undantag** är valfria, så lämna det tomt just nu.

1. Välj ellipsen **Principdefinition** för att öppna listan med tillgängliga definitioner. Azure Policy har inbyggda principdefinitioner som du kan använda. Många är tillgängliga, till exempel:

   - Framtvinga tagg och dess värde
   - Använd tagg och dess värde
   - Ärva en tagg från resursgruppen om den saknas

   En ofullständig lista över tillgängliga inbyggda principer finns i [Azure Policy exempel](../../../governance/policy/samples/index.md).

1. Sök igenom listan med principdefinitioner för att hitta _\[ förhandsversionen]: Log Analytics-agenten_ ska installeras på definitionen för dina Windows Azure Arc-datorer om du har aktiverat Arc-aktiverad serveragent på en Windows-baserad dator. För en Linux-baserad dator hittar du motsvarande förhandsversion]: Log Analytics-agenten ska installeras på din _\[ principdefinition för Linux Azure Arc datorer._ Klicka på principen och sedan på **Välj**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-available-definition.png" alt-text="Hitta rätt principdefinition" border="false":::

1. **Tilldelningsnamn** fylls i automatiskt med namnet på principen som du valde, men du kan ändra det om du vill. I det här exemplet lämnar du _\[ Förhandsversion]: Log Analytics-agenten_ ska installeras på dina Windows Azure Arc-datorer eller _\[ förhandsversion]: Log Analytics-agenten_ ska installeras på dina Linux Azure Arc-datorer beroende på vilken du har valt. Du kan också lägga till en valfri **Beskrivning**. Beskrivningen innehåller information om den här principtilldelningen.
   **Assigned by** (Tilldelats av) anges automatiskt baserat på vem som är inloggad. Det här fältet är valfritt, så du kan ange anpassade värden.

1. Lämna **Skapa en hanterad identitet** avmarkerat. Den här rutan _måste_ markeras när principen eller initiativet omfattar en princip med effekten [deployIfNotExists](../../../governance/policy/concepts/effects.md#deployifnotexists). Eftersom principen som används för den här snabbstarten inte gör det kan du lämna det tomt. Mer information finns i avsnitten om [hanterade identiteter](../../../active-directory/managed-identities-azure-resources/overview.md) och [hur reparationssäkerhet fungerar](../../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works).

1. Klicka på **Tilldela**.

Nu är du redo att identifiera icke-kompatibla resurser för att förstå kompatibilitetstillståndet för din miljö.

## <a name="identify-non-compliant-resources"></a>Identifiera icke-kompatibla resurser

Välj **Efterlevnad** till vänster på sidan. Leta sedan upp **\[ förhandsversionen]: Log Analytics-agenten** ska installeras på dina Windows Azure Arc-datorer eller **\[ förhandsversion]: Log Analytics-agenten** ska installeras på din Principtilldelning för Linux Azure Arc-datorer som du skapade.

:::image type="content" source="./media/tutorial-assign-policy-portal/policy-compliance.png" alt-text="Efterlevnadsinformation på sidan Principefterlevnad" border="false":::

Om det finns befintliga resurser som inte är kompatibla med denna nya tilldelning visas de under **Icke-kompatibla resurser**.

När ett villkor utvärderas mot de befintliga resurserna och visas vara korrekt markeras dessa resurser som inkompatibla med principen. Följande tabell visar hur olika principåtgärder fungerar med villkorsutvärderingen för den efterlevnadsstatus som blir resultatet. Även om du inte ser utvärderingslogiken i Azure Portal visas resultatet av kompatibilitetstillståndet. Resultatet för kompatibilitetstillståndet är antingen kompatibla eller icke-kompatibla resurser.

| **Resurstillstånd** | **Effekt** | **Principutvärdering** | **Kompatibilitetstillstånd** |
| --- | --- | --- | --- |
| Finns | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | Sant | Icke-kompatibel |
| Finns | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | Falskt | Kompatibel |
| Ny | Audit, AuditIfNotExist\* | Sant | Icke-kompatibel |
| Ny | Audit, AuditIfNotExist\* | Falskt | Kompatibel |

\* För åtgärderna Append, DeployIfNotExist och AuditIfNotExist måste IF-instruktionen är TRUE.
Åtgärderna kräver också att villkoret Finns är FALSE för att vara icke-kompatibla. När det är TRUE utlöser IF-villkoret utvärdering av villkoret Finns för de relaterade resurserna.

## <a name="clean-up-resources"></a>Rensa resurser

Följ dessa steg för att ta bort tilldelningen som skapades:

1. Välj **Efterlevnad** (eller **Tilldelningar)** till vänster på Azure Policy-sidan och leta upp **\[ förhandsversionen]: Log Analytics-agenten** ska vara installerad på dina Windows Azure Arc-datorer eller **\[ förhandsversion]: Log Analytics-agenten** ska vara installerad på den principtilldelning för Linux Azure Arc-datorer som du skapade.

1. Högerklicka på principtilldelningen och välj Ta **bort tilldelning.**

   :::image type="content" source="./media/tutorial-assign-policy-portal/delete-assignment.png" alt-text="Ta bort en tilldelning från sidan Efterlevnad" border="false":::

## <a name="next-steps"></a>Nästa steg

I den här självstudien tilldelade du en principdefinition till ett omfång och utvärderade dess efterlevnadsrapport. Principdefinitionen kontrollerar att alla resurser i omfånget är kompatibla och identifierar vilka som inte är det. Nu är du redo att övervaka din Azure Arc-aktiverade serverdatorn genom att aktivera [VM-insikter.](../../../azure-monitor/vm/vminsights-overview.md)

Om du vill lära dig hur du övervakar och visar prestanda, kör processen och deras beroenden från datorn fortsätter du till självstudien:

> [!div class="nextstepaction"]
> [Aktivera insikter om virtuella datorer](tutorial-enable-vm-insights.md)