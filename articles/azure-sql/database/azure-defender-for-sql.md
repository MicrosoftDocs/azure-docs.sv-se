---
title: Azure Defender för SQL
description: Lär dig mer om funktioner för att hantera dina databas sårbarheter och identifiera avvikande aktiviteter som kan tyda på ett hot mot databasen i Azure SQL Database, Azure SQL-hanterad instans eller Azure-Synapse.
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.date: 03/08/2021
ms.openlocfilehash: 27f17b3d1060e8b693c2a34cdb4643840f1bfd13
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452325"
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
Det finns flera sätt att aktivera Azure Defender-planer. Du kan aktivera den på prenumerations nivån (**rekommenderas**) från:

- [Azure Security Center](#enable-azure-defender-for-azure-sql-database-at-the-subscription-level-from-azure-security-center)
- [Program mässigt med REST API, Azure CLI, PowerShell eller Azure Policy](#enable-azure-defender-plans-programatically)

Alternativt kan du aktivera den på resurs nivå enligt beskrivningen i [Aktivera Azure Defender för Azure SQL Database på resurs nivå](#enable-azure-defender-for-azure-sql-database-at-the-resource-level)

### <a name="enable-azure-defender-for-azure-sql-database-at-the-subscription-level-from-azure-security-center"></a>Aktivera Azure Defender för Azure SQL Database på prenumerations nivå från Azure Security Center
Om du vill aktivera Azure Defender för Azure SQL Database på prenumerations nivån inifrån Azure Security Center:

1. Öppna **Security Center** från [Azure Portal](https://portal.azure.com).
1. Från Security Center menyn väljer du **priser och inställningar**.
1. Välj relevant prenumeration.
1. Ändra plan inställningen till **på**.

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender-sql-subscription-level.png" alt-text="Aktivera Azure Defender för Azure SQL Database på prenumerations nivån.":::

1. Välj **Spara**.


### <a name="enable-azure-defender-plans-programatically"></a>Aktivera Azure Defender-planer program mässigt 

Flexibiliteten i Azure ger ett antal programmerings metoder för att aktivera Azure Defender-planer. 

Använd något av följande verktyg för att aktivera Azure Defender för din prenumeration: 

| Metod       | Instruktioner                                                                                                                                       |
|--------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| REST-API     | [Prissättnings-API](/rest/api/securitycenter/pricings)                                                                                                  |
| Azure CLI    | [AZ Security-prissättning](/cli/azure/security/pricing)                                                                                                 |
| PowerShell   | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy | [Paket priser](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|              |                                                                                                                                                    |

### <a name="enable-azure-defender-for-azure-sql-database-at-the-resource-level"></a>Aktivera Azure Defender för Azure SQL Database på resurs nivå

Vi rekommenderar att du aktiverar Azure Defender-planer på prenumerations nivån och kan hjälpa dig att skapa oskyddade resurser. Men om du har en organisations orsak för att aktivera Azure Defender på server nivå, så gör du så här:

1. Öppna servern eller den hanterade instansen från [Azure Portal](https://portal.azure.com).
1. Under **säkerhets** rubriken väljer du **Security Center**.
1. Välj **Aktivera Azure Defender för SQL**.

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender.png" alt-text="Aktivera Azure Defender för SQL inifrån Azure SQL-databaser.":::

> [!NOTE]
> Ett lagrings konto skapas och konfigureras automatiskt för att lagra dina genomsöknings resultat för **sårbarhets bedömning** . Om du redan har aktiverat Azure Defender för en annan server i samma resurs grupp och region används det befintliga lagrings kontot.
>
> Kostnaden för Azure Defender är anpassad till Azure Security Center standard pris per nod, där en nod är hela servern eller en hanterad instans. Du betalar därför bara en gång för att skydda alla databaser på servern eller den hanterade instansen med Azure Defender. Du kan prova Azure Defender ut från början med en kostnads fri utvärderings version.


## <a name="manage-azure-defender-settings"></a>Hantera Azure Defender-inställningar

Visa och hantera Azure Defender-inställningar:

1. Från **säkerhets** delen av servern eller den hanterade instansen väljer du **Security Center**.

    På den här sidan ser du status för Azure Defender för SQL:

    :::image type="content" source="media/azure-defender-for-sql/status-of-defender-for-sql.png" alt-text="Kontrollerar status för Azure Defender för SQL i Azure SQL-databaser.":::

1. Om Azure Defender för SQL är aktiverat visas en **Konfigurera** -länk som visas i föregående bild. Om du vill redigera inställningarna för Azure Defender för SQL väljer du **Konfigurera**.

    :::image type="content" source="media/azure-defender-for-sql/security-server-settings.png" alt-text="Inställningar för Azure Defender för SQL.":::

1. Gör nödvändiga ändringar och välj **Spara**.


## <a name="next-steps"></a>Nästa steg

- Läs mer om [sårbarhets bedömning](sql-vulnerability-assessment.md)
- Läs mer om [Avancerat skydd](threat-detection-configure.md)
- Läs mer om [Azure Security Center](../../security-center/security-center-introduction.md)