---
title: Kom igång med Azure Lab Services
description: Den här artikeln beskriver hur du kommer igång med Azure Lab Services.
ms.topic: article
ms.date: 11/18/2020
ms.openlocfilehash: d260ace7d7819c3ca2db96d6c4984bf834e170da
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165050"
---
# <a name="get-started-with-lab-services"></a>Kom igång med labb tjänster 

Azure Lab Services ger studenter och lärare åtkomst till virtuella dator labb direkt från sina egna datorer.

Lärare måste veta hur man underhåller eleverna/föräldrarna att använda labb tjänster i sin instruktion genom en-till-en-student utfärdad maskin vara. Som ett resultat skulle eleverna kunna komma åt bransch standard program vara som krävs för sina program av studie genom Virtual Machines (VM). 

En virtuell dator är en virtuell miljö som fungerar som en virtuell dator. Virtuella datorer har sin egen processor, minne och lagring. Virtuella datorer ger en ersättning för en riktig dator och ger användare åtkomst till operativ system och program vara utan att behöva ha dem på sin egen enhet. Azure Lab Services tillhandahåller ett verktyg för studenter för att komma åt och navigera i virtuella datorer och för att personalen ska kunna hantera sina virtuella dator labb. 

Den här artikeln innehåller information för att träna personal om hur man kommer åt, hanterar och undervisar studenter/överordnad att använda Azure Lab Services.

## <a name="key-concepts"></a>Viktiga begrepp

### <a name="quota-hours"></a>Kvot timmar

Studenter kan komma åt sina virtuella datorer när som helst under den schemalagda perioden utan att påverka kvot timmarna. Kvot timmar anges för hela terminen och avgör hur många timmar en student kan använda sin virtuella dator utanför den regelbundet schemalagda klass tiden.

8 timmar per vecka, återställs till söndag – inte kumulativt.

