---
title: Felsöka dina anslutningar i Azure avdelningens kontroll
description: I den här artikeln beskrivs stegen för att felsöka dina anslutningar i Azure avdelningens kontroll.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 8be5def6d4780fd8b2ab65c5c9e1104a869f6d5a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391600"
---
# <a name="troubleshoot-your-connections-in-azure-purview"></a>Felsöka dina anslutningar i Azure avdelningens kontroll

Den här artikeln beskriver hur du felsöker anslutnings fel när du konfigurerar genomsökningar på data källor i Azure avdelningens kontroll.

## <a name="permission-the-credential-on-the-data-source"></a>Behörighet för data källans autentiseringsuppgifter

Om du använder en hanterad identitet eller tjänstens huvud namn som autentiseringsmetod för genomsökningar måste du tillåta dessa identiteter att ha åtkomst till data källan.

Det finns vissa instruktioner för varje källtyp:

- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Cosmos DB](register-scan-azure-cosmos-database.md#setting-up-authentication-for-a-scan)
- [Azure-datautforskaren](register-scan-azure-data-explorer.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database Hanterad instans](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
- [SQL Server](register-scan-on-premises-sql-server.md#setting-up-authentication-for-a-scan)
- [Power BI](register-scan-power-bi-tenant.md)

## <a name="storing-your-credential-in-your-key-vault-and-using-the-right-secret-name-and-version"></a>Lagra dina autentiseringsuppgifter i ditt nyckel valv och Använd rätt hemliga namn och version

Du måste också lagra autentiseringsuppgifterna i Azure Key Vault-instansen och använda rätt hemliga namn och version.

Kontrol lera detta genom att följa stegen nedan:

1. Navigera till din Key Vault.
1. Välj **Inställningar** > **Hemligheter**.
1. Välj den hemlighet som du använder för att autentisera mot data källan för genomsökningar.
1. Välj den version som du vill använda och kontrol lera att lösen ordet eller konto nyckeln är korrekt genom att klicka på **Visa hemligt värde**. 

## <a name="verify-permissions-for-the-purview-managed-identity-on-your-azure-key-vault"></a>Kontrol lera behörigheterna för den avdelningens kontroll-hanterade identiteten på din Azure Key Vault

Kontrol lera att rätt behörigheter har kon figurer ATS för den avdelningens kontroll-hanterade identiteten för att få åtkomst till din Azure Key Vault.

Verifiera detta genom att utföra följande steg:

1. Navigera till ditt nyckel valv och till avsnittet **åtkomst principer**
1. Kontrol lera att din avdelningens kontroll-hanterade identitet visas under avsnittet *aktuella åtkomst principer* med behörigheterna **Hämta** och **lista** på hemligheter minst

Om du inte ser din avdelningens kontroll-hanterade identitet visas följer du stegen i [skapa och hantera autentiseringsuppgifter för genomsökningar](manage-credentials.md) för att lägga till den. 

## <a name="next-steps"></a>Nästa steg

- [Bläddra i Azure avdelningens kontroll Data Catalog](how-to-browse-catalog.md)
- [Sök i Azure avdelningens kontroll-Data Catalog](how-to-search-catalog.md)
