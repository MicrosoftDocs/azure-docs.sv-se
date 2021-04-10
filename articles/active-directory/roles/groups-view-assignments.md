---
title: Visa roller som har tilldelats till en grupp i Azure Active Directory | Microsoft Docs
description: Lär dig hur rollerna som har tilldelats en grupp kan visas med hjälp av Azure AD administrations Center. Att Visa grupper och tilldelade roller är standard användar behörigheter.
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
ms.openlocfilehash: 7dac23897f8a17c9adb4ae78736a6a8afa85a18b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012011"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Visa roller som har tilldelats till en grupp i Azure Active Directory

I det här avsnittet beskrivs hur de roller som har tilldelats en grupp kan visas med hjälp av Azure AD administrations Center. Att Visa grupper och tilldelade roller är standard användar behörigheter.

1. Logga in på [administrations centret för Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) med eventuella autentiseringsuppgifter som inte är administratör eller administratör.

1. Välj den grupp som du är intresse rad av.

1. Välj **tilldelade roller**. Nu kan du se alla Azure AD-roller som har tilldelats den här gruppen.

   ![Visa alla roller som har tilldelats en vald grupp](./media/groups-view-assignments/view-assignments.png)

## <a name="using-powershell"></a>Använda PowerShell

### <a name="get-object-id-of-the-group"></a>Hämta objekt-ID för gruppen

```powershell
Get-AzureADMSGroup -SearchString "Contoso_Helpdesk_Administrators"
```

### <a name="view-role-assignment-to-a-group"></a>Visa roll tilldelning till en grupp

```powershell
Get-AzureADMSRoleAssignment -Filter "principalId eq '<object id of group>" 
```

## <a name="using-microsoft-graph-api"></a>Använda Microsoft Graph API

### <a name="get-object-id-of-the-group"></a>Hämta objekt-ID för gruppen

```http
GET https://graph.microsoft.com/beta/groups?$filter=displayName+eq+'Contoso_Helpdesk_Administrator'
```

### <a name="get-role-assignments-to-a-group"></a>Hämta roll tilldelningar till en grupp

```http
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq
```

## <a name="next-steps"></a>Nästa steg

- [Använda molngrupper för att hantera rolltilldelningar](groups-concept.md)
- [Felsökningsroller tilldelade till molngrupper](groups-faq-troubleshooting.md)
