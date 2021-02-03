---
title: Azure Defender för SQL
description: Lär dig mer om funktioner för att hantera dina databas sårbarheter och identifiera avvikande aktiviteter som kan tyda på ett hot mot databasen i Azure SQL Database, Azure SQL-hanterad instans eller Azure-Synapse.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.date: 02/02/2021
ms.openlocfilehash: 48df96373554f6e474c3835bf81e38a9aea5450c
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508819"
---
# <a name="azure-defender-for-sql"></a>Azure Defender för SQL
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure Defender för SQL är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Azure Defender är tillgängligt för Azure SQL Database, Azure SQL-hanterad instans och Azure Synapse Analytics. Det innefattar funktioner för att identifiera och klassificera känsliga data, lyfta fram och åtgärda potentiella säkerhetsrisker i databasen och identifiera avvikande aktiviteter som kan indikera ett hot mot databasen. Det ger en samlad plats för aktivering och hantering av dessa funktioner.

## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Vilka är fördelarna med Azure Defender för SQL?

Azure Defender innehåller en uppsättning avancerade SQL-säkerhetsfunktioner, inklusive SQL sårbarhets bedömning och Avancerat skydd.
- [Sårbarhets bedömning](sql-vulnerability-assessment.md) är en tjänst som är enkel att konfigurera som kan upptäcka, spåra och hjälpa dig att åtgärda potentiella databas sårbarheter. Den ger insyn i ditt säkerhets tillstånd och innehåller åtgärder som kan vidtas för att lösa säkerhets problem och förbättra din databas Fortifications.
- [Advanced Threat Protection](threat-detection-overview.md) identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja din databas. Den övervakar kontinuerligt databasen för misstänkta aktiviteter och ger omedelbara säkerhets aviseringar om potentiella sårbarheter, Azure SQL-injektering och avvikande databas åtkomst mönster. Advanced Threat Protection-aviseringar ger detaljerad information om misstänkt aktivitet och rekommenderar åtgärder för att undersöka och minska risken.

Du kan aktivera alla de här funktionerna genom att aktivera Azure Defender for SQL en enda gång. Med ett klick kan du aktivera Azure Defender för alla databaser på [servern](logical-servers.md) i Azure eller i din SQL-hanterade instans. Aktivering eller hantering av Azure Defender-inställningar kräver att du tillhöra rollen [SQL Security Manager](../../role-based-access-control/built-in-roles.md#sql-security-manager) eller någon av databas-eller Server administratörs rollerna.

Mer information om priser för Azure Defender för SQL finns på [sidan med Azure Security Center priser](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-azure-defender"></a>Aktivera Azure Defender

Azure Defender kan nås via [Azure Portal](https://portal.azure.com). Aktivera Azure Defender genom att navigera till **Security Center** under **säkerhets** rubriken för servern eller den hanterade instansen.

> [!NOTE]
> Ett lagrings konto skapas och konfigureras automatiskt för att lagra dina genomsöknings resultat för **sårbarhets bedömning** . Om du redan har aktiverat Azure Defender för en annan server i samma resurs grupp och region används det befintliga lagrings kontot.
>
> Kostnaden för Azure Defender är anpassad till Azure Security Center standard pris per nod, där en nod är hela servern eller en hanterad instans. Du betalar därför bara en gång för att skydda alla databaser på servern eller den hanterade instansen med Azure Defender. Du kan prova Azure Defender ut från början med en kostnads fri utvärderings version.

:::image type="content" source="media/azure-defender-for-sql/enable-azure-defender.png" alt-text="Aktivera Azure Defender för SQL inifrån Azure SQL-databaser":::

## <a name="track-vulnerabilities-and-investigate-threat-alerts"></a>Spåra sårbarheter och undersök hot aviseringar

Klicka på kortet för **sårbarhets bedömning** för att visa och hantera sårbarhets genomsökningar och rapporter och för att spåra din säkerhets datasekretesstandarder. Om säkerhets aviseringar har mottagits klickar du på kortet **Avancerat skydd** för att visa information om aviseringarna och visa en konsol IDE rad rapport om alla aviseringar i din Azure-prenumeration via sidan Azure Security Center säkerhets aviseringar.

## <a name="manage-azure-defender-settings"></a>Hantera Azure Defender-inställningar

Visa och hantera Azure Defender-inställningar:

1. Från **säkerhets** delen av servern eller den hanterade instansen väljer du **Security Center**.

    På den här sidan ser du status för Azure Defender för SQL:

    :::image type="content" source="media/azure-defender-for-sql/status-of-defender-for-sql.png" alt-text="Kontrol lera status för Azure Defender för SQL i Azure SQL-databaser":::

1. Om Azure Defender för SQL är aktiverat visas en **Konfigurera** -länk som visas i föregående bild. Om du vill redigera inställningarna för Azure Defender för SQL väljer du **Konfigurera**.

    :::image type="content" source="media/azure-defender-for-sql/security-server-settings.png" alt-text="säkerhets Server inställningar":::

1. Gör nödvändiga ändringar och välj **Spara**.


## <a name="next-steps"></a>Nästa steg

- Läs mer om [sårbarhets bedömning](sql-vulnerability-assessment.md)
- Läs mer om [Avancerat skydd](threat-detection-configure.md)
- Läs mer om [Azure Security Center](../../security-center/security-center-introduction.md)