---
title: Tilldela en roll till en grupp med hjälp av Privileged Identity Management i Azure AD | Microsoft Docs
description: Lär dig hur du kan tilldela en Azure Active Directory-roll (Azure AD) till en grupp med Azure AD Privileged Identity Management (PIM).
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02cd3f54823b80ae201316fee29c02616b9d8502
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012045"
---
# <a name="assign-a-role-to-a-group-using-privileged-identity-management"></a>Tilldela en roll till en grupp med hjälp av Privileged Identity Management

I den här artikeln beskrivs hur du kan tilldela en Azure Active Directory-roll (Azure AD) till en grupp med Azure AD Privileged Identity Management (PIM).

> [!NOTE]
> Du måste använda den uppdaterade versionen av Privileged Identity Management för att kunna tilldela en grupp till en Azure AD-roll med hjälp av PIM. Du kanske använder en äldre version av PIM om din Azure AD-organisation använder Privileged Identity Management API. I så fall kan du kontakta ditt alias pim_preview@microsoft.com för att flytta din organisation och uppdatera ditt API. Lär dig mer [om Azure AD-roller och-funktioner i PIM](../privileged-identity-management/azure-ad-roles-features.md).

## <a name="using-azure-ad-admin-center"></a>Använda Azure AD administrations Center

1. Logga in på [Azure AD Privileged Identity Management](https://ms.portal.azure.com/?Microsoft_AAD_IAM_GroupRoles=true&Microsoft_AAD_IAM_userRolesV2=true&Microsoft_AAD_IAM_enablePimIntegration=true#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) som privilegie rad roll administratör eller global administratör i din organisation.

1. Välj **Privileged Identity Management**  >  roller för **Azure AD-roller**  >    >  **Lägg till tilldelningar**

1. Välj en roll och välj sedan en grupp. Endast grupper som är berättigade till roll tilldelning (roll tilldelnings grupper) visas, inte alla grupper.

    ![Skärm bild som visar sidan Lägg till tilldelningar med avsnitten "Välj roll" och "Välj medlemmar" markerade.](./media/groups-pim-eligible/select-member.png)

1. Välj önskad medlemskaps inställning. För roller som kräver aktivering väljer du **berättigade**. Som standard är användaren permanent berättigad, men du kan också ange en start-och slut tid för användarens behörighet. När du är klar trycker du på Spara och Lägg till för att slutföra roll tilldelningen.

    ![Välj den användare som du tilldelar rollen](./media/groups-pim-eligible/set-assignment-settings.png)

## <a name="using-powershell"></a>Använda PowerShell

### <a name="download-the-azure-ad-preview-powershell-module"></a>Ladda ned Azure AD Preview PowerShell-modulen

Om du vill installera Azure AD #PowerShell-modulen använder du följande cmdlets:

```powershell
Install-Module -Name AzureADPreview
Import-Module -Name AzureADPreview
```

Använd följande cmdlet för att kontrol lera att modulen är redo att användas:

```powershell
Get-Module -Name AzureADPreview
```

### <a name="assign-a-group-as-an-eligible-member-of-a-role"></a>Tilldela en grupp som en berättigad medlem i en roll

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = "2019-04-26T20:49:11.770Z"
$schedule.endDateTime = "2019-07-25T20:49:11.770Z"
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId aadRoles -Schedule $schedule -ResourceId "[YOUR TENANT ID]" -RoleDefinitionId "9f8c1837-f885-4dfd-9a75-990f9222b21d" -SubjectId "[YOUR GROUP ID]" -AssignmentState "Eligible" -Type "AdminAdd"
```

## <a name="using-microsoft-graph-api"></a>Använda Microsoft Graph API

```http
POST
https://graph.microsoft.com/beta/privilegedAccess/aadroles/roleAssignmentRequests
{
 "roleDefinitionId": {roleDefinitionId},
 "resourceId": {tenantId},
 "subjectId": {GroupId},
 "assignmentState": "Eligible",
 "type": "AdminAdd",
 "reason": "reason string",
 "schedule": {
   "startDateTime": {DateTime},
   "endDateTime": {DateTime},
   "type": "Once"
 }
}
```

## <a name="next-steps"></a>Nästa steg

- [Använda molngrupper för att hantera rolltilldelningar](groups-concept.md)
- [Felsökningsroller tilldelade till molngrupper](groups-faq-troubleshooting.md)
- [Konfigurera inställningarna för Azure AD admin-rollen i Privileged Identity Management](../privileged-identity-management/pim-how-to-change-default-settings.md)
- [Tilldela Azure-resurs roller i Privileged Identity Management](../privileged-identity-management/pim-resource-roles-assign-roles.md)
