---
title: Kundhanterade nycklar för konto kryptering
titleSuffix: Azure Storage
description: Du kan använda din egen krypterings nyckel för att skydda data i ditt lagrings konto. När du anger en kundhanterad nyckel används nyckeln för att skydda och kontrollera åtkomsten till nyckeln som krypterar dina data. Kundhanterade nycklar ger större flexibilitet för att hantera åtkomstkontroller.
services: storage
author: tamram
ms.service: storage
ms.date: 03/05/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 92a83e03d486d0bf9344fbdcf705703ea788528c
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218578"
---
# <a name="customer-managed-keys-for-azure-storage-encryption"></a>Kundhanterade nycklar för Azure Storage kryptering

Du kan använda din egen krypterings nyckel för att skydda data i ditt lagrings konto. När du anger en kundhanterad nyckel används nyckeln för att skydda och kontrollera åtkomsten till nyckeln som krypterar dina data. Kundhanterade nycklar ger större flexibilitet för att hantera åtkomstkontroller.

Du måste använda antingen Azure Key Vault eller Azure Key Vault hanterad maskinvarubaserad säkerhets modell (HSM) (för hands version) för att lagra dina Kundhanterade nycklar. Du kan antingen skapa egna nycklar och lagra dem i Key Vault eller hanterad HSM, eller så kan du använda Azure Key Vault API: er för att generera nycklar. Lagrings kontot och nyckel valvet eller hanterad HSM måste finnas i samma region och i samma Azure Active Directory-klient (Azure AD), men de kan finnas i olika prenumerationer.

Mer information om Azure Key Vault finns i [Azure Key Vault?](../../key-vault/general/overview.md).

> [!IMPORTANT]
>
> Kryptering med Kundhanterade nycklar som lagras i Azure Key Vault hanterad HSM är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versioner av juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släpps till allmän tillgänglighet.
>
> Azure Key Vault och Azure Key Vault Managed HSM stöder samma API: er och hanterings gränssnitt för konfiguration.

## <a name="about-customer-managed-keys"></a>Om Kundhanterade nycklar

Följande diagram visar hur Azure Storage använder Azure Active Directory och ett nyckel valv eller en hanterad HSM för att göra förfrågningar med hjälp av kundhanterad nyckel:

![Diagram över hur Kundhanterade nycklar fungerar i Azure Storage](media/customer-managed-keys-overview/encryption-customer-managed-keys-diagram.png)

I följande lista beskrivs de numrerade stegen i diagrammet:

1. En Azure Key Vault administratör beviljar behörigheter till krypterings nycklar till den hanterade identitet som är kopplad till lagrings kontot.
2. En Azure Storage administratör konfigurerar kryptering med en kundhanterad nyckel för lagrings kontot.
3. Azure Storage använder den hanterade identitet som är kopplad till lagrings kontot för att autentisera åtkomst till Azure Key Vault via Azure Active Directory.
4. Azure Storage omsluter konto krypterings nyckeln med kund nyckeln i Azure Key Vault.
5. Vid Läs-och skriv åtgärder skickar Azure Storage begär anden till Azure Key Vault för att packa upp konto krypterings nyckeln för att utföra kryptering och dekryptering.

## <a name="customer-managed-keys-for-queues-and-tables"></a>Kundhanterade nycklar för köer och tabeller

Data som lagras i kö-och tabell lagring skyddas inte automatiskt av en kundhanterad nyckel när Kundhanterade nycklar har Aktiver ATS för lagrings kontot. Du kan också konfigurera dessa tjänster så att de ingår i det här skyddet när du skapar lagrings kontot.

Mer information om hur du skapar ett lagrings konto som stöder Kundhanterade nycklar för köer och tabeller finns i [skapa ett konto som stöder Kundhanterade nycklar för tabeller och köer](account-encryption-key-create.md).

Data i Blob Storage och Azure Files skyddas alltid av Kundhanterade nycklar när Kundhanterade nycklar har kon figurer ATS för lagrings kontot.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>Aktivera Kundhanterade nycklar för ett lagrings konto

