---
title: Konfigurera Advanced Threat Protection
description: Avancerat skydd identifierar avvikande databas aktiviteter som indikerar potentiella säkerhetshot till databasen i Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019, sqldbrb=1
ms.topic: how-to
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 12/01/2020
ms.openlocfilehash: 1425003c718ca52c0bea712e9d25cd3e4c035cf1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96453959"
---
# <a name="configure-advanced-threat-protection-for-azure-sql-database"></a>Konfigurera Avancerat skydd för Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Avancerat skydd](threat-detection-overview.md) för Azure SQL Database identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser. Avancerat skydd kan identifiera **potentiell SQL-inmatning**, **komma åt från ovanliga platser eller data Center**, **komma åt från ett välkänt huvud konto eller potentiellt skadligt program och orsaka** SQL-autentiseringsuppgifter i **brutet** läge. mer information finns i [varningarna om avancerade hot skydd](threat-detection-overview.md#alerts).

Du kan få meddelanden om identifierade hot via [e-postmeddelanden](threat-detection-overview.md#explore-detection-of-a-suspicious-event) eller [Azure Portal](threat-detection-overview.md#explore-alerts-in-the-azure-portal)

[Avancerat skydd](threat-detection-overview.md) är en del av [Azure Defender för SQL](azure-defender-for-sql.md) -erbjudandet, som är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Avancerat skydd kan nås och hanteras via den centrala Azure Defender för SQL-portalen.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Konfigurera Avancerat skydd i Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Gå till konfigurations sidan för den server som du vill skydda. I säkerhets inställningarna väljer du **Security Center**.
3. På konfigurations sidan för **Azure Defender för SQL** :

   - Aktivera **Azure Defender för SQL** på servern.
   - I **Inställningar för avancerat skydd** anger du listan över e-postmeddelanden som ska få säkerhets aviseringar vid identifiering av avvikande databas aktiviteter i text rutan **skicka aviseringar till** text.
   
   :::image type="content" source="media/azure-defender-for-sql/set-up-advanced-threat-protection.png" alt-text="Konfigurera Avancerat skydd":::

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Konfigurera Advanced Threat Protection med PowerShell

Ett skript exempel finns i [Konfigurera granskning och Avancerat skydd med PowerShell](scripts/auditing-threat-detection-powershell-configure.md).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Avancerat skydd](threat-detection-overview.md).
- Läs mer om [Avancerat skydd i SQL-hanterad instans](../managed-instance/threat-detection-configure.md).  
- Läs mer om [Azure Defender för SQL](azure-defender-for-sql.md).
- Läs mer om [granskning](../../azure-sql/database/auditing-overview.md)
- Läs mer om [Azure Security Center](../../security-center/security-center-introduction.md)
- Mer information om priser finns på sidan med [SQL Database priser](https://azure.microsoft.com/pricing/details/sql-database/)