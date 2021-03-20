---
title: Förstå Azure HPC cache-aggregerad namnrymd
description: Planera det virtuella namn området för Azure HPC-cachen
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 1c28f549cf93d77f6aef6bcde6a2225345a79cc9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91612956"
---
# <a name="plan-the-aggregated-namespace"></a>Planera det aggregerade namnområdet

Med Azure HPC cache får klienterna åtkomst till en mängd olika lagrings system via ett virtuellt namn område som döljer information om Server dels lagrings systemet.

När du har lagt till ett lagrings mål ställer du in en eller flera namn områdes Sök vägar för klienter som är riktade mot lagrings målet. Klient datorer monterar den här fil Sök vägen och kan göra fil läsnings begär anden till cacheminnet i stället för att montera lagrings systemet direkt.

Eftersom Azure HPC cache hanterar det här virtuella fil systemet kan du ändra lagrings målet utan att ändra sökvägen till klienten. Du kan till exempel ersätta ett maskin varu lagrings system med moln lagring utan att behöva skriva om klient sidans procedurer.

## <a name="aggregated-namespace-example"></a>Exempel på sammanställd namnrymd

Planera det sammansatta namn området så att klient datorerna enkelt kan komma åt den information de behöver, och så att administratörer och arbets flödes tekniker enkelt kan särskilja Sök vägarna.

Anta till exempel ett system där en Azure HPC cache-instans används för att bearbeta data som lagras i Azure blob. För analysen krävs mallfiler som lagras i ett lokalt Data Center.

Mal lin data lagras i ett Data Center och den information som krävs för det här jobbet lagras i följande under kataloger:

* */goldline/templates/acme2017/sku798*
* */goldline/templates/acme2017/sku980*

Data Center Storage-systemet visar dessa exporter:

* */*
* */goldline*
* */goldline/templates*

De data som ska analyseras har kopierats till en Azure Blob Storage-behållare med namnet "sourcecollection" med hjälp av [CLFSLoad-verktyget](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload).

Överväg att skapa lagrings mål med dessa sökvägar för virtuella namn områden för att tillåta enkel åtkomst via cachen:

| Server dels lagrings system <br/> (NFS-filsökväg eller BLOB-behållare) | Sökväg för virtuell namnrymd |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| sourcecollection                        | /source               |

Ett NFS-lagrings mål kan ha flera sökvägar för virtuella namn områden, så länge var och en refererar till en unik export Sök väg. (Läs [NFS-namnrymder](add-namespace-paths.md#nfs-namespace-paths) för att lära dig det rekommenderade maximala antalet sökvägar sökvägar per NFS-lagringsenhet.)

Eftersom NFS-källans sökvägar är under kataloger för samma export, måste du definiera flera namn områdes Sök vägar från samma lagrings mål.

| Värdnamn för lagrings mål  | Sökväg till NFS-export     | Sökväg till under Katalog | Sökväg till namnrymd    |
|--------------------------|---------------------|-------------------|-------------------|
| *IP-adress eller värdnamn* | /goldline/templates | acme2017/sku798   | /templates/sku798 |
| *IP-adress eller värdnamn* | /goldline/templates | acme2017/sku980   | /templates/sku980 |

Ett klient program kan montera cachen och enkelt komma åt de aggregerade Sök vägarna för namn rymds filen ``/source`` , ``/templates/sku798`` och ``/templates/sku980`` .

En alternativ metod kan vara att skapa en virtuell sökväg, till exempel `/templates` länkar till den överordnade katalogen, `acme2017` och sedan låta klienterna navigera till den enskilda katalogen `sku798` och `sku980` katalogerna när cachen har monterats. Du kan dock inte skapa en namn områdes Sök väg som är en under katalog till en annan namn områdes Sök väg. Så om du skapar en sökväg till `acme2017` katalogen kan du inte också skapa namn områdes Sök vägar för att få direkt åtkomst till dess under kataloger.

Sidan Inställningar för Azure HPC cache- **namnområde** visar det klientbaserade fil systemet och låter dig lägga till eller redigera sökvägar. Läs [Konfigurera det](add-namespace-paths.md) sammanställda namn området för mer information.

## <a name="next-steps"></a>Nästa steg

När du har bestämt dig för att skapa ett virtuellt fil system ska du göra följande:

* [Skapa lagrings mål](hpc-cache-add-storage.md) för att lägga till dina Server dels lagrings system i Azure HPC-cachen
* [Lägg till Sök vägs Sök vägar](add-namespace-paths.md) för att skapa det sammanställda namn området som klient datorer använder för att komma åt filer
