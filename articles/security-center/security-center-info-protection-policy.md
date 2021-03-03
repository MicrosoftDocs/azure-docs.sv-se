---
title: Princip för SQL information Protection i Azure Security Center
description: Lär dig hur du anpassar information Protection-principer i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2020
ms.author: memildin
ms.openlocfilehash: ca3a1083b512709ad5ef070b6f5bd0e363baf80b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720757"
---
# <a name="sql-information-protection-policy-in-azure-security-center"></a>Princip för SQL information Protection i Azure Security Center
 
SQL information Protections [funktion för data identifiering och klassificering](../azure-sql/database/data-discovery-and-classification-overview.md) ger avancerade funktioner för att upptäcka, klassificera, märka och rapportera känsliga data i dina databaser. Den är inbyggd i [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md), [Azure SQL-hanterad instans](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)och [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

Klassificerings mekanismen baseras på följande två element:

- **Etiketter** – huvudattributen för klassificering som används för att definiera *känslighets nivån för de data* som lagras i kolumnen. 
- **Informations typer** – ger ytterligare detaljerad information om vilken *typ av data* som lagras i kolumnen.

Alternativen för information Protection-princip i Security Center ger en fördefinierad uppsättning etiketter och informations typer som fungerar som standardvärden för klassificerings motorn. Du kan anpassa principen enligt organisationens behov enligt beskrivningen nedan.

> [!IMPORTANT]
> Om du vill anpassa information Protection-principen för din Azure-klient måste du ha administratörs behörighet för klient organisationens rot hanterings grupp. Läs mer om hur du får bättre insyn i en [klient organisation för Azure Security Center](security-center-management-groups.md).

:::image type="content" source="./media/security-center-info-protection-policy/sql-information-protection-policy-page.png" alt-text="Sidan visar din SQL information Protection-princip":::
 



## <a name="how-do-i-access-the-sql-information-protection-policy"></a>Hur gör jag för att åtkomst till SQL information Protection-principen?

Det finns tre sätt att komma åt information Protection-principen:

- **(Rekommenderas)** På sidan priser och inställningar i Security Center
- Från säkerhets rekommendationen "känsliga data i dina SQL-databaser ska klassificeras"
- Från sidan för identifiering av Azure SQL DB-data

Var och en av dem visas på fliken relevant nedan.



### <a name="from-security-centers-settings"></a>[**Från Security Center inställningar**](#tab/sqlip-tenant)

### <a name="access-the-policy-from-security-centers-pricing-and-settings-page"></a>Öppna principen från Security Center sidan priser och inställningar <a name="sqlip-tenant"></a>

Välj **SQL information Protection** på sidan **priser och inställningar** för Security Center.

> [!NOTE]
> Det här alternativet visas bara för användare med behörighet som klient organisations nivå. [Bevilja behörighet för hela klienten själv](./security-center-management-groups.md#grant-tenant-wide-permissions-to-yourself).

:::image type="content" source="./media/security-center-info-protection-policy/pricing-settings-link-to-information-protection.png" alt-text="Åtkomst till SQL Information Protection-principen på sidan priser och inställningar i Azure Security Center":::



### <a name="from-security-centers-recommendation"></a>[**Från Security Center rekommendation**](#tab/sqlip-db)

### <a name="access-the-policy-from-the-security-center-recommendation"></a>Få åtkomst till principen från Security Center rekommendation <a name="sqlip-db"></a>

Använd Security Center rekommendation, "känsliga data i dina SQL-databaser ska klassificeras", för att visa sidan data identifiering och klassificering för din databas. Där visas även de kolumner som identifierats för att innehålla information som vi rekommenderar att du klassificerar.

1. Från Security Center sidan **rekommendationer** ska du söka efter rekommendations **känsliga data i dina SQL-databaser, klassificeras**.

    :::image type="content" source="./media/security-center-info-protection-policy/sql-sensitive-data-recommendation.png" alt-text="Hitta den rekommendation som ger åtkomst till SQL information Protection-principer":::

1. På sidan rekommendations information väljer du en databas från flikarna **felfria** eller **felaktiga** .

1. Sidan **klassificering av data identifierings &** öppnas. Välj **Konfigurera**.

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-security-center-recommendation.png" alt-text="Att öppna SQL information Protection-principen från rekommendationen i Azure Security Center":::



### <a name="from-azure-sql"></a>[**Från Azure SQL**](#tab/sqlip-azuresql)

### <a name="access-the-policy-from-azure-sql"></a>Få åtkomst till principen från Azure SQL <a name="sqlip-azuresql"></a>

1. Öppna Azure SQL från Azure Portal.

    :::image type="content" source="./media/security-center-info-protection-policy/open-azure-sql.png" alt-text="Öppna Azure SQL från Azure Portal":::

1. Välj en databas.

1. På menyn **säkerhet** på menyn öppnar du sidan för **data identifiering & klassificering** (1) och väljer **Konfigurera** (2).

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-azure-sql.png" alt-text="Öppnar SQL information Protection-principen från Azure SQL":::

--- 


## <a name="customize-your-information-types"></a>Anpassa dina informations typer

Så här hanterar och anpassar du informations typer:

1. Välj **Hantera informations typer**.

    :::image type="content" source="./media/security-center-info-protection-policy/manage-types.png" alt-text="Hantera informations typer för din information Protection-princip":::

1. Välj **skapa informations typ** om du vill lägga till en ny typ. Du kan konfigurera ett namn, en beskrivning och Sök efter mönster strängar för informations typen. Sök mönster strängar kan också använda nyckelord med jokertecken (med tecknet%) som den automatiserade identifierings motorn använder för att identifiera känsliga data i dina databaser, baserat på kolumnernas metadata.
 
    :::image type="content" source="./media/security-center-info-protection-policy/configure-new-type.png" alt-text="Konfigurera en ny informations typ för din information Protection-princip":::

1. Du kan också ändra de inbyggda typerna genom att lägga till fler Sök mönster strängar, inaktivera några av de befintliga strängarna eller genom att ändra beskrivningen. 

    > [!TIP]
    > Du kan inte ta bort inbyggda typer eller ändra deras namn. 

1. **Informations typer** visas i ordning efter stigande identifierings rangordning, vilket innebär att typerna som är högre i listan försöker matcha först. Om du vill ändra rangordningen mellan informations typer drar du typerna till rätt plats i tabellen eller använder knapparna **Flytta upp** och **Flytta ned** för att ändra ordningen. 

1. Välj **OK** när du är färdig.

1. När du har slutfört hanteringen av dina informations typer måste du associera de relevanta typerna med relevanta etiketter, genom att klicka på **Konfigurera** för en viss etikett och lägga till eller ta bort informations typer efter behov.

1. Om du vill tillämpa ändringarna väljer du **Spara** på sidan huvud **Etiketter** .
 

## <a name="exporting-and-importing-a-policy"></a>Exportera och importera en princip

Du kan ladda ned en JSON-fil med dina definierade etiketter och informations typer, redigera filen i valfritt redigerings program och sedan importera den uppdaterade filen. 

:::image type="content" source="./media/security-center-info-protection-policy/export-import.png" alt-text="Exportera och importera information Protection-princip":::

> [!NOTE]
> Du behöver behörighet som klient organisations nivå för att importera en princip fil. 


## <a name="manage-sql-information-protection-using-azure-powershell"></a>Hantera SQL information Protection med Azure PowerShell

- [Get-AzSqlInformationProtectionPolicy](/powershell/module/az.security/get-azsqlinformationprotectionpolicy): hämtar den effektiva klientens SQL information Protection-princip.
- [Set-AzSqlInformationProtectionPolicy](/powershell/module/az.security/set-azsqlinformationprotectionpolicy): anger den effektiva klientens SQL information Protection-princip.
 

## <a name="next-steps"></a>Nästa steg
 
I den här artikeln har du lärt dig hur du definierar en princip för informations skydd i Azure Security Center. Mer information om hur du använder SQL-Information Protection för att klassificera och skydda känsliga data i dina SQL-databaser finns i [Azure SQL Database identifiering och klassificering av data](../azure-sql/database/data-discovery-and-classification-overview.md).

Mer information om säkerhets principer och data säkerhet i Security Center finns i följande artiklar:
 
- [Ställa in säkerhets principer i Azure Security Center](tutorial-security-policy.md): Lär dig hur du konfigurerar säkerhets principer för dina Azure-prenumerationer och resurs grupper
- [Azure Security Center data säkerhet](security-center-data-security.md): Lär dig hur Security Center hanterar och skyddar data