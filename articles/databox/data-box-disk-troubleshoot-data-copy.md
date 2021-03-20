---
title: Azure Data Box Disk fel sökning av data kopierings problem | Microsoft Docs
description: Beskriver hur du felsöker problem som visas under data kopiering i Azure Data Box Disk med hjälp av loggar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 06/13/2019
ms.author: alkohli
ms.openlocfilehash: 5d977fe0b7459af35f678e77681d3b27c31431cc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85849190"
---
# <a name="troubleshoot-data-copy-issues-in-azure-data-box-disk"></a>Felsök problem med data kopiering i Azure Data Box Disk

Den här artikeln gäller för Microsoft Azure Data Box Disk och beskriver hur du felsöker de problem som visas när du kopierar data till diskar. Artikeln innehåller också problem när du använder verktyget för delad kopiering.


## <a name="data-copy-issues-when-using-a-linux-system"></a>Problem med data kopiering vid användning av ett Linux-system

I det här avsnittet beskrivs några av de vanligaste problemen vid användning av en Linux-klient för att kopiera data till diskar.

### <a name="issue-drive-getting-mounted-as-read-only"></a>Problem: enheten kommer att monteras som skrivskyddad
 
**Orsak** 

Detta kan bero på ett rent fil system.

Att montera om en enhet som lässkyddad fungerar inte med Data Box-enhet diskar. Det här scenariot stöds inte med dekrypterade enheter. Enheten kan ha monterats om med följande kommando:

```
# mount -o remount, rw /mnt/DataBoxDisk/mountVol1
```

Även om ommonteringen lyckades behålls inte data.

**Lösning**

Utför följande steg i Linux-systemet:

1. Installera `ntfsprogs` paketet för ntfsfix-verktyget.
2. Demontera monterings punkterna som anges för enheten med hjälp av upplåsnings verktyget. Antalet monterings punkter varierar för enheter.

    ```
    unmount /mnt/DataBoxDisk/mountVol1
    ```

3. Kör `ntfsfix` på motsvarande sökväg. Det markerade talet ska vara detsamma som steg 2.

    ```
    ntfsfix /mnt/DataBoxDisk/bitlockerVol1/dislocker-file
    ```

4. Kör följande kommando för att ta bort de metadata för vilo läge som kan orsaka monterings problemet.

    ```
    ntfs-3g -o remove_hiberfile /mnt/DataBoxDisk/bitlockerVol1/dislocker-file /mnt/DataBoxDisk/mountVol1
    ```

5. Utför en rensad demontering.

    ```
    ./DataBoxDiskUnlock_x86_64 /unmount
    ```

6. Gör en ren upplåsning och montering.
7. Testa monterings punkten genom att skriva en fil.
8. Demontera och montera om för att validera filen persisted.
9. Fortsätt med data kopieringen.
 
### <a name="issue-error-with-data-not-persisting-after-copy"></a>Problem: fel med data som inte behålls efter kopiering
 
**Orsak** 

Om du ser att enheten inte har data när den har demonterats (även om data har kopierats till den) är det möjligt att du har monterat om en enhet som Läs-och Skriv behörighet när enheten monterats som skrivskyddad.

**Lösning**
 
I så fall, se lösningarna för [enheter som är monterade i skrivskyddat läge](#issue-drive-getting-mounted-as-read-only).

Om det inte var fallet kopierar du loggarna från den mapp som har Data Box Disk Unlock-verktyget och [kontaktar Microsoft Support](data-box-disk-contact-microsoft-support.md).


## <a name="data-box-disk-split-copy-tool-errors"></a>Fel med verktyget Data Box Disk Split Copy

Problemen som visas när du använder ett verktyg för delad kopiering för att dela data över flera diskar sammanfattas i följande tabell.

|Felmeddelande/varningar |Rekommendationer |
|---------|---------|
|Statusinformation Hämtar BitLocker-lösenord för volym: m <br>Fels Ett undantag inträffade vid hämtning av BitLocker-nyckeln för volym m:<br> Sekvensen innehåller inga element.|Det här felet utlöses om Data Box-måldisken är offline. <br> Använd verktyget `diskmgmt.msc` till onlinediskar.|
|[Fel] Ett undantag utlöstes: WMI-åtgärden misslyckades:<br> Method=UnlockWithNumericalPassword, ReturnValue=2150694965, <br>Win32Message = Formatet för det angivna återställningslösenordet är ogiltigt. <br>BitLocker-återställningslösenord är 48 siffror. <br>Kontrollera att återställningslösenordet har rätt format och försök sedan igen.|Använda verktyget Data Box Disk Unlock för att först låsa upp diskarna och prova kommandot igen. Mer information finns i <li> [Låsa upp Data Box-disk för Windows-klienter](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client). </li><li> [Låsa upp Data Box-disk för Linux-klienter](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client). </li>|
|[Fel] Ett undantag utlöstes: En DriveManifest.xml-fil finns på målenheten. <br> Detta anger att målenheten kanske har förberetts med en annan journalfil. <br>För att lägga till mer data till samma enhet använder du föregående journalfil. Om du vill ta bort befintliga data och återanvända mål enheten för ett nytt import jobb tar du bort *DriveManifest.xml* på enheten. Kör det här kommandot igen med en ny journalfil.| Det här felet tas emot när du försöker använda samma uppsättning enheter för flera importsessioner. <br> Använda endast en uppsättning enheter för endast en delnings- och kopieringssession.|
|[Fel] Ett undantag utlöstes: CopySessionId importdata-sept-test-1 refererar till en tidigare kopieringssession och kan inte återanvändas för en ny kopieringssession.|Det här felet rapporteras när du försöker använda samma jobbnamn för ett nytt jobb som använts för ett tidigare slutfört jobb.<br> Tilldela det nya jobbet ett unikt namn.|
|[Info] Målfilens eller katalogens namn överskrider NTFS-längdbegränsningen. |Det här meddelandet rapporteras när målfilen bytte namn på grund av en lång filsökväg.<br> Ändra dispositionsalternativet i `config.json`-filen för att styra det här beteendet.|
|[Fel] Ett undantag utlöses: Felaktig JSON-escape-sekvens. |Det här meddelandet rapporteras när config.json har ett format som inte är giltigt. <br> Verifiera din `config.json` med hjälp av [JSONlint](https://jsonlint.com/) innan du sparar filen.|


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [felsöker problem med validerings verktyget](data-box-disk-troubleshoot.md).
