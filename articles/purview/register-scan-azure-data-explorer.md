---
title: Så här skannar du Azure-Datautforskaren
description: I den här guiden beskrivs hur du skannar Azure-Datautforskaren.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: 7adc7f568fb82692f2c96f610575076e397bd99c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896170"
---
# <a name="register-and-scan-azure-data-explorer"></a>Registrera och skanna Azure-Datautforskaren

Den här artikeln beskriver hur du registrerar ett Azure Datautforskaren-konto i Azure avdelningens kontroll och konfigurerar en genomsökning.

## <a name="supported-capabilities"></a>Funktioner som stöds

Azure Datautforskaren stöder fullständiga och stegvisa genomsökningar för att avbilda metadata och scheman. Genomsökningar klassificerar även data automatiskt baserat på system-och anpassade klassificerings regler.

## <a name="prerequisites"></a>Förutsättningar

- Skapa ett Azure avdelningens kontroll-konto innan du registrerar data källor. Mer information om hur du skapar ett avdelningens kontroll-konto finns i [snabb start: skapa ett Azure avdelningens kontroll-konto](create-catalog-portal.md).
- Du måste vara en Azure avdelningens kontroll data source-administratör

## <a name="setting-up-authentication-for-a-scan"></a>Konfigurera autentisering för en sökning

Det finns bara ett sätt att konfigurera autentisering för Azure Data Explorer:

- Tjänstens huvudnamn

### <a name="service-principal"></a>Tjänstens huvudnamn

Om du vill använda tjänstens huvud namns autentisering för genomsökningar kan du använda en befintlig eller skapa en ny. 

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

#### <a name="granting-the-service-principal-access-to-your-azure-data-explorer-instance"></a>Bevilja tjänstens huvud namn åtkomst till Azure Data Explorer-instansen

1. Gå till Azure-portalen. Gå sedan till Azure Data Explorer-instansen.

1. Lägg till tjänstens huvud namn i **AllDatabasesViewer** -rollen på fliken **behörigheter** , som visas på följande skärm bild.

    :::image type="content" source="./media/register-scan-azure-data-explorer/permissions-auth.png" alt-text="Skärm bild för att lägga till tjänstens huvud namn i behörigheter" border="true":::

## <a name="register-an-azure-data-explorer-account"></a>Registrera ett Azure Datautforskaren-konto

Gör så här för att registrera ett nytt Azure Datautforskaren-konto (Kusto) i din data katalog:

1. Navigera till ditt avdelningens kontroll-konto
1. Välj **källor** i det vänstra navigerings fältet
1. Välj **register**
1. Välj **Azure-datautforskaren** på **register källor**
1. Välj **Fortsätt**

:::image type="content" source="media/register-scan-azure-data-explorer/register-new-data-source.png" alt-text="Registrera ny data Källa" border="true":::

Gör följande på skärmen **Registrera källor (Azure datautforskaren (Kusto))** :

1. Ange ett **namn** som data källan ska visas med i katalogen.
1. Välj hur du vill peka på önskat lagrings konto:
   1. Välj **från Azure-prenumeration**, Välj lämplig prenumeration i list rutan **Azure-prenumeration** och lämpligt kluster i list rutan **kluster** .
   1. Alternativt kan du välja **ange manuellt** och ange en tjänst slut punkt (URL).
1. **Slutför** för att registrera data källan.

:::image type="content" source="media/register-scan-azure-data-explorer/register-sources.png" alt-text="alternativ för registrering av källor" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans-azure-data-explorer.md)]

## <a name="next-steps"></a>Nästa steg

- [Bläddra i Azure avdelningens kontroll Data Catalog](how-to-browse-catalog.md)
- [Sök i Azure avdelningens kontroll-Data Catalog](how-to-search-catalog.md)
