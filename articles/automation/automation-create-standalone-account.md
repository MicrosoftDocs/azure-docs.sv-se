---
title: Skapa ett fristående Azure Automation-konto
description: Den här artikeln beskriver hur du skapar ett fristående Azure Automation-konto och ett klassiskt kör som-konto.
services: automation
ms.subservice: process-automation
ms.date: 01/07/2021
ms.topic: conceptual
ms.openlocfilehash: e0088fb129e9c6558de7539ba754a45e067dc3d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99576845"
---
# <a name="create-a-standalone-azure-automation-account"></a>Skapa ett fristående Azure Automation-konto

Den här artikeln visar hur du skapar ett Azure Automation konto i Azure Portal. Du kan använda Portal Automation-kontot för att utvärdera och lära dig om automatisering utan att använda ytterligare hanterings funktioner eller integrera med Azure Monitor loggar. Du kan lägga till hanterings funktioner eller integrera med Azure Monitor loggar för avancerad övervakning av Runbook-jobb när som helst i framtiden.

Med ett Automation-konto kan du autentisera runbooks genom att hantera resurser i antingen Azure Resource Manager eller den klassiska distributions modellen. Ett Automation-konto kan hantera resurser i alla regioner och prenumerationer för en viss klientorganisation.

När du skapar ett Automation-konto i Azure Portal skapas **Kör som** -kontot automatiskt. Detta konto utför följande aktiviteter:

* Skapar ett huvud namn för tjänsten i Azure Active Directory (Azure AD).
* Skapar ett certifikat.
* Tilldelar rollen deltagare, som hanterar Azure Resource Manager-resurser med hjälp av Runbooks.

Med det här kontot skapat kan du snabbt börja skapa och distribuera Runbooks som stöder dina automatiserings behov.

## <a name="permissions-required-to-create-an-automation-account"></a>Behörigheter som krävs för att skapa ett Automation-konto

Om du vill skapa eller uppdatera ett Automation-konto och utföra de uppgifter som beskrivs i den här artikeln måste du ha följande behörigheter och behörigheter:

* För att skapa ett Automation-konto måste ditt Azure AD-användarkonto läggas till i en roll med behörigheter som motsvarar ägar rollen för `Microsoft.Automation` resurser. Mer information finns i [rollbaserad Access Control i Azure Automation](automation-role-based-access-control.md).
* I Azure Portal, under **Azure Active Directory**  >  **Hantera**  >  **användar inställningar**, om **Appregistreringar** har angetts till **Ja**, kan användare som inte är administratörer i Azure AD-klienten [Registrera Active Directory program](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions). Om **Appregistreringar** är inställt på **Nej**, måste den användare som utför den här åtgärden ha minst en programutvecklare-roll i Azure AD.

Om du inte är medlem i prenumerationens Active Directory instans innan du lägger till den globala administratören/rollen som administratör i prenumerationen, läggs du till Active Directory som gäst. I det här scenariot ser du det här meddelandet i fönstret Lägg till Automation-konto: `You do not have permissions to create.`

Om du först lägger till en användare i rollen global administratör/superadministratör kan du ta bort användaren från prenumerationens Active Directory instans. Du kan läsa användaren till användar rollen i Active Directory. Så här verifierar du användar roller:

1. I Azure Portal går du till fönstret Azure Active Directory.
1. Välj **Användare och grupper**.
1. Välj **alla användare**.
1. När du har valt en speciell användare väljer du **profil**. Värdet för attributet **användar typ** under användarens profil ska inte vara **gäst**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Skapa ett nytt Automation-konto i Azure Portal

Utför följande steg för att skapa ett Azure Automation konto i Azure Portal:

