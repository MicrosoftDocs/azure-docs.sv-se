---
title: Ange förfallo datum för Microsoft 365 grupper – Azure Active Directory | Microsoft Docs
description: Så här konfigurerar du förfallo datum för Microsoft 365 grupper i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a4ba5a9211cdb89f6ac5a92a315378e58f5d08a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105709522"
---
# <a name="configure-the-expiration-policy-for-microsoft-365-groups"></a>Konfigurera förfallo principen för Microsoft 365 grupper

Den här artikeln beskriver hur du hanterar Microsoft 365 gruppers livs cykel genom att ange en förfallo princip för dem. Du kan bara ange förfallo princip för Microsoft 365 grupper i Azure Active Directory (Azure AD).

När du har angett att en grupp upphör att gälla:

- Grupper med användar aktiviteter förnyas automatiskt när de upphör snart.
- Ägare av gruppen meddelas om att förnya gruppen om gruppen inte förnyas automatiskt.
- Alla grupper som inte förnyas tas bort.
- Alla Microsoft 365s grupper som tas bort kan återställas inom 30 dagar av gruppen ägare eller administratören.

För närvarande kan endast en princip för förfallo datum konfigureras för alla Microsoft 365 grupper i en Azure AD-organisation.

> [!NOTE]
> Genom att konfigurera och använda principen för förfallo datum för Microsoft 365 grupper måste du ha, men inte nödvändigt vis tilldela Azure AD Premium licenser för medlemmarna i alla grupper som förfallo principen tillämpas för.

