---
title: Vägledning för haveri beredskap för AVERT vFXT för Azure
description: Skydda data i aver vFXT för Azure från oavsiktlig borttagning eller avbrott
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rohogue
ms.openlocfilehash: 5cc4678b082aa5a4a3f90518ff8fac448f414f1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92342254"
---
# <a name="disaster-recovery-guidance-for-avere-vfxt-for-azure"></a>Vägledning för haveri beredskap för AVERT vFXT för Azure

Den här artikeln beskriver strategier för att skydda ditt AVERT vFXT för Azure-arbetsflöde och ger vägledning för att säkerhetskopiera data så att du kan återställa från haverier eller avbrott.

Aver vFXT for Azure lagrar tillfälligt data i cacheminnet. Data lagras långsiktigt i Server dels lagrings system – lokala maskin varu system, Azure Blob Storage-behållare eller båda.

Överväg följande fyra områden för att skydda mot avbrott och möjliga data förluster:

* Skydd mot nedtid om ett AVERT vFXT för Azure-system blir otillgängligt
* Skydda data i klustrets cacheminne
* Skydda data i backend-maskinvara på Server Sidan
* Skydda data i Server dels Azure Blob-moln lagring

Varje AVERT vFXT för Azure-kunder måste skapa en egen omfattande katastrof återställnings plan som innehåller planer för dessa objekt. Du kan också bygga återhämtning till program som du använder med vFXT-klustret. Läs länkarna i [Nästa steg](#next-steps) för att få hjälp.

## <a name="protect-against-downtime"></a>Skydda mot drift stopp

Redundans är inbyggt i AVERT vFXT för Azure-produkten:

* Klustret har hög tillgänglighet och enskilda klusternoder kan redundansväxla med minimalt avbrott.
* Data som har ändrats i cachen skrivs regelbundet till Server dels Core-(maskinvaru-NAS eller Azure-Blob) för långsiktig lagring.

Varje AVERT vFXT för Azure-kluster måste finnas i en enda tillgänglighets zon, men du kan använda redundanta kluster som finns i olika zoner eller i olika regioner för att snabbt ge åtkomst i händelse av ett regionalt avbrott.

Du kan också placera lagrings behållare i flera regioner om du är bekymrad om att förlora åtkomsten till data. Tänk dock på att transaktioner mellan regioner har högre latens och högre kostnad än transaktioner som ligger inom en region.

## <a name="protect-data-in-the-cluster-cache"></a>Skydda data i klustrets cacheminne

Cachelagrade data skrivs alltid till core-filer före en vanlig avstängning, men i en okontrollerad avstängning kan ändrade data i cacheminnet gå förlorade.

Om du använder klustret för att optimera fil läsningar finns det inga ändringar att förlora. Om du också använder klustret för att cachelagra fil ändringar (skrivningar) bör du överväga om du vill justera Core-filvägarnas [cache-principer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html)<!-- link to legacy doc --> för att anpassa hur ofta data skrivs till långsiktig lagring.

I allmänhet bör din återställnings plan fokusera på att säkerhetskopiera backend-lagrings systemen, som innehåller mer data och är vanligt vis viktiga för att återupprätta arbets flödet efter ett haveri.

## <a name="protect-data-in-nas-core-filers"></a>Skydda data i NAS Core-Filskydd

Använd godkända metoder för att skydda data som lagras i en lokal NAS-maskinvara, inklusive ögonblicks bilder och fullständiga säkerhets kopior som rekommenderas av NAS-providern. Haveri beredskap för dessa Core-ligger utanför den här artikelns omfång.

## <a name="protect-data-in-azure-blob-storage"></a>Skydda data i Azure Blob Storage

Aver vFXT for Azure använder lokalt redundant lagring (LRS) för Azure Blob Core-Filskydd. Det innebär att data i dina BLOB-behållare kopieras automatiskt för skydd mot tillfälliga maskin varu problem i ett Data Center.

Det här avsnittet innehåller tips om hur du ytterligare skyddar dina data i Blob Storage från ovanliga avbrott i regioner eller oavsiktliga borttagningar.

Metod tips för att skydda data i Azure Blob Storage är:

* Kopiera dina viktiga data till ett annat lagrings konto i en annan region ofta (så ofta som det bestäms av din katastrof återställnings plan).
* Kontrol lera åtkomsten till data på alla mål system för att förhindra oavsiktlig borttagning eller skada. Överväg att använda [resurs lås](../azure-resource-manager/management/lock-resources.md) på data lagring.
* Aktivera funktionen aver vFXT för [ögonblicks bilder](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_snapshot_policies.html>) av Azure-molnet för dina BLOB core-filer.

### <a name="copy-avere-vfxt-core-filer-data-to-a-backup-account"></a>Kopiera aver vFXT core-filer till ett säkerhets kopierings konto

Följ dessa steg om du vill upprätta en säkerhets kopia av data i ett annat konto.

1. Om det behövs kan du generera en ny krypterings nyckel och lagra den på ett säkert sätt utanför de berörda systemen.

   Om dina data har krypterats av AVERT vFXT för Azure-kluster, bör du skapa en ny krypterings nyckel innan du kopierar data till ett annat lagrings konto. Lagra nyckeln och lösen ordet säkert i en säker funktion som inte påverkas av ett regionalt fel.

   Du måste ange den här nyckeln när du lägger till behållaren i ett kluster, även om du lägger till den i det ursprungliga klustret igen.

   Läs [moln krypterings inställningar](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_encryption_settings.html>)<!-- link to legacy doc site --> för detaljerad information.

   Om din behållare använder Azures inbyggda kryptering, kan du hoppa över det här steget.

1. Ta bort kärn filen från systemet. Detta tvingar klustret att skriva alla ändrade data till Server delens lagrings plats.

   Även om du kommer att behöva lägga till kärn filen efter säkerhets kopieringen är det bästa sättet att garantera att alla data skrivs fullständigt till Server delen. (Alternativet "pausa" kan ibland lämna kvar ändrade data i cacheminnet.) <!-- xxx true? or just metadata? -->

   Anteckna huvud namn och kopplings information (visas på sidan **namn område** i kontroll panelen) så att du kan replikera den när du lägger till behållaren igen efter säkerhets kopieringen.

   Använd kluster kontroll panelen för att ta bort kärn filer. [Öppna kluster kontroll panelen](avere-vfxt-cluster-gui.md) och välj **Core**-  >  **filer hantera Core**-. Hitta det lagrings system som du vill säkerhetskopiera och Använd knappen **ta bort** för att ta bort det från klustret.

1. Skapa en ny, Tom Blob Storage-behållare i ett annat lagrings konto i en annan region.

1. Använd valfritt användbart kopierings verktyg för att kopiera data från kärn filen till den nya behållaren. Kopian måste replikera data utan ändringar och utan att störa det patentskyddade moln fil system formatet som används av AVERT vFXT för Azure. Azure-baserade verktyg omfattar [AzCopy](../storage/common/storage-use-azcopy-v10.md), [Azure PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)och [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

1. När du har kopierat data till behållaren för säkerhets kopiering lägger du till den ursprungliga behållaren tillbaka till klustret enligt beskrivningen i [Konfigurera lagring](avere-vfxt-add-storage.md).

   * Använd samma namn och kopplings information för kärnor så att klient arbets flöden inte behöver ändras.
   * Ange värdet för **Bucket-innehåll** till det befintliga data alternativet.
   * Om behållaren har krypterats av klustret måste du ange den aktuella krypterings nyckeln för innehållet. (Det här är den nyckel som du uppdaterade i steg ett.)

För säkerhets kopieringar efter den första måste du inte skapa en ny lagrings behållare. Men du kan skapa en ny krypterings nyckel varje gång du gör en säkerhets kopia för att kontrol lera att du har den aktuella nyckeln lagrad på en plats som du kommer ihåg.

### <a name="access-a-backup-data-source-during-an-outage"></a>Få åtkomst till en säkerhets kopie rad data källa under ett avbrott

Följ den här processen om du vill komma åt behållaren för säkerhets kopiering från ett AVERT vFXT för Azure-kluster:

1. Om det behövs kan du skapa ett nytt AVERT vFXT för Azure-kluster i en region som inte påverkas.

   > [!TIP]
   > När du skapar en aver-vFXT för Azure-kluster kan du spara en kopia av dess mall och parametrar. Om du sparar den här informationen när du skapar ditt primära kluster kan du använda det för att skapa ett ersättnings kluster med samma egenskaper. På sidan [Sammanfattning](avere-vfxt-deploy.md#validation-and-purchase) klickar du på länken **Hämta mall och parametrar** . Spara informationen i en fil innan du skapar klustret.

1. Lägg till en ny Cloud core-filer som pekar på den duplicerade BLOB-behållaren.

   Se till att ange att mål behållaren redan innehåller data i inställningen **Bucket-innehåll** i guiden för att skapa kärn filer. (Systemet varnar dig om du av misstag lämnar den här inställningen **Tom**.)  <!-- you can't add a populated volume at cluster creation time via template, only create a fresh one -->

1. Vid behov kan du uppdatera klienter så att de monterar det nya klustret eller nya kärnor i stället för originalet. (Om du lägger till ersättnings core-filer med samma namn och knutna sökväg som den ursprungliga behållaren behöver du inte uppdatera klient processerna om du inte behöver montera det nya klustret på en ny IP-adress.)

## <a name="next-steps"></a>Nästa steg

* Läs [kluster justeringen](avere-vfxt-tuning.md)om du vill ha mer information om att anpassa inställningar för AVERT VFXT för Azure.
* Lär dig mer om haveri beredskap och att skapa elastiska program i Azure:

  * [Azure-återhämtning, tekniska riktlinjer](/azure/architecture/framework/resiliency/overview)
  * [Återställa från regionomfattande tjänststörningar](/azure/architecture/resiliency/recovery-loss-azure-region)
  * [Haveriberedskap och hög tillgänglighet för Azure-program](/azure/architecture/framework/resiliency/backup-and-recovery)
  <!-- can't find these in the source tree to use relative links -->