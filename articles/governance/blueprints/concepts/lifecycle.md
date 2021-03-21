---
title: Förstå en skisss livs cykel
description: Lär dig mer om livs cykeln som en skiss definition går igenom och information om varje steg, inklusive uppdatering och borttagning av skiss uppgifter.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: 721a198b5732af01c6712e86bc0c8e8ef543b404
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98918525"
---
# <a name="understand-the-lifecycle-of-an-azure-blueprint"></a>Förstå livs cykeln för en Azure Blueprint

Precis som många resurser i Azure har en skiss i Azure-ritningar en typisk och naturlig livs cykel. De skapas, distribueras och tas slutligen bort när de inte längre behövs eller är relevanta. Azure-ritningar har stöd för standard livs cykel åtgärder. Det bygger sedan vidare på dem för att tillhandahålla ytterligare nivåer av status som stöder vanlig kontinuerlig integrering och kontinuerliga distributions pipeliner för organisationer som hanterar sin infrastruktur som kod – ett nyckel element i DevOps.

För att helt förstå en skiss och stegen tar vi upp en standard livs cykel:

> [!div class="checklist"]
> - Skapa och redigera en skiss
> - Publicera skissen
> - Skapa och redigera en ny version av skissen
> - Publicera en ny version av skissen
> - Ta bort en enskild version av skissen
> - Ta bort skissen

## <a name="creating-and-editing-a-blueprint"></a>Skapa och redigera en skiss

När du skapar en skiss kan du lägga till artefakter till den, Spara till en hanterings grupp eller prenumeration och ange ett unikt namn och en unik version. Skissen är nu i ett **utkast** läge och kan inte tilldelas ännu. I **utkast** läge kan du fortsätta att uppdatera och ändra det.

En aldrig publicerad skiss i **utkast** läge visar en annan ikon på sidan **skiss definitioner** än de som har **publicerats**. Den **senaste versionen** visas som **utkast** för dessa aldrig publicerade skisser.

Skapa och redigera en skiss med [Azure Portal](../create-blueprint-portal.md#create-a-blueprint) eller [REST API](../create-blueprint-rest-api.md#create-a-blueprint).

## <a name="publishing-a-blueprint"></a>Publicera en skiss

När alla planerade ändringar har gjorts i en skiss i **utkast** läge kan den **publiceras** och göras tillgänglig för tilldelning. Den **publicerade** versionen av skissen kan inte ändras. När du har **publicerat** ritningen visas den med en annan ikon än **utkast** ritningar och det angivna versions numret visas i den **senaste versions** kolumnen.

Publicera en skiss med [Azure Portal](../create-blueprint-portal.md#publish-a-blueprint) eller [REST API](../create-blueprint-rest-api.md#publish-a-blueprint).

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Skapa och redigera en ny version av skissen

En **publicerad** version av en skiss kan inte ändras. En ny version av skissen kan dock läggas till i den befintliga skissen och ändras efter behov. Ändra en befintlig skiss genom att redigera den. När de nya ändringarna sparas innehåller skissen nu **opublicerade ändringar**. Dessa ändringar är en ny **utkast** version av skissen.

Redigera en skiss med [Azure Portal](../create-blueprint-portal.md#edit-a-blueprint).

## <a name="publishing-a-new-version-of-the-blueprint"></a>Publicera en ny version av skissen

Varje redige rad version av en skiss måste **publiceras** innan den kan tilldelas. När **opublicerade ändringar** har gjorts i en skiss men inte **publicerat**, är knappen **publicera skiss** tillgänglig på sidan Redigera skiss. Om knappen inte visas har skissen redan **publicerats** och inga **opublicerade ändringar** har gjorts.

> [!NOTE]
> En enda skiss kan ha flera **publicerade** versioner som var och en kan tilldelas till prenumerationer.

Om du vill publicera en skiss med **opublicerade ändringar** använder du samma steg för att publicera en ny skiss.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>Ta bort en enskild version av skissen

Varje version av en skiss är ett unikt objekt och kan **publiceras** separat. Därför kan varje version av en skiss också tas bort. Att ta bort en version av en skiss påverkar inte andra versioner av den ritningen.

> [!NOTE]
> Det går inte att ta bort en skiss som har aktiva tilldelningar. Ta bort tilldelningarna först och ta sedan bort den version som du vill ta bort.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj **skiss definitioner** på sidan till vänster och Använd filter alternativen för att hitta den skiss som du vill ta bort en version av. Välj den för att öppna sidan Redigera.

1. Välj fliken **publicerade versioner** och leta upp den version som du vill ta bort.

1. Högerklicka på den version som du vill ta bort och välj **ta bort den här versionen**.

## <a name="deleting-the-blueprint"></a>Ta bort skissen

Kärn skissen kan också tas bort. När du tar bort kärn skissen tas även skiss versioner av den ritningen bort, inklusive både **utkast** -och **publicerings** ritningar. När du tar bort en version av en skiss tas inte de befintliga uppdragen bort från någon av skiss versionerna när du tar bort huvud ritningen.

> [!NOTE]
> Det går inte att ta bort en skiss som har aktiva tilldelningar. Ta bort tilldelningarna först och ta sedan bort den version som du vill ta bort.

Ta bort en skiss med [Azure Portal](../create-blueprint-portal.md#delete-a-blueprint) eller [REST API](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Tilldelningar

Det finns flera punkter i livs cykeln som en skiss kan tilldelas till en prenumeration. När läget i en version av skissen **publiceras**, kan den versionen tilldelas till en prenumeration. Den här livs cykeln gör det möjligt att använda versioner av en skiss och aktivt tilldelas medan en nyare version utvecklas.

När versioner av ritningar tilldelas är det viktigt att förstå var de är tilldelade och med vilka parametrar de har tilldelats. Parametrarna kan antingen vara statiska eller dynamiska. Läs mer i [statiska och dynamiska parametrar](./parameters.md).

### <a name="updating-assignments"></a>Uppdaterar tilldelningar

När en skiss tilldelas kan tilldelningen uppdateras. Det finns flera anledningar till att uppdatera en befintlig tilldelning, inklusive:

- Lägg till eller ta bort [resurs låsning](./resource-locking.md)
- Ändra värdet för [dynamiska parametrar](./parameters.md#dynamic-parameters)
- Uppgradera tilldelningen till en nyare **publicerad** version av skissen

Mer information finns i [Uppdatera befintliga tilldelningar](../how-to/update-existing-assignments.md).

### <a name="unassigning-assignments"></a>Ta bort tilldelningar

Om skissen inte längre behövs kan den tas bort från hanterings gruppen eller prenumerationen. Följande inträffar under skiss tilldelningen:

- Borttagning av [skiss resurs låsning](./resource-locking.md)
- Borttagning av skiss tilldelnings objekt
- Före Om en **systemtilldelad hanterad identitet** användes, tas den också bort

> [!NOTE]
> Alla resurser som distribueras av skiss tilldelningen förblir på plats, men skyddas inte längre av Azure-ritningar.

## <a name="next-steps"></a>Nästa steg

- Förstå hur du använder [statiska och dynamiska parametrar](./parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](./sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](./resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md).
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).