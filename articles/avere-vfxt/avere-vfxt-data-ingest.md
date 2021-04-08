---
title: Flytta data till AVERT vFXT för Azure
description: Så här lägger du till data till en ny lagrings volym för användning med AVERT vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/16/2019
ms.author: rohogue
ms.openlocfilehash: 76bbe60397ebb01aed5694d933b3067f778a4c21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85505604"
---
# <a name="moving-data-to-the-vfxt-cluster---parallel-data-ingest"></a>Flytta data till vFXT-kluster – parallella data inmatningar

När du har skapat ett nytt vFXT-kluster kan din första uppgift vara att flytta data till en ny lagrings volym i Azure. Men om din vanliga metod för att flytta data utfärdar ett enkelt kopierings kommando från en klient, visas förmodligen en långsam kopierings prestanda. Enkel tråds kopiering är inte ett lämpligt alternativ för att kopiera data till det vFXT klustrets Server dels lagrings utrymme.

Eftersom värdet aver vFXT för Azure Cluster är ett skalbart cacheminne för flera klienter, är det snabbaste och mest effektiva sättet att kopiera data till den med flera klienter. Den här metoden parallelizes inmatning av filer och objekt.

![Diagram över flera klienter, multi-threadd data förflyttning: längst upp till vänster finns det flera pilar från den lokala maskin varu lagringen. Pilarna pekar på fyra klient datorer. Från varje klient dator tre pilar pekar du mot AVERT vFXT. Från AVERT vFXT pekar flera pilar på Blob Storage.](media/avere-vfxt-parallel-ingest.png)

De ``cp`` eller ``copy`` -kommandon som används ofta för att överföra data från ett lagrings system till ett annat är processer som bara kopierar en fil i taget. Det innebär att fil servern bara matar in en fil i taget, vilket är ett avfall från klustrets resurser.

I den här artikeln beskrivs strategier för att skapa en fil kopierings system med flera klienter och flera trådar för att flytta data till ett AVERT vFXT-kluster. Den förklarar fil överförings koncept och besluts punkter som kan användas för effektiv data kopiering med hjälp av flera klienter och kommandon för enkel kopiering.

Det beskriver också vissa verktyg som kan hjälpa dig. ``msrsync``Verktyget kan användas för att delvis automatisera processen med att dela upp en data uppsättning i buckets och använda ``rsync`` kommandon. ``parallelcp``Skriptet är ett annat verktyg som läser käll katalogen och utfärdar kopierings kommandon automatiskt. ``rsync``Verktyget kan också användas i två faser för att ge en snabb kopia som fortfarande ger data konsekvens.

Klicka på länken för att gå till ett avsnitt:

