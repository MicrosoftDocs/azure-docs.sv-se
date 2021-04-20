---
title: Skapa en instrumentpanel i Azure Portal
description: Den här artikeln beskriver hur du skapar och anpassar en instrumentpanel i Azure Portal.
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.topic: how-to
ms.date: 04/15/2021
ms.openlocfilehash: 0666a9f8ca9df2fa44a7eaa4045c9b5e9a724ff5
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726106"
---
# <a name="create-a-dashboard-in-the-azure-portal"></a>Skapa en instrumentpanel i Azure Portal

Instrumentpaneler är en fokuserad och ordnad vy över dina molnresurser i Azure Portal. Använd instrumentpaneler som en arbetsyta där du kan övervaka resurser och snabbt starta uppgifter för dagliga åtgärder. Du kan exempelvis skapa anpassade instrumentpaneler som baseras på projekt, uppgifter eller användarroller.

I Azure Portal finns en standardinstrumentpanel som utgångspunkt. Du kan redigera standardinstrumentpanelen och skapa och anpassa ytterligare instrumentpaneler.

> [!NOTE]
> Varje användare kan skapa upp till 100 privata instrumentpaneler. Om du [publicerar och delar instrumentpanelen](azure-portal-dashboard-share-access.md)implementeras den som en Azure-resurs i din prenumeration och räknas inte mot den här gränsen.

Den här artikeln beskriver hur du skapar en ny instrumentpanel och anpassar den. Information om hur du delar instrumentpaneler finns [i Dela Azure-instrumentpaneler med hjälp av rollbaserad åtkomstkontroll i Azure.](azure-portal-dashboard-share-access.md)

## <a name="create-a-new-dashboard"></a>Skapa en ny instrumentpanel

