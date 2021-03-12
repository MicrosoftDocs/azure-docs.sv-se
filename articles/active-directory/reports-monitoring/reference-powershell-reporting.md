---
title: Azure AD PowerShell-cmdletar för rapportering | Microsoft Docs
description: Referens för Azure AD PowerShell-cmdletar för rapportering.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/07/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 329036f3ed815eaaba94f441e372f4be86edd629
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558091"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>Azure AD PowerShell-cmdletar för rapportering

> [!NOTE] 
> Dessa PowerShell-cmdletar fungerar för närvarande bara med [Azure AD Preview](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#directory_auditing) -modulen. Observera att för hands versions modulen inte föreslås för produktions användning. 

Använd följande om du vill installera den offentliga för hands versionen. 

```powershell
Install-module AzureADPreview
```

Mer information om hur du ansluter till Azure AD med hjälp av PowerShell finns i artikeln [Azure AD PowerShell för Graph](/powershell/azure/active-directory/install-adv2).  

Med Azure Active Directory (Azure AD)-rapporter kan du få information om aktiviteter kring alla Skriv åtgärder i din riktning (gransknings loggar) och autentiserings data (inloggnings loggar). Även om informationen är tillgänglig med hjälp av MS-Graph API kan du hämta samma data med hjälp av Azure AD PowerShell-cmdlets för rapportering.

Den här artikeln innehåller en översikt över de PowerShell-cmdletar som används för gransknings loggar och inloggnings loggar.

## <a name="audit-logs"></a>Granskningsloggar

[Gransknings loggar](concept-audit-logs.md) ger spårning via loggar för alla ändringar som gjorts av olika funktioner i Azure AD. Exempel på gransknings loggar är ändringar som har gjorts i alla resurser i Azure AD, till exempel att lägga till eller ta bort användare, appar, grupper, roller och principer.

Du får åtkomst till gransknings loggarna med hjälp av cmdleten Get-AzureADAuditDirectoryLogs.


| Scenario                      | PowerShell-kommando |
| :--                           | :--                |
| Programmets visnings namn      | Get-AzureADAuditDirectoryLogs-filter "initiatedBy/app/displayName EQ" Azure AD Cloud Sync "" |
| Kategori                      | Get-AzureADAuditDirectoryLogs-filter "Category EQ" ApplicationManagement " |
| Datum/tid för aktivitet            | Get-AzureADAuditDirectoryLogs-filter "activityDateTime gt 2019-04-18" |
| Alla alternativ ovan              | Get-AzureADAuditDirectoryLogs-filter "initiatedBy/app/displayName EQ" Azure AD Cloud Sync "och Category EQ" ApplicationManagement "och activityDateTime gt 2019-04-18"|


Följande bild visar ett exempel på det här kommandot. 

![Skärm bild som visar resultatet av kommandot Get-Azure en D-granska katalog loggar.](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>Inloggningsloggar

[Inloggnings](concept-sign-ins.md) loggarna ger information om användningen av hanterade program och användar inloggnings aktiviteter.

Du får åtkomst till inloggnings loggarna med hjälp av cmdleten Get-AzureADAuditSignInLogs.


| Scenario                      | PowerShell-kommando |
| :--                           | :--                |
| Användarens visnings namn             | Get-AzureADAuditSignInLogs-filter "userDisplayName EQ Timothy Perkins" " |
| Skapa datum och tid              | Get-AzureADAuditSignInLogs-filter "createdDateTime gt 2019-04-18T17:30:00.0 Z" (allt sedan 5:30 PM på 4/18) |
| Status                        | Get-AzureADAuditSignInLogs-filter "status/errorCode EQ 50105" |
| Programmets visnings namn      | Get-AzureADAuditSignInLogs-filter "appDisplayName EQ" StoreFrontStudio [wsfed Enabled] " |
| Alla alternativ ovan              | Get-AzureADAuditSignInLogs-filter "userDisplayName EQ" Timothy Perkins "och status/errorCode Ne 0 och appDisplayName EQ StoreFrontStudio [wsfed Enabled]" |


Följande bild visar ett exempel på det här kommandot. 

![Skärm bild som visar resultatet av kommandot Get-Azure en D gransknings inloggnings loggar.](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure AD-rapporter](overview-reports.md).
- [Rapporten gransknings loggar](concept-audit-logs.md). 
- [Programmerings åtkomst till Azure AD-rapporter](concept-reporting-api.md)
