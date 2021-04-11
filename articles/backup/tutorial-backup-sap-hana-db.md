---
title: Självstudie – säkerhetskopiera SAP HANA databaser i virtuella Azure-datorer
description: I den här självstudien lär du dig att säkerhetskopiera SAP HANA databaser som körs på virtuella Azure-datorer till ett Azure Backup Recovery Services-valv.
ms.topic: tutorial
ms.date: 02/24/2020
ms.openlocfilehash: 00109de349c1fdfdbaff9de30d18f64d8b986a59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587652"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Självstudie: säkerhetskopiera SAP HANA databaser på en virtuell Azure-dator

Den här självstudien visar hur du säkerhetskopierar SAP HANA databaser som körs på virtuella Azure-datorer till ett Azure Backup Recovery Services-valv. I den här artikeln får du lära dig att:

> [!div class="checklist"]
>
> * Skapa och konfigurera ett valv
> * Identifiera databaser
> * Konfigurera säkerhets kopior

[Här](sap-hana-backup-support-matrix.md#scenario-support) följer alla scenarier som vi för närvarande stöder.

>[!NOTE]
>Från och med den 1 augusti 2020 är SAP HANA säkerhets kopiering för RHEL (7,4, 7,6, 7,7 & 8,1) allmänt tillgänglig.

## <a name="prerequisites"></a>Förutsättningar

Kontrol lera att du gör följande innan du konfigurerar säkerhets kopieringar:

* Identifiera eller skapa ett [Recovery Services valv](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) i samma region och prenumeration som den virtuella dator som kör SAP HANA.
* Tillåt anslutning från den virtuella datorn till Internet, så att den kan komma åt Azure, enligt beskrivningen i avsnittet [Konfigurera nätverks anslutning](#set-up-network-connectivity) nedan.
* Se till att den sammanlagda längden på den SAP HANA serverns virtuella dator namn och resurs gruppens namn inte överskrider 84 tecken för Azure Resource Manager (ARM_ virtuella datorer (och 77 tecken för klassiska virtuella datorer). Den här begränsningen beror på att vissa tecken reserveras av tjänsten.
* En nyckel ska finnas i **hdbuserstore** som uppfyller följande kriterier:
  * Den måste finnas i standard **hdbuserstore**. Standardvärdet är det `<sid>adm` konto som SAP HANA är installerat under.
  * För MDC ska nyckeln peka mot SQL-porten för **namnserver**. Om det är SDC ska det peka på SQL-porten för **INDEXSERVER**
  * Den bör ha autentiseringsuppgifter för att lägga till och ta bort användare
  * Observera att den här nyckeln kan tas bort när skriptet har körts
* Kör konfigurations skriptet för SAP HANA säkerhets kopiering (för registrerings skriptet) på den virtuella datorn där HANA är installerat, som rot användare. [Det här skriptet](https://aka.ms/scriptforpermsonhana) hämtar Hana-systemet för säkerhets kopiering. Mer information om skriptet för för registrering finns i avsnittet [Vad skriptet gör för registrering](#what-the-pre-registration-script-does) .
* Om din HANA-installation använder privata slut punkter kör du [skriptet för för registrering](https://aka.ms/scriptforpermsonhana) med parametern *-SN* eller *--Skip-Network-checkar* .

>[!NOTE]
>För registrerings skriptet installerar **unixODBC234** för SAP HANA arbets belastningar som körs på RHEL (7,4, 7,6 och 7,7) och **unixODBC** för RHEL 8,1. [Paketet finns i RHEL for SAP HANA (för RHEL 7-Server) uppdaterings tjänster för SAP-lösningar (RPMS) lagrings platsen](https://access.redhat.com/solutions/5094721).  För en Azure Marketplace RHEL-avbildning skulle lagrings platsen vara **rhui-RHEL-SAP-HANA-for-RHEL-7-Server-rhui-E4S-RPMS**.

## <a name="set-up-network-connectivity"></a>Konfigurera nätverks anslutning

För alla åtgärder kräver en SAP HANA databas som körs på en virtuell Azure-dator anslutning till Azure Backup tjänst, Azure Storage och Azure Active Directory. Detta kan uppnås med hjälp av privata slut punkter eller genom att tillåta åtkomst till de offentliga IP-adresser eller FQDN: er som krävs. Att inte tillåta korrekt anslutning till de nödvändiga Azure-tjänsterna kan leda till fel i åtgärder som databas identifiering, konfigurering av säkerhets kopiering, säkerhets kopiering och återställning av data.

I följande tabell visas de olika alternativ som du kan använda för att upprätta anslutningen:

| **Alternativ**                        | **Fördelar**                                               | **Nackdelar**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Privata slutpunkter                 | Tillåt säkerhets kopiering över privata IP-adresser i det virtuella nätverket  <br><br>   Ge detaljerad kontroll över nätverket och valv Sidan | Debiterar standard [avgifter](https://azure.microsoft.com/pricing/details/private-link/) för privata slut punkter |
| NSG service-Taggar                  | Enklare att hantera när intervall ändringar slås samman automatiskt   <br><br>   Inga ytterligare kostnader | Kan endast användas med NSG: er  <br><br>    Ger åtkomst till hela tjänsten |
| Azure Firewall FQDN-Taggar          | Enklare att hantera eftersom de nödvändiga FQDN-namnen hanteras automatiskt | Kan endast användas med Azure brand vägg                         |
| Tillåt åtkomst till tjänst-FQDN/IP-adresser | Inga ytterligare kostnader   <br><br>  Fungerar med alla nätverks säkerhetsutrustningar och brand väggar | En stor uppsättning IP-adresser eller FQDN-namn kan krävas för åtkomst   |
| Använda en HTTP-proxy                 | En enda punkt i Internet åtkomst till virtuella datorer                       | Ytterligare kostnader för att köra en virtuell dator med proxy-programvaran         |

Mer information om hur du använder dessa alternativ delas nedan:

### <a name="private-endpoints"></a>Privata slutpunkter

Med privata slut punkter kan du ansluta säkert från servrar i ett virtuellt nätverk till ditt Recovery Services-valv. Den privata slut punkten använder en IP-adress från VNET-adressutrymmet för ditt valv. Nätverks trafiken mellan resurserna i det virtuella nätverket och valvet överförs över ditt virtuella nätverk och en privat länk i Microsoft stamnät nätverket. Detta eliminerar exponeringen från det offentliga Internet. Läs mer om privata slut punkter för Azure Backup [här](./private-endpoints.md).

### <a name="nsg-tags"></a>NSG-Taggar

Om du använder nätverks säkerhets grupper (NSG) använder du tjänst tag gen *AzureBackup* för att tillåta utgående åtkomst till Azure Backup. Förutom taggen Azure Backup måste du också tillåta anslutning för autentisering och data överföring genom att skapa liknande [NSG-regler](../virtual-network/network-security-groups-overview.md#service-tags) för Azure AD (*AzureActiveDirectory*) och Azure Storage (*lagring*). Följande steg beskriver processen för att skapa en regel för taggen Azure Backup:

1. I **alla tjänster** går du till **nätverks säkerhets grupper** och väljer Nätverks säkerhets gruppen.

1. Välj **utgående säkerhets regler** under **Inställningar**.

1. Välj **Lägg till**. Ange all information som krävs för att skapa en ny regel enligt beskrivningen i [säkerhets regel inställningar](../virtual-network/manage-network-security-group.md#security-rule-settings). Se till att alternativet **destination** har angetts till *service tag* och **mål tjänst tag gen** är inställt på *AzureBackup*.

1. Välj **Lägg till**  för att spara den nyligen skapade utgående säkerhets regeln.

Du kan också skapa [NSG utgående säkerhets regler](../virtual-network/network-security-groups-overview.md#service-tags) för Azure Storage och Azure AD. Mer information om service märken finns i [den här artikeln](../virtual-network/service-tags-overview.md).

### <a name="azure-firewall-tags"></a>Azure Firewall-Taggar

Om du använder Azure-brandväggen kan du skapa en program regel med hjälp av *AzureBackup* [Azure Firewall FQDN-taggen](../firewall/fqdn-tags.md). Detta ger all utgående åtkomst till Azure Backup.

### <a name="allow-access-to-service-ip-ranges"></a>Tillåt åtkomst till tjänstens IP-intervall

Om du väljer att tillåta IP-adresser för åtkomst kan du se IP-intervallen i JSON-filen som finns [här](https://www.microsoft.com/download/confirmation.aspx?id=56519). Du måste tillåta åtkomst till IP-adresser som motsvarar Azure Backup, Azure Storage och Azure Active Directory.

### <a name="allow-access-to-service-fqdns"></a>Tillåt åtkomst till tjänstens FQDN

Du kan också använda följande fullständiga domän namn för att ge åtkomst till de nödvändiga tjänsterna från dina servrar:

| Tjänst    | Domän namn som ska nås                             |
| -------------- | ------------------------------------------------------------ |
| Azure Backup  | `*.backup.windowsazure.com`                             |
| Azure Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | Tillåt åtkomst till FQDN i avsnitten 56 och 59 enligt [den här artikeln](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) |

### <a name="use-an-http-proxy-server-to-route-traffic"></a>Använda en HTTP-proxyserver för att dirigera trafik

När du säkerhetskopierar en SAP HANA databas som körs på en virtuell Azure-dator använder säkerhets kopierings tillägget på den virtuella datorn HTTPS-API: er för att skicka hanterings kommandon till Azure Backup och data till Azure Storage. Säkerhets kopierings tillägget använder också Azure AD för autentisering. Dirigera trafiken för säkerhetskopieringstillägget för dessa tre tjänster via HTTP-proxyn. Använd listan över IP-adresser och FQDN: er som nämns ovan för att tillåta åtkomst till de nödvändiga tjänsterna. Autentiserade proxyservrar stöds inte.

## <a name="understanding-backup-and-restore-throughput-performance"></a>Förstå prestanda för säkerhets kopiering och återställning av data flöde

Säkerhets kopiorna (logg och icke-logg) i SAP HANA virtuella Azure-datorer som tillhandahålls via Backint är strömmar till Azure Recovery Services-valv och är därför viktigt att förstå den här strömnings metoden.

Backint-komponenten i HANA ger "pipes" (en pipe för att läsa från och en pipe att skriva till), ansluten till underliggande diskar där databasfilerna finns, som sedan läses av Azure Backup tjänsten och transporteras till Azure Recovery Services-valvet. I Azure Backups tjänsten utförs även en kontroll summa för att verifiera data strömmarna, utöver de inbyggda verifierings kontrollerna i backint. Dessa verifieringar ser till att de data som finns i Azure Recovery Services-valvet verkligen är pålitliga och kan återskapas.

Eftersom data strömmar främst hanterar diskar måste du förstå disk prestanda för att mäta säkerhets kopierings-och återställnings prestanda. I [den här artikeln](../virtual-machines/disks-performance.md) hittar du en djupgående förståelse för disk data flöde och prestanda i virtuella Azure-datorer. Dessa gäller också för säkerhets kopierings-och återställnings prestanda.

**Azure backups tjänsten försöker uppnå upp till ~ 420 Mbit/s för säkerhets kopieringar som inte är loggade (till exempel fullständig, differentiell och stegvis) och upp till 100 Mbit/s för logg säkerhets kopior för Hana**. Som nämnts ovan garanterar inte dessa hastigheter och är beroende av följande faktorer:

* Högsta antal cachelagrade disk data flöden för den virtuella datorn
* Underliggande disk typ och dess data flöde
* Antalet processer som försöker läsa och skriva till samma disk på samma tid.

> [!IMPORTANT]
> I mindre virtuella datorer, där det cachelagrade disk data flödet är mycket nära eller mindre än 400 Mbit/s, kan du behöva oroa dig för att hela disken som används av säkerhets kopierings tjänsten förbrukas av säkerhets kopierings tjänsten som kan påverka SAP HANA åtgärder som rör läsning/skrivning från diskarna. I så fall kan du se nästa avsnitt om du vill begränsa eller begränsa förbrukningen av säkerhets kopierings tjänsten till max gränsen.

### <a name="limiting-backup-throughput-performance"></a>Begränsa prestanda för data flöde för säkerhets kopiering

Om du vill begränsa disk-förbrukningen för säkerhets kopierings tjänsten till ett högsta värde utför du följande steg.

1. Gå till mappen "opt/msawb/bin"
2. Skapa en ny JSON-fil med namnet "ExtensionSettingOverrides.JSpå"
3. Lägg till ett nyckel/värde-par i JSON-filen enligt följande:

    ```json
    {
    "MaxUsableVMThroughputInMBPS": 200
    }
    ```

4. Ändra behörighet och ägarskap för filen på följande sätt:
    
    ```bash
    chmod 750 ExtensionSettingsOverrides.json
    chown root:msawb ExtensionSettingsOverrides.json
    ```

5. Ingen omstart krävs för någon tjänst. Azure Backups tjänsten försöker begränsa data flödes prestandan som anges i den här filen.

## <a name="what-the-pre-registration-script-does"></a>Vad skriptet för för registrering gör

Att köra skriptet för för registrering utför följande funktioner:

* Baserat på din Linux-distribution installerar eller uppdaterar skriptet alla nödvändiga paket som krävs av Azure Backup agenten.
* Den utför utgående nätverks anslutnings kontroller med Azure Backup servrar och beroende tjänster som Azure Active Directory och Azure Storage.
* Den loggar in i HANA-systemet med hjälp av användar nyckeln som anges som en del av [förutsättningarna](#prerequisites). Användar nyckeln används för att skapa en säkerhets kopierings användare (AZUREWLBACKUPHANAUSER) i HANA-systemet och **användar nyckeln kan tas bort när skriptet för för registrering har körts**.
* AZUREWLBACKUPHANAUSER har tilldelats följande obligatoriska roller och behörigheter:
  * För MDC: databas administratör och säkerhets kopierings administratör (från HANA 2,0 SPS05 och senare): om du vill skapa nya databaser under återställningen.
  * För SDC: säkerhets kopierings administratör: om du vill skapa nya databaser under återställningen.
  * Katalog läsning: för att läsa säkerhets kopierings katalogen.
  * SAP_INTERNAL_HANA_SUPPORT: för att få åtkomst till några privata tabeller. Krävs endast för SDC-och MDC-versionerna under HANA 2,0 SPS04 rev 46. Detta krävs inte för HANA 2,0 SPS04 rev 46 och senare eftersom vi hämtar nödvändig information från offentliga tabeller nu med korrigeringen från HANA-teamet.
* Skriptet lägger till en nyckel till **hdbuserstore** för AZUREWLBACKUPHANAUSER för plugin-programmet Hana backup för att hantera alla åtgärder (databas frågor, återställnings åtgärder, konfigurera och köra säkerhets kopiering).

>[!NOTE]
> Du kan uttryckligen skicka användar nyckeln som visas som en del av [förutsättningarna](#prerequisites) som en parameter till för registrerings skriptet: `-sk SYSTEM_KEY_NAME, --system-key SYSTEM_KEY_NAME` <br><br>
>Om du vill veta vilka andra parametrar som skriptet accepterar använder du kommandot `bash msawb-plugin-config-com-sap-hana.sh --help`

Bekräfta att nyckeln skapas genom att köra kommandot HDBSQL på datorn HANA med SIDADM-autentiseringsuppgifter:

```hdbsql
hdbuserstore list
```

Kommandots utdata ska Visa nyckeln {SID} {DBNAME}, där användaren visas som AZUREWLBACKUPHANAUSER.

>[!NOTE]
> Se till att du har en unik uppsättning SSFS-filer under `/usr/sap/{SID}/home/.hdb/` . Det får bara finnas en mapp i den här sökvägen.

Här är en sammanfattning av de steg som krävs för att köra skriptet för för registrering.

|Vem  |Från  |Vad som ska köras  |Kommentarer  |
|---------|---------|---------|---------|
|```<sid>```ADM (OS)     |  HANA OS       |   Läs självstudie och hämta skript för för registrering      |   Läs för [hands kraven ovan](#prerequisites)    Ladda ned skript för för [registrering härifrån](https://aka.ms/scriptforpermsonhana)  |
|```<sid>```ADM (OS) och SYSTEM användare (HANA)    |      HANA OS   |   Kör kommandot hdbuserstore set      |   t. ex. hdbuserstore ange SYSTEM värdnamn>:3 ```<Instance#>``` 13 system ```<password>``` **Obs:**  se till att använda hostname i stället för IP-adress eller fullständigt domän namn      |
|```<sid>```ADM (OS)    |   HANA OS      |  Kör kommandot hdbuserstore List       |   Kontrol lera om resultatet innehåller standard arkivet som nedan: ```KEY SYSTEM  ENV : <hostname>:3<Instance#>13  USER: SYSTEM```      |
|Rot (OS)     |   HANA OS        |    Kör Azure Backup HANA för registrerings skript      |    ```./msawb-plugin-config-com-sap-hana.sh -a --sid <SID> -n <Instance#> --system-key SYSTEM```     |
|```<sid>```ADM (OS)    |  HANA OS       |   Kör kommandot hdbuserstore List      |    Kontrol lera om resultatet innehåller nya rader enligt nedan:  ```KEY AZUREWLBACKUPHANAUSER  ENV : localhost: 3<Instance#>13   USER: AZUREWLBACKUPHANAUSER```     |

När skriptet har körts och verifierats kan du fortsätta med att kontrol lera [anslutnings kraven](#set-up-network-connectivity) och sedan [Konfigurera säkerhets kopiering](#discover-the-databases) från Recovery Services-valvet

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Ett Recovery Services-valv är en entitet som lagrar säkerhets kopior och återställnings punkter som skapats med tiden. Recovery Services valvet innehåller också de säkerhets kopierings principer som är associerade med de skyddade virtuella datorerna.

Så här skapar du ett Recovery Services-valv:

1. Logga in på din prenumeration i [Azure-portalen](https://portal.azure.com/).

2. Välj **alla tjänster** på den vänstra menyn

   ![Välj Alla tjänster](./media/tutorial-backup-sap-hana-db/all-services.png)

3. I dialogrutan **Alla tjänster** anger du **Recovery Services**. Listan med resurser filtreras enligt din inmatning. I resurslistan väljer du **Recovery Services-valv**.

   ![Välj Recovery Services valv](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. På instrument panelen för **Recovery Services** valv väljer du **Lägg till**.

   ![Lägg till Recovery Services valv](./media/tutorial-backup-sap-hana-db/add-vault.png)

   Dialogrutan **Recovery Services-valv** öppnas. Ange värden för **namn, prenumeration, resurs grupp** och **plats**

   ![Skapa Recovery Services-valv](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * **Namn**: namnet används för att identifiera Recovery Services valvet och måste vara unikt för Azure-prenumerationen. Ange ett namn som innehåller minst två, men högst 50 tecken. Namnet måste börja med en bokstav och får endast innehålla bokstäver, siffror och bindestreck. I den här självstudien har vi använt namnet **SAPHanaVault**.
   * **Prenumeration**: Välj den prenumeration som ska användas. Om du bara är medlem i en prenumeration ser du det namnet. Om du inte är säker på vilken prenumeration du ska använda, använder du standardprenumerationen (den föreslagna). Du kan bara välja mellan flera alternativ om ditt arbets- eller skolkonto är associerat med mer än en Azure-prenumeration. Här har vi använt prenumerations prenumerationen för **SAP HANA Solution Lab** .
   * **Resursgrupp**: Använd en befintlig resursgrupp eller skapa en ny. Här har vi använt **SAPHANADemo**.<br>
   Om du vill se en lista över tillgängliga resurs grupper i din prenumeration väljer du **Använd befintlig** och väljer sedan en resurs i list rutan. Skapa en ny resursgrupp genom att välja **Skapa ny** och ange namnet. Fullständig information om resurs grupper finns i [Azure Resource Manager översikt](../azure-resource-manager/management/overview.md).
   * **Plats**: Välj ett geografiskt område för valvet. Valvet måste finnas i samma region som den virtuella datorn som kör SAP HANA. Vi har använt **USA, östra 2**.

5. Välj **Granska + skapa**.

   ![Välj granska & skapa](./media/tutorial-backup-sap-hana-db/review-create.png)

Recovery Servicess valvet har nu skapats.

## <a name="discover-the-databases"></a>Identifiera databaserna

1. I valvet i **komma igång** väljer du **säkerhets kopiering**. I **var kör din arbets belastning?** väljer du **SAP HANA i virtuell Azure-dator**.
2. Välj **Starta identifiering**. Detta initierar identifiering av oskyddade virtuella Linux-datorer i valv regionen. Du ser den virtuella Azure-datorn som du vill skydda.
3. I **välj Virtual Machines** väljer du länken för att ladda ned skriptet som ger behörighet till tjänsten Azure Backup för att få åtkomst till de SAP HANA virtuella datorerna för databas identifiering.
4. Kör skriptet på den virtuella dator som är värd för SAP HANA databas (er) som du vill säkerhetskopiera.
5. När du har kört skriptet på den virtuella datorn väljer du den virtuella datorn i **välj Virtual Machines**. Välj sedan **identifiera databaser**.
6. Azure Backup identifierar alla SAP HANA-databaser på den virtuella datorn. Under identifieringen registrerar Azure Backup den virtuella datorn med valvet och installerar ett tillägg på den virtuella datorn. Ingen agent är installerad på databasen.

   ![Identifiera databaserna](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>Konfigurera säkerhetskopiering

Nu när de databaser som vi vill säkerhetskopiera identifieras är det dags att aktivera säkerhets kopiering.

1. Välj **Konfigurera säkerhets kopiering**.

   ![Konfigurera säkerhetskopiering](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. I **Välj objekt att säkerhetskopiera** väljer du en eller flera databaser som du vill skydda och väljer sedan **OK**.

   ![Välj objekt som ska säkerhets kopie ras](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. I **säkerhets kopierings policy > väljer du säkerhets kopierings princip**, skapar en ny säkerhets kopierings princip för databaserna, i enlighet med anvisningarna i nästa avsnitt.

   ![Välj säkerhets kopierings princip](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. När du har skapat principen väljer du **Aktivera säkerhets kopiering** på **menyn säkerhets kopiering**.

   ![Välj Aktivera säkerhets kopiering](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. Spåra förloppet för säkerhets kopierings konfigurationen i området **meddelanden** i portalen.

## <a name="creating-a-backup-policy"></a>Skapa en säkerhets kopierings princip

En säkerhets kopierings princip definierar när säkerhets kopieringar görs och hur länge de ska behållas.

* En policy skapas på valvnivå.
* Flera valv kan använda samma säkerhetskopieringspolicy, men du måste tillämpa säkerhetskopieringspolicyn på varje valv.

Ange princip inställningarna enligt följande:

1. I **Policynamn** anger du ett namn för den nya policyn. I det här fallet anger du **SAPHANA**.

   ![Ange namn för ny princip](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. Välj en **säkerhets kopierings frekvens** i **fullständig säkerhets kopierings princip**. Du kan välja **varje dag** eller **varje vecka**. I den här självstudien valde vi den **dagliga** säkerhets kopieringen.

   ![Välj en säkerhets kopierings frekvens](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. Konfigurera inställningar för kvarhållning för fullständig säkerhets kopiering i **kvarhållningsintervall**.
   * Som standard är alla alternativ markerade. Rensa eventuella gränser för kvarhållningsintervall som du inte vill använda och ange de som du gör.
   * Den minsta Retentions perioden för någon typ av säkerhets kopiering (fullständig/differentiell/log) är sju dagar.
   * Återställningspunkter taggas för kvarhållning baserat på deras kvarhållningsintervall. Om du till exempel väljer en daglig fullständig säkerhetskopia utlöses endast en fullständig säkerhetskopia varje dag.
   * Säkerhets kopian för en angiven dag märks och bevaras baserat på vecko kvarhållningsintervallet och-inställningen.
   * De månatliga och årliga kvarhållningsintervallen fungerar på liknande sätt.
4. På menyn **Policy för fullständig säkerhetskopia** väljer du **OK** för att acceptera inställningarna.
5. Välj sedan **differentiell säkerhets kopiering** för att lägga till en differentiell princip.
6. I **Policy för differentiell säkerhetskopia** väljer du **Aktivera** för att öppna kontrollerna för frekvens och kvarhållning. Vi har aktiverat en differentiell säkerhets kopia varje **söndag** kl. **2:00**, som sparas i **30 dagar**.

   ![Princip för differentiell säkerhets kopiering](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >Du kan välja antingen en differentiell eller en ökning som en daglig säkerhets kopia, men inte båda.

7. I **principen för stegvis säkerhets kopiering** väljer du **Aktivera** för att öppna kontrollerna frekvens och kvarhållning.
    * Högst kan du utlösa en stegvis säkerhets kopiering per dag.
    * Stegvisa säkerhets kopieringar kan behållas i högst 180 dagar. Om du behöver längre kvarhållning måste du använda fullständiga säkerhetskopior.

    ![Princip för stegvis säkerhets kopiering](./media/backup-azure-sap-hana-database/incremental-backup-policy.png)

8. Välj **OK** för att spara policyn och återgå till huvudmenyn **Säkerhetskopieringspolicy**.
9. Välj **logg säkerhets kopiering** för att lägga till en transaktions logg princip för säkerhets kopiering
   * **Logg säkerhets kopiering** är som standard inställt på **aktive** rad. Detta kan inte inaktive ras eftersom SAP HANA hanterar alla logg säkerhets kopior.
   * Vi har angett **2 timmar** som säkerhets kopierings schema och **15 dagars** kvarhållningsperiod.

    ![Logg säkerhets kopierings princip](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > Logg säkerhets kopior börjar bara att flyta efter en lyckad fullständig säkerhets kopiering har slutförts.
   >

10. Välj **OK** för att spara policyn och återgå till huvudmenyn **Säkerhetskopieringspolicy**.
11. När du är klar med att definiera säkerhets kopierings principen väljer du **OK**.

Du har nu konfigurerat säkerhets kopiering (er) för SAP HANA databas (er).

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [Kör säkerhets kopiering på begäran på SAP HANA databaser som körs på virtuella Azure-datorer](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* Lär dig hur du [återställer SAP HANA databaser som körs på virtuella Azure-datorer](sap-hana-db-restore.md)
* Lär dig hur du [hanterar SAP HANA databaser som säkerhets kopie ras med Azure Backup](sap-hana-db-manage.md)
* Lär dig hur du [felsöker vanliga problem när du säkerhetskopierar SAP HANA databaser](backup-azure-sap-hana-database-troubleshoot.md)