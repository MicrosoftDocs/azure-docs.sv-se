---
title: Migrera lokala SQL Server Integration Services-jobb (SSIS) till Azure Data Factory
description: I den här artikeln beskrivs hur du migrerar SQL Server Integration Services-jobb (SSIS) till Azure Data Factory pipelines/aktiviteter/utlösare med SQL Server Management Studio.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 4/7/2020
ms.openlocfilehash: 1072f915c5d8483676874422703820dc75719256
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98555754"
---
# <a name="migrate-sql-server-agent-jobs-to-adf-with-ssms"></a>Migrera SQL Server Agent jobb till ADF med SSMS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

När du migrerar [lokala SQL Server Integration Services (SSIS) arbets belastningar till SSIS i ADF](scenario-ssis-migration-overview.md), kan du utföra satsvis migrering av SQL Server Agent jobb med jobb stegs typ SQL Server Integration Services paket till Azure Data Factory (ADF) pipeline/aktiviteter/schema utlösare via SQL Server Management Studio (SSMS) SSIS-guide för **migrering av jobb**.

För utvalda SQL Agent-jobb med tillämpliga jobb stegs typer i allmänhet kan **guiden Migrera SSIS** -jobb:

- mappa lokala SSIS-paket platser till där paketen migreras till, som är tillgängliga i SSIS i ADF.
    > [!NOTE]
    > Paket platsen för fil systemet stöds bara.
- Migrera tillämpliga jobb med tillämpliga jobb steg till motsvarande ADF-resurser enligt nedan:

|Objekt för SQL Agent-jobb  |ADF-resurs  |Kommentarer|
|---------|---------|---------|
|SQL Agent-jobb|pipeline     |Namnet på pipelinen *som ska genereras för \<job name>*. <br> <br> Inbyggda Agent jobb är inte tillämpliga: <li> Underhålls jobb för SSIS-Server <li> syspolicy_purge_history <li> collection_set_ * <li> mdw_purge_data_ * <li> sysutility_ *|
|SSIS jobb steg|Kör SSIS-paket-aktivitet|<li> Namnet på aktiviteten blir \<step name> . <li> Det proxy-konto som används i jobb steget migreras som Windows-autentisering för aktiviteten. <li> *Körnings alternativ* förutom att *använda 32-bitars körning* som definierats i jobb steget ignoreras i migreringen. <li> *Verifieringen* som definierats i jobb steget kommer att ignoreras i migreringen.|
|schedule      |schema-utlösare        |Namnet på schema utlösaren *skapas för \<schedule name>*. <br> <br> Alternativen nedan i SQL-agentens jobb schema kommer att ignoreras i migreringen: <li> Intervall på den andra nivån. <li> *Starta automatiskt när SQL Server Agent startar* <li> *Starta när CPU: er blir inaktiv* <li> *veckodag* och *helg dag*<time zone> <br> Nedan visas skillnaderna efter att jobb schema för SQL-Agent migreras till ADF schema utlösare: <li> ADF schema utlösare efterföljande körningar är oberoende av körnings status för den Antecedent Utlös ande körningen. <li> Den återkommande konfigurationen av ADF-scheman skiljer sig från den dagliga frekvensen i SQL Agent-jobbet.|

- Skapa Azure Resource Manager ARM-mallar i den lokala mappen utdata och distribuera dem direkt eller senare manuellt. Mer information om ADF Resource Manager-mallar finns i [resurs typer för Microsoft. DataFactory](/azure/templates/microsoft.datafactory/allversions).

## <a name="prerequisites"></a>Förutsättningar

