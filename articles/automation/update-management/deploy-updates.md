---
title: Så här skapar du uppdateringsdistributioner för Azure Automation Uppdateringshantering
description: Den här artikeln beskriver hur du schemalägger uppdateringsdistributioner och granskar deras status.
services: automation
ms.subservice: update-management
ms.date: 04/19/2021
ms.topic: conceptual
ms.openlocfilehash: e410e5de529bde122fe42d21b593a6fc483dcbc0
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726689"
---
# <a name="how-to-deploy-updates-and-review-results"></a>Så här distribuerar du uppdateringar och granskar resultat

Den här artikeln beskriver hur du schemalägger en uppdateringsdistribution och granskar processen när distributionen är klar. Du kan konfigurera en uppdateringsdistribution från en vald virtuell Azure-dator, från den valda Arc-aktiverade servern eller från Automation-kontot på alla konfigurerade datorer och servrar.

I varje scenario skapar du den distribution som du har valt dator eller server som mål, eller om du skapar en distribution från ditt Automation-konto kan du rikta in dig på en eller flera datorer. När du schemalägger en uppdateringsdistribution från en virtuell Azure-dator eller Arc-aktiverad server är stegen desamma som vid distribution från ditt Automation-konto, med följande undantag:

* Operativsystemet förvals automatiskt baserat på datorns operativsystem
* Måldatorn som ska uppdateras är inställd på måldatorn automatiskt
* När du konfigurerar schemat kan du ange **Uppdatera nu**, sker en gång eller använder ett återkommande schema.

> [!IMPORTANT]
> Genom att skapa en uppdateringsdistribution godkänner du villkoren i licensavtalet (EULA) som tillhandahålls av det företag som erbjuder uppdateringar för sitt operativsystem.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com)

## <a name="schedule-an-update-deployment"></a>Schemalägga en uppdateringsdistribution

När du schemalägger [](../shared-resources/schedules.md) en uppdateringsdistribution skapas en schemaresurs som är länkad till **runbooken Patch-MicrosoftOMSComputers** som hanterar uppdateringsdistributionen på måldatorn eller datorerna. Du måste schemalägga en distribution som följer ditt lanseringsschema och servicefönster för att installera uppdateringar. Du kan välja vilka uppdateringstyper som ska ingå i distributionen. Du kan till exempel inkludera kritiska uppdateringar eller säkerhetsuppdateringar och exkludera samlade uppdateringar.

>[!NOTE]
>Om du tar bort schemaresursen från Azure Portal eller använder PowerShell när du har skapat distributionen, bryter borttagningen den schemalagda uppdateringsdistributionen och visar ett fel när du försöker konfigurera om schemaresursen från portalen. Du kan bara ta bort schemaresursen genom att ta bort motsvarande distributionsschema.  

Utför följande steg för att schemalägga en ny uppdateringsdistribution. Beroende på vilken resurs som valts (dvs. Automation-konto, Arc-aktiverad server, virtuell Azure-dator) gäller stegen nedan för alla med mindre skillnader när du konfigurerar distributionsschemat.

1. I portalen schemalägger du en distribution för:

   * En eller flera datorer, navigera till **Automation-konton** och välj ditt Automation-konto med Uppdateringshantering aktiverat i listan.
   * För en virtuell Azure-dator går **du till Virtuella** datorer och väljer den virtuella datorn i listan.
   * För en Arc-aktiverad server går du **till Servrar Azure Arc** och väljer din server i listan.

2. Beroende på vilken resurs du har valt går du till Uppdateringshantering:

   * Om du har valt ditt Automation-konto går du **till Uppdateringshantering** under **Uppdateringshantering** och väljer sedan **Schemalägg uppdateringsdistribution.**
   * Om du har valt en virtuell Azure-dator går **du till Gäst +** värduppdateringar och väljer sedan Gå till **Uppdateringshantering**.
   * Om du har valt en Arc-aktiverad server **går du till Uppdateringshantering** och väljer sedan **Schemalägg uppdateringsdistribution.**

3. Under **Ny uppdateringsdistribution** anger du **ett** unikt namn för distributionen i fältet Namn.

4. Välj det operativsystem som uppdateringsdistributionen ska vara mål för.

    > [!NOTE]
    > Det här alternativet är inte tillgängligt om du har valt en Azure VM- eller Arc-aktiverad server. Operativsystemet identifieras automatiskt.

5. I området **Grupper att** uppdatera definierar du en fråga som kombinerar prenumeration, resursgrupper, platser och taggar för att skapa en dynamisk grupp med virtuella Azure-datorer som ska ingå i distributionen. Mer information finns i [Använda dynamiska grupper med Uppdateringshantering](configure-groups.md).

    > [!NOTE]
    > Det här alternativet är inte tillgängligt om du har valt en Azure VM- eller Arc-aktiverad server. Datorn är automatiskt mål för den schemalagda distributionen.

