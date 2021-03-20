---
title: Visa tjänstens huvud namn för en hanterad identitet med PowerShell – Azure AD
description: Stegvisa instruktioner för att Visa tjänstens huvud namn för en hanterad identitet med hjälp av PowerShell.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 15ccc0faa4d74a2ef95aca00a6257f27b9a209c3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91611953"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Visa tjänstens huvud namn för en hanterad identitet med hjälp av PowerShell

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du visar tjänstens huvud namn för en hanterad identitet med hjälp av PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [avsnittet Översikt](overview.md).
- Om du inte redan har ett Azure-konto kan du [Registrera dig för ett kostnads fritt konto](https://azure.microsoft.com/free/).
- Aktivera [systemtilldelad identitet på en virtuell dator eller ett](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) [program](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity).
- Om du vill köra exempel skripten har du två alternativ:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md)som du kan öppna med knappen **prova** på det övre högra hörnet av kodblock.
    - Kör skript lokalt genom att installera den senaste versionen av [Azure PowerShell](/powershell/azure/install-az-ps)och logga sedan in på Azure med hjälp av `Connect-AzAccount` .

## <a name="view-the-service-principal"></a>Visa tjänstens huvud namn

Följande kommando visar hur du visar tjänstens huvud namn för en virtuell dator eller ett program med systemtilldelad identitet aktive rad. Ersätt `<Azure resource name>` med dina egna värden.

```azurepowershell-interactive
Get-AzADServicePrincipal -DisplayName <Azure resource name>
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du visar Azure AD-tjänstens huvud namn med hjälp av PowerShell finns i [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).
