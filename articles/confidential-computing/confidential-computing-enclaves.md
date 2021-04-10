---
title: Konfidentiell dator användning av virtuella datorer i Azure
description: Lär dig mer om Intel SGX-maskinvara för att aktivera dina konfidentiella arbets belastningar.
services: virtual-machines
author: JenCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/3/2020
ms.author: JenCook
ms.openlocfilehash: 554260b2a2760380d3bb2d91ee25b4a03bf2f1ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102551376"
---
# <a name="azure-confidential-computing-virtual-machines-vms-overview"></a>Översikt över Azure konfidentiella datorer för virtuella datorer (VM)


Azure är den första moln leverantören som erbjuder konfidentiell data behandling i en virtualiserad miljö. Vi har utvecklat virtuella datorer som fungerar som ett abstraktions lager mellan maskin varan och ditt program. Du kan köra arbets belastningar i skala och med alternativ för redundans och tillgänglighet.  

## <a name="intel-sgx-enabled-virtual-machines"></a>Intel SGX-aktiverade Virtual Machines

I Azures konfidentiella virtuella datorer är en del av PROCESSORns maskin vara reserverad för en del av kod och data i ditt program. Den här begränsade delen är enklaven. 

![VM-modell](media/overview/hardware-backed-enclave.png)

Azures infrastruktur för konfidentiell behandling består för närvarande av en special-SKU av virtuella datorer (VM). De här virtuella datorerna körs på Intel-processorer med Software Guard-tillägget (Intel SGX). [Intel SGX](https://intel.com/sgx) är den komponent som möjliggör det ökade skyddet som vi har med konfidentiell dator användning. 

Idag erbjuder Azure [DCsv2-serien](../virtual-machines/dcv2-series.md) som bygger på Intel SGX-teknik för maskinvarubaserad enklaven-skapande. Du kan skapa säkra enklaven-baserade program som ska köras i DCsv2-serien med virtuella datorer för att skydda dina program data och kod som används. 

[Läs mer](virtual-machine-solutions.md) om att distribuera virtuella datorer med Azure konfidentiell dator med maskinvarubaserad betrodda enclaves.

## <a name="enclaves"></a>Enclaves

Enclaves är säkra delar av maskin varans processor och minne. Det finns inget sätt att visa data eller kod inuti enklaven, även med en fel sökare. Om ej betrott kod försök ändrar innehållet i enklaven-minnet inaktive ras miljön och åtgärderna nekas.

I grunden kan du tänka på en enklaven som en säker ruta. Du anger krypterad kod och data i rutan. Från utsidan av rutan kan du inte se något. Du ger enklaven en nyckel för att dekryptera data, och sedan bearbetas och krypteras data innan de skickas ut från enklaven.

Varje enklaven har en uppsättning storlek på en krypterad Page cache (EPC) som avgör mängden minne som varje enklaven kan innehålla. Större virtuella DCsv2-datorer har mer EPC-minne. Läs sidan med [DCsv2-specifikationer](../virtual-machines/dcv2-series.md) för maximal EPC per virtuell dator storlek.



### <a name="developing-applications-to-run-inside-enclaves"></a>Utveckla program som ska köras i enclaves
När du utvecklar program kan du använda- [program verktyg](application-development.md) för att avskärma delar av koden och data i enklaven. Dessa verktyg ser till att din kod och dina data inte kan visas eller ändras av någon utanför den betrodda miljön. 

## <a name="next-steps"></a>Nästa steg
- [Läs metod tips](virtual-machine-solutions.md) för att distribuera lösningar på virtuella Azure-datorer med konfidentiella data behandling.
- [Distribuera en DCsv2-Series virtuell dator](quick-create-portal.md)
- [Utveckla ett enklaven-medvetet program](application-development.md) med hjälp av OE SDK