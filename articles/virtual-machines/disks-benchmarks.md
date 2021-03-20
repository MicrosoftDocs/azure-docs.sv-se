---
title: Benchmarka ditt program på Azure-disklagring
description: Lär dig mer om processen för att mäta ditt program på Azure.
author: roygara
ms.author: rogarana
ms.date: 01/29/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: bfda14acc2e50005e25faafa3037805af871c1df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99094650"
---
# <a name="benchmark-a-disk"></a>Beräkna benchmark för en disk

Benchmarking är en process för att simulera olika arbets belastningar i ditt program och mäta programmets prestanda för varje arbets belastning. Med hjälp av stegen som beskrivs i [artikeln designa för hög prestanda](premium-storage-performance.md)har du samlat in program prestanda kraven. Genom att köra benchmarking-verktyg på de virtuella datorerna som är värdar för programmet kan du bestämma vilka prestanda nivåer som programmet kan uppnå med Premium-SSD. I den här artikeln får du exempel på hur du kan mäta en Standard_D8ds_v4 virtuell dator som är etablerad med Azure Premium-SSD.

Vi har använt vanliga benchmarking-verktyg DiskSpd och FIO, för Windows respektive Linux. Dessa verktyg skapar flera trådar som simulerar en produktion som arbets belastning och mäter systemets prestanda. Med hjälp av verktygen kan du också konfigurera parametrar som block storlek och ködjup, som du normalt inte kan ändra för ett program. Detta ger dig större flexibilitet för att öka högsta prestanda på en virtuell dator med hög skalning med Premium-SSD för olika typer av program arbets belastningar. Om du vill veta mer om varje benchmarking-verktyg går du till [DiskSpd](https://github.com/Microsoft/diskspd/wiki/) och [FIO](http://freecode.com/projects/fio).

Om du vill följa exemplen nedan skapar du en Standard_D8ds_v4 och kopplar fyra Premium-SSD till den virtuella datorn. Av de fyra diskarna konfigurerar du tre med cachelagring av värden som "ingen" och stripe dem i en volym som heter NoCacheWrites. Konfigurera cachelagring av värd som "ReadOnly" på den återstående disken och skapa en volym som heter CacheReads med den här disken. Med den här installationen kan du se den maximala Läs-och skriv prestandan från en Standard_D8ds_v4 virtuell dator. Detaljerade anvisningar om hur du skapar en Standard_D8ds_v4 med Premium-SSD finns i [utforma för hög prestanda](premium-storage-performance.md).

[!INCLUDE [virtual-machines-disks-benchmarking](../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Nästa steg

Fortsätt till vår artikel om hur du [utformar för hög prestanda](premium-storage-performance.md).

I den artikeln skapar du en check lista som liknar ditt befintliga program för prototypen. Med hjälp av benchmarking-verktyg kan du simulera arbets belastningarna och mäta prestanda för prototyp programmet. Genom att göra det kan du bestämma vilket disk erbjudande som kan matcha eller överskrida dina program prestanda krav. Sedan kan du implementera samma rikt linjer för ditt produktions program.