När du konfigurerar en kundhanterad nyckel omsluts Azure Storage data krypterings nyckeln för kontot med den Kundhanterade nyckeln i det associerade nyckel valvet eller hanterad HSM. Att aktivera Kundhanterade nycklar påverkar inte prestanda och börjar gälla omedelbart.

När du aktiverar eller inaktiverar Kundhanterade nycklar, eller när du ändrar nyckeln eller nyckel versionen, ändras skyddet av rot krypterings nyckeln, men data i Azure Storage-kontot behöver inte krypteras igen.

Kundhanterade nycklar kan bara aktive ras på befintliga lagrings konton. Nyckel valvet eller hanterad HSM måste konfigureras för att ge behörighet till den hanterade identitet som är kopplad till lagrings kontot. Den hanterade identiteten är bara tillgänglig när lagrings kontot har skapats.

Du kan när som helst växla mellan Kundhanterade nycklar och Microsoft-hanterade nycklar. Mer information om Microsoft-hanterade nycklar finns i [om hantering av krypterings nycklar](storage-service-encryption.md#about-encryption-key-management).

Information om hur du konfigurerar Azure Storage kryptering med Kundhanterade nycklar i ett nyckel valv finns i [Konfigurera kryptering med Kundhanterade nycklar som lagras i Azure Key Vault](customer-managed-keys-configure-key-vault.md). Om du vill konfigurera Kundhanterade nycklar i en hanterad HSM, se [Konfigurera kryptering med Kundhanterade nycklar som lagras i Azure Key Vault hanterad HSM (för hands version)](customer-managed-keys-configure-key-vault-hsm.md).

> [!IMPORTANT]
> Kundhanterade nycklar är beroende av hanterade identiteter för Azure-resurser, en funktion i Azure AD. Hanterade identiteter stöder för närvarande inte scenarier mellan kataloger. När du konfigurerar Kundhanterade nycklar i Azure Portal tilldelas en hanterad identitet automatiskt till ditt lagrings konto under försättsblad. Om du senare flyttar prenumerationen, resurs gruppen eller lagrings kontot från en Azure AD-katalog till en annan överförs inte den hanterade identitet som är kopplad till lagrings kontot till den nya klienten, så Kundhanterade nycklar kanske inte längre fungerar. Mer information finns i **överföra en prenumeration mellan Azure AD-kataloger** i [vanliga frågor och svar och kända problem med hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

Azure Storage-kryptering stöder RSA-och RSA-HSM-nycklar i storlekarna 2048, 3072 och 4096. Mer information om nycklar finns i [om nycklar](../../key-vault/keys/about-keys.md).

Att använda ett nyckel valv eller en hanterad HSM har tillhör ande kostnader. Mer information finns i [Key Vault prissättning](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="update-the-key-version"></a>Uppdatera nyckel versionen

När du konfigurerar kryptering med Kundhanterade nycklar har du två alternativ för att uppdatera nyckel versionen:

- **Uppdatera nyckel versionen automatiskt:** Om du vill uppdatera en kundhanterad nyckel automatiskt när en ny version är tillgänglig, utelämnar du nyckel versionen när du aktiverar kryptering med Kundhanterade nycklar för lagrings kontot. Om nyckel versionen utelämnas kontrollerar Azure Storage nyckel valvet eller hanterad HSM dagligen för en ny version av en kundhanterad nyckel. Azure Storage använder automatiskt den senaste versionen av nyckeln.
- **Uppdatera nyckel versionen manuellt:** Om du vill använda en specifik version av en nyckel för Azure Storage kryptering anger du den nyckel versionen när du aktiverar kryptering med Kundhanterade nycklar för lagrings kontot. Om du anger nyckel versionen använder Azure Storage den versionen för kryptering tills du uppdaterar nyckel versionen manuellt.

    När nyckel versionen anges explicit måste du manuellt uppdatera lagrings kontot för att använda den nya nyckel versions-URI: n när en ny version skapas. Information om hur du uppdaterar lagrings kontot för att använda en ny version av nyckeln finns i [Konfigurera kryptering med Kundhanterade nycklar som lagras i Azure Key Vault](customer-managed-keys-configure-key-vault.md) eller [Konfigurera kryptering med Kundhanterade nycklar som lagras i Azure Key Vault hanterad HSM (för hands version)](customer-managed-keys-configure-key-vault-hsm.md).

När du uppdaterar nyckel versionen ändras skyddet av rot krypterings nyckeln, men data i Azure Storage-kontot krypteras inte igen. Det krävs ingen ytterligare åtgärd från användaren.

> [!NOTE]
> Om du vill rotera en nyckel skapar du en ny version av nyckeln i Key Vault eller hanterad HSM, enligt dina efterlevnadsprinciper. Du kan rotera nyckeln manuellt eller skapa en funktion för att rotera den enligt ett schema.

## <a name="revoke-access-to-customer-managed-keys"></a>Återkalla åtkomst till Kundhanterade nycklar

Du kan när som helst återkalla lagrings kontots åtkomst till den Kundhanterade nyckeln. När åtkomst till Kundhanterade nycklar har återkallats, eller när nyckeln har inaktiverats eller tagits bort, kan klienterna inte anropa åtgärder som läser från eller skriver till en BLOB eller dess metadata. Försök att anropa någon av följande åtgärder Miss lyckas med felkoden 403 (tillåts inte) för alla användare:

- [Visa en lista med blobar](/rest/api/storageservices/list-blobs), vid anrop med `include=metadata` parametern på begärande-URI
- [Hämta BLOB](/rest/api/storageservices/get-blob)
- [Hämta blobegenskaper](/rest/api/storageservices/get-blob-properties)
- [Hämta BLOB-metadata](/rest/api/storageservices/get-blob-metadata)
- [Ange BLOB-metadata](/rest/api/storageservices/set-blob-metadata)
- [Ögonblicks bilds-BLOB](/rest/api/storageservices/snapshot-blob)vid anrop med `x-ms-meta-name` begär ande huvudet
- [Kopiera blob](/rest/api/storageservices/copy-blob)
- [Kopiera BLOB från URL](/rest/api/storageservices/copy-blob-from-url)
- [Ange blobnivå](/rest/api/storageservices/set-blob-tier)
- [Spärra block](/rest/api/storageservices/put-block)
- [Skicka block från URL](/rest/api/storageservices/put-block-from-url)
- [Lägg till block](/rest/api/storageservices/append-block)
- [Lägg till block från URL](/rest/api/storageservices/append-block-from-url)
- [Placera blob](/rest/api/storageservices/put-blob)
- [Placerings sida](/rest/api/storageservices/put-page)
- [Lägg till sida från URL](/rest/api/storageservices/put-page-from-url)
- [BLOB för stegvis kopiering](/rest/api/storageservices/incremental-copy-blob)

Om du vill anropa dessa åtgärder igen återställer du åtkomsten till den Kundhanterade nyckeln.

Alla data åtgärder som inte listas i det här avsnittet kan fortsätta efter att Kundhanterade nycklar har återkallats eller att en nyckel har inaktiverats eller tagits bort.

Om du vill återkalla åtkomsten till Kundhanterade nycklar använder du [PowerShell](./customer-managed-keys-configure-key-vault.md#revoke-customer-managed-keys) eller [Azure CLI](./customer-managed-keys-configure-key-vault.md#revoke-customer-managed-keys).

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Kundhanterade nycklar för Azure Managed disks

Kundhanterade nycklar är också tillgängliga för hantering av kryptering av Azure Managed disks. Kundhanterade nycklar fungerar annorlunda för hanterade diskar än för Azure Storage resurser. Mer information finns i [kryptering på Server sidan av Azure Managed disks](../../virtual-machines/disk-encryption.md) for Windows eller [Server Side Encryption på Azure Managed disks](../../virtual-machines/disk-encryption.md) for Linux.

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering av vilande data](storage-service-encryption.md)
- [Konfigurera kryptering med kundhanterade nycklar som lagras i Azure Key Vault](customer-managed-keys-configure-key-vault.md)
- [Konfigurera kryptering med Kundhanterade nycklar som lagras i Azure Key Vault hanterad HSM (förhands granskning)](customer-managed-keys-configure-key-vault-hsm.md)