Funktionen som beskrivs i den här artikeln kräver SQL Server Management Studio version 18,5 eller senare. För att få den senaste versionen av SSMS, se [Ladda ned SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="migrate-ssis-jobs-to-adf"></a>Migrera SSIS-jobb till ADF

1. I SSMS i Object Explorer väljer du SQL Server Agent, väljer jobb, högerklickar och väljer **MIGRERA SSIS-jobb till ADF**.
![Skärm bild som visar SQL Server Management Studio Object Explorer, där du kan välja jobb och sedan migrera S S-jobb till en D F.](media/how-to-migrate-ssis-job-ssms/menu.png)

1. Logga in Azure, välj Azure-prenumeration, Data Factory och Integration Runtime. Azure Storage är valfritt, som används i steget för att mappa paket platser om SSIS-jobb som ska migreras har fil system paket för SSIS.
![hoppmeny](media/how-to-migrate-ssis-job-ssms/step1.png)

1. Mappa Sök vägarna för SSIS-paket och konfigurationsfiler i SSIS-jobb till mål Sök vägar där migrerade pipelines kan komma åt. I det här mappnings steget kan du:

    1. Välj en källmapp och Lägg sedan **till mappning**.
    1. Uppdatera sökvägen till käll katalogen. Giltiga sökvägar är sökvägar eller sökvägar till överordnade mappar i paket.
    1. Uppdatera sökvägen till målmappen. Standard är en relativ sökväg till standard lagrings kontot, som väljs i steg 1.
    1. Ta bort en vald mappning via **ta bort mappning**.
![Skärm bild som visar sidan mappnings-s-paket och konfigurations Sök vägar där du kan lägga till mappning. ](media/how-to-migrate-ssis-job-ssms/step2.png)
 ![ Skärm bild som visar sidan kartans S-paket och konfigurations Sök vägar, där du kan uppdatera Sök vägarna till käll-och målmappen.](media/how-to-migrate-ssis-job-ssms/step2-1.png)

1. Välj tillämpliga jobb som ska migreras och konfigurera inställningarna för motsvarande *utförda SSIS-paket-aktivitet*.

    - *Standardinställning*, gäller för alla valda steg som standard. Mer information om varje egenskap finns på *fliken Inställningar* för [aktiviteten kör SSIS-paket](how-to-invoke-ssis-package-ssis-activity.md) när paket platsen är *fil system (paket)*.
    ![Skärm bild som visar sidan Välj S S. s jobb där du kan konfigurera inställningarna för motsvarande utförda SSIS-paket-aktivitet.](media/how-to-migrate-ssis-job-ssms/step3-1.png)
    - *Steg inställning*, konfigurera inställningen för ett valt steg.
        
        **Använd standardinställning**: standard är markerat. Avmarkera om du vill konfigurera inställningen endast för valt steg.  
        Mer information om andra egenskaper finns på *fliken Inställningar* för [aktiviteten kör SSIS-paket](how-to-invoke-ssis-package-ssis-activity.md) när paket platsen är *fil system (paket)*.
    ![Skärm bild som visar sidan Välj S. S-jobb där du kan använda standardinställningarna.](media/how-to-migrate-ssis-job-ssms/step3-2.png)

1. Skapa och distribuera ARM-mall.
    1. Välj eller ange sökvägen till utmatningen för ARM-mallarna i de migrerade ADF-pipelinen. Mappen kommer att skapas automatiskt om den inte finns.
    2. Välj alternativet för **att distribuera arm-mallar till data fabriken**:
        - Standardvärdet är omarkerat. Du kan distribuera skapade ARM-mallar senare manuellt.
        - Välj om du vill distribuera genererade ARM-mallar till Data Factory direkt.
    ![Skärm bild som visar sidan Konfigurera migrering där du kan välja eller ange en sökväg för ARM-mallarna i de migrerade ADF-pipelinerna och välja alternativet för att distribuera ARM-mallar till din data fabrik.](media/how-to-migrate-ssis-job-ssms/step4.png)

1. Migrera och kontrol lera sedan resultatet.
![Skärm bild som visar sidan för migrerings resultat, som visar förloppet för migreringen.](media/how-to-migrate-ssis-job-ssms/step5.png)

## <a name="next-steps"></a>Nästa steg

[Kör och övervaka pipeline](how-to-invoke-ssis-package-ssis-activity.md)