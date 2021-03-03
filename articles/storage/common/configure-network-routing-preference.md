---
title: Konfigurera inställningar för nätverksroutning
titleSuffix: Azure Storage
description: Konfigurera inställningar för nätverks routning för ditt Azure Storage-konto för att ange hur nätverks trafik dirigeras till ditt konto från klienter via Internet.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 02/21/2020
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: fb427de170764e5cd1fca57f9fb2d1410829e521
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700908"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Konfigurera inställningar för nätverks routning för Azure Storage

Den här artikeln beskriver hur du kan konfigurera inställningar för nätverks Routning och dirigerade slut punkter för ditt lagrings konto. 

Inställningen nätverks routning anger hur nätverks trafik dirigeras till ditt konto från klienter via Internet. Route-/regionsspecifika slut punkter är nya slut punkter som Azure Storage skapar för ditt lagrings konto. De här slut punkterna dirigerar trafik över en önskad sökväg utan att ändra standard inställningarna för routning. Mer information finns i [Inställningar för nätverks routning för Azure Storage](network-routing-preference.md).

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>Konfigurera Dirigerings inställningarna för den offentliga standard slut punkten

Som standard är cirkulations inställningen för lagrings kontots offentliga slut punkt inställd på Microsoft globalt nätverk. Du kan välja mellan Microsofts globala nätverk och Internet routning som standardinställning för routning för den offentliga slut punkten för ditt lagrings konto. Mer information om skillnaden mellan dessa två typer av routning finns i [Inställningar för nätverks routning för Azure Storage](network-routing-preference.md). 

Så här ändrar du inställningarna för routning till Internet Routning:

1. Navigera till ditt lagrings konto i portalen.

2. Välj **nätverk** under **Inställningar**.

    > [!div class="mx-imgBorder"]
    > ![Meny alternativ för nätverk](./media/configure-network-routing-preference/networking-option.png)

3.  I fliken **brand väggar och virtuella nätverk** , under **nätverks routning**, ändrar du inställningarna för **routning** till **Internet routning**.

4.  Klicka på **Spara**.

    > [!div class="mx-imgBorder"]
    > ![alternativ för Internet routning](./media/configure-network-routing-preference/internet-routing-option.png)

## <a name="configure-a-route-specific-endpoint"></a>Konfigurera en cirkulations bara slut punkt

Du kan också konfigurera en cirkulations bara slut punkt. Du kan till exempel ange cirkulations inställningar för standard slut punkten till *Internet routning* och sedan publicera en molnbaserad slut punkt som aktiverar trafik mellan klienter på Internet och ditt lagrings konto som ska dirigeras via Microsofts globala nätverk.

1.  Navigera till ditt lagrings konto i portalen.

2.  Välj **nätverk** under **Inställningar**.

3.  I fliken **brand väggar och virtuella nätverk** under **publicera dirigerade slut punkter**, väljer du cirkulations inställningen för den dirigerade slut punkten och klickar sedan på **Spara**. Den här inställningen påverkar bara den platsspecifika slut punkten. Inställningen påverkar inte standardvärdet för routning.  

    Följande bild visar alternativet **Microsoft Network routing** .

    > [!div class="mx-imgBorder"]
    > ![Alternativ för Microsoft-nätverks routning](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>Hitta slut punkts namnet för en cirkulations bara slut punkt

Om du har konfigurerat en molnbaserad slut punkt kan du hitta slut punkten i egenskaperna för ditt lagrings konto.

1.  Under **Inställningar** väljer du **Egenskaper**.

    > [!div class="mx-imgBorder"]
    > ![meny alternativet egenskaper](./media/configure-network-routing-preference/properties.png)

2.  Slut punkten för **Microsoft Network routing** visas för varje tjänst som stöder inställningar för routning. Den här bilden visar slut punkten för blob-och fil tjänsterna.

    > [!div class="mx-imgBorder"]
    > ![Alternativ för Microsoft-nätverks dirigering för dirigerade slut punkter](./media/configure-network-routing-preference/routing-url.png)


## <a name="see-also"></a>Se även

- [Inställningar för nätverks routning](network-routing-preference.md)
- [Konfigurera brandväggar och virtuella nätverk i Azure Storage](storage-network-security.md)
- [Säkerhets rekommendationer för Blob Storage](../blobs/security-recommendations.md)
