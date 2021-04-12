---
title: Använda NFS Blob Storage med Azure HPC-cache
description: Beskriver procedurer och begränsningar när du använder ADLS-NFS-Blob Storage med Azure HPC cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: v-erkel
ms.openlocfilehash: d861a41d8cdeac548729ff341b3ffe27c0aa8152
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107260010"
---
# <a name="use-nfs-mounted-blob-storage-preview-with-azure-hpc-cache"></a>Använd NFS-monterad Blob Storage (för hands version) med Azure HPC cache

Från och med mars 2021 kan du använda NFS-monterade BLOB-behållare med Azure HPC-cache. Läs mer om [NFS 3,0-protokoll stöd i Azure Blob Storage](../storage/blobs/network-file-system-protocol-support.md) på dokumentations webbplatsen för Blob Storage.

> [!NOTE]
> NFS 3,0 protokoll stöd i Azure Blob Storage är i för hands version och bör inte användas i produktions miljöer. Sök efter uppdateringar och mer information i [stöd dokumentationen för NFS-protokollet](../storage/blobs/network-file-system-protocol-support.md).

Azure HPC cache använder NFS-aktiverad Blob Storage i sin ADLS-NFS-lagrings mål typ. Dessa lagrings mål liknar vanliga NFS-lagringsenheter, men har också vissa överlappande av vanliga Azure Blob-mål.

I den här artikeln beskrivs strategier och begränsningar som du bör känna till när du använder ADLS-NFS-lagrings mål.

Du bör också läsa dokumentationen för NFS-bloben, särskilt dessa avsnitt som beskriver kompatibla och inkompatibla scenarier:

