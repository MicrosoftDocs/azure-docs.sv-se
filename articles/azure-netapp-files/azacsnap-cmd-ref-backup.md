---
title: Säkerhetskopiera med Azure Application konsekvent ögonblicks bilds verktyg för Azure NetApp Files | Microsoft Docs
description: Innehåller en guide för att köra säkerhets kopierings kommandot för Azure Application konsekvent ögonblicks bilds verktyg som du kan använda med Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 17c29fdf88495f6ecc40963eda08858887173fd1
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730946"
---
# <a name="back-up-using-azure-application-consistent-snapshot-tool-preview"></a>Säkerhetskopiera med Azure Application konsekvent ögonblicks bild verktyget (förhands granskning)

Den här artikeln innehåller en guide för att köra säkerhets kopierings kommandot för Azure Application konsekvent ögonblicks bilds verktyg som du kan använda med Azure NetApp Files.

## <a name="introduction"></a>Introduktion

En lagrings ögonblicks bild baserad säkerhets kopiering körs med hjälp av `azacsnap -c backup` kommandot.  Det här kommandot utför dirigeringen av en databas med konsekvent lagring av ögonblicks bilder på DATA volymerna och en ögonblicks bild av lagring (utan någon databas konsekvens installation) på de andra volymerna.  

För att DATA volymer `azacsnap` ska förbereda databasen för en lagrings ögonblicks bild kommer den att ta ögonblicks bilden av lagringen för alla konfigurerade volymer. Slutligen kommer den att meddela databasen som ögonblicks bilden är slutförd.  Den hanterar också alla databas poster som registrerar aktiviteter för ögonblicks bilds säkerhets kopiering (t. ex. SAP HANA säkerhets kopierings katalog).

## <a name="command-options"></a>Kommando alternativ

`-c backup`Kommandot tar följande argument:

- `--volume=` typ av volym som ögonblicks bild, den här parametern kan innehålla `data` eller `other`
  - `data` ögonblicks bilder av volymerna i dataVolume-Stanza i konfigurations filen.
  - `other` ögonblicks bilder av volymerna i otherVolume-Stanza i konfigurations filen.
  
  > [!NOTE]
  > Genom att skapa en separat konfigurations fil med start volymen som otherVolume är det möjligt för `boot` ögonblicks bilder att tas i ett helt annat schema (till exempel dagligen).

- `--prefix=` prefix för kundens ögonblicks bild för ögonblicks bildens namn. Den här parametern har två syfte. Det första syftet är att ange ett unikt namn för gruppering av ögonblicks bilder. För det andra för att fastställa `--retention` antalet lagrings ögonblicks bilder som behålls för den angivna `--prefix` .

    > [!IMPORTANT]
    > Endast alfanumeriska tecken ("A-Z, A-z, 0-9"), under streck ("_") och bindestreck ("-") är tillåtna.

- `--retention` antal ögonblicks bilder av definierad som ska `--prefix` behållas. Eventuella ytterligare ögonblicks bilder tas bort efter att en ny ögonblicks bild har gjorts för detta `--prefix` .

- `--trim` för SAP HANA v2 och senare, upprätthåller det här alternativet säkerhets kopierings katalogen och disk katalogen och logg säkerhets kopior. Antalet poster som ska behållas i säkerhets kopierings katalogen bestäms av `--retention` alternativet ovan och tar bort äldre poster för det definierade prefixet ( `--prefix` ) från säkerhets kopierings katalogen och den relaterade säkerhets kopian av fysiska loggar. Den tar också bort eventuella logg säkerhets kopierings poster som är äldre än den äldsta säkerhets kopian för icke-logg. De här åtgärderna hjälper till att förhindra att säkerhets kopior av loggar använder upp allt tillgängligt disk utrymme.

  > [!NOTE]
  > Följande exempel kommando behåller 9 lagrings ögonblicks bilder och säkerställer att säkerhets kopierings katalogen ständigt trimmas för att matcha de 9 lagrings ögonblicks bilder som behålls.

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
    ```

- `[--ssl=]` en valfri parameter som definierar krypterings metoden som används för att kommunicera med SAP HANA, antingen `openssl` eller `commoncrypto` . Om det här alternativet anges `azacsnap -c backup` förväntar sig kommandot att hitta två filer i samma katalog. filerna måste ha namnet efter motsvarande sid. Se [använda SSL för kommunikation med SAP HANA](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana). I följande exempel används en `hana` typ ögonblicks bild med ett prefix för `hana_TEST` och som håller `9` på att kommunicera med SAP HANA med hjälp av SSL ( `openssl` ).

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim --ssl=openssl
    ```

- `[--configfile <config filename>]` är en valfri parameter som tillåter anpassade konfigurations fil namn.

## <a name="snapshot-backups-are-fast"></a>Ögonblicks säkerhets kopieringar är snabba

Varaktigheten för en ögonblicks bild säkerhets kopia är oberoende av volymens storlek, med en volym på 10 TB som har fästs inom samma ungefärliga tid som en 10 GB-volym.  

