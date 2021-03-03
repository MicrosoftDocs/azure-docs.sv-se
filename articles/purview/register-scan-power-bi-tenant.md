---
title: Registrera och skanna en Power BI klient (förhands granskning)
description: Lär dig hur du använder Azure avdelningens kontroll-portalen för att registrera och skanna en Power BI klient.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: 2ecc5df9db51bb6c923b9e0f47163e492bd76cfa
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695760"
---
# <a name="register-and-scan-a-power-bi-tenant-preview"></a>Registrera och skanna en Power BI klient (förhands granskning)

Den här artikeln visar hur du använder Azure avdelningens kontroll-portalen för att registrera och skanna en Power BI klient.

> [!Note]
> Om avdelningens kontroll-instansen och Power BI klienten finns i samma Azure-klient kan du bara använda autentisering med hanterad identitet (MSI) för att konfigurera en genomsökning av en Power BI klient. 

## <a name="create-a-security-group-for-permissions"></a>Skapa en säkerhets grupp för behörigheter

Skapa en säkerhets grupp och Lägg till den avdelningens kontroll-hanterade identiteten för att konfigurera autentisering.

1. Sök efter **Azure Active Directory** i [Azure Portal](https://portal.azure.com).
1. Skapa en ny säkerhets grupp i Azure Active Directory genom att följa [skapa en grundläggande grupp och lägga till medlemmar med Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    > [!Tip]
    > Du kan hoppa över det här steget om du redan har en säkerhets grupp som du vill använda.

1. Välj **säkerhet** som **grupptyp**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Typ av säkerhets grupp":::

1. Lägg till din avdelningens kontroll-hanterade identitet i den här säkerhets gruppen. Välj **medlemmar** och välj sedan **+ Lägg till medlemmar**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Lägg till katalogens hanterade instans i gruppen.":::

1. Sök efter din avdelningens kontroll-hanterade identitet och markera den.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Lägg till katalog genom att söka efter den":::

    Du bör se ett meddelande som visar att det har lagts till.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="Tillägget av katalog-MSI lyckades":::

## <a name="associate-the-security-group-with-the-tenant"></a>Associera säkerhets gruppen med klienten

1. Logga in på [Power BI admin-portalen](https://app.powerbi.com/admin-portal/tenantSettings).
1. Välj sidan **klient inställningar** .

    > [!Important]
    > Du måste vara Power BI administratör för att se sidan klient inställningar.

1. Välj **admin API**  >  **-Inställningar Tillåt att tjänstens huvud namn använder skrivskyddade Power BI administrations-API: er (för hands version)**.
1. Välj **vissa säkerhets grupper**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Bild som visar hur du tillåter tjänstens huvud namn för att få skrivskyddade Power BI administrations-API-behörigheter":::

    > [!Caution]
    > När du tillåter säkerhets gruppen som du skapade (som har din avdelningens kontroll-hanterade identitet som medlem) att använda skrivskyddade Power BI administrations-API: er kan du också få åtkomst till metadata (t. ex. instrument panel och rapport namn, ägare, beskrivningar osv.) för alla dina Power BI artefakter i den här klienten. När metadata har hämtats till Azure-avdelningens kontroll, avdelningens kontroll-behörigheter, inte Power BI behörigheter, avgör vem som kan se dessa metadata.

    > [!Note]
    > Du kan ta bort säkerhets gruppen från dina inställningar för utvecklare, men de metadata som tidigare extraheras tas inte bort från avdelningens kontroll-kontot. Du kan ta bort den separat, om du vill.

## <a name="register-your-power-bi-and-set-up-a-scan"></a>Registrera din Power BI och konfigurera en sökning

Nu när du har fått avdelningens kontroll-hanterade identitets behörigheter för att ansluta till administrations-API: t för din Power BI-klient kan du konfigurera din sökning från Azure avdelningens kontroll Studio.

Lägg först till en särskild funktions flagga till din avdelningens kontroll-URL 

1. Välj ikonen för **hanterings Center** .

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/management-center.png" alt-text="Ikonen för hanterings Center.":::

1. Välj sedan **+ ny** på **data källor**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/data-sources.png" alt-text="Bild av knappen ny data Källa":::

    Välj **Power BI** som data källa.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="Bild som visar en lista över data källor som kan väljas":::

3. Ge Power BI instansen ett eget namn.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Bild som visar Power BI data källa – eget namn":::

    Namnet måste vara mellan 3-63 tecken långt och får bara innehålla bokstäver, siffror, under streck och bindestreck.  Blank steg är inte tillåtna.

    Som standard hittar systemet Power BI klient som finns i samma Azure-prenumeration.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="Power BI data källa registrerad":::

    > [!Note]
    > För Power BI tillåts registrering och genomsökning av data källor bara för en instans.


4. Ge skanningen ett namn. Välj sedan alternativet för att inkludera eller exkludera de personliga arbets ytorna. Observera att den enda autentiseringsmetoden som stöds är **hanterad identitet**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="Bild som visar Power BI skannings konfiguration":::

    > [!Note]
    > * Om du växlar konfigurationen av en sökning för att ta med eller undanta en personlig arbets yta utlöses en fullständig genomsökning av PowerBI-källan
    > * Skannings namnet måste innehålla mellan 3-63 tecken och får bara innehålla bokstäver, siffror, under streck och bindestreck. Blank steg är inte tillåtna.

5. Konfigurera en genomsöknings utlösare. Dina alternativ är **en gång**, **var sjunde dag** och **var 30: e dag**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="Skanna utlösare avbildning":::

6. Vid **Granska ny genomsökning** väljer du **Spara och kör** för att starta din sökning.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Spara och kör Power BI skärm bild":::

## <a name="next-steps"></a>Nästa steg

- [Bläddra i Azure avdelningens kontroll Data Catalog](how-to-browse-catalog.md)
- [Sök i Azure avdelningens kontroll-Data Catalog](how-to-search-catalog.md)
