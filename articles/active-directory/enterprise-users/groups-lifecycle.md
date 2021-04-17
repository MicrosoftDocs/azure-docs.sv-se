---
title: Ange förfallodatum för Microsoft 365 grupper – Azure Active Directory | Microsoft Docs
description: Konfigurera förfallotid för Microsoft 365 grupper i Azure Active Directory
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
ms.openlocfilehash: 8af1a5e73592dc1c3392f0bc1fecfe6139a54710
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529839"
---
# <a name="configure-the-expiration-policy-for-microsoft-365-groups"></a>Konfigurera förfalloprincipen för Microsoft 365 grupper

Den här artikeln beskriver hur du hanterar livscykeln för Microsoft 365 genom att ange en förfalloprincip för dem. Du kan bara ange förfalloprincip för Microsoft 365 grupper i Azure Active Directory (Azure AD).

När du har angett att en grupp ska upphöra att gälla:

- Grupper med användaraktiviteter förnyas automatiskt när förfallotiden närmar sig.
- Ägare av gruppen meddelas om att förnya gruppen om gruppen inte förnyas automatiskt.
- Alla grupper som inte förnyas tas bort.
- Alla Microsoft 365 borttagna grupper kan återställas inom 30 dagar av gruppägare eller administratören.

För närvarande kan endast en förfalloprincip konfigureras för alla Microsoft 365 grupper i en Azure AD-organisation.

> [!NOTE]
> Konfiguration och användning av förfalloprincipen för Microsoft 365-grupper kräver att du har men inte nödvändigtvis tilldelar Azure AD Premium-licenser för medlemmarna i alla grupper som förfalloprincipen tillämpas på.

