---
title: Så här söker du igenom Azure Files
description: I den här guiden beskrivs hur du skannar Azure Files.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/01/2020
ms.openlocfilehash: a0bd7a4cd8afafc16f05b4a37cd5723304ad931e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96554886"
---
# <a name="register-and-scan-azure-files"></a>Registrera och skanna Azure Files

## <a name="supported-capabilities"></a>Funktioner som stöds

Azure Files stöder fullständiga och stegvisa genomsökningar för att avbilda metadata och tillämpa klassificeringar på metadata, baserat på system-och kund klassificeringar.

## <a name="prerequisites"></a>Förutsättningar

- Skapa ett Azure avdelningens kontroll-konto innan du registrerar data källor. Mer information om hur du skapar ett avdelningens kontroll-konto finns i [snabb start: skapa ett Azure avdelningens kontroll-konto](create-catalog-portal.md).
- Du måste vara administratör av data källan för att kunna konfigurera och schemalägga genomsökningar. mer information finns i [katalog behörigheter](catalog-permissions.md) .

## <a name="register-an-azure-files-storage-account"></a>Registrera ett Azure Files lagrings konto

Gör så här för att registrera ett nytt Azure Files konto i din data katalog:

1. Navigera till din avdelningens kontroll-Data Catalog.
1. Välj **hanterings Center** i det vänstra navigerings fältet.
1. Välj **data källor** under **källor och genomsökning**.
1. Välj **+ Ny**.
1. Välj **Azure Files** på **register källor**. Välj **Fortsätt**.

:::image type="content" source="media/register-scan-azure-files/register-new-data-source.png" alt-text="Registrera ny data Källa" border="true":::

Gör följande på skärmen **Registrera källor (Azure Files)** :

1. Ange ett **namn** som data källan ska visas med i katalogen.
1. Välj hur du vill peka på önskat lagrings konto:
   1. Välj **från Azure-prenumeration**, Välj lämplig prenumeration i list rutan **Azure-prenumeration** och lämpligt lagrings konto i list rutan **lagrings konto namn** .
   1. Alternativt kan du välja **ange manuellt** och ange en tjänst slut punkt (URL).
1. **Slutför** för att registrera data källan.

:::image type="content" source="media/register-scan-azure-files/register-sources.png" alt-text="alternativ för registrering av källor" border="true":::

## <a name="set-up-authentication-for-a-scan"></a>Konfigurera autentisering för en sökning

Gör så här för att konfigurera autentisering för Azure Files lagring med hjälp av en konto nyckel:

1. Välj autentiseringsmetod som **konto nyckel**.
2. Välj **från alternativ för Azure-prenumeration** .
3. Välj din Azure-prenumeration där Azure Filess kontot finns.
4. Välj ditt lagrings konto namn i listan.
5. Klicka på **Finish**.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Nästa steg

- [Bläddra i Azure avdelningens kontroll Data Catalog](how-to-browse-catalog.md)
- [Sök i Azure avdelningens kontroll-Data Catalog](how-to-search-catalog.md)