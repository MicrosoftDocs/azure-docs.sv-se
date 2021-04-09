---
title: Ta bort med Azure Application konsekvent ögonblicks bilds verktyget för Azure NetApp Files | Microsoft Docs
description: Innehåller en guide för att köra kommandot DELETE i Azure Application ett konsekvent ögonblicks bilds verktyg som du kan använda med Azure NetApp Files.
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
ms.openlocfilehash: 1f2c767d45bb08e25a057c7db1f380ceb250f607
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864915"
---
# <a name="delete-using-azure-application-consistent-snapshot-tool-preview"></a>Ta bort med Azure Application konsekvent ögonblicks bild verktyget (förhands granskning)

Den här artikeln innehåller en guide för att köra kommandot DELETE i Azure Application enhetligt ögonblicks bilds verktyg som du kan använda med Azure NetApp Files.

## <a name="introduction"></a>Introduktion

Det går att ta bort ögonblicks bilder av volymen och databas katalog poster med `azacsnap -c delete` kommandot.

> [!IMPORTANT]
> Ögonblicks bilder som skapats mindre än 10 minuter innan du kör det här kommandot bör inte tas bort på grund av risken för störningar med ögonblicks bilds replikering.

## <a name="command-options"></a>Kommando alternativ

`-c delete`Kommandot har följande alternativ:

- `--delete hana` När den används med alternativen `--dbsid <SID>` och `--hanabackupid <HANA backup id>` tas poster bort från katalogen för SAP HANA säkerhets kopiering som matchar kriterierna.

- `--delete storage` När det används med alternativet `--snapshot <snapshot name>` tar bort ögonblicks bilden från lagrings systemet på Server sidan.

- `--delete sync` När det används med alternativ `--dbsid <SID>` och `--hanabackupid <HANA backup id>` hämtar namn på lagrings ögonblicks bilder från säkerhets kopierings katalogen för `<HANA backup id>` , och sedan tar bort posten i säkerhets kopie katalogen _och_ ögonblicks bilden från någon av volymerna som innehåller den namngivna ögonblicks bilden.

- `--delete sync` När den används med `--snapshot <snapshot name>` kommer att söka efter poster i säkerhets kopierings katalogen för `<snapshot name>` . hämtar SAP HANA säkerhets KOPIERINGS-ID och tar bort både posten i säkerhets kopie katalogen _och_ ögonblicks bilden från någon av de volymer som innehåller den namngivna ögonblicks bilden.

- `[--force]` valfritt *Använd med försiktighet*.  Den här åtgärden framtvingar borttagning utan att du uppmanas att bekräfta.

- `[--configfile <config filename>]` är en valfri parameter som tillåter anpassade konfigurations fil namn.

### <a name="delete-a-snapshot-using-sync-option"></a>Ta bort en ögonblicks bild med `sync` alternativet

```bash
azacsnap -c delete --delete sync --dbsid H80 --hanabackupid 157979797979
```

> [!NOTE]
> Söker efter poster i säkerhets kopie katalogen för SAP HANA säkerhets kopierings-ID 157979797979, hämtar ögonblicks bildens namn och tar bort både posten i säkerhets kopie katalogen och ögonblicks bilden från alla volymer som innehåller den namngivna ögonblicks bilden.

```bash
azacsnap -c delete --delete sync --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> Söker efter poster i säkerhets kopie katalogen för ögonblicks bilden med namnet hana_hourly .2020-01 -22 _2358, hämtar ID för SAP HANA säkerhets kopian och tar bort både posten i säkerhets kopie katalogen och ögonblicks bilden från någon av de volymer som innehåller den namngivna ögonblicks bilden.

### <a name="delete-a-snapshot-using-hana-option"></a>Ta bort en ögonblicks bild med `hana` alternativet

```bash
azacsnap -c delete --delete hana --dbsid H80 --hanabackupid 157979797979
```

> [!NOTE]
> Tar bort SAP HANA säkerhets kopierings-ID 157979797979 från säkerhets kopierings katalogen för SID-H80.

### <a name="delete-a-snapshot-using-storage-option"></a>Ta bort en ögonblicks bild med `storage` alternativet

```bash
azacsnap -c delete --delete storage --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> Tar bort ögonblicks bilden från volymer som innehåller en ögonblicks bild med namnet hana_hourly .2020 – 01 -22 _2358.

**Utdata med `--delete storage` alternativet**

Användaren uppmanas att bekräfta borttagningen.

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T221702.2535255Z
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z'.
Are you sure you want to permanently delete the snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z' from all storage volumes.? (y/n) [n]: y
Snapshot deletion completed
```

Du kan undvika användar bekräftelse genom att använda den valfria parametern på `--force` följande sätt:

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T222201.4988618Z --force
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T222201.4988618Z'.
Snapshot deletion completed
```

## <a name="next-steps"></a>Nästa steg

- [Hämta information om ögonblicks bild](azacsnap-cmd-ref-details.md)
- [Gör en säkerhets kopia](azacsnap-cmd-ref-backup.md)
