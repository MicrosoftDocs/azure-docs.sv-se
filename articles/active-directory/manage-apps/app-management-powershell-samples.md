---
title: PowerShell-exempel Azure Active Directory programhantering
description: Dessa PowerShell-exempel används för appar som du hanterar i din Azure Active Directory klientorganisation. Du kan använda dessa exempelskript för att hitta förfalloinformation om hemligheter och certifikat.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 02/18/2021
ms.author: iangithinji
ms.reviewer: mifarca
ms.openlocfilehash: 6b6314935bfafc2fe6288c30619e1d01242a991d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378830"
---
# <a name="azure-active-directory-powershell-examples-for-application-management"></a>Azure Active Directory PowerShell-exempel för programhantering

Följande tabell innehåller länkar till PowerShell-skriptexempel för Azure AD-programhantering. Dessa exempel kräver antingen:
- [AzureAD V2 PowerShell för Graph-modulen](/powershell/azure/active-directory/install-adv2) eller,
- Förhandsversionen [av AzureAD V2 PowerShell för Graph-modulen,](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true)om inget annat anges.

Mer information om de cmdlets som används i dessa exempel finns i [Program](/powershell/module/azuread/#applications).

| Länk | Beskrivning |
|---|---|
|**Programhanteringsskript**||
| [Exportera hemligheter och certifikat (appregistreringar)](scripts/powershell-export-all-app-registrations-secrets-and-certs.md) | Exportera hemligheter och certifikat för appregistreringar i Azure Active Directory klientorganisation. |
| [Exportera hemligheter och certifikat (företagsappar)](scripts/powershell-export-all-enterprise-apps-secrets-and-certs.md) | Exportera hemligheter och certifikat för företagsappar i Azure Active Directory klientorganisation. |
| [Exportera utgångna hemligheter och certifikat](scripts/powershell-export-apps-with-expriring-secrets.md) | Exportera appregistreringar med utgångna hemligheter och certifikat och deras ägare i Azure Active Directory klientorganisation. |
| [Exportera hemligheter och certifikat som upphör att gälla efter det obligatoriska datumet](scripts/powershell-export-apps-with-secrets-beyond-required.md) | Exportera appregistreringar med hemligheter och certifikat som går ut efter det obligatoriska datumet Azure Active Directory klientorganisationen. Detta använder det icke-interaktiva Client_Credentials Oauth-flödet. |
