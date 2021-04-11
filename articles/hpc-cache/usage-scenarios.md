---
title: Scenarier med Azure HPC-cache
description: Beskriver hur du vet om ditt data bearbetnings jobb fungerar bra med Azure HPC-cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: v-erkel
ms.openlocfilehash: 36e0135102fbede5505e96fb1aa255588b2f2ae2
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107260006"
---
# <a name="is-your-job-a-good-fit-for-azure-hpc-cache"></a>Passar ditt jobb bra för Azure HPC-cache?

Azure HPC cache kan påskynda åtkomst till data för data behandling med höga prestanda i en mängd olika ämnes områden. Men det är inte perfekt för alla typer av arbets flöden. Den här artikeln innehåller rikt linjer för hur du avgör om HPC cache är ett lämpligt alternativ för dina behov.

[Översikts](hpc-cache-overview.md) artikeln ger också en kort beskrivning av när du ska använda Azure HPC cache och några exempel på användnings fall.

Läs även [den här artikeln](nfs-blob-considerations.md) om hur du effektivt använder [NFS-monterad Blob Storage](../storage/blobs/network-file-system-protocol-support.md), som finns i för hands version.

## <a name="nfs-version-30-applications"></a>NFS version 3,0-program

Azure HPC cache stöder endast NFS 3,0-klienter.

## <a name="high-read-to-write-ratio"></a>Högt Skriv-till-Skriv-förhållande

Arbets belastningar där beräknings klienterna gör mer läsning än att skriva är ofta bra kandidater för en cache. Om ditt Skriv-till-Skriv-förhållande till exempel är 80/20 eller 70/30 kan Azure HPC-cache hjälpa till med att betjäna ofta begärda filer från cachen i stället för att behöva hämta dem från Fjärrlagring över och över.

Att hämta en fil och lagra den i cacheminnet för första gången har en liten extra fördröjning över en normal klient förfrågan direkt till lagring, så effektivitets ökningen kommer att visas nästa gången en klient begär samma fil. Detta gäller särskilt för stora filer. Om varje klientbegäran är unik är HPC-cachens påverkan begränsad. Men ju större filen är desto bättre blir prestandan över tid efter den första gången.

## <a name="file-based-analytic-workload"></a>Filbaserad analytisk arbets belastning

Azure HPC-cachen är idealisk för en pipeline som använder filbaserade data och körs över ett stort antal beräknings klienter, särskilt om Compute-klienterna är virtuella Azure-datorer. Det kan hjälpa till att åtgärda långsamma eller inkonsekventa prestanda som orsakas av långa fil åtkomst tider.

## <a name="remote-data-access"></a>Fjärråtkomst till data

Azure HPC-cache kan hjälpa till att minska svars tiden om din arbets belastning behöver åtkomst till fjärrdata som inte kan flyttas närmare dator resurserna. Dina poster kan till exempel vara i slutet av en WAN-miljö, i en annan Azure-region eller i ett kund Data Center. (Detta kallas ibland "fil bursting".)

## <a name="heavy-request-load"></a>Belastning för tung begäran

Om ett stort antal klienter begär data från källan samtidigt kan Azure HPC-cache påskynda åtkomsten till filen. Om till exempel används med ett högpresterande data behandlings kluster tillhandahåller Azure HPC cache skalbarhet för ett stort antal samtidiga begär Anden via cachen.

## <a name="compute-resources-are-located-in-azure"></a>Beräknings resurser finns i Azure

Virtuella Azure-datorer är ett skalbart och kostnads effektivt svar på arbets belastningen med hög prestanda. Azure HPC-cache kan hjälpa dig genom att ta fram den information som de behöver närmare, särskilt om de ursprungliga data lagras på ett fjärrsystem.

Om en kund vill köra sin nuvarande pipeline "i befintligt skick" i Azure Virtual Machines kan Azure HPC-cache tillhandahålla en POSIX-baserad lösning för delad lagring (eller caching) för skalbarhet.

Genom att använda Azure HPC-cache behöver du inte skapa om arbets pipelinen för att göra interna anrop till Azure Blob Storage. Du kan komma åt dina data på det ursprungliga systemet eller använda HPC-cache för att flytta den till en ny BLOB-behållare.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om hur du planerar och konfigurerar en cache i [översikts](hpc-cache-overview.md) -och [krav](hpc-cache-prerequisites.md) artiklarna
* Läs överväganden för att använda [NFS-aktiverad Blob Storage](nfs-blob-considerations.md) (för hands version) med Azure HPC cache
