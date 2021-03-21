---
title: Anslut Azure SQL Database-diagnostik och gransknings loggar till Azure Sentinel
description: Lär dig hur du ansluter Azure SQL Database-diagnostikloggar och säkerhets gransknings loggar till Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/06/2021
ms.author: yelevin
ms.openlocfilehash: a3a09ceffc75e2d396d7bd7aeedd97b7f2b6ec2b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99807741"
---
# <a name="connect-azure-sql-database-diagnostics-and-auditing-logs"></a>Anslut Azure SQL Database-diagnostik och gransknings loggar

Azure SQL är en fullständigt hanterad databas motor för PaaS (Platform-as-a-Service) som hanterar de flesta funktioner för databas hantering, till exempel uppgradering, uppdatering, säkerhets kopiering och övervakning, utan medverkan från användaren. 

Med Azure SQL Database Connector kan du strömma dina databasers gransknings-och diagnostikloggar till Azure Sentinel, så att du kontinuerligt kan övervaka aktivitet i alla dina instanser.

- Genom att ansluta diagnostikloggar loggar kan du skicka databasens diagnostikloggar av olika data typer till din Azure Sentinel-arbetsyta.

- Genom att ansluta gransknings loggar kan du strömma säkerhets gransknings loggar från alla dina Azure SQL-databaser på server nivå.

Läs mer om [övervakning av Azure SQL-databaser](../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha läs-och Skriv behörighet på Azure Sentinel-arbetsytan.

- Om du vill ansluta gransknings loggar måste du ha läs-och Skriv behörighet till Azure SQL Server gransknings inställningar.

## <a name="connect-to-azure-sql-database"></a>Ansluta till Azure SQL-databas
    
1. Välj **data kopplingar** på navigerings menyn i Azure Sentinel.

1. Välj **Azure SQL Database** i data kopplings galleriet och välj sedan **Öppna kopplings sida**  i förhands gransknings fönstret.

1. I **konfigurations** avsnittet på kopplings sidan noterar du de två kategorier av loggar som du kan ansluta.

### <a name="connect-diagnostics-logs"></a>Ansluta diagnostikloggar

1. Under **diagnostikloggar loggar** du in **Aktivera diagnostikloggar på var och en av dina Azure SQL-databaser manuellt**.

1. Välj länken **öppna Azure SQL->** för att öppna bladet **Azure SQL** -resurser.

1. **(Valfritt)** Om du vill hitta din databas resurs enkelt, väljer du **Lägg till filter** i fältet Filter överst.
    1. Välj **resurs typ** i list rutan **filter** .
    1. I list rutan **värde** avmarkerar du **Markera alla** och väljer sedan **SQL Database**.
    1. Klicka på **Applicera**.
    
1. Välj den databas resurs vars diagnostikloggar du vill skicka till Azure Sentinel.

    > [!NOTE]
    > För varje databas resurs vars loggar du vill samla in måste du upprepa den här processen med början från det här steget.

1. På sidan resurser i den valda databasen, under **övervakning** på navigerings menyn, väljer du **diagnostikinställningar**.

    1. Välj länken **+ Lägg till diagnostisk inställning** längst ned i tabellen.

    1. På skärmen **diagnostikinställningar** anger du ett namn i fältet namn på  **diagnostik-inställning** .
    
    1. Markera kryss rutan **Skicka till Log Analytics arbets yta** i kolumnen **mål information** . Två nya fält kommer att visas under det. Välj relevant **prenumeration** och **Log Analytics arbets yta** (där Azure Sentinel finns).

    1. Markera kryss rutorna för de logg-och mått typer som du vill mata in i kolumnen **kategori information** . Vi rekommenderar att du väljer alla tillgängliga typer under både **log** och **Metric**.

    1. Välj **Spara** längst upp på skärmen.

- Du kan också använda det angivna **PowerShell-skriptet** för att ansluta dina diagnostikloggar.
    1. Expandera **Aktivera med PowerShell-skript** under **diagnostikloggar**.

    1. Kopiera kod blocket och klistra in det i PowerShell.

### <a name="connect-audit-logs"></a>Anslut gransknings loggar

1. Under **gransknings loggar (för hands version)**, expanderar du **Aktivera gransknings loggar på alla Azure SQL-databaser (på server nivå)**.

1. Välj länken **öppna Azure SQL->** för att öppna resurs bladet för **SQL-servrar** .

1. Välj den SQL Server vars gransknings loggar du vill skicka till Azure Sentinel.

    > [!NOTE]
    > För varje server resurs vars loggar du vill samla in måste du upprepa den här processen med början från det här steget.

1. Välj **granskning** på menyn resurs på den server som du har valt, under **säkerhet** på navigerings menyn.

    1. Flytta **Aktivera Aktivera Azure SQL-granskning** till **på**.

    1. Under **gransknings loggens mål** väljer du **Log Analytics (för hands version)**.
    
    1. I listan över arbets ytor som visas väljer du din arbets yta (där Azure Sentinel finns).

    1. Välj **Spara** längst upp på skärmen.

- Du kan också använda det angivna **PowerShell-skriptet** för att ansluta dina diagnostikloggar.
    1. Expandera **Aktivera med PowerShell-skript** under **gransknings loggar**.

    1. Kopiera kod blocket och klistra in det i PowerShell.


> [!NOTE]
>
> Med den här specifika data kopplingen visas anslutnings status indikatorerna (en färg remsa i galleriet för data anslutningar och anslutnings ikoner bredvid data typens namn) som *ansluten* (grön) endast om data har matats in vid någon tidpunkt under de senaste två veckorna. När två veckor har passerat utan data inmatning visas kopplingen som frånkopplad. Den tid då mer data kommer till kommer den *anslutna* statusen att returneras.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Azure SQL Database Diagnostics och gransknings loggar till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).