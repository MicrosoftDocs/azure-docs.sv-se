---
title: Data inmatning för Azure HPC-cache – manuell kopiering
description: Använda CP-kommandon för att flytta data till ett Blob Storage-mål i Azure HPC-cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: f96a0fa264124f9d050e667b003d98579da63b77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87092345"
---
# <a name="azure-hpc-cache-data-ingest---manual-copy-method"></a>Data inmatning för Azure HPC-cache – manuell kopierings metod

Den här artikeln innehåller detaljerade anvisningar för att manuellt kopiera data till en Blob Storage-behållare för användning med Azure HPC-cache. Den använder flera trådar parallella åtgärder för att optimera kopierings hastigheten.

Läs mer om hur du flyttar data till Blob Storage för Azure HPC cache genom [att läsa flytta data till Azure Blob Storage](hpc-cache-ingest.md).

## <a name="simple-copy-example"></a>Enkelt kopierings exempel

Du kan manuellt skapa en flertrådad kopia på en klient genom att köra fler än ett kopierings kommando på en gång i bakgrunden mot fördefinierade uppsättningar av filer eller sökvägar.

Kommandot Linux/UNIX ``cp`` innehåller argumentet ``-p`` för att bevara ägarskap och mtime metadata. Att lägga till det här argumentet i kommandona nedan är valfritt. (Om du lägger till argumentet ökar antalet fil system anrop som skickas från klienten till mål fil systemet för ändring av metadata.)

I det här enkla exemplet kopieras två filer parallellt:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

När det här kommandot har utfärdats `jobs` visar kommandot att två trådar körs.

## <a name="copy-data-with-predictable-file-names"></a>Kopiera data med förutsägbara fil namn

Om dina fil namn är förutsägbara kan du använda uttryck för att skapa parallella kopierings trådar.

Om din katalog till exempel innehåller 1000 filer som är numrerade från `0001` till `1000` kan du använda följande uttryck för att skapa 10 parallella trådar som varje kopia 100-filer:

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

## <a name="copy-data-with-unstructured-file-names"></a>Kopiera data med ostrukturerade fil namn

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

## <a name="when-to-add-mount-points"></a>När du ska lägga till monterings punkter

När du har tillräckligt parallella trådar för en enda mål fil system monterings punkt kommer det att finnas en punkt där det inte finns mer data flöde att lägga till fler trådar. (Data flödet mäts i filer/sekund eller byte per sekund, beroende på vilken typ av data du använder.) Eller sämre, kan övertråds teknik ibland orsaka en data flödes försämring.

När detta inträffar kan du lägga till monterings punkter på klient sidan till andra Azure HPC cache-Mount-adresser med samma fil system monterings Sök väg:

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

## <a name="when-to-add-clients"></a>När du ska lägga till klienter

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

## <a name="create-file-manifests"></a>Skapa fil manifest

När du har lärt dig mer om metoderna ovan (flera Copy-threads per mål, flera mål per klient, flera klienter per nätverks åtkomligt käll fil system), bör du tänka på följande rekommendation: Bygg fil manifest och Använd dem med kopiera kommandon på flera klienter.

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
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
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
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
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
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_hpccache_catchup
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
