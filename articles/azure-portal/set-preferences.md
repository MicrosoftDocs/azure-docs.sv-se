---
title: Hantera dina inställningar i Microsoft Azure-portalen
description: Du kan ändra Azure Portal standardinställningarna så att de passar dina egna inställningar. Inställningarna omfattar tids gräns för inaktiv session, standardvy, Meny läge, kontrast, tema, meddelanden och språk och nationella format
keywords: inställningar, tids gräns, språk, regional
ms.date: 08/05/2020
ms.topic: how-to
ms.openlocfilehash: 5a108f1cd3b5af6655b592e7283192d4d54498ac
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99094118"
---
# <a name="manage-azure-portal-settings-and-preferences"></a>Hantera dina inställningar i Microsoft Azure-portalen

Du kan ändra standardinställningarna för Azure Portal så att de passar dina egna inställningar. De flesta inställningar är tillgängliga på menyn **Inställningar** i det globala sidhuvudet.

![Skärm bild som visar globala sidhuvuds ikoner med markerade inställningar](./media/set-preferences/header-settings.png)


## <a name="choose-your-default-subscription"></a>Välj din standard prenumeration

Du kan ändra den prenumeration som öppnas som standard när du loggar in på Azure Portal. Detta är användbart om du har en primär prenumeration som du arbetar med, men använder andra ibland. 

:::image type="content" source="media/set-preferences/filter-subscription-default-view.png" alt-text="Filtrera resurs lista efter prenumeration.":::

1. Välj ikonen katalog och prenumerations filter i den globala sidhuvudet.

1. Välj de prenumerationer som du vill använda som standard prenumerationer när du startar portalen. 

    :::image type="content" source="media/set-preferences/default-directory-subscription-filter.png" alt-text="Välj de prenumerationer som du vill använda som standard prenumerationer när du startar portalen."::: 


## <a name="choose-your-default-view"></a>Välj standardvy 

Du kan ändra sidan som öppnas som standard när du loggar in på Azure Portal.

![Skärm bild som visar Azure Portal inställningar med standard visning markerat](./media/set-preferences/default-view.png)

- Det går inte att anpassa **Start sidan** .  Den visar genvägar till populära Azure-tjänster och listar de resurser som du har använt senast. Vi ger dig också användbara länkar till resurser som Microsoft Learn och Azure-översikten.

- Instrument paneler kan anpassas för att skapa en arbets yta som har utformats precis för dig. Du kan till exempel bygga en instrument panel som är projekt, uppgift eller roll fokuserad. Om du väljer **instrument panel**, kommer din standardvy att gå till den senast använda instrument panelen. Mer information finns i [skapa och dela instrument paneler i Azure Portal](azure-portal-dashboards.md).

## <a name="choose-a-portal-menu-mode"></a>Välj ett meny läge för portalen

Standard läget för Portal-menyn styr hur mycket utrymme som Portal menyn tar upp på sidan.

![Skärm bild som visar hur du ställer in standard läget för Portal menyn.](./media/set-preferences/menu-mode.png)

- När Portal menyn är i **utfällt** läge är den dold tills du behöver den. Välj Meny ikonen för att öppna eller stänga menyn.

- Om du väljer **dockat läge** för Portal-menyn är det alltid synligt. Du kan komprimera menyn för att få mer arbets yta.

## <a name="choose-a-theme-or-enable-high-contrast"></a>Välj ett tema eller aktivera hög kontrast

Det tema som du väljer påverkar bakgrunds-och teckensnitts färgerna som visas i Azure Portal. Du kan välja någon av fyra förinställda färg teman. Välj varje miniatyr bild för att hitta det tema som passar dig bäst.

Alternativt kan du välja ett tema med hög kontrast. Med teman för hög kontrast blir Azure Portal lättare att läsa för personer som har visuellt nedskrivningar. de åsidosätter alla andra tema val.

![Skärm bild som visar Azure Portal inställningar med teman markerade](./media/set-preferences/theme.png)

## <a name="enable-or-disable-pop-up-notifications"></a>Aktivera eller inaktivera popup-meddelanden

