---
title: Använda Modern Backup Storage med Azure Backup Server
description: Lär dig mer om de nya funktionerna i Azure Backup Server. Den här artikeln beskriver hur du uppgraderar installationen av säkerhets kopierings servern.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: b077296e58e1193e454a686a392d802e905500a5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91292905"
---
# <a name="add-storage-to-azure-backup-server"></a>Lägga till lagringsutrymme på Azure Backup Server

Azure Backup Server v2 och senare har stöd för Modern Backup Storage som ger lagrings besparingar på 50 procent, säkerhets kopior som är tre gånger snabbare och mer effektiv lagring. Den erbjuder även arbets belastnings medveten lagring.

> [!NOTE]
> Om du vill använda Modern Backup Storage måste du köra backup server v2 eller v3 på Windows Server 2016 eller v3 på Windows Server 2019.
> Om du kör backup server v2 på en tidigare version av Windows Server kan Azure Backup Server inte dra nytta av Modern Backup Storage. I stället skyddas arbets belastningar på samma sätt som med säkerhets kopierings servern v1. Mer information finns i [matrisen](backup-mabs-protection-matrix.md)för säkerhets kopiering av Server versions skydd.
>
> För att uppnå förbättrade prestanda för säkerhets kopiering rekommenderar vi att du distribuerar MABS v3 med skiktad lagring på Windows Server 2019. Se DPM-artikeln "[Konfigurera MB med Tiered Storage](/system-center/dpm/add-storage#set-up-mbs-with-tiered-storage)" för steg för att konfigurera skiktat lagrings utrymme.

## <a name="volumes-in-backup-server"></a>Volymer i säkerhets kopierings Server

Backup server v2 eller senare accepterar lagrings volymer. När du lägger till en volym formaterar backup-servern volymen till elastiskt fil system (ReFS) som Modern Backup Storage kräver. Om du vill lägga till en volym och expandera den senare om du behöver det rekommenderar vi att du använder det här arbets flödet:

1. Konfigurera säkerhets kopierings server på en virtuell dator.
2. Skapa en volym på en virtuell disk i en lagringspool:
    1. Lägg till en disk i en lagringspool och skapa en virtuell disk med enkel layout.
    2. Lägg till ytterligare diskar och utöka den virtuella disken.
    3. Skapa volymer på den virtuella disken.
3. Lägg till volymerna på säkerhets kopierings servern.
4. Konfigurera arbets belastnings medveten lagring.

## <a name="create-a-volume-for-modern-backup-storage"></a>Skapa en volym för Modern Backup Storage

Genom att använda backup server v2 eller senare med volymer som disk lagring kan du behålla kontrollen över lagringen. En volym kan vara en enskild disk. Men om du vill utöka lagringen i framtiden kan du skapa en volym från en disk som skapats med hjälp av lagrings utrymmen. Detta kan hjälpa dig om du vill expandera volymen för lagring av säkerhets kopior. Det här avsnittet innehåller metod tips för att skapa en volym med den här installationen.

