---
title: ta med fil
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: emuench
ms.author: mingshen
ms.date: 10/09/2020
ms.openlocfilehash: f4e34e850391696506beed9f6f386f85528dff24
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92284814"
---
Om du har valt en av de virtuella datorer som är förkonfigurerade med ett operativ system (och valfria ytterligare tjänster) har du redan valt en standard storlek för virtuella Azure-datorer. Den rekommenderade metoden är att starta din lösning med ett förkonfigurerat operativ system. Om du installerar ett operativ system manuellt måste du dock ändra den primära virtuella hård disken i din VM-avbildning. Se till att OS-diskens storlek ligger inom gränserna för Linux eller Windows.

| Operativsystem | VHD-storlek |
| --- | --- |
| Linux | 30 till 1023 GB |
| Windows | 30 till 250 GB |
|

De grundläggande Windows-eller SQL Server-avbildningarna som tillhandahålls som en godkänd bas uppfyller redan dessa krav, så ändra inte formatet eller storleken på den virtuella hård disken.

Data diskar kan vara så stora som 1 TB. Kom ihåg att kunder inte kan ändra storlek på VHD: er i en avbildning vid tidpunkten för distributionen när de väljer storlek. Skapa datadisk-VHD: er som virtuella hård diskar med fast format. De bör också vara expanderbara (glesa/dynamiska). Data diskar kan inlednings vis vara tomma eller innehålla data.