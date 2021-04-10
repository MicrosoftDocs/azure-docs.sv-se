---
title: Tilldela känslighets etiketter till grupper – Azure AD | Microsoft Docs
description: Lär dig hur du tilldelar känslighets etiketter till grupper. Se felsöknings information och Visa fler tillgängliga resurser.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a78cf9b7d78078030ac0db8bd2f0fddb93a8dda4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97881404"
---
# <a name="assign-sensitivity-labels-to-microsoft-365-groups-in-azure-active-directory"></a>Tilldela känslighets etiketter till Microsoft 365 grupper i Azure Active Directory

Azure Active Directory (Azure AD) stöder användning av känslighets etiketter som publiceras av [Microsoft 365 Compliance Center](https://sip.protection.office.com/homepage) för att Microsoft 365 grupper. Känslighets etiketter gäller för grupp över tjänster som Outlook, Microsoft Teams och SharePoint. Mer information om stöd för Microsoft 365 Apps finns i [Microsoft 365 stöd för känslighets etiketter](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-sensitivity-labels).

> [!IMPORTANT]
> Om du vill konfigurera den här funktionen måste det finnas minst en aktiv Azure Active Directory Premium P1-licens i din Azure AD-organisation.

## <a name="enable-sensitivity-label-support-in-powershell"></a>Aktivera stöd för känslighets etikett i PowerShell

Om du vill tillämpa publicerade etiketter på grupper måste du först aktivera funktionen. De här stegen aktiverar funktionen i Azure AD.

1. Öppna ett Windows PowerShell-fönster på datorn. Du kan öppna den utan utökade privilegier.
1. Kör följande kommandon för att förbereda körningen av cmdletarna.

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    På sidan **Logga in på ditt konto** anger du ditt administratörs konto och lösen ord för att ansluta dig till tjänsten och väljer sedan **Logga** in.
1. Hämta de aktuella grupp inställningarna för Azure AD-organisationen.

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > Om inga grupp inställningar har skapats för den här Azure AD-organisationen får du ett fel i ovanstående cmdlet som läser "det går inte att binda argumentet till parametern" ID "eftersom det är null". I det här fallet måste du först skapa inställningarna. Följ stegen i [Azure Active Directory-cmdletar för att konfigurera grupp inställningar](../enterprise-users/groups-settings-cmdlets.md) för att skapa grupp inställningar för den här Azure AD-organisationen.

1. Sedan visar du de aktuella grupp inställningarna.

    ```PowerShell
    $Setting.Values
    ```

1. Aktivera sedan funktionen:

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. Spara ändringarna och tillämpa inställningarna:

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

Du måste också synkronisera dina känslighets etiketter till Azure AD. Instruktioner finns i [så här aktiverar du känslighets etiketter för behållare och synkronisera etiketter](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#how-to-enable-sensitivity-labels-for-containers-and-synchronize-labels).

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>Tilldela en etikett till en ny grupp i Azure Portal

1. Logga in på [administrations centret för Azure AD](https://aad.portal.azure.com).
1. Välj **grupper** och välj sedan **ny grupp**.
1. På sidan **ny grupp** väljer du **Office 365** och fyller sedan i nödvändig information för den nya gruppen och väljer en känslighets etikett i listan.

   ![Tilldela en känslighets etikett på sidan nya grupper](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. Spara ändringarna och välj **skapa**.

Gruppen skapas och de webbplats-och grupp inställningar som är associerade med den valda etiketten verkställs sedan automatiskt.

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>Tilldela en etikett till en befintlig grupp i Azure Portal

1. Logga in på [administrations centret för Azure AD](https://aad.portal.azure.com) med ett grupp administratörs konto eller som grupp ägare.
1. Välj **grupper**.
1. Välj den grupp som du vill märka på sidan **alla grupper** .
1. Välj **Egenskaper** på den valda gruppens sida och välj en känslighets etikett i listan.

   ![Tilldela en känslighets etikett på översikts sidan för en grupp](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. Välj **Spara** för att spara ändringarna.

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>Ta bort en etikett från en befintlig grupp i Azure Portal

1. Logga in på [administrations centret för Azure AD](https://aad.portal.azure.com) med ett globalt administratörs-eller grupp administratörs konto eller som grupp ägare.
1. Välj **grupper**.
1. På sidan **alla grupper** väljer du den grupp som du vill ta bort etiketten från.
1. På sidan **grupp** väljer du **Egenskaper**.
1. Välj **Ta bort**.
1. Klicka på **Spara** för att tillämpa dina ändringar.

## <a name="using-classic-azure-ad-classifications"></a>Använda klassiska Azure AD-klassificeringar

När du har aktiverat den här funktionen visas klassificeringarna "klassiska" för grupper endast befintliga grupper och platser, och du bör endast använda dem för nya grupper om du skapar grupper i appar som inte stöder känslighets etiketter. Administratören kan konvertera dem till känslighets etiketter senare om det behövs. Klassiska klassificeringar är de gamla klassificeringar som du ställer in genom att definiera värden för `ClassificationList` inställningen i Azure AD PowerShell. När den här funktionen är aktive rad kommer dessa klassificeringar inte att tillämpas på grupper.

## <a name="troubleshooting-issues"></a>Felsökning av problem

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>Känslighets etiketter är inte tillgängliga för tilldelning i en grupp

Alternativet känslighets etikett visas bara för grupper när följande villkor uppfylls:

1. Etiketter publiceras i Microsoft 365 Compliance Center för den här Azure AD-organisationen.
1. Funktionen är aktive rad, EnableMIPLabels har värdet true i från Azure AD PowerShell-modulen.
1. Etiketter synkroniseras till Azure AD med Execute-AzureAdLabelSync-cmdlet i PowerShell-modulen för säkerhets & kompatibilitet.
1. Gruppen är en Microsoft 365 grupp.
1. Organisationen har en aktiv Azure Active Directory Premium P1-licens.
1. Den aktuella inloggade användaren har tillräcklig behörighet för att tilldela etiketter. Användaren måste antingen vara global administratör, grupp administratör eller grupp ägare.

Se till att alla villkor är uppfyllda för att tilldela etiketter till en grupp.

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>Etiketten som jag vill tilldela finns inte i listan

Om etiketten som du söker efter inte finns i listan kan detta vara fallet av någon av följande orsaker:

- Etiketten kanske inte har publicerats i Microsoft 365 Compliance Center. Detta kan också gälla etiketter som inte längre publiceras. Kontakta administratören om du vill ha mer information.
- Etiketten kan vara publicerad, men den är inte tillgänglig för den användare som är inloggad. Kontakta administratören om du vill ha mer information om hur du får åtkomst till etiketten.

### <a name="how-to-change-the-label-on-a-group"></a>Ändra etiketten för en grupp

Etiketter kan bytas när som helst med samma steg som för att tilldela en etikett till en befintlig grupp, enligt följande:

1. Logga in på [administrations centret för Azure AD](https://aad.portal.azure.com) med ett globalt konto eller grupp administratörs konto eller som grupp ägare.
1. Välj **grupper**.
1. Välj den grupp som du vill märka på sidan **alla grupper** .
1. Välj **Egenskaper** på den valda gruppens sida och välj en ny känslighets etikett i listan.
1. Välj **Spara**.

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>Grupp inställnings ändringar för publicerade etiketter uppdateras inte i grupperna

Som bästa praxis rekommenderar vi inte att du ändrar grupp inställningarna för en etikett när etiketten har tillämpats på grupper. När du gör ändringar i grupp inställningarna som är associerade med publicerade etiketter i [Microsoft 365 Compliance Center](https://sip.protection.office.com/homepage), tillämpas dessa princip ändringar inte automatiskt på de berörda grupperna.

Om du måste göra en ändring använder du ett [Azure AD PowerShell-skript](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1) för att manuellt tillämpa uppdateringar på de berörda grupperna. Den här metoden säkerställer att alla befintliga grupper tillämpar den nya inställningen.

## <a name="next-steps"></a>Nästa steg

- [Använd känslighets etiketter med Microsoft Teams, Microsoft 365 grupper och SharePoint-webbplatser](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [Uppdatera grupper efter att etikett policyn ändrats manuellt med Azure AD PowerShell-skriptet](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [Redigera dina gruppinställningar](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Hantera grupper med PowerShell-kommandon](../enterprise-users/groups-settings-v2-cmdlets.md)
