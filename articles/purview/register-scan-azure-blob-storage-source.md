---
title: Så här skannar du Azure Storage BLOB
description: Lär dig hur du skannar Azure Blob Storage i din Azure avdelningens kontroll Data Catalog.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: b27b46c68d018d2ddf79d284b20cc05b51640891
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880649"
---
# <a name="register-and-scan-azure-blob-storage"></a>Registrera och genomsöka Azure Blob Storage

Den här artikeln beskriver hur du registrerar ett Azure Blob Storage-konto i avdelningens kontroll och konfigurerar en genomsökning.

## <a name="supported-capabilities"></a>Funktioner som stöds

Azure Blob Storage stöder fullständiga och stegvisa genomsökningar för att avbilda metadata och scheman. Den klassificerar också data automatiskt baserat på system-och anpassade klassificerings regler.

## <a name="prerequisites"></a>Förutsättningar

- Skapa ett Azure avdelningens kontroll-konto innan du registrerar data källor. Mer information om hur du skapar ett avdelningens kontroll-konto finns i [snabb start: skapa ett Azure avdelningens kontroll-konto](create-catalog-portal.md).
- Du måste vara en Azure avdelningens kontroll data source-administratör

## <a name="setting-up-authentication-for-a-scan"></a>Konfigurera autentisering för en sökning

Det finns tre sätt att konfigurera autentisering för Azure Blob Storage:

- Hanterad identitet
- Konto nyckel
- Tjänstens huvudnamn

### <a name="managed-identity-recommended"></a>Hanterad identitet (rekommenderas)

När du väljer **hanterad identitet**, för att konfigurera anslutningen, måste du först ge ditt avdelningens kontroll-konto behörighet att söka igenom data källan:

1. Navigera till ditt lagringskonto.
1. Välj **Access Control (IAM)** i den vänstra navigerings menyn. 
1. Välj **+ Lägg till**.
1. Ange **rollen** som **Storage BLOB data Reader** och ange ditt Azure avdelningens kontroll-kontonamn under **Välj** inmatnings ruta. Välj sedan **Spara** för att ge roll tilldelningen till ditt avdelningens kontroll-konto.

> [!Note]
> Mer information finns i steg i [ge åtkomst till blobbar och köer med hjälp av Azure Active Directory](../storage/common/storage-auth-aad.md)

### <a name="account-key"></a>Konto nyckel

När den valda autentiseringsmetoden är **konto nyckel** måste du hämta din åtkomst nyckel och lagra den i nyckel valvet:

1. Navigera till ditt lagrings konto
1. Välj **inställningar > åtkomst nycklar**
1. Kopiera *nyckeln* och spara den någonstans för nästa steg
1. Navigera till ditt nyckel valv
1. Välj **inställningar > hemligheter**
1. Välj **+ generera/importera** och ange **namn** och **värde** som *nyckel* från ditt lagrings konto
1. Välj **skapa** för att slutföra
1. Om nyckel valvet inte är anslutet till avdelningens kontroll måste du [skapa en ny nyckel valv anslutning](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Slutligen [skapar du en ny autentiseringsuppgift](manage-credentials.md#create-a-new-credential) med nyckeln för att konfigurera din sökning

### <a name="service-principal"></a>Tjänstens huvudnamn

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

#### <a name="granting-the-service-principal-access-to-your-blob-storage"></a>Bevilja tjänstens huvud namn åtkomst till Blob Storage

1. Navigera till ditt lagringskonto.
1. Välj **Access Control (IAM)** i den vänstra navigerings menyn. 
1. Välj **+ Lägg till**.
1. Ange **rollen** som **Storage BLOB data Reader** och ange tjänstens huvud namn eller objekt-ID under **Välj** indata box. Välj sedan **Spara** för att ge den här roll tilldelningen till tjänstens huvud namn.

## <a name="firewall-settings"></a>Brandväggsinställningar

> [!NOTE]
> Om du har en brand vägg som är aktive rad för lagrings kontot måste du använda autentiseringsmetoden för **hanterad identitet** när du konfigurerar en sökning.

1. Gå till ditt lagrings konto i [Azure Portal](https://portal.azure.com)
1. Navigera till **inställningar > nätverk** och
1. Välj **valda nätverk** under **Tillåt åtkomst från**
1. I avsnittet **brand vägg** väljer du **Tillåt att betrodda Microsoft-tjänster har åtkomst till det här lagrings kontot** och trycker på **Spara**

:::image type="content" source="./media/register-scan-azure-blob-storage-source/firewall-setting.png" alt-text="Skärm bild som visar brand Väggs inställning":::

## <a name="register-an-azure-blob-storage-account"></a>Registrera ett Azure Blob Storage-konto

Gör så här för att registrera ett nytt BLOB-konto i data katalogen:

1. Navigera till ditt avdelningens kontroll-konto
1. Välj **källor** i det vänstra navigerings fältet
1. Välj **register**
1. Välj **Azure-Blob Storage** på **register källor**
1. Välj **Fortsätt**

Gör följande på skärmen **Registrera källor (Azure Blob Storage)** :

1. Ange ett **namn** som data källan ska visas med i katalogen. 
1. Välj din prenumeration för att filtrera lagrings konton
1. Välj ett lagrings konto
1. Välj en samling eller skapa en ny (valfritt)
1. **Slutför** för att registrera data källan.

:::image type="content" source="media/register-scan-azure-blob-storage-source/register-sources.png" alt-text="alternativ för registrering av källor" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Nästa steg

- [Bläddra i Azure avdelningens kontroll Data Catalog](how-to-browse-catalog.md)
- [Sök i Azure avdelningens kontroll-Data Catalog](how-to-search-catalog.md)