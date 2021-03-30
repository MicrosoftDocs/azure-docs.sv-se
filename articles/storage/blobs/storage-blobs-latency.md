---
title: Svars tid i Blob Storage – Azure Storage
description: Förstå och mäta svars tider för Blob Storage-åtgärder och lär dig hur du utformar dina Blob Storage-program för låg latens.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 09/05/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 78440b8150a0992bed2e2a3e597fdac8e7a1c7b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75749722"
---
# <a name="latency-in-blob-storage"></a>Latens i Blobblagring

Svars tid, som ibland refereras som svars tid, är den tid som ett program måste vänta på att en begäran ska slutföras. Svars tiden kan påverka programmets prestanda direkt. Låg latens är ofta viktigt för scenarier med människor i slingan, till exempel för att utföra kredit korts transaktioner eller läsa in webb sidor. System som behöver bearbeta inkommande händelser med hög taxa, till exempel loggning av telemetri eller IoT-händelser, kräver också låg latens. Den här artikeln beskriver hur du förstår och mäter svars tider för åtgärder på block-blobbar och hur du utformar dina program för låg latens.

Azure Storage erbjuder två olika prestanda alternativ för block blobbar: Premium och standard. Premium block-blobar erbjuder betydligt lägre och mer konsekvent svars tid än standard block-blobar via hård diskar med höga prestanda. Mer information finns i **Premium Performance Block Blob Storage** i [Azure Blob Storage: frekvent åtkomst, låg frekvent åtkomst och Arkiv](storage-blob-storage-tiers.md)lag rings nivåer.

## <a name="about-azure-storage-latency"></a>Om Azure Storage svars tid

Azure Storage fördröjningen är relaterad till begär ande avgifter för Azure Storage åtgärder. Begär ande frekvenser kallas även för in-/utdata-åtgärder per sekund (IOPS).

Om du vill beräkna begär ande frekvensen bestämmer du först hur lång tid varje begäran tar att slutföra och sedan beräknar du hur många begär Anden som kan bearbetas per sekund. Anta till exempel att en begäran tar 50 millisekunder (MS) att slutföras. Ett program som använder en tråd med en utestående Läs-eller Skriv åtgärd bör uppnå 20 IOPS (1 sekund eller 1000 MS/50 ms per begäran). Teoretiskt sett, om antalet trådar är dubblerat till två, ska programmet kunna uppnå 40 IOPS. Om de utestående asynkrona Läs-eller Skriv åtgärderna för varje tråd dubbleras till två bör programmet kunna uppnå 80 IOPS.

I praktiken skalar begär ande avgifter inte alltid så linjärt, på grund av omkostnader i klienten från aktivitets schemaläggningen, kontext växling och så vidare. På tjänst sidan kan det finnas variabilitet i latens på grund av ett tryck på Azure Storage systemet, skillnader i de lagrings medier som används, brus från andra arbets belastningar, underhålls aktiviteter och andra faktorer. Slutligen kan nätverks anslutningen mellan klienten och servern påverka Azure Storage latens på grund av överbelastning, omdirigering eller andra avbrott.

Azure Storage bandbredd, som även kallas data flöde, är relaterat till begär ande frekvensen och kan beräknas genom att multiplicera begär ande frekvensen (IOPS) med begär ande storleken. Anta till exempel att du antar 160 begär Anden per sekund, varje 256 KiB av data i genomflödet av 40 960 KiB per sekund eller 40 MiB per sekund.

## <a name="latency-metrics-for-block-blobs"></a>Svars tids mått för block-blobar

Azure Storage ger två fördröjnings mått för block-blobar. De här måtten kan visas i Azure Portal:

- **Svars tiden från slut punkt till slut punkt (E2E)** mäter intervallet från när Azure Storage tar emot det första paketet i begäran tills Azure Storage får en klient bekräftelse på det sista paketet av svaret.

- **Server fördröjning** mäter det intervall från vilket Azure Storage tar emot det sista paketet i begäran tills det första paketet av svaret returneras från Azure Storage.