Meddelanden är system meddelanden relaterade till din aktuella session. De tillhandahåller information som ditt aktuella kredit saldo när resurser som du just har skapat blir tillgängliga eller bekräfta din senaste åtgärd, till exempel. När popup-meddelanden är aktiverade visas meddelandena kortfattat i det övre hörnet på skärmen. 

Om du vill aktivera eller inaktivera popup-meddelanden väljer eller rensar du **Aktivera popup-meddelanden**.

![Skärm bild som visar Azure Portal inställningar med popup-meddelanden markerade](./media/set-preferences/popup-notifications.png)

Om du vill läsa alla meddelanden som tagits emot under den aktuella sessionen väljer du **meddelanden** från den globala rubriken.

![Skärm bild som visar Azure Portal global rubrik med meddelanden markerade](./media/set-preferences/read-notifications.png)

Om du vill läsa meddelanden från tidigare sessioner söker du efter händelser i aktivitets loggen. Mer information finns i [Visa aktivitets loggen](../azure-monitor/platform/activity-log.md#view-the-activity-log). 

## <a name="change-the-inactivity-timeout-setting"></a>Ändra inställningen för tids gräns för inaktivitet

Inställningen tids gräns för inaktivitet hjälper till att skydda resurser från obehörig åtkomst om du glömmer att skydda din arbets Station. När du har varit inaktiv under en stund loggas du in automatiskt från Azure Portal-sessionen. Som en individ kan du ändra timeout-inställningen för dig själv. Om du är administratör kan du ange den på katalog nivå för alla dina användare i katalogen.

### <a name="change-your-individual-timeout-setting-user"></a>Ändra din individuella timeout-inställning (användare)

Välj List rutan under **Logga ut mig när du är inaktiv**. Välj hur länge din Azure Portal-session ska loggas ut om du är inaktiv.

![Skärm bild som visar Portal inställningar med inaktiva timeout-inställningar markerade](./media/set-preferences/inactive-signout-user.png)

Ändringen sparas automatiskt. Om du är inaktiv kommer din Azure Portal-session att logga ut efter den varaktighet som du har angett.

Om din administratör har aktiverat en timeout-princip för inaktivitet kan du fortfarande ange en egen, så länge den är mindre än inställningen på katalog nivå. Välj **Åsidosätt timeout-principen för katalog inaktivitet** och ange sedan ett tidsintervall.

![Skärm bild som visar Portal inställningar som åsidosätter inställningen för timeout för katalog inaktivitet](./media/set-preferences/inactive-signout-override.png)

### <a name="change-the-directory-timeout-setting-admin"></a>Ändra timeout-inställningen för katalogen (admin)

Administratörer i [rollen global administratör](../active-directory/roles/permissions-reference.md#global-administrator) kan genomdriva den längsta inaktiva tiden innan en session loggas ut. Inställningen för tids gräns för inaktivitet gäller på katalog nivå. Inställningen börjar gälla för nya sessioner. Den gäller inte omedelbart för användare som redan är inloggade. Mer information om kataloger finns i [Active Directory Domain Services översikt](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

Om du är global administratör och vill framtvinga en tids gräns för inaktivitet för alla användare av Azure Portal följer du dessa steg:

1. Välj länk texten **Konfigurera timeout för katalog nivå**.

    ![Skärm bild som visar Portal inställningar med länk text markerad](./media/set-preferences/settings-admin.png)

1. På sidan **Konfigurera tids gräns för inaktivitet på katalog nivå** väljer du **Aktivera timeout för inaktiv katalog nivå för Azure Portal** aktivera inställningen.

1. Ange sedan **timmar** och **minuter** för den längsta tid som en användare kan vara inaktiv innan sessionen loggas ut automatiskt.

1. Välj **Använd**.

    ![Skärm bild som visar sidan för att ange tids gräns för inaktivitet på katalog nivå](./media/set-preferences/configure.png)

Om du vill bekräfta att tids gränsen för inaktivitet har angetts korrekt väljer du **meddelanden** från den globala sidhuvudet. Kontrol lera att en lyckad avisering visas.

![Skärm bild som visar ett meddelande om att aviseringar har slutförts på katalog nivå](./media/set-preferences/confirmation.png)

## <a name="restore-default-settings"></a>Återställ standardinställningarna

Om du har ändrat Azure Portal inställningarna och vill ta bort dem väljer du **Återställ standardinställningar**. Eventuella ändringar som du har gjort i Portal inställningarna går förlorade. Det här alternativet påverkar inte anpassningar av instrument paneler.

![Skärm bild som visar återställning av standardinställningar](./media/set-preferences/useful-links-restore-defaults.png)

## <a name="export-user-settings"></a>Exportera användar inställningar

Information om dina anpassade inställningar lagras i Azure. Du kan exportera följande användar data:

* Privata instrument paneler i Azure Portal
* Användar inställningar som favorit prenumerationer eller kataloger och senast inloggade katalog
* Teman och andra anpassade Portal inställningar

Det är en bra idé att exportera och granska dina inställningar om du planerar att ta bort dem. Det kan vara tids krävande att skapa instrument paneler eller göra om inställningar.

Om du vill exportera dina Portal inställningar väljer du **Exportera alla inställningar**.

![Skärm bild som visar export av inställningar](./media/set-preferences/useful-links-export-settings.png)

När du exporterar inställningar skapas en *. JSON* -fil som innehåller dina användar inställningar som färg tema, favoriter och privata instrument paneler. På grund av den dynamiska typen av användar inställningar och risk för skadade data kan du inte importera inställningar från *. JSON* -filen.

## <a name="delete-user-settings-and-dashboards"></a>Ta bort användar inställningar och instrument paneler

Information om dina anpassade inställningar lagras i Azure. Du kan ta bort följande användar data:

* Privata instrument paneler i Azure Portal
* Användar inställningar som favorit prenumerationer eller kataloger och senast inloggade katalog
* Teman och andra anpassade Portal inställningar

Det är en bra idé att exportera och granska dina inställningar innan du tar bort dem. Det kan vara tids krävande att skapa instrument paneler eller göra om anpassade inställningar.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

Om du vill ta bort dina Portal inställningar väljer du **ta bort alla inställningar och privata instrument paneler**.

![Skärm bild som visar borttagning av inställningar](./media/set-preferences/useful-links-delete-settings.png)

## <a name="change-language-and-regional-settings"></a>Ändra språk och nationella inställningar

Det finns två inställningar som styr hur texten i Azure Portal visas: 
- **Språk** inställningen styr språket som visas för text i Azure Portal. 

- **Regions formatet** styr hur datum, tid, siffror och valuta visas.

Om du vill ändra vilket språk som används i Azure Portal använder du List rutan för att välja i listan över tillgängliga språk.

Det regionala format valet ändras för att Visa regionala alternativ för endast det språk som du har valt. Om du vill ändra det automatiskt väljer du det regionala formatet du vill använda i list rutan.

Om du till exempel väljer engelska som språk och väljer USA som regions format, visas valutan i amerikanska dollar. Om du väljer engelska som språk och väljer sedan Europa som regions format visas valutan i euro.

Välj **tillämpa** för att uppdatera språk och nationella format inställningar.

   ![Skärm bild som visar inställningar för språk och nationella format](./media/set-preferences/language.png)

>[!NOTE]
>Dessa språk och nationella inställningar påverkar bara Azure Portal. Dokumentations länkar som öppnas i en ny flik eller i ett fönster använder webbläsarens språk inställningar för att avgöra vilket språk som ska visas.
>

## <a name="next-steps"></a>Nästa steg

- [Kortkommandon i Azure Portal](azure-portal-keyboard-shortcuts.md)
- [Webbläsare och enheter som stöds](azure-portal-supported-browsers-devices.md)
- [Lägg till, ta bort och ordna om favoriter](azure-portal-add-remove-sort-favorites.md)
- [Skapa och dela anpassade instrumentpaneler](azure-portal-dashboards.md)
- [Instruktionsvideoserie om Azure-portalen](azure-portal-video-series.md)