* [Exempel på manuell kopiering](#manual-copy-example) – en grundlig förklaring med hjälp av kopierings kommandon
* [Rsync-exempel för två faser](#use-a-two-phase-rsync-process)
* [Exempel på delvis automatiserad (msrsync)](#use-the-msrsync-utility)
* [Exempel på parallell kopiering](#use-the-parallel-copy-script)

## <a name="data-ingestor-vm-template"></a>Mall för data inmatnings dator

En Resource Manager-mall är tillgänglig på GitHub för att automatiskt skapa en virtuell dator med de verktyg för parallell data inmatning som nämns i den här artikeln.

![diagram som visar flera pilar var och en från Blob Storage, maskin varu lagring och Azure-filkällor. Pilarna pekar på en "data insugnings VM" och därifrån pekar du på vFXT](media/avere-vfxt-ingestor-vm.png)

Data inmatnings datorn är en del av en själv studie kurs där den nyskapade virtuella datorn monterar det vFXT klustret och laddar ned start skriptet från klustret. Läs [bootstrap en data inmatnings dator](https://github.com/Azure/Avere/blob/master/docs/data_ingestor.md) om du vill ha mer information.

## <a name="strategic-planning"></a>Strategisk planering

När du utformar en strategi för att kopiera data parallellt bör du förstå kompromisserna i fil storlek, antal filer och katalog djup.

* När filerna är små är måtten för intresse filer per sekund.
* När filerna är stora (10MiBi eller större) är måttet för ränta byte per sekund.

Varje kopierings process har en data flödes hastighet och en fil överförings hastighet, som kan mätas med tiden för kopierings kommandot och väga fil storleken och antalet filer. Att förklara hur priserna ska mätas ligger utanför omfånget för det här dokumentet, men det är viktigt att förstå om du ska hantera små eller stora filer.

## <a name="manual-copy-example"></a>Exempel på manuell kopiering

Du kan manuellt skapa en flertrådad kopia på en klient genom att köra fler än ett kopierings kommando på en gång i bakgrunden mot fördefinierade uppsättningar av filer eller sökvägar.

Kommandot Linux/UNIX ``cp`` innehåller argumentet ``-p`` för att bevara ägarskap och mtime metadata. Att lägga till det här argumentet i kommandona nedan är valfritt. (Om du lägger till argumentet ökar antalet fil Systems anrop som skickas från klienten till mål fil systemet för ändring av metadata.)

I det här enkla exemplet kopieras två filer parallellt:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

När det här kommandot har utfärdats `jobs` visar kommandot att två trådar körs.

### <a name="predictable-filename-structure"></a>Struktur för förutsägbar fil namn

Om dina fil namn är förutsägbara kan du använda uttryck för att skapa parallella kopierings trådar.

Om din katalog till exempel innehåller 1000 filer som är numrerade från `0001` till `1000` kan du använda följande uttryck för att skapa tio parallella trådar som varje kopia 100-filer:

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination1/ & \
cp /mnt/source/file2* /mnt/destination1/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination1/ & \
cp /mnt/source/file5* /mnt/destination1/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination1/ & \
cp /mnt/source/file8* /mnt/destination1/ & \
cp /mnt/source/file9* /mnt/destination1/
```

### <a name="unknown-filename-structure"></a>Okänd fil namns struktur

Om fil namns strukturen inte är förutsägbar, kan du gruppera filer efter katalog namn.

Det här exemplet samlar in hela kataloger som ska skickas till ``cp`` kommandon som körs som bakgrunds aktiviteter:

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

När filerna har samlats in kan du köra kommandon för parallell kopiering för att rekursivt kopiera under katalogerna och allt dess innehåll:

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

### <a name="when-to-add-mount-points"></a>När du ska lägga till monterings punkter

När du har tillräckligt parallella trådar för en enda mål-filsystem-monterings punkt kommer det att finnas en punkt där det inte finns mer data flöde att lägga till fler trådar. (Data flödet mäts i filer/sekund eller byte per sekund, beroende på vilken typ av data du använder.) Eller sämre, kan övertråds teknik ibland orsaka en data flödes försämring.

När detta inträffar kan du lägga till monterings punkter på klient sidan till andra vFXT-kluster-IP-adresser med samma fjärrfilsystem-monterings Sök väg:

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

Genom att lägga till monterings punkter på klient sidan kan du förgrena ut ytterligare kopierings kommandon till ytterligare `/mnt/destination[1-3]` monterings punkter, vilket ger ytterligare parallellitet.

Om filerna t. ex. är mycket stora kan du definiera kopierings kommandona för att använda olika mål Sök vägar, vilket skickar ut fler kommandon parallellt från den klient som utför kopian.

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination2/ & \
cp /mnt/source/file2* /mnt/destination3/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination2/ & \
cp /mnt/source/file5* /mnt/destination3/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination2/ & \
cp /mnt/source/file8* /mnt/destination3/ & \
```

I exemplet ovan är alla tre mål monterings punkter riktade till klient fil kopierings processerna.

### <a name="when-to-add-clients"></a>När du ska lägga till klienter

Slutligen, när du har nått klientens funktioner, ger ytterligare kopierings trådar eller ytterligare monterings punkter inga ytterligare filer/SEK eller byte/s ökar. I så fall kan du distribuera en annan klient med samma uppsättning monterings punkter som kommer att köra egna uppsättningar fil kopierings processer.

Exempel:

```bash
Client1: cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
Client1: cp -R /mnt/source/dir2/dir2a /mnt/destination/dir2/ &
Client1: cp -R /mnt/source/dir3/dir3a /mnt/destination/dir3/ &

Client2: cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
Client2: cp -R /mnt/source/dir2/dir2b /mnt/destination/dir2/ &
Client2: cp -R /mnt/source/dir3/dir3b /mnt/destination/dir3/ &

Client3: cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ &
Client3: cp -R /mnt/source/dir2/dir2c /mnt/destination/dir2/ &
Client3: cp -R /mnt/source/dir3/dir3c /mnt/destination/dir3/ &

Client4: cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
Client4: cp -R /mnt/source/dir2/dir2d /mnt/destination/dir2/ &
Client4: cp -R /mnt/source/dir3/dir3d /mnt/destination/dir3/ &
```

### <a name="create-file-manifests"></a>Skapa fil manifest

När du har lärt dig mer om metoderna ovan (flera kopierings trådar per mål, flera mål per klient, flera klienter per nätverks lättillgängligt käll fil system), bör du tänka på följande rekommendation: Bygg fil manifest och Använd dem med kopiera kommandon på flera klienter.

I det här scenariot används UNIX- ``find`` kommandot för att skapa manifest med filer eller kataloger:

```bash
user@build:/mnt/source > find . -mindepth 4 -maxdepth 4 -type d
./atj5b55c53be6-01/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-01/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-01/support/trace/rolling
./atj5b55c53be6-03/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-03/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-03/support/trace/rolling
./atj5b55c53be6-02/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-02/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-02/support/trace/rolling
```

Omdirigera det här resultatet till en fil: `find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`

Sedan kan du iterera genom manifestet med hjälp av BASH-kommandon för att räkna filer och fastställa storlekarna för under katalogerna:

```bash
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l` `du -sh ${i}`"; done
244    3.5M    ./atj5b5ab44b7f-02/support/gsi/2018-07-18T00:07:03EDT
9      172K    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.8M    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T01:01:00UTC
131    13M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    6.2M    ./atj5b5ab44b7f-02/support/gsi/2018-07-20T21:59:41UTC
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_vfxt_catchup
7      16K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:12:19UTC
8      83K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:17:17UTC
575    7.7M    ./atj5b5ab44b7f-02/support/cores/armada_main.2000.1531980253.gsi
33     4.4G    ./atj5b5ab44b7f-02/support/trace/rolling
281    6.6M    ./atj5b5ab44b7f-01/support/gsi/2018-07-18T00:07:03EDT
15     182K    ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-18T05:01:00UTC
244    17M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-19T01:01:01UTC
299    31M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T01:01:00UTC
256    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T21:59:41UTC_partial
889    7.7M    ./atj5b5ab44b7f-01/support/gsi/2018-07-20T21:59:41UTC
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_vfxt_catchup
11     248K    ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:12:19UTC
11     88K     ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:17:17UTC
645    11M     ./atj5b5ab44b7f-01/support/cores/armada_main.2019.1531980253.gsi
33     4.0G    ./atj5b5ab44b7f-01/support/trace/rolling
244    2.1M    ./atj5b5ab44b7f-03/support/gsi/2018-07-18T00:07:03EDT
9      158K    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.3M    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T01:01:00UTC
131    12M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    8.4M    ./atj5b5ab44b7f-03/support/gsi/2018-07-20T21:59:41UTC
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_vfxt_catchup
7      159K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:12:19UTC
7      157K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:17:17UTC
576    12M     ./atj5b5ab44b7f-03/support/cores/armada_main.2013.1531980253.gsi
33     2.8G    ./atj5b5ab44b7f-03/support/trace/rolling
```

Slutligen måste du hantverka de faktiska fil kopierings kommandona till klienterna.

Om du har fyra klienter använder du följande kommando:

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

Om du har fem klienter använder du något som liknar följande:

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

Och för sex.... Extrapolera vid behov.

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

Du får *N* resulterande filer, en för var och en av dina *N* -klienter som har Sök vägs namnen på nivå fyra kataloger som erhålls som en del av utdata från `find` kommandot.

Använd varje fil för att bygga kopierings kommandot:

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

Ovan får du *N* filer, var och en med ett kopierings kommando per rad, som kan köras som ett bash-skript på klienten.

Målet är att köra flera trådar av dessa skript samtidigt per klient parallellt på flera klienter.

## <a name="use-a-two-phase-rsync-process"></a>Använda en rsync process med två faser

Standard ``rsync`` verktyget fungerar inte bra för att fylla i moln lagring via det Avera vFXT för Azure-systemet eftersom det genererar ett stort antal fil skapande-och namnbytes åtgärder för att garantera data integriteten. Du kan dock använda ``--inplace`` alternativet med ``rsync`` för att hoppa över den mer noggranna kopierings proceduren om du följer den med en andra körning som kontrollerar fil integriteten.

En standard ``rsync`` kopierings åtgärd skapar en temporär fil och fyller den med data. Om data överföringen har slutförts får den temporära filen namnet till det ursprungliga fil namnet. Den här metoden garanterar konsekvens även om filerna nås under kopieringen. Men den här metoden genererar fler Skriv åtgärder, vilket saktar ned fil flyttningen via cachen.

Alternativet ``--inplace`` skriver den nya filen direkt på dess slutgiltiga plats. Filerna är inte garanterat konsekventa vid överföring, men det är inte viktigt om du Prima ett lagrings system för användning senare.

Den andra ``rsync`` åtgärden fungerar som en konsekvens kontroll för den första åtgärden. Eftersom filerna redan har kopierats är den andra fasen en snabb genom gång för att säkerställa att filerna på målet matchar filerna på källan. Om några filer inte matchar kopieras de om.

Du kan skicka båda faserna tillsammans i ett kommando:

```bash
rsync -azh --inplace <source> <destination> && rsync -azh <source> <destination>
```

Den här metoden är en enkel och tids effektiv metod för data uppsättningar upp till det antal filer som den interna katalog hanteraren kan hantera. (Detta är normalt 200 000 000 filer för ett kluster med tre noder, 500 000 000-filer för ett kluster med sex noder och så vidare.)

## <a name="use-the-msrsync-utility"></a>Använda verktyget msrsync

``msrsync``Verktyget kan också användas för att flytta data till en server del för Server delen för det Avera klustret. Det här verktyget är utformat för att optimera bandbredds användningen genom att köra flera parallella ``rsync`` processer. Den är tillgänglig från GitHub på <https://github.com/jbd/msrsync> .

``msrsync`` delar upp käll katalogen i separata "buckets" och kör sedan enskilda ``rsync`` processer i varje Bucket.

Preliminär testning med en virtuell dator med fyra kärnor visade bäst effektivitet vid användning av 64-processer. Använd ``msrsync`` alternativet ``-p`` för att ange antalet processer till 64.

Du kan också använda ``--inplace`` argumentet med- ``msrsync`` kommandon. Om du använder det här alternativet bör du överväga att köra ett andra kommando (som med [rsync](#use-a-two-phase-rsync-process), som beskrivs ovan) för att säkerställa data integriteten.

``msrsync`` Det går bara att skriva till och från lokala volymer. Källa och mål måste vara tillgängliga som lokala monteringar i klustrets virtuella nätverk.

Följ dessa instruktioner om du vill använda ``msrsync`` för att fylla i en Azure Cloud-volym med ett AVERT-kluster:

1. Installera ``msrsync`` och dess förutsättningar (rsync och Python 2,6 eller senare)
1. Fastställ det totala antalet filer och kataloger som ska kopieras.

   Använd till exempel verktyget aver ``prime.py`` med argument ```prime.py --directory /path/to/some/directory``` (tillgängligt genom nedladdning av URL <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py> ).

   Om du inte använder ``prime.py`` kan du beräkna antalet objekt med ``find`` verktyget GNU enligt följande:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Dividera antalet objekt med 64 för att fastställa antalet objekt per process. Använd det här talet med ``-f`` alternativet för att ställa in storleken på buckets när du kör kommandot.

1. Utfärda ``msrsync`` kommandot för att kopiera filer:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Om du använder ``--inplace`` lägger du till en andra körning utan alternativet för att kontrol lera att data har kopierats korrekt:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH> && msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Det här kommandot är till exempel utformat för att flytta 11 000-filer i 64-processer från/test/source-repository till/mnt/vfxt/repository:

   ``msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository && msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository``

## <a name="use-the-parallel-copy-script"></a>Använd skriptet för parallell kopiering

``parallelcp``Skriptet kan också vara användbart för att flytta data till vFXT-klustrets backend-lagring.

Skriptet nedan lägger till den körbara filen `parallelcp` . (Det här skriptet är utformat för Ubuntu. om du använder en annan distribution måste du installera ``parallel`` separat.)

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM
#!/bin/bash

display_usage() {
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n"
}

if [  \$# -le 1 ] ; then
    display_usage
    exit 1
fi

if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then
    display_usage
    exit 0
fi

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

### <a name="parallel-copy-example"></a>Exempel på parallell kopiering

I det här exemplet används skriptet för parallell kopiering för att kompilera ``glibc`` med källfiler från det Avera klustret.
<!-- xxx what is stored where? what is 'the avere cluster mount point'? xxx -->

Källfilerna lagras på kluster monterings punkten aver och objekten lagras på den lokala hård disken.

Skriptet använder parallellt kopierings skript ovan. Alternativet ``-j`` används med ``parallelcp`` och ``make`` för att få parallellisering.

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/node1 avere
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```
