---
title: Data Discovery & Classification
description: Data identifiering & klassificering för Azure SQL Database, Azure SQL-hanterad instans och Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=1
titleSuffix: Azure SQL Database, Azure SQL Managed Instance, and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/17/2021
tags: azure-synapse
ms.openlocfilehash: f47303a953dbb06b4c9efdfbbae41715cad81d2a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690812"
---
# <a name="data-discovery--classification"></a>Data Discovery & Classification
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Data identifierings & klassificeringen är inbyggd i Azure SQL Database, Azure SQL-hanterad instans och Azure Synapse Analytics. Den innehåller grundläggande funktioner för att upptäcka, klassificera, märka och rapportera känsliga data i dina databaser.

Dina mest känsliga data kan omfatta affärs-, finans-, sjukvårds-eller personlig information. Identifiering och klassificering av dessa data kan spela en pivot-roll i din organisations informations skydds metod. Tjänsten kan fungera som infrastruktur inom följande områden:

- Bidra till att uppfylla standarder för data sekretess och krav för regelefterlevnad.
- Olika säkerhets scenarier, till exempel övervakning (granskning) åtkomst till känsliga data.
- Kontrol lera åtkomst till och härdning av säkerheten för databaser som innehåller mycket känsliga data.

> [!NOTE]
> Information om SQL Server lokalt finns i avsnittet [SQL data Discovery & klassificering](/sql/relational-databases/security/sql-data-discovery-and-classification).

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>Vad är data identifierings & klassificering?

Data identifierings & klassificeringen introducerar en uppsättning grundläggande tjänster och nya funktioner i Azure. Den bildar ett nytt informations skydds paradigm för SQL Database, SQL-hanterad instans och Azure-Synapse, som syftar till att skydda data och inte bara databasen. Paradigmet innehåller:

- **Identifiering och rekommendationer:** Klassificerings motorn genomsöker din databas och identifierar kolumner som innehåller potentiellt känsliga data. Det ger dig ett enkelt sätt att granska och tillämpa Rekommenderad klassificering via Azure Portal.

- **Märkning:** Du kan använda känslighets klassificerings etiketter bestående av kolumner genom att använda nya metadata-attribut som har lagts till i SQL Server databas motorn. Dessa metadata kan sedan användas för känslighets gransknings-och skydds scenarier.

- **Frågeresultat – ange känslighet:** Känsligheten för en frågeresultat beräknas i real tid för gransknings syfte.

- **Synlighet:** Du kan visa databas klassificerings tillstånd på en detaljerad instrument panel i Azure Portal. Du kan också ladda ned en rapport i Excel-format som används för efterlevnad och gransknings ändamål och andra behov.

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>Identifiera, klassificera och etikettera känsliga kolumner

I det här avsnittet beskrivs stegen för:

- Identifiera, klassificera och etikettera kolumner som innehåller känsliga data i databasen.
- Visa den aktuella klassificerings statusen för din databas och exportera rapporter.

Klassificeringen innehåller två attribut för metadata:

- **Etiketter**: huvudattributen för klassificering som används för att definiera känslighets nivån för de data som lagras i kolumnen.  
- **Informations typer**: attribut som ger detaljerad information om den typ av data som lagras i kolumnen.

### <a name="define-and-customize-your-classification-taxonomy"></a>Definiera och anpassa din klassificerings-taxonomi

Data identifierings & klassificeringen innehåller en inbyggd uppsättning känslighets etiketter och en inbyggd uppsättning informations typer och identifierings logik. Du kan anpassa den här taxonomin och definiera en uppsättning och rangordning av klassificeringskonstruktioner som är specifika för din miljö.

Du definierar och anpassar din klassificerings taxonomi på en central plats för hela Azure-organisationen. Den platsen finns [Azure Security Center](../../security-center/security-center-introduction.md), som en del av din säkerhets princip. Endast användare med administratörs behörighet för organisationens rot hanterings grupp kan utföra den här åtgärden.

Som en del av princip hanteringen för informations skydd kan du definiera anpassade etiketter, ranka dem och koppla dem till en viss uppsättning informations typer. Du kan också lägga till egna anpassade informations typer och konfigurera dem med sträng mönster. Mönstren läggs till i identifierings logiken för att identifiera den här typen av data i dina databaser.

