---
title: Cirkulations inställningar avlästa i Azure
description: Läs om hur du kan konfigurera cirkulations inställningar för dina resurser som utvärderar data till CDN-providern.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
Customer intent: As an Azure customer, I want to learn more about enabling routing preference for my CDN origin resources.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: 1fcc918e5b4b54a415fed0f38d210aeeaa62c008
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101680308"
---
# <a name="what-is-routing-preference-unmetered"></a>Vad är cirkulations inställningar avlästa?

Dirigerings inställningar som inte går att mäta är tillgängliga för CDN-leverantörer (Content Delivery Network) med kunder som är värdar för sitt ursprungs innehåll i Azure. Med tjänsten kan CDN-providers upprätta direkt peering-anslutning med Microsoft Global Network Edge-routrar på olika platser.

![Dirigerings inställningar avlästes](media/routing-preference-unmetered/unmetered.png)

Nätverks trafiken från ursprung i Azure till CDN-providerns fördelar från den direkta anslutningen.
* Data överförings notan för trafik som utgående från dina Azure-resurser som dirigeras via dessa direkta länkar är kostnads fri.
* Direkt anslutning mellan CDN-Provider och ursprung i Azure ger optimala prestanda eftersom det inte finns några hopp mellan. Detta fördelar den CDN-belastning som ofta hämtar data från ursprunget.

## <a name="configuring-routing-preference-unmetered"></a>Konfigurering av cirkulations inställningar avlästes

För att dra nytta av cirkulations inställningarna, måste CDN-providers vara en del av det här programmet. Om CDN-providern inte är en del av programmet kontaktar du CDN-providern.

Konfigurera sedan cirkulations inställningar för dina resurser och ange inställning för routning till **Internet**. Du kan konfigurera cirkulations inställningar när du skapar en offentlig IP-adress och sedan associerar den offentliga IP-adressen med resurser, till exempel virtuella datorer, belastningsutjämnare mot Internet och mycket annat. [Lär dig hur du konfigurerar cirkulations inställningar för en offentlig IP-adress med hjälp av Azure Portal](routing-preference-portal.md)

Du kan också aktivera inställningar för routning för ditt lagrings konto och publicera en andra slut punkt som måste användas av CDN-providern för att hämta data från lagrings ursprunget. Om du till exempel publicerar en *StorageAccountA* slut punkt för lagrings kontot kommer att publicera den andra slut punkten för dina lagrings tjänster så som visas nedan:

![Dirigerings inställningar för lagrings konton](media/routing-preference-unmetered/storage-endpoints.png)


## <a name="next-steps"></a>Nästa steg

* [Konfigurera Dirigerings inställningar för en virtuell dator med hjälp av Azure PowerShell](configure-routing-preference-virtual-machine-powershell.md)
* [Konfigurera Dirigerings inställningar för en virtuell dator med hjälp av Azure CLI](configure-routing-preference-virtual-machine-cli.md)
* [Konfigurera Dirigerings inställningar för ditt lagrings konto](/azure/storage/common/network-routing-preference)