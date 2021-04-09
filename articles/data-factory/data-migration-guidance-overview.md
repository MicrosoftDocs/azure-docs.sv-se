---
title: Migrera data från data Lake och informations lager till Azure
description: Använd Azure Data Factory för att migrera data från data Lake och data lagret till Azure.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/30/2019
ms.openlocfilehash: 4e7421cb8ea4b0c14e4f4b59a688cdb1afe7d462
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100367715"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Använd Azure Data Factory för att migrera data från data Lake eller data lager till Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Om du vill migrera data Lake-eller ANALYSLÖSNINGAR-data (Enterprise Data Warehouse) till Microsoft Azure bör du överväga att använda Azure Data Factory. Azure Data Factory lämpar sig väl för följande scenarier:

- Migrering av stor data arbets belastning från Amazon Simple Storage Service (Amazon S3) eller en lokal Hadoop Distributed File System (HDFS) till Azure
- ANALYSLÖSNINGAR-migrering från Oracle Exadata, Netezza, Teradata eller Amazon RedShift till Azure

Azure Data Factory kan flytta petabyte (PB) data för data Lake migration och många terabyte (TB) data för migrering av data lager.

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Varför Azure Data Factory kan användas för datamigrering

- Azure Data Factory kan enkelt skala upp mängden bearbetnings kraft för att flytta data på ett Server lös sätt med hög prestanda, återhämtning och skalbarhet. Och du betalar bara för det du använder. Observera också följande: 
  - Azure Data Factory har inga begränsningar för data volym eller antalet filer.
  - Azure Data Factory kan använda din nätverks-och lagrings bandbredd fullt ut för att uppnå den högsta volymen av data förflyttnings flödet i din miljö.
  - Azure Data Factory använder en metod för att betala per användning, så att du bara betalar för den tid som du faktiskt använder för att köra datamigreringen till Azure.  
- Azure Data Factory kan utföra historisk och schemalagda inläsningar både vid en och samma tidpunkt.
- Azure Data Factory använder Azure integration Runtime (IR) för att flytta data mellan offentligt tillgängliga data Lake-och lager slut punkter. Den kan också använda IR med egen värd för att flytta data för data Lake-och lager slut punkter i Azure Virtual Network (VNet) eller bakom en brand vägg.
- Azure Data Factory har säkerhet i företags klass: du kan använda Windows Installer (MSI) eller tjänst identitet för skyddad tjänst-till-tjänst-integrering eller använda Azure Key Vault för hantering av autentiseringsuppgifter.
- Azure Data Factory ger en kod fri redigerings upplevelse och en omfattande inbyggd övervaknings instrument panel.  

## <a name="online-vs-offline-data-migration"></a>Online vs. offline-datamigrering

Azure Data Factory är ett standard verktyg för datamigrering för att överföra data över ett nätverk (Internet, ER eller VPN). Med datamigrering offline levererar användare fysiskt data överförings enheter från organisationen till ett Azure-datacenter.  

Det finns tre viktiga överväganden när du väljer mellan en metod för online-och offline-migrering:  

- Storlek på data som ska migreras
- Nätverksbandbredd
- Migrera fönster

Anta till exempel att du planerar att använda Azure Data Factory för att slutföra datamigreringen inom två veckor ( *fönstret migrering*). Lägg märke till den rosa/blå urklipps linjen i följande tabell. Den lägsta rosa cellen för en specifik kolumn visar data storlek/nätverks bandbredds länkning vars migreringsjobb närmar sig, men mindre än två veckor. (Alla storleks-och bandbredds par i en blå cell har ett online-Migreringsverktyg på mer än två veckor.) 

![Online vs. offline ](media/data-migration-guidance-overview/online-offline.png) den här tabellen hjälper dig att avgöra om du kan uppfylla det avsedda flyttnings fönstret genom en online-migrering (Azure Data Factory) baserat på storleken på dina data och din tillgängliga nätverks bandbredd. Om fönstret online-migrering är mer än två veckor ska du använda offline-migrering.

> [!NOTE]
> Med hjälp av online-migrering kan du uppnå både historiska data inläsningar och stegvisa flöden från slut punkt till slut punkt via ett enda verktyg.  Med den här metoden kan dina data vara synkroniserade mellan det befintliga arkivet och den nya butiken i hela migreringstabellen. Det innebär att du kan återskapa din ETL-logik på den nya butiken med uppdaterade data.


## <a name="next-steps"></a>Nästa steg

- [Migrera data från AWS S3 till Azure](data-migration-guidance-s3-azure-storage.md)
- [Migrera data från ett lokalt Hadoop-kluster till Azure](data-migration-guidance-hdfs-azure-storage.md)
- [Migrera data från lokal Netezza-server till Azure](data-migration-guidance-netezza-azure-sqldw.md)
