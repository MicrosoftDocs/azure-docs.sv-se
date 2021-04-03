---
title: Migrera från lokal HDFS-butiken till Azure Storage med Azure Data Box
description: Migrera data från en lokal HDFS-lagring till Azure Storage (Blob Storage eller Data Lake Storage Gen2) genom att använda en Data Box-enhet enhet.
author: normesta
ms.service: storage
ms.date: 02/14/2019
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: e58137dd680ff9a2be2bd657f0969304b526873f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95913121"
---
# <a name="migrate-from-on-prem-hdfs-store-to-azure-storage-with-azure-data-box"></a>Migrera från lokal HDFS-butiken till Azure Storage med Azure Data Box

Du kan migrera data från en lokal HDFS-lagring av ditt Hadoop-kluster till Azure Storage (Blob Storage eller Data Lake Storage Gen2) genom att använda en Data Box-enhet enhet. Du kan välja mellan Data Box Disk, en 80 TB-Data Box-enhet eller en 770-TB-Data Box Heavy.

Den här artikeln hjälper dig att utföra följande uppgifter:

> [!div class="checklist"]
> * Förbered migreringen av dina data.
> * Kopiera data till en Data Box Disk Data Box-enhet eller en Data Box Heavy enhet.
> * Skicka tillbaka enheten till Microsoft.
> * Tillämpa åtkomst behörigheter för filer och kataloger (endast Data Lake Storage Gen2)

## <a name="prerequisites"></a>Förutsättningar

Du behöver dessa saker för att slutföra migreringen.

* Ett Azure Storage-konto.

* Ett lokalt Hadoop-kluster som innehåller dina källdata.

