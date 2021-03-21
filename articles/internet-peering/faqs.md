---
title: Internet-peering – vanliga frågor och svar
titleSuffix: Azure
description: Internet-peering – vanliga frågor och svar
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9b0b2b08e01c99fc918c4bc5649197c9caa4978a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "75775503"
---
# <a name="internet-peering---faqs"></a>Internet-peering – vanliga frågor och svar

Du kan granska informationen nedan för allmänna frågor.

**Vad är skillnaden mellan Internet peering-och peering-tjänsten?**

Peering-tjänsten är en tjänst som avser att ge företags klassens offentliga IP-anslutning till Microsoft för sina företags kunder. Internet i företags klass inkluderar anslutning via Internet-leverantörer som har hög genom strömnings anslutning till Microsoft och redundans för en HA-anslutning. Dessutom optimeras användar trafiken för svars tider till närmaste Microsoft Edge. Peering-tjänsten bygger på peering-anslutningar med partner företag. Peering-anslutningen med partner måste vara direkt peering i stället för Exchange-peering. Direkt peering måste ha lokal och GEO-redundans.

**Vad är äldre peering?**

Peering-anslutning som konfigureras med Azure PowerShell hanteras som en Azure-resurs. Peering-anslutningar som har kon figurer ATS tidigare lagras i vårt system som äldre peering som du kan välja att konvertera till att hantera som en Azure-resurs.

**Vilka IP-adresser ges till Microsoft-och peer-enheter när New-AzPeeringDirectConnectionObject anropas?**

När du anropar New-AzPeeringDirectConnectionObject cmdleten anges en/31-adress (a. b. c. d/31) eller en/30-adress (a. b. c. d/30). Den första IP-adressen (a. b. c. d + 0) tilldelas peer-enheten och den andra IP-adressen (a. b. c. d + 1) har tilldelats till Microsoft Device.

**Vad är MaxPrefixesAdvertisedIPv4-och MaxPrefixesAdvertisedIPv6-parametrar i New-AzPeeringDirectConnectionObject cmdlet?**

Parametrarna MaxPrefixesAdvertisedIPv4 och MaxPrefixesAdvertisedIPv6 representerar det maximala antalet IPv4-och IPv6-prefix som en peer vill att Microsoft accepterar. Dessa parametrar kan ändras när som helst.