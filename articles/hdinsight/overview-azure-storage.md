---
title: Översikt över Azure Storage i HDInsight
description: Översikt över Azure Storage i HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 419b4939c19ee03651a406db44244897e17087f2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865476"
---
# <a name="azure-storage-overview-in-hdinsight"></a>Översikt över Azure Storage i HDInsight

Azure Storage är en robust lagrings lösning för allmänna ändamål som integreras sömlöst med HDInsight. HDInsight kan använda en blobcontainer i Azure Storage som standardfilsystem för klustret. Med ett HDFS-gränssnitt kan den fullständiga uppsättningen komponenter i HDInsight köras direkt på strukturerade eller ostrukturerade data som lagras som blobbar.

Vi rekommenderar att du använder separata lagrings behållare för standard kluster lagringen och dina affärs data. Separationen är att isolera HDInsight-loggar och temporära filer från dina egna affärs data. Vi rekommenderar också att du tar bort standard-BLOB-behållaren, som innehåller program-och system loggar, efter varje användning för att minska lagrings kostnaderna. Se till att hämta loggarna innan du tar bort containern.

Om du väljer att skydda ditt lagrings konto med **brand väggar och begränsningar för virtuella nätverk** på **valda nätverk** måste du aktivera undantaget **Tillåt betrodda Microsoft-tjänster...**. Undantaget är så att HDInsight kan komma åt ditt lagrings konto.

## <a name="hdinsight-storage-architecture"></a>Lagringsarkitekturen i HDInsight

Följande diagram innehåller en översikt över HDInsight-arkitekturen för Azure Storage:

:::image type="content" source="./media/overview-azure-storage/storage-architecture.png" alt-text="HDInsight Storage arkitektur" border="false":::

HDInsight ger tillgång till det distribuerade filsystemet som är lokalt anslutet till beräkningsnoderna. Detta filsystem kan nås med hjälp av den fullständigt kvalificerade URI-strängen, till exempel:

`hdfs://<namenodehost>/<path>`

Via HDInsight kan du också komma åt data i Azure Storage. Syntaxen ser ut så här:

`wasb://<containername>@<accountname>.blob.core.windows.net/<path>`

Tänk på följande principer när du använder ett Azure Storage-konto med HDInsight-kluster:

* **Containrar på de lagringskonton som är anslutna till ett kluster:** Eftersom kontonamnet och nyckeln associeras med klustret när det skapas har du full tillgång till blobarna i dessa containrar.

* **Offentliga behållare eller offentliga blobbar i lagrings konton som inte är anslutna till ett kluster:** Du har skrivskyddad behörighet till Blobbarna i behållarna.
  
  > [!NOTE]
  > Med offentliga behållare kan du hämta en lista över alla blobar som är tillgängliga i behållaren och hämta metadata för behållare. Du kan endast komma åt offentliga blobar om du känner till den exakta webbadressen. Mer information finns i [Hantera anonym Läs behörighet till behållare och blobbar](../storage/blobs/anonymous-read-access-configure.md).

* **Privata behållare i lagrings konton som inte är anslutna till något kluster:** Du har inte åtkomst till Blobbarna i behållarna om du inte definierar lagrings kontot när du skickar WebHCat-jobben.

De lagringskonton som definieras under skapandeprocessen och deras nycklar lagras i %HADOOP_HOME%/conf/core-site.xml i klusternoderna. Som standard använder HDInsight de lagrings konton som definierats i core-site.xml-filen. Du kan ändra den här inställningen med hjälp av [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Flera WebHCat-jobb, inklusive Apache Hive. Och MapReduce, Apache Hadoop strömning och Apache gris, bär en beskrivning av lagrings konton och metadata. (Den här aspekten är för närvarande sann för gris med lagrings konton, men inte för metadata.) Mer information finns i [använda ett HDInsight-kluster med alternativa lagrings konton och metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Blobar kan användas för strukturerade och ostrukturerade data. BLOB-behållare lagrar data som nyckel/värde-par och har ingen katalogpartition. Nyckel namnet kan dock innehålla ett snedstreck (/) som gör att det visas som om en fil lagras i en katalog struktur. En blobs nyckel kan till exempel vara `input/log1.txt` . `input`Det finns ingen faktisk katalog, men på grund av snedstrecket i nyckel namnet ser nyckeln ut som en fil Sök väg.

## <a name="benefits-of-azure-storage"></a>Fördelar med Azure Storage

Beräknings kluster och lagrings resurser som inte samplaceras har underförstådda prestanda kostnader. Dessa kostnader begränsas av hur beräknings klustren skapas nära lagrings konto resurserna i Azure-regionen. I den här regionen kan Compute-noderna effektivt komma åt data över det snabba nätverket i Azure Storage.

När du lagrar data i Azure Storage i stället för HDFS får du flera fördelar:

* **Återanvändning och delning av data:** Data i HDFS lagras inuti beräkningsklustren. Endast de program som har åtkomst till beräkningsklustren kan använda dessa data med hjälp av HDFS-API:er. Data i Azure Storage, med kontrast, kan nås via antingen HDFS-API: erna eller REST-API: er för Blob Storage. På grund av den här ordningen kan en större uppsättning program (inklusive andra HDInsight-kluster) och verktyg användas för att skapa och använda data.

* **Data arkivering:** När data lagras i Azure Storage kan HDInsight-kluster som används för beräkning vara säkert borttagna utan att förlora användar data.

* **Kostnad för data lagring:** Att lagra data i DFS för lång sikt är dyrare än att lagra data i Azure Storage. Eftersom kostnaden för ett beräknings kluster är högre än kostnaden för Azure Storage. Eftersom data inte behöver läsas in på nytt för varje beräknings kluster skapas även data inläsnings kostnader.

* **Elastisk utskalning:** Även om HDFS ger dig ett utskalat filsystem bestäms skalan av antalet noder som du skapar för klustret. Att ändra skalan kan vara mer komplicerat än de elastiska skalnings funktioner som du får automatiskt i Azure Storage.

* **Geo-replikering:** Din Azure Storage kan vara geo-replikerad. Även om geo-replikering ger dig geografisk återställning och dataredundans, påverkar en redundansväxling till den geo-replikerade platsen prestanda och ytterligare kostnader kan tillkomma. Välj därför försiktigt geo-replikering och endast om värdet för data motiverar den extra kostnaden.

Vissa MapReduce-jobb och-paket kan skapa mellanliggande resultat som du inte vill lagra i Azure Storage. I så fall kan du välja att lagra data i den lokala HDFS. HDInsight använder DFS för flera av dessa mellanliggande resultat i Hive-jobb och andra processer.

> [!NOTE]  
> De flesta HDFS-kommandon (till exempel,, `ls` `copyFromLocal` och `mkdir` ) fungerar som förväntat i Azure Storage. Endast de kommandon som är specifika för den inbyggda HDFS-implementeringen (som kallas DFS), till exempel `fschk` och `dfsadmin` , visar olika beteenden i Azure Storage.

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introduktion till Azure Storage](../storage/common/storage-introduction.md)
* [Azure Data Lake Storage Gen1-översikt](./overview-data-lake-storage-gen1.md)
