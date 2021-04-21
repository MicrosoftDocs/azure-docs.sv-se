---
title: Azure Synapse Analytics kryptering
description: En artikel som förklarar kryptering i Azure Synapse Analytics
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 11/19/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: 6ddafb0e76799e3d8011232534c505f97c79b22e
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751138"
---
# <a name="encryption-for-azure-synapse-analytics-workspaces"></a>Kryptering för Azure Synapse Analytics-arbetsytor

Den här artikeln beskriver:
* Kryptering av vilodata i Synapse Analytics arbetsytor.
* Konfiguration av Synapse-arbetsytor för att aktivera kryptering med en kund hanterad nyckel.
* Hantera nycklar som används för att kryptera data i arbetsytor.

## <a name="encryption-of-data-at-rest"></a>Kryptering av vilodata

En fullständig kryptering i vila-lösning säkerställer att data aldrig bevaras i okrypterad form. Dubbel kryptering av vilodata minimerar hot med två separata krypteringslager för att skydda mot kompromettering av ett enskilt lager. Azure Synapse Analytics ett andra krypteringslager för data på din arbetsyta med en kundhanteringsnyckel. Den här nyckeln skyddas i ditt [Azure Key Vault](../../key-vault/general/overview.md), vilket gör att du kan bli ägare till nyckelhantering och rotering.

Det första krypteringslagret för Azure-tjänster är aktiverat med plattformsbaserade nycklar. Som standard krypteras Azure-diskar och data i Azure Storage automatiskt i vila. Läs mer om hur kryptering används i Microsoft Azure i [Översikt över Azure Encryption.](../../security/fundamentals/encryption-overview.md)

## <a name="azure-synapse-encryption"></a>Azure Synapse kryptering

Det här avsnittet hjälper dig att bättre förstå hur kundhanteringsnyckelkryptering aktiveras och framtvingas i Synapse-arbetsytor. Den här krypteringen använder befintliga nycklar eller nya nycklar som genererats Azure Key Vault. En enda nyckel används för att kryptera alla data i en arbetsyta. Synapse-arbetsytor stöder RSA-nycklar med nycklar med 2048 och 3 072 byte.

> [!NOTE]
> Synapse-arbetsytor stöder inte användning Elliptic-Curve ECC-nycklar (Cryptography) för kryptering.

Data i följande Synapse-komponenter krypteras med den kund hanterade nyckeln som konfigurerats på arbetsytenivå:
* SQL-pooler
 * Dedikerade SQL-pooler
 * Serverlösa SQL-pooler
* Apache Spark-pooler
* Azure Data Factory integreringskörningar, pipelines, datauppsättningar.

## <a name="workspace-encryption-configuration"></a>Konfiguration av kryptering för arbetsyta

Arbetsytor kan konfigureras för att aktivera dubbel kryptering med en kund hanterad nyckel när arbetsytan skapas. Välj alternativet "Aktivera dubbel kryptering med en kund hanterad nyckel" på fliken "Säkerhet" när du skapar din nya arbetsyta. Du kan välja att ange en nyckelidentifierar-URI eller välja från en lista över nyckelvalv i **samma region** som arbetsytan. Själva Key Vault måste ha **rensningsskydd aktiverat.**

> [!IMPORTANT]
> Konfigurationsinställningen för dubbel kryptering kan inte ändras när arbetsytan har skapats.

:::image type="content" source="./media/workspaces-encryption/workspaces-encryption.png" alt-text="Det här diagrammet visar det alternativ som måste väljas för att aktivera en arbetsyta för dubbel kryptering med en kund hanterad nyckel.":::

### <a name="key-access-and-workspace-activation"></a>Nyckelåtkomst och aktivering av arbetsyta