Information om hur du laddar ned och installerar Azure AD PowerShell-cmdlets finns i Azure Active Directory PowerShell för [Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="activity-based-automatic-renewal"></a>Aktivitet baserad på automatisk förnyelse

Med Azure AD-intelligens förnyas grupper automatiskt baserat på om de nyligen har använts. Den här funktionen eliminerar behovet av manuella åtgärder av gruppägare, eftersom den baseras på användaraktivitet i grupper över Microsoft 365 tjänster som Outlook, SharePoint eller Teams. Om en ägare eller gruppmedlem till exempel gör något som att ladda upp ett dokument i SharePoint, besöka en Teams-kanal eller skicka ett e-postmeddelande till gruppen i Outlook, förnyas gruppen automatiskt cirka 35 dagar innan gruppen upphör att gälla och ägaren får inga förnyelsemeddelanden.

### <a name="activities-that-automatically-renew-group-expiration"></a>Aktiviteter som automatiskt förnyar gruppförfallotid

Följande användaråtgärder orsakar automatisk gruppförnyelse:

- SharePoint: Visa, redigera, ladda ned, flytta, dela eller ladda upp filer
- Outlook: Anslut till grupp, läsa/skriva gruppmeddelande från grupputrymme, som ett meddelande (i Outlook Web Access)
- Teams: Besök en Teams-kanal

### <a name="auditing-and-reporting"></a>Granskning och rapportering

Administratörer kan hämta en lista över automatiskt förnyade grupper från aktivitetsgranskningsloggarna i Azure AD.

![Automatisk förnyelse av grupper baserat på aktivitet](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>Roller och behörigheter

Följande är roller som kan konfigurera och använda förfallotid för Microsoft 365 grupper i Azure AD.

Roll | Behörigheter
-------- | --------
Global administratör, gruppadministratör eller användaradministratör | Kan skapa, läsa, uppdatera eller ta bort Microsoft 365 förfalloprincipinställningar för grupper<br>Kan förnya valfri Microsoft 365 grupp
Användare | Kan förnya en Microsoft 365 grupp som de äger<br>Kan återställa en Microsoft 365 grupp som de äger<br>Kan läsa inställningarna för förfalloprincipen

Mer information om behörigheter för att återställa en borttagna grupp finns i [Restore a deleted Microsoft 365 group in Azure Active Directory](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Ange gruppförfallotid

1. Öppna [Azure AD-administrationscentret](https://aad.portal.azure.com) med ett konto som är en global administratör i Din Azure AD-organisation.

2. Välj **Grupper** och välj sedan **Förfallotid** för att öppna förfalloinställningarna.
  
   ![Förfalloinställningar för grupper](./media/groups-lifecycle/expiration-settings.png)

3. På  sidan Förfallotid kan du:

    - Ange gruppens livslängd i dagar. Du kan välja ett av de förinställda värdena eller ett anpassat värde (bör vara 30 dagar eller mer).
    - Ange en e-postadress där meddelanden om förnyelse och förfallodatum ska skickas när en grupp inte har någon ägare.
    - Välj vilka Microsoft 365 grupper ska upphöra att gälla. Du kan ange förfallotid för:
      - **Alla** Microsoft 365 grupper
      - En lista över **valda** Microsoft 365 grupper
      - **Ingen** för att begränsa förfallotiden för alla grupper
    - Spara inställningarna när du är klar genom att välja **Spara.**

> [!NOTE]
> - När du först ställer in förfallotid anges alla grupper som är äldre än utgångsintervallet till 35 dagar tills de upphör att gälla, såvida inte gruppen förnyas automatiskt eller ägaren förnyar den.
> - När en dynamisk grupp tas bort och återställs visas den som en ny grupp och fylls i igen enligt regeln. Den här processen kan ta upp till 24 timmar.
> - Förfallovarningar för grupper som används i Teams visas i Teams-ägarfeeden.
> - När du aktiverar förfallotid för valda grupper kan du lägga till upp till 500 grupper i listan. Om du behöver lägga till fler än 500 grupper kan du aktivera förfallotid för alla dina grupper. I det scenariot gäller inte begränsningen på 500 grupper.

## <a name="email-notifications"></a>E-postmeddelanden

Om grupper inte förnyas automatiskt skickas e-postaviseringar som denna till Microsoft 365-gruppägare 30 dagar, 15 dagar och 1 dag innan gruppen upphör att gälla. Språket för e-postmeddelandet bestäms av gruppägarens föredragna språk eller Språkinställning för Azure AD. Om gruppägaren har definierat ett föredraget språk, eller om flera ägare har samma önskade språk, används det språket. I alla andra fall används språkinställningen i Azure AD.

![E-postaviseringar för förfallodatum](./media/groups-lifecycle/expiration-notification.png)

Från **e-postmeddelandet** Förnya gruppaviseringar kan gruppägare komma åt sidan med gruppinformation direkt i [Åtkomstpanelen](https://account.activedirectory.windowsazure.com/r#/applications). Där kan användarna få mer information om gruppen, till exempel dess beskrivning, när den förnyades senast, när den upphör att gälla och även möjligheten att förnya gruppen. Sidan med gruppinformation innehåller nu även länkar till Microsoft 365 gruppresurser, så att gruppägaren enkelt kan visa innehållet och aktiviteten i gruppen.

När en grupp upphör att gälla tas gruppen bort en dag efter förfallodatumet. Ett e-postmeddelande som detta skickas till Microsoft 365-gruppägare som informerar dem om förfallodatumet och efterföljande borttagning av Microsoft 365 grupp.

![E-postaviseringar om gruppborttagning](./media/groups-lifecycle/deletion-notification.png)

Gruppen kan återställas inom 30 dagar från  borttagningen genom att välja Återställ grupp eller med hjälp av PowerShell-cmdlets, enligt beskrivningen i Återställa en borttagen [Microsoft 365-grupp i Azure Active Directory](groups-restore-deleted.md). Observera att perioden för gruppåterställning på 30 dagar inte kan anpassas.

Om gruppen som du återställer innehåller dokument, SharePoint-webbplatser eller andra beständiga objekt kan det ta upp till 24 timmar att återställa gruppen och dess innehåll helt.

## <a name="how-to-retrieve-microsoft-365-group-expiration-date"></a>Så här hämtar Microsoft 365 förfallodatum för grupp

Förutom att Åtkomstpanelen användare kan visa gruppinformation, inklusive förfallodatum och senaste förnyade datum, kan utgångsdatum för en Microsoft 365-grupp hämtas från Microsoft Graph REST API Beta. expirationDateTime som en gruppegenskap har aktiverats i Microsoft Graph Beta. Den kan hämtas med en GET-begäran. Mer information finns i det [här exemplet.](/graph/api/group-get?view=graph-rest-beta#example&preserve-view=true)

> [!NOTE]
> För att kunna hantera gruppmedlemskap på Åtkomstpanelen måste "Begränsa åtkomsten till grupper i Åtkomstpanelen" vara inställt på "Nej" i Azure Active Directory grupp allmänna inställningar.

## <a name="how-microsoft-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Så Microsoft 365 gruppförfallotid med en postlåda i juridiskt giltigt förfallotid

När en grupp upphör att gälla och tas bort tas gruppens data bort permanent 30 dagar efter borttagningen från appar som Planner, Sites eller Teams, men grupppostlådepostlådan som finns kvar i juridiska frågor bevaras och tas inte bort permanent. Administratören kan använda Exchange-cmdlets för att återställa postlådan för att hämta data.

## <a name="how-microsoft-365-group-expiration-works-with-retention-policy"></a>Så här Microsoft 365 gruppförfallotid med kvarhållningsprincip

Bevarandeprincipen konfigureras via Säkerhets- och efterlevnadscenter. Om du har ställt in en bevarandeprincip för Microsoft 365-grupper sparas gruppkonversationer i grupppostlådan och filer på gruppplatsen i kvarhållningscontainern under det specifika antal dagar som definierats i kvarhållningsprincipen när en grupp upphör att gälla och tas bort. Användarna ser inte gruppen eller dess innehåll efter förfallodatumet, men kan återställa plats- och postlådedata via e-identifiering.

## <a name="powershell-examples"></a>PowerShell-exempel

Här följer exempel på hur du kan använda PowerShell-cmdlets för att konfigurera förfalloinställningar för Microsoft 365 grupper i Azure AD-organisationen:

1. Installera PowerShell v2.0-modulen och logga in i PowerShell-prompten:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. Konfigurera förfalloinställningarna Använd cmdleten New-AzureADMSGroupLifecyclePolicy för att ange livslängden för alla Microsoft 365 i Azure AD-organisationen till 365 dagar. Förnyelsemeddelanden för Microsoft 365 grupper utan ägare skickas till emailaddress@contoso.com " "
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. Hämta den befintliga principen Get-AzureADMSGroupLifecyclePolicy: Den här cmdleten hämtar de Microsoft 365 inställningarna för gruppförfallotid som har konfigurerats. I det här exemplet kan du se:

   - Princip-ID
   - Livslängden för alla Microsoft 365 i Azure AD-organisationen är inställd på 365 dagar
   - Förnyelsemeddelanden för Microsoft 365 grupper utan ägare skickas till " emailaddress@contoso.com ."
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. Uppdatera den befintliga principen Set-AzureADMSGroupLifecyclePolicy: Den här cmdleten används för att uppdatera en befintlig princip. I exemplet nedan ändras grupplivslängden i den befintliga principen från 365 dagar till 180 dagar.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. Lägg till specifika grupper i principen Add-AzureADMSLifecyclePolicyGroup: Den här cmdleten lägger till en grupp i livscykelprincipen. Som exempel:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. Ta bort den befintliga principen Remove-AzureADMSGroupLifecyclePolicy: Den här cmdleten tar Microsoft 365 inställningarna för gruppförfallotid men kräver princip-ID:t. Den här cmdleten inaktiverar förfallotid för Microsoft 365 grupper.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
Följande cmdlets kan användas för att konfigurera principen i detalj. Mer information finns i [PowerShell-dokumentationen.](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#groups)

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