* [Översikt över funktioner](../storage/blobs/network-file-system-protocol-support.md#applications-and-workloads-suited-for-this-feature)
* [Funktioner som ännu inte stöds](../storage/blobs/network-file-system-protocol-support.md#azure-storage-features-not-yet-supported)
* [Saker att tänka på gällande prestanda](../storage/blobs/network-file-system-protocol-support-performance.md)

## <a name="understand-consistency-requirements"></a>Förstå konsekvens krav

HPC-cache kräver stark konsekvens för lagrings mål för ADLS-NFS. Som standard uppdaterar NFS-aktiverad Blob Storage inte bara fil-metadata, vilket hindrar HPC-cache från att korrekt jämföra fil versioner.

För att undvika den här skillnaden inaktiverar Azure HPC cache automatiskt cachelagring av NFS-attribut på valfri NFS-aktiverad BLOB-behållare som används som ett lagrings mål.

Den här inställningen behålls för behållarens livs längd, även om du tar bort den från cachen.

## <a name="preload-data-with-nfs-protocol"></a>Förhandsladda data med NFS-protokoll

I en NFS-aktiverad BLOB-behållare *kan en fil bara redige ras av samma protokoll som användes när den skapades*. Det innebär att om du använder Azure-REST API för att fylla i en behållare kan du inte använda NFS för att uppdatera filerna. Eftersom Azure HPC cache bara använder NFS kan du inte redigera filer som har skapats med Azure-REST API.

Det är inte ett problem för cachen om din behållare är tom, eller om filerna har skapats med hjälp av NFS.

Om filerna i din behållare har skapats med Azure blobs REST API i stället för NFS, begränsas Azure HPC-cachen till dessa åtgärder på de ursprungliga filerna:

* Visa en lista över filen i en katalog.
* Läs filen (och håll den i cacheminnet för efterföljande läsningar).
* Ta bort filen.
* Töm filen (trunkera den till 0).
* Spara en kopia av filen. Kopian markeras som en NFS-skapad fil och kan redige ras med NFS.

Azure HPC cache **kan inte** redigera innehållet i en fil som har skapats med rest. Det innebär att det inte går att spara en ändrad fil från en klient tillbaka till lagrings målet.

Det är viktigt att förstå den här begränsningen eftersom den kan orsaka problem med data integriteten om du använder användnings modeller för läsning/skrivning för att skapa filer som inte har skapats med NFS.

> [!TIP]
> Lär dig mer om att läsa och skriva cachelagring i [förstå användnings modeller för cache](cache-usage-models.md).

### <a name="write-caching-scenarios"></a>Scenarier för skrivcache

Dessa cache-användnings modeller innehåller skrivcache:

* **Större än 15% skrivningar**
* **Större än 15% skrivningar, kontrollerar att servern har ändrats var 30: e sekund**
* **Mer än 15% skrivningar, kontrollerar den server där ändringarna ska återställas var 60 sekund**
* **Större än 15% skrivningar, skriv tillbaka till servern var 30: e sekund**

Använd bara dessa användnings modeller för att redigera filer som har skapats med NFS.

Om du försöker använda skrivcache på REST-skapade filer kan fil ändringarna gå förlorade. Detta beror på att cacheminnet inte försöker spara fil redigeringar i lagrings behållaren omedelbart.

Gör så här för att försöka cache-skrivningar till REST-skapade filer placerar data i risk zonen:

1. Cachen accepterar ändringar från klienter och returnerar ett meddelande om att varje ändring har slutförts.
1. Cachen behåller den ändrade filen i sin lagring och väntar på ytterligare ändringar.
1. När en stund har passerat försöker cacheminnet Spara den ändrade filen till backend-behållaren. I det här läget visas ett fel meddelande eftersom det försöker skriva till en REST-skapad fil med NFS.

   Det är för sent att tala om för klient datorn att ändringarna inte accepterades, och cachen har inget sätt att uppdatera original filen. Ändringarna från klienter kommer att gå förlorade.

### <a name="read-caching-scenarios"></a>Läsnings scenarier för cachelagring

Scenarier för läsning av cachelagring är lämpliga för filer som skapats med antingen NFS eller Azure Blob REST API.

Följande användnings modeller använder bara Read caching:

* **Läs tung, sällan skrivna skrivningar**
* **Klienter skriver till NFS-målet, vilket kringgår cachen**
* **Läs tung, kontrol lera servern var 3: e timme**

Du kan använda dessa användnings modeller med filer som skapats av REST API eller av NFS. Alla NFS-skrivningar som skickas från en klient till backend-behållaren fungerar fortfarande, men de kommer att Miss kopie ras omedelbart och returnera ett fel meddelande till klienten.

Ett arbets flöde för läsning av cachelagring kan fortfarande omfatta fil ändringar, förutsatt att de inte cachelagras. Klienter kan till exempel komma åt filer från behållaren, men de kan spara ändringarna som en ny fil eller spara ändrade filer på en annan plats.

## <a name="recognize-network-lock-manager-nlm-limitations"></a>Identifiera begränsningar för Nätverks Lås hanteraren (NLM)

NFS-aktiverade BLOB-behållare stöder inte Network Lock Manager (NLM), som är ett vanligt NFS-protokoll för att skydda filer från konflikter.

Om ditt NFS-arbetsflöde ursprungligen skrevs för maskin varu lagrings system kan klient programmen innehålla NLM-begäranden. Undvik den här begränsningen när du flyttar din process till NFS-aktiverad Blob Storage genom att se till att klienterna inaktiverar NLM när de monterar cacheminnet.

Om du vill inaktivera NLM använder du alternativet ``-o nolock`` i klienternas ``mount`` kommando. Det här alternativet förhindrar att klienter begär NLM lås och tar emot fel som svar.

I några situationer bekräftar Azure HPC cache sig själva NLM-begäranden från klienten. Användnings modellen för cachen med namnet **Read Stored, ovanliga skrivningar** bekräftar NLM-begäranden för Server del lagringens uppdrag. Det här systemet förhindrar fel på klienten, men skapar inget lås på ADLS-NFS-behållaren.

Om du växlar användnings modellen för ett ADLS-NFS-lagrings mål till eller från **läsning tung, ovanliga skrivningar**, måste du montera om alla klienter med ``nolock`` alternativet.

Mer information om NLM, HPC cache och användnings modeller finns i [förstå användnings modeller för cache](cache-usage-models.md#know-when-to-remount-clients-for-nlm).

## <a name="streamline-writes-to-nfs-enabled-containers-with-hpc-cache"></a>Effektivisera skrivningar till NFS-aktiverade behållare med HPC-cache

Azure HPC cache kan hjälpa till att förbättra prestanda i en arbets belastning som innehåller skrivning av ändringar till ett ADLS-NFS-lagrings mål.

> [!NOTE]
> Du måste använda NFS för att fylla i ADLS-NFS-lagringsplatsen om du vill ändra dess filer via Azure HPC-cache.

En av de begränsningar som beskrivs i artikeln NFS-aktiverade BLOB [Performance-överväganden](../storage/blobs/network-file-system-protocol-support-performance.md) är att ADLS-NFS-lagring inte är mycket effektivt när du skriver över befintliga filer. Om du använder Azure HPC cache med NFS-monterad blob-lagring, hanterar cachen återkommande omskrivning när klienter ändrar en aktiv fil. Svars tiden för att skriva en fil till Server delens behållare är dold från klienterna.

Tänk på de begränsningar som beskrivs ovan i [Förhandsladda data med NFS-protokollet](#preload-data-with-nfs-protocol).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [ADLS-NFS-krav för lagrings mål](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements-preview)
* Skapa ett [NFS-aktiverat Blob Storage-konto](../storage/blobs/network-file-system-protocol-support-how-to.md)
