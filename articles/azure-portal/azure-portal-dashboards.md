---
title: Skapa en instrument panel i Azure Portal
description: Den här artikeln beskriver hur du skapar och anpassar en instrument panel i Azure Portal.
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: fa7f1813d86571b568d23d64cab5705f8a117faa
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104774657"
---
# <a name="create-a-dashboard-in-the-azure-portal"></a>Skapa en instrument panel i Azure Portal

Instrument paneler är en fokuserad och ordnad vy av moln resurserna i Azure Portal. Använd instrument paneler som en arbets yta där du kan övervaka resurser och snabbt starta aktiviteter för dagliga åtgärder. Du kan exempelvis skapa anpassade instrumentpaneler som baseras på projekt, uppgifter eller användarroller.

Azure Portal tillhandahåller en standard instrument panel som start punkt. Du kan redigera standard instrument panelen och skapa och anpassa ytterligare instrument paneler. Den här artikeln beskriver hur du skapar en ny instrument panel och anpassar den. Information om hur du delar instrument paneler finns i [dela Azure-instrumentpaneler med hjälp av rollbaserad åtkomst kontroll i Azure](azure-portal-dashboard-share-access.md).

## <a name="create-a-new-dashboard"></a>Skapa en ny instrumentpanel

I det här exemplet skapar vi en ny privat instrument panel och tilldelar ett namn. Kom igång genom att följa dessa steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **instrument panel** på Azure Portal-menyn. Din standardvy kanske redan är inställd på instrument panelen.

    ![Öppna instrument panelen](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. Välj **ny instrument panel** och sedan en **Tom instrument panel**.

    ![Skärm bild av ny instrument panel](./media/azure-portal-dashboards/create-new-dashboard.png)

    Den här åtgärden öppnar **panel galleriet** där du väljer paneler och ett tomt rutnät där du ordnar panelerna.

1. Välj **min instrument panels** text i instrument panelens etikett och ange ett namn som hjälper dig att enkelt identifiera den anpassade instrument panelen.

    ![Skärm bild av panel galleriet och tomt rutnät](./media/azure-portal-dashboards/dashboard-name.png)

1. I sidhuvudet väljer du **anpassning** för att avsluta redigerings läge och väljer sedan **Spara**.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-save.png" alt-text="Skärm bild av Spara-processen för instrument panelen":::

I vyn instrument panel visas nu din nya instrument panel. Välj pilen bredvid instrument panelens namn för att se vilka instrument paneler som är tillgängliga för dig. Listan kan innehålla instrument paneler som andra användare har skapat och delat.

## <a name="edit-a-dashboard"></a>Redigera en instrumentpanel

Nu ska vi redigera instrument panelen för att lägga till, ändra storlek på och arrangera paneler som representerar dina Azure-resurser.

### <a name="add-tiles-from-the-tile-gallery"></a>Lägga till paneler från panel galleriet

Följ dessa steg om du vill lägga till paneler i en instrument panel:

1. Välj ![ Redigera ikon ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Redigera** från sidhuvudet.

    ![Skärm bild av instrument panelens markerings redigering](./media/azure-portal-dashboards/dashboard-edit.png)

1. Bläddra i **panel galleriet** eller Använd Sök fältet för att hitta den panel som du vill använda.

1. Välj **Lägg** till för att lägga till panelen på instrument panelen med standard storlek och plats. Du kan också dra panelen till rutnätet och placera den där du vill. Lägg till de paneler som du vill ha, men här är några idéer:

    - Lägg till **alla resurser** för att se eventuella resurser som du redan har skapat.

    - Om du arbetar med mer än en organisation lägger du till panelen **organisations identitet** på din instrument panel för att tydligt visa vilken organisation resurserna tillhör.

1. I sidhuvudet väljer du **Spara**.

### <a name="add-tiles-from-a-resource-page"></a>Lägg till paneler från en resurs sida

Det finns ett alternativt sätt att lägga till paneler på instrument panelen. Många resurs sidor innehåller en ikon för kartnål i kommando fältet. Om du väljer ikonen fästs en panel som representerar käll sidan på den instrument panel som för närvarande är aktiv. 

![Skärm bild av sid kommando fältet med ikonen fäst](./media/azure-portal-dashboards/dashboard-pin-blade.png)

### <a name="resize-or-rearrange-tiles"></a>Ändra storlek på eller arrangera om paneler

Följ dessa steg om du vill ändra storleken på en panel eller arrangera om panelerna på en instrument panel:

1. Välj ![ Redigera ikon ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Redigera** från sidhuvudet.

1. Välj snabb menyn i det övre högra hörnet på en panel. Välj sedan en panel storlek. Paneler som har stöd för valfri storlek innehåller även ett "handtag" i det nedre högra hörnet som låter dig dra panelen till den storlek du önskar.

    ![Skärm bild av instrument panelen med panel storleks meny öppen](./media/azure-portal-dashboards/dashboard-tile-resize.png)

1. Välj en panel och dra den till en ny plats i rutnätet för att ordna instrument panelen.

### <a name="additional-tile-configuration"></a>Ytterligare panel konfiguration

Vissa paneler kan kräva mer konfiguration för att visa den information som du vill ha. **Mått diagram** panelen måste till exempel konfigureras för att visa ett mått från Azure Monitor. Du kan också anpassa panel data om du vill åsidosätta instrument panelens standard tids inställningar.

Alla paneler som måste ställas in visar en banderoll tills du anpassar panelen. I **mått diagrammet** är banderollen **redige ras i mått**. Så här anpassar du panelen:

1. I sidhuvudet väljer du **Spara** för att avsluta redigerings läget.

1. Välj banderollen och gör sedan den nödvändiga installationen.

    ![Skärm bild av panel som kräver konfiguration](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Med en markdown-panel kan du visa anpassat, statiskt innehåll på din instrument panel. Detta kan vara grundläggande instruktioner, en bild, en uppsättning hyperlänkar eller till och med kontakt information. Mer information om hur du använder en markdown-panel finns i [använda en markdown-panel på Azure-instrumentpaneler för att visa anpassat innehåll](azure-portal-markdown-tile.md).

### <a name="customize-tile-data"></a>Anpassa panel data

Data på instrument panelen visar automatiskt aktivitet under de senaste 24 timmarna. Följ dessa steg om du vill visa en annan tidsrymd för just den här panelen:

1. Välj **Anpassa panel data** från snabb menyn eller från ![ filtret filter ikon ](./media/azure-portal-dashboards/dashboard-filter.png) i det övre vänstra hörnet i panelen.

    ![Skärm bild av snabb menyn för panel](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. Markera kryss rutan om du vill **åsidosätta inställningarna för instrument panels tid på panel nivån**.

    ![Skärm bild av dialog rutan för att konfigurera inställningar för panel tid](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. Välj den tids period som ska visas för den här panelen. Du kan välja mellan de senaste 30 minuterna för de senaste 30 dagarna eller definiera ett anpassat intervall.

1. Välj tids kornig het som ska visas. Du kan visa var som helst från en minut till en månad.

1. Välj **Använd**.

## <a name="delete-a-tile"></a>Ta bort en panel

Följ dessa steg om du vill ta bort en panel från en instrument panel:

* Välj snabb menyn i det övre högra hörnet i panelen och välj **ta bort från instrument panelen**. Eller:

* Välj ![ Redigera Redigera ](./media/azure-portal-dashboards/dashboard-edit-icon.png)  ikon för att ange anpassnings läge. Hovra i det övre högra hörnet i panelen och välj ikonen Ta bort ![ ikon ](./media/azure-portal-dashboards/dashboard-delete-icon.png) ta bort för att ta bort panelen från instrument panelen.

   ![Skärm bild som visar hur du tar bort en panel från instrument panelen](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Klona en instrumentpanel

Följ dessa steg om du vill använda en befintlig instrument panel som mall för en ny instrument panel:

1. Se till att instrument panelens vy visar den instrument panel som du vill kopiera.

1. I sidhuvudet väljer du ![ klona ikon ](./media/azure-portal-dashboards/dashboard-clone.png) **kloning**.

1. En kopia av instrument panelen, som heter **klon av** *ditt instrument panels namn* , öppnas i redigerings läge. Använd föregående steg i den här artikeln för att byta namn på och anpassa instrument panelen.

## <a name="publish-and-share-a-dashboard"></a>Publicera och dela en instrument panel

När du skapar en instrument panel är den privat som standard, vilket innebär att du är den enda som kan se den. Om du vill göra instrument paneler tillgängliga för andra kan du publicera och dela dem. Mer information finns i [dela Azure-instrumentpaneler med hjälp av rollbaserad åtkomst kontroll i Azure](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Öppna en delad instrument panel

Följ dessa steg om du vill söka efter och öppna en delad instrument panel:

1. Välj pilen bredvid instrument panelens namn.

1. Välj i listan över visade instrument paneler. Om den instrument panel som du vill öppna inte visas i listan:

    1. Välj **Bläddra alla instrument paneler**.

        ![Skärm bild av menyn Välj instrument panel](./media/azure-portal-dashboards/dashboard-browse.png)

    1. I fältet **typ** väljer du **delade instrument paneler**.

        ![Skärm bild av menyn alla val av instrument paneler](./media/azure-portal-dashboards/dashboard-browse-all.png)

    1. Välj en eller flera prenumerationer. Du kan också ange text för att filtrera instrument paneler efter namn.

    1. Välj en instrument panel i listan med delade instrument paneler.

## <a name="delete-a-dashboard"></a>Ta bort en instrumentpanel

Följ dessa steg om du vill ta bort en privat eller delad instrument panel permanent:

1. Välj den instrument panel som du vill ta bort från listan bredvid instrument panelens namn.

1. Välj ![ ta bort ikon ](./media/azure-portal-dashboards/dashboard-delete-icon.png) **ta** bort från sidhuvudet.

1. För en privat instrument panel väljer du **OK** i bekräftelse dialog rutan för att ta bort instrument panelen. För en delad instrument panel, i bekräftelse dialog rutan, markerar du kryss rutan för att bekräfta att den publicerade instrument panelen inte längre kan visas av andra. Välj sedan **OK**.

    ![Skärm bild av borttagnings bekräftelse](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="recover-a-deleted-dashboard"></a>Återställa en borttagen instrumentpanel

Om du är i det globala Azure-molnet och du tar bort en _publicerad_ instrument panel i Azure Portal kan du återställa instrument panelen inom 14 dagar från borttagningen. Mer information finns i [återställa en borttagen instrument panel i Azure Portal](recover-shared-deleted-dashboard.md).

## <a name="next-steps"></a>Nästa steg

* [Dela Azure-instrumentpaneler med hjälp av rollbaserad åtkomst kontroll i Azure](azure-portal-dashboard-share-access.md)
* [Skapa Azure-instrumentpaneler programmässigt](azure-portal-dashboards-create-programmatically.md)