* En [Azure Data Box enhet](https://azure.microsoft.com/services/storage/databox/).

  * [Beställ data Box-enhet](../../databox/data-box-deploy-ordered.md) eller [data Box Heavy](../../databox/data-box-heavy-deploy-ordered.md). 

  * Kabeln och ansluter [data Box-enhet](../../databox/data-box-deploy-set-up.md) eller [data Box Heavy](../../databox/data-box-heavy-deploy-set-up.md) till ett lokalt nätverk.

Låt oss börja om du är klar.

## <a name="copy-your-data-to-a-data-box-device"></a>Kopiera data till en Data Box-enhet enhet

Om dina data passar in på en enda Data Box-enhet enhet kommer du att kopiera data till Data Box-enhet enheten. 

Om data storleken överskrider den Data Box-enhet enhetens kapacitet använder du den [valfria proceduren för att dela upp data över flera data Box-enhet enheter](#appendix-split-data-across-multiple-data-box-devices) och utför sedan det här steget. 

Om du vill kopiera data från din lokala HDFS-butik till en Data Box-enhet enhet, ställer du in några saker och använder sedan [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) -verktyget.

Följ dessa steg om du vill kopiera data via REST-API: er för BLOB/objekt-lagring till din Data Box-enhet-enhet. REST API-gränssnittet gör att enheten visas som en HDFS-lagring i klustret.

1. Innan du kopierar data via REST ska du identifiera säkerhets-och anslutnings primitiver för att ansluta till REST-gränssnittet på Data Box-enhet eller Data Box Heavy. Logga in på det lokala webb gränssnittet för Data Box-enhet och gå till sidan **Anslut och kopiera** . Gå till Azure Storage-kontot för din enhet, under **åtkomst inställningar**, leta upp och välj **rest**.

    ![Sidan "Anslut och kopiera"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. I dialog rutan åtkomst till lagrings konto och ladda upp data kopierar du **BLOB service slut punkten** och **lagrings konto nyckeln**. Från BLOB service-slutpunkten utelämnar `https://` och avslutande snedstreck.

    I det här fallet är slut punkten: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/` . Värd delen av den URI som du ska använda är: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com` . Ett exempel finns i så här [ansluter du till rest över http](../../databox/data-box-deploy-copy-data-via-rest.md). 

     ![Dialog rutan "åtkomst till lagrings konto och uppladdning av data"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Lägg till slut punkten och Data Box-enhet-eller Data Box Heavy nodens IP-adress till `/etc/hosts` på varje nod.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    Om du använder någon annan mekanism för DNS bör du se till att Data Box-enhet-slutpunkten kan lösas.

4. Ställ in Shell-variabeln på `azjars` platsen för- `hadoop-azure` och `azure-storage` jar-filerna. Du hittar dessa filer under installations katalogen för Hadoop.

    Använd följande kommando för att ta reda på om filerna finns: `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure` . Ersätt `<hadoop_install_dir>` plats hållaren med sökvägen till den katalog där du har installerat Hadoop. Se till att du använder fullständigt kvalificerade sökvägar.

    Exempel:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. Skapa den lagrings behållare som du vill använda för data kopiering. Du bör också ange en mål katalog som en del av det här kommandot. Detta kan vara en vår mål katalog för mottagare i detta läge.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Ersätt `<blob_service_endpoint>` plats hållaren med namnet på din BLOB service-slutpunkt.

    * Ersätt `<account_key>` plats hållaren med åtkomst nyckeln för ditt konto.

    * Ersätt `<container-name>` plats hållaren med namnet på din behållare.

    * Ersätt `<destination_directory>` plats hållaren med namnet på den katalog som du vill kopiera data till.

6. Kör ett List kommando för att se till att din behållare och katalog har skapats.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * Ersätt `<blob_service_endpoint>` plats hållaren med namnet på din BLOB service-slutpunkt.

   * Ersätt `<account_key>` plats hållaren med åtkomst nyckeln för ditt konto.

   * Ersätt `<container-name>` plats hållaren med namnet på din behållare.

7. Kopiera data från Hadoop HDFS till Data Box-enhet Blob Storage i den behållare som du skapade tidigare. Om den katalog som du kopierar till inte hittas skapas den automatiskt.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Ersätt `<blob_service_endpoint>` plats hållaren med namnet på din BLOB service-slutpunkt.

    * Ersätt `<account_key>` plats hållaren med åtkomst nyckeln för ditt konto.

    * Ersätt `<container-name>` plats hållaren med namnet på din behållare.

    * Ersätt `<exlusion_filelist_file>` plats hållaren med namnet på den fil som innehåller listan över fil undantag.

    * Ersätt `<source_directory>` plats hållaren med namnet på den katalog som innehåller de data som du vill kopiera.

    * Ersätt `<destination_directory>` plats hållaren med namnet på den katalog som du vill kopiera data till.

    `-libjars`Alternativet används för att göra `hadoop-azure*.jar` och beroende `azure-storage*.jar` filerna tillgängliga för `distcp` . Detta kan redan inträffa i vissa kluster.

    I följande exempel visas hur `distcp` kommandot används för att kopiera data.

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    Så här förbättrar du kopierings hastigheten:

    * Försök att ändra antalet mappningar. (Exemplet ovan använder `m` = 4 mappningar.)

    * Testa att köra flera `distcp` parallellt.

    * Kom ihåg att stora filer fungerar bättre än små filer.

## <a name="ship-the-data-box-to-microsoft"></a>Leverera Data Box-enhet till Microsoft

Följ de här stegen för att förbereda och leverera Data Box-enhet-enheten till Microsoft.

1. Börja  [med att Förbered för att skicka på data Box-enhet eller data Box Heavy](../../databox/data-box-deploy-copy-data-via-rest.md).

2. När enhets förberedelsen är klar laddar du ned filerna. Du kommer att använda dessa STRUKTURLISTE-eller manifest-filer senare för att kontrol lera de data som överförs till Azure.

3. Stäng av enheten och ta bort kablarna.

4. Schemalägg en upphämtning med UPS.

    * För Data Box-enhet enheter, se [leverera din data Box-enhet](../../databox/data-box-deploy-picked-up.md).

    * För Data Box Heavy enheter, se [leverera din data Box Heavy](../../databox/data-box-heavy-deploy-picked-up.md).

5. När Microsoft har tagit emot enheten är den ansluten till data Center nätverket och data överförs till det lagrings konto du angav när du placerade enhets ordningen. Verifiera mot de BOM-filer som alla dina data laddas upp till Azure. 

## <a name="apply-access-permissions-to-files-and-directories-data-lake-storage-gen2-only"></a>Tillämpa åtkomst behörigheter för filer och kataloger (endast Data Lake Storage Gen2)

Du har redan data till ditt Azure Storage-konto. Nu ska du tillämpa åtkomst behörigheter för filer och kataloger.

> [!NOTE]
> Det här steget behövs bara om du använder Azure Data Lake Storage Gen2 som data lager. Om du bara använder ett Blob Storage-konto utan hierarkiskt namn område som data lager kan du hoppa över det här avsnittet.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Skapa ett huvud namn för tjänsten för ditt Azure Data Lake Storage Gen2-konto

Information om hur du skapar ett huvud namn för tjänsten finns i [How to: använda portalen för att skapa ett Azure AD-program och tjänstens huvud namn som kan komma åt resurser](../../active-directory/develop/howto-create-service-principal-portal.md).

* När du utför stegen i avsnittet [Tilldela programmet till en roll](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) i artikeln ska du tilldela rollen **Storage Blob Data-deltagare** till tjänstens huvudnamn.

* När du utför stegen i avsnittet [Hämta värden för signering i](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) artikeln sparar du program-ID: t och klientens hemliga värden i en textfil. Du kommer att behöva dem snart.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>Generera en lista över kopierade filer med deras behörigheter

Kör det här kommandot från det lokala Hadoop-klustret:

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

Det här kommandot genererar en lista över kopierade filer med deras behörigheter.

> [!NOTE]
> Beroende på antalet filer i HDFS kan det ta lång tid att köra det här kommandot.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Generera en lista med identiteter och mappa dem till Azure Active Directory (lägga till) identiteter

1. Hämta `copy-acls.py` skriptet. Se [skript för hämtnings hjälp och konfigurera Edge-noden för att köra dem](#download-helper-scripts) i den här artikeln.

2. Kör det här kommandot för att generera en lista över unika identiteter.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   Det här skriptet skapar en fil med namnet `id_map.json` som innehåller de identiteter som du behöver mappa till lägga till-baserade identiteter.

3. Öppna `id_map.json` filen i en text redigerare.

4. För varje JSON-objekt som visas i filen uppdaterar du `target` attributet för antingen AAD-användarens huvud namn (UPN) eller ObjectId (OID) med lämplig mappad identitet. När du är klar sparar du filen. Du behöver den här filen i nästa steg.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>Tillämpa behörigheter på kopierade filer och tillämpa identitets mappningar

Kör det här kommandot för att tillämpa behörigheter på de data som du kopierade till Data Lake Storage Gen2-kontot:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* Ersätt platshållaren `<storage-account-name>` med namnet på ditt lagringskonto.

* Ersätt `<container-name>` plats hållaren med namnet på din behållare.

* Ersätt `<application-id>` `<client-secret>` plats hållarna och med det program-ID och den klient hemlighet som du samlade in när du skapade tjänstens huvud namn.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>Bilaga: dela data över flera Data Box-enhet enheter

Innan du flyttar dina data till en Data Box-enhet-enhet måste du ladda ned vissa hjälp skript, se till att dina data är ordnade för att få plats på en Data Box-enhet enhet och undanta alla onödiga filer.

<a id="download-helper-scripts"></a>

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>Hämta hjälp program skript och konfigurera Edge-noden för att köra dem

1. Kör följande kommando från din Edge-eller Head-nod i ditt lokala Hadoop-kluster:

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   Det här kommandot klonar GitHub-lagringsplatsen som innehåller hjälp skripten.

2. Kontrol lera att har [JQ](https://stedolan.github.io/jq/) -paketet installerat på den lokala datorn.

   ```bash
   
   sudo apt-get install jq
   ```

3. Installera python-paketet med [begär Anden](https://2.python-requests.org/en/master/) .

   ```bash
   
   pip install requests
   ```

4. Ange kör behörigheter för de skript som krävs.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>Se till att dina data är ordnade så att de passar in på en Data Box-enhet enhet

Om storleken på dina data överskrider storleken på en enskild Data Box-enhet enhet kan du dela upp filer i grupper som du kan lagra på flera Data Box-enhet-enheter.

Om dina data inte överskrider storleken på en enkel Data Box-enhet enhet kan du fortsätta till nästa avsnitt.

1. Med utökade behörigheter kör du `generate-file-list` skriptet som du laddade ned genom att följa anvisningarna i föregående avsnitt.

   Här är en beskrivning av kommando parametrarna:

   ```
   sudo -u hdfs ./generate-file-list.py [-h] [-s DATABOX_SIZE] [-b FILELIST_BASENAME]
                    [-f LOG_CONFIG] [-l LOG_FILE]
                    [-v {DEBUG,INFO,WARNING,ERROR}]
                    path

   where:
   positional arguments:
   path                  The base HDFS path to process.

   optional arguments:
   -h, --help            show this help message and exit
   -s DATABOX_SIZE, --databox-size DATABOX_SIZE
                        The size of each Data Box in bytes.
   -b FILELIST_BASENAME, --filelist-basename FILELIST_BASENAME
                        The base name for the output filelists. Lists will be
                        named basename1, basename2, ... .
   -f LOG_CONFIG, --log-config LOG_CONFIG
                        The name of a configuration file for logging.
   -l LOG_FILE, --log-file LOG_FILE
                        Name of file to have log output written to (default is
                        stdout/stderr)
   -v {DEBUG,INFO,WARNING,ERROR}, --log-level {DEBUG,INFO,WARNING,ERROR}
                        Level of log information to output. Default is 'INFO'.
   ```

2. Kopiera de genererade fil listorna till HDFS så att de är tillgängliga för [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) -jobbet.

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>Ta bort onödiga filer

Du måste undanta vissa kataloger från DisCp-jobbet. Du kan till exempel utelämna kataloger som innehåller tillståndsinformation som håller klustret igång.

I det lokala Hadoop-klustret där du planerar att initiera DistCp-jobbet skapar du en fil som anger listan över kataloger som du vill undanta.

Här är ett exempel:

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur Data Lake Storage Gen2 fungerar med HDInsight-kluster. Se [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).