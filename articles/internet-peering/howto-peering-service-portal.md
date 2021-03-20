---
title: Aktivera Azure peering-tjänsten på en direkt peering med hjälp av Azure Portal
titleSuffix: Azure
description: Aktivera Azure peering-tjänsten på en direkt peering med hjälp av Azure Portal
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: a52e6038b622c004dc0d133394cd4f53600b2935
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84700050"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-the-azure-portal"></a>Aktivera Azure peering-tjänsten på en direkt peering med hjälp av Azure Portal

I den här artikeln beskrivs hur du aktiverar Azure [peering-tjänsten](overview-peering-service.md) på en direkt peering med hjälp av Azure Portal.

Om du vill kan du slutföra den här guiden med hjälp av [PowerShell](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [kraven](prerequisites.md) innan du påbörjar konfigurationen.
* Välj en direkt peering i din prenumeration som du vill aktivera peering-tjänsten för. Om du inte har någon kan du antingen konvertera en äldre direkt peering eller skapa en ny direkt peering:
    * Om du vill konvertera en äldre direkt peering följer du anvisningarna i [konvertera en äldre direkt peering till en Azure-resurs med hjälp av portalen](howto-legacy-direct-portal.md).
    * Om du vill skapa en ny direkt peering följer du anvisningarna i [skapa eller ändra en direkt peering med hjälp av portalen](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Aktivera Peering Service på en Direct-peering

### <a name="view-direct-peering"></a><a name= get></a>Visa direkt peering
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Aktivera direkt peering för peering-tjänsten

När du har öppnat en direkt peering i föregående steg aktiverar du den för peering-tjänsten.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Ändra en direkt peering-anslutning

Om du vill ändra anslutnings inställningarna läser du avsnittet "ändra en direkt peering" i [skapa eller ändra en direkt peering med hjälp av portalen](howto-direct-portal.md).

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra Exchange-peering med hjälp av portalen](howto-exchange-portal.md)
* [Konvertera en äldre Exchange-peering till en Azure-resurs med hjälp av portalen](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Ytterligare resurser

Vanliga frågor om [peering-tjänsten finns i vanliga frågor och svar om peering-tjänsten](service-faqs.md).