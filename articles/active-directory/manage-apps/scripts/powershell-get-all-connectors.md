---
title: PowerShell-exempel – Lista alla Programproxy anslutningsgrupper
description: PowerShell-exempel som visar alla Azure Active Directory (Azure AD) Programproxy anslutningsgrupper och anslutningsappar i din katalog.
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 032d4930e29126ec9636e710f016d9612dc2cda0
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377589"
---
# <a name="get-all-connector-groups-and-connectors-in-the-directory"></a>Hämta alla anslutningsgrupper och anslutningsappar i katalogen

Det här PowerShell-skriptexempel visar en lista Azure Active Directory (Azure AD) Programproxy anslutningsgrupper och anslutningsappar i din katalog.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Det här exemplet kräver [AzureAD V2 PowerShell för Graph-modulen](/powershell/azure/active-directory/install-adv2) (AzureAD) eller förhandsversionen av [AzureAD V2 PowerShell för Graph-modulen](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Exempelskript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-connectors.ps1 "Get all connector groups and connectors in the directory")]

## <a name="script-explanation"></a>Förklaring av skript

| Kommando | Kommentarer |
|---|---|
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup) | Hämtar en lista över alla anslutningsgrupper, eller om detta anges, information om den angivna anslutningsgruppen. |
| [Get-AzureADApplicationProxyConnectorGroupMembers](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroupmembers) | Hämtar alla Programproxy som är associerade med varje anslutningsgrupp.|

## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD PowerShell-modulen finns i Översikt över [Azure AD PowerShell-modulen](/powershell/azure/active-directory/overview).

Andra PowerShell-exempel för Programproxy finns i [Azure AD PowerShell-exempel för Azure AD Programproxy](../application-proxy-powershell-samples.md).