1. Logga in på Azure Portal med ett konto som är medlem i rollen prenumerations administratörer och en administratör för prenumerationen.
1. Välj **+ skapa en resurs**.
1. Sök efter **Automation**. I Sök resultaten väljer du **Automation**.

   ![Sök efter och välj Automation & Control på Azure Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. På nästa skärm väljer du **Skapa nytt**.

   ![Lägg till Automation-konto](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > Om du ser följande meddelande i fönstret Lägg till Automation-konto är ditt konto inte medlem i rollen prenumerations administratörer och en administratör för prenumerationen.
   >
   > :::image type="content" source="media/automation-create-standalone-account/create-account-without-perms.png" alt-text="Skärm bild av prompten du har inte behörighet att skapa ett Kör som-konto i Azure Active Directory.":::

1. I fönstret Lägg till Automation-konto anger du ett namn för det nya Automation-kontot i fältet **namn** . Du kan inte ändra det här namnet efter att det har valts. 

    > [!NOTE]
    > Namn på Automation-konton är unika per region och resurs grupp. Namn för borttagna Automation-konton kanske inte är omedelbart tillgängliga.

1. Om du har mer än en prenumeration använder du fältet **prenumeration** för att ange vilken prenumeration som ska användas för det nya kontot.
1. För **resurs grupp** anger eller väljer du en ny eller befintlig resurs grupp.
1. För **plats** väljer du en plats för Azure-datacenter.
1. För alternativet **Skapa Azure kör som-konto** kontrollerar du att **Ja** är markerat och klickar sedan på **skapa**.

   > [!NOTE]
   > Om du väljer att inte skapa kör som-kontot genom att välja **Nej** för **skapa kör som-konto för Azure** visas ett meddelande i fönstret Lägg till Automation-konto. Även om kontot har skapats i Azure Portal, har kontot inte någon motsvarande Autentiseringsidentitet i den klassiska distributions modell prenumerationen eller i Azure Resource Manager prenumerations katalog tjänst. Automation-kontot har därför inte åtkomst till resurser i din prenumeration. Detta förhindrar att Runbooks som refererar till det här kontot kan autentisera och utföra åtgärder mot resurser i dessa distributions modeller.
   >
   > :::image type="content" source="media/automation-create-standalone-account/create-account-decline-create-runas-msg.png" alt-text="Skärm bild av prompt med meddelande &quot;du har valt att inte skapa ett Kör som-konto.&quot;":::
   >
   > När tjänstens huvud namn inte har skapats är deltagar rollen inte tilldelad.
   >

1. Om du vill följa förloppet för att skapa Automation-kontot väljer du **meddelanden** på menyn.

När Automation-kontot har skapats skapas flera resurser automatiskt. När du har skapat dessa Runbooks kan de tas bort på ett säkert sätt om du inte vill behålla dem. Kör som-kontona kan användas för att autentisera till ditt konto i en Runbook och bör vara kvar om du inte skapar ett annat eller inte behöver det. I följande tabell sammanfattas resurserna för Kör som-kontot.

| Resurs | Beskrivning |
| --- | --- |
| AzureAutomationTutorial-runbook |Ett exempel på en grafisk Runbook som visar hur du autentiserar med hjälp av kör som-kontot. Runbook hämtar alla Resource Manager-resurser. |
| AzureAutomationTutorialScript-runbook |Ett exempel på en PowerShell-Runbook som visar hur du autentiserar med hjälp av kör som-kontot. Runbook hämtar alla Resource Manager-resurser. |
| AzureAutomationTutorialPython2 Runbook |Ett exempel på en python-Runbook som visar hur du autentiserar med hjälp av kör som-kontot. Runbooken visar en lista över alla resurs grupper som finns i prenumerationen. |
| AzureRunAsCertificate |En certifikat till gång som skapas automatiskt när Automation-kontot skapas, eller genom att använda ett PowerShell-skript för ett befintligt konto. Certifikatet autentiseras med Azure så att du kan hantera Azure Resource Manager resurser från Runbooks. Det här certifikatet har en livslängd på ett år. |
| AzureRunAsConnection |En anslutnings till gång som skapas automatiskt när Automation-kontot skapas, eller genom att använda ett PowerShell-skript för ett befintligt konto. |

## <a name="create-a-classic-run-as-account"></a>Skapa ett klassiskt kör som-konto

Klassiska kör som-konton skapas inte som standard när du skapar ett Azure Automation-konto. Utför följande steg om du behöver ett klassiskt kör som-konto för att hantera klassiska Azure-resurser:

1. Från ditt Automation-konto väljer du **Kör som-konton** under **konto inställningar**.
2. Välj **det klassiska kör som-kontot i Azure**.
3. Klicka på **skapa** för att fortsätta med att skapa det klassiska kör som-kontot.

## <a name="next-steps"></a>Nästa steg

* Mer information om grafisk redigering finns [i Redigera grafiska runbooks i Azure Automation](automation-graphical-authoring-intro.md).
* Information om hur du kommer igång med PowerShell-Runbooks finns i [Självstudier: skapa en PowerShell-Runbook](learn/automation-tutorial-runbook-textual-powershell.md).
* Information om hur du kommer igång med PowerShell Workflow-Runbooks finns i [självstudie: skapa en PowerShell Workflow-Runbook](learn/automation-tutorial-runbook-textual.md).
* Information om hur du kommer igång med python 3-Runbooks finns i [Självstudier: skapa en python 3-Runbook](learn/automation-tutorial-runbook-textual-python-3.md).
* En PowerShell-cmdlet-referens finns i [AZ. Automation](/powershell/module/az.automation).
