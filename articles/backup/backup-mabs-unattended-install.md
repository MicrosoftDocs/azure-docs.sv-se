---
title: Tyst installation av Azure Backup Server v2
description: Använd ett PowerShell-skript för att installera Azure Backup Server v2 obevakat. Den här typen av installation kallas även för en obevakad installation.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 1539089e713bcf8e959707c6ff4a608f062a7c00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "74172248"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Köra en obevakad installation av Azure Backup Server

Lär dig hur du kör en obevakad installation av Azure Backup Server.

De här stegen gäller inte om du installerar Azure Backup Server v1.

## <a name="install-backup-server"></a>Installera säkerhets kopierings Server

1. Skapa en textfil på den server som är värd för Azure Backup Server v2 eller senare. (Du kan skapa filen i anteckningar eller i en annan text redigerare.) Spara filen som MABSSetup.ini.

2. Klistra in följande kod i MABSSetup.ini-filen. Ersätt texten innanför hakparenteserna ( \< \> ) med värden från din miljö. Följande text är ett exempel:

   ```text
   [OPTIONS]
   UserName=administrator
   CompanyName=<Microsoft Corporation>
   SQLMachineName=localhost
   SQLInstanceName=<SQL instance name>
   SQLMachineUserName=administrator
   SQLMachinePassword=<admin password>
   SQLMachineDomainName=<machine domain>
   ReportingMachineName=localhost
   ReportingInstanceName=<reporting instance name>
   SqlAccountPassword=<admin password>
   ReportingMachineUserName=<username>
   ReportingMachinePassword=<reporting admin password>
   ReportingMachineDomainName=<domain>
   VaultCredentialFilePath=<vault credential full path and complete name>
   SecurityPassphrase=<passphrase>
   PassphraseSaveLocation=<passphrase save location>
   UseExistingSQL=<1/0 use or do not use existing SQL>
   ```

3. Spara filen. Ange sedan följande kommando i en upphöjd kommando tolk på installations servern:

   ```cmd
   start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
   ```

Du kan använda dessa flaggor för installationen:</br>
**/f**:. ini fil Sök väg</br>
**/l**: logg Sök väg</br>
**/i**: installations Sök väg</br>
**/x**: Avinstallera sökväg</br>

## <a name="next-steps"></a>Nästa steg

När du har installerat säkerhets kopierings servern lär du dig hur du förbereder servern eller börjar skydda en arbets belastning.

- [Förbered arbets belastningar för säkerhets kopierings Server](backup-azure-microsoft-azure-backup.md)
- [Säkerhetskopiera en VMware-server med hjälp av Backup Server](backup-azure-backup-server-vmware.md)
- [Säkerhetskopiera SQL Server med hjälp av Backup Server](backup-azure-sql-mabs.md)
- [Lägga till Modern Backup Storage på Backup Server](backup-mabs-add-storage.md)
