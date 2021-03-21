---
title: Noder för data skiktning och tillägg för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Noder för data skiktning och tillägg för SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: eb780d10996cb22f5e6fe5bc8889e897e8c3854d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666766"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Använd SAP HANA data skiktning och tilläggs noder

SAP stöder en data skikts modell för SAP BW av olika SAP NetWeaver-versioner och SAP BW/4HANA. Mer information om data skikts modellen finns i SAP [-dokument SAP BW/4HANA och SAP BW på Hana med SAP HANA-tilläggsprovider](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
Med en stor instans av HANA kan du använda alternativ-1-konfiguration för SAP HANA tilläggsfunktioner som beskrivs i vanliga frågor och svar och SAP blogg dokument. Alternativ-2 konfigurationer kan konfigureras med följande HANA stor instans-SKU: er: S72m, S192, S192m, S384 och S384m. 

När du tittar på dokumentationen kanske inte fördelen visas omedelbart. Men när du tittar på rikt linjerna för SAP-storlek kan du se en fördel genom att använda alternativ-1 och alternativ-2 SAP HANA tilläggsfunktioner. Här följer några exempel:

- Rikt linjerna för SAP HANA storlek kräver vanligt vis dubbelt så mycket data volym som minne. När du kör SAP HANA-instansen med frekventa data, har du bara 50 procent eller mindre av minnet som är fyllt med data. Resten av minnet är idealisk för SAP HANA att utföra sitt arbete.
- Det innebär att du i en S192-enhet med ett stort antal instanser med 2 TB minne, som kör en SAP BW databas, bara har 1 TB som data volym.
- Om du använder ytterligare en nod för SAP HANA tillägg i alternativ-1, även en S192 HANA stor instans-SKU, ger du ytterligare 2 TB-kapacitet för data volym. I alternativ-2-konfigurationen får du ytterligare 4 TB för varm data volym. Jämfört med den aktiva noden kan den fullständiga minnes kapaciteten för tillägget "varm" användas för data lagring för alternativ-1. Dubbla minnet kan användas för data volymer i alternativ-2 SAP HANA-tilläggsprovider.
- Du är klar med en kapacitet på 3 TB för dina data och ett frekvent till varmt förhållande på 1:2 för alternativ-1. Du har 5 TB data och ett 1:4-förhållande med alternativ-2-tilläggets konfiguration.

Ju högre data volym jämfört med minnet, desto högre är risken att de varma data som du frågar efter lagras på disk lagring.

**Nästa steg**
- Referera [SAP HANA (stora instanser) arkitektur i Azure](hana-architecture.md)