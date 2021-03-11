---
title: PowerShell-exempel för Azure Active Directory program hantering
description: Dessa PowerShell-exempel används för appar som du hanterar i Azure Active Directory-klienten. Du kan använda dessa exempel skript för att hitta förfallo information om hemligheter och certifikat.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 02/18/2021
ms.author: kenwith
ms.reviewer: mifarca
ms.openlocfilehash: 46297f7f0f648c8bebc887a9093e25dfea99f695
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561508"
---
# <a name="azure-active-directory-powershell-examples-for-application-management"></a>Azure Active Directory PowerShell-exempel för program hantering

Följande tabell innehåller länkar till exempel på PowerShell-skript för hantering av Azure AD-program. Dessa exempel kräver antingen:
- [AzureAD v2 PowerShell för Graph-modul](/powershell/azure/active-directory/install-adv2) eller,
- [AzureAD v2 PowerShell för för hands versionen av Graph module](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true), om inget annat anges.

Mer information om de cmdletar som används i de här exemplen finns i [program](/powershell/module/azuread/#applications).

| Länk | Beskrivning |
|---|---|
|**Skript för program hantering**||
| [Exportera hemligheter och certifikat (app-registreringar)](scripts/powershell-export-all-app-registrations-secrets-and-certs.md) | Exportera hemligheter och certifikat för app-registreringar i Azure Active Directory klient organisation. |
| [Exportera hemligheter och certifikat (företags program)](scripts/powershell-export-all-enterprise-apps-secrets-and-certs.md) | Exportera hemligheter och certifikat för företags program i Azure Active Directory klient organisationen. |
| [Exportera utgångna hemligheter och certifikat](scripts/powershell-export-apps-with-expriring-secrets.md) | Exportera appar med förfallna hemligheter och certifikat i Azure Active Directory klient organisationen. |
| [Exportera hemligheter och certifikat som upphör att gälla efter det datum som krävs](scripts/powershell-export-apps-with-secrets-beyond-required.md) | Exportera appar med hemligheter och certifikat som upphör att gälla efter det datum som krävs i Azure Active Directory klient organisationen. |