1. I Serverhanteraren väljer du **fil-och lagrings tjänst**  >  **volymer**  >  **lagringspooler**. Under **fysiska diskar** väljer du **ny lagringspool**.

    ![Skapa en ny lagringspool](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. I list rutan **uppgifter** väljer du **ny virtuell disk**.

    ![Lägg till en virtuell disk](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Markera lagringspoolen och välj sedan **Lägg till fysisk disk**.

    ![Lägg till en fysisk disk](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. Välj den fysiska disken och välj sedan **utöka virtuell disk**.

    ![Utöka den virtuella disken](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. Välj den virtuella disken och välj sedan **ny volym**.

    ![Skapa en ny volym](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. I dialog rutan **Välj server och disk** väljer du servern och den nya disken. Välj **Nästa**.

    ![Välj server och disk](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Lägg till volymer på säkerhets kopierings serverns disk lagring

> [!NOTE]
>
> - Lägg endast till en disk i poolen för att behålla kolumn antalet till 1. Sedan kan du lägga till diskar efter behov efteråt.
> - Om du lägger till flera diskar i lagringspoolen är antalet diskar lagrade som antalet kolumner. När du lägger till fler diskar kan de bara vara en multipel av antalet kolumner.

Om du vill lägga till en volym på säkerhets kopierings servern går du igenom **hanterings** fönstret och skannar lagringen på nytt och väljer sedan **Lägg till**. En lista över alla volymer som ska läggas till för lagring av reserv servrar visas. När de tillgängliga volymerna har lagts till i listan med valda volymer kan du ge dem ett eget namn som hjälper dig att hantera dem. Om du vill formatera dessa volymer till ReFS så att backup server kan använda fördelarna med Modern Backup Storage väljer du **OK**.

![Lägg till tillgängliga volymer](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Konfigurera arbets belastnings medveten lagring

Med arbets belastnings medveten lagring kan du välja de volymer som i stor lag lagrar vissa typer av arbets belastningar. Du kan till exempel ange dyra volymer som har stöd för ett stort antal in-/utdata-åtgärder per sekund (IOPS) för att endast lagra arbets belastningar som kräver frekventa säkerhets kopieringar med hög volym. Ett exempel är SQL Server med transaktions loggar. Andra arbets belastningar som säkerhets kopie ras mindre ofta, t. ex. virtuella datorer, kan säkerhets kopie ras till låga volymer.

### <a name="update-dpmdiskstorage"></a>Uppdate-DPMDiskStorage

Du kan ställa in arbets belastnings medveten lagring med hjälp av PowerShell-cmdlet Update-DPMDiskStorage, som uppdaterar egenskaperna för en volym i lagringspoolen på en Azure Backup Server.

Syntax:

`Parameter Set: Volume`

```powershell
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```

Följande skärm bild visar Update-DPMDiskStorage cmdlet i PowerShell-fönstret.

![Kommandot Update-DPMDiskStorage i PowerShell-fönstret](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

De ändringar du gör med hjälp av PowerShell visas i Administratörskonsol för säkerhets kopierings servern.

![Diskar och volymer i Administratörskonsol](./media/backup-mabs-add-storage/mabs-add-storage-9.png)

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrera äldre lagrings utrymme till Modern Backup Storage

När du har uppgraderat till eller installerat säkerhets kopierings Server v2 och uppgraderat operativ systemet till Windows Server 2016 uppdaterar du skydds grupperna så att de använder Modern Backup Storage. Skydds grupper ändras inte som standard. De fortsätter att fungera som de ursprungligen konfigurerades.

Uppdatering av skyddsgrupper för användning av Modern Backup Storage är valfritt. Om du vill uppdatera skydds gruppen stoppar du skyddet av alla data källor med alternativet Kvarhåll data. Lägg sedan till data källorna i en ny skydds grupp.

1. I Administratörskonsol väljer du **skydds** funktionen. I listan **skydds grupps medlem** högerklickar du på medlemmen och väljer sedan **stoppa skyddet av medlem**.

   ![Stoppa skyddet av medlem](/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. I dialog rutan **ta bort från grupp** granskar du använt disk utrymme och tillgängligt ledigt utrymme för lagringspoolen. Standardinställningen är att lämna återställningspunkterna på disken och låta dem upphöra per deras associerade bevarandeprincip. Välj **OK**.

   Om du omedelbart vill returnera det använda disk utrymmet till den kostnads fria lagringspoolen markerar du kryss rutan **ta bort replik på disk** för att ta bort de säkerhets kopierings data (och återställnings punkter) som är associerade med medlemmen.

   ![Dialog rutan ta bort från grupp](/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Skapa en skydds grupp som använder Modern Backup Storage. Inkludera oskyddade data källor.

## <a name="add-disks-to-increase-legacy-storage"></a>Lägg till diskar för att öka äldre lagrings utrymme

Om du vill använda äldre lagrings enheter med backup server kan du behöva lägga till diskar för att öka den äldre lagringen.

Lägga till disklagring:

1. I administratörskonsol väljer du **hantering**  >  **disklagring**  >  **Lägg till**.

    ![Dialog rutan Lägg till Disklagring](/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

2. I dialog rutan **Lägg till disklagring** väljer du **Lägg till diskar**.

3. I listan över tillgängliga diskar väljer du de diskar som du vill lägga till, väljer **Lägg till** och väljer sedan **OK**.

## <a name="next-steps"></a>Nästa steg

När du har installerat säkerhets kopierings servern lär du dig hur du förbereder servern eller börjar skydda en arbets belastning.

- [Förbered arbets belastningar för säkerhets kopierings Server](backup-azure-microsoft-azure-backup.md)
- [Säkerhetskopiera en VMware-server med hjälp av Backup Server](backup-azure-backup-server-vmware.md)
- [Säkerhetskopiera SQL Server med hjälp av Backup Server](backup-azure-sql-mabs.md)
