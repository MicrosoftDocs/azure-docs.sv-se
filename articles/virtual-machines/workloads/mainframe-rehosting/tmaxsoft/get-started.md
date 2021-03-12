---
title: Kom igång med TmaxSoft OpenFrame på Azure Virtual Machines
description: Revara värd för dina IBM z/OS stordatorer-arbetsbelastningar med hjälp av TmaxSoft OpenFrame-miljö på Azure Virtual Machines (VM).
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: workloads
ms.openlocfilehash: a8210a47cdc31b28bd7dd2b0021570c18db89423
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558210"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Kom igång med TmaxSoft OpenFrame på Azure

Ta dina befintliga stordator till gångar och flytta dem till Microsoft Azure med TmaxSoft OpenFrame. Den här lösningen för att använda den här populära lösningen skapar en emulerings miljö på Azure, så att du snabbt kan migrera program. Ingen omformatering krävs.

## <a name="openframe-rehosting-environment"></a>OpenFrame revärding-miljö

Konfigurera en miljö med öppna ramar i Azure för utveckling, demonstrationer, testning eller produktions arbets belastningar. Som följande bild visas innehåller OpenFrame flera komponenter som skapar stordator-emuleringsklienten på Azure. Exempel: OpenFrame onlinetjänster ersätter stordator mellanprogram, till exempel IBM Customer information Control system (CICS). OpenFrame batch med dess TJES-komponent ersätter IBM-stordatorens del system (JES). 

![Process för OpenFrame-omvärdering](media/openframe-01.png)

> [!NOTE]
> Om du vill köra öppna ram miljön på Azure måste du ha en giltig produkt licens eller utvärderings licens från TmaxSoft.

## <a name="openframe-components"></a>Openlist-komponenter

Följande komponenter är en del av OpenFrame-miljön på Azure:

- **Migreringsverktyg** , inklusive OFMiner, en lösning som analyserar stordatorernas till gångar och sedan migrerar dem till Azure.
- **Kompilatorer**, inklusive OFCOBOL, en kompilator som tolkar STORDATOR programmets COBOL-program; OFPLI, som tolkar stordatorens PL/I-program; och OFASM, en-kompilator som tolkar stordator programmets sammansatta program.
- **Klient dels** komponenter, inklusive Java Enterprise User Solution (JEUS), en webb program server som är certifierad för Java Enterprise Edition 6. OFGW och komponenten OpenFrame gateway som tillhandahåller en 3270-lyssnare.
- **Program** miljö. OpenFrame Base är mellanprogram som hanterar hela systemet. Den här typen av ram Server typ C (OSC) ersätter stordatorens mellan-och IBM-CICS.
- **Relations databas**, till exempel Tibero (visas), Oracle Database, Microsoft SQL Server, IBM DB2 eller MySQL. OpenFrame-programmen använder Open Database Connectivity-protokollet (ODBC) för att kommunicera med databasen.
- **Säkerhet** via TACF, en service-modul som styr användar åtkomst till system och resurser. 
- **OFManager** är en lösning som ger openframes drift-och hanterings funktioner i webb miljön.

![Arkitektur för OpenFrame](media/openframe-02.png)

## <a name="next-steps"></a>Nästa steg

- [Installera TmaxSoft OpenFrame på Azure](./install-openframe-azure.md)
