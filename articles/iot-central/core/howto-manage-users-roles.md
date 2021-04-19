---
title: Hantera användare och roller i Azure IoT Central program | Microsoft Docs
description: Som administratör kan du lära dig hur du hanterar användare och roller i Azure IoT Central program
author: lmasieri
ms.author: lmasieri
ms.date: 04/16/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: cff8830d180b0c234e54f7578ed9fafafeb598f0
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719177"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Hantera användare och roller i ditt IoT Central-program

Den här artikeln beskriver hur du som administratör kan lägga till, redigera och ta bort användare i ditt Azure IoT Central program. Artikeln beskriver också hur du hanterar roller i ditt program.

Om du vill komma åt **och använda** avsnittet Administration måste du ha rollen **Administratör** för ett Azure IoT Central program. Om du skapar Azure IoT Central program läggs du automatiskt till i **administratörsrollen** för programmet.

## <a name="add-users"></a>Lägga till användare

Varje användare måste ha ett användarkonto innan de kan logga in och komma åt ett program. IoT Central stöder för närvarande Microsoft-konton och Azure Active Directory-konton, men inte Azure Active Directory grupper.

Mer information finns i Microsoft-konto [snabbstart: Lägga till nya användare i Azure Active Directory](../../active-directory/fundamentals/add-users-azure-active-directory.md). [](https://support.microsoft.com/products/microsoft-account?category=manage-account)

1. Om du vill lägga till en användare IoT Central ett program går du till **sidan** Användare i **avsnittet** Administration.

  :::image type="content" source="media/howto-manage-users-roles/manage-users-pnp.png" alt-text="Hantera användare":::

1. Om du vill lägga till en användare går **du till sidan** Användare och väljer + Lägg till **användare.**

1. Välj en roll för användaren i **listrutan** Roll. Läs mer om roller i avsnittet [Hantera roller](#manage-roles) i den här artikeln.

  :::image type="content" source="media/howto-manage-users-roles/add-user-pnp.png" alt-text="Lägg till en användare och välj en roll.":::

  > [!NOTE]
  > En användare som har en anpassad roll som ger dem behörighet att lägga till andra användare kan bara lägga till användare i en roll med samma eller färre behörigheter än sin egen roll.

  > [!NOTE]
  > Om en användare tas bort från Azure Active Directory och sedan läggs till igen kan de inte logga in på IoT Central program. Om du vill återaktivera åtkomst bör programmets administratör ta bort och lägga till användaren i programmet på samma sätt.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Redigera de roller som är tilldelade till användare

Roller kan inte ändras efter att de har tilldelats. Om du vill ändra rollen som är tilldelad till en användare tar du bort användaren och lägger sedan till användaren igen med en annan roll.

> [!NOTE]
> De tilldelade rollerna är specifika för IoT Central och kan inte hanteras från Azure Portal.

## <a name="delete-users"></a>Ta bort användare

Om du vill ta bort användare markerar du en eller flera kryssrutor **på sidan** Användare. Välj sedan **Ta bort**.

## <a name="manage-roles"></a>Hantera roller

Med roller kan du styra vem inom din organisation som får utföra olika uppgifter i IoT Central. Det finns tre inbyggda roller som du kan tilldela till användare av ditt program. Du kan [också skapa anpassade](#create-a-custom-role) roller om du behöver mer begränsad kontroll.

> [!div class="mx-imgBorder"]
> ![Hantera val av roller](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>Administratör

Användare i **administratörsrollen** kan hantera och kontrollera alla delar av programmet, inklusive fakturering.

Den användare som skapar ett program tilldelas automatiskt **rollen** Administratör. Det måste alltid finnas minst en användare i **administratörsrollen.**

### <a name="builder"></a>Builder

Användare i **builder-rollen** kan hantera alla delar av appen, men kan inte göra ändringar på flikarna Administration eller Kontinuerlig dataexport.

### <a name="operator"></a>Operator

Användare i **operatörsrollen** kan övervaka enhetens hälsotillstånd och status. De får inte göra ändringar i enhetsmallar eller administrera programmet. Operatörer kan lägga till och ta bort enheter, hantera enhetsuppsättningar och köra analyser och jobb.

## <a name="create-a-custom-role"></a>Skapa en anpassad roll

Om din lösning kräver mer begränsade åtkomstkontroller kan du skapa roller med anpassade uppsättningar med behörigheter. Om du vill skapa en anpassad roll går **du till sidan** Roller i **avsnittet** Administration i ditt program. Välj sedan **+ Ny roll** och lägg till ett namn och en beskrivning för din roll. Välj de behörigheter som din roll kräver och välj sedan **Spara.**

Du kan lägga till användare i din anpassade roll på samma sätt som du lägger till användare i en inbyggd roll.

> [!div class="mx-imgBorder"]
> ![Skapa en anpassad roll](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Alternativ för anpassade roller

När du definierar en anpassad roll väljer du den uppsättning behörigheter som en användare beviljas om de är medlemmar i rollen. Vissa behörigheter är beroende av andra. Om du till exempel lägger till **behörigheten Uppdatera personliga instrumentpaneler** till en roll läggs **behörigheten Visa personliga** instrumentpaneler till automatiskt. I följande tabeller sammanfattas de tillgängliga behörigheterna och deras beroenden som du kan använda när du skapar anpassade roller.

#### <a name="managing-devices"></a>Hantera enheter

**Behörigheter för enhetsmall**

| Name | Beroenden |
| ---- | -------- |
| Visa | Inget     |
| Hantera | Visa <br/> Andra beroenden: Visa enhetsinstanser  |
| Fullständig kontroll | Visa, hantera <br/> Andra beroenden: Visa enhetsinstanser |

**Behörigheter för enhetsinstanser**

| Name | Beroenden |
| ---- | -------- |
| Visa | Inget <br/> Andra beroenden: Visa enhetsmallar och enhetsgrupper |
| Uppdatera | Visa <br/> Andra beroenden: Visa enhetsmallar och enhetsgrupper  |
| Skapa | Visa <br/> Andra beroenden: Visa enhetsmallar och enhetsgrupper  |
| Ta bort | Visa <br/> Andra beroenden: Visa enhetsmallar och enhetsgrupper  |
| Köra kommandon | Uppdatera, visa <br/> Andra beroenden: Visa enhetsmallar och enhetsgrupper  |
| Visa rådata | Visa <br/> Andra beroenden: Visa enhetsmallar och enhetsgrupper  |
| Fullständig kontroll | Visa, uppdatera, skapa, ta bort, köra kommandon, visa rådata <br/> Andra beroenden: Visa enhetsmallar och enhetsgrupper  |

**Behörigheter för enhetsgrupper**

| Name | Beroenden |
| ---- | -------- |
| Visa | Inget <br/> Andra beroenden: Visa enhetsmallar och enhetsinstanser |
| Uppdatera | Visa <br/> Andra beroenden: Visa enhetsmallar och enhetsinstanser   |
| Skapa | Visa, uppdatera <br/> Andra beroenden: Visa enhetsmallar och enhetsinstanser   |
| Ta bort | Visa <br/> Andra beroenden: Visa enhetsmallar och enhetsinstanser  |
| Fullständig kontroll | Visa, uppdatera, skapa, ta bort <br/> Andra beroenden: Visa enhetsmallar och enhetsinstanser |

**Behörigheter för hantering av enhetsanslutning**

| Name | Beroenden |
| ---- | -------- |
| Läs-instans | Inget <br/> Andra beroenden: Visa enhetsmallar, enhetsgrupper, enhetsinstanser |
| Hantera instans | Läs-instans <br /> Andra beroenden: Visa enhetsmallar, enhetsgrupper, enhetsinstanser |
| Läsa globalt | Inget   |
| Hantera globala | Läsa globalt |
| Fullständig kontroll | Läs instans, Hantera instans, Läsa global, Hantera global <br/> Andra beroenden: Visa enhetsmallar, enhetsgrupper, enhetsinstanser |

**Jobbbehörigheter**

| Name | Beroenden |
| ---- | -------- |
| Visa | Inget <br/> Andra beroenden: Visa enhetsmallar, enhetsinstanser och enhetsgrupper |
| Uppdatera | Visa <br/> Andra beroenden: Visa enhetsmallar, enhetsinstanser och enhetsgrupper |
| Skapa | Visa, uppdatera <br/> Andra beroenden: Visa enhetsmallar, enhetsinstanser och enhetsgrupper |
| Ta bort | Visa <br/> Andra beroenden: Visa enhetsmallar, enhetsinstanser och enhetsgrupper |
| Genomförande | Visa <br/> Andra beroenden: Visa enhetsmallar, enhetsinstanser och enhetsgrupper; Uppdatera enhetsinstanser; Köra kommandon på enhetsinstanser |
| Fullständig kontroll | Visa, uppdatera, skapa, ta bort, köra <br/> Andra beroenden: Visa enhetsmallar, enhetsinstanser och enhetsgrupper; Uppdatera enhetsinstanser; Köra kommandon på enhetsinstanser |

**Regelbehörigheter**

| Name | Beroenden |
| ---- | -------- |
| Visa | Inget <br/> Andra beroenden: Visa enhetsmallar |
| Uppdatera | Visa <br/> Andra beroenden: Visa enhetsmallar |
| Skapa | Visa, uppdatera <br/> Andra beroenden: Visa enhetsmallar |
| Ta bort | Visa <br/> Andra beroenden: Visa enhetsmallar |
| Fullständig kontroll | Visa, uppdatera, skapa, ta bort <br/> Andra beroenden: Visa enhetsmallar |

#### <a name="managing-the-app"></a>Hantera appen

**Behörigheter för programinställningar**

| Name | Beroenden |
| ---- | -------- |
| Visa | Inget     |
| Uppdatera | Visa   |
| Kopiera | Visa <br/> Andra beroenden: Visa enhetsmallar, enhetsinstanser, enhetsgrupper, instrumentpaneler, dataexport, anpassning, hjälplänkar, anpassade roller, regler |
| Ta bort | Visa   |
| Fullständig kontroll | Visa, uppdatera, kopiera, ta bort <br/> Andra beroenden: Visa enhetsmallar, enhetsgrupper, instrumentpaneler för program, dataexport, anpassning, hjälplänkar, anpassade roller, regler |

**Exportbehörigheter för programmall**

| Name | Beroenden |
| ---- | -------- |
| Visa | Inget     |
| Exportera | Visa <br/> Andra beroenden: Visa enhetsmallar, enhetsinstanser, enhetsgrupper, instrumentpaneler, dataexport, varumärkesanpassad, hjälplänkar, anpassade roller, regler |
| Fullständig kontroll | Visa, exportera <br/> Andra beroenden: Visa enhetsmallar, enhetsgrupper, instrumentpaneler för program, dataexport, anpassning, hjälplänkar, anpassade roller, regler |

**Behörigheter för uppladdning av enhetsfil**

| Name | Beroenden |
| ---- | -------- |
| Visa | Inget     |
| Hantera | Visa   |
| Fullständig kontroll | Visa, hantera |

**Faktureringsbehörigheter**

| Name | Beroenden |
| ---- | -------- |
| Hantera | Inget     |
| Fullständig kontroll | Hantera |

#### <a name="managing-users-and-roles"></a>Hantera användare och roller

**Behörigheter för anpassade roller**

| Name | Beroenden |
| ---- | -------- |
| Visa | Inget |
| Uppdatera | Visa |
| Skapa | Visa, uppdatera |
| Ta bort | Visa |
| Fullständig kontroll | Visa, uppdatera, skapa, ta bort |

**Behörigheter för användarhantering**

| Name | Beroenden |
| ---- | -------- |
| Visa | Inget <br/> Andra beroenden: Visa anpassade roller |
| Lägg till | Visa <br/> Andra beroenden: Visa anpassade roller |
| Ta bort | Visa <br/> Andra beroenden: Visa anpassade roller |
| Fullständig kontroll | Visa, lägga till, ta bort <br/> Andra beroenden: Visa anpassade roller |

> [!NOTE]
> En användare som har en anpassad roll som ger dem behörighet att lägga till andra användare kan bara lägga till användare i en roll med samma eller färre behörigheter än sin egen roll.

#### <a name="customizing-the-app"></a>Anpassa appen

**Behörigheter för instrumentpanelen för program**

| Name | Beroenden |
| ---- | -------- |
| Visa | Inget     |
| Uppdatera | Visa   |
| Skapa | Visa, uppdatera |
| Ta bort | Visa   |
| Fullständig kontroll | Visa, uppdatera, skapa, ta bort |

**Behörigheter för personliga instrumentpaneler**

| Name | Beroenden |
| ---- | -------- |
| Visa | Inget     |
| Uppdatera | Visa   |
| Skapa | Visa, uppdatera   |
| Ta bort | Visa   |
| Fullständig kontroll | Visa, uppdatera, skapa, ta bort |

**Varumärkes-, uttryckssymbol- och färgbehörigheter**

| Name | Beroenden |
| ---- | -------- |
| Visa | Inget     |
| Uppdatera | Visa   |
| Fullständig kontroll | Visa, uppdatera |

**Hjälp länkar behörigheter**

| Name | Beroenden |
| ---- | -------- |
| Visa | Inget     |
| Uppdatera | Visa   |
| Fullständig kontroll | Visa, uppdatera |

#### <a name="extending-the-app"></a>Utöka appen

**Dataexportbehörigheter**

| Name | Beroenden |
| ---- | -------- |
| Visa | Inget     |
| Uppdatera | Visa   |
| Skapa | Visa, uppdatera  |
| Ta bort | Visa   |
| Fullständig kontroll | Visa, uppdatera, skapa, ta bort |

**API-tokenbehörigheter**

| Name | Beroenden |
| ---- | -------- |
| Visa | Inget  <br/> Andra beroenden: Visa anpassade roller |
| Skapa | Visa <br/> Andra beroenden: Visa anpassade roller |
| Ta bort | Visa <br/> Andra beroenden: Visa anpassade roller |
| Fullständig kontroll | Visa, skapa, ta bort <br/> Andra beroenden: Visa anpassade roller |

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar användare och roller i ditt IoT Central program föreslår vi att du går vidare med att lära [dig hur du hanterar din faktura.](howto-view-bill.md)