---
title: Så här skapar du uppdaterings distributioner för Azure Automation Uppdateringshantering
description: Den här artikeln beskriver hur du schemalägger uppdaterings distributioner och granskar deras status.
services: automation
ms.subservice: update-management
ms.date: 12/09/2020
ms.topic: conceptual
ms.openlocfilehash: ec0d6d618edad87b76861ea834adb2f29531e6f5
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929675"
---
# <a name="how-to-deploy-updates-and-review-results"></a>Så här distribuerar du uppdateringar och granskar resultat

Den här artikeln beskriver hur du schemalägger en uppdaterings distribution och granskar processen när distributionen är klar. Du kan konfigurera en uppdaterings distribution från en vald virtuell Azure-dator från den valda Arc-aktiverade servern eller från Automation-kontot för alla konfigurerade datorer och servrar.

Under varje scenario kan du skapa mål för den valda datorn eller servern, eller om du vill skapa en distribution från ditt Automation-konto, kan du ange en eller flera datorer. När du schemalägger en uppdaterings distribution från en virtuell Azure-dator eller en ARC-aktiverad server är stegen samma som att distribuera från ditt Automation-konto, med följande undantag:

* Operativ systemet väljs automatiskt baserat på datorns operativ system
* Mål datorn som ska uppdateras är inställd på själva målet automatiskt
* När du konfigurerar schemat kan du ange **Uppdatera nu**, inträffar en gång eller använda ett återkommande schema.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com)

## <a name="schedule-an-update-deployment"></a>Schemalägga en uppdateringsdistribution

Schemaläggning av en uppdaterings distribution skapar en [schema](../shared-resources/schedules.md) resurs som är länkad till **MicrosoftOMSComputers-** runbooken som hanterar uppdaterings distributionen på mål datorn eller-datorerna. Du måste schemalägga en distribution som följer ditt versions schema och service fönster för att installera uppdateringar. Du kan välja vilka uppdaterings typer som ska ingå i distributionen. Du kan till exempel inkludera kritiska uppdateringar eller säkerhets uppdateringar och exkludera samlade uppdateringar.

>[!NOTE]
>Om du tar bort schema resursen från Azure Portal eller använder PowerShell när du har skapat distributionen bryts den schemalagda uppdaterings distributionen och ett fel uppstår när du försöker konfigurera om schema resursen från portalen. Du kan bara ta bort schema resursen genom att ta bort motsvarande distributions schema.  

Utför följande steg för att schemalägga en ny uppdaterings distribution. Beroende på vilken resurs som valts (det vill säga Automation-konto, Arc-aktiverad server, virtuell Azure-dator) gäller stegen nedan för alla med mindre skillnader när du konfigurerar distributions schemat.

1. I portalen för att schemalägga en distribution för:

   * En eller flera datorer, navigera till **Automation-konton** och välj ditt Automation-konto med uppdateringshantering aktiverat i listan.
   * För en virtuell Azure-dator går du till **virtuella datorer** och väljer den virtuella datorn i listan.
   * För en ARC-aktiverad server navigerar du till **servrar – Azure-båge** och väljer din server i listan.

2. Beroende på den resurs du har valt, för att navigera till Uppdateringshantering:

   * Om du har valt ditt Automation-konto går du till **uppdaterings hantering** under **uppdaterings hantering** och väljer sedan **Schemalägg uppdaterings distribution**.
   * Om du har valt en virtuell Azure-dator går du till **gäst + värd uppdateringar** och väljer sedan **gå till uppdateringshantering**.
   * Om du har valt en ARC-aktiverad server går du till **uppdateringshantering** och väljer sedan **Schemalägg uppdaterings distribution**.

3. Under **ny uppdaterings distribution** anger du ett unikt namn för distributionen i fältet **namn** .

4. Välj det operativ system som ska vara mål för uppdaterings distributionen.

    > [!NOTE]
    > Det här alternativet är inte tillgängligt om du har valt en virtuell Azure-dator eller en ARC-aktiverad server. Operativ systemet identifieras automatiskt.

