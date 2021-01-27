---
title: Uppdatera en befintlig tilldelning från portalen
description: Lär dig mer om mekanismen för att uppdatera en befintlig skiss tilldelning från portalen i Azure-ritningar.
ms.date: 01/27/2021
ms.topic: how-to
ms.openlocfilehash: c383ebedaf83b3a52062c91f98b816c3baf6618e
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919401"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Så här uppdaterar du en befintlig skiss tilldelning

När en skiss tilldelas kan tilldelningen uppdateras. Det finns flera anledningar till att uppdatera en befintlig tilldelning, inklusive:

- Lägg till eller ta bort [resurs låsning](../concepts/resource-locking.md)
- Ändra värdet för [dynamiska parametrar](../concepts/parameters.md#dynamic-parameters)
- Uppgradera tilldelningen till en nyare **publicerad** version av skissen

## <a name="updating-assignments"></a>Uppdaterar tilldelningar

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj **tilldelade skisser** från sidan till vänster.

1. I listan över ritningar väljer du skiss tilldelningen. Använd sedan knappen **Uppdatera tilldelning** eller högerklicka på skiss tilldelningen och välj **Uppdatera tilldelning**.

   :::image type="content" source="../media/update-existing-assignments/update-assignment.png" alt-text="Skärm bild av skiss tilldelnings sidan med knappen Uppdatera tilldelning markerad." border="false":::

1. Sidan **tilldela skisser** läser in ifyllda med alla värden från den ursprungliga tilldelningen. Du kan ändra **skiss definitions versionen**, **Lås tilldelnings** status och alla dynamiska parametrar som finns i skiss definitionen. Välj **tilldela** när du är klar med ändringarna.

1. På sidan uppdaterad tilldelnings information, se den nya statusen. I det här exemplet har vi lagt till **låsning** till tilldelningen.

   :::image type="content" source="../media/update-existing-assignments/updated-assignment.png" alt-text="Skärm bild av en uppdaterad skiss tilldelning som visar att Lås läget har ändrats." border="false":::

1. Utforska information om andra **tilldelnings åtgärder** med hjälp av list rutan. Tabellen med **hanterade resurser** uppdateringar per vald tilldelnings åtgärd.

   :::image type="content" source="../media/update-existing-assignments/assignment-operations.png" alt-text="Skärm bild av en uppdaterad skiss tilldelning som visar tilldelnings åtgärderna och deras status." border="false":::

## <a name="rules-for-updating-assignments"></a>Regler för att uppdatera tilldelningar

Distributionen av de uppdaterade tilldelningarna följer några viktiga regler. De här reglerna avgör vad som händer med redan distribuerade resurser. Den begärda ändringen och typen av artefakt resurs som distribueras eller uppdateras avgör vilka åtgärder som vidtas.

- Rolltilldelningar
  - Om rollen eller rollen som tilldelas av rollen (användare, grupp eller app) ändras, skapas en ny roll tilldelning. Roll tilldelningar som tidigare har distribuerats är kvar på plats.
- Principtilldelningar
  - Om parametrarna för princip tilldelningen ändras, uppdateras den befintliga tilldelningen.
  - Om definitionen av princip tilldelningen ändras skapas en ny princip tilldelning.
    Princip tilldelningar som tidigare har distribuerats är kvar på plats.
  - Om principen tilldelnings artefakt tas bort från skissen, lämnas distribuerade princip tilldelningar kvar.
- Azure Resource Manager mallar (ARM-mallar)
  - Mallen bearbetas via Resource Manager som en **placering**. När varje resurs typ hanterar den här åtgärden på olika sätt kan du läsa igenom dokumentationen för varje resurs som ingår för att fastställa effekten av den här åtgärden när den körs av ritningar.

## <a name="possible-errors-on-updating-assignments"></a>Möjliga fel vid uppdatering av tilldelningar

När du uppdaterar tilldelningar är det möjligt att göra ändringar som bryts när de körs. Ett exempel är att ändra platsen för en resurs grupp när den redan har distribuerats. Alla ändringar som stöds av [Resource Manager](../../../azure-resource-manager/management/overview.md) kan göras, men eventuella ändringar som skulle resultera i ett fel via Resource Manager leder också till att tilldelningen Miss lyckas.

Det finns ingen gräns för hur många gånger en tilldelning kan uppdateras. Om ett fel inträffar bestämmer du felet och gör en annan uppdatering av tilldelningen.  Exempel på fel scenarier:

- En felaktig parameter
- Ett befintligt objekt som redan finns
- En ändring som inte stöds av Resource Manager

## <a name="next-steps"></a>Nästa steg

- Mer information om [livscykeln för en skiss](../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../concepts/resource-locking.md).
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).