---
title: Skapa och hantera autentiseringsuppgifter för genomsökningar
description: Lär dig mer om stegen för att skapa och hantera autentiseringsuppgifter i Azure avdelningens kontroll.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: 3802d25ebd8f21ab5b8991a66ceb6650f2f276a9
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2021
ms.locfileid: "103461716"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Autentiseringsuppgifter för Source Authentication i Azure avdelningens kontroll

I den här artikeln beskrivs hur du kan skapa autentiseringsuppgifter i Azure avdelningens kontroll. Dessa sparade autentiseringsuppgifter gör att du snabbt kan återanvända och använda Sparad autentiseringsinformation för dina data källor.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure Key Vault. Information om hur du skapar ett finns i [snabb start: skapa ett nyckel valv med hjälp av Azure Portal](../key-vault/general/quick-create-portal.md).

## <a name="introduction"></a>Introduktion

En autentiseringsuppgift är autentiseringsinformation som Azure avdelningens kontroll kan använda för att autentisera till dina registrerade data källor. Ett Credential-objekt kan skapas för olika typer av autentiserings scenarier, till exempel grundläggande autentisering som kräver användar namn/lösen ord. Autentiseringsuppgift som samlar in viss information som krävs för att autentisera, baserat på den valda typen av autentiseringsmetod. Autentiseringsuppgifterna använder dina befintliga Azure Key Vaults-hemligheter för att hämta känslig autentiseringsinformation under processen för skapande av autentiseringsuppgifter.

## <a name="use-purview-managed-identity-to-set-up-scans"></a>Konfigurera genomsökningar med avdelningens kontroll-hanterad identitet

Om du använder den avdelningens kontroll-hanterade identiteten för att konfigurera genomsökningar behöver du inte uttryckligen skapa en autentiseringsuppgift och länka ditt nyckel valv till avdelningens kontroll för att lagra dem. Detaljerade anvisningar om hur du lägger till avdelningens kontroll-hanterad identitet för att få åtkomst att söka igenom data källorna finns i avsnitten om autentisering av data källa nedan:

- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database Hanterad instans](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Skapa Azure Key Vault-anslutningar i ditt Azure avdelningens kontroll-konto

Innan du kan skapa en autentiseringsuppgift måste du först associera en eller flera av dina befintliga Azure Key Vault-instanser med ditt Azure avdelningens kontroll-konto.

1. Från [Azure Portal](https://portal.azure.com)väljer du ditt Azure avdelningens kontroll-konto och öppnar Azure avdelningens kontroll Studio. Navigera till **Management Center** på Azure avdelningens kontroll Studio och navigera sedan till **autentiseringsuppgifter**.

2. Välj **hantera Key Vault anslutningar** på sidan **autentiseringsuppgifter** .

   :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="Hantera Azure Key Vault anslutningar":::

3. Välj **+ nytt** på sidan Hantera Key Vault anslutningar.

4. Ange nödvändig information och välj sedan **skapa**.

5. Bekräfta att Key Vault har kopplats till ditt Azure avdelningens kontroll-konto som det visas i det här exemplet:

   :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="Visa Azure Key Vault anslutningar som ska bekräftas.":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>Bevilja avdelningens kontroll hanterad identitets åtkomst till din Azure Key Vault

1. Navigera till din Azure Key Vault.

2. Välj sidan **åtkomst principer** .

3. Välj **Lägg till åtkomst princip**.

   :::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="Lägg till avdelningens kontroll MSI i AKV":::

4. Välj **Get** -och **list** -behörigheter i list rutan **hemligheter** .

5. För **Välj huvud konto** väljer du den avdelningens kontroll-hanterade identiteten. Du kan söka efter avdelningens kontroll-MSI med antingen avdelningens kontroll-instansnamnet **eller** det hanterade ID: t för identitets programmet. Vi stöder för närvarande inte sammansatta identiteter (hanterat identitets namn + program-ID).

   :::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="Lägg till åtkomst princip":::

6. Välj **Lägg till**.

7. Välj **Spara** för att spara åtkomst principen.

   :::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="Spara åtkomst princip":::

## <a name="create-a-new-credential"></a>Skapa en ny autentiseringsuppgift

Dessa autentiseringstyper stöds i avdelningens kontroll:

- Grundläggande autentisering: du lägger till **lösen ordet** som en hemlighet i Key Vault.
- Tjänstens huvud namn: du lägger till **tjänstens huvud namn** som en hemlighet i Key Vault.
- SQL-autentisering: du lägger till **lösen ordet** som en hemlighet i Key Vault.
- Konto nyckel: du lägger till **konto nyckeln** som en hemlighet i Key Vault.
- Roll ARN: för en Amazon S3-data Källa lägger du till din **roll ARN** i AWS. 

Mer information finns i [lägga till en hemlighet till Key Vault](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault) och [skapa en ny AWS-roll för avdelningens kontroll](register-scan-amazon-s3.md#create-a-new-aws-role-for-purview).

När du har lagrat dina hemligheter i nyckel valvet:

1. Gå till sidan autentiseringsuppgifter i Azure-avdelningens kontroll.

2. Skapa din nya autentiseringsuppgift genom att välja **+ ny**.

3. Ange den information som krävs. Välj **autentiseringsmetoden** och en **Key Vault anslutning** som du vill välja en hemlighet från.

4. När all information har fyllts i väljer du **skapa**.

   :::image type="content" source="media/manage-credentials/new-credential.png" alt-text="Ny autentiseringsuppgift":::

5. Kontrol lera att dina nya autentiseringsuppgifter visas i listvyn och är redo att användas.

   :::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="Visa autentiseringsuppgifter":::

## <a name="manage-your-key-vault-connections"></a>Hantera dina nyckel valvs anslutningar

1. Sök/hitta Key Vault anslutningar efter namn

   :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="Sök nyckel valv":::

2. Ta bort en eller flera Key Vault anslutningar

   :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="Ta bort nyckel valv":::

## <a name="manage-your-credentials"></a>Hantera dina autentiseringsuppgifter

1. Sök/Sök efter autentiseringsuppgifter efter namn.
  
2. Välj och gör uppdateringar till en befintlig autentiseringsuppgift.

3. Ta bort en eller flera autentiseringsuppgifter.

## <a name="next-steps"></a>Nästa steg

[Skapa en regeluppsättning för genomsökning](create-a-scan-rule-set.md)
