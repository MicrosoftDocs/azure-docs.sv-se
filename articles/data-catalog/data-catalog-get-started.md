---
title: 'Snabb start: skapa en Azure Data Catalog'
description: I den här snabb starten beskrivs hur du skapar en Azure Data Catalog med hjälp av Azure Portal.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: quickstart
ms.date: 05/26/2020
ms.openlocfilehash: a56e5a4cae7c5a8e931b074f08a7152e53a8eb31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674741"
---
# <a name="quickstart-create-an-azure-data-catalog"></a>Snabb start: skapa en Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Azure Data Catalog är en helt hanterad molntjänst som fungerar som ett registrerings- och identifieringssystem för datatillgångar på ett företag. En detaljerad översikt finns i [Vad är Azure Data Catalog?](overview.md)

Den här snabb starten hjälper dig att komma igång med att skapa en Azure Data Catalog.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

> [!Note]
> På grund av säkerhets kraven i Azure har Azure Data Catalog Transport Layer Security (TLS) 1,2. TLS 1,0 och TLS 1,1 har inaktiverats. Du kan stöta på fel när du kör registrerings verktyget om datorn inte har uppdaterats för TLS 1,2. Se [aktivera Transport Layer Security (1,2)](/mem/configmgr/core/plan-design/security/enable-tls-1-2) för att uppdatera datorn för TLS 1,2.

För att komma igång måste du ha:

* En [Microsoft Azure](https://azure.microsoft.com/)-prenumeration.
* Du måste ha en egen [Azure Active Directory-klient](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

Om du vill konfigurera Data Catalog måste du vara ägare eller medägare till en Azure-prenumeration.

## <a name="create-a-data-catalog"></a>Skapa en data katalog

Du kan bara etablera en datakatalog per organisation (Azure Active Directory-domän). Om ägaren eller medägaren av en Azure-prenumeration som tillhör den här Azure Active Directorys domänen redan har skapat en katalog kan du därför inte skapa en katalog igen även om du har flera Azure-prenumerationer. Du kan testa om en katalog har skapats av en användare i din Azure Active Directory-domän genom att gå till [startsidan för Azure Data Catalog](http://azuredatacatalog.com) och kontrollera om du ser katalogen. Om en katalog redan har skapats för dig hoppar du över följande procedur och går till nästa avsnitt.

1. Gå till [Azure Portal](https://portal.azure.com)  >  **skapa en resurs** och välj **Data Catalog**.

    ![Knappen Azure Data Catalog skapa](media/data-catalog-get-started/data-catalog-create.png)

2. Ange ett **namn** för data katalogen, den **prenumeration** som du vill använda, **platsen** för katalogen och **pris nivån**. Välj sedan **Skapa**.

3. Gå till [startsidan för Azure Data Catalog](http://azuredatacatalog.com) och klicka på **Publicera data**.

   ![Azure Data Catalog – knappen Publicera data](media/data-catalog-get-started/data-catalog-publish-data.png)

   Du kan också gå till start sidan för Data Catalog från [Data Catalog service-sidan](https://azure.microsoft.com/services/data-catalog) genom att välja **Kom igång**.

   ![Azure Data Catalog – landningssida för marknadsföring](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)

4. Gå till sidan **Inställningar** .

    ![Azure Data Catalog – etablering av datakatalog](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)

5. Expandera **priser** och verifiera din Azure Data Catalog- **utgåva** (kostnads fri eller standard).

    ![Azure Data Catalog – välj version](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)

6. Om du väljer *standard* Edition som pris nivå kan du expandera **säkerhets grupper** och aktivera auktorisering Active Directory säkerhets grupper för att få åtkomst till Data Catalog och aktivera automatisk justering av fakturering.

    ![Azure Data Catalog säkerhets grupper](media/data-catalog-get-started/data-catalog-standard-security-groups.png)

7. Expandera **Kataloganvändare** och klicka på **Lägg till** för att lägga till användare för datakatalogen. Du läggs automatiskt till i den här gruppen.

    ![Azure Data Catalog – användare](media/data-catalog-get-started/data-catalog-add-catalog-user.png)

8. Om du väljer *standard* Edition som pris nivå kan du expandera **ord lista administratörer** och klicka på **Lägg** till för att lägga till användare av ordlista-administratörer. Du läggs automatiskt till i den här gruppen.

    ![Azure Data Catalog ord lista administratörer](media/data-catalog-get-started/data-catalog-standard-glossary-admin.png)

9. Expandera **Katalogadministratörer** och klicka på **Lägg till** för att lägga till ytterligare administratörer för datakatalogen. Du läggs automatiskt till i den här gruppen.

    ![Azure Data Catalog – administratörer](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)

10. Expandera **Portal rubrik** och Lägg till ytterligare text som ska visas i Portal rubriken.

    ![Rubrik för Azure Data Catalog-Portal](media/data-catalog-get-started/data-catalog-portal-title.png)

11. När du är klar med sidan **Inställningar** går du till sidan **publicera** .

    ![Azure Data Catalog – skapa katalogen](media/data-catalog-get-started/data-catalog-created.png)

## <a name="find-a-data-catalog-in-the-azure-portal"></a>Hitta en datakatalog på Azure-portalen

1. Öppna en separat flik i webbläsaren eller ett separat webbläsarfönster, gå till [Azure-portalen](https://portal.azure.com) och logga in med samma konto som du använde för att skapa datakatalogen i föregående steg.

2. Välj **alla tjänster** och klicka sedan på **Data Catalog**.

    ![Azure Data Catalog – bläddra i Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png)

    Du ser data katalogen som du skapade.

    ![Azure Data Catalog – visa katalog i lista](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)

3. Klicka på den katalog som du skapade. Bladet **Datakatalog** visas på portalen.

   ![Azure Data Catalog – blad på portalen](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)

4. Du kan visa egenskaperna för datakatalogen och uppdatera dem. Klicka till exempel **Prisnivå** och ändra versionen.

    ![Azure Data Catalog – prisnivå](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar en Azure Data Catalog för din organisation. Nu kan du registrera data källor i din data katalog.

> [!div class="nextstepaction"]
> [Registrera data källor i Azure Data Catalog](data-catalog-how-to-register.md)