6. I området **Datorer att uppdatera** väljer du en sparad sökning, en importerad grupp eller väljer **Datorer** i listrutan och väljer enskilda datorer. Med det här alternativet kan du se om Log Analytics-agenten är redo för varje dator. Mer information om de olika metoderna för att skapa datorgrupper Azure Monitor loggar finns i [Datorgrupper i Azure Monitor loggar](../../azure-monitor/logs/computer-groups.md). Du kan inkludera upp till högst 1 000 datorer i en schemalagd uppdateringsdistribution.

    > [!NOTE]
    > Det här alternativet är inte tillgängligt om du har valt en Azure VM- eller Arc-aktiverad server. Datorn är automatiskt mål för den schemalagda distributionen.

7. Använd regionen **Uppdateringsklassificering** för att [ange uppdateringsklassificering](view-update-assessments.md#work-with-update-classifications) för produkter. Avmarkera alla uppdateringsklassificeringar som stöds men de som ska ingå i uppdateringsdistributionen för varje produkt.

   :::image type="content" source="./media/deploy-updates/update-classifications-example.png" alt-text="Exempel som visar val av specifika uppdateringsklassificeringar.":::

    Om distributionen endast är avsedd att tillämpa en vald uppsättning uppdateringar måste du avmarkera alla förvalda uppdateringsklassificeringarna när du konfigurerar alternativet **Inkludera/exkludera** uppdateringar enligt beskrivningen i nästa steg. Detta säkerställer att endast de uppdateringar som du har angett *att* inkludera i den här distributionen installeras på måldatorerna.

   >[!NOTE]
   > Distribution av uppdateringar efter uppdateringsklassificering fungerar inte på RTM-versioner av CentOS. Om du vill distribuera uppdateringar för CentOS korrekt väljer du alla klassificeringar för att se till att uppdateringarna tillämpas. Det finns för närvarande ingen metod som stöds för att aktivera intern tillgänglighet för klassificeringsdata i CentOS. Se följande för mer information om [uppdateringsklassificering.](overview.md#update-classifications)

8. Använd regionen **Inkludera/exkludera uppdateringar** för att lägga till eller exkludera valda uppdateringar från distributionen. På sidan **Inkludera/exkludera anger** du KB-artikel-ID-nummer som ska inkluderas eller exkluderas för Windows-uppdateringar. För Linux-distributioner som stöds anger du paketnamnet.

   :::image type="content" source="./media/deploy-updates/include-specific-updates-example.png" alt-text="Exempel som visar hur du inkluderar specifika uppdateringar.":::

   > [!IMPORTANT]
   > Kom ihåg att undantag åsidosätter inkludering. Om du till exempel definierar en undantagsregel `*` för , Uppdateringshantering alla korrigeringar eller paket från installationen. Undantagna korrigeringar visas fortfarande som saknade på datorerna. Om du inkluderar ett paket som har ett beroende paket som har undantagits för Linux-datorer Uppdateringshantering inte huvudpaketet.

   > [!NOTE]
   > Du kan inte ange uppdateringar som har ersatts för att inkluderas i uppdateringsdistributionen.

   Här är några exempelscenarier som hjälper dig att förstå hur du använder inkludering/exkludering och uppdateringsklassificering samtidigt i uppdateringsdistributioner:

   * Om du bara vill installera en specifik lista över uppdateringar bör du inte välja några **uppdateringsklassificeringar** och ange en lista över uppdateringar som ska tillämpas med hjälp av **alternativet** Inkludera.

   * Om du bara vill installera säkerhetsuppdateringar och kritiska uppdateringar, tillsammans med en eller flera valfria drivrutinsuppdateringar, bör du välja **Säkerhet** och **Kritisk** under **Uppdateringsklassificering.** För alternativet **Inkludera anger** du sedan drivrutinsuppdateringarna.

   * Om du bara vill installera säkerhetsuppdateringar och kritiska uppdateringar, men hoppa över en eller  flera uppdateringar för Python för att undvika att det äldre programmet bryts, bör du välja Säkerhet och Kritisk **under** **Uppdateringsklassificering.** För alternativet Exkludera **lägger du** sedan till python-paketen som ska hoppas över.

9. Välj **Schemainställningar.** Starttiden är som standard 30 minuter efter den aktuella tiden. Du kan ange starttiden till helst från 10 minuter i framtiden.

    > [!NOTE]
    > Det här alternativet är annorlunda om du har valt en Arc-aktiverad server. Du kan välja **Uppdatera nu** eller en starttid 20 minuter in i framtiden.

10. Använd Upprepning **för** att ange om distributionen inträffar en gång eller använder ett återkommande schema och välj sedan **OK.**

11. I **regionen Pre-scripts + Post-scripts (Förskript +** Efterskript) väljer du de skript som ska köras före och efter distributionen. Mer information finns i [Hantera förskript och efterskript.](pre-post-scripts.md)

12. Använd fältet **Underhållsfönstret (minuter)** för att ange hur lång tid som uppdateringar ska installeras. Tänk på följande när du anger en underhållsfönstret:

    * Underhållsfönster styr hur många uppdateringar som installeras.
    * Uppdateringshantering inte att installera nya uppdateringar om slutet av en underhållsfönstret närmar sig.
    * Uppdateringshantering avslutar inte pågående uppdateringar om underhållsfönstret överskrids. Eventuella återstående uppdateringar som ska installeras görs inte. Om detta sker konsekvent bör du omvärdera underhållsperiodens varaktighet.
    * Om underhållsfönstret överskrids i Windows beror det ofta på att en service pack tar lång tid att installera.

    > [!NOTE]
    > Om du vill undvika att uppdateringar tillämpas utanför en underhållsfönstret i Ubuntu konfigurerar du om `Unattended-Upgrade` paketet för att inaktivera automatiska uppdateringar. Information om hur du konfigurerar paketet finns i [avsnittet Automatiska uppdateringar i Ubuntu Server Guide](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

13. Använd fältet **Omstartsalternativ** för att ange hur omstarter ska hanteras under distributionen. Följande alternativ är tillgängliga: 
    * Starta om vid behov (standard)
    * Starta alltid om
    * Starta aldrig om
    * Starta bara om; Det här alternativet installerar inte uppdateringar

    > [!NOTE]
    > Registernycklarna som anges under [Registernycklar som används för att](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) hantera omstart kan orsaka en omstartshändelse om **omstartsalternativen** är inställda på **Starta aldrig om**.

14. När du är klar med att konfigurera distributionsschemat väljer du **Skapa**.

    ![Inställningsfönster för uppdateringsschema](./media/deploy-updates/manageupdates-schedule-win.png)

    > [!NOTE]
    > När du är klar med att konfigurera distributionsschemat för en vald Arc-aktiverad server väljer du **Granska + skapa.**

15. Du kommer tillbaka till statusinstrumentpanelen. Välj **Distributionsscheman** för att visa distributionsschemat som du har skapat. Högst 500 scheman visas. Om du har fler än 500 scheman och vill granska den fullständiga listan kan du läsa mer i [programuppdateringskonfigurationer – lista](/rest/api/automation/softwareupdateconfigurations/list) REST API metod. Ange API-version 2019-06-01 eller senare.

## <a name="schedule-an-update-deployment-programmatically"></a>Schemalägga en uppdateringsdistribution programmässigt

Information om hur du skapar en uppdateringsdistribution med REST API finns i [Programuppdateringskonfigurationer – Skapa](/rest/api/automation/softwareupdateconfigurations/create).

Du kan använda en exempel-runbook för att skapa en veckovis uppdateringsdistribution. Mer information om denna runbook finns i [Skapa en veckovis uppdateringsdistribution för en eller flera virtuella datorer i en resursgrupp.](https://github.com/azureautomation/create-a-weekly-update-deployment-for-one-or-more-vms-in-a-resource-group)

## <a name="check-deployment-status"></a>Kontrollera distributionsstatus

När den schemalagda distributionen startar kan du se dess status på **fliken Historik** under **Uppdateringshantering.** Statusen är **Pågår** när distributionen körs. När distributionen avslutas ändras statusen till **Lyckades.** Om det uppstår fel med en eller flera uppdateringar i distributionen är statusen **Misslyckad.**

## <a name="view-results-of-a-completed-update-deployment"></a>Visa resultat av en slutförd uppdateringsdistribution

När distributionen är klar kan du välja den för att se resultatet.

[![Instrumentpanel för uppdateringsdistributionsstatus för en specifik distribution](./media/deploy-updates/manageupdates-view-results.png)](./media/deploy-updates/manageupdates-view-results-expanded.png#lightbox)

Under **Uppdateringsresultat** visar en sammanfattning det totala antalet uppdateringar och distributionsresultat på de virtuella mål datorerna. Tabellen till höger visar en detaljerad analys av uppdateringarna och installationsresultaten för var och en.

Tillgängliga värden är:

* **Inget försök gjordes** – Uppdateringen installerades inte eftersom det inte fanns tillräckligt med tid, baserat på den definierade underhållsperiodens varaktighet.
* **Inte valt** – Uppdateringen har inte valts för distribution.
* **Lyckades –** Uppdateringen lyckades.
* **Misslyckades** – Uppdateringen misslyckades.

Välj **Alla loggar** om du vill se alla loggposter som distributionen har skapat.

Välj **Utdata** för att se jobbströmmen för den runbook som ansvarar för att hantera uppdateringsdistributionen på de virtuella mål datorerna.

Välj **Fel** om du vill se detaljerad information om fel som uppstått vid distributionen.

## <a name="next-steps"></a>Nästa steg

* Information om hur du skapar aviseringar som meddelar dig om uppdateringsdistributionsresultat finns i [Skapa aviseringar för Uppdateringshantering](configure-alerts.md).
* Information om hur du felsöker Uppdateringshantering allmänna fel finns [i Felsöka Uppdateringshantering problem.](../troubleshoot/update-management.md)
