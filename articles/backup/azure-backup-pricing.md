---
title: Priser för Azure Backup
description: Lär dig att beräkna kostnaderna för budgetering av Azure Backup-prissättningen.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 03ec0076d3089562ddaace6db413fb3f1ba949a6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88654539"
---
# <a name="azure-backup-pricing"></a>Priser för Azure Backup

Läs mer om prissättningen av Azure Backup på [sidan Azure Backup-prissättning](https://azure.microsoft.com/pricing/details/backup/).

## <a name="download-detailed-estimates-for-azure-backup-pricing"></a>Ladda ned detaljerade beräkningar för Azure Backup-prissättningen

Om du vill beräkna dina kostnader för budgetering eller kostnadsjämförelse, kan du ladda ned den detaljerade [Azure Backup-prisberäknaren](https://aka.ms/AzureBackupCostEstimates).  

### <a name="what-does-the-estimator-contain"></a>Vad innehåller prisberäknaren?

På bladet för Azure Backup-prisberäknaren finns ett alternativ där du kan beräkna alla arbetsbelastningar som du kan komma att säkerhetskopiera med Azure Backup. Arbetsbelastningarna är:

- Virtuella Azure-datorer
- Lokala servrar
- SQL på virtuella Azure-datorer
- SAP HANA i virtuella Azure-datorer
- Azure-filresurser

## <a name="estimate-costs-for-backing-up-azure-vms-or-on-premises-servers"></a>Beräkna kostnader för säkerhetskopiering av virtuella Azure-datorer eller lokala servrar

Om du ska beräkna kostnaderna för att säkerhetskopiera virtuella Azure-datorer eller lokala servrar med Azure Backup, behöver du följande parametrar:

- Storleken på de virtuella datorer eller lokala servrar som du ska säkerhetskopiera
  - Ange ”använd storlek” för de diskar eller servrar som ska säkerhetskopieras

- Antal servrar med den storleken

- Vilken dataomsättning förväntas på dessa servrar?<br>
  Omsättningen syftar på mängden data som ska ändras. Om du till exempel har en virtuell dator med 200 GB data som ska säkerhetskopieras och 10 GB av den ändras varje dag, är den dagliga omsättningen 5 %.

  - Högre omsättning innebär att du säkerhetskopierar mer data

  - Välj **Låg** eller **Måttlig** för filservrar och **Hög** om du kör databaser

  - Om du känner till **omsättningens %** kan du använda alternativet **Ange egen %**

- Välj säkerhetskopieringspolicy

  - Hur länge förväntar du dig att behålla ”dagliga” säkerhetskopior? (i dagar)

  - Hur länge förväntar du dig att behålla ”veckovisa” säkerhetskopior? (i veckor)

  - Hur länge förväntar du dig att behålla ”månatliga” säkerhetskopior? (i månader)

  - Hur länge förväntar du dig att behålla ”årliga” säkerhetskopior? (i år)

  - Hur länge förväntar du dig att behålla ”Ögonblicksbilder för omedelbar återställning”? (1–5 dagar)

    - Med det här alternativet kan du återställa från sju dagar bakåt på ett snabbt sätt med hjälp av ögonblicksbilder som lagras på diskar.

- **Valfritt** – Selektiv säkerhetskopiering av disk

  - Om du använder alternativet **Selektiv säkerhetskopiering av disk** när du säkerhetskopierar virtuella Azure-datorer, väljer du alternativet **Exkludera disk** och anger procentandelen diskar som exkluderas från säkerhetskopian i storlek. Om du till exempel har en virtuell dator som är ansluten till tre diskar och 200 GB används på varje disk, anger du 66,7 % om du vill undanta två av dem från säkerhetskopieringen.

- **Valfritt** – Redundans i lagringsenhet för säkerhetskopior

  - Detta anger redundansen för det lagringskonto där dina säkerhetskopieringsdata hamnar. Vi rekommenderar att du använder **GRS** för att få högsta möjliga tillgänglighet. Eftersom det säkerställer att en kopia av dina säkerhetskopierade data behålls i en annan region, hjälper det dig att uppfylla flera efterlevnadskrav. Ändra redundansen till **LRS** om du säkerhetskopierar utvecklings- eller testmiljöer som inte behöver någon säkerhetskopia på företagsnivå. Välj alternativet **RAGRS** i bladet om du vill förstå kostnaderna när [Återställning mellan regioner](backup-azure-arm-restore-vms.md#cross-region-restore) har aktiverats för dina säkerhetskopior.

- **Valfritt** – Ändra regional prissättning eller tillämpa rabatterade priser

  - Om du vill kontrollera dina beräkningar för en annan region eller rabatterade priser, väljer du **Ja** i alternativet **Testa beräkningar för en annan region?** och anger de priser som du vill köra beräkningarna med.

## <a name="estimate-costs-for-backing-up-sql-servers-in-azure-vms"></a>Beräkna kostnader för att säkerhetskopiera SQL-servrar i virtuella Azure-datorer

Om du vill beräkna kostnaderna för att säkerhetskopiera SQL-servrar som körs på virtuella Azure-datorer med Azure Backup, behöver du följande parametrar:

- Storleken på de SQL-servrar som du ska säkerhetskopiera

- Antal SQL-servrar med ovanstående storlek

- Vilken är den förväntade komprimeringen av SQL-servrarnas säkerhetskopieringsdata?

  - De flesta Azure Backup-kunder märker att säkerhetskopieringsdatan har en komprimering på 80 % jämfört med SQL-serverstorleken när SQL-komprimeringen är **aktiverad**.

  - Om du förväntar dig att se en annan komprimering, anger du siffran i det här fältet

- Vilken är den förväntade storleken på loggsäkerhetskopiorna?

  - %-talet visar den dagliga loggstorleken i % av SQL-serverstorleken

- Vilken daglig dataomsättning förväntas på dessa servrar?

  - Vanligtvis har databaser omsättningen ”Hög”

  - Om du känner till **omsättningens %** kan du använda alternativet **Ange egen %**

- Välj säkerhetskopieringspolicy

  - Typ av säkerhetskopiering

    - Den mest effektiva principen du kan välja är **Dagliga differentiella säkerhetskopior** med fullständiga säkerhetskopieringar varje vecka/månad/år. Azure Backup kan även återställas från differentiella säkerhetskopior med ett klick.

    - Du kan också välja att ha en princip med fullständiga säkerhetskopieringar varje dag/vecka/månad/år. Det här alternativet förbrukar lite mer lagringsutrymme än det första alternativet.

  - Hur länge förväntar du dig att behålla ”logg”-säkerhetskopior? (i dagar) [7–35]

  - Hur länge förväntar du dig att behålla ”dagliga” säkerhetskopior? (i dagar)

  - Hur länge förväntar du dig att behålla ”veckovisa” säkerhetskopior? (i veckor)

  - Hur länge förväntar du dig att behålla ”månatliga” säkerhetskopior? (i månader)

  - Hur länge förväntar du dig att behålla ”årliga” säkerhetskopior? (i år)

- **Valfritt** – Redundans i lagringsenhet för säkerhetskopior

  - Detta anger redundansen för det lagringskonto där dina säkerhetskopieringsdata hamnar. Vi rekommenderar att du använder **GRS** för att få högsta möjliga tillgänglighet. Eftersom det säkerställer att en kopia av dina säkerhetskopierade data behålls i en annan region, hjälper det dig att uppfylla flera efterlevnadskrav. Ändra redundansen till **LRS** om du säkerhetskopierar utvecklings- eller testmiljöer som inte behöver någon säkerhetskopia på företagsnivå.

- **Valfritt** – Ändra regional prissättning eller tillämpa rabatterade priser

  - Om du vill kontrollera dina beräkningar för en annan region eller rabatterade priser, väljer du **Ja** i alternativet **Testa beräkningar för en annan region?** och anger de priser som du vill köra beräkningarna med.

## <a name="estimate-costs-for-backing-up-sap-hana-servers-in-azure-vms"></a>Beräkna kostnaderna för att säkerhetskopiera SAP HANA-servrar i virtuella Azure-datorer

Om du vill beräkna kostnaderna för att säkerhetskopiera SAP HANA-servrar som körs på virtuella Azure-datorer med Azure Backup, behöver du följande parametrar:

- Total storlek för de SAP HANA-databaser som du ska säkerhetskopiera. Detta ska vara summan av den fullständiga säkerhetskopieringsstorleken för varje databas, enligt rapport från SAP HANA.
- Antal SAP HANA-servrar med ovanstående storlek
- Vilken är den förväntade storleken på loggsäkerhetskopiorna?
  
  - %-talet visar genomsnittlig daglig loggstorlek i % av den totala storleken på de SAP HANA-databaser som du säkerhetskopierar på SAP HANA-servern
- Vilken daglig dataomsättning förväntas på dessa servrar?
  - %-talet visar genomsnittlig daglig omsättningsstorlek i % av den totala storleken på de SAP HANA-databaser som du säkerhetskopierar på SAP HANA-servern
  - Vanligtvis har databaser omsättningen ”Hög”
  - Om du känner till **omsättningens %** kan du använda alternativet **Ange egen %**
- Välj säkerhetskopieringspolicy
  - Typ av säkerhetskopiering
    - Den mest effektiva principen du kan välja är **Dagliga differentiella säkerhetskopior** med fullständiga säkerhetskopieringar **varje vecka/månad/år**. Azure Backup kan även återställas från differentiella säkerhetskopior med ett klick.
    - Du kan också välja att ha en princip med fullständiga säkerhetskopieringar **varje dag/vecka/månad/år**. Det här alternativet förbrukar lite mer lagringsutrymme än det första alternativet.
  - Hur länge förväntar du dig att behålla ”logg”-säkerhetskopior? (i dagar) [7–35]
  - Hur länge förväntar du dig att behålla ”dagliga” säkerhetskopior? (i dagar)
  - Hur länge förväntar du dig att behålla ”veckovisa” säkerhetskopior? (i veckor)
  - Hur länge förväntar du dig att behålla ”månatliga” säkerhetskopior? (i månader)
  - Hur länge förväntar du dig att behålla ”årliga” säkerhetskopior? (i år)
- **Valfritt** – Redundans i lagringsenhet för säkerhetskopior
  
  - Detta anger redundansen för det lagringskonto där dina säkerhetskopieringsdata hamnar. Vi rekommenderar att du använder **GRS** för att få högsta möjliga tillgänglighet. Eftersom det säkerställer att en kopia av dina säkerhetskopierade data behålls i en annan region, hjälper det dig att uppfylla flera efterlevnadskrav. Ändra redundansen till **LRS** om du säkerhetskopierar utvecklings- eller testmiljöer som inte behöver någon säkerhetskopia på företagsnivå.
- **Valfritt** – Ändra regional prissättning eller tillämpa rabatterade priser
  
  - Om du vill kontrollera dina beräkningar för en annan region eller rabatterade priser, väljer du **Ja** i alternativet **Testa beräkningar för en annan region?** och anger de priser som du vill köra beräkningarna med.
  
## <a name="estimate-costs-for-backing-up-azure-file-shares"></a>Beräkna kostnader för säkerhetskopiering av Azure-filresurser

Om du vill beräkna kostnaderna för att säkerhetskopiera Azure-filresurser med hjälp av en [ögonblicksbildbaserad säkerhetskopieringslösning](azure-file-share-backup-overview.md) i Azure Backup, behöver du följande parametrar:

- Storlek (**i GB**) på de filresurser som du vill säkerhetskopiera.

- Om du vill säkerhetskopiera filresurser som finns på flera lagringskonton, anger du hur många lagringskonton som innehåller filresurser med ovanstående storlek.

- Förväntad dataomsättning på de filresurser som du vill säkerhetskopiera. <br>Omsättningen syftar på mängden ändrade data och påverkar direkt lagringsstorleken för ögonblicksbilden. Om du till exempel har en filresurs med 200 GB data som ska säkerhetskopieras och 10 GB av den ändras varje dag, är den dagliga omsättningen 5 %.
  - Högre omsättning innebär att mängden data som ändras i filresursinnehållet varje dag är hög, och att den inkrementella ögonblicksbilden (som endast samlar in dataändringar) också blir större.
  - Välj Låg (1 %), Måttlig (3 %) eller Hög (5 %), baserat på egenskaper och användning för din filresurs.
  - Om du känner till exakt **omsättning i %** för filresursen, kan du välja alternativet **Ange egen %** i listrutan. Ange värdena (i %) för omsättning varje dag, vecka, månad och år.

- Typ av lagringskonto (Standard eller Premium) och inställning av lagringsredundans för det lagringskonto som är värd för den säkerhetskopierade filresursen. <br>I den nuvarande säkerhetskopieringslösningen för Azure-filresurser, lagras ögonblicksbilder på samma lagringskonto som den säkerhetskopierade filresursen. Det innebär att lagringskostnaden för ögonblicksbilder faktureras som en del av fakturan för dina Azure-filresurser, baserat på priserna för ögonblicksbilder för kontotypen och redundansinställningen för det lagringskonto som är värd för den säkerhetskopierade filresursen och ögonblicksbilderna.

- Kvarhållning för olika säkerhetskopior
  - Hur länge förväntar du dig att behålla ”dagliga” säkerhetskopior? (i dagar)
  - Hur länge förväntar du dig att behålla ”veckovisa” säkerhetskopior? (i veckor)
  - Hur länge förväntar du dig att behålla ”månatliga” säkerhetskopior? (i månader)
  - Hur länge förväntar du dig att behålla ”årliga” säkerhetskopior? (i år)

  Se [Azure-filresursens supportmatris](azure-file-share-support-matrix.md#retention-limits) för de högsta värden för kvarhållning som stöds i varje kategori.

- **Valfritt** – Ändra regional prissättning eller tillämpa rabatterade priser.
  - Standardvärdena för lagringskostnaden per GB för ögonblicksbilder och kostnaden för skyddade instanser i beräknaren avser regionen USA, östra. Om du vill kontrollera dina beräkningar för en annan region eller rabatterade priser, väljer du **Ja** i alternativet **Testa beräkningar för en annan region?** och anger de priser som du vill köra beräkningarna med.

## <a name="next-steps"></a>Nästa steg

[Vad är Azure Backup-tjänsten?](backup-overview.md)
