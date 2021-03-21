---
title: Säkerhetskopiera en SQL Server VM från fönstret VM
description: I den här artikeln får du lära dig hur du säkerhetskopierar SQL Server databaser på virtuella Azure-datorer från fönstret VM.
ms.topic: conceptual
ms.date: 08/13/2020
ms.openlocfilehash: 4f4ea202ee96e93a621c8dd0025c9ebc8b8d445d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88891665"
---
# <a name="back-up-a-sql-server-from-the-vm-pane"></a>Säkerhetskopiera en SQL Server från fönstret VM

Den här artikeln beskriver hur du säkerhetskopierar SQL Server som körs i virtuella Azure-datorer med tjänsten [Azure Backup](backup-overview.md) . Du kan säkerhetskopiera SQL Server virtuella datorer på två sätt:

- Single SQL Server virtuell Azure-dator: instruktionerna i den här artikeln beskriver hur du säkerhetskopierar en SQL Server VM direkt från vyn VM.
- Flera SQL Server virtuella Azure-datorer: du kan konfigurera ett Recovery Services-valv och konfigurera säkerhets kopiering för flera virtuella datorer. Följ anvisningarna i [den här artikeln](backup-sql-server-database-azure-vms.md) för det scenariot.

## <a name="before-you-start"></a>Innan du börjar

1. Verifiera din miljö med [support mat ris](sql-support-matrix.md).
2. Få en [Översikt](backup-azure-sql-database.md) över Azure Backup för SQL Server VM.
3. Kontrol lera att den virtuella datorn är [ansluten till nätverket](backup-sql-server-database-azure-vms.md#establish-network-connectivity).

## <a name="configure-backup-on-the-sql-server"></a>Konfigurera säkerhets kopiering på SQL Server

Du kan aktivera säkerhets kopiering på SQL Server VM från **säkerhets kopierings** fönstret på den virtuella datorn. Den här metoden gör två saker:

- Registrerar den virtuella SQL-datorn med Azure Backup tjänsten för att ge den åtkomst.
- Skyddar alla SQL Server instanser som körs inuti den virtuella datorn. Det innebär att säkerhets kopierings principen tillämpas på alla befintliga databaser, samt de databaser som kommer att läggas till i dessa instanser i framtiden.

1. Välj banderollen överst på sidan för att öppna vyn SQL Server säkerhets kopia.

    ![SQL Server säkerhets kopierings vy](./media/backup-sql-server-vm-from-vm-pane/sql-server-backup-view.png)

    >[!NOTE]
    >Ser du inte banderollen? Banderollen visas bara för de SQL Server virtuella datorer som skapas med Azure Marketplace-avbildningar. Den visas också för de virtuella datorer som skyddas med Azure VM-säkerhetskopiering. För andra avbildningar kan du konfigurera säkerhets kopieringen enligt beskrivningen [här](backup-sql-server-database-azure-vms.md).

2. Ange namnet på Recovery Services valvet. Ett valv är en logisk enhet för att lagra och hantera alla dina säkerhets kopior. Om du skapar ett nytt valv:

    - Den kommer att skapas i samma prenumeration och region som SQL Server VM som du skyddar.
    - Den kommer att skapas med inställningen Geo-redundant lagring (GRS) för alla säkerhets kopieringar. Om du vill ändra typen av redundans bör du göra det innan du skyddar den virtuella datorn. Mer information finns i [den här artikeln](backup-create-rs-vault.md#set-storage-redundancy).

3. Välj en **säkerhets kopierings princip**. Du kan välja från standard principen eller andra befintliga principer som du har skapat i valvet. Om du vill skapa en ny princip kan du läsa [den här artikeln](backup-sql-server-database-azure-vms.md#create-a-backup-policy) för en steg-för-steg-guide.

    ![Välj en säkerhetskopieringsprincip](./media/backup-sql-server-vm-from-vm-pane/backup-policy.png)

4. Välj **Aktivera säkerhetskopiering**. Åtgärden kan ta några minuter att slutföra.

    ![Välj Aktivera säkerhets kopiering](./media/backup-sql-server-vm-from-vm-pane/enable-backup.png)

5. När åtgärden har slutförts visas **valv namnet** i banderollbilden.

    ![Valv namn visas i banderollen](./media/backup-sql-server-vm-from-vm-pane/vault-name.png)

6. Välj banderollen för att gå vyn valv där du kan se alla registrerade virtuella datorer och deras skydds status.

    ![Valv visning med registrerade virtuella datorer](./media/backup-sql-server-vm-from-vm-pane/vault-view.png)

7. För icke-Marketplace-avbildningar kan registreringen lyckas, men **Konfigurera säkerhets kopiering** kanske inte utlöses förrän Azure Backup-tillägget har fått behörighet för SQL Server. I sådana fall är kolumnen för **säkerhets kopiering** **inte klar**. Du måste [tilldela rätt behörigheter](backup-azure-sql-database.md#set-vm-permissions) manuellt för avbildningar som inte är Marketplace-avbildningar så att du kan aktivera Konfigurera säkerhets kopiering.

    ![Beredskap för säkerhets kopiering är inte klar](./media/backup-sql-server-vm-from-vm-pane/backup-readiness-not-ready.png)

8. För ytterligare åtgärder eller övervakning som du behöver göra på säkerhetskopierade SQL Server VM går du till motsvarande Recovery Services valv. Gå till **säkerhets kopierings objekt** om du vill se alla databaser som har säkerhetskopierats i det här valvet och utlösa åtgärder som säkerhets kopiering och återställning på begäran. På samma sätt går du till **säkerhets kopierings jobb** för att [övervaka](manage-monitor-sql-database-backup.md) jobb som motsvarar åtgärder, till exempel konfigurera skydd, säkerhets kopiering och återställning.

    ![Se säkerhetskopierade databaser i säkerhets kopierings objekt](./media/backup-sql-server-vm-from-vm-pane/backup-items.png)

>[!NOTE]
>Säkerhets kopieringen konfigureras inte automatiskt på någon av de nya SQL Server instanser som kan läggas till senare i den skyddade virtuella datorn. Om du vill konfigurera säkerhets kopiering på de nyligen tillagda instanserna måste du gå till valvet som den virtuella datorn är registrerad på och följa stegen som visas [här](backup-sql-server-database-azure-vms.md).

## <a name="next-steps"></a>Nästa steg

Lär dig att:

- [Återställa säkerhetskopierade SQL Server-databaser](restore-sql-database-azure-vm.md)
- [Hantera säkerhetskopierade SQL Server-databaser](manage-monitor-sql-database-backup.md)
