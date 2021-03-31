---
title: Felsöka biblioteks installations fel
description: I den här självstudien får du en översikt över hur du felsöker biblioteks installations fel.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: conceptual
ms.date: 01/04/2021
ms.openlocfilehash: 57e9d0c584600a8fac90499d72cfac1620052603
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101694928"
---
# <a name="troubleshoot-library-installation-errors"></a>Felsöka biblioteks installations fel 
Om du vill göra tredje part eller lokalt skapad kod tillgänglig för dina program, kan du installera ett bibliotek på någon av dina Server lös Apache Spark pooler. De paket som anges i requirements.txt-filen laddas ned från PyPi vid tidpunkten för poolen startades. Den här krav filen används varje gång en spark-instans skapas från den Spark-poolen. När ett bibliotek har installerats för en spark-pool är det tillgängligt för alla sessioner som använder samma pool. 

I vissa fall kanske du upptäcker att ett bibliotek inte visas i Apache Spark-poolen. Det här fallet inträffar ofta när det finns ett fel i de angivna requirements.txt eller angivna biblioteken. När ett fel uppstår i biblioteks installationen återgår Apache Spark poolen tillbaka till bibliotek som anges i bas körningen för Synapse.

Målet med det här dokumentet är att ge vanliga problem och hjälpa dig att felsöka biblioteks installations fel.

## <a name="force-update-your-apache-spark-pool"></a>Framtvinga uppdatering av Apache Spark-poolen
När du uppdaterar biblioteken i Apache Spark-poolen hämtas dessa ändringar när poolen har startats om. Om du har aktiva jobb fortsätter de här jobben att köras på den ursprungliga versionen av Spark-poolen.

Du kan tvinga ändringarna att tillämpas genom att välja alternativet för att **tvinga nya inställningar**. Den här inställningen kommer att avsluta alla aktuella sessioner för den valda Spark-poolen. När sessionerna har slutförts måste du vänta tills poolen startats om. 

