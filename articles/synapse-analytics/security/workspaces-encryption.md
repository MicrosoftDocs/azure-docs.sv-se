---
title: Azure Synapse Analytics-kryptering
description: En artikel som förklarar kryptering i Azure Synapse Analytics
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 11/19/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: d4bc59a9bd5299698bff9949aaaa881fbdf385ee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100526280"
---
# <a name="encryption-for-azure-synapse-analytics-workspaces"></a>Kryptering för Azure Synapse Analytics-arbetsytor

Den här artikeln beskriver:
* Kryptering av data i vila i Synapse Analytics-arbetsytor.
* Konfiguration av Synapse-arbetsytor för att aktivera kryptering med en kundhanterad nyckel.
* Hantera nycklar som används för att kryptera data i arbets ytor.

## <a name="encryption-of-data-at-rest"></a>Kryptering av data i vila

En fullständig lösning för kryptering vid rest säkerställer att data aldrig sparas i okrypterad form. Dubbel kryptering av data i vila minimerar hot med två separata lager med kryptering för att skydda mot kompromisser i ett enskilt lager. Azure Synapse Analytics erbjuder ett sekundärt krypterings lager för data i din arbets yta med en kundhanterad nyckel. Den här nyckeln skyddas i [Azure Key Vault](../../key-vault/general/overview.md), vilket gör att du kan bli ägare till nyckel hantering och rotation.

Det första lagret av kryptering för Azure-tjänster är aktiverat med plattforms hanterade nycklar. Som standard krypteras Azure-diskar och data i Azure Storage-konton automatiskt i vila. Läs mer om hur du använder kryptering i Microsoft Azure i [översikten över Azure-kryptering](../../security/fundamentals/encryption-overview.md).

## <a name="azure-synapse-encryption"></a>Azure Synapse-kryptering

Det här avsnittet hjälper dig att bättre förstå hur kundhanterad nyckel kryptering är aktive rad och tillämpas i Synapse-arbetsytor. Den här krypteringen använder befintliga nycklar eller nya nycklar som genereras i Azure Key Vault. En enskild nyckel används för att kryptera alla data i en arbets yta. Synapse-arbetsytor stöder RSA-nycklar med 2048 och 3072 byte storleks nycklar.

> [!NOTE]
> Synapse-arbetsytor stöder inte användning av ECC-nycklar (Elliptic-Curve Cryptography) för kryptering.

Data i följande Synapse-komponenter krypteras med den Kundhanterade nyckeln som kon figurer ATS på arbets ytans nivå:
* SQL-pooler
 * Dedikerade SQL-pooler
 * SQL-pooler utan Server
* Apache Spark-pooler
* Azure Data Factory integrerings körningar, pipeliner, data uppsättningar.

## <a name="workspace-encryption-configuration"></a>Konfiguration av arbets ytans kryptering

Arbets ytor kan konfigureras för att aktivera Double Encryption med en kundhanterad nyckel när arbets ytan skapas. Välj alternativet "Aktivera Double Encryption med en kundhanterad nyckel" på fliken "säkerhet" när du skapar den nya arbets ytan. Du kan välja att ange en nyckel-ID-URI eller välja från en lista över nyckel valv i **samma region** som arbets ytan. Key Vault måste ha **rensnings skyddet aktiverat**.

> [!IMPORTANT]
> Det går inte att ändra konfigurations inställningen för Double Encryption när arbets ytan har skapats.

:::image type="content" source="./media/workspaces-encryption/workspaces-encryption.png" alt-text="Det här diagrammet visar det alternativ som måste väljas för att aktivera en arbets yta för dubbel kryptering med en kundhanterad nyckel.":::

### <a name="key-access-and-workspace-activation"></a>Åtkomst till nyckel och arbets yta

