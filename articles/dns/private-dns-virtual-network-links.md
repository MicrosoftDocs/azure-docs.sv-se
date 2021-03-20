---
title: Vad är en virtuell nätverks länk under resurs för Azure DNS privata zoner
description: Översikt över virtuell nätverks länk under resurs en Azure DNS privat zon
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 5e43ad35cdaad89d5b9f43007bad8782f358c1a6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94954349"
---
# <a name="what-is-a-virtual-network-link"></a>Vad är en virtuell nätverks länk?

När du skapar en privat DNS-zon i Azure är den inte direkt tillgänglig från något virtuellt nätverk. Du måste länka den till ett virtuellt nätverk innan en virtuell dator som finns i nätverket kan komma åt den privata DNS-zonen.
Om du vill länka en privat DNS-zon med ett virtuellt nätverk måste du skapa en virtuell nätverks länk under den privata DNS-zonen. Varje privat DNS-zon har en samling med underordnade resurser för virtuella nätverks länkar. Var och en av dessa resurser representerar en anslutning till ett virtuellt nätverk.

Du kan länka ett virtuellt nätverk till en privat DNS-zon som ett virtuellt registrerings nätverk eller som ett virtuellt matchnings nätverk.

## <a name="registration-virtual-network"></a>Registrera virtuellt nätverk

När du [skapar en länk](./private-dns-getstarted-portal.md#link-the-virtual-network) mellan en privat DNS-zon och ett virtuellt nätverk har du ett alternativ för att aktivera [AUTOREGISTRERING](./private-dns-autoregistration.md) av DNS-poster för virtuella datorer. Om du väljer det här alternativet blir det virtuella nätverket ett virtuellt registrerings nätverk för den privata DNS-zonen. En DNS-post skapas automatiskt för de virtuella datorer som du distribuerar i nätverket. DNS-poster skapas för de virtuella datorer som du redan har distribuerat i det virtuella nätverket. Från det virtuella nätverkets perspektiv blir den privata DNS-zonen registrerings zonen för det virtuella nätverket.
En privat DNS-zon kan ha flera virtuella registrerings nätverk, men varje virtuellt nätverk kan ha exakt en registrerings zon kopplad till sig.

## <a name="resolution-virtual-network"></a>Virtuella matchnings nätverk

När du skapar en virtuell nätverks länk under en privat DNS-zon och väljer att inte aktivera registrering av DNS-poster, behandlas det virtuella nätverket som en upplösnings bara virtuellt nätverk. DNS-poster för virtuella datorer som distribueras i sådana nätverk skapas inte automatiskt i den länkade privata DNS-zonen. De virtuella datorer som distribueras i ett sådant nätverk kan dock skicka frågor till DNS-posterna från den privata DNS-zonen. Dessa poster kan skapas manuellt av dig eller så kan de fyllas i andra virtuella nätverk som har länkats till registrerings nätverk med den privata DNS-zonen.
En privat DNS-zon kan ha flera virtuella matchnings nätverk och ett virtuellt nätverk kan ha flera associerade matchnings zoner.

## <a name="limits"></a>Gränser

För att förstå hur många registrerings-och lösnings nätverk kan du länka till privata DNS-zoner se [Azure DNS gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits)

## <a name="other-considerations"></a>Ytterligare överväganden

* Virtuella nätverk som distribueras med den klassiska distributions modellen stöds inte.

* Du kan bara skapa en länk mellan en privat DNS-zon och ett virtuellt nätverk.

* Varje virtuell nätverks länk under en privat DNS-zon måste ha ett unikt namn inom kontexten för den privata DNS-zonen. Du kan ha länkar med samma namn i olika privata DNS-zoner.

* När du har skapat en virtuell nätverks länk kontrollerar du fältet länk status i resursen för den virtuella nätverks länken. Beroende på storleken på det virtuella nätverket kan det ta några minuter innan länken är igång och länkens status ändras till *slutförd*.

* När du tar bort ett virtuellt nätverk raderas alla virtuella nätverks länkar och automatiskt registrerade DNS-poster som är kopplade till den i olika privata DNS-zoner automatiskt.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du länkar ett virtuellt nätverk till en privat DNS-zon med hjälp av [Azure Portal](./private-dns-getstarted-portal.md#link-the-virtual-network)

* Lär dig hur du skapar en privat zon i Azure DNS med [Azure PowerShell](./private-dns-getstarted-powershell.md) eller [Azure CLI](./private-dns-getstarted-cli.md).

* Läs om några vanliga [scenarier för privata](./private-dns-scenarios.md) zoner som kan realiseras med privata zoner i Azure DNS.

* Vanliga frågor och svar om privata zoner i Azure DNS, inklusive specifika funktioner som kan förväntas för vissa typer av åtgärder, finns i [privat DNS vanliga frågor](./dns-faq-private.md)och svar.