---
title: Konfigurera Advanced Threat Protection
titleSuffix: Azure SQL Managed Instance
description: Avancerat skydd identifierar avvikande databas aktiviteter som indikerar potentiella säkerhetshot till databasen i Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 12/01/2020
ms.openlocfilehash: 69bebcf872f55055117acf5cef410d1f89eafe34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96446920"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-managed-instance"></a>Konfigurera Avancerat skydd i Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Avancerat skydd](../database/threat-detection-overview.md) för en [Azure SQL-hanterad instans](sql-managed-instance-paas-overview.md) identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser. Avancerat skydd kan identifiera **potentiell SQL-inmatning**, **komma åt från ovanliga platser eller data Center**, **komma åt från ett välkänt huvud konto eller potentiellt skadligt program och orsaka** SQL-autentiseringsuppgifter i **brutet** läge. mer information finns i [varningarna om avancerade hot skydd](../database/threat-detection-overview.md#alerts).

Du kan få meddelanden om identifierade hot via [e-postmeddelanden](../database/threat-detection-overview.md#explore-detection-of-a-suspicious-event) eller [Azure Portal](../database/threat-detection-overview.md#explore-alerts-in-the-azure-portal)

[Avancerat skydd](../database/threat-detection-overview.md) är en del av [Azure Defender för SQL](../database/azure-defender-for-sql.md)  -erbjudandet, som är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Avancerat skydd kan nås och hanteras via den centrala Azure Defender för SQL-portalen.

##  <a name="azure-portal"></a>Azure Portal

1. Logga in på  [Azure Portal](https://portal.azure.com). 
2. Gå till konfigurations sidan för instansen av SQL-hanterad instans som du vill skydda. Under **säkerhet** väljer du **Security Center**.
3. På sidan konfiguration av Azure Defender för SQL
   - Aktivera **Azure** Defender för SQL.
   - Konfigurera **aviseringarna skicka aviseringar till** e-postadress för att få säkerhets aviseringar vid identifiering av avvikande databas aktiviteter.
   - Välj det **Azure Storage-konto** där avvikande hot gransknings poster sparas.
   - Välj de **typer av avancerade hot skydd** som du vill konfigurera. Läs mer om [aviseringar om Avancerat skydd](../database/threat-detection-overview.md).
4. Klicka på **Spara** för att spara den nya eller uppdaterade Azure Defender för SQL-principen.

   :::image type="content" source="../database/media/azure-defender-for-sql/set-up-advanced-threat-protection-mi.png" alt-text="Konfigurera Avancerat skydd":::

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Avancerat skydd](../database/threat-detection-overview.md).
- Lär dig mer om hanterade instanser, se [Vad är en Azure SQL-hanterad instans](sql-managed-instance-paas-overview.md).
- Läs mer om [Avancerat skydd för Azure SQL Database](../database/threat-detection-configure.md).
- Läs mer om [granskning av SQL-hanterad instans](./auditing-configure.md).
- Läs mer om [Azure Security Center](../../security-center/security-center-introduction.md).