![Lägg till Python-bibliotek](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Lägg till Python-bibliotek")

## <a name="track-installation-progress"></a>Spåra installations förlopp
Ett system reserverat Spark-jobb startas varje gång en pool uppdateras med en ny uppsättning bibliotek. Detta Spark-jobb hjälper dig att övervaka status för biblioteks installationen. Om installationen Miss lyckas på grund av biblioteks konflikter eller andra problem, kommer Spark-poolen att återgå till sitt tidigare eller standard tillstånd. 

Dessutom kan användarna också kontrol lera installations loggarna för att identifiera beroende konflikter eller se vilka bibliotek som installerades under uppdateringen av poolen.

Så här visar du loggarna:
1. Navigera till listan Spark-program på fliken **övervaka** . 
2. Välj det system Spark-programjobb som motsvarar din uppdatering av poolen. Dessa system jobb körs under rubriken *SystemReservedJob-LibraryManagement* .
   ![Skärm bild som fokuserar på system reserverat biblioteks jobb.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Visa system biblioteks jobb")
3. Växla för att visa **driv rutins** -och **STDOUT** -loggar. 
4. I resultaten ser du de loggar som är relaterade till installationen av dina paket.
    ![Skärm bild som visar system reserverade biblioteks jobb resultat.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Visa jobb förlopp för system bibliotek")

## <a name="validate-your-permissions"></a>Verifiera dina behörigheter
Om du vill installera och uppdatera bibliotek måste du ha behörighet för **Storage BLOB-data deltagare** eller **lagrings-BLOB-data** på det primära Azure Data Lake Storage Gen2 lagrings konto som är länkat till Azure Synapse Analytics-arbetsytan.

Du kan kontrol lera att du har dessa behörigheter genom att köra följande kod:

```python
from pyspark.sql.types import StructType,StructField, StringType, IntegerType
data2 = [("James","Smith","Joe","4355","M",3000),
    ("Michael","Rose","Edward","40288","F",4000)
  ]

schema = StructType([ \
    StructField("firstname",StringType(),True), \
    StructField("middlename",StringType(),True), \
    StructField("lastname",StringType(),True), \
    StructField("id", StringType(), True), \
    StructField("gender", StringType(), True), \
    StructField("salary", IntegerType(), True) \
  ])
 
df = spark.createDataFrame(data=data2,schema=schema)

df.write.csv("abfss://<<ENTER NAME OF FILE SYSTEM>>@<<ENTER NAME OF PRIMARY STORAGE ACCOUNT>>.dfs.core.windows.net/validate_permissions.csv")

```
Om du får ett fel, saknar du förmodligen de behörigheter som krävs. Information om hur du hämtar de behörigheter som krävs finns i det här dokumentet: [tilldela Storage BLOB data-deltagare eller behörigheter för lagrings-BLOB-data](../../storage/common/storage-auth-aad-rbac-portal.md#assign-an-azure-built-in-role).

Dessutom, om du kör en pipeline, måste MSI-filen för arbets ytan ha behörigheten lagrings-BLOB-data ägare eller lagrings-BLOB-data deltagare. Om du vill veta hur du beviljar din arbets ytans identitet kan du gå [till: bevilja behörigheter till hanterad identitet för arbets ytan](../security/how-to-grant-workspace-managed-identity-permissions.md).

## <a name="check-the-environment-configuration-file"></a>Kontrol lera miljö konfigurations filen
En miljö konfigurations fil kan användas för att uppgradera Conda-miljön. De här acceptabla fil formaten för hantering av python-pooler visas [här](./apache-spark-manage-python-packages.md).

Det är viktigt att Observera följande begränsningar:
   -  Innehållet i krav filen får inte innehålla extra tomma rader eller tecken. 
   -  [Synapse-körningsmiljön](apache-spark-version-support.md) innehåller en uppsättning bibliotek som är förinstallerade på varje server lös Apache Spark-pool. Det går inte att ta bort eller avinstallera paket som är förinstallerade på bas körningen.
   -  Det finns inte stöd för att ändra PySpark-, python-, Scala-/Java-, .NET-eller Spark-versionen.
   -  Python-sessionsbaserade bibliotek accepterar bara filer med ett YML-tillägg.

## <a name="validate-wheel-files"></a>Validera Wheel-filer
Synapse-serverns Apache Spark pooler baseras på Linux-distributionen. När du laddar ned och installerar Wheel-filer direkt från PyPI måste du välja den version som är byggd på Linux och köras på samma python-version som Spark-poolen.

>[!IMPORTANT]
>Anpassade paket kan läggas till eller ändras mellan sessioner. Du måste dock vänta tills poolen och sessionen har startats om för att se det uppdaterade paketet.

## <a name="check-for-dependency-conflicts"></a>Sök efter beroende konflikter
 I allmänhet kan python-beroende upplösning vara svårt att hantera. För att under lätta fel sökning av beroende konflikter lokalt kan du skapa en egen virtuell miljö baserat på Synapse-körningsmiljön och verifiera dina ändringar.

Så här återskapar du miljön och verifierar dina uppdateringar:
 1. [Hämta](https://github.com/Azure-Samples/Synapse/blob/main/Spark/Python/base_environment.yml) mallen för att lokalt återskapa Synapse-körningen. Det kan finnas mindre skillnader mellan mallen och den faktiska Synapse-miljön.
   
 2. Skapa en virtuell miljö med hjälp av [följande instruktioner](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html). I den här miljön kan du skapa en isolerad python-installation med den angivna listan över bibliotek. 
    
    ```
    conda myenv create -f environment.yml
    conda activate myenv
    ```
   
 3. Används ``pip install -r <provide your req.txt file>`` för att uppdatera den virtuella miljön med dina angivna paket. Om installationen resulterar i ett fel, kan det finnas en konflikt mellan vad som är förinstallerat i Synapse-bas körningen och vad som anges i den angivna krav filen. Dessa beroende konflikter måste lösas för att de uppdaterade biblioteken ska kunna hämtas på den server lösa Apache Spark poolen.

>[!IMPORTANT]
>Problem kan arrise när PIP och Conda används tillsammans. När du kombinerar pip och Conda är det bäst att [följa rekommendationerna.](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html#using-pip-in-an-environment)

## <a name="next-steps"></a>Nästa steg
- Visa standard biblioteken: [Apache Spark versions stöd](apache-spark-version-support.md)