Mer information finns i [Ange kvot](how-to-configure-student-usage.md#set-quotas-for-users).

### <a name="automatic-shut-down"></a>Automatisk avstängning

För att minska kostnaderna och spara studenters kvot timmar är automatiska avstängningar aktiverade för labbet. Automatisk avstängning stänger av virtuella datorer efter en tids inaktivitet (inga musen eller tangent bords inmatningar). Automatisk avstängning fungerar i två steg, först en student kommer att kopplas bort från den virtuella datorn efter en tids inaktivitet. Den virtuella datorn **körs** fortfarande i det här läget och eleverna kan ansluta. Efter en annan period av inaktivitet vid från koppling stängs den virtuella datorn av.

Automatisk avstängning är ett viktigt verktyg för kostnads besparingar, men de kommer att presentera en utmaning för studenter i avseende på att spara sitt arbete och återge stora projektfiler. Om dina studenter ofta är frånkopplade eller om de virtuella datorerna stängs av för snabbt, kan du kontakta common Table EXPRESSIONS-administratören. 

Mer information finns i [Konfigurera automatisk avstängning av virtuella datorer för ett labb konto](how-to-configure-lab-accounts.md).

### <a name="managing-virtual-machines"></a>Hantera virtuella datorer

Genom att hantera labbet kan lärare styra saker som labb kapacitet (antalet virtuella datorer som är tillgängliga för studenter) och manuellt starta, stoppa eller återställa virtuella datorer. lärare kan också ansluta till virtuella datorer för att uppleva student gränssnitt, komma åt filer och felsöka problem med program vara eller den virtuella datorn.

Det viktigaste att komma ihåg när du hanterar de virtuella datorerna är att när som helst en dator **körs**, kommer vi att debiteras kostnaderna, även om ingen är ansluten till den virtuella datorn.

## <a name="lab-dashboards"></a>Labb instrument paneler

### <a name="overview"></a>Översikt

Instrument paneler för labb i Azure Lab Services ger en ögonblicks bild av olika aspekter av ett visst labb, inklusive information om virtuella datorer, antal tilldelade och Otilldelade virtuella datorer, antal registrerade och avregistrerade användare och information om labb scheman. 

> [!NOTE]
> Även om de flesta administrativa aspekter av instrument panelen och [Azure Lab Services webbplats](https://labs.azure.com/) kommer att vara synliga för lärare, kan behörigheter som är specifika för din roll påverka möjligheten att ändra vissa kriterier i instrument panelen. Om du stöter på ett problem med din specifika labb konfiguration kan du kontakta din common Table EXPRESSIONS-administratör.

:::image type="content" source="./media/use-dashboard/dashboard.png" alt-text="Azure Lab Services Portal":::

### <a name="examine-a-dashboard"></a>Granska en instrument panel

1. Navigera och logga in på [Azure Lab Services webbplats](https://labs.azure.com/).
1. Välj ditt labb.
1. En **instrument panel** visas till vänster i fönstret. Klicka på **instrument panelen** så visas ett antal paneler på instrument panelen.
1. Under **kostnaderna & fakturerings** panelen finns det också paneler för mallar, pooler för virtuella datorer, användare och scheman, som gör att du kan ändra aspekter och Visa mer information om klass rums labbet.

    * Mall – beskriver datumet då mallen skapades och publicerades senast. 
    * Virtuell dator-pool – antal tilldelade och Otilldelade virtuella datorer.
    * Användare-antal registrerade användare och användare som har lagts till i labbet, men inte registrerats.
    * Scheman – visar kommande schemalagda händelser för labbet och en länk för att visa fler händelser.

Mer information finns i [använda instrument panel](use-dashboard.md).

### <a name="manually-starting-vms"></a>Starta virtuella datorer manuellt

1. På sidan **virtuell dator** kan du starta alla virtuella datorer i ett labb genom att klicka på knappen **starta alla** överst på sidan.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/start-all-vms-button.png" alt-text="Starta dina virtuella datorer":::
1. Du kan starta enskilda virtuella datorer genom att klicka på Växla läge. 

    Växlingen läses in med **Start** när den virtuella datorn startas och **körs** sedan när den virtuella datorn har startats.
1. Du kan också välja ett antal virtuella datorer med hjälp av kontrollerna till vänster om kolumnen **namn** . 

    När du har valt önskade virtuella datorer klickar du på knappen **Start** överst på skärmen.
1. När du har startat kan du klicka på knappen **stoppa alla** för att stoppa alla virtuella datorer.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png" alt-text="Stoppa dina virtuella datorer":::

### <a name="stopping-and-resetting-vms"></a>Stoppa och återställa virtuella datorer

* Du kan stoppa enskilda virtuella datorer genom att klicka på Växla läge.
* Du kan också stoppa flera virtuella datorer genom att använda kontrollerna och klicka på knappen "stoppa" överst på skärmen.

Om en student har problem med att ansluta till den virtuella datorn, eller om den virtuella datorn måste återställas av någon annan anledning, kan du använda funktionen reset.
1. Om du vill återställa en eller flera virtuella datorer markerar du dem med hjälp av kontrollerna och klickar sedan på knappen **Återställ** högst upp på sidan.
1. I popup-fönstret klickar du på **Återställ**.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png" alt-text="Återställa den virtuella datorn":::

    > [!NOTE]
    > Att aktivera en elev-VM påverkar inte kvoten för studenten. Kvoter för användare anger antalet Labb timmar som är tillgängliga för användaren utanför den schemalagda klass tiden.

### <a name="connect-to-vms"></a>Ansluta till virtuella datorer

Lärare kan ansluta till en elev-VM så länge den är påslagen och studenten inte är ansluten till den virtuella datorn. Genom att ansluta till den virtuella datorn kommer du att kunna komma åt lokala filer på den virtuella datorn och hjälpa studenter att felsöka problem.

1. För att ansluta till den virtuella student datorn, hovrar du över musen på den virtuella datorn i listan och klickar på knappen **Anslut** . 
1. Följ sedan komma igång-guiden för studenter för antingen Chromebooks, Mac eller PC

:::image type="content" source="./media/how-to-set-virtual-machine-passwords/connect-student-vm.png" alt-text="Knappen Anslut till virtuell dator för student":::

## <a name="manage-users-in-a-lab"></a>Hantera användare i ett labb

Lärare kan lägga till student användare i ett labb och övervaka sina Tim kvoter. Mer information om hur du lägger till användare via e-postadress eller genom att använda en kalkyl blads lista och registrera användare finns i [lägga till och hantera labb användare](how-to-configure-student-usage.md).

När du har bjudit in användare eller delat länken kan du övervaka vilka användare som har registrerats på sidan **användare** i kolumnen **status** . 

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda resurser som du har skapat i den här snabb starten tar du bort resurserna.

## <a name="next-steps"></a>Nästa steg

[Konfigurera ett labbkonto](tutorial-setup-lab-account.md)
