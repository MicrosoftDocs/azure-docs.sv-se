---
title: Registrera och skanna Azure Data Lake Storage (ADLS) Gen2
description: I den här självstudien beskrivs hur du skannar Azure Data Lake Storage Gen2.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 4b7f71b5405708cc1988fafa5ca9c4628fe0d80b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98882407"
---
# <a name="register-and-scan-azure-data-lake-storage-gen2"></a>Registrera och skanna Azure Data Lake Storage Gen2

Den här artikeln beskriver hur du registrerar Azure Data Lake Storage Gen2 som data källa i Azure avdelningens kontroll och konfigurerar en genomsökning.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den Azure Data Lake Storage Gen2 data källan har stöd för följande funktioner:

- **Fullständiga och stegvisa genomsökningar** för att avbilda metadata och klassificering i Azure Data Lake Storage Gen2

- **Härkomst** mellan data till gångar för ADF-kopiering/data flödes aktiviteter

## <a name="prerequisites"></a>Förutsättningar

Skapa ett Azure avdelningens kontroll-konto innan du registrerar data källor. Mer information om hur du skapar ett avdelningens kontroll-konto finns i [snabb start: skapa ett Azure avdelningens kontroll-konto](create-catalog-portal.md).

### <a name="setting-up-authentication-for-a-scan"></a>Konfigurera autentisering för en sökning

Följande autentiseringsmetoder stöds för Azure Data Lake Storage Gen2:

- Hanterad identitet
- Tjänstens huvudnamn
- Konto nyckel

#### <a name="managed-identity-recommended"></a>Hanterad identitet (rekommenderas)

När du väljer **hanterad identitet**, för att konfigurera anslutningen, måste du först ge ditt avdelningens kontroll-konto behörighet att söka igenom data källan:

1. Navigera till ditt ADLS Gen2 lagrings konto.
1. Välj **Access Control (IAM)** i den vänstra navigerings menyn. 
1. Välj **+ Lägg till**.
1. Ange **rollen** som **Storage BLOB data Reader** och ange ditt Azure avdelningens kontroll-kontonamn under **Välj** inmatnings ruta. Välj sedan **Spara** för att ge roll tilldelningen till ditt avdelningens kontroll-konto.

> [!Note]
> Mer information finns i steg i [ge åtkomst till blobbar och köer med hjälp av Azure Active Directory](../storage/common/storage-auth-aad.md)

#### <a name="account-key"></a>Konto nyckel

När den valda autentiseringsmetoden är **konto nyckel** måste du hämta din åtkomst nyckel och lagra den i nyckel valvet:

1. Navigera till ditt ADLS Gne2-lagrings konto
1. Välj **inställningar > åtkomst nycklar**
1. Kopiera *nyckeln* och spara den någonstans för nästa steg
1. Navigera till ditt nyckel valv
1. Välj **inställningar > hemligheter**
1. Välj **+ generera/importera** och ange **namn** och **värde** som *nyckel* från ditt lagrings konto
1. Välj **skapa** för att slutföra
1. Om nyckel valvet inte är anslutet till avdelningens kontroll måste du [skapa en ny nyckel valv anslutning](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Slutligen [skapar du en ny autentiseringsuppgift](manage-credentials.md#create-a-new-credential) med nyckeln för att konfigurera din sökning

#### <a name="service-principal"></a>Tjänstens huvudnamn

Om du vill använda ett huvud namn för tjänsten kan du använda ett befintligt konto eller skapa ett nytt. 

> [!Note]
> Följ dessa steg om du behöver skapa ett nytt huvud namn för tjänsten:
> 1. Navigera till [Azure Portal](https://portal.azure.com).
> 1. Välj **Azure Active Directory** på menyn till vänster.
> 1. Välj **Appregistreringar**.
> 1. Välj **+ ny program registrering**.
> 1. Ange ett namn för **programmet** (tjänstens huvud namn).
> 1. Välj **konton endast i den här organisations katalogen**.
> 1. För omdirigerings **-** URI väljer du webb och anger vilken URL du vill ha. Det behöver inte vara verkligt eller arbete.
> 1. Välj **Registrera**.

Det krävs för att hämta tjänstens huvud namn för program-ID och hemlighet:

1. Navigera till tjänstens huvud namn i [Azure Portal](https://portal.azure.com)
1. Kopiera värdena **program-ID (klient) ID** från **Översikt** och **klient hemlighet** från **certifikat & hemligheter**.
1. Navigera till ditt nyckel valv
1. Välj **inställningar > hemligheter**
1. Välj **+ generera/importera** och ange **namnet** på ditt val och **värde** som **klient hemlighet** från tjänstens huvud namn
1. Välj **skapa** för att slutföra
1. Om nyckel valvet inte är anslutet till avdelningens kontroll måste du [skapa en ny nyckel valv anslutning](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Skapa slutligen [en ny autentiseringsuppgift](manage-credentials.md#create-a-new-credential) med tjänstens huvud namn för att konfigurera din sökning

##### <a name="granting-the-service-principal-access-to-your-adls-gen2-account"></a>Bevilja tjänstens huvud namn åtkomst till ditt ADLS Gen2-konto

1. Navigera till ditt lagringskonto.
1. Välj **Access Control (IAM)** i den vänstra navigerings menyn. 
1. Välj **+ Lägg till**.
1. Ange **rollen** som **Storage BLOB data Reader** och ange tjänstens huvud namn eller objekt-ID under **Välj** indata box. Välj sedan **Spara** för att ge den här roll tilldelningen till tjänstens huvud namn.
### <a name="firewall-settings"></a>Brandväggsinställningar

> [!NOTE]
> Om du har en brand vägg som är aktive rad för lagrings kontot måste du använda autentiseringsmetoden för **hanterad identitet** när du konfigurerar en sökning.

1. Gå till ditt ASLS Gen2 Storage-konto i [Azure Portal](https://portal.azure.com)
1. Navigera till **inställningar > nätverk** och
1. Välj **valda nätverk** under **Tillåt åtkomst från**
1. I avsnittet **undantag** väljer du **Tillåt att betrodda Microsoft-tjänster har åtkomst till det här lagrings kontot** och trycker på **Spara**

:::image type="content" source="./media/register-scan-adls-gen2/firewall-setting.png" alt-text="Skärm bild som visar brand Väggs inställning":::

## <a name="register-azure-data-lake-storage-gen2-data-source"></a>Registrera Azure Data Lake Storage Gen2 data Källa

Gör så här för att registrera ett nytt ADLS Gen2 konto i din data katalog:

1. Navigera till ditt avdelningens kontroll-konto
2. Välj **källor** i det vänstra navigerings fältet
3. Välj **register**
4. Välj **Azure Data Lake Storage Gen2** på **register källor**
5. Välj **Fortsätt**

Gör följande på skärmen **Registrera källor (Azure Data Lake Storage Gen2)** :

1. Ange ett **namn** som data källan ska visas med i katalogen.
2. Välj din prenumeration för att filtrera lagrings konton
3. Välj ett lagrings konto
4. Välj en samling eller skapa en ny (valfritt)
5. **Slutför** för att registrera data källan.

:::image type="content" source="media/register-scan-adls-gen2/register-sources.png" alt-text="alternativ för registrering av källor" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Nästa steg

- [Bläddra i Azure avdelningens kontroll Data Catalog](how-to-browse-catalog.md)
- [Sök i Azure avdelningens kontroll-Data Catalog](how-to-search-catalog.md)