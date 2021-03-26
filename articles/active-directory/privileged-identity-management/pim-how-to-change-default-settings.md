---
title: Konfigurera inställningar för Azure AD-roller i PIM – Azure AD | Microsoft Docs
description: Lär dig hur du konfigurerar inställningar för Azure AD-roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 02/28/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 676fdf264cb930069425a330c3da9cd753bf8e0a
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567041"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Konfigurera inställningar för Azure AD-roller i Privileged Identity Management

En privilegie rad roll administratör kan anpassa Privileged Identity Management (PIM) i sin Azure Active Directory (Azure AD) organisation, inklusive ändra upplevelsen för en användare som aktiverar en berättigad roll tilldelning.

## <a name="determine-your-version-of-pim"></a>Fastställ din version av PIM

Från och med november 2019 uppdateras Azure AD-rollerna i Privileged Identity Management till en ny version som matchar upplevelserna för Azures resurs roller. Detta skapar ytterligare funktioner samt [ändringar i det befintliga API: et](azure-ad-roles-features.md#api-changes). Även om den nya versionen publiceras, vilka procedurer som du följer i den här artikeln beror på vilken version av Privileged Identity Management du för närvarande har. Följ stegen i det här avsnittet för att ta reda på vilken version av Privileged Identity Management du har. När du känner till din version av Privileged Identity Management kan du välja de procedurer i den här artikeln som matchar den versionen.

1. Logga in på [Azure Portal](https://portal.azure.com/) med en användare som har rollen [privilegierad roll administratör](../roles/permissions-reference.md#privileged-role-administrator) .
1. Öppna **Azure AD Privileged Identity Management**. Om du har en banderoll överst på sidan Översikt, följer du anvisningarna på fliken **ny version** i den här artikeln. Annars följer du anvisningarna på fliken **tidigare version** .

  [![Välj Azure AD-> Privileged Identity Management.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Följ stegen i den här artikeln för att godkänna eller neka begär Anden för Azure AD-roller.

# <a name="new-version"></a>[Ny version](#tab/new)

## <a name="open-role-settings"></a>Öppna roll inställningar

Följ de här stegen för att öppna inställningarna för en Azure AD-roll.

1. Logga in på [Azure Portal](https://portal.azure.com/) med en användare i rollen [privilegie rad roll administratör](../roles/permissions-reference.md#privileged-role-administrator) .

1. Öppna **Azure AD Privileged Identity Management** &gt; roll inställningar för **Azure AD-roller** &gt; .

    ![Sidan roll inställningar visar Azure AD-roller](./media/pim-how-to-change-default-settings/role-settings.png)

1. Välj den roll vars inställningar du vill konfigurera.

    ![Sidan information om roll inställning visar flera tilldelnings-och aktiverings inställningar](./media/pim-how-to-change-default-settings/role-settings-page.png)

1. Välj **Redigera** för att öppna sidan roll inställningar.

    ![Sidan Redigera roll inställningar med alternativ för att uppdatera tilldelnings-och aktiverings inställningar](./media/pim-how-to-change-default-settings/role-settings-edit.png)

    I fönstret roll inställning för varje roll finns det flera inställningar som du kan konfigurera.

## <a name="assignment-duration"></a>Varaktighet för tilldelning

Du kan välja mellan två alternativ för tilldelnings varaktighet för varje tilldelnings typ (giltig och aktiv) när du konfigurerar inställningar för en roll. De här alternativen blir den maximala standard varaktigheten när en användare tilldelas rollen i Privileged Identity Management.

Du kan välja något av följande **tillgängliga** varaktighets alternativ för tilldelning:

| | Description |
| --- | --- |
| **Tillåt permanent berättigad tilldelning** | Globala administratörer och administratörer för privilegierade roller kan tilldela permanent berättigad tilldelning. |
| **Giltig tilldelning har förfallit efter** | Globala administratörer och administratörer av privilegierade roller kan kräva att alla kvalificerade tilldelningar har ett angivet start-och slutdatum. |

Och du kan välja något av dessa alternativ för varaktighet för **aktiv** tilldelning:

| | Description |
| --- | --- |
| **Tillåt permanent aktiv tilldelning** | Globala administratörer och administratörer för privilegierade roller kan tilldela permanent aktiv tilldelning. |
| **Aktiv tilldelning förfaller efter** | Globala administratörer och administratörer av privilegierade roller kan kräva att alla aktiva tilldelningar har ett angivet start-och slutdatum. |

> [!NOTE]
> Alla tilldelningar som har ett angivet slutdatum kan förnyas av globala administratörer och privilegierade roll administratörer. Användare kan även initiera självbetjänings begär Anden för att [utöka eller förnya roll tilldelningar](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Kräv Multi-Factor Authentication

Privileged Identity Management tillhandahåller en valfri tillämpning av Azure AD Multi-Factor Authentication för två olika scenarier.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Kräv Multi-Factor Authentication för aktiv tilldelning

I vissa fall kanske du vill tilldela en användare en roll för en kort varaktighet (till exempel en dag). I det här fallet behöver de tilldelade användarna inte begära aktivering. I det här scenariot kan Privileged Identity Management inte tillämpa Multi-Factor Authentication när användaren använder sin roll tilldelning eftersom de redan är aktiva i rollen från den tid som den tilldelas.

För att säkerställa att administratören som uppfyller tilldelningen är den som de säger att de är, kan du tillämpa Multi-Factor Authentication för aktiv tilldelning genom att markera rutan **kräv Multi-Factor Authentication i den aktiva tilldelningen** .

### <a name="require-multi-factor-authentication-on-activation"></a>Kräv Multi-Factor Authentication vid aktivering

Du kan kräva att användare som är berättigade till en roll för att bevisa vem de använder Azure AD Multi-Factor Authentication innan de kan aktivera. Multi-Factor Authentication säkerställer att användaren är den som har rimlig säkerhet. Att framtvinga det här alternativet skyddar viktiga resurser i situationer när användar kontot kan ha komprometterats.

Om du vill kräva Multi-Factor Authentication före aktiveringen markerar du rutan **kräv Multi-Factor Authentication på aktivering** på fliken tilldelning i **inställningen redigera roll**.

Mer information finns i [Multi-Factor Authentication och Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Maximal varaktighet för aktivering

Använd skjutreglaget **maximal varaktighet** för att ange den maximala tiden, i timmar, som en roll förblir aktiv innan den upphör att gälla. Värdet kan vara mellan 1 och 24 timmar.

## <a name="require-justification"></a>Kräv motivering

Du kan kräva att användarna anger en affärs justering när de aktiverar. Om du vill kräva en motivering markerar du kryss rutan **Kräv motivering för aktiva tilldelningar** eller kryss rutan **Kräv motivering för aktivering** .

## <a name="require-approval-to-activate"></a>Kräv godkännande för att aktivera

Om du anger flera god kännare slutförs godkännandet så snart en av dem godkänner eller nekar. Du kan inte kräva godkännande från minst två användare. Följ dessa steg om du vill kräva godkännande för att aktivera en roll.

1. Markera kryss rutan **Kräv godkännande för aktivering** .

1. Välj **Välj god kännare**.

    ![Välj en användare eller grupp fönster för att välja god kännare](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Välj minst en användare och klicka sedan på **Välj**. Du måste välja minst en god kännare. Det finns inga standard god kännare.

    Dina val kommer att visas i listan över valda god kännare.

1. När du har angett alla dina roll inställningar väljer du **Uppdatera** för att spara ändringarna.

# <a name="previous-version"></a>[Tidigare version](#tab/previous)

## <a name="open-role-settings-previous-version"></a>Öppna roll inställningar (tidigare version)

Följ de här stegen för att öppna inställningarna för en Azure AD-roll.

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure AD-roller**.

1. Välj **inställningar**.

    ![Azure AD-roller – inställningar](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Välj **roller**.

1. Välj den roll vars inställningar du vill konfigurera.

    ![Azure AD-roller – inställnings roller](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    På sidan Inställningar för varje roll finns det flera inställningar som du kan konfigurera. De här inställningarna påverkar endast användare som är **berättigade** tilldelningar, inte **permanenta** tilldelningar.

## <a name="activations"></a>Aktiveringar

Använd skjutreglaget **aktiveringar** för att ange den maximala tiden, i timmar, som en roll förblir aktiv innan den upphör att gälla. Värdet kan vara mellan 1 och 72 timmar.

## <a name="notifications"></a>Meddelanden

Använd växeln **meddelanden** för att ange om administratörer ska få e-postaviseringar när roller aktive ras. Det här meddelandet kan vara användbart för att upptäcka otillåtna eller illegitimatea aktiveringar.

När inställningen är **aktive** rad skickas meddelanden till:

- Privilegie rad roll administratör
- Säkerhetsadministratör
- Global administratör

Mer information finns i [e-postmeddelanden i Privileged Identity Management](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Incident/begär biljett

Använd växeln **incident/begär biljett** för att kräva att berättigade administratörer inkluderar ett biljett nummer när de aktiverar sin roll. Den här metoden kan göra granskningar av roll åtkomsten mer effektiva.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Använd växeln **Multi-Factor Authentication** för att ange om du vill kräva att användarna verifierar sin identitet med MFA innan de kan aktivera sina roller. De behöver bara verifiera sin identitet en gång per session, inte varje gång de aktiverar en roll. Det finns två tips att tänka på när du aktiverar MFA:

- Användare som har Microsoft-konton för sina e-postadresser (vanligt vis @outlook.com , men inte alltid) kan inte registreras för Azure AD-Multi-Factor Authentication. Om du vill tilldela roller till användare med Microsoft-konton bör du antingen göra dem permanenta administratörer eller inaktivera Multi-Factor Authentication för rollen.
- Det går inte att inaktivera Azure AD Multi-Factor Authentication för mycket privilegierade roller för Azure AD och Microsoft 365. Den här säkerhetsfunktionen hjälper till att skydda följande roller:  
  
  - Azure Information Protection administratör
  - Faktureringsadministratör
  - Moln program administratör
  - Administratör för efterlevnad
  - Administratör för villkorsstyrd åtkomst
  - Dynamics 365-administratör
  - Åtkomst god kännare för kund lås
  - Katalog skrivare
  - Exchange-administratör
  - Global administratör
  - Intune-administratör
  - Power BI administratör
  - Privilegie rad roll administratör
  - Säkerhetsadministratör
  - SharePoint-administratör
  - Skype for Business-administratör
  - Användaradministratör

Mer information finns i [Multi-Factor Authentication och Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Kräv godkännande

Följ dessa steg om du vill delegera det godkännande som krävs för att aktivera en roll.

1. Ange växeln **Kräv godkännande** till **aktive rad**. Fönstret expanderar med alternativ för att välja god kännare.

    ![Skärm bild som visar växeln "Kräv godkännande" med "Aktivera" vald.](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Om du inte anger några god kännare blir den privilegierade roll administratören som standard god kännare och krävs sedan för att godkänna alla aktiverings begär Anden för den här rollen.

1. Klicka på **Välj god kännare** om du vill lägga till god kännare.

    ![Azure AD-roller – inställningar – Kräv godkännande](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Välj en eller flera god kännare förutom den privilegierade roll administratören och klicka sedan på **Välj**. Vi rekommenderar att du lägger till minst två god kännare. Även om du lägger till dig själv som god kännare kan du inte själv godkänna en roll aktivering. Dina val kommer att visas i listan över valda god kännare.

1. När du har angett alla dina roll inställningar väljer du **Spara** för att spara ändringarna.

---

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure AD-roller i Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Konfigurera säkerhets aviseringar för Azure AD-roller i Privileged Identity Management](pim-how-to-configure-security-alerts.md)
