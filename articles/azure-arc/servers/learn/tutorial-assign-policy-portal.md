---
title: Självstudie – ny princip tilldelning med Azure Portal
description: I den här självstudien använder du Azure Portal för att skapa en Azure Policy tilldelning för att identifiera icke-kompatibla resurser.
ms.topic: tutorial
ms.date: 10/07/2020
ms.openlocfilehash: 9a07e490525ce532f8f843b30b3b83715e65ce3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91826590"
---
# <a name="tutorial-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Självstudie: skapa en princip tilldelning för att identifiera icke-kompatibla resurser

Det första steget mot att förstå kompatibilitet i Azure är att identifiera dina resursers status. Azure Policy stöder granskning av status för din ARC-aktiverade server med gäst konfigurations principer. Konfigurations principer för gäster tillämpar inte konfigurationer, de bara granskar inställningarna på datorn. Den här kursen vägleder dig genom processen att skapa och tilldela en princip och identifiera vilken av dina Arc-aktiverade servrar som inte har Log Analytics-agenten installerad.

I slutet av den här processen kommer du att kunna identifiera datorer som inte har Log Analytics agent för Windows eller Linux installerat. De är _inkompatibla_ med principtilldelningen.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

I den här självstudien skapar du en princip tilldelning och tilldelar _\[ förhands granskningen]: Log Analytics-agenten ska installeras på princip definitionen för Linux Azure-Arc-datorer_ .

1. Starta Azure Policy-tjänsten i Azure Portal genom att klicka på **Alla tjänster** och sedan söka efter och välja **Princip**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/search-policy.png" alt-text="Sök efter princip i alla tjänster" border="false":::

1. Välj **Tilldelningar** till vänster på sidan Azure Policy. En tilldelning är en princip som tilldelats ett specifikt område.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assignment.png" alt-text="Sidan Välj tilldelningar från sidan princip översikt" border="false":::

1. Välj **Tilldela princip** längst upp på sidan **Princip – Tilldelningar**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assign-policy.png" alt-text="Tilldela en princip definition från sidan tilldelningar" border="false":::

1. På sidan **Tilldela princip** väljer du **Omfång** genom att klicka på ellipsen och antingen välja en hanteringsgrupp eller en prenumeration. Du kan även välja en resursgrupp. Ett omfång avgör vilka resurser eller grupper med resurser som principtilldelningen används i. Klicka sedan på **Välj** längst ned på sidan **Omfång**.

   I det här exemplet används **Larssons Aerospace** -prenumerationen. Din prenumeration skiljer sig.

1. Resurser som kan uteslutas baserat på **omfång**. **Undantag** startar på en nivå som är lägre än nivån för **Omfång**. **Undantag** är valfria, så lämna det tomt just nu.

1. Välj ellipsen **Principdefinition** för att öppna listan med tillgängliga definitioner. Azure Policy har inbyggda principdefinitioner som du kan använda. Många är tillgängliga, till exempel:

   - Framtvinga tagg och dess värde
   - Använd tagg och dess värde
   - Ärv en tagg från resurs gruppen om den saknas

   En lista över tillgängliga inbyggda principer finns i [Azure policy exempel](../../../governance/policy/samples/index.md).

1. Sök igenom listan princip definitioner för att hitta _\[ förhands granskningen]: Log Analytics agenten ska installeras på Windows Azure Arc Machines_ -definitionen om du har aktiverat agenten för Arc-aktiverade servrar på en Windows-baserad dator. För en Linux-baserad dator hittar du motsvarande för _\[ hands version]: Log Analytics agenten ska installeras på princip definitionen för Linux-datorer i Linux_ . Klicka på principen och sedan på **Välj**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-available-definition.png" alt-text="Hitta rätt principdefinition" border="false":::

1. **Tilldelningsnamn** fylls i automatiskt med namnet på principen som du valde, men du kan ändra det om du vill. I det här exemplet lämnar du _\[ förhands granskning]: Log Analytics agenten ska installeras på dina Windows Azure Arc-datorer_ eller för _\[ hands version]: Log Analytics agenten ska installeras på dina Linux Azure Arc-datorer_ beroende på vilken du har valt. Du kan också lägga till en valfri **Beskrivning**. Beskrivningen innehåller information om den här principtilldelningen.
   **Assigned by** (Tilldelats av) anges automatiskt baserat på vem som är inloggad. Det här fältet är valfritt, så du kan ange anpassade värden.

1. Lämna **Skapa en hanterad identitet** avmarkerat. Den här rutan _måste_ markeras när principen eller initiativet omfattar en princip med effekten [deployIfNotExists](../../../governance/policy/concepts/effects.md#deployifnotexists). Eftersom principen som används för den här snabbstarten inte gör det kan du lämna det tomt. Mer information finns i avsnitten om [hanterade identiteter](../../../active-directory/managed-identities-azure-resources/overview.md) och [hur reparationssäkerhet fungerar](../../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works).

1. Klicka på **Tilldela**.

Du är nu redo att identifiera icke-kompatibla resurser för att förstå miljöns kompatibilitetstillstånd.

## <a name="identify-non-compliant-resources"></a>Identifiera icke-kompatibla resurser

Välj **Efterlevnad** till vänster på sidan. Leta sedan reda på för **\[ hands versionen]: Log Analytics-agenten ska installeras på dina Windows Azure Arc-datorer eller för** **\[ hands version]: Log Analytics agenten bör installeras på den princip tilldelning för Linux-datorer i Linux** som du har skapat.

:::image type="content" source="./media/tutorial-assign-policy-portal/policy-compliance.png" alt-text="Information om efterlevnad på sidan efterlevnadsprincip" border="false":::

Om det finns befintliga resurser som inte är kompatibla med denna nya tilldelning visas de under **Icke-kompatibla resurser**.

När ett villkor utvärderas mot de befintliga resurserna och visas vara korrekt markeras dessa resurser som inkompatibla med principen. Följande tabell visar hur olika principåtgärder fungerar med villkorsutvärderingen för den efterlevnadsstatus som blir resultatet. Även om du inte ser utvärderings logiken i Azure Portal visas resultatet för kompatibilitetstillstånd. Resultatet för kompatibilitetstillståndet är antingen kompatibla eller icke-kompatibla resurser.

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

1. Välj **efterlevnad** (eller **tilldelningar**) till vänster på sidan Azure policy och leta upp **\[ förhands granskningen]: Log Analytics agenten ska installeras på dina Windows Azure Arc-datorer eller för** **\[ hands version]: Log Analytics agenten ska installeras på din Linux Azure Arc-dator** princip tilldelning som du har skapat.

1. Högerklicka på princip tilldelningen och välj **ta bort tilldelning**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/delete-assignment.png" alt-text="Ta bort en tilldelning från sidan efterlevnad" border="false":::

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du tilldelat en princip definition till ett omfång och utvärderat dess Kompatibilitetsrapport. Principdefinitionen kontrollerar att alla resurser i omfånget är kompatibla och identifierar vilka som inte är det. Nu är du redo att övervaka din dator med Azure Arc-aktiverade servrar med Azure Monitor for VMs.

Om du vill lära dig hur du övervakar och visar prestanda, kör process och deras beroenden från datorn fortsätter du till självstudien:

> [!div class="nextstepaction"]
> [Aktivera Azure Monitor for VMs](tutorial-enable-vm-insights.md)