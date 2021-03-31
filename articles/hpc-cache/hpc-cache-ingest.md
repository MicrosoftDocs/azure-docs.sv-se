---
title: Flytta data till en Azure HPC cache-molnbaserad behållare
description: Så här fyller du i Azure Blob Storage för användning med Azure HPC cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: 0da8a4fc1b59976c50cd96f2155715a4cb178cc9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87072764"
---
# <a name="move-data-to-azure-blob-storage"></a>Flytta data till Azure Blob Storage

Om arbets flödet innefattar att flytta data till Azure Blob Storage bör du se till att du använder en effektiv strategi. Du kan antingen förinstallera data i en ny BLOB-behållare innan du definierar den som ett lagrings mål, eller lägga till behållaren och sedan kopiera dina data med Azure HPC cache.

Den här artikeln beskriver de bästa sätten att flytta data till Blob Storage för användning med Azure HPC-cache.

Tänk på följande saker:

* Azure HPC cache använder ett specialiserat lagrings format för att organisera data i blob-lagring. Detta är anledningen till att ett Blob Storage-mål antingen måste vara en ny, tom behållare eller en BLOB-behållare som tidigare använts för Azure HPC cache-data.

* Att kopiera data via Azure HPC-cache till ett Server dels lagrings mål är mer effektivt när du använder flera klienter och parallella åtgärder. Ett enkelt kopierings kommando från en klient kommer att flytta data långsamt.

Det finns ett python-baserat verktyg för att läsa in innehåll i en Blob Storage-behållare. Läs för [inläsning av data i Blob Storage](#pre-load-data-in-blob-storage-with-clfsload) för mer information.

Om du inte vill använda inläsnings verktyget, eller om du vill lägga till innehåll i ett befintligt lagrings mål, följer du tipsen för parallell data inmatning i [Kopiera data via Azure HPC-cachen](#copy-data-through-the-azure-hpc-cache).

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>För inläsning av data i blob-lagring med CLFSLoad

Du kan använda verktyget aver CLFSLoad för att kopiera data till en ny Blob Storage-behållare innan du lägger till den som ett lagrings mål. Det här verktyget körs på ett enda Linux-system och skriver data i det tillverkarspecifika format som krävs för Azure HPC-cache. CLFSLoad är det mest effektiva sättet att fylla i en Blob Storage-behållare för användning med cachen.

Verktyget aver CLFSLoad är tillgängligt på begäran från Azure HPC cache-teamet. Be din grupp kontakta din kontakt eller öppna ett [support ärende](hpc-cache-support-ticket.md) för att be om hjälp.

Det här alternativet fungerar med nya, tomma behållare. Skapa behållaren innan du använder AVERT CLFSLoad.

Detaljerad information finns i CLFSLoad-distributionen för AVERT, som är tillgänglig på begäran från Azure HPC cache-teamet.

En allmän översikt över processen:

1. Förbered ett Linux-system (VM eller fysisk) med python version 3,6 eller senare. Python 3,7 rekommenderas för bättre prestanda.
1. Installera Avere-CLFSLoad-programvaran på Linux-systemet.
1. Kör överföringen från Linux-kommandoraden.

Verktyget aver CLFSLoad behöver följande information:

* Det lagrings konto-ID som innehåller din Blob Storage-behållare
* Namnet på den tomma Blob storage-behållaren
* En signatur för signatur för delad åtkomst (SAS) som tillåter att verktyget skriver till behållaren
* En lokal sökväg till data källan – antingen en lokal katalog som innehåller de data som ska kopieras eller en lokal sökväg till ett monterat fjärrsystem med data

## <a name="copy-data-through-the-azure-hpc-cache"></a>Kopiera data via Azure HPC-cache

Om du inte vill använda verktyget aver CLFSLoad eller om du vill lägga till en stor mängd data till ett befintligt Blob Storage-mål, kan du kopiera det via cachen. Azure HPC cache är utformat för att betjäna flera klienter samtidigt, så att du kan kopiera data via cachen genom att använda parallella skrivningar från flera klienter.

![Diagram över flera klienter, multi-threadd data förflyttning: längst upp till vänster finns det flera pilar från den lokala maskin varu lagringen. Pilarna pekar på fyra klient datorer. Från varje klient dator tre pilar pekar du mot Azure HPC-cachen. Från Azure HPC cache pekar flera pilar på Blob Storage.](media/hpc-cache-parallel-ingest.png)

De ``cp`` eller ``copy`` -kommandon som du vanligt vis använder för att överföra data från ett lagrings system till ett annat är processer med enkel tråd som bara kopierar en fil i taget. Det innebär att fil servern bara matar in en fil i taget, vilket är slöseri med cachens resurser.

I det här avsnittet beskrivs strategier för att skapa en fil kopierings system med flera klienter och flera trådar för att flytta data till blob-lagring med Azure HPC-cache. Den förklarar fil överförings koncept och besluts punkter som kan användas för effektiv data kopiering med hjälp av flera klienter och kommandon för enkel kopiering.

Det beskriver också vissa verktyg som kan hjälpa dig. ``msrsync``Verktyget kan användas för att delvis automatisera processen med att dela upp en data uppsättning i buckets och använda rsync-kommandon. ``parallelcp``Skriptet är ett annat verktyg som läser käll katalogen och utfärdar kopierings kommandon automatiskt.

### <a name="strategic-planning"></a>Strategisk planering

När du skapar en strategi för att kopiera data parallellt bör du förstå kompromisserna i fil storlek, antal filer och katalog djup.

* När filerna är små är måtten för intresse filer per sekund.
* När filerna är stora (10MiBi eller större) är måttet för ränta byte per sekund.

Varje kopierings process har en data flödes hastighet och en fil överförings hastighet, som kan mätas med tiden för kopierings kommandot och väga fil storleken och antalet filer. Att förklara hur priserna ska mätas ligger utanför omfånget för det här dokumentet, men det är absolut nödvändigt att förstå om du ska hantera små eller stora filer.

Strategier för parallell data inmatning med Azure HPC-cache inkluderar:

* Manuell kopiering – du kan manuellt skapa en flertrådad kopia på en klient genom att köra fler än ett kopierings kommando på en gång i bakgrunden mot fördefinierade uppsättningar av filer eller sökvägar. Läs mer i [Azure HPC cache-data inmatning – manuell kopierings metod](hpc-cache-ingest-manual.md) .

* Delvis automatiserad kopiering med ``msrsync``  -  ``msrsync`` är ett wrapper-verktyg som kör flera parallella ``rsync`` processer. Mer information finns i [Azure HPC-data intag – msrsync-metoden](hpc-cache-ingest-msrsync.md).

* Skriptad kopiering med ``parallelcp`` – Lär dig hur du skapar och kör ett parallellt kopierings skript i [Azure HPC-data inmatning – parallell kopiering skript metod](hpc-cache-ingest-parallelcp.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat lagringen kan du läsa om hur klienter kan montera cacheminnet.

* [Få åtkomst till Azure HPC cache-systemet](hpc-cache-mount.md)