Det här exemplet visar hur du skapar en ny privat instrumentpanel med ett tilldelat namn. Alla instrumentpaneler är privata när de skapas, men du kan välja att publicera och dela din instrumentpanel med andra användare i din organisation om du vill.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. På Azure Portal väljer du **Instrumentpanel.** Standardvyn kanske redan är inställd på instrumentpanel.

    ![Skärmbild av Azure Portal med Instrumentpanel valt.](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. Välj **Ny instrumentpanel och** sedan Tom **instrumentpanel.**

    ![Skärmbild av alternativen för Ny instrumentpanel.](./media/azure-portal-dashboards/create-new-dashboard.png)

    Den här åtgärden öppnar **panelgalleriet,** där du kan välja paneler och ett tomt rutnät där du ordnar panelerna.

1. Välj texten **Min instrumentpanel** på instrumentpanelens etikett och ange ett namn som gör det enkelt att identifiera den anpassade instrumentpanelen.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-name.png" alt-text="Skärmbild av ett tomt rutnät med panelgalleriet.":::

1. Om du vill spara instrumentpanelen som den är **väljer du Anpassningen är** klar i sidhuvudet. Eller fortsätt till nästa avsnitt för att lägga till paneler och spara din instrumentpanel.

Instrumentpanelsvyn visar nu den nya instrumentpanelen. Välj pilen bredvid instrumentpanelens namn för att se instrumentpaneler som är tillgängliga för dig. Listan kan innehålla instrumentpaneler som andra användare har skapat och delat.

## <a name="edit-a-dashboard"></a>Redigera en instrumentpanel

Nu ska vi redigera instrumentpanelen för att lägga till, ändra storlek på och ordna paneler som representerar dina Azure-resurser.

### <a name="add-tiles-from-the-tile-gallery"></a>Lägga till paneler från panelgalleriet

Följ dessa steg om du vill lägga till paneler på en instrumentpanel:

1. Välj ![ ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **redigeringsikonen** Redigera från instrumentpanelens sidhuvud.

    ![Skärmbild av instrumentpanelen med alternativet Redigera framhävt.](./media/azure-portal-dashboards/dashboard-edit.png)

1. Bläddra i **panelgalleriet** eller använd sökfältet för att hitta en viss panel. Välj den panel som du vill lägga till på instrumentpanelen.

   :::image type="content" source="media/azure-portal-dashboards/dashboard-tile-gallery.png" alt-text="Skärmbild av panelgalleriet.":::

1. Välj **Lägg** till för att lägga till panelen på instrumentpanelen med en standardstorlek och plats. Eller dra panelen till rutnätet och placera den där du vill. Lägg till valfria paneler, men här är några idéer:

    - Lägg **till Alla** resurser för att se alla resurser som du redan har skapat.

    - Om du arbetar med mer än  en organisation lägger du till panelen Organisationsidentitet på instrumentpanelen för att tydligt visa vilken organisation resurserna tillhör.

1. Om du vill ändrar du storlek på panelen genom att dra och släppa panelens nedre högra hörn.

1. Spara ändringarna genom att välja **Spara** i sidhuvudet. Du kan också förhandsgranska ändringarna utan att spara genom att **välja Förhandsgranska** i sidhuvudet. På förhandsgranskningsskärmen kan  du välja Spara för att behålla  **ändringarna,** Ignorera för att ta bort dem eller Redigera för att gå tillbaka till redigeringsalternativen och göra ytterligare ändringar.

   :::image type="content" source="media/azure-portal-dashboards/dashboard-save.png" alt-text="Skärmbild av alternativen Förhandsgranska, Spara och Ignorera.":::

### <a name="pin-content-from-a-resource-page"></a>Fästa innehåll från en resurssida

Ett annat sätt att lägga till paneler på instrumentpanelen är direkt från en resurssida.

Många resurssidor innehåller en fästikon i kommandofältet. Om du väljer den här ikonen kan du fästa en panel som representerar källsidan på en befintlig instrumentpanel eller på en ny instrumentpanel som du skapar.

![Skärmbild av sidans kommandofält med fästikonen](./media/azure-portal-dashboards/dashboard-pin-blade.png)

I vissa fall kan en fästikon också visas av specifikt innehåll på en sida, vilket innebär att du kan fästa en panel för det specifika innehållet i stället för hela sidan.

### <a name="resize-or-rearrange-tiles"></a>Ändra storlek på eller ordna om paneler

Följ dessa steg om du vill ändra storleken på en panel eller ordna om panelerna på en instrumentpanel:

1. Välj ![ ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **redigeringsikonen** Redigera i sidhuvudet.

1. Välj snabbmenyn i det övre högra hörnet av en panel. Välj sedan en panelstorlek. Paneler som stöder alla storlekar innehåller också en "referens" i det nedre högra hörnet som gör att du kan dra panelen till den storlek du vill ha.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-tile-resize.png" alt-text="Skärmbild av instrumentpanelen med menyn panelstorlek öppen.":::

1. Välj en panel och dra den till en ny plats i rutnätet för att ordna din instrumentpanel.

### <a name="additional-tile-configuration"></a>Ytterligare panelkonfiguration

Vissa paneler kan kräva mer konfiguration för att visa den information du vill ha. Panelen måttdiagram **måste till exempel konfigureras** för att visa ett mått från Azure Monitor. Du kan också anpassa paneldata för att åsidosätta instrumentpanelens standardtidsinställningar.

Alla paneler som måste konfigureras visar en banderoll tills du anpassar panelen. För **måttdiagrammet är** banderollen **Redigera i Mått**. Anpassa panelen:

1. I sidhuvudet väljer du **Spara för** att avsluta redigeringsläget.

1. Välj banderollen och gör sedan den nödvändiga konfigurationen.

    ![Skärmbild av panel som kräver konfiguration](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Med en markdown-panel kan du visa anpassat statiskt innehåll på instrumentpanelen. Det kan vara grundläggande instruktioner, en bild, en uppsättning hyperlänkar eller till och med kontaktinformation. Mer information om hur du använder en markdown-panel finns i [Använda en markdown-panel på Azure-instrumentpaneler för att visa anpassat innehåll.](azure-portal-markdown-tile.md)

### <a name="customize-tile-data"></a>Anpassa paneldata

Data på instrumentpanelen visar automatiskt aktivitet under de senaste 24 timmarna. Följ dessa steg om du vill visa ett annat tidsintervall för bara den här panelen:

1. Välj **Anpassa paneldata** på snabbmenyn eller från ![ filterikonfiltret ](./media/azure-portal-dashboards/dashboard-filter.png) i det övre vänstra hörnet av panelen.

    ![Skärmbild av panelens snabbmeny.](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. Markera kryssrutan för att **åsidosätta instrumentpanelens tidsinställningar på panelnivå.**

    ![Skärmbild av dialogrutan för att konfigurera inställningar för paneltid.](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. Välj det tidsintervall som ska visas för den här panelen. Du kan välja mellan de senaste 30 minuterna och de senaste 30 dagarna eller definiera ett anpassat intervall.

1. Välj den tidskornighet som ska visas. Du kan visa allt från en minuts ökning till en månad.

1. Välj **Använd**.

## <a name="delete-a-tile"></a>Ta bort en panel

Om du vill ta bort en panel från en instrumentpanel gör du något av följande:

- Välj snabbmenyn i det övre högra hörnet av panelen och välj sedan Ta **bort från instrumentpanelen.**

- Välj ![ redigeringsikonen ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Redigera** för att gå till anpassningsläge. Hovra i det övre högra hörnet av panelen och välj sedan ikonen Ta ![ bort ikonen Ta bort för att ta bort panelen från ](./media/azure-portal-dashboards/dashboard-delete-icon.png) instrumentpanelen.

   ![Skärmbild som visar hur du tar bort panelen från instrumentpanelen.](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Klona en instrumentpanel

Följ dessa steg om du vill använda en befintlig instrumentpanel som mall för en ny instrumentpanel:

1. Kontrollera att instrumentpanelsvyn visar den instrumentpanel som du vill kopiera.

1. I sidhuvudet väljer du ![ klonikonen ](./media/azure-portal-dashboards/dashboard-clone.png) **Klona**.

1. En kopia av instrumentpanelen med namnet Klon av *instrumentpanelens namn öppnas* i redigeringsläge.  Använd föregående steg i den här artikeln för att byta namn på och anpassa instrumentpanelen.

## <a name="publish-and-share-a-dashboard"></a>Publicera och dela en instrumentpanel

När du skapar en instrumentpanel är den privat som standard, vilket innebär att du är den enda som kan se den. Om du vill göra instrumentpaneler tillgängliga för andra kan du publicera och dela dem. Mer information finns i Dela [Azure-instrumentpaneler med hjälp av rollbaserad åtkomstkontroll i Azure.](azure-portal-dashboard-share-access.md)

### <a name="open-a-shared-dashboard"></a>Öppna en delad instrumentpanel

Följ dessa steg om du vill hitta och öppna en delad instrumentpanel:

1. Välj pilen bredvid instrumentpanelens namn.

1. Välj från den visade listan över instrumentpaneler. Om den instrumentpanel som du vill öppna inte visas:

    1. välj **Bläddra bland alla instrumentpaneler.**

        ![Skärmbild av menyn för val av instrumentpanel](./media/azure-portal-dashboards/dashboard-browse.png)

    1. I fältet **Typ** väljer du Delade **instrumentpaneler.**

        ![Skärmbild av valmenyn för alla instrumentpaneler](./media/azure-portal-dashboards/dashboard-browse-all.png)

    1. Välj en eller flera prenumerationer. Du kan också ange text för att filtrera instrumentpaneler efter namn.

    1. Välj en instrumentpanel i listan över delade instrumentpaneler.

## <a name="delete-a-dashboard"></a>Ta bort en instrumentpanel

Följ dessa steg om du vill ta bort en privat eller delad instrumentpanel permanent:

1. Välj den instrumentpanel som du vill ta bort från listan bredvid namnet på instrumentpanelen.

1. Välj ![ ikonen Ta bort ](./media/azure-portal-dashboards/dashboard-delete-icon.png) **Ta** bort från sidhuvudet.

1. För en privat instrumentpanel väljer du **OK i** bekräftelsedialogrutan för att ta bort instrumentpanelen. För en delad instrumentpanel markerar du kryssrutan i bekräftelsedialogrutan för att bekräfta att den publicerade instrumentpanelen inte längre kan visas av andra. Välj sedan **OK**.

    ![Skärmbild av bekräftelse av borttagning.](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="recover-a-deleted-dashboard"></a>Återställa en borttagen instrumentpanel

Om du är i det globala Azure-molnet och tar bort en publicerad instrumentpanel i Azure Portal kan du återställa instrumentpanelen inom 14 dagar från borttagningen.  Mer information finns i [Återställa en borttagna instrumentpanel i Azure Portal](recover-shared-deleted-dashboard.md).

## <a name="next-steps"></a>Nästa steg

- [Dela Azure-instrumentpaneler med hjälp av rollbaserad åtkomstkontroll i Azure](azure-portal-dashboard-share-access.md)
- [Skapa Azure-instrumentpaneler programmässigt](azure-portal-dashboards-create-programmatically.md)
