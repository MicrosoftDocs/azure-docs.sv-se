---
title: Viktig information för Microsoft Azure Backup Server v3
description: Den här artikeln innehåller information om kända problem och lösningar för Microsoft Azure Backup Server (MABS) v3.
ms.topic: conceptual
ms.date: 06/03/2020
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: 87bc415c125a387d98ac88255d77fb1867564acf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91254269"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Viktig information för Microsoft Azure Backup Server

Den här artikeln innehåller kända problem och lösningar för Microsoft Azure Backup Server (MABS) v3.

## <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Säkerhets kopiering och återställning Miss lyckas för klustrade arbets belastningar

**Beskrivning:** Säkerhets kopiering/återställning Miss lyckas för klustrade data källor som Hyper-V-kluster eller SQL-kluster (alltid SQL Always on) eller Exchange i databasen tillgänglighets grupp (DAG) efter uppgradering av MABS v2 till MABS v3.

Lösning **:** Förhindra detta genom att öppna SQL Server Management Studio (SSMS)) och köra följande SQL-skript på DPM-databasen:

```sql
    IF EXISTS (SELECT * FROM dbo.sysobjects
        WHERE id = OBJECT_ID(N'[dbo].[tbl_PRM_DatasourceLastActiveServerMap]')
        AND OBJECTPROPERTY(id, N'IsUserTable') = 1)
        DROP TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap]
        GO

        CREATE TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] (
            [DatasourceId]          [GUID]          NOT NULL,
            [ActiveNode]            [nvarchar](256) NULL,
            [IsGCed]                [bit]           NOT NULL
            ) ON [PRIMARY]
        GO

        ALTER TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] ADD
    CONSTRAINT [pk__tbl_PRM_DatasourceLastActiveServerMap__DatasourceId] PRIMARY KEY NONCLUSTERED
        (
            [DatasourceId]
        )  ON [PRIMARY],

    CONSTRAINT [DF_tbl_PRM_DatasourceLastActiveServerMap_IsGCed] DEFAULT
        (
            0
        ) FOR [IsGCed]
    GO
```

## <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>Det går inte att uppgradera till MABS v3 på ryskt språk

**Beskrivning:** Det går inte att uppgradera från MABS v2 till MABS v3 på den ryska språkvarianten med felkod **4387**.

Lösning **:** Utför följande steg för att uppgradera till MABS v3 med installations paketet för ryska:

1. [Säkerhetskopiera](/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server#SSMSProcedure) SQL-databasen och avinstallera Mabs v2 (Välj att behålla skyddade data under avinstallationen).
2. Uppgradera till SQL 2017 (Enterprise) och avinstallera rapportering som en del av uppgraderingen.
3. [Installera](/sql/reporting-services/install-windows/install-reporting-services#install-your-report-server) SQL Server Reporting Services (SSRS).
4. [Installera](/sql/ssms/download-sql-server-management-studio-ssms) SQL Server Management Studio (SSMS).
5. Konfigurera rapportering med parametrarna enligt beskrivningen i [SSRS-konfigurationen med SQL 2017](./backup-azure-microsoft-azure-backup.md#upgrade-mabs).
6. [Installera](backup-azure-microsoft-azure-backup.md) MABS V3.
7. [Återställ](/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms) SQL med SSMS och kör DPM-Sync verktyg som beskrivs [här](/system-center/dpm/back-up-the-dpm-server#using-dpmsync).
8. Uppdatera egenskapen ' DataBaseVersion ' i dbo.tbl_DLS_GlobalSetting tabell med hjälp av följande kommando:

    ```sql
            UPDATE dbo.tbl_DLS_GlobalSetting
            set PropertyValue = '13.0.415.0'
            where PropertyName = 'DatabaseVersion'
    ```

9. Starta MSDPM-tjänsten.

## <a name="after-installing-ur1-the-mabs-reports-arent-updated-with-new-rdl-files"></a>När du har installerat UR1 uppdateras MABS-rapporterna inte med nya RDL-filer

**Beskrivning**: med UR1 åtgärdas problemet med Mabs rapport formatering med uppdaterade RDL-filer. De nya RDL-filerna ersätts inte automatiskt med befintliga filer.

**Lösning**: om du vill ersätta RDL-filerna följer du stegen nedan:

1. Öppna SQL Reporting Services-webbportalens URL på MABS-datorn.
1. I webb portalens URL finns mappen katalogen DpmReports i formatet **`DPMReports_<GUID>`**

    >[!NOTE]
    >Det finns alltid en mapp med denna namngivnings konvention. Om MABS har uppgraderats från en tidigare version kan det också finnas en annan äldre mapp, men du kan inte öppna den.

    ![Katalogen DpmReports-mapp](./media/backup-mabs-release-notes-v3/dpm-reports-folder.png)

1. Välj och öppna **`DPMReports_<GUID>`** mappen. De enskilda uppdateringsfilerna visas på det sätt som visas nedan.

    ![Lista med enskilda rapportmallar](./media/backup-mabs-release-notes-v3/individual-report-files.png)

1. Markera de uppdateringsfiler som inte slutar med **rapporten**, högerklicka på **alternativet** och välj **Hantera**.

    ![Välj Hantera för rapportmapp](./media/backup-mabs-release-notes-v3/manage-files.png)

1. På sidan ny väljer du alternativet **Ersätt** för att ersätta filerna med de senaste uppdateringsfilerna.

    Du hittar de senaste uppdateringsfilerna i sökvägen `<MABS Installation Directory>\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports`

    Exempelvis: `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports`

    ![Ersätt filerna med de senaste uppdateringsfilerna](./media/backup-mabs-release-notes-v3/replace-files.png)

    När filerna har ersatts kontrollerar du att **namn** och **Beskrivning** är intakt och inte är tomt.

1. När filerna har ersatts startar du om MABS-tjänsterna och använder dem.

## <a name="next-steps"></a>Nästa steg

[Nyheter i MABS](backup-mabs-whats-new-mabs.md)
