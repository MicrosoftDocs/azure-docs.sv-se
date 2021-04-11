---
title: Azure Data Lake Storage Gen2 hierarkiskt namn område
description: Beskriver konceptet ett hierarkiskt namn område för Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: de7f62dd4d4205e819278e6dc90d905e825580f2
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220512"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Azure Data Lake Storage Gen2 hierarkiskt namn område

En nyckel mekanism som gör det möjligt för Azure Data Lake Storage Gen2 att tillhandahålla fil system prestanda vid objekt lagrings skala och priser är att lägga till ett **hierarkiskt namn område**. På så sätt kan samling av objekt/filer i ett konto ordnas i en hierarki med kataloger och kapslade under kataloger på samma sätt som fil systemet på datorn är ordnat. När ett hierarkiskt namn område är aktiverat kan ett lagrings konto tillhandahålla skalbarhet och kostnads effektivitet för objekt lagring, med semantiska fil system som är välkända för analys motorer och ramverk.

## <a name="the-benefits-of-a-hierarchical-namespace"></a>Fördelarna med ett hierarkiskt namn område

Följande fördelar är kopplade till fil system som implementerar ett hierarkiskt namn område över BLOB-data:

- **Atomic Directory-manipulering:** Objekt lager uppskattar en katalog-hierarki genom att använda en konvention för att bädda in snedstreck (/) i objekt namnet för att ange Sök vägs segment. Även om den här konventionen används för att ordna objekt ger konventionen ingen hjälp för åtgärder som att flytta, byta namn på eller ta bort kataloger. Utan riktiga kataloger måste program bearbeta potentiellt miljon tals enskilda blobbar för att kunna uppnå aktiviteter på katalog nivå. Ett hierarkiskt namn område bearbetar däremot dessa uppgifter genom att uppdatera en enskild post (överordnad katalog).

    Den här dramatiska optimeringen är särskilt viktig för många Big data Analytics-ramverk. Verktyg som Hive, Spark osv. skriver ofta utdata till tillfälliga platser och byter sedan namn på platsen i slutet av jobbet. Utan ett hierarkiskt namn område kan detta namn ofta ta längre tid än själva analys processen. Lägre jobb fördröjning motsvarar lägre totala ägande kostnader (TCO) för analyser av arbets belastningar.

- **Välbekant gränssnitts stil:** Fil systemen kan förstås av både utvecklare och användare. Du behöver inte lära dig ett nytt lagrings paradigm när du flyttar till molnet eftersom fil system gränssnittet som exponeras av Data Lake Storage Gen2 är samma paradigm som används av datorer, stora och små.

En av orsakerna till att objektet inte tidigare har stöd för ett hierarkiskt namn område är att ett hierarkiskt namn område begränsar skala. Men den hierarkiska namn rymden för Data Lake Storage Gen2 skalas linjärt och försämrar inte data kapaciteten eller prestandan.

## <a name="deciding-whether-to-enable-a-hierarchical-namespace"></a>Bestämma om du vill aktivera ett hierarkiskt namn område

När du har aktiverat ett hierarkiskt namn område på ditt konto kan du inte återställa det till ett plant namn område. Därför bör du överväga om det är klokt att aktivera ett hierarkiskt namn område baserat på objekt lagringens arbets belastningar.

Vissa arbets belastningar kanske inte får någon förmån genom att aktivera ett hierarkiskt namn område. Exempel på detta är säkerhets kopior, avbildnings lagring och andra program där objekt organisation lagras separat från själva objekten (till exempel: i en separat databas). 

Även om stöd för Blob Storage-funktioner och Azure-tjänstens eko system fortsätter att växa, finns det fortfarande vissa funktioner och Azure-tjänster som ännu inte stöds i konton som har ett hierarkiskt namn område. Se [kända problem](data-lake-storage-known-issues.md). 

I allmänhet rekommenderar vi att du aktiverar ett hierarkiskt namn område för lagrings arbets belastningar som är utformade för fil system som manipulerar kataloger. Detta omfattar alla arbets belastningar som främst används för analys bearbetning. Data uppsättningar som kräver en hög grad av organisation kommer också att ha nytta av att aktivera ett hierarkiskt namn område.

Orsakerna till att aktivera ett hierarkiskt namn område bestäms av en TCO-analys. I allmänhet kan förbättringar av arbets belastnings fördröjningen på grund av lagrings acceleration kräva beräknings resurser för kortare tid. Svars tiden för många arbets belastningar kan förbättras på grund av atomisk katalog manipulation som aktive ras av ett hierarkiskt namn område. I många arbets belastningar representerar beräknings resursen > 85% av den totala kostnaden och så vidare är en liten minskning av arbets belastnings fördröjningen lika med en betydande mängd TCO-besparingar. Även i de fall där aktivering av ett hierarkiskt namn område ökar lagrings kostnaderna sänks ägande kostnaden fortfarande på grund av sänkta beräknings kostnader.

Information om hur du analyserar skillnader i priser för data lagring, transaktions priser och reservation av lagrings kapacitet mellan konton som har ett hierarkiskt hierarkiskt namn område jämfört med ett hierarkiskt namn område finns [Azure Data Lake Storage Gen2 prissättning](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="enabling-a-hierarchical-namespace"></a>Aktivera ett hierarkiskt namn område

Du kan inte aktivera ett hierarkiskt namn område för ett befintligt lagrings konto. Du kan bara aktivera ett hierarkiskt namn område när du skapar ett lagrings konto. Vägledning finns i [skapa ett lagrings konto som ska användas med Azure Data Lake Storage Gen2](create-data-lake-storage-account.md).

## <a name="next-steps"></a>Nästa steg

- [Skapa ett lagrings konto](../common/storage-account-create.md)