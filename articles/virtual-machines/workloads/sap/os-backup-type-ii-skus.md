---
title: 'Säkerhets kopiering och återställning av operativ system för SAP HANA på Azure (stora instanser) typ II SKU: er | Microsoft Docs'
description: 'Utföra säkerhets kopiering och återställning av operativ system för SAP HANA på Azure (stora instanser) typ II SKU: er'
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c03aa119b40a81f553a97ec013f89f88daabf293
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668664"
---
# <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>Säkerhets kopiering och återställning av OS för typ II SKU: er av revision 3-stämplar

Det här dokumentet beskriver stegen för att utföra en säkerhets kopiering och återställning av operativ Systems fil nivå för **typ II-SKU: er** för de stora instanser av revision 3. 

>[!Important]
> **Den här artikeln gäller inte för typ II SKU-distributioner i revision 4 HANA stora instans stämplar.** Start-LUN av typ II HANA stora instans enheter som distribueras i revision 4 HANA stora instans stämplar kan säkerhets kopie ras med lagrings ögonblicks bilder som detta är fallet med typ I SKU: er som redan finns i revision 3-stämplar


>[!NOTE]
>Skripten för säkerhets kopiering av OS använder den bakre program varan, som är förinstallerad på-servern.  

När etableringen har slutförts av Microsoft `Service Management` -teamet konfigureras servern som standard med två säkerhets kopierings scheman för att säkerhetskopiera fil system nivån på operativ systemet. Du kan kontrol lera scheman för säkerhets kopierings jobben med hjälp av följande kommando:
```
#crontab –l
```
Du kan ändra schemat för säkerhets kopiering när som helst med hjälp av följande kommando:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Hur gör jag för att göra en manuell säkerhets kopiering?

Säkerhets kopieringen av operativ systemets fil system schemaläggs redan med ett **cron-jobb** . Du kan dock även utföra säkerhets kopieringen av operativ Systems fil nivån manuellt. Kör följande kommando för att utföra en manuell säkerhets kopiering:

```
#rear -v mkbackup
```
Följande skärm bild visar exempel på manuell säkerhets kopiering:

![vilken](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Så här återställer du en säkerhets kopia?

Du kan återställa en fullständig säkerhets kopia eller en enskild fil från säkerhets kopian. Använd följande kommando för att återställa:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
Efter återställningen återställs filen i den aktuella arbets katalogen.

Följande kommando visar återställningen av en fil */etc/fstabfrom* säkerhets kopian av säkerhets kopierings filen *. tar. gz*
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>Du måste kopiera filen till önskad plats efter att den har återställts från säkerhets kopian.

Följande skärm bild visar återställningen av en fullständig säkerhets kopia:

![Skärm bild som visar ett kommando tolks fönster med Restore.](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Hur du installerar bakre verktyget och ändrar konfigurationen? 

Paketen för att minska och återställa (bak) är **förinstallerade** i **typ II-SKU: er** av Hana-stora instanser och ingen åtgärd krävs från dig. Du kan direkt börja använda bak sidan för säkerhets kopieringen av operativ systemet.
I de fall där du behöver installera paketen i din egen kan du dock följa stegen i listan för att installera och konfigurera bak-verktyget.

Om du vill installera de **bakre** säkerhets kopie paketen använder du följande kommandon:

Använd följande kommando för **SLES** -operativ system:
```
#zypper install <rear rpm package>
```
Använd följande kommando för **RHEL** -operativ system: 
```
#yum install rear -y
```
Om du vill konfigurera bakre verktyget måste du uppdatera parametrarna **OUTPUT_URL**  och **BACKUP_URL**  i *filen/etc/Rear/Local.conf*.
```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

Följande skärm bild visar en fullständig säkerhets kopiering: ![ skärm bild som visar ett kommando tolks fönster med återställningen med hjälp av bakre verktyget.](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
