---
title: Felsöka SAP HANA säkerhetskopieringsfel för databaser
description: Beskriver hur du felsöker vanliga fel som kan uppstå när du använder Azure Backup för att SAP HANA databaser.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: cdf4c26aa32d65ec63ec84d85e454adaaf2ece8d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517240"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Felsöka säkerhetskopiering SAP HANA databaser i Azure

Den här artikeln innehåller felsökningsinformation för att eftersom SAP HANA databaser på virtuella Azure-datorer. Mer information om de säkerhetskopieringsscenarier SAP HANA stöder för närvarande finns i [Scenariostöd](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Förutsättningar och behörigheter

Se avsnitten [Förutsättningar och](tutorial-backup-sap-hana-db.md#prerequisites) [Vad förregistreringsskriptet gör innan](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) du konfigurerar säkerhetskopior.

## <a name="common-user-errors"></a>Vanliga användarfel

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Felmeddelande**      | <span style="font-weight:normal">Azure Backup har inte de rollbehörigheter som krävs för att utföra säkerhetskopiering</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **Möjliga orsaker**    | Rollen kan ha skrivits över.                          |
| **Rekommenderad åtgärd** | Lös problemet genom att köra skriptet från fönstret **Identifiera databas** eller ladda ned det [här](https://aka.ms/scriptforpermsonhana). Du kan också lägga till rollen "SAP_INTERNAL_HANA_SUPPORT" till arbetsbelastningssäkerhetskopieringsanvändaren (AZUREWLBACKUPHANAUSER). |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Felmeddelande      | <span style="font-weight:normal">Det gick inte att ansluta till HANA-systemet</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | Den SAP HANA instansen kan vara nere.<br/>De behörigheter som krävs Azure Backup att interagera med HANA-databasen har inte angetts. |
| **Rekommenderad åtgärd** | Kontrollera om SAP HANA databasen är slut. Om databasen är igång kontrollerar du att alla behörigheter som krävs har angetts. Om någon av behörigheterna saknas kör du [förregistreringsskriptet för att](https://aka.ms/scriptforpermsonhana) lägga till de behörigheter som saknas. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| Felmeddelande      | <span style="font-weight:normal">Den angivna SAP HANA-instansen är ogiltig eller kan inte hittas</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | Flera SAP HANA instanser på en enda virtuell Azure-dator kan inte säkerhetskopieras. |
| **Rekommenderad åtgärd** | Kör [förregistreringsskriptet](https://aka.ms/scriptforpermsonhana) på den SAP HANA instans som du vill registra upp. Kontakta Microsoft-supporten om problemet kvarstår. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Felmeddelande      | <span style="font-weight:normal">Den SAP HANA åtgärden stöds inte</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | Azure Backup för SAP HANA stöder inte inkrementell säkerhetskopiering och åtgärder som utförs på SAP HANA inbyggda klienter (Studio/Cockpit/DBA Cockpit) |
| **Rekommenderad åtgärd** | Mer information finns [här.](./sap-hana-backup-support-matrix.md#scenario-support) |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| Felmeddelande      | <span style="font-weight:normal">Loggkedjan för säkerhetskopiering är bruten</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | Målet för loggsäkerhetskopiering kan ha uppdaterats från backint till filsystemet eller så kan backint-filen ha ändrats |
| **Rekommenderad åtgärd** | Utlösa en fullständig säkerhetskopia för att lösa problemet                   |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| Felmeddelande      | <span style="font-weight:normal">SDC till MDC-uppgradering har identifierats</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | Instansen SAP HANA har uppgraderats från SDC till MDC. Säkerhetskopieringar misslyckas efter uppdateringen. |
| **Rekommenderad åtgärd** | Följ stegen i [SDC till MDC-uppgraderingen för](#sdc-to-mdc-upgrade-with-a-change-in-sid) att lösa problemet |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Felmeddelande      | <span style="font-weight:normal">Ogiltig backint-konfiguration har identifierats</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | Parametrarna för stöd har angetts felaktigt för Azure Backup |
| **Rekommenderad åtgärd** | Kontrollera om följande (backint)-parametrar har angetts:<br/>\* [catalog_backup_using_backint:true]<br/>\* [enable_accumulated_catalog_backup:falskt]<br/>\* [parallel_data_backup_backint_channels:1]<br/>\* [log_backup_timeout_s:900)]<br/>\* [backint_response_timeout:7200]<br/>Om det finns backint-baserade parametrar i HOST tar du bort dem. Om parametrarna inte finns på VÄRD-nivå men har ändrats manuellt på databasnivå återställer du dem till lämpliga värden enligt beskrivningen ovan. Du kan också [köra stoppskydd och behålla säkerhetskopierade data](./sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) från Azure Portal och sedan välja **Återuppta säkerhetskopiering.** |

### <a name="usererrorincompatiblesrctargetsystemsforrestore"></a>UserErrorIncompatibleSrcTargetSystemsForRestore

|Felmeddelande  |Käll- och målsystemen för återställning är inkompatibla  |
|---------|---------|
|Möjliga orsaker   | Käll- och målsystemen som valts för återställning är inkompatibla        |
|Rekommenderad åtgärd   |   Se till att återställningsscenariot inte finns med i följande lista över möjliga inkompatibla återställningar: <br><br>   **Fall 1:** SYSTEMDB kan inte byta namn under återställningen.  <br><br> **Fall 2:** Källa – SDC och mål – MDC: Källdatabasen kan inte återställas som SYSTEMDB eller klientdatabas på målet. <br><br> **Fall 3:** Källa – MDC och mål – SDC: Källdatabasen (SYSTEMDB eller klientdatabasen) kan inte återställas till målet. <br><br>  Mer information finns i anmärkning **1642148** i [SAP-supportfönstret](https://launchpad.support.sap.com). |

## <a name="restore-checks"></a>Återställningskontroller

### <a name="single-container-database-sdc-restore"></a>Återställning av enkel containerdatabas (SDC)

Ta hand om indata när du återställer en enskild containerdatabas (SDC) för HANA till en annan SDC-dator. Databasnamnet ska anges med gemener och med "sdc" tillagt inom hakparenteser. HANA-instansen visas i versaler.

Anta att en SDC HANA-instans "H21" säkerhetskopieras. På sidan säkerhetskopieringsobjekt visas namnet på säkerhetskopieringsobjektet **som "h21(sdc)".** Om du försöker återställa den här databasen till en annan mål-SDC, t.ex. H11, måste följande indata anges.

![Återställt SDC-databasnamn](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Observera följande punkter:

- Som standard fylls det återställda db-namnet i med namnet på säkerhetskopieringsobjektet. I det här fallet h21(sdc).
- Om du väljer målet som H11 ändras inte namnet på den återställda databasen automatiskt. **Den bör redigeras till h11(sdc).** När det gäller SDC blir det återställda db-namnet målinstans-ID:t med gemener och sdc tillagt inom hakparenteser.
- Eftersom SDC bara kan ha en enkel databas måste du också markera kryssrutan för att tillåta åsidosättning av befintliga databasdata med återställningspunktsdata.
- Linux är fallkänsligt. Var därför noga med att bevara ärendet.

### <a name="multiple-container-database-mdc-restore"></a>Återställning med flera containerdatabaser (MDC)

I flera containerdatabaser för HANA är standardkonfigurationen SYSTEMDB + 1 eller fler klientdatabaser. Att återställa en hel SAP HANA-instans innebär att återställa både SYSTEMDB och klient-DBs. En återställer FÖRST SYSTEMDB och fortsätter sedan för Klientorganisationsdatabas. System-DB innebär i princip att åsidosätta systeminformationen på det valda målet. Den här återställningen åsidosätter även den BackInt-relaterade informationen i målinstansen. Så när system-DB har återställts till en målinstans kör du förregistreringsskriptet igen. Först därefter lyckas de efterföljande återställningarna av klientdatabasen.

## <a name="back-up-a-replicated-vm"></a>Backa upp en replikerad virtuell dator

### <a name="scenario-1"></a>Scenario 1

Den ursprungliga virtuella datorn replikerades med hjälp av Azure Site Recovery eller säkerhetskopiering av virtuella Azure-datorer. Den nya virtuella datorn har skapats för att simulera den gamla virtuella datorn. Det innebär att inställningarna är exakt desamma. (Det beror på att den ursprungliga virtuella datorn togs bort och återställningen gjordes från säkerhetskopiering av virtuell dator eller Azure Site Recovery).

Det här scenariot kan omfatta två möjliga fall. Lär dig hur du kopierar den replikerade virtuella datorn i båda dessa fall:

1. Den nya virtuella datorn som skapades har samma namn och finns i samma resursgrupp och prenumeration som den borttagna virtuella datorn.

    - Tillägget finns redan på den virtuella datorn, men är inte synligt för någon av tjänsterna
    - Kör förregistreringsskriptet
    - Omregistrera tillägget för samma dator i Azure Portal (**Information** om säkerhetskopieringsvy - > Välj relevant virtuell  ->   Azure-dator - > registrera dig)
    - De redan befintliga säkerhetskopierade databaserna (från den borttagna virtuella datorn) bör sedan börja säkerhetskopieras

2. Den nya virtuella datorn som skapades har antingen:

    - ett annat namn än den borttagna virtuella datorn
    - samma namn som den borttagna virtuella datorn men finns i en annan resursgrupp eller prenumeration (jämfört med den borttagna virtuella datorn)

    Om så är fallet gör du följande:

    - Tillägget finns redan på den virtuella datorn, men är inte synligt för någon av tjänsterna
    - Kör förregistreringsskriptet
    - Om du identifierar och skyddar de nya databaserna börjar du se duplicerade aktiva databaser i portalen. Undvik detta genom att [stoppa skyddet med kvarhållna data](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) för de gamla databaserna. Fortsätt sedan med de återstående stegen.
    - Identifiera databaserna för att aktivera säkerhetskopiering
    - Aktivera säkerhetskopieringar på dessa databaser
    - De redan befintliga säkerhetskopierade databaserna (från den borttagna virtuella datorn) fortsätter att lagras i valvet (med sina säkerhetskopior kvar enligt principen)

### <a name="scenario-2"></a>Scenario 2

Den ursprungliga virtuella datorn replikerades med hjälp Azure Site Recovery eller azure VM-säkerhetskopiering. Den nya virtuella datorn har byggts ut från innehållet – som ska användas som en mall. Det här är en ny virtuell dator med ett nytt SID.

Följ dessa steg om du vill aktivera säkerhetskopieringar på den nya virtuella datorn:

- Tillägget finns redan på den virtuella datorn, men är inte synligt för någon av tjänsterna
- Kör förregistreringsskriptet. Baserat på SID för den nya virtuella datorn kan två scenarier uppstå:
  - Den ursprungliga virtuella datorn och den nya virtuella datorn har samma SID. Förregistreringsskriptet körs korrekt.
  - Den ursprungliga virtuella datorn och den nya virtuella datorn har olika SID:er. Förregistreringsskriptet misslyckas. Kontakta supporten för att få hjälp i det här scenariot.
- Identifiera de databaser som du vill backa upp
- Aktivera säkerhetskopieringar på dessa databaser

## <a name="sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm"></a>Uppgradering av SDC-version eller uppgradering av MDC-version på samma virtuella dator

Uppgraderingar av operativsystemet, ändring av SDC-version eller MDC-version som inte orsakar en SID-ändring kan hanteras på följande sätt:

- Kontrollera att den nya os-versionen, SDC- eller MDC-versionen stöds [av Azure Backup](sap-hana-backup-support-matrix.md#scenario-support)
- [Stoppa skyddet med kvarhållna data](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) för databasen
- Utföra uppgraderingen eller uppdateringen
- Kör skriptet för förregistrering igen. Uppgraderingsprocessen kan ofta ta bort [de nödvändiga rollerna](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does). Genom att köra förregistreringsskriptet kan du kontrollera alla roller som krävs.
- Återuppta skyddet av databasen igen

## <a name="sdc-to-mdc-upgrade-with-no-change-in-sid"></a>Uppgradering från SDC till MDC utan ändring av SID

Uppgraderingar från SDC till MDC som inte orsakar en SID-ändring kan hanteras på följande sätt:

- Kontrollera att den nya MDC-versionen stöds [av Azure Backup](sap-hana-backup-support-matrix.md#scenario-support)
- [Stoppa skyddet med kvarhållna data](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) för den gamla SDC-databasen
- Utför uppgraderingen. Efter slutförandet är HANA-systemet nu MDC med en systemdatabas och klientdatabas
- Kör skriptet [för förregistrering igen](https://aka.ms/scriptforpermsonhana)
- Omregistrera tillägget för samma dator i Azure Portal (**Information** om säkerhetskopieringsvy - > Välj relevant virtuell  ->   Azure-dator - > registrera dig)
- Välj Rediscover DBs for the same VM **(Omidentifiera DBs** för samma virtuella dator). Den här åtgärden bör visa de nya databaserna i steg 3 som SYSTEMDB och Klientdatabas, inte SDC
- Den äldre SDC-databasen fortsätter att finnas i valvet och har gamla säkerhetskopierade data kvar enligt principen
- Konfigurera säkerhetskopiering för dessa databaser

## <a name="sdc-to-mdc-upgrade-with-a-change-in-sid"></a>Uppgradering från SDC till MDC med ändring av SID

Uppgraderingar från SDC till MDC som orsakar en SID-ändring kan hanteras på följande sätt:

- Kontrollera att den nya MDC-versionen stöds [av Azure Backup](sap-hana-backup-support-matrix.md#scenario-support)
- **Stoppa skyddet med kvarhållna data** för den gamla SDC-databasen
- Utför uppgraderingen. Efter slutförandet är HANA-systemet nu MDC med en systemdatabas och klientdatabas
- Kör skriptet för [förregistrering igen med](https://aka.ms/scriptforpermsonhana) rätt information (ny SID och MDC). På grund av en ändring i SID kan det uppstå problem med att köra skriptet. Kontakta Azure Backup supporten om du får problem.
- Omregistrera tillägget för samma dator i Azure Portal (**Information** om  ->  **säkerhetskopieringsvy** - > Välj relevant virtuell Azure-dator - > registrera dig)
- Välj Rediscover DBs for the same VM **(Återidentifiera DBs** för samma virtuella dator). Den här åtgärden bör visa de nya databaserna i steg 3 som SYSTEMDB och Klientdatabas, inte SDC
- Den äldre SDC-databasen fortsätter att finnas i valvet och har gamla säkerhetskopierade data kvarhållna enligt principen
- Konfigurera säkerhetskopiering för dessa databaser

## <a name="re-registration-failures"></a>Fel vid omregistrering

Sök efter ett eller flera av följande symptom innan du utlöser omregistrerar åtgärden:

- Alla åtgärder (till exempel säkerhetskopiering, återställning och konfiguration av säkerhetskopiering) misslyckas på den virtuella datorn med någon av följande felkoder: **WorkloadExtensionNotReachable, UserErrorWorkloadExtensionNotInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDidntDequeueMsg**.
- Om området **Säkerhetskopieringsstatus** för säkerhetskopieringsobjektet inte kan nås **utesluter** du alla andra orsaker som kan resultera i samma status:

  - Saknar behörighet att utföra säkerhetskopieringsrelaterade åtgärder på den virtuella datorn
  - Den virtuella datorn stängs av, så det går inte att säkerhetskopiera
  - Nätverksproblem

Dessa symptom kan uppstå av en eller flera av följande orsaker:

- Ett tillägg har tagits bort eller avinstallerats från portalen.
- Den virtuella datorn återställdes bakåt i tiden genom återställning på plats av disken.
- Den virtuella datorn stängdes av under en längre period, så tilläggskonfigurationen på den har upphört att gälla.
- Den virtuella datorn har tagits bort och en annan virtuell dator har skapats med samma namn och i samma resursgrupp som den borttagna virtuella datorn.

I föregående scenarier rekommenderar vi att du utlöser en omregistrerad åtgärd på den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

- Läs vanliga [frågor och svar om](./sap-hana-faq-backup-azure-vm.yml) hur du SAP HANA databaser på virtuella Azure-datorer.
