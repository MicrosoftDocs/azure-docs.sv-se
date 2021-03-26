---
title: Konfigurera inställningar för privilegierade åtkomst grupper i PIM-Azure Active Directory | Microsoft Docs
description: Lär dig hur du konfigurerar inställningar för roll tilldelnings grupper i Azure AD Privileged Identity Management (PIM).
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
ms.date: 07/27/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cfb09f383d8425a644d3e2e87d190b350f5f41a
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564644"
---
# <a name="configure-privileged-access-group-settings-preview-in-privileged-identity-management"></a>Konfigurera inställningar för privilegierad åtkomst grupp (för hands version) i Privileged Identity Management

Roll inställningar är de standardinställningar som tillämpas på grupp ägares och privilegierade åtkomst tilldelningar för grupp medlemmar i Privileged Identity Management (PIM). Använd följande steg för att konfigurera arbets flödet för godkännande för att ange vem som kan godkänna eller neka begär Anden om behörighets höjning.

## <a name="open-role-settings"></a>Öppna roll inställningar

Följ de här stegen för att öppna inställningarna för en Azure Privileged Access Group-roll.

1. Logga in på [Azure Portal](https://portal.azure.com/) med en användare i rollen [privilegie rad roll administratör](../roles/permissions-reference.md#privileged-role-administrator) .

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **privilegie rad åtkomst (för hands version)**.

1. Välj den grupp som du vill hantera.

    ![Privilegierade åtkomst grupper som filtrerats efter ett grupp namn](./media/groups-role-settings/group-select.png)

1. Välj **inställningar**.

    ![Sidan Inställningar visar grupp inställningarna för den valda gruppen](./media/groups-role-settings/group-settings-select-role.png)

1. Välj den ägare eller medlems roll vars inställningar du vill visa eller ändra. Du kan visa de aktuella inställningarna för rollen på sidan **information om roll inställningar** .

    ![Sidan information om roll inställning visar flera tilldelnings-och aktiverings inställningar](./media/groups-role-settings/group-role-setting-details.png)

1. Klicka på **Redigera** för att öppna sidan **Redigera inställning för roll** . På fliken **aktivering** kan du ändra inställningarna för roll aktivering, inklusive om du vill tillåta permanent kvalificerade och aktiva tilldelningar.

    ![Sidan Redigera roll inställningar med fliken aktivering öppen](./media/groups-role-settings/role-settings-activation-tab.png)

1. Välj fliken **tilldelning** för att öppna fliken tilldelnings inställningar. De här inställningarna styr inställningarna för Privileged Identity Management tilldelning för den här rollen.

    ![Fliken roll tilldelning på sidan roll inställningar](./media/groups-role-settings/role-settings-assignment-tab.png)

1. Använd fliken **meddelande** eller **Nästa: aktiverings** knappen längst ned på sidan för att komma till fliken meddelande inställning för den här rollen. De här inställningarna styr alla e-postaviseringar relaterade till den här rollen.

    ![Fliken roll meddelanden på sidan roll inställningar](./media/groups-role-settings/role-settings-notification-tab.png)

1. Välj knappen **Uppdatera** när som helst när du vill uppdatera roll inställningarna.

På fliken **meddelanden** på sidan roll inställningar kan Privileged Identity Management ge detaljerad kontroll över vem som tar emot meddelanden och vilka meddelanden de får.

- **Stänga av ett e-postmeddelande**<br>Du kan inaktivera vissa e-postmeddelanden genom att avmarkera kryss rutan standard mottagare och ta bort ytterligare mottagare.  
- **Begränsa e-postmeddelanden till angivna e-postadresser**<br>Du kan stänga av e-postmeddelanden som skickas till standard mottagare genom att avmarkera kryss rutan standard mottagare. Du kan sedan lägga till ytterligare e-postadresser som ytterligare mottagare. Om du vill lägga till fler än en e-postadress avgränsar du dem med ett semikolon (;).
- **Skicka e-postmeddelanden till både standard mottagare och ytterligare mottagare**<br>Du kan skicka e-postmeddelanden till både standard mottagare och ytterligare mottagare genom att markera kryss rutan standard mottagare och lägga till e-postadresser för ytterligare mottagare.
- **Endast kritiska e-postmeddelanden**<br>För varje typ av e-post kan du bara markera kryss rutan för att ta emot viktiga e-postmeddelanden. Det innebär att Privileged Identity Management fortsätter att skicka e-postmeddelanden till de konfigurerade mottagarna endast om e-postmeddelandet kräver en omedelbar åtgärd. E-postmeddelanden som uppmanar användarna att utöka sin roll tilldelning utlöses till exempel inte medan ett e-postmeddelande som kräver administratörer för att godkänna en tilläggs förfrågan utlöses.

## <a name="assignment-duration"></a>Varaktighet för tilldelning

Du kan välja mellan två alternativ för tilldelnings varaktighet för varje tilldelnings typ (giltig och aktiv) när du konfigurerar inställningar för en roll. De här alternativen blir den maximala standard varaktigheten när en användare tilldelas rollen i Privileged Identity Management.

Du kan välja något av följande **tillgängliga** varaktighets alternativ för tilldelning:

| | Description |
| --- | --- |
| **Tillåt permanent berättigad tilldelning** | Resurs administratörer kan tilldela permanent berättigad tilldelning. |
| **Giltig tilldelning har förfallit efter** | Resurs administratörer kan kräva att alla kvalificerade tilldelningar har ett angivet start-och slutdatum. |

Och du kan välja något av dessa alternativ för varaktighet för **aktiv** tilldelning:

| | Description |
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

    ![Välj en användare eller grupp fönster för att välja god kännare](./media/groups-role-settings/group-settings-select-approvers.png)

1. Välj minst en användare eller grupp och klicka sedan på **Välj**. Du kan lägga till valfri kombination av användare och grupper. Du måste välja minst en god kännare. Det finns inga standard god kännare.

    Dina val kommer att visas i listan över valda god kännare.

1. När du har angett alla dina roll inställningar väljer du **Uppdatera** för att spara ändringarna.

## <a name="next-steps"></a>Nästa steg

- [Tilldela grupp medlemskap eller ägarskap för privilegie rad åtkomst i PIM](groups-assign-member-owner.md)
