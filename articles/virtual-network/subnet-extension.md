---
title: Undernätstillägg i Azure | Microsoft Docs
description: Läs mer om undernätstillägg i Azure.
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
ms.openlocfilehash: e39859e3cc4d28ac4b1456fcdaec65ecfb9c7f31
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728220"
---
# <a name="subnet-extension"></a>Undernätstillägg
Migrering av arbetsbelastningar till det offentliga molnet kräver noggrann planering och samordning. Ett av de viktigaste övervägandena kan vara möjligheten att behålla dina IP-adresser. Detta kan vara viktigt, särskilt om dina program är beroende av IP-adresser eller om du har efterlevnadskrav för att använda specifika IP-adresser. Azure Virtual Network löser det här problemet åt dig genom att låta dig skapa virtuella nätverk och undernät med val av IP-adressintervall.

Migreringar kan bli lite utmanande när ovanstående krav kombineras med ytterligare ett krav för att vissa program ska vara lokala. I en sådan situation måste du dela upp programmen mellan Azure och lokalt, utan att omnumrera IP-adresserna på någon av sidorna. Dessutom måste du tillåta att programmen kommunicerar som om de vore i samma nätverk.

En lösning på problemet ovan är undernätstillägget. Genom att utöka ett nätverk kan program prata över samma sändningsdomän när de finns på olika fysiska platser, vilket gör att du inte behöver göra om arkitekturen för nätverkstopologin. 

Att utöka nätverket är inte en bra idé i allmänhet, men användningsfallen nedan kan göra det nödvändigt.

- **Fasindelade** migrering: Det vanligaste scenariot är att du vill fasa in migreringen. Du vill göra några program först och över tid för att migrera resten av programmen till Azure.
- **Svarstid:** Krav på låg latens kan vara en annan anledning till att du behåller vissa program lokalt för att säkerställa att de ligger så nära ditt datacenter som möjligt.
- **Efterlevnad:** Ett annat användningsfall är att du kan ha efterlevnadskrav för att behålla vissa av dina program lokalt.
 
> [!NOTE] 
> Du bör inte utöka dina undernät om det inte är nödvändigt. I de fall där du utökar dina undernät bör du försöka göra det till ett mellanliggande steg. Med tiden bör du försöka numrera om program i ditt lokala nätverk och migrera dem till Azure.

I nästa avsnitt diskuterar vi hur du kan utöka dina undernät till Azure.


## <a name="extend-your-subnet-to-azure"></a>Utöka ditt undernät till Azure
 Du kan utöka dina lokala undernät till Azure med en nätverksbaserad layer-3-överläggslösning. De flesta lösningar använder en överläggsteknik som VXLAN för att utöka layer-2-nätverket med ett layer-3-överläggsnätverk. Diagrammet nedan visar en generaliserad lösning. I den här lösningen finns samma undernät på båda sidor, det vill säga Azure och lokalt. 

![Exempel på undernätstillägg](./media/subnet-extension/subnet-extension.png)

IP-adresserna från undernätet tilldelas till virtuella datorer i Azure och lokalt. Både Azure och lokalt har en NVA infogade i sina nätverk. När en virtuell dator i Azure försöker prata med en virtuell dator i ett lokalt nätverk samlar Azure NVA in paketet, kapslar in det och skickar det via VPN/Express Route till det lokala nätverket. Den lokala NVA:n tar emot paketet, avkapslar det och vidarebefordrar det till den avsedda mottagaren i nätverket. Returtrafiken använder en liknande sökväg och logik.

I exemplet ovan kommunicerar Azure NVA och den lokala NVA:n och lär sig mer om IP-adresser bakom varandra. Mer komplexa nätverk kan också ha en mappningstjänst som underhåller mappningen mellan NVA:erna och IP-adresserna bakom dem. När en NVA tar emot ett paket frågar den mappningstjänsten efter adressen till den NVA som har mål-IP-adressen bakom sig.

I nästa avsnitt hittar du information om lösningar för undernätstillägg som vi har testat i Azure.

## <a name="next-steps"></a>Nästa steg 
[Utöka dina lokala undernät till Azure med hjälp av Azure Extended Network](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/azure-extended-network).