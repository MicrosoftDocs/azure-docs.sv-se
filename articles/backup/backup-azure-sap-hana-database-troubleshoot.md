---
title: Felsöka fel vid säkerhets kopiering av SAP HANA databaser
description: Beskriver hur du felsöker vanliga fel som kan uppstå när du använder Azure Backup för att säkerhetskopiera SAP HANA-databaser.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 22800adc323bda8a60278160f24bc559103fb57e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713345"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Felsöka säkerhets kopiering av SAP HANA databaser på Azure

Den här artikeln innehåller felsöknings information för att säkerhetskopiera SAP HANA databaser på virtuella Azure-datorer. Mer information om de SAP HANA säkerhets kopierings scenarier som vi för närvarande stöder finns i [scenario support](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Krav och behörigheter

Se [kraven](tutorial-backup-sap-hana-db.md#prerequisites) och [Vad skriptet för för registrering gör](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) i avsnitten innan du konfigurerar säkerhets kopieringar.

## <a name="common-user-errors"></a>Vanliga användar fel

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Fel meddelande**      | <span style="font-weight:normal">Azure Backup saknar de roll behörigheter som krävs för att utföra säkerhets kopiering</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **Möjliga orsaker**    | Rollen kan ha skrivits över.                          |
| **Rekommenderad åtgärd** | Lös problemet genom att köra skriptet från rutan för att **hitta databas** eller ladda ned det [här](https://aka.ms/scriptforpermsonhana). Du kan också lägga till rollen SAP_INTERNAL_HANA_SUPPORT till säkerhets kopierings användaren för arbets belastningen (AZUREWLBACKUPHANAUSER). |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Felmeddelande      | <span style="font-weight:normal">Det gick inte att ansluta till HANA-systemet</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | SAP HANA-instansen kan vara avstängd.<br/>De behörigheter som krävs för att Azure Backup interagera med HANA-databasen har inte angetts. |
| **Rekommenderad åtgärd** | Kontrol lera om SAP HANA databasen är igång. Om databasen är igång kontrollerar du om alla nödvändiga behörigheter är inställda. Om någon av behörigheterna saknas kör du för [registrerings skriptet](https://aka.ms/scriptforpermsonhana) för att lägga till de behörigheter som saknas. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| Felmeddelande      | <span style="font-weight:normal">Den angivna SAP HANA-instansen är antingen ogiltig eller kan inte hittas</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | Flera SAP HANA-instanser på en enskild virtuell Azure-dator kan inte säkerhets kopie ras. |
| **Rekommenderad åtgärd** | Kör för [registrerings skriptet](https://aka.ms/scriptforpermsonhana) på den SAP HANA-instans som du vill säkerhetskopiera. Kontakta Microsoft-supporten om problemet kvarstår. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Felmeddelande      | <span style="font-weight:normal">Den angivna SAP HANA åtgärden stöds inte</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | Azure Backup för SAP HANA stöder inte stegvis säkerhets kopiering och åtgärder som utförs på SAP HANA ursprungliga klienter (Studio/cockpit/DBA-cockpit) |
| **Rekommenderad åtgärd** | Mer information finns [här](./sap-hana-backup-support-matrix.md#scenario-support). |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| Felmeddelande      | <span style="font-weight:normal">Logg kedjan för säkerhets kopiering är bruten</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | Mål platsen för säkerhets kopieringen kan ha uppdaterats från backint till fil systemet, eller så har den körbara filen backint ändrats |
| **Rekommenderad åtgärd** | Utlös en fullständig säkerhets kopiering för att lösa problemet                   |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| Felmeddelande      | <span style="font-weight:normal">SDC till MDC-uppgradering upptäcktes</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | SAP HANA-instansen har uppgraderats från SDC till MDC. Säkerhets kopieringar kommer att Miss lyckas efter uppdateringen. |
| **Rekommenderad åtgärd** | Följ stegen som anges i [SDC för att MDC uppgraderingen](#sdc-to-mdc-upgrade-with-a-change-in-sid) för att lösa problemet |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Felmeddelande      | <span style="font-weight:normal">En ogiltig backint-konfiguration upptäcktes</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | De här säkerhets parametrarna har angetts felaktigt för Azure Backup |
| **Rekommenderad åtgärd** | Kontrol lera om följande (backint) parametrar har angetts:<br/>\* [catalog_backup_using_backint: sant]<br/>\* [enable_accumulated_catalog_backup: falskt]<br/>\* [parallel_data_backup_backint_channels: 1]<br/>\* [log_backup_timeout_s: 900)]<br/>\* [backint_response_timeout: 7200]<br/>Om det finns backint-baserade parametrar i värden tar du bort dem. Om parametrarna inte finns på VÄRDnivå men har ändrats manuellt på en databas nivå, återställer du dem till lämpliga värden enligt beskrivningen ovan. Du kan också köra [stoppa skyddet och behålla säkerhets kopierings data](./sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) från Azure Portal och sedan välja **återuppta säkerhets kopiering**. |

### <a name="usererrorincompatiblesrctargetsystemsforrestore"></a>UserErrorIncompatibleSrcTargetSystemsForRestore

|Felmeddelande  |Käll-och mål systemen för återställning är inkompatibla  |
|---------|---------|
|Möjliga orsaker   | Käll-och mål systemen som valts för återställning är inkompatibla        |
|Rekommenderad åtgärd   |   Se till att ditt återställnings scenario inte finns i följande lista över möjliga inkompatibla återställningar: <br><br>   **Fall 1:** SYSTEMDB kan inte byta namn under återställningen.  <br><br> **Fall 2:** Source-SDC och Target-MDC: käll databasen kan inte återställas som SYSTEMDB eller klient organisations databasen på målet. <br><br> **Fall 3:** Source-MDC och Target-SDC: det går inte att återställa käll databasen (SYSTEMDB eller klient organisations databasen) till målet. <br><br>  Mer information finns i anmärkning **1642148** i Start [fönstret för SAP-support](https://launchpad.support.sap.com). |

## <a name="restore-checks"></a>Återställnings kontroller

### <a name="single-container-database-sdc-restore"></a>Återställning av en SDC-databas (Single container Database)

Ta hand om indata när du återställer en enda behållar databas (SDC) för HANA till en annan SDC-dator. Databas namnet måste anges med gemener och "SDC" har lagts till inom hakparenteser. HANA-instansen kommer att visas med versaler.

Anta att en SDC HANA-instans "H21" säkerhets kopie ras. På sidan säkerhets kopierings objekt visas namnet **"H21 (SDC)"** för säkerhets kopierings objekt. Om du försöker återställa databasen till en annan mål SDC, säg H11, måste du ange följande indata.

![Namn på återställd SDC-databas](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Observera följande punkter:

- Som standard fylls det återställda DB-namnet på med namnet på säkerhets kopie posten. I det här fallet H21 (SDC).
- Om du väljer målet som H11 ändras inte det återställda databas namnet automatiskt. **Den bör redige ras till H11 (SDC)**. För SDC kommer det återställda databas namnet att vara mål instans-ID: t med gemener och "SDC" i hakparenteser.
- Eftersom SDC bara kan ha en enda databas, måste du också markera kryss rutan för att tillåta åsidosättning av befintliga databas data med återställnings punkt data.
- Linux är Skift läges känsligt. Var noga med att bevara ärendet.

### <a name="multiple-container-database-mdc-restore"></a>Återställning av MDC (Multiple container Database)

I flera container-databaser för HANA är standard konfigurationen SYSTEMDB + 1 eller flera klient-databaser. Att återställa en hel SAP HANA instans innebär att återställa både SYSTEMDB-och klient databaser. En återställer SYSTEMDB först och fortsätter sedan för klient organisations databasen. System DB innebär i princip att åsidosätta system informationen på det valda målet. Den här återställningen åsidosätter också BackInt-relaterad information i mål instansen. Så när system databasen har återställts till en mål instans kör du skriptet för för registrering igen. Det går bara att återställa efterföljande klient databas återställningar.

## <a name="back-up-a-replicated-vm"></a>Säkerhetskopiera en replikerad virtuell dator

### <a name="scenario-1"></a>Scenario 1

Den ursprungliga virtuella datorn replikerades med Azure Site Recovery eller virtuella Azure-säkerhetskopieringar. Den nya virtuella datorn har skapats för att simulera den gamla virtuella datorn. Det vill säga inställningarna är exakt desamma. (Detta beror på att den ursprungliga virtuella datorn togs bort och återställningen gjordes från VM-säkerhetskopiering eller Azure Site Recovery).

Det här scenariot kan innehålla två möjliga fall. Lär dig hur du säkerhetskopierar den replikerade virtuella datorn i båda fallen:

1. Den nya virtuella datorn har samma namn och finns i samma resurs grupp och prenumeration som den borttagna virtuella datorn.

    - Tillägget finns redan på den virtuella datorn, men är inte synligt för någon av tjänsterna
    - Kör skriptet för för registrering
    - Registrera tillägget på nytt för samma dator i Azure Portal (  ->  **information om** säkerhets kopia-> Markera den relevanta Azure VM-> omregistrera)
    - De redan befintliga säkerhetskopierade databaserna (från den borttagna virtuella datorn) bör sedan börja säkerhets kopie ras

2. Den nya virtuella datorn som skapas har antingen:

    - ett annat namn än den borttagna virtuella datorn
    - samma namn som den borttagna virtuella datorn men finns i en annan resurs grupp eller prenumeration (jämfört med den borttagna virtuella datorn)

    Gör i så fall följande steg:

    - Tillägget finns redan på den virtuella datorn, men är inte synligt för någon av tjänsterna
    - Kör skriptet för för registrering
    - Om du identifierar och skyddar de nya databaserna börjar du se duplicera aktiva databaser i portalen. Undvik detta genom att [stoppa skyddet med Behåll data](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) för de gamla databaserna. Fortsätt sedan med återstående steg.
    - Identifiera databaserna för att aktivera säkerhets kopiering
    - Aktivera säkerhets kopiering på dessa databaser
    - De redan befintliga säkerhetskopierade databaserna (från den borttagna virtuella datorn) fortsätter att lagras i valvet (och deras säkerhets kopior bevaras enligt principen)

### <a name="scenario-2"></a>Scenario 2

Den ursprungliga virtuella datorn replikerades med Azure Site Recovery eller virtuella Azure-säkerhetskopieringar. Den nya virtuella datorn byggdes ut från innehållet – och används som mall. Det här är en ny virtuell dator med ett nytt SID.

Följ de här stegen för att aktivera säkerhets kopiering på den nya virtuella datorn:

- Tillägget finns redan på den virtuella datorn, men är inte synligt för någon av tjänsterna
- Kör skriptet för för registrering. Två scenarier kan uppstå baserat på SID för den nya virtuella datorn:
  - Den ursprungliga virtuella datorn och den nya virtuella datorn har samma SID. Skriptet för för registrering kommer att köras.
  - Den ursprungliga virtuella datorn och den nya virtuella datorn har olika sid. Skriptet för för registrering kommer inte att fungera. Kontakta supporten för att få hjälp i det här scenariot.
- Identifiera de databaser som du vill säkerhetskopiera
- Aktivera säkerhets kopiering på dessa databaser

## <a name="sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm"></a>SDC versions uppgradering eller MDC versions uppgradering på samma virtuella dator

Uppgraderingar av operativ systemet, SDC versions ändring eller MDC versions ändring som inte orsakar en SID ändring kan hanteras på följande sätt:

- Se till att den nya versionen av OS, SDC eller MDC stöds för närvarande [av Azure Backup](sap-hana-backup-support-matrix.md#scenario-support)
- [Stoppa skyddet med Behåll data](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) för databasen
- Genomför uppgraderingen eller uppdateringen
- Kör skriptet för för registrering igen. Uppgraderings processen kan ofta ta bort [de nödvändiga rollerna](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does). Genom att köra skriptet för för registrering kan du kontrol lera alla nödvändiga roller.
- Återuppta skyddet av databasen igen

## <a name="sdc-to-mdc-upgrade-with-no-change-in-sid"></a>SDC till MDC-uppgradering utan ändring i SID

Uppgraderingar från SDC till MDC som inte orsakar en SID ändring kan hanteras på följande sätt:

- Se till att den nya MDC-versionen [stöds för närvarande av Azure Backup](sap-hana-backup-support-matrix.md#scenario-support)
- [Stoppa skyddet med Behåll data](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) för den gamla SDC-databasen
- Genomför uppgraderingen. Efter slut för ande är HANA-systemet nu MDC med system DB och klient databaser
- Kör [skriptet för för registrering](https://aka.ms/scriptforpermsonhana) igen
- Registrera tillägget på nytt för samma dator i Azure Portal (  ->  **information om** säkerhets kopia-> Markera den relevanta Azure VM-> omregistrera)
- Välj **identifiera databaser** på samma virtuella dator. Den här åtgärden ska visa den nya databaser i steg 3 som SYSTEMDB och klient organisations databasen, inte SDC
- Den äldre SDC-databasen fortsätter att finnas i valvet och har tidigare säkerhetskopierade data som kvarhålls enligt principen
- Konfigurera säkerhets kopiering för dessa databaser

## <a name="sdc-to-mdc-upgrade-with-a-change-in-sid"></a>SDC till MDC-uppgradering med en ändring i SID

Uppgraderingar från SDC till MDC som orsakar en SID ändring kan hanteras på följande sätt:

- Se till att den nya MDC-versionen [stöds för närvarande av Azure Backup](sap-hana-backup-support-matrix.md#scenario-support)
- **Stoppa skyddet med Behåll data** för den gamla SDC-databasen
- Genomför uppgraderingen. Efter slut för ande är HANA-systemet nu MDC med system DB och klient databaser
- Kör [skriptet för för registrering](https://aka.ms/scriptforpermsonhana) med rätt information (ny sid-och MDC). På grund av en ändring i SID kan du stöta på problem med att köra skriptet. Kontakta Azure Backup support om du har problem.
- Registrera tillägget på nytt för samma dator i Azure Portal (  ->  **information om** säkerhets kopia-> Markera den relevanta Azure VM-> omregistrera)
- Välj **identifiera databaser** på samma virtuella dator. Den här åtgärden ska visa den nya databaser i steg 3 som SYSTEMDB och klient organisations databasen, inte SDC
- Den äldre SDC-databasen kommer fortfarande att finnas kvar i valvet och har tidigare säkerhetskopierade data som kvarhålls enligt principen
- Konfigurera säkerhets kopiering för dessa databaser

## <a name="re-registration-failures"></a>Försök att registrera igen

Kontrol lera om det finns ett eller flera av följande symptom innan du utlöser omregistrerings åtgärden:

- Alla åtgärder (till exempel säkerhets kopiering, återställning och konfigurations säkerhets kopiering) kan inte utföras på den virtuella datorn med någon av följande felkoder: **WorkloadExtensionNotReachable, UserErrorWorkloadExtensionNotInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDidntDequeueMsg**.
- Om **säkerhets kopierings status** fältet för det säkerhetskopierade objektet **visas kan du** utesluta alla andra orsaker som kan resultera i samma status:

  - Saknar behörighet att utföra säkerhetskopierade åtgärder på den virtuella datorn
  - Den virtuella datorn stängs av, så säkerhets kopieringen kan inte ske
  - Nätverks problem

Dessa symtom kan uppstå på grund av en eller flera av följande orsaker:

- Ett tillägg har tagits bort eller avinstallerats från portalen.
- Den virtuella datorn återställdes i tid genom återställning av disk på plats.
- Den virtuella datorn stängdes under en längre period, så tilläggs konfigurationen på den har upphört att gälla.
- Den virtuella datorn har tagits bort och en annan virtuell dator har skapats med samma namn och i samma resurs grupp som den borttagna virtuella datorn.

I föregående scenarier rekommenderar vi att du utlöser en ny registrering på den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

- Läs [vanliga frågor](./sap-hana-faq-backup-azure-vm.md) om hur du säkerhetskopierar SAP HANA databaser på virtuella Azure-datorer.
