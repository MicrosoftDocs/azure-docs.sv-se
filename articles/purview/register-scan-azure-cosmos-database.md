---
title: Så här genomsöker du Azure Cosmos Database (SQL API)
description: I den här guiden beskrivs hur du genomsöker Azure Cosmos Database (SQL API).
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: 1aaeed1973ebd15af312b722ab61938aa4271947
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97696248"
---
# <a name="register-and-scan-azure-cosmos-database-sql-api"></a>Registrera och skanna Azure Cosmos Database (SQL API)

Den här artikeln beskriver hur du registrerar ett SQL-konto (Azure Cosmos Database) i Azure avdelningens kontroll och konfigurerar en genomsökning.

## <a name="supported-capabilities"></a>Funktioner som stöds

Azure Cosmos Database (SQL API) stöder fullständiga och stegvisa genomsökningar för att avbilda metadata och scheman. Genomsökningar klassificerar även data automatiskt baserat på system-och anpassade klassificerings regler.

## <a name="prerequisites"></a>Förutsättningar

- Skapa ett Azure avdelningens kontroll-konto innan du registrerar data källor. Mer information om hur du skapar ett avdelningens kontroll-konto finns i [snabb start: skapa ett Azure avdelningens kontroll-konto](create-catalog-portal.md).
- Du måste vara en Azure avdelningens kontroll data source-administratör

## <a name="setting-up-authentication-for-a-scan"></a>Konfigurera autentisering för en sökning

Det finns bara ett sätt att konfigurera autentisering för Azure Cosmos Database (SQL API):

- Kontonyckel
 
### <a name="account-key"></a>Kontonyckel

När den valda autentiseringsmetoden är **konto nyckel** måste du hämta din åtkomst nyckel och lagra den i nyckel valvet:

1. Navigera till ditt Cosmos DB konto i Azure Portal 
1. Välj **inställnings**  >  **nycklar** 
1. Kopiera *nyckeln* och spara den någonstans för nästa steg
1. Navigera till ditt nyckel valv
1. Välj **inställningar > hemligheter**
1. Välj **+ generera/importera** och ange **namn** och **värde** som *nyckel* från ditt lagrings konto
1. Välj **skapa** för att slutföra
1. Om nyckel valvet inte är anslutet till avdelningens kontroll måste du [skapa en ny nyckel valv anslutning](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Slutligen [skapar du en ny autentiseringsuppgift](manage-credentials.md#create-a-new-credential) med nyckeln för att konfigurera din sökning

## <a name="register-an-azure-cosmos-database-sql-api-account"></a>Registrera ett konto för Azure Cosmos Database (SQL-API)

Gör så här för att registrera ett nytt konto för Azure Cosmos Database (SQL-databas) i data katalogen:

1. Navigera till ditt avdelningens kontroll-konto
1. Välj **källor** i det vänstra navigerings fältet
1. Välj **register**
1. Välj **Azure Cosmos dB (SQL API)** på **register källor**
1. Välj **Fortsätt**

:::image type="content" source="media/register-scan-azure-cosmos-database/register-new-data-source.png" alt-text="Registrera ny data Källa" border="true":::

Gör följande på skärmen **register källor (Azure Cosmos dB (SQL API))** :

1. Ange ett **namn** som data källan ska visas med i katalogen.
1. Välj hur du vill peka på önskat lagrings konto:
   1. Välj **från Azure-prenumeration**, Välj lämplig prenumeration i list rutan för **Azure-prenumeration** och lämpligt cosmosDB-konto från List rutan **Cosmos DB konto namn** .
   1. Alternativt kan du välja **ange manuellt** och ange en tjänst slut punkt (URL).
1. **Slutför** för att registrera data källan.

:::image type="content" source="media/register-scan-azure-cosmos-database/register-sources.png" alt-text="alternativ för registrering av källor" border="true":::


[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Nästa steg

- [Bläddra i Azure avdelningens kontroll Data Catalog](how-to-browse-catalog.md)
- [Sök i Azure avdelningens kontroll-Data Catalog](how-to-search-catalog.md)
