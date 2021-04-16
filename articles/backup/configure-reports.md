---
title: Konfigurera Azure Backup-rapporter
description: Konfigurera och visa rapporter för Azure Backup med hjälp av Log Analytics och Azure-arbetsböcker
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 0f3638e7649fc02f050c575ee621ce9dc237c24f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517274"
---
# <a name="configure-azure-backup-reports"></a>Konfigurera Azure Backup-rapporter

Ett vanligt krav för säkerhetskopieringsadministratörer är att få insikter om säkerhetskopior baserat på data som sträcker sig över en längre tidsperiod. Användningsfall för en sådan lösning är:

- Allokera och göra prognoser för förbrukad molnlagring.
- Granskning av säkerhetskopieringar och återställningar.
- Identifiera viktiga trender med olika kornighetsnivåer.

I dag Azure Backup en rapporteringslösning som använder [Azure Monitor-loggar](../azure-monitor/logs/log-analytics-tutorial.md) och [Azure-arbetsböcker.](../azure-monitor/visualize/workbooks-overview.md) De här resurserna hjälper dig att få omfattande insikter om dina säkerhetskopior i hela din säkerhetskopieringse egendom. Den här artikeln förklarar hur du konfigurerar och visar Azure Backup rapporter.

## <a name="supported-scenarios"></a>Scenarier som stöds

- Säkerhetskopieringsrapporter stöds för virtuella Azure-datorer, SQL i virtuella Azure-datorer, SAP HANA på virtuella Azure-datorer, Microsoft Azure Recovery Services-agenten (MARS), Microsoft Azure Backup Server (MABS) och System Center Data Protection Manager (DPM). För säkerhetskopiering av Azure-filresurs visas data för poster som skapats den 1 juni 2020 eller senare.
- För säkerhetskopiering av Azure-filresurs visas data på skyddade instanser för poster som skapats efter den 1 februari 2021 (standardvärdet är noll för äldre poster).
- För DPM-arbetsbelastningar stöds säkerhetskopieringsrapporter för DPM version 5.1.363.0 och senare och agentversion 2.0.9127.0 och senare.
- För MABS-arbetsbelastningar stöds säkerhetskopieringsrapporter för MABS version 13.0.415.0 och senare och agentversion 2.0.9170.0 och senare.
- Säkerhetskopieringsrapporter kan visas för alla säkerhetskopieringsobjekt, valv, prenumerationer och regioner så länge deras data skickas till en Log Analytics-arbetsyta som användaren har åtkomst till. Om du vill visa rapporter för en uppsättning valv behöver du bara ha läsåtkomst till Log Analytics-arbetsytan som valven skickar sina data till. Du behöver inte ha åtkomst till de enskilda valven.
- Om du är en [Azure Lighthouse-användare](../lighthouse/index.yml) med delegerad åtkomst till dina kunders prenumerationer kan du använda dessa rapporter med Azure Lighthouse för att visa rapporter över alla dina klienter.
- För närvarande kan data visas i Backup-rapporter över högst 100 Log Analytics-arbetsytor (mellan klienter).
- Data för loggsäkerhetskopieringsjobb visas för närvarande inte i rapporterna.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="get-started"></a>Kom igång

Följ de här stegen för att börja använda rapporterna.

### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. Skapa en Log Analytics-arbetsyta eller använd en befintlig

Konfigurera en eller flera Log Analytics-arbetsytor för att lagra dina rapporteringsdata för Säkerhetskopiering. Platsen och prenumerationen där Log Analytics-arbetsytan kan skapas är oberoende av den plats och prenumeration där dina valv finns.

Om du vill konfigurera en Log Analytics-arbetsyta [kan du gå till Skapa en Log Analytics-arbetsyta i Azure Portal](../azure-monitor/logs/quick-create-workspace.md).

Som standard bevaras data i en Log Analytics-arbetsyta i 30 dagar. Om du vill se data under en längre tidsperiod ändrar du kvarhållningsperioden för Log Analytics-arbetsytan. Information om hur du ändrar kvarhållningsperioden [finns i Hantera användning och kostnader med Azure Monitor loggar.](../azure-monitor/logs/manage-cost-storage.md)

### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. Konfigurera diagnostikinställningar för dina valv

