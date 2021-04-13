---
title: Vad är en Azure DNS privat zon
description: Översikt över en privat DNS-zon
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 04/09/2021
ms.author: rohink
ms.openlocfilehash: 0e04d7525cbd0c707ba0050f31414c2472602d1b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311421"
---
# <a name="what-is-a-private-azure-dns-zone"></a>Vad är en privat Azure DNS zon

Azure Privat DNS ger en tillförlitlig och säker DNS-tjänst för att hantera och lösa domän namn i ett virtuellt nätverk utan att behöva lägga till en anpassad DNS-lösning. Genom att använda privata DNS-zoner kan du använda dina egna anpassade domän namn istället för de namn som tillhandahålls av Azure som är tillgängliga idag. 

De poster som finns i en privat DNS-zon går inte att matcha från Internet. DNS-matchning mot en privat DNS-zon fungerar bara från virtuella nätverk som är länkade till den.

Du kan länka en privat DNS-zon till ett eller flera virtuella nätverk genom att skapa [virtuella nätverks länkar](./private-dns-virtual-network-links.md).
Du kan också aktivera funktionen för automatisk [registrering](./private-dns-autoregistration.md) för att automatiskt hantera livs cykeln för DNS-poster för de virtuella datorer som distribueras i ett virtuellt nätverk.

## <a name="limits"></a>Gränser

Information om hur många privata DNS-zoner du kan skapa i en prenumeration och hur många post uppsättningar som stöds i en privat DNS-zon finns i [Azure DNS gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits)

## <a name="restrictions"></a>Begränsningar

* Enkla, namngivna privata DNS-zoner stöds inte. Din privata DNS-zon måste ha två eller flera etiketter. Till exempel contoso.com har två etiketter avgränsade med en punkt. En privat DNS-zon kan ha maximalt 34 etiketter.
* Du kan inte skapa zon delegeringar (NS-poster) i en privat DNS-zon. Om du tänker använda en underordnad domän kan du direkt skapa domänen som en privat DNS-zon. Sedan kan du länka den till ett virtuellt nätverk utan att konfigurera en namnserver-delegering från den överordnade zonen.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du skapar en privat zon i Azure DNS med [Azure PowerShell](./private-dns-getstarted-powershell.md) eller [Azure CLI](./private-dns-getstarted-cli.md).

* Läs om några vanliga [scenarier för privata](./private-dns-scenarios.md) zoner som kan realiseras med privata zoner i Azure DNS.

* Vanliga frågor och svar om privata zoner i Azure DNS finns [privat DNS vanliga frågor](./dns-faq-private.md)och svar.