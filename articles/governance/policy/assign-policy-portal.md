---
title: 'Snabb start: ny princip tilldelning med Portal'
description: I den här snabb starten använder du Azure Portal för att skapa en Azure Policy tilldelning för att identifiera icke-kompatibla resurser.
ms.date: 08/17/2020
ms.topic: quickstart
ms.openlocfilehash: eb3f97ab2f8da3ff2809cb969c8442779e173983
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548387"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Snabb start: skapa en princip tilldelning för att identifiera icke-kompatibla resurser

Det första steget mot att förstå kompatibilitet i Azure är att identifiera dina resursers status.
Denna snabbstart vägleder dig genom processen för att skapa en principtilldelning som identifierar virtuella datorer som inte använder hanterade diskar.

Efter den här genomgången kommer du att kunna identifiera virtuella datorer som inte använder hanterade diskar. De är _inkompatibla_ med principtilldelningen.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

I den här snabbstarten skapar du en principtilldelning och tilldelar policydefinitionen _Granska virtuella datorer som inte använder hanterade diskar_.

1. Starta Azure Policy-tjänsten i Azure Portal genom att klicka på **Alla tjänster** och sedan söka efter och välja **Princip**.

   :::image type="content" source="./media/assign-policy-portal/search-policy.png" alt-text="Sök efter princip i alla tjänster" border="false":::

1. Välj **Tilldelningar** till vänster på sidan Azure Policy. En tilldelning är en princip som tilldelats ett specifikt område.

   :::image type="content" source="./media/assign-policy-portal/select-assignments.png" alt-text="Sidan Välj tilldelningar från sidan princip översikt" border="false":::

1. Välj **Tilldela princip** längst upp på sidan **Princip – Tilldelningar**.

   :::image type="content" source="./media/assign-policy-portal/select-assign-policy.png" alt-text="Tilldela en princip definition från sidan tilldelningar" border="false":::

1. På sidan **tilldela princip** anger du **omfånget** genom att välja ellipsen och sedan välja antingen en hanterings grupp eller en prenumeration. Du kan även välja en resursgrupp. Ett omfång avgör vilka resurser eller grupper med resurser som principtilldelningen används i. Använd sedan knappen **Välj** längst ned på sidan **omfång** .

   I det här exemplet används **Contoso-prenumerationen**. Din prenumeration skiljer sig.

1. Resurser som kan uteslutas baserat på **omfång**. **Undantag** startar på en nivå som är lägre än nivån för **Omfång**. **Undantag** är valfria, så lämna det tomt just nu.

1. Välj ellipsen **Principdefinition** för att öppna listan med tillgängliga definitioner. Azure Policy har inbyggda principdefinitioner som du kan använda. Många är tillgängliga, till exempel:

   - Framtvinga tagg och dess värde
   - Använd tagg och dess värde
   - Ärv en tagg från resurs gruppen om den saknas

   En lista över tillgängliga inbyggda principer finns i [Azure policy exempel](./samples/index.md).

1. Sök igenom listan med principdefinitioner för att hitta definitionen _Granska virtuella datorer som inte använder hanterade diskar_. Välj den principen och Använd sedan knappen **Välj** .

   :::image type="content" source="./media/assign-policy-portal/select-available-definition.png" alt-text="Hitta rätt principdefinition" border="false":::

1. **Tilldelningsnamn** fylls i automatiskt med namnet på principen som du valde, men du kan ändra det om du vill. I det här exemplet lämnar du _Granska virtuella datorer som inte använder hanterade diskar_. Du kan också lägga till en valfri **Beskrivning**. Beskrivningen innehåller information om den här principtilldelningen.
   **Assigned by** (Tilldelats av) anges automatiskt baserat på vem som är inloggad. Det här fältet är valfritt, så du kan ange anpassade värden.

1. Lämna **Skapa en hanterad identitet** avmarkerat. Den här rutan _måste_ markeras när principen eller initiativet omfattar en princip med effekten [deployIfNotExists](./concepts/effects.md#deployifnotexists). Eftersom principen som används för den här snabbstarten inte gör det kan du lämna det tomt. Mer information finns i avsnitten om [hanterade identiteter](../../active-directory/managed-identities-azure-resources/overview.md) och [hur reparationssäkerhet fungerar](./how-to/remediate-resources.md#how-remediation-security-works).

1. Välj **Tilldela**.

Du är nu redo att identifiera icke-kompatibla resurser för att förstå miljöns kompatibilitetstillstånd.

## <a name="identify-non-compliant-resources"></a>Identifiera icke-kompatibla resurser

Välj **Efterlevnad** till vänster på sidan. Leta sedan upp principtilldelningen _Granska virtuella datorer som inte använder hanterade diskar_ som du skapade.

:::image type="content" source="./media/assign-policy-portal/policy-compliance.png" alt-text="Information om efterlevnad på sidan efterlevnadsprincip" border="false":::

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

1. Välj **Efterlevnad** (eller **Tilldelningar**) till vänster på sidan Azure Policy och leta upp principtilldelningen _Granska virtuella datorer som inte använder hanterade diskar_ som du skapade.

1. Högerklicka på tjänsten _Granska virtuella datorer som inte använder princip tilldelning för hanterade diskar_ och välj **ta bort tilldelning**.

   :::image type="content" source="./media/assign-policy-portal/delete-assignment.png" alt-text="Ta bort en tilldelning från sidan efterlevnad" border="false":::

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du tilldelat en principdefinition till ett omfång och utvärderat dess efterlevnadsrapport.
Principdefinitionen kontrollerar att alla resurser i omfånget är kompatibla och identifierar vilka som inte är det.

Om du vill ha mer information om tilldelning av principer för att validera att de nya resurserna är kompatibla fortsätter du till självstudien för att:

> [!div class="nextstepaction"]
> [Skapa och hantera principer](./tutorials/create-and-manage.md)