Azure Resource Manager resurser, till exempel Recovery Services-valv, registrerar information om schemalagda åtgärder och användarutlösta åtgärder som diagnostikdata.

I övervakningsavsnittet i Recovery Services-valvet väljer du **Diagnostikinställningar** och anger målet för Recovery Services-valvets diagnostikdata. Mer information om hur du använder diagnostikhändelser finns i [Använda diagnostikinställningar för Recovery Services-valv.](./backup-azure-diagnostic-events.md)

![Fönstret Diagnostikinställningar](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure Backup också en inbyggd definition Azure Policy som automatiserar konfigurationen av diagnostikinställningar för alla valv i ett visst omfång. Information om hur du använder den här principen finns i [Konfigurera valvdiagnostikinställningar i stor skala.](./azure-policy-configure-diagnostics.md)

> [!NOTE]
> När du har konfigurerat diagnostiken kan det ta upp till 24 timmar innan den första data push-installationen är klar. När data börjar flöda till Log Analytics-arbetsytan kanske du inte ser data i rapporterna direkt eftersom data för den aktuella deldagen inte visas i rapporterna. Mer information finns i Konventioner [som används i Backup-rapporter.](#conventions-used-in-backup-reports) Vi rekommenderar att du börjar visa rapporterna två dagar efter att du har konfigurerat dina valv för att skicka data till Log Analytics.

#### <a name="3-view-reports-in-the-azure-portal"></a>3. Visa rapporter i Azure Portal

När du har konfigurerat dina valv så att de skickar data till Log Analytics kan du visa dina säkerhetskopieringsrapporter genom att gå till ett valvs fönster och **välja Backup-rapporter**.

![Instrumentpanel för valv](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Välj den här länken för att öppna arbetsboken för säkerhetskopieringsrapport.

> [!NOTE]
>
> - Den första inläsningen av rapporten kan för närvarande ta upp till 1 minut.
> - Recovery Services-valvet är bara en startpunkt för Säkerhetskopieringsrapporter. När arbetsboken för säkerhetskopieringsrapport öppnas från ett valvs fönster väljer du lämplig uppsättning Log Analytics-arbetsytor för att se data aggregerade över alla dina valv.

Rapporten innehåller olika flikar:

##### <a name="summary"></a>Sammanfattning

Använd den här fliken för att få en översikt på hög nivå över din säkerhetskopieringse egendom. Du kan få en snabb överblick över det totala antalet säkerhetskopieringsobjekt, förbrukad total molnlagring, antalet skyddade instanser och antalet lyckade jobb per arbetsbelastningstyp. Mer detaljerad information om en specifik typ av säkerhetskopieringsartefakt finns på respektive flik.

   ![Fliken Sammanfattning](./media/backup-azure-configure-backup-reports/summary.png)

##### <a name="backup-items"></a>Säkerhetskopieringsobjekt

Använd den här fliken om du vill se information och trender om molnlagring som förbrukats på nivån Säkerhetskopieringsobjekt. Om du till exempel använder SQL i en säkerhetskopiering av en virtuell Azure-dator kan du se molnlagringen som förbrukas för varje SQL-databas som säkerhetskopieras. Du kan också välja att visa data för säkerhetskopieringsobjekt med en viss skyddsstatus. Om du till exempel väljer **panelen** Skydd har stoppats överst på fliken filtreras alla widgetar under för att endast visa data för säkerhetskopieringsobjekt med tillståndet Skydd stoppad.

   ![Fliken Säkerhetskopieringsobjekt](./media/backup-azure-configure-backup-reports/backup-items.png)

##### <a name="usage"></a>Användning

Använd den här fliken för att visa viktiga faktureringsparametrar för dina säkerhetskopior. Informationen som visas på den här fliken finns på faktureringsentitetsnivå (skyddad container). Om en DPM-server till exempel säkerhetskopieras till Azure kan du se trenden för skyddade instanser och molnlagring som används för DPM-servern. Om du använder SQL i Azure Backup eller SAP HANA i Azure Backup ger den här fliken användningsrelaterad information på nivån för den virtuella dator där databaserna finns.

   ![Fliken Användning](./media/backup-azure-configure-backup-reports/usage.png)

> [!NOTE]
> För DPM-arbetsbelastningar kan användarna se en viss skillnad (i ordningen på 20 MB per DPM-server) mellan användningsvärdena som visas i rapporterna jämfört med det aggregerade användningsvärdet som visas på fliken Översikt för Recovery **Services-valvet.** Den här skillnaden förklaras av det faktum att varje DPM-server som registreras för säkerhetskopiering har en associerad "metadata"-datakälla som inte är en artefakt för rapportering.

##### <a name="jobs"></a>Jobb

Använd den här fliken för att visa långvariga trender för jobb, till exempel antalet misslyckade jobb per dag och de främsta orsakerna till jobbfel. Du kan visa den här informationen både på aggregeringsnivå och på nivån Säkerhetskopieringsobjekt. Välj ett visst säkerhetskopieringsobjekt i ett rutnät för att visa detaljerad information om varje jobb som utlöstes för säkerhetskopieringsobjektet i det valda tidsperioden.

   ![Fliken Jobb](./media/backup-azure-configure-backup-reports/jobs.png)

##### <a name="policies"></a>Principer

Använd den här fliken om du vill visa information om alla aktiva principer, till exempel antalet associerade objekt och den totala molnlagringen som förbrukas av objekt som säkerhetskopieras under en viss princip. Välj en viss princip om du vill visa information om var och en av dess associerade säkerhetskopieringsobjekt.

   ![Fliken Principer](./media/backup-azure-configure-backup-reports/policies.png)

##### <a name="optimize"></a>Optimera

Använd den här fliken för att få insyn i potentiella möjligheter till kostnadsoptimering för dina säkerhetskopior. Följande är de scenarier där fliken Optimera för närvarande ger insikter:

###### <a name="inactive-resources"></a>Inaktiva resurser

Med den här vyn kan du identifiera de säkerhetskopieringsobjekt som inte har haft en lyckad säkerhetskopiering under en längre tid. Detta kan antingen innebära att den underliggande datorn som säkerhetskopieras inte finns längre (vilket resulterar i misslyckade säkerhetskopieringar), eller att det finns ett problem med datorn som förhindrar att säkerhetskopior tas på ett tillförlitligt sätt.

Om du vill visa inaktiva resurser går du **till** fliken Optimera och väljer **panelen Inaktiva** resurser. Välj den här panelen visar ett rutnät som innehåller information om alla inaktiva resurser som finns i det valda omfånget. Som standard visar rutnätet objekt som inte har någon återställningspunkt under de senaste sju dagarna. Om du vill hitta inaktiva resurser för ett annat tidsperioder kan du justera **filtret** För tidsintervall högst upp på fliken.

När du har identifierat en inaktiv resurs kan du undersöka problemet ytterligare genom att gå till instrumentpanelen för säkerhetskopieringsobjekt eller Azure-resursfönstret för resursen (där det är tillämpligt). Beroende på ditt scenario kan du välja att antingen stoppa säkerhetskopieringen för datorn (om den inte finns längre) och ta bort onödiga säkerhetskopior, vilket sparar kostnader, eller så kan du åtgärda problem på datorn för att säkerställa att säkerhetskopiorna tas på ett tillförlitligt sätt.

![Fliken Optimera – Inaktiva resurser](./media/backup-azure-configure-backup-reports/optimize-inactive-resources.png)

###### <a name="backup-items-with-a-large-retention-duration"></a>Säkerhetskopieringsobjekt med lång kvarhållningsperiod

Med den här vyn kan du identifiera de objekt som har säkerhetskopior kvarhållna under en längre tid än vad som krävs av din organisation.

Om du väljer panelen **Principoptimeringar** följt av panelen Kvarhållningsoptimering visas ett rutnät som innehåller alla säkerhetskopieringsobjekt där kvarhållningen av antingen den dagliga, veckovisa, månatliga eller årliga **kvarhållningspunkten** (RP) är större än ett angivet värde. Som standard visar rutnätet alla säkerhetskopieringsobjekt i det valda omfånget. Du kan använda filtren för kvarhållning per dag, vecka, månad och år för att filtrera rutnätet ytterligare och identifiera de objekt för vilka kvarhållning kan minskas för att minska kostnaderna för lagring av säkerhetskopior.

För databasarbetsbelastningar som SQL och SAP HANA motsvarar kvarhållningsperioderna som visas i rutnätet kvarhållningsperioderna för de fullständiga säkerhetskopieringspunkterna och inte de differentiella säkerhetskopieringspunkterna. Samma sak gäller även för kvarhållningsfilter.  

![Fliken Optimera – Kvarhållningsoptimeringar](./media/backup-azure-configure-backup-reports/optimize-retention.png)

###### <a name="databases-configured-for-daily-full-backup"></a>Databaser som konfigurerats för fullständig säkerhetskopiering dagligen

Med den här vyn kan du identifiera databasarbetsbelastningar som har konfigurerats för daglig fullständig säkerhetskopiering. Det är ofta mer kostnadseffektivt att använda daglig differentiell säkerhetskopiering tillsammans med veckovis fullständig säkerhetskopiering.

Om du **väljer panelen Principoptimeringar** följt av panelen Optimering av schema för säkerhetskopiering visas ett rutnät som innehåller alla databaser med en princip för fullständig säkerhetskopiering varje dag.  Du kan välja att navigera till ett visst säkerhetskopieringsobjekt och ändra principen så att den använder daglig differentiell säkerhetskopiering med veckovis fullständig säkerhetskopiering.

Filtret **Typ av säkerhetskopieringshantering** längst upp på fliken bör ha objekten SQL på den virtuella **Azure-datorn** och SAP HANA i den virtuella **Azure-datorn** markerade för att rutnätet ska kunna visa databasarbetsbelastningar som förväntat.

![Fliken Optimera – Optimering av säkerhetskopieringsschema](./media/backup-azure-configure-backup-reports/optimize-backup-schedule.png)

###### <a name="policy-adherence"></a>Principeftersening

På den här fliken kan du identifiera om alla dina säkerhetskopieringsinstanser har haft minst en lyckad säkerhetskopiering varje dag. För objekt med veckovisa säkerhetskopieringspolicyer kan du använda den här fliken för att avgöra om alla säkerhetskopieringsinstanser har haft minst en lyckad säkerhetskopiering per vecka.

Det finns två typer av vyer för principeftersening:

* **Principefterseningen** efter tidsperiod: Med den här vyn kan du identifiera hur många objekt som har haft minst en lyckad säkerhetskopiering under en viss dag och hur många som inte har haft någon lyckad säkerhetskopiering under den dagen. Du kan klicka på en rad om du vill se information om alla säkerhetskopieringsjobb som har utlösts på den valda dagen. Observera att om du ökar intervallet till ett större värde, till exempel de senaste 60 dagarna, återges rutnätet i veckovyn och visar antalet objekt som har haft minst en lyckad säkerhetskopiering varje dag under den angivna veckan. På samma sätt finns det en månadsvy för större tidsintervall.

När det gäller objekt som säkerhetskopieras varje vecka hjälper det här rutnätet dig att identifiera alla objekt som har haft minst en lyckad säkerhetskopiering under den aktuella veckan. För ett större tidsintervall, till exempel de senaste 120 dagarna, återges rutnätet i månadsvyn och visar antalet objekt som har haft minst en lyckad säkerhetskopiering varje vecka under den angivna månaden. Se [Konventioner som används i Backup-rapporter](#conventions-used-in-backup-reports) mer information om dagliga, veckovisa och månatliga vyer.

![Principefter efterlevnad efter tidsperiod](./media/backup-azure-configure-backup-reports/policy-adherence-by-time-period.png)

* **Principefter efterlevnad av säkerhetskopieringsinstans:** Med den här vyn kan du principefterse efterlevnadsinformation på nivån för säkerhetskopieringsinstanser. En cell som är grön anger att säkerhetskopieringsinstansen hade minst en lyckad säkerhetskopiering den angivna dagen. En cell som är röd anger att säkerhetskopieringsinstansen inte ens har en lyckad säkerhetskopiering den angivna dagen. Aggregeringar per dag, vecka och månad följer samma beteende som vyn Principeftersening efter tidsperiod. Du kan klicka på valfri rad om du vill visa alla säkerhetskopieringsjobb på den angivna säkerhetskopieringsinstansen inom det valda tidsperioden.

![Principefter efterlevnad av Säkerhetskopieringsinstans](./media/backup-azure-configure-backup-reports/policy-adherence-by-backup-instance.png)

###### <a name="email-azure-backup-reports"></a>E-Azure Backup rapporter

Med hjälp **av funktionen e-postrapport** som är Backup-rapporter kan du skapa automatiserade uppgifter för att få regelbundna rapporter via e-post. Den här funktionen fungerar genom att distribuera en logikapp i din Azure-miljö som frågar efter data från dina valda Log Analytics-arbetsytor (LA), baserat på de indata som du anger.

När logikappen har skapats måste du auktorisera anslutningar till Azure Monitor Logs och Office 365. Det gör du genom att **Logic Apps** i Azure Portal och söka efter namnet på den uppgift som du har skapat. Om du **väljer menyalternativet API-anslutningar** öppnas listan över API-anslutningar som du behöver auktorisera. [Läs mer om hur du konfigurerar e-postmeddelanden och felsöker problem.](backup-reports-email.md)

###### <a name="customize-azure-backup-reports"></a>Anpassa Azure Backup rapporter

Backup-rapporter använder [systemfunktioner på Azure Monitor loggar](backup-reports-system-functions.md). Dessa funktioner fungerar på data i råtabellerna Azure Backup i LA och returnerar formaterade data som hjälper dig att enkelt hämta information om alla dina säkerhetskopieringsrelaterade entiteter med hjälp av enkla frågor. 

Om du vill skapa egna rapportarbetsböcker med Backup-rapporter som bas kan du  gå till Backup-rapporter, klicka på Redigera överst i rapporten och visa/redigera de frågor som används i rapporterna. Läs dokumentationen [om Azure-arbetsböcker](../azure-monitor/visualize/workbooks-overview.md) om du vill veta mer om hur du skapar anpassade rapporter. 

## <a name="export-to-excel"></a>Exportera till Excel

Välj nedåtpilen längst upp till höger i en widget, till exempel en tabell eller ett diagram, för att exportera innehållet i widgeten som ett Excel-blad som det är med befintliga filter tillämpade. Om du vill exportera fler rader i en tabell till Excel kan du öka antalet rader som visas på sidan med hjälp av listrpilen **Rader per** sida överst i varje rutnät.

## <a name="pin-to-dashboard"></a>Fäst vid instrumentpanelen

Välj fästknappen längst upp i varje widget för att fästa widgeten på Azure Portal instrumentpanel. Den här funktionen hjälper dig att skapa anpassade instrumentpaneler som är skräddarsydda för att visa den viktigaste informationen du behöver.

## <a name="cross-tenant-reports"></a>Rapporter mellan klienter

Om du använder [Azure Lighthouse med](../lighthouse/index.yml) delegerad åtkomst till prenumerationer i flera klientmiljöer kan du använda standardprenumerationsfiltret. Välj filterknappen i det övre högra hörnet av Azure Portal för att välja alla prenumerationer som du vill visa data för. På så sätt kan du välja Log Analytics-arbetsytor i klientorganisationen för att visa rapporter med flera klienter.

## <a name="conventions-used-in-backup-reports"></a>Konventioner som används i Säkerhetskopieringsrapporter

- Filter fungerar från vänster till höger och uppifrån och ned på varje flik. Det innebär att alla filter endast gäller för alla widgetar som är placerade till höger om filtret eller under filtret.
- Om du väljer en färgad panel filtreras widgetarna under panelen för poster som hör till värdet för panelen. Om du till exempel väljer  **panelen Stoppat** skydd på fliken Säkerhetskopieringsobjekt filtreras rutnäten och diagrammen nedan för att visa data för säkerhetskopieringsobjekt med tillståndet Skydd stoppade.
- Paneler som inte är färgade kan inte väljas.
- Data för den aktuella deldagen visas inte i rapporterna. När det valda värdet för Time **Range (Tidsintervall)** är **Last 7 days**(Senaste 7 dagarna) visar rapporten poster för de senaste sju slutförda dagarna. Den aktuella dagen ingår inte.
- Rapporten visar information om jobb (förutom loggjobb) som *utlöstes i* det valda tidsperioden.
- Värdena som visas **för Molnlagring** **och Skyddade instanser** finns i slutet *av* det valda tidsperioden.
- Säkerhetskopieringsobjekten som visas i rapporterna är de objekt som finns *i slutet* av det valda tidsperioden. Säkerhetskopieringsobjekt som togs bort mitt i det valda tidsperioden visas inte. Samma konvention gäller även för säkerhetskopieringsprinciper.
- Om det valda tidsintervallet sträcker sig över en period på 30 dagar mindre återges diagram i daglig vy, där det finns en datapunkt för varje dag. Om tidsintervallet sträcker sig över en period som är större än 30 dagar och mindre än (eller lika med) 90 dagar återges diagram i veckovyn. För större tidsintervall återges diagram i månadsvyn. Genom att aggregera data varje vecka eller varje månad får du bättre prestanda för frågor och enklare att läsa data i diagram.
- Rutnäten för principefter efterlevnad följer också en liknande aggregeringslogik som beskrivs ovan. Det finns dock några mindre skillnader. Den första skillnaden är att för objekt med princip för veckovis säkerhetskopiering finns det ingen daglig vy (endast veckovisa och månatliga vyer är tillgängliga). I rutnäten för objekt med en veckovis säkerhetskopieringspolicy betraktas dessutom en "månad" som en 4-veckorsperiod (28 dagar) och inte 30 dagar, för att eliminera partiella veckor från övervägande.

## <a name="query-load-times"></a>Frågebelastningstider

Widgetarna i säkerhetskopieringsrapporten drivs av Kusto-frågor som körs på användarens Log Analytics-arbetsytor. Dessa frågor omfattar vanligtvis bearbetning av stora mängder data, med flera kopplingar för att ge bättre insikter. Därför kanske widgetarna inte läses in omedelbart när användaren visar rapporter över en stor säkerhetskopia. Den här tabellen ger en ungefärlig uppskattning av hur lång tid det kan ta att läsa in olika widgetar, baserat på antalet säkerhetskopieringsobjekt och det tidsperiod som rapporten visas för.

| **Antal datakällor**                         | **Tids horisont** | **Ungefärliga inläsningstider**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~5 K                       | 1 månad          | Paneler: 5–10 sek <br> Rutnät: 5–10 sek <br> Diagram: 5–10 sek <br> Filter på rapportnivå: 5–10 sek|
| ~5 K                       | 3 månader          | Paneler: 5–10 sek <br> Rutnät: 5–10 sek <br> Diagram: 5–10 sek <br> Filter på rapportnivå: 5–10 sek|
| ~10 K                       | 3 månader          | Paneler: 15–20 sek <br> Rutnät: 15–20 sek <br> Diagram: 1–2 minuter <br> Filter på rapportnivå: 25–30 sek|
| ~15 K                       | 1 månad          | Paneler: 15–20 sek <br> Rutnät: 15–20 sek <br> Diagram: 50–60 sek <br> Filter på rapportnivå: 20–25 sek|
| ~15 K                       | 3 månader          | Paneler: 20–30 sekunder <br> Rutnät: 20–30 sek <br> Diagram: 2–3 minuter <br> Filter på rapportnivå: 50–60 sek |

## <a name="what-happened-to-the-power-bi-reports"></a> Vad har hänt med Power BI-rapporterna? 

- Den tidigare Power BI för rapportering, som har data från ett Azure Storage-konto, är på en utfasningsväg. Vi rekommenderar att du börjar skicka valvdiagnostikdata till Log Analytics för att visa rapporter.

- Dessutom är [V1-schemat för](./backup-azure-diagnostics-mode-data-model.md#v1-schema-vs-v2-schema) att skicka diagnostikdata till ett lagringskonto eller en LA-arbetsyta också på en utfasningsväg. Det innebär att om du har skrivit anpassade frågor eller automatiseringar baserat på V1-schemat rekommenderar vi att du uppdaterar dessa frågor så att de använder det V2-schema som stöds för närvarande.

## <a name="next-steps"></a>Nästa steg

[Läs mer om övervakning och rapportering med Azure Backup](./backup-azure-monitor-alert-faq.yml)