---
title: Konfigurera inställningar för Azure-resurs roller i PIM – Azure AD | Microsoft Docs
description: Lär dig hur du konfigurerar inställningar för Azure-resurs roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 01/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40779e4517b610e52e41caacc46fc613b72023e5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567143"
---
# <a name="configure-azure-resource-role-settings-in-privileged-identity-management"></a>Konfigurera inställningar för Azure-resurs roll i Privileged Identity Management

När du konfigurerar inställningar för Azure-resurs roller definierar du de standardinställningar som tillämpas på Azure-resursens roll tilldelningar i Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Använd följande procedurer för att konfigurera arbets flödet för godkännande och ange vem som kan godkänna eller neka begär Anden.

## <a name="open-role-settings"></a>Öppna roll inställningar

Följ dessa steg för att öppna inställningarna för en Azure-resurs roll.

1. Logga in på [Azure Portal](https://portal.azure.com/) med en användare i rollen [privilegie rad roll administratör](../roles/permissions-reference.md#privileged-role-administrator) .

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure-resurser**.

1. Välj den resurs som du vill hantera, till exempel en prenumeration eller hanterings grupp.

    ![Sidan Azure-resurser visar resurser som kan hanteras](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Välj **roll inställningar**.

    ![Sidan roll inställningar visar Azure-resurs roller](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Välj den roll vars inställningar du vill konfigurera.

    ![Sidan information om roll inställning visar flera tilldelnings-och aktiverings inställningar](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Välj **Redigera** för att öppna fönstret **roll inställningar** . På den första fliken kan du uppdatera konfigurationen för roll aktivering i Privileged Identity Management.

    ![Sidan Redigera roll inställningar med fliken aktivering öppen](./media/pim-resource-roles-configure-role-settings/role-settings-activation-tab.png)

1. Välj fliken **tilldelning** eller **Nästa: tilldelnings** knapp längst ned på sidan för att öppna fliken tilldelnings inställning. De här inställningarna kontrollerar roll tilldelningarna som görs i Privileged Identity Management-gränssnittet.

    ![Fliken roll tilldelning på sidan roll inställningar](./media/pim-resource-roles-configure-role-settings/role-settings-assignment-tab.png)

1. Använd fliken **meddelande** eller **Nästa: aktiverings** knappen längst ned på sidan för att komma till fliken meddelande inställning för den här rollen. De här inställningarna styr alla e-postaviseringar relaterade till den här rollen.

    ![Fliken roll meddelanden på sidan roll inställningar](./media/pim-resource-roles-configure-role-settings/role-settings-notification-tab.png)

    På fliken **meddelanden** på sidan roll inställningar kan Privileged Identity Management ge detaljerad kontroll över vem som tar emot meddelanden och vilka meddelanden de får.

    - **Stänga av ett e-postmeddelande**<br>Du kan inaktivera vissa e-postmeddelanden genom att avmarkera kryss rutan standard mottagare och ta bort ytterligare mottagare.  

    - **Begränsa e-postmeddelanden till angivna e-postadresser**<br>Du kan stänga av e-postmeddelanden som skickas till standard mottagare genom att avmarkera kryss rutan standard mottagare. Du kan sedan lägga till ytterligare e-postadresser som ytterligare mottagare. Om du vill lägga till fler än en e-postadress avgränsar du dem med ett semikolon (;).

    - **Skicka e-postmeddelanden till både standard mottagare och ytterligare mottagare**<br>Du kan skicka e-postmeddelanden till både standard mottagare och ytterligare mottagare genom att markera kryss rutan standard mottagare och lägga till e-postadresser för ytterligare mottagare.

    - **Endast kritiska e-postmeddelanden**<br>För varje typ av e-post kan du bara markera kryss rutan för att ta emot viktiga e-postmeddelanden. Det innebär att Privileged Identity Management fortsätter att skicka e-postmeddelanden till de konfigurerade mottagarna endast om e-postmeddelandet kräver en omedelbar åtgärd. E-postmeddelanden som uppmanar användarna att utöka sin roll tilldelning utlöses till exempel inte medan ett e-postmeddelande som kräver administratörer för att godkänna en tilläggs förfrågan utlöses.

1. Välj knappen **Uppdatera** när som helst när du vill uppdatera roll inställningarna.

## <a name="assignment-duration"></a>Varaktighet för tilldelning

Du kan välja mellan två alternativ för tilldelnings varaktighet för varje tilldelnings typ (giltig och aktiv) när du konfigurerar inställningar för en roll. De här alternativen blir den maximala standard varaktigheten när en användare tilldelas rollen i Privileged Identity Management.

Du kan välja något av följande **tillgängliga** varaktighets alternativ för tilldelning:

| | Beskrivning |
| --- | --- |
| **Tillåt permanent berättigad tilldelning** | Resurs administratörer kan tilldela permanent berättigad tilldelning. |
| **Giltig tilldelning har förfallit efter** | Resurs administratörer kan kräva att alla kvalificerade tilldelningar har ett angivet start-och slutdatum. |

Och du kan välja något av dessa alternativ för varaktighet för **aktiv** tilldelning:

| | Beskrivning |
| --- | --- |
| **Tillåt permanent aktiv tilldelning** | Resurs administratörer kan tilldela permanent aktiv tilldelning. |
| **Aktiv tilldelning förfaller efter** | Resurs administratörer kan kräva att alla aktiva tilldelningar har ett angivet start-och slutdatum. |

> [!NOTE]
> Alla tilldelningar som har ett angivet slutdatum kan förnyas av resurs administratörer. Användare kan även initiera självbetjänings begär Anden för att [utöka eller förnya roll tilldelningar](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Kräv Multi-Factor Authentication

Privileged Identity Management tillhandahåller en valfri tillämpning av Azure AD Multi-Factor Authentication för två olika scenarier.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Kräv Multi-Factor Authentication för aktiv tilldelning

I vissa fall kanske du vill tilldela en användare eller grupp till en roll under en kort varaktighet (till exempel en dag). I det här fallet behöver de tilldelade användarna inte begära aktivering. I det här scenariot kan Privileged Identity Management inte tillämpa Multi-Factor Authentication när användaren använder sin roll tilldelning eftersom de redan är aktiva i rollen från den tid som den tilldelas.

För att säkerställa att resurs administratören som uppfyller tilldelningen är den som de säger att de är, kan du tillämpa Multi-Factor Authentication för aktiv tilldelning genom att markera rutan **kräv Multi-Factor Authentication i den aktiva tilldelningen** .

### <a name="require-multi-factor-authentication-on-activation"></a>Kräv Multi-Factor Authentication vid aktivering

Du kan kräva att användare som är berättigade till en roll för att bevisa vem de använder Azure AD Multi-Factor Authentication innan de kan aktivera. Multi-Factor Authentication säkerställer att användaren är den som har rimlig säkerhet. Att framtvinga det här alternativet skyddar viktiga resurser i situationer när användar kontot kan ha komprometterats.

Om du vill kräva Multi-Factor Authentication innan aktiveringen, markerar du kryss rutan **kräv Multi-Factor Authentication vid aktivering** .

Mer information finns i [Multi-Factor Authentication och Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Maximal varaktighet för aktivering

Använd skjutreglaget **maximal varaktighet** för att ange den maximala tiden, i timmar, som en roll förblir aktiv innan den upphör att gälla. Värdet kan vara mellan 1 och 24 timmar.

## <a name="require-justification"></a>Kräv motivering

Du kan kräva att användarna anger en affärs justering när de aktiverar. Om du vill kräva en motivering markerar du kryss rutan **Kräv motivering för aktiva tilldelningar** eller kryss rutan **Kräv motivering för aktivering** .

## <a name="require-approval-to-activate"></a>Kräv godkännande för att aktivera

Följ dessa steg om du vill kräva godkännande för att aktivera en roll.

1. Markera kryss rutan **Kräv godkännande för aktivering** .

1. Välj **Välj god kännare** för att öppna sidan **Välj en medlem eller grupp** .

    ![Välj en användare eller grupp fönster för att välja god kännare](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Välj minst en användare eller grupp och klicka sedan på **Välj**. Du kan lägga till valfri kombination av användare och grupper. Du måste välja minst en god kännare. Det finns inga standard god kännare.

    Dina val kommer att visas i listan över valda god kännare.

1. När du har angett alla dina roll inställningar väljer du **Uppdatera** för att spara ändringarna.

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure-resurs roller i Privileged Identity Management](pim-resource-roles-assign-roles.md)
- [Konfigurera säkerhets aviseringar för Azures resurs roller i Privileged Identity Management](pim-resource-roles-configure-alerts.md)
