---
title: Hantera Azure Recovery Services-valv och-servrar
description: I den här artikeln lär du dig hur du använder översikts instrument panelen för Recovery Services valv för att övervaka och hantera Recovery Services-valv.
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 68c6354fa15ee2a6873b57e5c1622afb108b9a10
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263338"
---
# <a name="monitor-and-manage-recovery-services-vaults"></a>Övervaka och hantera Recovery Services-valv

I den här artikeln förklaras hur du använder **översikts** instrument panelen för Recovery Services valv för att övervaka och hantera dina Recovery Services-valv. När du öppnar ett Recovery Services valv i listan öppnas **översikts** instrument panelen för det valda valvet. Instrument panelen innehåller olika detaljer om valvet. Det finns *paneler* som visar: status för kritiska varningar och varnings aviseringar, pågående och misslyckade säkerhets kopierings jobb samt mängden lokalt redundant lagring (LRS) och Geo-redundant lagring (GRS) som används. Om du säkerhetskopierar virtuella Azure-datorer till valvet [visas kritiska eller varnings objekt i status panelen för **säkerhets kopiering** ](#backup-pre-check-status). Följande bild är **översikts** instrument panelen för **contoso-valvet**. På panelen **säkerhets kopierings objekt** visas nio objekt som registrerats för valvet.

![instrument panel för Recovery Services-valv](./media/backup-azure-manage-windows-server/rs-vault-blade.png)

Kraven för den här artikeln är: en Azure-prenumeration, ett Recovery Services-valv och att minst ett säkerhets kopierings objekt har kon figurer ATS för valvet.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="open-a-recovery-services-vault"></a>Öppna ett Recovery Services-valv

Öppna valvet om du vill övervaka aviseringar eller Visa hanterings data om ett Recovery Services valv.

1. Logga in på [Azure-portalen](https://portal.azure.com/) med din Azure-prenumeration.

2. Välj **Alla tjänster** i portalen.

   ![Öppna listan över Recovery Services valv steg 1](./media/backup-azure-manage-windows-server/open-rs-vault-list.png)

3. I dialog rutan **alla tjänster** skriver du **Recovery Services**. När du börjar skriva filtreras listan baserat på det du skriver. När alternativet **Recovery Services valv** visas väljer du det för att öppna listan över Recovery Services valv i din prenumeration.

    ![Skapa Recovery Services-valv (steg 1)](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. Välj ett valv i listan över valv för att öppna dess **översikts** instrument panel.

    ![instrument panel för Recovery Services-valv](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    Översikts instrument panelen använder paneler för att tillhandahålla aviseringar och säkerhets kopierings jobb data.

## <a name="monitor-backup-jobs-and-alerts"></a>Övervaka säkerhets kopierings jobb och aviseringar

**Översikts** instrument panelen för Recovery Services Vault innehåller paneler för övervakning och användnings information. Panelerna i övervaknings avsnittet visar kritiska varningar och varnings aviseringar, samt pågående och misslyckade jobb. Välj en viss avisering eller ett visst jobb för att öppna menyn säkerhets kopierings varningar eller säkerhets kopierings jobb, filtrerat för det jobbet eller aviseringen.

![Säkerhetskopiera instrument panels aktiviteter](./media/backup-azure-manage-windows-server/monitor-dashboard-tiles-warning.png)

I avsnittet övervakning visas resultaten av fördefinierade **säkerhets aviseringar** och **säkerhets kopierings jobb** frågor. Övervaknings panelerna innehåller uppdaterad information om:

* Kritiska och varnings aviseringar för säkerhets kopierings jobb (under de senaste 24 timmarna)
* Status före kontroll för virtuella Azure-datorer. Fullständig information om för kontroll status finns i [säkerhets kopierings kontrollens status](#backup-pre-check-status).
* De säkerhets kopierings jobb som pågår och jobb som har misslyckats (under de senaste 24 timmarna).

Användnings panelerna tillhandahåller:

* Antalet säkerhets kopierings objekt som har kon figurer ATS för valvet.
* Azure Storage (avgränsat med LRS och GRS) används av valvet.

Välj panelerna (förutom lagring av säkerhets kopior) för att öppna den associerade menyn. I bilden ovan visar panelen Aviseringar för säkerhets kopiering tre viktiga aviseringar. Om du väljer raden kritiska aviseringar i panelen säkerhets kopierings aviseringar öppnas de säkerhets kopierings varningar som filtrerats för kritiska aviseringar.

![Menyn säkerhetskopiera aviseringar filtrerade för kritiska varningar](./media/backup-azure-manage-windows-server/critical-backup-alerts.png)

Menyn för säkerhets kopierings aviseringar i bilden ovan filtreras efter: status är aktiv, allvarlighets grad är kritisk och tiden är de föregående 24 timmarna.

### <a name="backup-pre-check-status"></a>Status för säkerhets kopierings kontroll

Säkerhets kopierings kontroller kontrol lera den virtuella datorns konfiguration för problem som kan påverka säkerhets kopieringar. De sammanställer den här informationen så att du kan visa den direkt från instrument panelen för Recovery Services valv och ge rekommendationer för korrigerande åtgärder för att säkerställa en lyckad filkonsekvent eller programkonsekvent säkerhets kopiering. De behöver ingen infrastruktur och har ingen ytterligare kostnad.  

Säkerhetskopiera innan kontroller körs som en del av de schemalagda säkerhets kopierings åtgärderna för dina virtuella Azure-datorer. De ingår i något av följande tillstånd:

* **Överfört**: det här läget anger att konfigurationen av den virtuella datorn ska leda till lyckade säkerhets kopieringar och att ingen korrigerings åtgärd behöver vidtas.
* **Varning**! det här läget anger ett eller flera problem i den virtuella datorns konfiguration som *kan* leda till säkerhets kopierings fel. Det innehåller *rekommenderade* steg för att säkerställa att säkerhets kopieringen har slutförts. Om du till exempel inte har installerat den senaste virtuella dator agenten kan säkerhets kopieringen sluta fungera. Den här situationen skulle ge ett varnings tillstånd.
* **Kritiskt**: det här läget anger ett eller flera kritiska problem i den virtuella datorns *konfiguration som leder* till säkerhets kopierings fel och som innehåller de steg som *krävs* för att säkerställa att säkerhets kopieringen är klar. Ett nätverks problem som orsakas av en uppdatering av NSG-reglerna för en virtuell dator innebär till exempel att säkerhets kopieringen Miss lyckas eftersom den förhindrar att den virtuella datorn kommunicerar med Azure Backup tjänsten. Den här situationen skulle ge ett kritiskt tillstånd.

Följ stegen nedan för att börja lösa eventuella problem som rapporter ATS av säkerhets kopierings kontroller för säkerhets kopior av virtuella datorer på Recovery Services valvet.

* Välj panelen för att **kontrol lera status för säkerhets kopiering (virtuella Azure-datorer)** på instrument panelen i Recovery Services Vault.
* Välj en virtuell dator med en säkerhets kopierings status, antingen **kritisk** eller **Varning**. Den här åtgärden öppnar fönstret **information om virtuell dator** .
* Välj meddelande fönstret överst i fönstret för att visa beskrivningen av konfigurations problemet och stegen.

## <a name="manage-backup-alerts"></a>Hantera säkerhets kopierings aviseringar

Öppna menyn för säkerhets kopierings aviseringar genom att välja **säkerhets kopierings aviseringar**på menyn Recovery Services valv.

![Säkerhetskopieringsaviseringar](./media/backup-azure-manage-windows-server/backup-alerts-menu.png)

I rapporten säkerhets kopierings aviseringar visas aviseringarna för valvet.

![Rapport över säkerhets kopierings aviseringar](./media/backup-azure-manage-windows-server/backup-alerts.png)

### <a name="alerts"></a>Aviseringar

Listan med aviseringar om säkerhets kopiering visar den valda informationen för de filtrerade aviseringarna. I menyn säkerhets kopierings aviseringar kan du filtrera efter kritiska eller varnings aviseringar.

| Aviseringsnivå | Händelser som genererar aviseringar |
| ----------- | ----------- |
| Kritiskt | Du får viktiga aviseringar när: säkerhets kopierings jobben inte fungerar, om återställnings jobben inte fungerar och när du slutar skydda en server, men behåller data.|
| Varning | Du får varnings aviseringar när: säkerhets kopierings jobben har slutförts med varningar, till exempel när färre än 100 filer inte säkerhets kopie ras på grund av problem med skada, eller när fler än 1 000 000 filer har säkerhetskopierats). |
| Information | för närvarande används inga informations aviseringar. |

### <a name="viewing-alert-details"></a>Visa aviseringsinformation

Rapporten aviseringar om säkerhets kopiering spårar åtta Detaljer om varje avisering. Använd knappen **Välj kolumner** för att redigera informationen i rapporten.

![Knappen för säkerhets kopierings aviseringar Välj kolumner](./media/backup-azure-manage-windows-server/backup-alerts.png)

Som standard visas all information, förutom den **senaste förekomst tiden**, i rapporten.

* Varning
* Säkerhetskopieringsobjekt
* Skyddad Server
* Allvarlighetsgrad
* Varaktighet
* Skapande tid
* Status
* Senaste förekomst tid

### <a name="change-the-details-in-alerts-report"></a>Ändra informationen i aviserings rapporten

1. Om du vill ändra rapport informationen väljer du **Välj kolumner**i menyn **säkerhets kopierings aviseringar** .

   ![Välj Välj kolumner](./media/backup-azure-manage-windows-server/alerts-menu-choose-columns.png)

   Menyn **Välj kolumner** öppnas.

2. I menyn **Välj kolumner** väljer du den information som du vill ska visas i rapporten.

    ![Menyn Välj kolumner](./media/backup-azure-manage-windows-server/choose-columns-menu.png)

3. Välj **gjorda** för att spara ändringarna och Stäng menyn Välj kolumner.

   Om du gör ändringar, men inte vill behålla ändringarna väljer du **Återställ** för att returnera den valda till den senast sparade konfigurationen.

### <a name="change-the-filter-in-alerts-report"></a>Ändra filtret i aviserings rapporten

Använd menyn **filter** om du vill ändra allvarlighets grad, status, start tid och slut tid för aviseringarna.

> [!NOTE]
> Om du redigerar filtret för säkerhets kopierings varningar ändras inte kritiska eller varnings aviseringar på instrument panelen för valv översikt.
>  

1. Om du vill ändra filtret för säkerhets kopierings aviseringar väljer du **filter**i menyn säkerhets kopierings aviseringar.

   ![Välj Filter meny](./media/backup-azure-manage-windows-server/alerts-menu-choose-filter.png)

   Menyn filter visas.

   ![Filter varnings meny](./media/backup-azure-manage-windows-server/filter-alert-menu.png)

2. Redigera allvarlighets grad, status, start tid eller slut tid och välj **klar** för att spara ändringarna.

## <a name="configuring-notifications-for-alerts"></a>Konfigurera aviseringar för aviseringar

Konfigurera meddelanden för att generera e-postmeddelanden när en varning eller kritisk varning inträffar. Du kan skicka e-postaviseringar varje timme eller när en viss avisering inträffar.

   ![Filtrera aviseringar](./media/backup-azure-manage-windows-server/configure-notification.png)

Som standard är e-postaviseringar **på**. Välj **av** för att stoppa e-postaviseringarna.

I **meddelande** kontrollen väljer du **per avisering** om du inte vill gruppera eller inte har många objekt som kan generera aviseringar. Varje avisering resulterar i ett meddelande (standardinställningen) och en lösnings-e-postadress skickas omedelbart.

Om du väljer **sammanställning per timme**skickas ett e-postmeddelande till mottagarna som förklarar de olösta aviseringarna som genererats under den senaste timmen. En lösnings-e-postadress skickas ut i slutet av timmen.

Välj aviseringens allvarlighets grad (kritisk eller varning) som används för att generera e-post. Det finns för närvarande inga informations aviseringar.

## <a name="manage-backup-items"></a>Hantera säkerhets kopierings objekt

Ett Recovery Services-valv innehåller många typer av säkerhets kopierings data. [Läs mer](backup-overview.md#what-can-i-back-up) om vad du kan säkerhetskopiera. Om du vill hantera de olika servrarna, datorerna, databaserna och arbets belastningarna väljer du panelen **säkerhets kopierings objekt** för att visa innehållet i valvet.

![Panelen säkerhets kopierings objekt](./media/backup-azure-manage-windows-server/backup-items.png)

Listan över säkerhets kopierings objekt, ordnade efter hanterings typ för säkerhets kopiering, öppnas.

![Lista över säkerhets kopierings objekt](./media/backup-azure-manage-windows-server/list-backup-items.png)

Om du vill utforska en speciell typ av skyddad instans markerar du objektet i kolumnen typ av säkerhets kopierings hantering. I bilden ovan finns det till exempel två virtuella Azure-datorer som skyddas i det här valvet. Om du väljer **virtuell Azure-dator**öppnas listan över skyddade virtuella datorer i det här valvet.

![Lista över skyddade virtuella datorer](./media/backup-azure-manage-windows-server/list-of-protected-virtual-machines.png)

Listan över virtuella datorer har användbara data: den associerade resurs gruppen, föregående [säkerhets kopierings kontroll](#backup-pre-check-status), status för senaste säkerhets kopiering och datumet för den senaste återställnings punkten. Ellipsen i den sista kolumnen öppnar menyn för att utlösa vanliga uppgifter. De användbara data som finns i kolumner är olika för varje säkerhets kopierings typ.

![Öppna ellips-menyn för vanliga uppgifter](./media/backup-azure-manage-windows-server/ellipsis-menu.png)

## <a name="manage-backup-jobs"></a>Hantera säkerhetskopieringsjobb

På panelen **säkerhets kopierings jobb** på instrument panelen för valv visas antalet pågående jobb eller misslyckade under de senaste 24 timmarna. Panelen ger en insyn i menyn säkerhets kopierings jobb.

![Panelen tillbaka jobb](./media/backup-azure-manage-windows-server/backup-jobs-tile.png)

Om du vill visa mer information om jobben väljer du **pågår** eller så **gick det inte** att öppna menyn säkerhets kopierings jobb filtrerat för detta tillstånd.

### <a name="backup-jobs-menu"></a>Menyn säkerhetskopiera jobb

Menyn **säkerhets kopierings jobb** visar information om objekt typ, operation, status, start tid och varaktighet.  

Öppna menyn säkerhets kopierings jobb i valvets huvud meny genom att välja **säkerhets kopierings jobb**.

![Välj säkerhets kopierings jobb](./media/backup-azure-manage-windows-server/backup-jobs-menu-item.png)

Listan över säkerhets kopierings jobb öppnas.

![Lista över säkerhets kopierings jobb](./media/backup-azure-manage-windows-server/backup-jobs-list.png)

Menyn säkerhets kopierings jobb visar status för alla åtgärder, på alla säkerhets kopierings typer, under de senaste 24 timmarna. Använd **filter** för att ändra filtren. Filtren förklaras i följande avsnitt.

Ändra filtren:

1. I menyn valv säkerhets kopierings jobb väljer du **filter**.

   ![Välj filter för säkerhets kopierings jobb](./media/backup-azure-manage-windows-server/vault-backup-job-menu-filter.png)

    Filter menyn öppnas.

   ![Filter menyn öppnas för säkerhets kopierings jobb](./media/backup-azure-manage-windows-server/filter-menu-backup-jobs.png)

2. Välj filter inställningarna och välj **Slutför**. Den filtrerade listan uppdateras utifrån de nya inställningarna.

#### <a name="item-type"></a>Objekt typ

Objekt typen är typen av säkerhets kopierings hantering för den skyddade instansen. Det finns fyra typer. Se följande lista. Du kan visa alla objekt typer eller en objekt typ. Det går inte att markera två eller tre objekt typer. De tillgängliga objekt typerna är:

* Alla objekt typer
* Virtuell Azure-dator
* Filer och mappar
* Azure Storage
* Azure-arbetsbelastning

#### <a name="operation"></a>Åtgärd

Du kan visa en åtgärd eller alla åtgärder. Det går inte att välja två eller tre åtgärder. Tillgängliga åtgärder är:

* Alla åtgärder
* Registrera dig
* Konfigurera säkerhetskopiering
* Backup
* Återställ
* Inaktivera säkerhets kopiering
* Ta bort säkerhetskopieringsdata

#### <a name="status"></a>Status

Du kan visa all status eller en. Du kan inte välja två eller tre status värden. Tillgängliga statusar är:

* Alla status
* Slutförd
* Pågår
* Misslyckad
* Avbrutna
* Slutfört med varningar

#### <a name="start-time"></a>Starttid

Den dag och tidpunkt då frågan börjar. Standardvärdet är en 24-timmarsperiod.

#### <a name="end-time"></a>Sluttid

Dagen och tiden då frågan slutar.

### <a name="export-jobs"></a>Exportera jobb

Använd **Exportera jobb** för att skapa ett kalkyl blad som innehåller alla jobb-meny information. Kalkyl bladet har ett blad som innehåller en sammanfattning av alla jobb och enskilda blad för varje jobb.

Om du vill exportera jobb informationen till ett kalkyl blad väljer du **Exportera jobb**. Tjänsten skapar ett kalkyl blad med namnet på valvet och datumet, men du kan ändra namnet.

## <a name="monitor-backup-usage"></a>Övervaka användning av säkerhets kopiering

Panelen lagring av säkerhets kopior på instrument panelen visar förbrukad lagring i Azure. Lagrings användningen tillhandahålls för:

* Moln LRS lagrings användning som är associerad med valvet
* Moln GRS lagrings användning som är associerad med valvet

## <a name="troubleshooting-monitoring-issues"></a>Felsöka övervaknings problem

**Problem:** Jobb och/eller varningar från Azure Backup Agent visas inte i portalen.

**Fel söknings steg:** Processen ```OBRecoveryServicesManagementAgent``` skickar jobbet och aviserings data till Azure Backup tjänsten. Ibland kan den här processen bli fastnad eller stängas av.

1. Kontrol lera att processen inte körs genom att öppna **aktivitets hanteraren**och ```OBRecoveryServicesManagementAgent``` köra kontrollen.

2. Om processen inte körs öppnar du **kontroll panelen**och bläddrar i listan över tjänster. Starta eller starta om **Microsoft Azure Recovery Services hanterings agenten**.

    Om du vill ha mer information bläddrar du till loggarna på:<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*` Exempel:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>Nästa steg

* [Återställa Windows Server eller Windows-klient från Azure](backup-azure-restore-windows-server.md)
* Mer information om Azure Backup finns i [Azure Backup översikt](./backup-overview.md)
