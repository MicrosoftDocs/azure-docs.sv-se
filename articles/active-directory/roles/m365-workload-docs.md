---
title: Administratörs roll dokument i Microsoft 365 Services – Azure AD | Microsoft Docs
description: Hitta innehålls-och API-referenser för administratörs roller för Microsoft 365 Services i Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f56f8ac42f0db3d2cd27453cd023a2e869b0cde0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103466090"
---
# <a name="roles-for-microsoft-365-services-in-azure-active-directory"></a>Roller för Microsoft 365 tjänster i Azure Active Directory

Alla produkter i Microsoft 365 kan hanteras med administrativa roller i Azure Active Directory (Azure AD). Vissa produkter ger också ytterligare roller som är speciella för produkten. Information om vilka roller som stöds av varje produkt finns i tabellen nedan. Rikt linjer för planering av roll säkerhet finns i [skydda privilegie rad åtkomst för Hybrid-och moln distributioner i Azure AD](security-planning.md).

## <a name="where-to-find-content"></a>Var du hittar innehåll

Microsoft 365 tjänst | Roll innehåll | API-innehåll
---------------------- | ------------------ | -----------------
Administratörs roller i Office 365 och Microsoft 365 affärs planer | [Microsoft 365 administratörs roller](/office365/admin/add-users/about-admin-roles) | Inte tillgängligt
Azure Active Directory (Azure AD) och Azure AD Identity Protection| [Administratörs roller för Azure AD](permissions-reference.md) | [Graph API](/graph/api/overview)<br>[Hämta roll tilldelningar](/graph/api/directoryrole-list)
Exchange Online| [Rollbaserad åtkomst kontroll i Exchange](/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell för Exchange](/powershell/module/exchange/role-based-access-control/add-managementroleentry)<br>[Hämta roll tilldelningar](/powershell/module/exchange/role-based-access-control/get-rolegroup)
sharepoint online | [Administratörs roller för Azure AD](permissions-reference.md)<br>Även [om administrations rollen för SharePoint i Microsoft 365](/sharepoint/sharepoint-admin-role) | [Graph API](/graph/api/overview)<br>[Hämta roll tilldelningar](/graph/api/directoryrole-list)
Team/Skype för företag | [Administratörs roller för Azure AD](permissions-reference.md) | [Graph API](/graph/api/overview)<br>[Hämta roll tilldelningar](/graph/api/directoryrole-list)
Säkerhets & Compliance Center (Office 365 Avancerat skydd, Exchange Online Protection, Information Protection) | [Office 365-administratörsroller](/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](/powershell/module/exchange/role-based-access-control/add-managementroleentry)<br>[Hämta roll tilldelningar](/powershell/module/exchange/role-based-access-control/get-rolegroup)
Säkra Poäng | [Administratörs roller för Azure AD](permissions-reference.md) | [Graph API](/graph/api/overview)<br>[Hämta roll tilldelningar](/graph/api/directoryrole-list)
Compliance Manager (Efterlevnadshanteraren) | [Compliance Manager-roller](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Inte tillgängligt
Azure Information Protection | [Administratörs roller för Azure AD](permissions-reference.md) | [Graph API](/graph/api/overview)<br>[Hämta roll tilldelningar](/graph/api/directoryrole-list)
Microsoft Cloud App Security | [Rollbaserad åtkomst kontroll](/cloud-app-security/manage-admins) | [API-referens](/cloud-app-security/api-tokens) 
Azure Advanced Threat Protection | [Azure ATP-rollgrupper](/azure-advanced-threat-protection/atp-role-groups) | Inte tillgängligt
Windows Defender Advanced Threat Protection | [Windows Defender ATP – rollbaserad åtkomst kontroll](/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Inte tillgängligt
Privileged Identity Management | [Administratörs roller för Azure AD](permissions-reference.md) | [Graph API](/graph/api/overview)<br>[Hämta roll tilldelningar](/graph/api/directoryrole-list)
Intune | [Intune-rollbaserad åtkomst kontroll](/intune/role-based-access-control) | [Graph API](/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta&preserve-view=true)<br>[Hämta roll tilldelningar](/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta&preserve-view=true)
Hanterat skriv bord | [Administratörs roller för Azure AD](permissions-reference.md) | [Graph API](/graph/api/overview)<br>[Hämta roll tilldelningar](/graph/api/directoryrole-list)

## <a name="next-steps"></a>Nästa steg

* [Tilldela eller ta bort administratörs roller för Azure AD](manage-roles-portal.md)
* [Roll referens för Azure AD-administratörer](permissions-reference.md)