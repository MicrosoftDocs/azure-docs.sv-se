---
title: Ändringar på plats av SQL Server version
description: Lär dig hur du ändrar versionen för din SQL Server virtuella dator i Azure.
services: virtual-machines-windows
documentationcenter: na
author: ramakoni1
manager: ramakoni1
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/08/2020
ms.author: RamaKoni
ms.reviewer: sqlblt, daleche
ms.custom: seo-lt-2019
ms.openlocfilehash: 5dbd5725c666afe04d57d4432f0a4798fcb34c03
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97358851"
---
# <a name="in-place-change-of-sql-server-version-on-azure-vm"></a>Förändringar på plats av SQL Server version på virtuell Azure-dator

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

I den här artikeln beskrivs hur du ändrar den version av Microsoft SQL Server på en virtuell Windows-dator (VM) i Microsoft Azure.

## <a name="prerequisites"></a>Förutsättningar

För att utföra en uppgradering på plats av SQL Server gäller följande villkor:

- Installations mediet för den önskade versionen av SQL Server måste anges. Kunder som har [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) kan hämta installationsmedia från [Volume Licensing Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Kunder som inte har Software Assurance kan använda installations mediet från en Azure Marketplace SQL Server VM-avbildning som har en senare version av SQL Server (finns vanligt vis i C:\SQLServerFull).
- Uppgraderingar av utgåvor bör följa [sökvägar för support uppgradering](/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15).

## <a name="planning-for-version-change"></a>Planera för versions ändring

Vi rekommenderar att du läser följande objekt innan du ändrar versions ändringen:

1. Se vad som är nytt i den version som du planerar att uppgradera till:

   - Nyheter i [SQL 2019](/sql/sql-server/what-s-new-in-sql-server-ver15)
   - Nyheter i [SQL 2017](/sql/sql-server/what-s-new-in-sql-server-2017)
   - Nyheter i [SQL 2016](/sql/sql-server/what-s-new-in-sql-server-2016)


1. Vi rekommenderar att du kontrollerar [kompatibiliteten](/sql/database-engine/install-windows/compatibility-certification) för den version som du ska ändra till så att du kan använda databasens kompatibilitetsalternativ för att minimera uppgraderingens resultat.
1. Du kan gå igenom följande artiklar för att se hur resultatet lyckades:

   - [Video: att SQL Server | Pam-Lahoud & Pedro Lopes | 20 års PASS](https://www.youtube.com/watch?v=5RPkuQHcxxs&feature=youtu.be)
   - [Database Experimentation Assistant för AB-testning](/sql/dea/database-experimentation-assistant-overview)
   - [Uppgradera databaser med hjälp av fråge justerings assistenten](/sql/relational-databases/performance/upgrade-dbcompat-using-qta)
   - [Ändra kompatibilitetsnivån för databas och Använd Frågearkivet](/sql/database-engine/install-windows/change-the-database-compatibility-mode-and-use-the-query-store)

## <a name="upgrade-sql-version"></a>Uppgradera SQL-version

> [!WARNING]
> Om du uppgraderar versionen av SQL Server startas om tjänsten för SQL Server utöver associerade tjänster, till exempel Analysis Services-och R-tjänster.

Om du vill uppgradera versionen av SQL Server hämtar du SQL Server installations mediet för den senare versionen som har [stöd för uppgraderings Sök vägen](/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15) för SQL Server och utför följande steg:

1. Säkerhetskopiera databaserna, inklusive system (förutom tempdb) och användar databaser, innan du startar processen. Du kan också skapa en programkonsekvent säkerhets kopiering på VM-nivå genom att använda Azure Backup Services.
1. Starta Setup.exe från SQL Server-installations mediet.
1. Installations guiden startar installations Center för SQL Server. Om du vill uppgradera en befintlig instans av SQL Server väljer du **installation** i navigerings fönstret och väljer sedan **Uppgradera från en tidigare version av SQL Server**.

   :::image type="content" source="./media/change-sql-server-version/upgrade.png" alt-text="Val för uppgradering av versionen av SQL Server":::

1. På sidan **produkt nyckel** väljer du ett alternativ för att ange om du uppgraderar till en kostnads fri version av SQL Server eller om du har en PID-nyckel för en produktions version av produkten. Mer information finns i [utgåvor och stödda funktioner i SQL Server 2019 (15. x)](/sql/sql-server/editions-and-components-of-sql-server-version-15) och [versioner som stöds och uppgraderingar av utgåvor (SQL Server 2016)](/sql/database-engine/install-windows/supported-version-and-edition-upgrades).
1. Välj **Nästa** tills du kommer till sidan **redo att uppgradera** och välj sedan **Uppgradera**. Installations fönstret kan sluta svara i flera minuter medan ändringen börjar att fungera. En **fullständig** sida bekräftar att uppgraderingen är klar. En steg-för-steg-procedur för att uppgradera finns i [hela proceduren](/sql/database-engine/install-windows/upgrade-sql-server-using-the-installation-wizard-setup#procedure).

   :::image type="content" source="./media/change-sql-server-version/complete-page.png" alt-text="Slutför Sidan":::

Om du har ändrat den SQL Server versionen, förutom att ändra versionen, uppdaterar du även versionen och läser avsnittet **kontrol lera version och utgåva i portalen** för att ändra den virtuella SQL-instansen.

   :::image type="content" source="./media/change-sql-server-version/change-portal.png" alt-text="Ändra metadata för version":::

## <a name="downgrade-the-version-of-sql-server"></a>Nedgradera versionen av SQL Server

Om du vill nedgradera versionen av SQL Server måste du avinstallera SQL Server och installera om den igen med hjälp av den önskade versionen. Detta påminner om en ny installation av SQL Server eftersom du inte kommer att kunna återställa den tidigare databasen från en senare version till den nyligen installerade tidigare versionen. Databaserna måste skapas på nytt från grunden. Om du även ändrade versionen av SQL Server under uppgraderingen ändrar du egenskapen **version** för SQL Server VM i Azure Portal till det nya värdet. Detta uppdaterar de metadata och faktureringar som är associerade med den här virtuella datorn.

> [!WARNING]
> En degradering på plats av SQL Server stöds inte.

Du kan nedgradera versionen av SQL Server genom att följa dessa steg:

1. Se till att du inte använder någon funktion som bara är [tillgänglig i den senare versionen](https://social.technet.microsoft.com/wiki/contents/articles/24222.find-enterprise-only-features-in-your-database.aspx).
1. Säkerhetskopiera alla databaser, inklusive system (förutom tempdb) och användar databaser.
1. Exportera alla nödvändiga objekt på server nivå (t. ex. Server utlösare, roller, inloggningar, länkade servrar, jobb, autentiseringsuppgifter och certifikat).
1. Om du inte har skript för att återskapa användar databaserna på den tidigare versionen måste du skriva ut alla objekt och exportera alla data med hjälp av BCP.exe, SSIS eller DACPAC.

   Se till att du väljer rätt alternativ när du skriptar sådana objekt som mål version, beroende objekt och avancerade alternativ.

   :::image type="content" source="./media/change-sql-server-version/scripting-options.png" alt-text="Skript alternativ":::

1. Avinstallera SQL Server och alla tillhör ande tjänster fullständigt.
1. Starta om den virtuella datorn.
1. Installera SQL Server med hjälp av mediet för den önskade versionen av programmet.
1. Installera de senaste Service Pack-versionerna och kumulativa uppdateringarna.
1. Importera alla nödvändiga objekt på server nivå (som exporterades i steg 3).
1. Återskapa alla nödvändiga användar databaser från grunden (med hjälp av skapade skript eller filer från steg 4).

## <a name="verify-the-version-and-edition-in-the-portal"></a>Verifiera versionen och utgåvan i portalen

När du har ändrat versionen av SQL Server registrerar du SQL Server VM med [SQL IaaS agent-tillägget](sql-agent-extension-manually-register-single-vm.md) igen så att du kan använda Azure Portal för att Visa versionen av SQL Server. Det listade versions numret ska nu avspegla den nyligen uppgraderade versionen och versionen av din SQL Server-installation.

:::image type="content" source="./media/change-sql-server-version/verify-portal.png" alt-text="Verifiera version":::

> [!NOTE]
> Om du redan har registrerat dig med SQL IaaS agent-tillägget [avregistrerar](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) du [den virtuella SQL-resursen](sql-agent-extension-manually-register-single-vm.md#register-with-extension) igen så att den identifierar rätt version och utgåva av SQL Server som är installerad på den virtuella datorn. Detta uppdaterar de metadata-och fakturerings uppgifter som är associerade med den här virtuella datorn.

## <a name="remarks"></a>Kommentarer

- Vi rekommenderar att du initierar säkerhets kopieringar/uppdaterar statistik/Rebuild index/kontrollerar konsekvens när uppgraderingen är färdig. Du kan också kontrol lera de enskilda kompatibilitetsnivån för databas för att se till att de återspeglar önskad nivå.
- När SQL Server har uppdaterats på den virtuella datorn kontrollerar du att egenskapen **version** för SQL Server i Azure Portal matchar det installerade versions numret för fakturering.
- Möjligheten att [ändra versionen](change-sql-server-edition.md#change-edition-in-portal) är en funktion i SQL IaaS agent-tillägget. Genom att distribuera en Azure Marketplace-avbildning via Azure Portal registreras automatiskt en SQL Server VM med tillägget. Kunder som själv installerar SQL Server måste dock [registrera sina SQL Server VM](sql-agent-extension-manually-register-single-vm.md)manuellt.
- Om du släpper SQL Server VM-resursen återställs den hårdkodade versions inställningen för avbildningen.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- [Översikt över SQL Server på en virtuell Windows-dator](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Vanliga frågor och svar om SQL Server på en virtuell Windows-dator](frequently-asked-questions-faq.md)
- [Pris vägledning för SQL Server på en virtuell Windows-dator](pricing-guidance.md)
- [Viktig information för SQL Server på en virtuell Windows-dator](doc-changes-updates-release-notes.md)