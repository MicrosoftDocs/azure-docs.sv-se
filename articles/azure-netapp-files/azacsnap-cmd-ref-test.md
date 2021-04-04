---
title: Testa Azure Application ett konsekvent ögonblicks bilds verktyg för Azure NetApp Files | Microsoft Docs
description: Förklarar hur du kör test kommandot för Azure Application konsekvent ögonblicks bild verktyget som du kan använda med Azure NetApp Files.
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
ms.openlocfilehash: 03ffa93a71ded40e033f2068ea23fc6b994a5bbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97632816"
---
# <a name="test-azure-application-consistent-snapshot-tool-preview"></a>Testa Azure Application konsekvent ögonblicks bild verktyget (förhands granskning)

Den här artikeln beskriver hur du kör test kommandot för Azure Application konsekvent ögonblicks bild verktyget som du kan använda med Azure NetApp Files.

## <a name="introduction"></a>Introduktion

Ett test av konfigurationen görs med hjälp av `azacsnap -c test` kommandot.

## <a name="command-options"></a>Kommando alternativ

`-c test`Kommandot har följande alternativ:

- `--test hana`  testar anslutningen till SAP HANA-instansen.

- `--test storage` testar kommunikationen med det underliggande lagrings gränssnittet genom att skapa en tillfällig lagrings ögonblicks bild på alla konfigurerade `data` volymer och sedan ta bort dem. 

- `--test all` utför både och- `hana` `storage` testerna i följd.

- `[--configfile <config filename>]` är en valfri parameter som tillåter anpassade konfigurations fil namn.

## <a name="check-connectivity-with-sap-hana-azacsnap--c-test---test-hana"></a>Kontrol lera anslutningen till SAP HANA `azacsnap -c test --test hana`

Det här kommandot kontrollerar HANA-anslutningen för alla HANA-instanser i konfigurations filen. Den använder HDBuserstore för att ansluta till SYSTEMDB och hämtar SID-informationen.

För SSL kan detta kommando ta med följande valfria argument:

- `--ssl=` tvingar en krypterad anslutning till databasen och definierar krypterings metoden som används för att kommunicera med SAP HANA, antingen `openssl` eller `commoncrypto` . Om detta är definierat förväntar detta kommando att hitta två filer i samma katalog. filerna måste ha namnet efter motsvarande SID. Se [använda SSL för kommunikation med SAP HANA](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana).

### <a name="output-of-the-azacsnap--c-test---test-hana-command"></a>`azacsnap -c test --test hana`Kommandots utdata

```bash
azacsnap -c test --test hana
```

```output
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
PASSED: Successful connectivity to HANA version 2.00.032.00.1533114046
END   : Test process complete for 'hana'
```

## <a name="check-connectivity-with-storage-azacsnap--c-test---test-storage"></a>Kontrol lera anslutningen till lagringen `azacsnap -c test --test storage`

`azacsnap`Kommandot tar en ögonblicks bild för alla data volymer som har kon figurer ATS för att verifiera att den har rätt åtkomst till volymerna för varje SAP HANA instans. En tillfällig ögonblicks bild skapas och tas sedan bort för varje data volym för att kontrol lera ögonblicks bilds åtkomst för varje fil system.

### <a name="output-of-the-azacsnap--c-test---test-storage-command"></a>`azacsnap -c test --test storage`Kommandots utdata

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 2 task(s) to Test Snapshots for Storage Volume Type 'data'
PASSED: Task#2/2 Storage test successful for Volume
PASSED: Task#1/2 Storage test successful for Volume
END   : Storage tests complete
END   : Test process complete for 'storage'
```

> [!NOTE]
> För Azure Large instance `azacsnap -c test --test storage` extrapolerar kommandot lagrings generation och HLI SKU.  Baserat på den här informationen ger du vägledning om hur du konfigurerar "boot"-ögonblicks bilder (se raden som börjar med `Action:` utdata).

```output
SID1   : Generation 4
Storage: ams07-a700s-saphan-1-01v250-client25-nprod
HLI SKU: S96
Action : Configure the 'boot' snapshots on ALL the servers.
```

## <a name="next-steps"></a>Nästa steg

- [Ögonblicks bild säkerhets kopiering med Azure Application konsekvent ögonblicks bild verktyget](azacsnap-cmd-ref-backup.md)
