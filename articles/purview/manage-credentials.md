---
title: Skapa och hantera autentiseringsuppgifter för genomsökningar
description: I den här artikeln beskrivs stegen för att skapa och hantera autentiseringsuppgifter i Azure avdelningens kontroll.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 30004306d9ff44df04a26640a2bd7a09256fce25
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516680"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Autentiseringsuppgifter för Source Authentication i Azure avdelningens kontroll

I den här artikeln beskrivs hur du kan skapa autentiseringsuppgifter i Azure avdelningens kontroll för att snabbt kunna återanvända och använda Sparad autentiseringsinformation för dina data källor.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure Key Vault. Information om hur du skapar ett finns i [snabb start: skapa ett nyckel valv med hjälp av Azure Portal](../key-vault/general/quick-create-portal.md).

## <a name="introduction"></a>Introduktion
En autentiseringsuppgift är autentiseringsinformation som Azure avdelningens kontroll kan använda för att autentisera till dina registrerade data källor. Du kan skapa ett Credential-objekt för olika typer av autentiserings scenarier (till exempel grundläggande autentisering som kräver användar namn/lösen ord) och hämta den information som krävs baserat på den valda typen av autentiseringsmetod. Autentiseringsuppgifterna använder dina befintliga Azure Key Vaults-hemligheter för att hämta känslig autentiseringsinformation under processen för skapande av autentiseringsuppgifter.

## <a name="using-purview-managed-identity-to-set-up-scans"></a>Använda avdelningens kontroll-hanterad identitet för att konfigurera genomsökningar
Om du använder den avdelningens kontroll-hanterade identiteten för att konfigurera genomsökningar behöver du inte uttryckligen skapa en autentiseringsuppgift och länka ditt nyckel valv till avdelningens kontroll för att lagra dem. Detaljerade anvisningar om hur du lägger till avdelningens kontroll-hanterad identitet för att få åtkomst att söka igenom dina data källor finns i avsnitten om autentisering av data källa nedan:

- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Hanterad Azure SQL Database-instans](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Skapa Azure Key Vault-anslutningar i ditt Azure avdelningens kontroll-konto

Innan du kan skapa en autentiseringsuppgift måste du först associera en eller flera av dina befintliga Azure Key Vault-instanser med ditt Azure avdelningens kontroll-konto.

1. Från navigerings menyn i Azure avdelningens kontroll navigerar du till hanterings centret och navigerar sedan till autentiseringsuppgifter

2. I kommando fältet autentiseringsuppgifter väljer du hantera Key Vault anslutningar

    :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="Hantera AKV-anslutningar":::

3. Välj + nytt från panelen hantera Key Vault anslutningar 

4. Ange nödvändig information och välj sedan skapa

5. Bekräfta att din Key Vault har kopplats till ditt Azure avdelningens kontroll-konto

    :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="Visa AKV-anslutningar":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>Bevilja avdelningens kontroll hanterad identitets åtkomst till din Azure Key Vault

Navigera till ditt nyckel valv – > åtkomst principer – > Lägg till åtkomst princip. Bevilja behörigheten Hämta i list rutan behörighet för hemligheter och välj huvud konto som avdelningens kontroll MSI. 

:::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="Lägg till avdelningens kontroll MSI i AKV":::


:::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="Lägg till åtkomst princip":::


:::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="Spara åtkomst princip":::

## <a name="create-a-new-credential"></a>Skapa en ny autentiseringsuppgift

Typ av autentiseringsuppgift som stöds i avdelningens kontroll idag:
* Grundläggande autentisering: du kommer att lägga till **lösen ordet** som en hemlighet i Key Vault
* Tjänstens huvud namn: du lägger till **tjänstens huvud namn** som en hemlighet i Key Vault 
* SQL-autentisering: du kommer att lägga till **lösen ordet** som en hemlighet i Key Vault
* Konto nyckel: du kommer att lägga till **konto nyckeln** som en hemlighet i Key Vault

Här är mer information om hur du lägger till hemligheter i ett nyckel valv: (Infoga Key Vault-artikel)

När du har lagrat dina hemligheter i ditt nyckel valv skapar du din nya autentiseringsuppgift genom att välja + nytt från kommando fältet autentiseringsuppgifter. Ange den information som krävs, inklusive att välja autentiseringsmetoden och en Key Vault instans som du vill välja en hemlighet från. När all information har fyllts i klickar du på Skapa.

:::image type="content" source="media/manage-credentials/new-credential.png" alt-text="Ny autentiseringsuppgift":::

Kontrol lera att dina nya autentiseringsuppgifter visas i listvyn för autentiseringsuppgifter och är redo att användas

:::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="Visa autentiseringsuppgifter":::

## <a name="manage-your-key-vault-connections"></a>Hantera dina nyckel valvs anslutningar

1. Sök/hitta Key Vault anslutningar efter namn

    :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="Sök nyckel valv":::

1. Ta bort en eller flera Key Vault anslutningar
 
    :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="Ta bort nyckel valv":::

## <a name="manage-your-credentials"></a>Hantera dina autentiseringsuppgifter

1. Sök/Sök efter autentiseringsuppgifter efter namn
  
2. Välj och gör uppdateringar till en befintlig autentiseringsuppgift

3. Ta bort en eller flera autentiseringsuppgifter