Mer information finns i [Anpassa SQL information Protection-principen i Azure Security Center (för hands version)](../../security-center/security-center-info-protection-policy.md).

När du har definierat principen för hela organisationen kan du fortsätta att klassificera enskilda databaser med hjälp av den anpassade principen.

### <a name="classify-your-database"></a>Klassificera databasen

> [!NOTE]
> I exemplet nedan används Azure SQL Database, men du bör välja lämplig produkt som du vill konfigurera data identifiering & klassificering.

1. Gå till [Azure-portalen](https://portal.azure.com).

1. Gå till **data identifiering & klassificering** under **säkerhets** rubriken i Azure SQL Databases fönstret. På fliken Översikt finns en sammanfattning av databasens aktuella klassificerings tillstånd. Sammanfattningen innehåller en detaljerad lista över alla klassificerade kolumner, som du även kan filtrera så att endast vissa schema delar, informations typer och etiketter visas. Om du inte har klassificerat några kolumner än går [du vidare till steg 4](#step-4).

    ![Översikt](./media/data-discovery-and-classification-overview/data-discovery-and-classification.png)

1. Om du vill ladda ned en rapport i Excel-format väljer du **Exportera** i den övre menyn i fönstret.

1. <a id="step-4"></a>Om du vill börja klassificera dina data väljer du fliken **klassificering** på sidan **klassificering av data identifiering &** .

    Klassificerings motorn söker igenom databasen efter kolumner som innehåller potentiellt känsliga data och innehåller en lista över rekommenderade kolumn klassificeringar.

1. Visa och tillämpa klassificerings rekommendationer:

   - Om du vill visa en lista över rekommenderade kolumn klassificeringar väljer du panelen rekommendationer längst ned i fönstret.

   - Om du vill godkänna en rekommendation för en speciell kolumn markerar du kryss rutan i den vänstra kolumnen i relevant rad. Markera kryss rutan längst till vänster i tabell rubriken rekommendationer för att markera alla rekommendationer som godkända.

   - Välj **acceptera markerade rekommendationer** för att tillämpa de valda rekommendationerna.

   ![Rekommendationer för klassificering](./media/data-discovery-and-classification-overview/recommendation.png)

1. Du kan också klassificera kolumner manuellt, som ett alternativ eller förutom den rekommendationbaserade klassificeringen:

   1. Välj **Lägg till klassificering** i den översta menyn i fönstret.

   1. I Sammanhangs fönstret som öppnas väljer du det schema, den tabell och den kolumn som du vill klassificera, samt etiketten information typ och känslighet.

   1. Välj **Lägg till klassificering** längst ned i kontext fönstret.

   ![Lägg till klassificering manuellt](./media/data-discovery-and-classification-overview/manually-add-classification.png)


1. Om du vill slutföra klassificeringen och etiketten (tag) i databas kolumnerna med de nya klassificerings-metadata väljer du **Spara** på **klassificerings** sidan.

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Granska åtkomst till känsliga data

En viktig aspekt av informations skyddets paradigm är möjligheten att övervaka åtkomsten till känsliga data. [Azure SQL-granskning](../../azure-sql/database/auditing-overview.md) har förbättrats för att innehålla ett nytt fält i gransknings loggen som kallas `data_sensitivity_information` . Det här fältet loggar känslighets klassificeringarna (etiketter) för de data som returnerades av en fråga. Här är ett exempel:

![Granskningslogg](./media/data-discovery-and-classification-overview/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Behörigheter

Dessa inbyggda roller kan läsa data klassificeringen för en databas:

- Ägare
- Läsare
- Deltagare
- SQL-säkerhetshanteraren
- Administratör för användaråtkomst

Dessa inbyggda roller kan ändra data klassificeringen för en databas:

- Ägare
- Deltagare
- SQL-säkerhetshanteraren

Lär dig mer om rollbaserade behörigheter i [Azure RBAC](../../role-based-access-control/overview.md).

## <a name="manage-classifications"></a><a id="manage-classification"></a>Hantera klassificeringar

Du kan använda T-SQL, en REST API eller PowerShell för att hantera klassificeringar.

### <a name="use-t-sql"></a>Använd T-SQL

Du kan använda T-SQL för att lägga till eller ta bort kolumn klassificeringar och hämta alla klassificeringar för hela databasen.

> [!NOTE]
> När du använder T-SQL för att hantera etiketter finns det ingen validering av etiketter som du lägger till i en kolumn i organisationens informations skydds princip (den uppsättning etiketter som visas i Portal rekommendationer). Så är det upp till dig att validera detta.

Information om hur du använder T-SQL för klassificeringar finns i följande referenser:

- Lägga till eller uppdatera klassificeringen för en eller flera kolumner: [Lägg till känslighets klassificering](/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Ta bort klassificeringen från en eller flera kolumner: [släpp känslighets klassificering](/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Så här visar du alla klassificeringar i databasen: [sys.sensitivity_classifications](/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-powershell-cmdlets"></a>Använda PowerShell-cmdletar
Hantera klassificeringar och rekommendationer för Azure SQL Database och Azure SQL-hanterad instans med hjälp av PowerShell.

#### <a name="powershell-cmdlets-for-azure-sql-database"></a>PowerShell-cmdletar för Azure SQL Database

- [Get-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Aktivera – AzSqlDatabaSesensitivityRecommendation](/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Disable-AzSqlDatabaseSensitivityRecommendation](/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-azure-sql-managed-instance"></a>PowerShell-cmdletar för Azure SQL-hanterad instans

- [Get-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Aktivera – AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Disable-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

### <a name="use-the-rest-api"></a>Använd REST-API: et

Du kan använda REST API för att program mässigt hantera klassificeringar och rekommendationer. Den publicerade REST API har stöd för följande åtgärder:

- [Skapa eller uppdatera](/rest/api/sql/sensitivitylabels/createorupdate): skapar eller uppdaterar känslighets etiketten för den angivna kolumnen.
- [Ta bort](/rest/api/sql/sensitivitylabels/delete): tar bort känslighets etiketten för den angivna kolumnen.
- [Inaktivera rekommendation](/rest/api/sql/sensitivitylabels/disablerecommendation): inaktiverar känslighets rekommendationer i den angivna kolumnen.
- [Aktivera rekommendation](/rest/api/sql/sensitivitylabels/enablerecommendation): möjliggör känslighets rekommendationer i den angivna kolumnen. (Rekommendationer är aktiverade som standard på alla kolumner.)
- [Get](/rest/api/sql/sensitivitylabels/get): hämtar känslighets etiketten för den angivna kolumnen.
- [Lista aktuell efter databas](/rest/api/sql/sensitivitylabels/listcurrentbydatabase): hämtar de aktuella känslighets etiketterna för den angivna databasen.
- [Lista rekommenderas av databasen](/rest/api/sql/sensitivitylabels/listrecommendedbydatabase): hämtar de rekommenderade känslighets etiketterna för den angivna databasen.


## <a name="faq---advanced-classification-capabilities"></a>Vanliga frågor och svar om avancerade funktioner

**Fråga**: kommer [Azure AVDELNINGENS kontroll](../../purview/overview.md) att ersätta SQL data Discovery & klassificering eller kommer att inaktive ras för SQL data Discovery &-klassificeringen snart?
**Svar**: vi fortsätter att stödja SQL Data Discovery & klassificering och du uppmanas att använda [Azure-avdelningens kontroll](../../purview/overview.md) som har rikare funktioner för att driva avancerade klassificerings funktioner och data styrning. Om vi bestämmer sig för att dra tillbaka alla tjänster, funktioner, API eller SKU: er, får du förhands meddelande, inklusive en migrering eller över gångs Sök väg. Läs mer om Microsofts livs cykel principer här.


## <a name="next-steps"></a><a id="next-steps"></a>Nästa steg

- Överväg att konfigurera [Azure SQL-granskning](../../azure-sql/database/auditing-overview.md) för övervakning och granskning av åtkomst till dina klassificerade känsliga data.
- En presentation som innehåller & klassificering för data identifiering finns i avsnittet [upptäcka, klassificera, etikettera & skydda SQL-data | Data som visas](https://www.youtube.com/watch?v=itVi9bkJUNc).
- Information om hur du klassificerar dina Azure SQL-databaser och Azure Synapse-analys med Azure avdelningens kontroll-etiketter med hjälp av T-SQL-kommandon finns i [klassificera dina Azure SQL-data med Azure avdelningens kontroll-etiketter](../../sql-database/scripts/sql-database-import-purview-labels.md).