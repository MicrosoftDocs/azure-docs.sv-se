---
title: Skapa eller ändra en direkt peering med hjälp av PowerShell
titleSuffix: Azure
description: Skapa eller ändra en direkt peering med hjälp av PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: be4bab10d369fcefe626bbe890acfff7fc7004b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079072"
---
# <a name="create-or-modify-a-direct-peering-by-using-powershell"></a>Skapa eller ändra en direkt peering med hjälp av PowerShell

Den här artikeln beskriver hur du skapar en Microsoft Direct-peering med hjälp av PowerShell-cmdletar och Azure Resource Manager distributions modell. Den här artikeln visar också hur du kontrollerar status för resursen, uppdaterar den eller tar bort och avetablerar den.

Om du vill kan du slutföra den här guiden med hjälp av Azure [Portal](howto-direct-portal.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [kraven](prerequisites.md) och [genom gången direkt peering](walkthrough-direct-all.md) innan du påbörjar konfigurationen.
* Om du redan har direkt peering-anslutningar med Microsoft som inte har konverterats till Azure-resurser kan du läsa mer i [konvertera en äldre direkt peering till en Azure-resurs med hjälp av PowerShell](howto-legacy-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Arbeta med Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Skapa och etablera en direkt peering

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Logga in på ditt Azure-konto och välj din prenumeration
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-direct-peering"></a><a name=direct-location></a>Hämta listan över peering-platser som stöds för direkt peering
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Skapa en direkt peering
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verifiera direkt peering
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Ändra en direkt peering
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Avetablera en direkt peering
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra Exchange-peering med hjälp av PowerShell](howto-exchange-powershell.md)
* [Konvertera en äldre Exchange-peering till en Azure-resurs med hjälp av PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Ytterligare resurser
Du kan få detaljerade beskrivningar av alla parametrar genom att köra följande kommando:

```powershell
Get-Help Get-AzPeering -detailed
```

Mer information finns i [vanliga frågor och svar om Internet-peering](faqs.md).
