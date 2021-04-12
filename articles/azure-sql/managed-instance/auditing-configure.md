---
title: SQL-hanterad instans granskning
description: Lär dig hur du kommer igång med Azure SQL-hanterad instans granskning med T-SQL
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 05/26/2020
ms.openlocfilehash: ae0d9696d869b2a260de643482a9f86c34bcc824
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575461"
---
# <a name="get-started-with-azure-sql-managed-instance-auditing"></a>Kom igång med granskning av Azure SQL Managed instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Azure SQL-hanterad instans](sql-managed-instance-paas-overview.md) granskning spårar databas händelser och skriver dem till en Gransknings logg i ditt Azure Storage-konto. Granskning gör även följande:

- Det hjälper dig att upprätthålla regelefterlevnad, förstå databasaktiviteter och få insikter om i avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser.
- Det främjar och underlättar uppfyllandet av efterlevnadsstandarder, även om det inte garanterar efterlevnad. Mer information om Azure-program som stöder standardkompatibilitet finns i [Azure Säkerhetscenter](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), där du hittar den mest aktuella listan med certifieringar för regelefterlevnad.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Konfigurera granskning av servern till Azure Storage

I följande avsnitt beskrivs konfigurationen av granskning på din hanterade instans.

1. Gå till [Azure-portalen](https://portal.azure.com).
2. Skapa en Azure Storage- **behållare** där gransknings loggar lagras.

   1. Navigera till det Azure Storage-konto där du vill lagra gransknings loggarna.

      > [!IMPORTANT]
      > - Använd ett lagrings konto i samma region som den hanterade instansen för att undvika läsning/skrivningar över flera regioner. 
      > - Om ditt lagrings konto ligger bakom en Virtual Network eller en brand vägg kan du se [bevilja åtkomst från ett virtuellt nätverk](../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network).
      > - Om du ändrar kvarhållningsperioden från 0 (obegränsad kvarhållning) till ett annat värde, Observera att kvarhållning endast kommer att gälla för loggar som skrivits efter att kvarhållning har ändrats (loggar som skrivits under perioden när kvarhållning hade värdet obegränsat bevaras, även efter att kvarhållning har Aktiver ATS).

   1. I lagrings kontot går du till **Översikt** och klickar på **blobbar**.

      ![Widgeten Azure-blobbar](./media/auditing-configure/1_blobs_widget.png)

   1. I den översta menyn klickar du på **+ container** för att skapa en ny behållare.

      ![Ikon för att skapa BLOB-behållare](./media/auditing-configure/2_create_container_button.png)

   1. Ange ett behållar **namn**, ange **offentlig åtkomst nivå** till **privat** och klicka sedan på **OK**.

      ![Skapa konfiguration av BLOB-behållare](./media/auditing-configure/3_create_container_config.png)

    > [!IMPORTANT]
    > Kunder som vill konfigurera ett oåterkalleligt logg Arkiv för deras server-eller databas nivå gransknings händelser bör följa [anvisningarna i Azure Storage](../../storage/blobs/storage-blob-immutability-policies-manage.md#enabling-allow-protected-append-blobs-writes). (Kontrol lera att du har valt **Tillåt ytterligare tillägg** när du konfigurerar den oföränderliga blob-lagringen.)
  
3. När du har skapat behållaren för gransknings loggarna finns det två sätt att konfigurera den som mål för gransknings loggarna: [använda T-SQL](#blobtsql) eller [använda SQL Server Management Studio (SSMS)-gränssnittet](#blobssms):

   - <a id="blobtsql"></a>**Konfigurera Blob Storage för gransknings loggar med T-SQL:**

     1. I listan behållare klickar du på den nyligen skapade behållaren och klickar sedan på **Egenskaper för behållare**.

        ![Egenskaper för BLOB container](./media/auditing-configure/4_container_properties_button.png)

     1. Kopiera behållar-URL: en genom att klicka på kopierings ikonen och spara URL: en (till exempel i anteckningar) för framtida användning. URL-formatet för behållare ska vara `https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![URL för kopia av BLOB-behållare](./media/auditing-configure/5_container_copy_name.png)

     1. Generera en Azure Storage **SAS-token** för att bevilja gransknings behörighet för hanterade instanser till lagrings kontot:

        - Navigera till det Azure Storage-konto där du skapade behållaren i föregående steg.

        - Klicka på **signatur för delad åtkomst** på menyn **lagrings inställningar** .

          ![Ikon för signatur för delad åtkomst i menyn lagrings inställningar](./media/auditing-configure/6_storage_settings_menu.png)

        - Konfigurera SAS på följande sätt:

          - **Tillåtna tjänster**: BLOB

          - **Start datum**: Använd igår datum för att undvika problem med tids zoner

          - **Slutdatum**: Välj det datum då SAS-token upphör att gälla

            > [!NOTE]
            > Förnya token när den upphör att gälla för att undvika gransknings problem.

          - Klicka sedan på **Generera signatur för delad åtkomst (SAS)**.

            ![SAS-konfiguration](./media/auditing-configure/7_sas_configure.png)

        - SAS-token visas längst ned. Kopiera token genom att klicka på kopierings ikonen och spara den (till exempel i anteckningar) för framtida användning.

          ![Kopiera SAS-token](./media/auditing-configure/8_sas_copy.png)

          > [!IMPORTANT]
          > Ta bort frågetecknet ("?") från början av token.

     1. Anslut till din hanterade instans via SQL Server Management Studio eller något annat verktyg som stöds.

     1. Kör följande T-SQL-instruktion för att **skapa en ny autentiseringsuppgift** med URL: en och SAS-token som du skapade i föregående steg:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Kör följande T-SQL-instruktion för att skapa en ny server granskning (Välj ditt eget gransknings namn och Använd URL: en för den behållare som du skapade i föregående steg). Om inget `RETENTION_DAYS` värde anges är standardvärdet 0 (obegränsad kvarhållning):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' , RETENTION_DAYS =  integer )
        GO
        ```

     1. Fortsätt genom att [skapa en server gransknings specifikation eller databas gransknings specifikation](#createspec).

   - <a id="blobssms"></a>**Konfigurera Blob Storage för gransknings loggar med SQL Server Management Studio 18:**

     1. Anslut till den hanterade instansen med hjälp av SQL Server Management Studio gränssnittet.

     1. Expandera rot anteckningen för Object Explorer.

     1. Expandera noden **säkerhet** , högerklicka på noden **granskningar** och klicka på **ny granskning**:

        ![Expandera noden säkerhet och granskning](./media/auditing-configure/10_mi_SSMS_new_audit.png)

     1. Kontrol lera att **URL** är markerat i **Granska mål** och klicka på **Bläddra**:

        ![Bläddra Azure Storage](./media/auditing-configure/11_mi_SSMS_audit_browse.png)

     1. Valfritt Logga in på ditt Azure-konto:

        ![Logga in på Azure](./media/auditing-configure/12_mi_SSMS_sign_in_to_azure.png)

     1. Välj en prenumeration, ett lagrings konto och en BLOB-behållare i list rutorna eller skapa en egen behållare genom att klicka på **skapa**. När du är färdig klickar du på **OK**:

        ![Välj Azure-prenumeration, lagrings konto och blob-behållare](./media/auditing-configure/13_mi_SSMS_select_subscription_account_container.png)

     1. Klicka på **OK** i dialog rutan **skapa granskning** .
     
     1. <a id="createspec"></a>När du har konfigurerat BLOB-behållaren som mål för gransknings loggarna skapar du och aktiverar en server gransknings specifikation eller databas gransknings specifikation på samma sätt som för SQL Server:

   - [Skapa server gransknings specifikation T-SQL-guide](/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Skapa databas gransknings specifikation T-SQL-guide](/sql/t-sql/statements/create-database-audit-specification-transact-sql)

5. Aktivera server granskningen som du skapade i steg 3:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Ytterligare information:

- [Gransknings skillnader mellan Azure SQL-hanterad instans och en databas i SQL Server](#auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server)
- [SKAPA SERVER GRANSKNING](/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hubs-or-azure-monitor-logs"></a>Konfigurera granskning av servern till Event Hubs eller Azure Monitor loggar

Gransknings loggar från en hanterad instans kan skickas till Azure Event Hubs eller Azure Monitor loggar. I det här avsnittet beskrivs hur du konfigurerar detta:

1. Navigera i [Azure Portal](https://portal.azure.com/) till den hanterade instansen.

2. Klicka på **diagnostiska inställningar**.

3. Klicka på **Aktivera diagnostik**. Om diagnostik redan har Aktiver ATS visas **+ Lägg till diagnostisk inställning** i stället.

4. Välj **SQLSecurityAuditEvents** i listan över loggar.

5. Välj ett mål för gransknings händelserna: Event Hubs, Azure Monitor loggar eller båda. Konfigurera för varje mål nödvändiga parametrar (t. ex. Log Analytics arbets yta).

6. Klicka på **Spara**.

    ![Konfigurera diagnostikinställningar](./media/auditing-configure/9_mi_configure_diagnostics.png)

7. Anslut till den hanterade instansen med hjälp av **SQL Server Management Studio (SSMS)** eller någon annan klient som stöds.

8. Kör följande T-SQL-instruktion för att skapa en server granskning:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Skapa och aktivera en server gransknings specifikation eller databas gransknings specifikation på samma sätt som för SQL Server:

   - [Skapa server gransknings specifikation T-SQL-guide](/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Skapa databas gransknings specifikation T-SQL-guide](/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Aktivera server granskningen som skapades i steg 8:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Använda gransknings loggar

### <a name="consume-logs-stored-in-azure-storage"></a>Använda loggar som lagras i Azure Storage

Det finns flera metoder som du kan använda för att Visa BLOB gransknings loggar.

- Använd system funktionen `sys.fn_get_audit_file` (T-SQL) för att returnera Gransknings logg data i tabell format. Mer information om hur du använder den här funktionen finns i [sys.fn_get_audit_file-dokumentationen](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Du kan utforska gransknings loggar med hjälp av ett verktyg som [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). I Azure Storage sparas gransknings loggar som en samling BLOB-filer i en behållare som har definierats för att lagra gransknings loggarna. Mer information om hierarkin för lagringsmappen, namngivnings konventioner och logg format finns i [referensen för logg format för BLOB-granskning](../database/audit-log-format.md).

- En fullständig lista över användnings metoder för gransknings loggen finns i [komma igång med Azure SQL Database granskning](../../azure-sql/database/auditing-overview.md).

### <a name="consume-logs-stored-in-event-hubs"></a>Använda loggar som lagras i Event Hubs

Om du vill använda gransknings loggar från Event Hubs måste du konfigurera en data ström för att använda händelser och skriva dem till ett mål. Mer information finns i Azure Event Hubs-dokumentationen.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Använda och analysera loggar som lagras i Azure Monitor loggar

Om gransknings loggar skrivs till Azure Monitor loggar, är de tillgängliga i arbets ytan Log Analytics där du kan köra avancerade sökningar på gransknings data. Som start punkt navigerar du till arbets ytan Log Analytics. Under avsnittet **Allmänt** klickar du på **loggar** och anger en enkel fråga, till exempel: `search "SQLSecurityAuditEvents"` för att Visa gransknings loggarna.  

Med Azure Monitor loggar får du operativa insikter i real tid med integrerad sökning och anpassade instrument paneler för att enkelt analysera miljon tals poster över alla dina arbets belastningar och servrar. Mer värdefull information om Azure Monitor loggar Sök språk och-kommandon finns i [Sök referens för Azure Monitor loggar](../../azure-monitor/logs/log-query-overview.md).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server"></a>Granska skillnader mellan databaser i Azure SQL-hanterad instans och databaser i SQL Server

De viktigaste skillnaderna mellan granskning i databaser i Azure SQL-hanterad instans och databaser i SQL Server är:

- Med Azure SQL Managed instance fungerar granskning på server nivå och lagrar `.xel` loggfiler i Azure Blob Storage.
- I SQL Server fungerar granskning på server nivå, men lagrar händelser på filer system/Windows-händelseloggar.

XEvent-granskning i hanterade instanser stöder Azure Blob Storage-mål. Fil-och Windows-loggar **stöds inte**.

Viktiga skillnader i `CREATE AUDIT` syntaxen för granskning till Azure Blob Storage är:

- En ny syntax anges `TO URL` och du kan ange URL: en för den Azure Blob Storage-behållare där `.xel` filerna placeras.
- Det finns en ny syntax `TO EXTERNAL MONITOR` för att aktivera Event Hubs och Azure Monitor logga mål.
- Syntaxen `TO FILE` **stöds inte** eftersom den HANTERAde Azure SQL-instansen inte kan komma åt Windows-filresurser.
- Avslutnings alternativet **stöds inte**.
- `queue_delay` av 0 **stöds inte**.

## <a name="next-steps"></a>Nästa steg

- En fullständig lista över användnings metoder för gransknings loggen finns i [komma igång med Azure SQL Database granskning](../../azure-sql/database/auditing-overview.md).
- Mer information om Azure-program som stöder standardkompatibilitet finns i [Azure Säkerhetscenter](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), där du hittar den mest aktuella listan med certifieringar för regelefterlevnad.

<!--Image references-->