De primära faktorer som påverkar den övergripande körnings tiden är antalet volymer som ska vara ögonblicks bilder och eventuella ändringar i `--retention` parametern (där en minskning kan öka körnings tiden då en överstigande ögonblicks bilder tas bort).

I exempel konfigurationen ovan (för **stor Azure-instans**) tog ögonblicks bilder för de två volymerna mindre än 5 sekunder att slutföra. För **Azure NetApp Files** tar ögonblicks bilder för de två volymerna att ta cirka 60 sekunder.

> [!NOTE]
> Om `--retention` är betydligt lägre än den föregående tiden `azacsnap` körs (till exempel från `--retention 50` till `--retention 5` ), kommer den tid som tar att öka om det `azacsnap` behövs att ta bort de extra ögonblicks bilderna.

## <a name="example-with-data-parameter"></a>Exempel med `data` parameter

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
```

Kommandot matas inte ut till konsolen, men skriver till en loggfil, en resultat fil och `/var/log/messages` .

*Logg filen* består av kommando namnet + alternativet-c + config-filnamnet. Som standard är ett logg fil namn för en `-c backup` körning med ett standard konfigurations fil namn `azacsnap-backup-azacsnap.log` .

*Resultat* filen har samma bas namn som logg filen, med `.result` som suffix, till exempel `azacsnap-backup-azacsnap.result` som innehåller följande utdata:

```bash
cat logs/azacsnap-backup-azacsnap.result
```

```output
Database # 1 (H80) : completed ok
```

`/var/log/messages`Filen innehåller samma utdata som `.result` filen. Se följande exempel (kör som rot):

```bash
grep "azacsnap.*Database" /var/log/messages | tail -n10
```

```output
Jul  2 05:22:07 server01 azacsnap[183868]: Database # 1 (H80) : completed ok
Jul  2 05:27:06 server01 azacsnap[4069]: Database # 1 (H80) : completed ok
Jul  2 05:32:07 server01 azacsnap[19769]: Database # 1 (H80) : completed ok
Jul  2 05:37:06 server01 azacsnap[35312]: Database # 1 (H80) : completed ok
Jul  2 05:42:06 server01 azacsnap[50877]: Database # 1 (H80) : completed ok
Jul  2 05:47:06 server01 azacsnap[66429]: Database # 1 (H80) : completed ok
Jul  2 05:52:06 server01 azacsnap[82964]: Database # 1 (H80) : completed ok
Jul  2 05:57:06 server01 azacsnap[98522]: Database # 1 (H80) : completed ok
Jul  2 05:59:13 server01 azacsnap[105519]: Database # 1 (H80) : completed ok
Jul  2 06:02:06 server01 azacsnap[114280]: Database # 1 (H80) : completed ok
```

## <a name="example-with-other-parameter"></a>Exempel med `other` parameter

```bash
azacsnap -c backup --volume other --prefix logs_TEST --retention 9
```

Kommandot matas inte ut till konsolen, men skriver bara till en loggfil.  Den skriver _inte_ till en resultat fil eller `/var/log/messages` .

*Logg filen* består av kommando namnet + alternativet-c + config-filnamnet. Som standard är ett logg fil namn för en `-c backup` körning med ett standard konfigurations fil namn `azacsnap-backup-azacsnap.log` .

## <a name="example-with-other-parameter-to-backup-host-os"></a>Exempel med `other` parameter (för att säkerhetskopiera värd-OS)

> [!NOTE]
> Användning av en annan konfigurations fil ( `--configfile bootVol.json` ) som bara innehåller start volymerna.

```bash
azacsnap -c backup --volume other --prefix boot_TEST --retention 9 --configfile bootVol.json
```

Kommandot matas inte ut till konsolen, men skriver bara till en loggfil.  Den skriver _inte_ till en resultat fil eller `/var/log/messages` .

*Logg filens* namn i det här exemplet är `azacsnap-backup-bootVol.log` .

> [!NOTE]
> Logg fil namnet består av "(kommando namnet-( `-c` alternativet)-(konfigurations fil namn)".  Om du till exempel använder `-c backup` alternativet med namnet på logg filen `h80.json` kommer logg filen att anropas `azacsnap-backup-h80.log` .  Eller om du använder `-c test` alternativet med samma konfigurations fil kommer logg filen att anropas `azacsnap-test-h80.log` .

- HANA stor instans typ: det finns två giltiga värden med `TYPEI` eller som är `TYPEII` beroende av den stora volymen Hana-instans.
- Se [tillgängliga SKU: er för Hana-stora instanser](../virtual-machines/workloads/sap/hana-available-skus.md) för att bekräfta tillgängliga SKU: er.

## <a name="next-steps"></a>Nästa steg

- [Hämta information om ögonblicks bild](azacsnap-cmd-ref-details.md)
- [Ta bort ögonblicks bilder](azacsnap-cmd-ref-delete.md)