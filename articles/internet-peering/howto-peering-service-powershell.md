---
title: Aktivera Azure peering-tjänsten på en direkt peering med hjälp av PowerShell
titleSuffix: Azure
description: Aktivera Azure peering-tjänsten på en direkt peering med hjälp av PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 98341fbbbcafb6aee938870c22050c6edec352ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079055"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-powershell"></a>Aktivera Azure peering-tjänsten på en direkt peering med hjälp av PowerShell

Den här artikeln beskriver hur du aktiverar Azure [peering-tjänsten](overview-peering-service.md) på en direkt peering med hjälp av PowerShell-cmdletar och Azure Resource Manager distributions modell.

Om du vill kan du slutföra den här guiden med hjälp av Azure [Portal](howto-peering-service-portal.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [kraven](prerequisites.md) innan du påbörjar konfigurationen.
* Välj en direkt peering i din prenumeration som du vill aktivera peering-tjänsten för. Om du inte har någon kan du antingen konvertera en äldre direkt peering eller skapa en ny direkt peering:
    * Om du vill konvertera en äldre direkt peering följer du anvisningarna i [konvertera en äldre direkt peering till en Azure-resurs med hjälp av PowerShell](howto-legacy-direct-powershell.md).
    * Om du vill skapa en ny direkt peering följer du anvisningarna i [skapa eller ändra en direkt peering med hjälp av PowerShell](howto-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Arbeta med Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Aktivera Peering Service på en Direct-peering

### <a name="view-direct-peering"></a><a name= get></a>Visa direkt peering
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Aktivera direkt peering för peering-tjänsten

När du har direkt peering i föregående steg aktiverar du det för peering-tjänsten.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Ändra en direkt peering-anslutning

Om du behöver ändra anslutnings inställningarna läser du avsnittet "ändra en direkt peering" i [skapa eller ändra en direkt peering med hjälp av PowerShell](howto-direct-powershell.md).

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra Exchange-peering med hjälp av PowerShell](howto-exchange-powershell.md)
* [Konvertera en äldre Exchange-peering till en Azure-resurs med hjälp av PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Ytterligare resurser
Du kan få detaljerade beskrivningar av alla parametrar genom att köra följande kommando:

```powershell
Get-Help Get-AzPeering -detailed
```

Vanliga frågor om [peering-tjänsten finns i vanliga frågor och svar om peering-tjänsten](service-faqs.md).