Krypteringsmodellen Azure Synapse kund hanterade nycklar innebär att arbetsytan får åtkomst till nycklarna i Azure Key Vault krypterar och dekrypterar efter behov. Nycklarna görs tillgängliga för arbetsytan antingen via en åtkomstprincip eller genom Azure Key Vault [RBAC-åtkomst.](../../key-vault/general/rbac-guide.md) När du beviljar behörigheter via en Azure Key Vault-åtkomstprincip väljer du alternativet ["Endast program"](../../key-vault/general/security-overview.md#key-vault-authentication-options) när du skapar principen (välj arbetsytans hanterade identitet och lägg inte till den som ett auktoriserat program).

 Den hanterade identiteten för arbetsytan måste beviljas de behörigheter som krävs för nyckelvalvet innan arbetsytan kan aktiveras. Den här fasbaserade metoden för aktivering av arbetsytor säkerställer att data i arbetsytan krypteras med den kund hanterade nyckeln. Observera att kryptering kan aktiveras eller inaktiveras för dedikerade SQL-pooler – varje pool är inte aktiverad för kryptering som standard.

#### <a name="permissions"></a>Behörigheter

För att kryptera eller dekryptera vilodata måste den hanterade identiteten för arbetsytan ha följande behörigheter:
* WrapKey (för att infoga en nyckel Key Vault när du skapar en ny nyckel).
* UnwrapKey (för att hämta nyckeln för dekryptering).
* Hämta (för att läsa den offentliga delen av en nyckel)

#### <a name="workspace-activation"></a>Aktivering av arbetsyta

När arbetsytan (med dubbel kryptering aktiverad) har skapats förblir den i tillståndet "Väntar" tills aktiveringen lyckas. Arbetsytan måste aktiveras innan du kan använda alla funktioner fullt ut. Du kan till exempel bara skapa en ny dedikerad SQL-pool när aktiveringen lyckas. Ge arbetsytans hanterade identitet åtkomst till nyckelvalvet och klicka på aktiveringslänken i arbetsytans Azure Portal banderoll. När aktiveringen är klar är arbetsytan redo att användas med en garanti för att alla data i den skyddas med din kund hanterade nyckel. Som tidigare nämnts måste nyckelvalvet ha rensningsskydd aktiverat för att aktiveringen ska lyckas.

:::image type="content" source="./media/workspaces-encryption/workspace-activation.png" alt-text="Det här diagrammet visar banderollen med aktiveringslänken för arbetsytan.":::


### <a name="manage-the-workspace-customer-managed-key"></a>Hantera arbetsytans kundhanterade nyckel 

Du kan ändra den kund hanterade nyckeln som används för att kryptera data **från** sidan Kryptering i Azure Portal. Även här kan du välja en ny nyckel med hjälp av en nyckelidentifierare eller välja från Nyckelvalv som du har åtkomst till i samma region som arbetsytan. Om du väljer en nyckel i ett annat nyckelvalv än det som användes tidigare ger du arbetsytans hanterade identitet behörigheten "Get", "Wrap" och "Unwrap" för det nya nyckelvalvet. Arbetsytan verifierar åtkomsten till det nya nyckelvalvet och alla data på arbetsytan krypteras om med den nya nyckeln.

:::image type="content" source="./media/workspaces-encryption/workspace-encryption-management.png" alt-text="Det här diagrammet visar avsnittet Kryptering för arbetsytan i Azure Portal.":::

>[!IMPORTANT]
>När du ändrar krypteringsnyckeln för en arbetsyta behåller du nyckeln tills du ersätter den i arbetsytan med en ny nyckel. Detta är för att tillåta dekryptering av data med den gamla nyckeln innan de krypteras på nytt med den nya nyckeln.

Azure Key Vaults-principer för automatisk, periodisk rotation av nycklar eller åtgärder för nycklarna kan resultera i att nya nyckelversioner skapas. Du kan välja att omkryptera alla data på arbetsytan med den senaste versionen av den aktiva nyckeln. Om du vill kryptera om ändrar du nyckeln i Azure Portal till en tillfällig nyckel och växlar sedan tillbaka till den nyckel som du vill använda för kryptering. Om du till exempel vill uppdatera datakryptering med den senaste versionen av den aktiva nyckeln Key1 ändrar du arbetsytans kundhanteringsnyckel till den tillfälliga nyckeln Key2. Vänta tills krypteringen med Key2 har avslutats. Sedan växlar du tillbaka arbetsytans kund hanterade nyckel till Key1-data på arbetsytan och krypterar om den med den senaste versionen av Key1.

> [!NOTE]
> Azure Synapse Analytics arbetar aktivt med att lägga till funktioner för att automatiskt omkryptera data när nya nyckelversioner skapas. Fram till dess att den här funktionen är tillgänglig, för att säkerställa konsekvens i arbetsytan, framt tvingar du fram omkryptering av data med hjälp av den process som beskrivs ovan.

#### <a name="sql-transparent-data-encryption-with-service-managed-keys"></a>SQL transparent datakryptering med tjänst hanterade nycklar

SQL transparent datakryptering (TDE) är tillgängligt för dedikerade SQL-pooler på arbetsytor *som inte har* aktiverats för dubbel kryptering. I den här typen av arbetsyta används en tjänst hanterad nyckel för att tillhandahålla dubbel kryptering för data i de dedikerade SQL-poolerna. TDE med den tjänst hanterade nyckeln kan aktiveras eller inaktiveras för enskilda dedikerade SQL-pooler.

## <a name="next-steps"></a>Nästa steg

[Använda inbyggda Azure-principer för att implementera krypteringsskydd för Synapse-arbetsytor](../policy-reference.md)

