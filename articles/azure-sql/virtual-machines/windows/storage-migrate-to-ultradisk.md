---
title: Migrera logg disken till Ultra disk
description: Lär dig hur du migrerar din SQL Server på logg disken för virtuella Azure-datorer (VM) till en Azure-Ultradisk för att dra nytta av hög prestanda och låg latens.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/09/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: e8410b4e0997798eba5ee91f361c3a5f1ce47ef1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586309"
---
# <a name="migrate-log-disk-to-ultra-disk"></a>Migrera logg disken till Ultra disk
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure Ultra disks levererar högt data flöde, hög IOPS och konsekvent låg latens disk lagring för SQL Server på den virtuella Azure-datorn. 

I den här artikeln lär du dig att migrera logg disken till en ultra SSD för att dra nytta av de prestanda fördelar som erbjuds av Ultra disks. 

## <a name="back-up-database"></a>Säkerhetskopiera databasen

Slutför en [fullständig säkerhets kopiering](backup-restore.md) av databasen. 

## <a name="attach-disk"></a>Bifoga disk

Koppla Ultra SSD till den virtuella datorn när du har aktiverat ultradisk-kompatibilitet på den virtuella datorn. 

Ultra disk stöds på en delmängd av VM-storlekar och-regioner. Innan du fortsätter kontrollerar du att den virtuella datorn finns i en region, zon och storlek som har stöd för Ultra disk. Du kan [bestämma och verifiera storlek och region för virtuella datorer](../../../virtual-machines/disks-enable-ultra-ssd.md#determine-vm-size-and-region-availability) med hjälp av Azure CLI eller PowerShell. 

### <a name="enable-compatibility"></a>Aktivera kompatibilitet

Följ dessa steg om du vill aktivera kompatibilitet:

1. Gå till den virtuella datorn i [Azure Portal](https://portal.azure.com/). 
1. Stoppa/frigör den virtuella datorn. 
1. Välj **diskar** under **Inställningar** och välj sedan **ytterligare inställningar**. 

   :::image type="content" source="media/storage-migrate-to-ultradisk/additional-disks-settings-azure-portal.png" alt-text="Välj ytterligare inställningar för diskar under Inställningar i Azure Portal":::

1. Välj **Ja** om du vill **Aktivera Ultra disk-kompatibilitet**. 

   :::image type="content" source="../../../virtual-machines/media/virtual-machines-disks-getting-started-ultra-ssd/enable-ultra-disks-existing-vm.png" alt-text="Skärm bild som visar alternativet Ja.":::

1. Välj **Spara**. 



### <a name="attach-disk"></a>Bifoga disk

Använd Azure Portal för att ansluta en Ultra disk till den virtuella datorn. Mer information finns i [bifoga en Ultra disk](../../../virtual-machines/disks-enable-ultra-ssd.md#attach-an-ultra-disk).

När disken är ansluten startar du den med hjälp av Azure Portal. 



## <a name="format-disk"></a>Formatera disk

Anslut till din virtuella dator och formatera din Ultra disk.  

Följ dessa steg om du vill formatera din Ultra disk:

1. Anslut till din virtuella dator med hjälp av Remote Desktop Protocol (RDP).
1. Använd [disk hantering](/windows-server/storage/disk-management/overview-of-disk-management) för att formatera och partitionera den nyligen anslutna Ultra-disken. 


## <a name="use-disk-for-log"></a>Använd disk för logg

Konfigurera SQL Server att använda den nya logg enheten. Du kan göra det med hjälp av Transact-SQL (T-SQL) eller SQL Server Management Studio (SSMS). Kontot som används för SQL Server tjänst kontot måste ha fullständig kontroll över den nya logg fils platsen. 

### <a name="configure-permissions"></a>Konfigurera behörigheter

1. Verifiera det tjänst konto som används av SQL Server. Du kan göra detta med hjälp av Konfigurationshanteraren för SQL Server eller Services. msc.
1. Navigera till den nya disken. 
1. Skapa en mapp (eller flera mappar) som ska användas för logg filen. 
1. Högerklicka på mappen och välj **Egenskaper**.
1. På fliken **säkerhet** beviljar du fullständig behörighet till SQL Server tjänst konto. 
1. Välj **OK**  för att spara inställningarna. 
1. Upprepa detta för varje mapp på rotnivå där du planerar att ha SQL-data. 

### <a name="use-new-log-drive"></a>Använd ny logg enhet 

När behörigheten har beviljats använder du antingen Transact-SQL (T-SQL) eller SQL Server Management Studio (SSMS) för att koppla från databasen och flytta befintliga loggfiler till den nya platsen.

   > [!CAUTION]
   > Att koppla från databasen tar den offline, stänger anslutningar och rullar tillbaka alla transaktioner som är i flygning. Fortsätt med varningen och under en tids period där underhåll pågår. 



# <a name="transact-sql-t-sql"></a>[Transact-SQL (T-SQL)](#tab/tsql)

Använd T-SQL för att flytta befintliga filer till en ny plats:

1. Anslut till databasen i SQL Server Management Studio och öppna ett **nytt frågefönster** . 
1. Hämta befintliga filer och platser:

   ```sql
   USE AdventureWorks
   GO

   sp_helpfile
   GO
   ```

1. Koppla från databasen: 

   ```sql
   USE master
   GO

   sp_detach_db 'AdventureWorks'
   GO
   ```

1. Använd Utforskaren för att flytta logg filen till den nya platsen på den Ultra disken. 

1. Anslut databasen och ange de nya fil Sök vägarna: 

   ```sql
    sp_attach_db 'AdventureWorks'
   'E:\Fixed_FG\AdventureWorks.mdf',
   'E:\Fixed_FG\AdventureWorks_2.ndf',
   'F:\New_Log\AdventureWorks_log.ldf'
   GO
   ```

I det här läget kommer databasen att vara online med loggen på den nya platsen. 



# <a name="sql-server-management-studio-ssms"></a>[SQL Server Management Studio (SSMS)](#tab/ssms)

Använd SSMS för att flytta befintliga filer till en ny plats:

1. Anslut till databasen i SQL Server Management Studio (SSMS). 
1. Högerklicka på databasen, Välj **Egenskaper** och välj sedan **filer**. 
1. Anteckna sökvägen till de befintliga filerna. 
1. Välj **OK** för att stänga dialogrutan. 
1. Högerklicka på databasen, Välj **aktiviteter**  >  **Koppla från**. 
1. Ta bort databasen genom att följa guiden. 
1. Använd Utforskaren för att manuellt flytta logg filen till den nya platsen.
1. Bifoga databasen i SQL Server Management Studio
   1. Högerklicka på **databaser** i **Object Explorer** och välj **bifoga databas**. 
   1. Använd dialog rutan för att lägga till varje fil, inklusive logg filen på den nya platsen. 
   1. Välj **OK** för att bifoga databasen. 

I det här läget kommer databasen att vara online med loggen på den nya platsen.

---


## <a name="next-steps"></a>Nästa steg

Granska [prestanda metod tips](performance-guidelines-best-practices.md) för ytterligare inställningar för att förbättra prestandan. 

En översikt över SQL Server på Azure Virtual Machines finns i följande artiklar:

- [Översikt över SQL Server på virtuella Windows-datorer](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Översikt över SQL Server på Linux virtuella datorer](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