Azure-Synapse krypterings modell med Kundhanterade nycklar omfattar arbets ytan som har åtkomst till nycklarna i Azure Key Vault för att kryptera och dekryptera efter behov. Nycklarna görs tillgängliga för arbets ytan antingen via en åtkomst princip eller [Azure Key Vault RBAC-åtkomst](../../key-vault/general/rbac-guide.md). När du beviljar behörigheter via en Azure Key Vault åtkomst princip väljer du alternativet ["endast program"](../../key-vault/general/secure-your-key-vault.md#key-vault-authentication-options) när du skapar principer (Välj arbets ytans hanterade identitet och Lägg inte till den som ett auktoriserat program).

 Arbets ytans hanterade identitet måste beviljas de behörigheter som krävs för nyckel valvet innan arbets ytan kan aktive ras. Med den här stegvisa metoden för aktivering av arbets ytor ser du till att data i arbets ytan krypteras med den Kundhanterade nyckeln. Observera att kryptering kan aktive ras eller inaktive ras för dedikerade SQL-pooler – varje pool är inte aktive rad för kryptering som standard.

#### <a name="permissions"></a>Behörigheter

För att kryptera eller dekryptera data i vila måste den hanterade arbets ytan ha följande behörigheter:
* WrapKey (för att infoga en nyckel i Key Vault när du skapar en ny nyckel).
* UnwrapKey (för att hämta nyckeln för dekryptering).
* Hämta (om du vill läsa den offentliga delen av en nyckel)

#### <a name="workspace-activation"></a>Aktivering av arbets yta

När din arbets yta (med dubbel kryptering aktive rad) har skapats, behålls den i ett väntande tillstånd tills aktiveringen lyckas. Du måste aktivera arbets ytan innan du kan använda alla funktioner fullt ut. Du kan till exempel bara skapa en ny dedikerad SQL-pool när aktiveringen lyckas. Ge arbets ytan hanterad identitets åtkomst till nyckel valvet och klicka på aktiverings länken i arbets ytan Azure Portal banderoll. När aktiveringen har slutförts är din arbets yta redo att användas med garantin att alla data i den skyddas med din Kundhanterade nyckel. Som tidigare nämnts måste nyckel valvet ha rensnings skyddet aktiverat för att aktiveringen ska lyckas.

:::image type="content" source="./media/workspaces-encryption/workspace-activation.png" alt-text="Det här diagrammet visar banderollen med aktiverings länken för arbets ytan.":::


### <a name="manage-the-workspace-customer-managed-key"></a>Hantera den Kundhanterade nyckeln för arbets ytan 

Du kan ändra den Kundhanterade nyckeln som används för att kryptera data från sidan **kryptering** i Azure Portal. Här kan du välja en ny nyckel med en nyckel identifierare eller välja från nyckel valv som du har åtkomst till i samma region som arbets ytan. Om du väljer en nyckel i ett annat nyckel valv än de som tidigare har använts, beviljar du behörigheterna "Get", "wrap" och "unwrap" för arbets ytan för det nya nyckel valvet. Arbets ytan kommer att verifiera åtkomsten till det nya nyckel valvet och alla data i arbets ytan krypteras igen med den nya nyckeln.

:::image type="content" source="./media/workspaces-encryption/workspace-encryption-management.png" alt-text="Det här diagrammet visar sektionen arbets ytans kryptering i Azure Portal.":::

>[!IMPORTANT]
>När du ändrar krypterings nyckeln för en arbets yta behåller du nyckeln tills du ersätter den i arbets ytan med en ny nyckel. Detta är att tillåta dekryptering av data med den gamla nyckeln innan den krypteras igen med den nya nyckeln.

Azure Key Vaults-principer för automatisk, periodisk rotation av nycklar eller åtgärder på nycklarna kan resultera i att nya nyckel versioner skapas. Du kan välja att kryptera alla data på arbets ytan på nytt med den senaste versionen av den aktiva nyckeln. Att omkryptera, ändra nyckeln i Azure Portal till en tillfällig nyckel och gå tillbaka till den nyckel som du vill använda för kryptering. Om du exempelvis vill uppdatera data kryptering med den senaste versionen av Active Key KEY1, ändrar du den Kundhanterade nyckeln för arbets ytan till temporär nyckel, Key2. Vänta tills krypteringen har slutförts med Key2. Byt sedan tillbaka den Kundhanterade nyckeln för arbets ytan till KEY1-data i arbets ytan igen med den senaste versionen av KEY1.

> [!NOTE]
> Azure Synapse Analytics arbetar aktivt med att lägga till funktioner för automatisk Omkryptering av data när nya nyckel versioner skapas. Innan den här funktionen är tillgänglig kan du tvinga fram en ny kryptering av data med hjälp av processen som beskrivs ovan för att säkerställa konsekvensen på arbets ytan.

#### <a name="sql-transparent-data-encryption-with-service-managed-keys"></a>SQL-transparent datakryptering med tjänst nycklar som hanteras

SQL transparent datakryptering (TDE) är tillgängligt för dedikerade SQL-pooler i arbets ytor som *inte* har Aktiver ATS för dubbel kryptering. I den här typen av arbets yta används en tjänst-hanterad nyckel för att tillhandahålla Double kryptering för data i dedikerade SQL-pooler. TDE med den tjänst-hanterade nyckeln kan aktive ras eller inaktive ras för enskilda dedikerade SQL-pooler.

## <a name="next-steps"></a>Nästa steg

[Använd inbyggda Azure-principer för att implementera krypterings skydd för Synapse-arbetsytor](../policy-reference.md)