Information om hur du hämtar och installerar Azure AD PowerShell-cmdlets finns i [Azure Active Directory PowerShell för Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="activity-based-automatic-renewal"></a>Aktivitet baserad på automatisk förnyelse

Med Azure AD Intelligence förnyas nu grupper automatiskt utifrån om de har använts nyligen. Den här funktionen eliminerar behovet av manuell åtgärd av grupp ägare, eftersom den baseras på användar aktivitet i grupper mellan Microsoft 365 tjänster som Outlook, SharePoint eller teams. Om till exempel en ägare eller en grupp medlem gör något som t. ex. att ladda upp ett dokument i SharePoint kan du gå till en Team kanal eller skicka ett e-postmeddelande till gruppen i Outlook. gruppen förnyas automatiskt runt 35 dagar innan gruppen upphör att gälla och ägaren inte får några förnyelse meddelanden.

### <a name="activities-that-automatically-renew-group-expiration"></a>Aktiviteter som automatiskt förnyar grupp förfallo datum

Följande användar åtgärder orsakar automatisk grupp förnyelse:

- SharePoint: Visa, redigera, ladda ned, flytta, dela eller ladda upp filer
- Outlook: Anslut till grupp, läsa/skriva grupp meddelande från grupp utrymme, som ett meddelande (i Outlook Web Access)
- Team: besök en Team-kanal

### <a name="auditing-and-reporting"></a>Granskning och rapportering

Administratörer kan hämta en lista över automatiskt förnyade grupper från aktivitets gransknings loggarna i Azure AD.

![Automatisk förnyelse av grupper baserat på aktivitet](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>Roller och behörigheter

Följande är roller som kan konfigurera och använda förfallo datum för Microsoft 365 grupper i Azure AD.

Roll | Behörigheter
-------- | --------
Global administratör, grupp administratör eller användar administratör | Kan skapa, läsa, uppdatera eller ta bort princip inställningarna för utgångna Microsoft 365 grupper<br>Kan förnya alla Microsoft 365s grupper
User | Kan förnya en Microsoft 365 grupp som de äger<br>Kan återställa en Microsoft 365 grupp som de äger<br>Kan läsa princip inställningarna för förfallo datum

Mer information om behörigheter för att återställa en borttagen grupp finns [i återställa en borttagen Microsoft 365 grupp i Azure Active Directory](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Ange gruppförfallotid

1. Öppna [administrations centret för Azure AD](https://aad.portal.azure.com) med ett konto som är en global administratör i din Azure AD-organisation.

2. Välj **grupper** och välj sedan **förfallo datum** för att öppna inställningarna för förfallo datum.
  
   ![Förfallo inställningar för grupper](./media/groups-lifecycle/expiration-settings.png)

3. På sidan **förfallo datum** kan du:

    - Ange gruppens livs längd i dagar. Du kan välja ett av de förinställda värdena eller ett anpassat värde (bör vara 30 dagar eller mer).
    - Ange en e-postadress där förnyelse-och förfallo meddelanden ska skickas när en grupp saknar ägare.
    - Välj vilka Microsoft 365 grupper som upphör att gälla. Du kan ange förfallo datum för:
      - **Alla** Microsoft 365 grupper
      - En lista med **valda** Microsoft 365 grupper
      - **Ingen** för att begränsa förfallo datum för alla grupper
    - Spara inställningarna när du är klar genom att välja **Spara**.

> [!NOTE]
> - När du först konfigurerar förfallo datum är alla grupper som är äldre än utgångs intervallet inställt på 35 dagar tills det upphör att gälla, om inte gruppen förnyas automatiskt eller om ägaren förnyar den.
> - När en dynamisk grupp tas bort och återställs, visas den som en ny grupp och fylls i igen enligt regeln. Den här processen kan ta upp till 24 timmar.
> - Meddelanden om förfallo datum för grupper som används i team visas i team ägarens feed.

## <a name="email-notifications"></a>E-postmeddelanden

Om grupper inte förnyas automatiskt, skickas e-postaviseringar som den här skickas till Microsoft 365 grupp ägare 30 dagar, 15 dagar och 1 dag innan gruppen upphör att gälla. E-postmeddelandets språk bestäms av grupp ägarens önskade språk eller språk inställning för Azure AD. Om grupp ägaren har definierat ett önskat språk, eller om flera ägare har samma föredragna språk, används det språket. I alla andra fall används språk inställningen för Azure AD.

![E-postmeddelanden för förfallo datum](./media/groups-lifecycle/expiration-notification.png)

Från e-postmeddelandet **förnya grupp** kan grupp ägare direkt komma åt grupp informations sidan på [åtkomst panelen](https://account.activedirectory.windowsazure.com/r#/applications). Användarna kan få mer information om gruppen, till exempel en beskrivning, när den senast förnyades, när den upphör att gälla och även möjligheten att förnya gruppen. Sidan grupp information innehåller nu också länkar till Microsoft 365 grupp resurser, så att grupp ägaren enkelt kan se innehållet och aktiviteten i gruppen.

När en grupp upphör att gälla tas gruppen bort en dag efter förfallo datumet. Ett e-postmeddelande, till exempel det här, skickas till Microsoft 365 grupp ägare som informerar dem om förfallo datum och efterföljande borttagning av Microsoft 365s gruppen.

![E-postmeddelanden för grupp borttagning](./media/groups-lifecycle/deletion-notification.png)

Gruppen kan återställas inom 30 dagar från dess borttagning genom att välja **återställnings grupp** eller med hjälp av PowerShell-cmdletar, enligt beskrivningen i [återställa en borttagen Microsoft 365 grupp i Azure Active Directory](groups-restore-deleted.md). Observera att återställnings perioden på 30 dagar inte kan anpassas.

Om gruppen som du återställer innehåller dokument, SharePoint-webbplatser eller andra beständiga objekt kan det ta upp till 24 timmar att helt återställa gruppen och dess innehåll.

## <a name="how-to-retrieve-microsoft-365-group-expiration-date"></a>Hämta Microsoft 365 gruppens förfallo datum

Förutom åtkomst panelen där användare kan visa grupp information inklusive förfallo datum och senaste förnyat datum, kan utgångs datumet för en Microsoft 365 grupp hämtas från Microsoft Graph REST API beta. expirationDateTime som en grupp egenskap har Aktiver ATS i Microsoft Graph beta. Den kan hämtas med en GET-begäran. Mer information finns i [det här exemplet](/graph/api/group-get?view=graph-rest-beta#example).

> [!NOTE]
> För att kunna hantera grupp medlemskap på åtkomst panelen måste "begränsa åtkomst till grupper i åtkomst panelen" anges till "nej" i Azure Active Directory grupper generell inställning.

## <a name="how-microsoft-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Så här upphör Microsoft 365s gruppen att fungera med en post låda för juridiskt bevarande

När en grupp upphör att gälla och tas bort, kommer 30 dagar efter att gruppens data har tagits bort från appar som planerar, webbplatser eller team tas bort permanent, men grupp post lådan som är i juridiskt bevarande behålls och tas inte bort permanent. Administratören kan använda Exchange-cmdletar för att återställa post lådan för att hämta data.

## <a name="how-microsoft-365-group-expiration-works-with-retention-policy"></a>Så här upphör Microsoft 365s gruppen att fungera med bevarande principen

Bevarande principen konfigureras med hjälp av Security and Compliance Center. Om du har ställt in en bevarande princip för Microsoft 365 grupper, när en grupp upphör att gälla och tas bort, behålls grupp konversationerna i gruppens post låda och filer på grupp webbplatsen i kvarhållning-behållaren för det angivna antalet dagar definierade i bevarande principen. Användare ser inte gruppen eller dess innehåll efter förfallo datum, men kan återställa plats-och Mailbox-data via e-identifiering.

## <a name="powershell-examples"></a>PowerShell-exempel

Här följer några exempel på hur du kan använda PowerShell-cmdlets för att konfigurera förfallo inställningarna för Microsoft 365 grupper i din Azure AD-organisation:

1. Installera PowerShell v 2.0-modulen och logga in i PowerShell-prompten:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. Konfigurera förfallo inställningarna Använd New-AzureADMSGroupLifecyclePolicy cmdlet för att ange livs längden för alla Microsoft 365 grupper i Azure AD-organisationen till 365 dagar. Förnyelse meddelanden för Microsoft 365 grupper utan att ägare skickas till emailaddress@contoso.com
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. Hämta den befintliga principen get-AzureADMSGroupLifecyclePolicy: denna cmdlet hämtar den aktuella Microsoft 365 gruppens förfallo inställningar som har kon figurer ATS. I det här exemplet kan du se:

   - Princip-ID
   - Livs längden för alla Microsoft 365 grupper i Azure AD-organisationen är inställd på 365 dagar
   - Förnyelse meddelanden för Microsoft 365 grupper utan att ägare skickas till " emailaddress@contoso.com ."
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. Uppdatera den befintliga princip uppsättningen-AzureADMSGroupLifecyclePolicy: den här cmdleten används för att uppdatera en befintlig princip. I exemplet nedan ändras gruppens livs längd i den befintliga principen från 365 dagar till 180 dagar.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. Lägga till vissa grupper i principen Add-AzureADMSLifecyclePolicyGroup: den här cmdleten lägger till en grupp i livs cykel principen. Som exempel:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. Ta bort den befintliga principen Remove-AzureADMSGroupLifecyclePolicy: den här cmdleten tar bort Microsoft 365 gruppens förfallo inställningar men kräver princip-ID. Den här cmdleten inaktiverar förfallo datum för Microsoft 365 grupper.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
Följande cmdletar kan användas för att konfigurera principen i mer detalj. Mer information finns i [PowerShell-dokumentationen](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#groups).

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Nästa steg

De här artiklarna innehåller ytterligare information om Azure AD-grupper.

- [Se befintliga grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Hantera inställningar för en grupp](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Hantera medlemmar i en grupp](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Hantera medlemskap i en grupp](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Hantera dynamiska regler för användare i en grupp](groups-dynamic-membership.md)