Följande bild visar den **genomsnittliga lyckade E2E-svars tiden** och den **genomsnittliga server fördröjningen** för en exempel arbets belastning som anropar `Get Blob` åtgärden:

![Skärm bild som visar svars värden för Get BLOB-åtgärd](media/storage-blobs-latency/latency-metrics-get-blob.png)

Under normala förhållanden uppstår det ett litet mellanrum mellan svars tiden från slut punkt till slut punkt och Server fördröjning, vilket är vad bilden visar för exempel arbets belastningen.

Om du granskar dina resultat från slut punkt till slut punkt och Server svars tid och upptäcker att svars tiden från slut punkt till slut punkt är betydligt högre än Server svars tiden kan du undersöka och åtgärda källan till den ytterligare svars tiden.

Om din slut punkt till slut punkt och Server fördröjningen är liknande, men du behöver lägre latens, kan du överväga att migrera till Premium Block Blob Storage.

## <a name="factors-influencing-latency"></a>Faktorer som påverkar svars tiden

Den huvudsakliga faktorn som påverkar svars tiden är åtgärds storlek. Det tar längre tid att slutföra större åtgärder, på grund av mängden data som överförs via nätverket och bearbetas av Azure Storage.

Följande diagram visar den totala tiden för åtgärder av olika storlekar. För små mängder data är svars tiden i huvudsak ägnad att hantera begäran, i stället för att överföra data. Latens intervallet ökar bara en aning när åtgärdens storlek ökar (markerade 1 i diagrammet nedan). När åtgärds storleken ökar ökar mer tid för att överföra data, så att det totala latens intervallet delas mellan hantering av begär Anden och data överföring (markerade 2 i diagrammet nedan). Med större drift storlekar är latens intervallet nästan uteslutande för överföring av data och hantering av begär Anden är i stort sett obetydligt (markerat 3 i diagrammet nedan).

![Skärm bild som visar den totala åtgärds tiden per åtgärds storlek](media/storage-blobs-latency/operation-time-size-chart.png)

Klient konfigurations faktorer som samtidighet och trådning påverkar också svars tiden. Det totala data flödet beror på hur många lagrings begär Anden som finns vid en viss tidpunkt och hur programmet hanterar trådar. Klient resurser, inklusive processor, minne, lokal lagring och nätverks gränssnitt kan också påverka svars tiden.

Bearbetning av Azure Storage begär Anden kräver klient-CPU-och minnes resurser. Om klienten befinner sig i tryck på grund av en undertryckt virtuell dator eller en Ströms par process i systemet finns det färre tillgängliga resurser för att bearbeta Azure Storage begär Anden. Om ingen konkurrens uppstår eller om klient resurser saknas ökar svars tiden från slut punkt till slut punkt utan en ökning av Server fördröjningen, vilket ökar mellanrummet mellan de två måtten.

Lika viktigt är nätverks gränssnittet och nätverks pipe mellan klienten och Azure Storage. Enbart fysiska avstånd kan vara en betydande faktor, till exempel om en virtuell klient dator finns i en annan Azure-region eller lokalt. Andra faktorer som nätverks hopp, ISP-Routning och Internet-tillstånd kan påverka den totala lagrings fördröjningen.

För att utvärdera svars tiden måste du först upprätta bas linje mått för ditt scenario. Med bas linje mått får du den förväntade slut punkt till slut punkt och Server fördröjning i kontexten för din program miljö, beroende på din arbets belastnings profil, program konfigurations inställningar, klient resurser, nätverks pipe och andra faktorer. När du har bas linje mått kan du enklare identifiera onormala jämfört med normala villkor. Med bas linje mått kan du också studera effekterna av ändrade parametrar, till exempel program konfiguration eller VM-storlekar.

## <a name="next-steps"></a>Nästa steg

- [Skalbarhets-och prestanda mål för Blob Storage](scalability-targets.md)
- [Check lista för prestanda och skalbarhet för Blob Storage](storage-performance-checklist.md)
