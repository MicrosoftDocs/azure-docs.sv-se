---
title: Registrera och skanna Azure Data Lake Storage (ADLS) gen1
description: I den här självstudien beskrivs hur du skannar data från Azure Data Lake Storage Gen1 till Azure dataavdelningens kontrolls.
author: shsandeep123
ms.author: sandeepshah
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: a831681f892de9f6aae50fa9a2fcf71e883fe6ba
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2020
ms.locfileid: "97693722"
---
# <a name="register-and-scan-azure-data-lake-storage-gen1"></a>Registrera och skanna Azure Data Lake Storage Gen1

Den här artikeln beskriver hur du registrerar Azure Data Lake Storage Gen1 som data källa i Azure avdelningens kontroll och konfigurerar en genomsökning på den.

> [!Note]
> Azure Data Lake Storage Gen2 är nu allmänt tillgänglig. Vi rekommenderar att du börjar använda den idag. Mer information finns på [produkt sidan](https://azure.microsoft.com/services/storage/data-lake-storage/).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den Azure Data Lake Storage Gen1 data källan har stöd för följande funktioner:

- **Fullständiga och stegvisa genomsökningar** för att avbilda metadata och klassificering i Azure Data Lake Storage gen1

- **Härkomst** mellan data till gångar för ADF-kopiering/data flödes aktiviteter

## <a name="prerequisites"></a>Förutsättningar

- Skapa ett Azure avdelningens kontroll-konto innan du registrerar data källor. Mer information om hur du skapar ett avdelningens kontroll-konto finns i [snabb start: skapa ett Azure avdelningens kontroll-konto](create-catalog-portal.md).
- Du måste vara en Azure avdelningens kontroll data source-administratör

## <a name="setting-up-authentication-for-a-scan"></a>Konfigurera autentisering för en sökning

Följande autentiseringsmetoder stöds för Azure Data Lake Storage Gen1:

- Hanterad identitet
- Tjänstens huvudnamn

### <a name="managed-identity-recommended"></a>Hanterad identitet (rekommenderas)

För enkel och säkerhet kanske du vill använda avdelningens kontroll MSI för att autentisera med ditt data lager.

Om du vill konfigurera en sökning med hjälp av Data Catalogs MSI måste du först ge ditt avdelningens kontroll-konto behörighet att söka igenom data källan. Det här steget måste utföras *innan* du konfigurerar din skanning eller så Miss söker sökningen.

#### <a name="adding-the-data-catalog-msi-to-an-azure-data-lake-storage-gen1-account"></a>Lägga till Data Catalog MSI till ett Azure Data Lake Storage Gen1-konto

Du kan lägga till katalogens MSI på prenumerationen, resurs gruppen eller resurs nivån, beroende på vad du vill att den ska ha genomsöknings behörighet på.

> [!Note]
> Du måste vara ägare till prenumerationen för att kunna lägga till en hanterad identitet på en Azure-resurs.

1. Från [Azure Portal](https://portal.azure.com)hittar du antingen prenumerationen, resurs gruppen eller resursen (till exempel ett Azure Data Lake Storage gen1 lagrings konto) som du vill tillåta katalogen att söka igenom.

2. Klicka på **Översikt** och välj sedan **data Utforskaren**

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="Välj åtkomstkontroll":::

3. Klicka på **åtkomst** i det övre navigerings fönstret

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="Klicka på åtkomst":::

4. Klicka på **Lägg till**. Lägg till **katalogen avdelningens kontroll** i urvalet Välj användare eller grupp. Välj **Läs** -och **Kör** behörigheter. Se till att välja **den här mappen och alla underordnade** i alternativet Lägg till i som visas i skärm bilden nedan och klicka på **OK** 
    :::image type="content" source="./media/register-scan-adls-gen1/managed-instance-authentication.png" alt-text="information om MSI-autentisering":::

5. Om nyckel valvet inte är anslutet till avdelningens kontroll måste du [skapa en ny nyckel valv anslutning](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

6. Skapa slutligen [en ny autentiseringsuppgift](manage-credentials.md#create-a-new-credential) med tjänstens huvud namn för att konfigurera din sökning
> [!Note]
> När du har lagt till katalogens MSI-fil på data källan, väntar du upp till 15 minuter innan behörigheterna kan spridas innan en sökning konfigureras.

Efter cirka 15 minuter bör katalogen ha rätt behörighet för att kunna söka igenom resurserna.

### <a name="service-principal"></a>Tjänstens huvudnamn

Om du vill använda ett huvud namn för tjänsten måste du först skapa ett av följande steg:

1. Navigera till [Azure Portal](https://portal.azure.com).

2. Välj **Azure Active Directory** på menyn till vänster.

3. Välj **Appregistreringar**.

4. Välj **+ ny program registrering**.

5. Ange ett namn för **programmet** (tjänstens huvud namn).

6. Välj **konton endast i den här organisations katalogen**.

7. För **omdirigerings-URI** väljer du **webb** och anger vilken URL du vill ha. Det behöver inte vara verkligt eller arbete.

8. Välj **Registrera**.

9. Kopiera värdena från både visnings namnet och program-ID: t.

#### <a name="adding-the-data-catalog-service-principal-to-an-azure-data-lake-storage-gen1-account"></a>Lägga till Data Catalog tjänstens huvud namn i ett Azure Data Lake Storage Gen1 konto
1. Från [Azure Portal](https://portal.azure.com)hittar du antingen prenumerationen, resurs gruppen eller resursen (till exempel ett Azure Data Lake Storage gen1 lagrings konto) som du vill tillåta katalogen att söka igenom.

2. Klicka på **Översikt** och välj sedan **data Utforskaren**

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="Välj åtkomstkontroll":::

3. Klicka på **åtkomst** i det övre navigerings fönstret

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="Klicka på åtkomst":::

4. Klicka på **Lägg till**. Lägg till **tjänstens huvud program** i valet Välj användare eller grupp. Välj **Läs** -och **Kör** behörigheter. Se till att välja **den här mappen och alla underordnade** i alternativet Lägg till i som visas i skärm bilden nedan och klicka på **OK** 
    :::image type="content" source="./media/register-scan-adls-gen1/service-principal-authentication.png" alt-text="information om tjänstens huvud namn för autentisering":::

5. Om nyckel valvet inte är anslutet till avdelningens kontroll måste du [skapa en ny nyckel valv anslutning](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

6. Skapa slutligen [en ny autentiseringsuppgift](manage-credentials.md#create-a-new-credential) med tjänstens huvud namn för att konfigurera din sökning.

## <a name="register-azure-data-lake-storage-gen1-data-source"></a>Registrera Azure Data Lake Storage Gen1 data Källa

Gör så här för att registrera ett nytt ADLS Gen1 konto i din data katalog:

1. Navigera till din avdelningens kontroll-Data Catalog.
2. Välj **källor** i det vänstra navigerings fältet.
3. Välj **register**
4. Välj **Azure Data Lake Storage gen1** på **register källor**. 
5. Välj **Fortsätt**.

Gör följande på skärmen registrera källor (Azure Data Lake Storage Gen1):

1. Ange ett **namn** som data källan ska visas med i katalogen.
2. Välj din prenumeration för att filtrera lagrings konton
3. Välj ett lagrings konto
4. Välj en samling eller skapa en ny (valfritt)
5. Slutför för att registrera data källan.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Nästa steg

- [Bläddra i Azure avdelningens kontroll Data Catalog](how-to-browse-catalog.md)
- [Sök i Azure avdelningens kontroll-Data Catalog](how-to-search-catalog.md)
