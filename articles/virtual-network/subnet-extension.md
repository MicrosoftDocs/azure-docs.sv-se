---
title: Under näts tillägg i Azure | Microsoft Docs
description: Lär dig mer om tillägg för undernät i Azure.
services: virtual-network
documentationcenter: na
author: anupam-p
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2019
ms.author: anupand
ms.openlocfilehash: 555c903dc3375080a8b0d006d2aac7216854c939
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99539306"
---
# <a name="subnet-extension"></a>Undernätstillägg
Migrering av arbets belastning till det offentliga molnet kräver noggrann planering och samordning. Ett av de viktigaste aspekterna kan vara möjligheten att behålla dina IP-adresser. Det kan vara viktigt, särskilt om dina program har IP-adress beroenden eller om du har krav på efterlevnad för att använda särskilda IP-adresser. Azure Virtual Network löser det här problemet åt dig genom att du kan skapa VNet och undernät med ett valfritt IP-adressintervall.

Migreringar kan få en bit utmaning när ovanstående krav är kopplade till ett ytterligare krav för att hålla vissa program lokalt. I så fall måste du dela upp programmen mellan Azure och lokalt, utan att omnumrera IP-adresserna på båda sidorna. Dessutom måste du tillåta att programmen kommunicerar som om de befinner sig i samma nätverk.

En lösning på ovanstående problem är under näts tillägg. Genom att utöka ett nätverk kan program kommunicera över samma broadcast-domän när de finns på olika fysiska platser och ta bort behovet av att bygga om nätverks sto pol Ogin. 

När du utökar nätverket är inte en bra praxis i allmänhet, under användnings fall kan det vara nödvändigt.

- Stegvis **migrering**: det vanligaste scenariot är att du vill fasa av migreringen. Du måste först ta med några program och migrera resten av programmen till Azure.
- **Svars tid**: låg latens krav kan vara ett annat skäl till att du behåller vissa program lokalt så att de är så nära som möjligt för ditt data Center.
- **Kompatibilitet**: ett annat användnings fall är att du kan ha krav på efterlevnad för att hålla vissa program lokala.
 
> [!NOTE] 
> Du bör inte utöka dina undernät om det inte är nödvändigt. I de fall där du utökar dina undernät bör du försöka göra det till ett mellanliggande steg. Med tiden bör du prova att numrera om program i ditt lokala nätverk och migrera dem till Azure.

I nästa avsnitt diskuterar vi hur du kan utöka dina undernät till Azure.


## <a name="extend-your-subnet-to-azure"></a>Utöka ditt undernät till Azure
 Du kan utöka dina lokala undernät till Azure med hjälp av en nätverks baserad lösning i Layer-3. De flesta lösningar använder en överlägg-teknik, till exempel VXLAN, för att utöka Layer-2-nätverket med ett lager-3 överläggs nätverk. Diagrammet nedan visar en generaliserad lösning. I den här lösningen finns samma undernät på båda sidor som är Azure och lokalt. 

![Exempel på under näts tillägg](./media/subnet-extension/subnet-extension.png)

IP-adresserna från under nätet tilldelas virtuella datorer på Azure och lokalt. Både Azure och lokalt har en NVA insatt i sina nätverk. När en virtuell dator i Azure försöker prata med en virtuell dator i ett lokalt nätverk fångar Azure-NVA paketet, kapslar in det och skickar det via VPN/Express Route till det lokala nätverket. Den lokala NVA tar emot paketet, decapsulates det och vidarebefordrar det till den avsedda mottagaren i sitt nätverk. Retur trafiken använder en liknande sökväg och logik.

I ovanstående exempel kommunicerar Azure-NVA och den lokala NVA och lär dig om IP-adresser bakom varandra. Mer komplexa nätverk kan också ha en mappnings tjänst som upprätthåller mappningen mellan NVA och IP-adresserna bakom dem. När en NVA tar emot ett paket, frågar den mappnings tjänsten för att ta reda på adressen till den NVA som har mål-IP-adressen bakom.

I nästa avsnitt hittar du information om under näts öknings lösningar som vi har testat på Azure.

## <a name="next-steps"></a>Nästa steg 
[Utöka ditt undernät till Azure med leverantörs lösningar.](https://github.com/microsoft/Azure-LISP)