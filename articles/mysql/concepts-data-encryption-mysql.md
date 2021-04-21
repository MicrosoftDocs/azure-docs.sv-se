---
title: Datakryptering med kund hanterad nyckel – Azure Database for MySQL
description: Azure Database for MySQL datakryptering med en kund hanterad nyckel kan du Bring Your Own Key (BYOK) för dataskydd i vila. Det gör det även möjligt för organisationer att implementera ansvarsfördelning vad gäller hanteringen av nycklar och data.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 809827fc61cde3c8077484dbe2a5be5037609cd8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748556"
---
# <a name="azure-database-for-mysql-data-encryption-with-a-customer-managed-key"></a>Azure Database for MySQL datakryptering med en kund hanterad nyckel

Datakryptering med kundhanterade nycklar för Azure Database for MySQL gör att du kan ta med din egen nyckel (BYOK) för dataskydd i vila. Det gör det även möjligt för organisationer att implementera ansvarsfördelning vad gäller hanteringen av nycklar och data. Med kundhanterad kryptering ansvarar du för och har fullständig kontroll över en nyckels livscykel, behörigheter för nyckelanvändning och granskning av åtgärder på nycklar.

Datakryptering med kund hanterade nycklar Azure Database for MySQL, anges på servernivå. För en viss server används en kund hanterad nyckel, som kallas nyckelkrypteringsnyckel (KEK), för att kryptera datakrypteringsnyckeln (DEK) som används av tjänsten. KEK är en asymmetrisk nyckel som lagras i en kundägd och Azure Key Vault [instans.](../key-vault/general/security-overview.md) Nyckelkrypteringsnyckeln (KEK) och datakrypteringsnyckeln (DEK) beskrivs i detalj senare i den här artikeln.

Key Vault är ett molnbaserat, externt nyckelhanteringssystem. Den har hög tillgänglig och ger skalbar och säker lagring för RSA-kryptografiska nycklar, som eventuellt backas upp av FIPS 140-2 Level 2-verifierade maskinvarusäkerhetsmoduler (HSM). Den tillåter inte direkt åtkomst till en lagrad nyckel, men tillhandahåller tjänster för kryptering och dekryptering till behöriga entiteter. Key Vault kan generera nyckeln, importera den eller överföra [den från en lokal HSM-enhet](../key-vault/keys/hsm-protected-keys.md).

