---
title: Registrera och skanna en Power BI klientorganisation (förhandsversion)
description: Lär dig hur du använder Azure Purview-portalen för att registrera och skanna en Power BI klientorganisation.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: 8fb4c797df7961726ca785a56a6ab25807999842
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600870"
---
# <a name="register-and-scan-a-power-bi-tenant-preview"></a>Registrera och skanna en Power BI klientorganisation (förhandsversion)

Den här artikeln visar hur du använder Azure Purview-portalen för att registrera och skanna Power BI klientorganisation.

> [!Note]
> Om Purview-instansen och Power BI-klienten finns i samma Azure-klientorganisation kan du bara använda hanterad identitetsautentisering (MSI) för att konfigurera en genomsökning av en Power BI klientorganisation. 

## <a name="create-a-security-group-for-permissions"></a>Skapa en säkerhetsgrupp för behörigheter

Om du vill konfigurera autentisering skapar du en säkerhetsgrupp och lägger till den hanterade identiteten Purview i den.

1. I [Azure Portal](https://portal.azure.com)du efter **Azure Active Directory**.
1. Skapa en ny säkerhetsgrupp i din Azure Active Directory genom att följa [Skapa en grundläggande grupp och lägga till medlemmar med hjälp av Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    > [!Tip]
    > Du kan hoppa över det här steget om du redan har en säkerhetsgrupp som du vill använda.

1. Välj **Säkerhet** som **Grupptyp.**

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Typ av säkerhetsgrupp":::

1. Lägg till din Purview-hanterade identitet i den här säkerhetsgruppen. Välj **Medlemmar** och välj sedan **+ Lägg till medlemmar.**

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Lägg till katalogens hanterade instans i gruppen.":::

1. Sök efter den hanterade Purview-identiteten och välj den.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Lägg till katalog genom att söka efter den":::

    Du bör se ett meddelande om att det har lagts till.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="Lägg till MSI-katalog":::

## <a name="associate-the-security-group-with-the-tenant"></a>Associera säkerhetsgruppen med klientorganisationen

1. Logga in på [Power BI-administratörsportalen.](https://app.powerbi.com/admin-portal/tenantSettings)
1. Välj sidan **Klientinställningar.**

    > [!Important]
    > Du måste vara en Power BI administratör för att se sidan klientinställningar.

1. Välj **Inställningar för administratörs-API**  >  **Tillåt att tjänstens huvudnamn använder skrivskyddade Power BI-API:er (förhandsversion)**.
1. Välj **Specifika säkerhetsgrupper.**

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Bild som visar hur du tillåter att tjänstens huvudnamn får skrivskyddade Power BI api-behörigheter för administratörer":::

    > [!Caution]
    > När du tillåter att den säkerhetsgrupp som du har skapat (som har din hanterade identitet i Purview som medlem) använder skrivskyddade Power BI-administratörs-API:er, ger du den även åtkomst till metadata (t.ex. instrumentpanels- och rapportnamn, ägare, beskrivningar osv.) för alla dina Power BI-artefakter i den här klientorganisationen. När metadata har dragits till Azure Purview avgör Purviews behörigheter, inte Power BI behörigheter, vem som kan se dessa metadata.

    > [!Note]
    > Du kan ta bort säkerhetsgruppen från dina utvecklarinställningar, men de metadata som extraherades tidigare tas inte bort från Purview-kontot. Du kan ta bort den separat om du vill.

## <a name="register-your-power-bi-and-set-up-a-scan"></a>Registrera din Power BI och konfigurera en genomsökning

Nu när du har gett Purview-hanterad identitet behörighet att ansluta till admin-API:et för din Power BI-klient kan du konfigurera genomsökningen från Azure Purview Studio.

1. Välj Källor **i** det vänstra navigeringsfönstret.

1. Välj **Registrera**.

    Välj **Power BI** som datakälla.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="Bild som visar listan över datakällor som är tillgängliga att välja":::

3. Ge Power BI instansen ett eget namn.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Bild som Power BI ett eget namn för datakällan":::

    Namnet måste vara mellan 3 och 63 tecken långt och får endast innehålla bokstäver, siffror, understreck och bindestreck.  Blanksteg tillåts inte.

    Som standard hittar systemet den klientorganisation Power BI som finns i samma Azure-prenumeration.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="Power BI registrerad datakälla":::

    > [!Note]
    > För Power BI tillåts endast registrering och genomsökning av datakällor för en instans.


4. Ge genomsökningen ett namn. Välj sedan alternativet för att inkludera eller exkludera personliga arbetsytor. Observera att den enda autentiseringsmetod som stöds är **Hanterad identitet**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="Bild som visar Power BI installation av skanning":::

    > [!Note]
    > * Om du växlar konfigurationen av en genomsökning för att inkludera eller exkludera en personlig arbetsyta utlöses en fullständig genomsökning av PowerBI-källan
    > * Genomsökningsnamnet måste vara mellan 3 och 63 tecken långt och får endast innehålla bokstäver, siffror, understreck och bindestreck. Blanksteg tillåts inte.

5. Konfigurera en sökningsutlösare. Alternativen är **En gång**, Var **7:e** dag och **Var 30:e dag.**

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="Bild av genomsökningsutlösare":::

6. På **Granska ny genomsökning** väljer du **Spara och Kör för** att starta genomsökningen.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Spara och kör Power BI skärmbild":::

## <a name="next-steps"></a>Nästa steg

- [Bläddra i Azure Purview Data-katalogen](how-to-browse-catalog.md)
- [Sök i Azure Purview-Data Catalog](how-to-search-catalog.md)