5. I gruppen **grupper att uppdatera** definierar du en fråga som kombinerar prenumeration, resurs grupper, platser och taggar för att skapa en dynamisk grupp med virtuella Azure-datorer som ska ingå i distributionen. Läs mer i [använda dynamiska grupper med uppdateringshantering](configure-groups.md).

    > [!NOTE]
    > Det här alternativet är inte tillgängligt om du har valt en virtuell Azure-dator eller en ARC-aktiverad server. Datorn är automatiskt avsedd för den schemalagda distributionen.

6. I området **datorer att uppdatera** väljer du en sparad sökning, en importerad grupp eller väljer **datorer** från List menyn och väljer enskilda datorer. Med det här alternativet kan du se beredskap för Log Analytics agenten för varje dator. Om du vill veta mer om olika metoder för att skapa dator grupper i Azure Monitor loggar, se [dator grupper i Azure Monitor loggar](../../azure-monitor/platform/computer-groups.md). Du kan inkludera upp till högst 1000 datorer i en schemalagd uppdaterings distribution.

    > [!NOTE]
    > Det här alternativet är inte tillgängligt om du har valt en virtuell Azure-dator eller en ARC-aktiverad server. Datorn är automatiskt avsedd för den schemalagda distributionen.

7. Använd området **uppdaterings klassificeringar** för att ange [uppdaterings klassificeringar](view-update-assessments.md#work-with-update-classifications) för produkter. För varje produkt avmarkerar du alla uppdaterings klassificeringar som stöds, men de som ska inkluderas i uppdaterings distributionen.

    Om din distribution endast är avsedd att tillämpa en urvals uppsättning uppdateringar, är det nödvändigt att avmarkera alla förvalda uppdaterings klassificeringar när du konfigurerar alternativet **Inkludera/exkludera uppdateringar** enligt beskrivningen i nästa steg. Detta säkerställer att endast de uppdateringar som du har angett ska *inkluderas* i den här distributionen installeras på mål datorerna.

8. Använd området **Inkludera/exkludera uppdateringar** om du vill lägga till eller undanta valda uppdateringar från distributionen. På sidan **Inkludera/exkludera** anger du KB-artikel-ID-nummer som ska tas med eller undantas för Windows-uppdateringar. För Linux-distributioner som stöds anger du paket namnet.

   > [!IMPORTANT]
   > Kom ihåg att undantag åsidosätter inkluderingar. Om du till exempel definierar en undantags regel för `*` uppdateringshantering undantas alla korrigeringar eller paket från installationen. Undantagna uppdateringar visas fortfarande som saknade från datorerna. För Linux-datorer, om du inkluderar ett paket som har ett beroende paket som har uteslutits, installerar Uppdateringshantering inte huvud paketet.

   > [!NOTE]
   > Du kan inte ange uppdateringar som har ersatts av med i uppdaterings distributionen.

9. Välj **schema inställningar**. Starttiden är som standard 30 minuter efter den aktuella tiden. Du kan ange starttiden till helst från 10 minuter i framtiden.

    > [!NOTE]
    > Det här alternativet skiljer sig om du har valt en ARC-aktiverad server. Du kan välja **Uppdatera nu** eller en start tid på 20 minuter i framtiden.

10. Använd **upprepningen** för att ange om distributionen ska ske en gång eller använder ett återkommande schema och välj sedan **OK**.

11. I området **för skript + efter skript** väljer du de skript som ska köras före och efter distributionen. Läs mer i [Hantera för-skript och post-skript](pre-post-scripts.md).

12. Använd fältet **underhålls period (minuter)** för att ange hur lång tid som tillåts för uppdateringar som ska installeras. Tänk på följande när du anger en underhålls period:

    * Underhålls fönster styr hur många uppdateringar som är installerade.
    * Uppdateringshantering slutar inte att installera nya uppdateringar om slutet av en underhålls period närmar sig.
    * Uppdateringshantering avslutar inte pågående uppdateringar om underhålls perioden har överskridits. Eventuella återstående uppdateringar som ska installeras görs inte. Om detta sker konsekvent, bör du utvärdera tiden för underhålls perioden.
    * Om underhålls perioden överskrids i Windows beror det ofta på att en service pack uppdatering tar lång tid att installera.

    > [!NOTE]
    > Om du vill undvika att uppdateringar tillämpas utanför en underhålls period på Ubuntu konfigurerar du om `Unattended-Upgrade` paketet för att inaktivera automatiska uppdateringar. Information om hur du konfigurerar paketet finns i [avsnittet automatiska uppdateringar i Ubuntu Server guide](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

13. Använd fältet **alternativ för omstart** för att ange hur omstarter ska hanteras under distributionen. Följande alternativ är tillgängliga: 
    * Starta om vid behov (standard)
    * Starta alltid om
    * Starta aldrig om
    * Endast omstart; Det här alternativet installerar inte uppdateringar

    > [!NOTE]
    > De register nycklar som listas under [register nycklar som används för att hantera omstart](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) kan orsaka händelsen omstart om **alternativen för omstart** är inställd på **Starta aldrig om**.

14. När du är klar med att konfigurera distributions schemat väljer du **skapa**.

    ![Inställningsfönster för uppdateringsschema](./media/deploy-updates/manageupdates-schedule-win.png)

    > [!NOTE]
    > När du är klar med att konfigurera distributions schema för en vald Arc-aktiverad server väljer du **Granska + skapa**.

15. Du kommer tillbaka till statusinstrumentpanelen. Välj **distributions scheman** om du vill visa det distributions schema som du har skapat. Högst 500 scheman visas. Om du har fler än 500 scheman och du vill granska den fullständiga listan, se REST API metoden [program uppdaterings konfiguration – lista](/rest/api/automation/softwareupdateconfigurations/list) . Ange API-version 2019-06-01 eller högre.

## <a name="schedule-an-update-deployment-programmatically"></a>Schemalägga en uppdaterings distribution program mässigt

Information om hur du skapar en uppdaterings distribution med REST API finns i [program uppdaterings konfiguration – skapa](/rest/api/automation/softwareupdateconfigurations/create).

Du kan använda en exempel-Runbook för att skapa en veckovis uppdaterings distribution. Mer information om denna Runbook finns i [skapa en veckovis uppdaterings distribution för en eller flera virtuella datorer i en resurs grupp](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="check-deployment-status"></a>Kontrol lera distributions status

När den schemalagda distributionen har startats kan du se dess status på fliken **Historik** under **uppdaterings hantering**. Statusen är **Pågår** när distributionen körs. När distributionen har slutförts ändras statusen till **slutförd**. Om det uppstår fel med en eller flera uppdateringar i distributionen, **Miss** lyckas statusen.

## <a name="view-results-of-a-completed-update-deployment"></a>Visa resultatet av en slutförd uppdaterings distribution

När distributionen är färdig kan du välja den för att se resultatet.

[![Uppdatera distributions status instrument panel för en angiven distribution](./media/deploy-updates/manageupdates-view-results.png)](./media/deploy-updates/manageupdates-view-results-expanded.png#lightbox)

Under **uppdaterings resultat** innehåller en sammanfattning det totala antalet uppdateringar och distributions resultat på de virtuella mål datorerna. Tabellen till höger visar en detaljerad analys av uppdateringarna och installations resultaten för var och en.

De tillgängliga värdena är:

* **Inget försök har gjorts** – uppdateringen installerades inte eftersom det inte fanns tillräckligt med tid, baserat på den definierade varaktigheten för underhålls perioden.
* **Inte valt** – uppdateringen har inte valts för distribution.
* **Lyckades** – uppdateringen är klar.
* **Misslyckades** -uppdateringen misslyckades.

Välj **alla loggar** om du vill se alla logg poster som har skapats i distributionen.

Välj **utdata** om du vill se jobb strömmen för den Runbook som ansvarar för att hantera uppdaterings distributionen på de virtuella mål datorerna.

Välj **Fel** om du vill se detaljerad information om fel som uppstått vid distributionen.

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar aviseringar för att meddela dig om uppdaterings distributions resultat finns i [skapa aviseringar för uppdateringshantering](configure-alerts.md).