> [!NOTE]
> Den här funktionen är tillgänglig i alla Azure-regioner Azure Database for MySQL har stöd för prisnivåerna "Generell användning" och "Minnesoptimerad". Andra begränsningar finns i [begränsningsavsnittet.](concepts-data-encryption-mysql.md#limitations)

## <a name="benefits"></a>Fördelar

Datakryptering med kund hanterade nycklar för Azure Database for MySQL ger följande fördelar:

* Dataåtkomsten styrs helt av dig genom möjligheten att ta bort nyckeln och göra databasen otillgänglig 
* Fullständig kontroll över nyckellivscykeln, inklusive rotation av nyckeln för att passa företagets principer
* Central hantering och organisering av nycklar i Azure Key Vault
* Möjlighet att implementera uppdelning av uppgifter mellan säkerhetsansvariga samt DBA och systemadministratörer


## <a name="terminology-and-description"></a>Terminologi och beskrivning

**Datakrypteringsnyckel (DEK):** En symmetrisk AES256-nyckel som används för att kryptera en partition eller ett block med data. Kryptering av varje datablock med en annan nyckel gör det svårare att analysera kryptografiattacker. Åtkomst till DEK:er krävs av resursprovidern eller programinstansen som krypterar och dekrypterar ett visst block. När du ersätter en DEK med en ny nyckel måste endast data i dess associerade block krypteras om med den nya nyckeln.

**Nyckelkrypteringsnyckel (KEK):** En krypteringsnyckel som används för att kryptera DEK:erna. En KEK som aldrig lämnar Key Vault tillåter att DEK:erna själva krypteras och kontrolleras. Entiteten som har åtkomst till kek kan vara annorlunda än den entitet som kräver DEK. Eftersom KEK krävs för att dekryptera DEK:erna är KEK i praktiken en enda punkt där DEK:er effektivt kan tas bort genom borttagning av KEK.

DEK:erna, krypterade med KEK:erna, lagras separat. Endast en entitet med åtkomst till KEK kan dekryptera dessa DEK:er. Mer information finns i [Säkerhet i kryptering i vila.](../security/fundamentals/encryption-atrest.md)

## <a name="how-data-encryption-with-a-customer-managed-key-work"></a>Så här fungerar datakryptering med en kund hanterad nyckel

:::image type="content" source="media/concepts-data-access-and-security-data-encryption/mysqloverview.png" alt-text="Diagram som visar en översikt över Bring Your Own Key":::

För att en MySQL-server ska kunna använda kund hanterade nycklar som lagras i Key Vault för kryptering av DEK ger en Key Vault-administratör följande åtkomstbehörigheter till servern:

* **get**: För att hämta den offentliga delen och egenskaperna för nyckeln i nyckelvalvet.
* **wrapKey:** För att kunna kryptera DEK. Den krypterade DEK lagras i Azure Database for MySQL.
* **unwrapKey:** För att kunna dekryptera DEK. Azure Database for MySQL behöver dekrypterad DEK för att kryptera/dekryptera data

Nyckelvalvsadministratören kan [också aktivera loggning Key Vault granskningshändelser,](../azure-monitor/insights/key-vault-insights-overview.md)så att de kan granskas senare.

När servern är konfigurerad att använda den kund hanterade nyckeln som lagras i nyckelvalvet skickar servern DEK till nyckelvalvet för kryptering. Key Vault returnerar den krypterade DEK som lagras i användardatabasen. När det behövs skickar servern på motsvarande sätt den skyddade DEK:en till nyckelvalvet för dekryptering. Granskare kan använda Azure Monitor för att granska Key Vault granskningshändelseloggar om loggning är aktiverat.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>Krav för att konfigurera datakryptering för Azure Database for MySQL

Följande är krav för att konfigurera Key Vault:

* Key Vault och Azure Database for MySQL måste tillhöra samma Azure Active Directory (Azure AD). Interaktioner mellan Key Vault klientorganisation och server stöds inte. När Key Vault en resurs efteråt måste du konfigurera om datakrypteringen.
* Aktivera funktionen [för mjuk borttagning](../key-vault/general/soft-delete-overview.md) i nyckelvalvet med kvarhållningsperiod inställd på **90** dagar för att skydda mot dataförlust om en oavsiktlig nyckel (eller Key Vault)-borttagning sker. Mjukt borttagna resurser bevaras som standard i 90 dagar, såvida inte kvarhållningsperioden uttryckligen är inställd på <= 90 dagar. Åtgärderna för att återställa och rensa har sina egna behörigheter associerade i en Key Vault åtkomstprincip. Funktionen för mjuk borttagning är inaktiverad som standard, men du kan aktivera den via PowerShell eller Azure CLI (observera att du inte kan aktivera den via Azure Portal).
* Aktivera funktionen [Rensa skydd i](../key-vault/general/soft-delete-overview.md#purge-protection) nyckelvalvet med kvarhållningsperioden inställd på **90 dagar.** Rensningsskydd kan bara aktiveras när mjuk borttagning har aktiverats. Den kan aktiveras via Azure CLI eller PowerShell. När rensningsskydd är på kan ett valv eller ett objekt i borttagna tillstånd inte rensas förrän kvarhållningsperioden har passerat. Mjukt borttagna valv och objekt kan fortfarande återställas, vilket säkerställer att bevarandeprincipen följs. 
* Ge Azure Database for MySQL åtkomst till nyckelvalvet med behörigheterna get, wrapKey och unwrapKey med hjälp av dess unika hanterade identitet. I Azure Portal skapas den unika tjänstidentiteten automatiskt när datakryptering är aktiverat på MySQL. Detaljerade stegvisa instruktioner finns i Konfigurera datakryptering för [MySQL](howto-data-encryption-portal.md) när du använder Azure Portal.

Följande är krav för att konfigurera den kund hanterade nyckeln:

* Den kund hanterade nyckeln som ska användas för kryptering av DEK kan bara vara asymmetrisk, RSA 2048.
* Nyckelaktiveringsdatumet (om det har angetts) måste vara ett datum och en tid i det förflutna. Förfallodatumet har inte angetts.
* Nyckeln måste vara i tillståndet *Aktiverad.*
* Nyckeln måste ha mjuk [borttagning med kvarhållningsperioden](../key-vault/general/soft-delete-overview.md) inställd på **90 dagar.** Detta anger implicit det nödvändiga nyckelattributet recoveryLevel: "Återställningsbar". Om kvarhållningen är inställd på < 90 dagar ser du till att recoveryLevel: "CustomizedRecoverable" som inte är kravet så se till att ange kvarhållningsperioden är inställd på **90 dagar**.
* Rensningsskydd måste [vara aktiverat för nyckeln.](../key-vault/general/soft-delete-overview.md#purge-protection)
* Om du importerar [en befintlig nyckel till nyckelvalvet](/rest/api/keyvault/ImportKey/ImportKey) måste du ange den i filformat som stöds ( , , `.pfx` `.byok` `.backup` ).

## <a name="recommendations"></a>Rekommendationer

När du använder datakryptering med hjälp av en kund hanterad nyckel finns här rekommendationer för att konfigurera Key Vault:

* Ange ett resurslås på Key Vault att styra vem som kan ta bort den här kritiska resursen och förhindra oavsiktlig eller obehörig borttagning.
* Aktivera granskning och rapportering för alla krypteringsnycklar. Key Vault innehåller loggar som är enkla att mata in i annan säkerhetsinformation och händelsehanteringsverktyg. Azure Monitor Log Analytics är ett exempel på en tjänst som redan är integrerad.
* Se till Key Vault och Azure Database for MySQL finns i samma region för att säkerställa snabbare åtkomst för DEK-omslutning och packa upp åtgärder.
* Lås Azure KeyVault till endast privata **slutpunkter och valda nätverk** och tillåt endast *betrodda Microsoft-tjänster* att skydda resurserna.

    :::image type="content" source="media/concepts-data-access-and-security-data-encryption/keyvault-trusted-service.png" alt-text="trusted-service-with-AKV":::

Här är rekommendationer för att konfigurera en kund hanterad nyckel:

* Förvara en kopia av den kund hanterade nyckeln på en säker plats eller depositionsnyckeln till depositionstjänsten.

* Om Key Vault genererar nyckeln skapar du en nyckelsäkerhetskopia innan du använder nyckeln för första gången. Du kan bara återställa säkerhetskopian till Key Vault. Mer information om säkerhetskopieringskommandot finns [i Backup-AzKeyVaultKey.](/powershell/module/az.keyVault/backup-azkeyVaultkey)

## <a name="inaccessible-customer-managed-key-condition"></a>Otillgängligt kund hanterat nyckelvillkor

När du konfigurerar datakryptering med en kund hanterad nyckel i Key Vault krävs kontinuerlig åtkomst till den här nyckeln för att servern ska fortsätta vara online. Om servern förlorar åtkomst till den kund hanterade nyckeln i Key Vault börjar servern neka alla anslutningar inom 10 minuter. Servern utfärdar ett motsvarande felmeddelande och ändrar servertillståndet *till Otillgängligt*. Några av orsakerna till varför servern kan nå det här tillståndet är:

* Om vi skapar en återställningsserver för Azure Database for MySQL, som har datakryptering aktiverat, är den nyligen skapade *servern* otillgänglig. Du kan åtgärda detta via [Azure Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) eller [CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers).
* Om vi skapar en skrivskyddad replik för din Azure Database for MySQL, som har datakryptering aktiverat, är replikservern *i ett otillgängligt* tillstånd. Du kan åtgärda detta via [Azure Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) eller [CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers).
* Om du tar bort KeyVault kan Azure Database for MySQL inte komma åt nyckeln och kommer att flyttas *till otillgängligt* tillstånd. Återställ [Key Vault](../key-vault/general/key-vault-recovery.md) och återställ datakrypteringen så att servern blir *tillgänglig.*
* Om vi tar bort nyckeln från KeyVault kan Azure Database for MySQL inte komma åt nyckeln och kommer att gå över till *otillgängligt* tillstånd. Återställ nyckeln [och](../key-vault/general/key-vault-recovery.md) återställ datakrypteringen för att göra servern *tillgänglig.*
* Om nyckeln som lagras i Azure KeyVault upphör att gälla blir nyckeln ogiltig och Azure Database for MySQL övergår *till otillgängligt* tillstånd. Utöka nyckelns förfallodatum med [CLI och](/cli/azure/keyvault/key#az-keyvault-key-set-attributes) förnya sedan datakrypteringen så att servern *blir tillgänglig.*

### <a name="accidental-key-access-revocation-from-key-vault"></a>Oavsiktlig återkallelse av nyckelåtkomst från Key Vault

Det kan hända att någon med tillräcklig behörighet för att Key Vault av misstag inaktiverar serveråtkomst till nyckeln genom att:

* Återkalla nyckelvalvsbehörigheterna get, wrapKey och unwrapKey från servern.
* Ta bort nyckeln.
* Ta bort nyckelvalvet.
* Ändra nyckelvalvs brandväggsregler.
* Ta bort den hanterade identiteten för servern i Azure AD.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Övervaka den kund hanterade nyckeln i Key Vault

Konfigurera följande Azure-funktioner för att övervaka databastillståndet och aktivera aviseringar om förlust av transparent datakrypteringsskydd:

* [Azure Resource Health:](../service-health/resource-health-overview.md)En otillgänglig databas som har förlorat åtkomst till kundnyckeln visas som "Otillgänglig" när den första anslutningen till databasen har nekats.
* [Aktivitetslogg:](../service-health/alerts-activity-log-service-notifications-portal.md)När åtkomsten till kundnyckeln i den kund-Key Vault misslyckas läggs poster till i aktivitetsloggen. Du kan återställa åtkomsten så snart som möjligt om du skapar aviseringar för dessa händelser.

* [Åtgärdsgrupper:](../azure-monitor/alerts/action-groups.md)Definiera dessa grupper för att skicka meddelanden och aviseringar till dig baserat på dina inställningar.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Återställa och replikera med en kunds hanterade nyckel i Key Vault

När Azure Database for MySQL krypteras med en kunds hanterade nyckel som lagras i Key Vault krypteras även alla nyligen skapade kopior av servern. Du kan göra den här nya kopian antingen via en lokal eller geo-återställningsåtgärd eller via skrivskyddade repliker. Kopian kan dock ändras så att den återspeglar en ny kunds hanterade nyckel för kryptering. När den kund hanterade nyckeln ändras börjar gamla säkerhetskopior av servern med den senaste nyckeln.

För att undvika problem med att konfigurera kundstyrd datakryptering under återställning eller skapande av skrivskyddade repliker är det viktigt att du följer dessa steg på käll- och återställda/replikservrarna:

* Starta återställningen eller skrivskyddade repliker från Azure Database for MySQL.
* Behåll den nyligen skapade servern (återställd/replik) i ett otillgängligt tillstånd eftersom dess unika identitet ännu inte har fått behörighet att Key Vault.
* På den återställda/replikservern, återställ den kund hanterade nyckeln i inställningarna för datakryptering för att säkerställa att den nyligen skapade servern får omslutnings- och packa upp behörigheter till nyckeln som lagras i Key Vault.

## <a name="limitations"></a>Begränsningar

Till Azure Database for MySQL har stödet för kryptering av vilodata med kunders hanterade nyckel (CMK) få begränsningar –

* Stödet för den här funktionen är **begränsat Generell användning** och **minnesoptimerade** prisnivåer.
* Den här funktionen stöds bara i regioner och på servrar som har stöd för lagring på upp till 16 TB. En lista över Azure-regioner som stöder lagring på upp till 16 TB finns i lagringsavsnittet i dokumentationen [här](concepts-pricing-tiers.md#storage)

    > [!NOTE]
    > - Alla nya MySQL-servrar som skapats i de regioner som anges ovan har stöd för kryptering med Kundhanterarens **nycklar tillgängligt.** Återställning till tidpunkt (PITR)-server eller skrivskyddad replik är inte kvalificerat även om de i teorin är "nya".
    > - Om du vill kontrollera om den etablerade servern stöder upp till 16 TB kan du gå till prisnivåbladet i portalen och se den maximala lagringsstorlek som stöds av den etablerade servern. Om du kan flytta skjutreglaget upp till 4 TB stöder servern kanske inte kryptering med kund hanterade nycklar. Data krypteras dock alltid med hjälp av tjänst hanterade nycklar. Kontakta om AskAzureDBforMySQL@service.microsoft.com du har några frågor.

* Kryptering stöds endast med rsa 2048 kryptografinyckel.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du ställer in datakryptering med en kund hanterad nyckel för din Azure-databas för MySQL med hjälp [av Azure Portal](howto-data-encryption-portal.md) och [Azure CLI](howto-data-encryption-cli.md).
