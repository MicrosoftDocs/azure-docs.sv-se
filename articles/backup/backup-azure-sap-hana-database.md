---
title: Säkerhetskopiera en SAP HANA-databas till Azure med Azure Backup
description: I den här artikeln lär du dig hur du säkerhetskopierar en SAP HANA-databas till virtuella Azure-datorer med tjänsten Azure Backup.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: e7735c4240529cc6fc9bb6470934dd335d22aa77
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101719618"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Säkerhetskopiera SAP HANA-databaser i virtuella Azure-datorer

SAP HANA-databaser är kritiska arbets belastningar som kräver återställnings punkt mål och långsiktig kvarhållning. Du kan säkerhetskopiera SAP HANA databaser som körs på virtuella datorer i Azure med hjälp av [Azure Backup](backup-overview.md).

Den här artikeln visar hur du säkerhetskopierar SAP HANA databaser som körs på virtuella Azure-datorer till ett Azure Backup Recovery Services-valv.

I den här artikeln får du lära dig att:
> [!div class="checklist"]
>
> * Skapa och konfigurera ett valv
> * Identifiera databaser
> * Konfigurera säkerhets kopior
> * Köra ett säkerhets kopierings jobb på begäran

>[!NOTE]
>Från och med den 1 augusti 2020 är SAP HANA säkerhets kopiering för RHEL (7,4, 7,6, 7,7 & 8,1) allmänt tillgänglig.

>[!NOTE]
>**Mjuk borttagning för SQL Server i Azure VM och mjuk borttagning för SAP HANA i Azure VM-arbetsbelastningar** finns nu i för hands version.<br>
>Registrera dig för för hands versionen genom att skriva till oss på [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="prerequisites"></a>Förutsättningar

Se [kraven](tutorial-backup-sap-hana-db.md#prerequisites) och [Vad skriptet för för registrering gör](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) i avsnitten för att konfigurera databasen för säkerhets kopiering.

### <a name="establish-network-connectivity"></a>Etablera nätverksanslutning

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

#### <a name="private-endpoints"></a>Privata slutpunkter

Med privata slut punkter kan du ansluta säkert från servrar i ett virtuellt nätverk till ditt Recovery Services-valv. Den privata slut punkten använder en IP-adress från VNET-adressutrymmet för ditt valv. Nätverks trafiken mellan resurserna i det virtuella nätverket och valvet överförs över ditt virtuella nätverk och en privat länk i Microsoft stamnät nätverket. Detta eliminerar exponeringen från det offentliga Internet. Läs mer om privata slut punkter för Azure Backup [här](./private-endpoints.md).

#### <a name="nsg-tags"></a>NSG-Taggar

Om du använder nätverks säkerhets grupper (NSG) använder du tjänst tag gen *AzureBackup* för att tillåta utgående åtkomst till Azure Backup. Förutom taggen Azure Backup måste du också tillåta anslutning för autentisering och data överföring genom att skapa liknande [NSG-regler](../virtual-network/network-security-groups-overview.md#service-tags) för Azure AD (*AzureActiveDirectory*) och Azure Storage (*lagring*).  Följande steg beskriver processen för att skapa en regel för taggen Azure Backup:

1. I **alla tjänster** går du till **nätverks säkerhets grupper** och väljer Nätverks säkerhets gruppen.

1. Välj **utgående säkerhets regler** under **Inställningar**.

1. Välj **Lägg till**. Ange all information som krävs för att skapa en ny regel enligt beskrivningen i [säkerhets regel inställningar](../virtual-network/manage-network-security-group.md#security-rule-settings). Se till att alternativet **destination** har angetts till *service tag* och **mål tjänst tag gen** är inställt på *AzureBackup*.

1. Välj **Lägg till**  för att spara den nyligen skapade utgående säkerhets regeln.

Du kan också skapa NSG utgående säkerhets regler för Azure Storage och Azure AD. Mer information om service märken finns i [den här artikeln](../virtual-network/service-tags-overview.md).

#### <a name="azure-firewall-tags"></a>Azure Firewall-Taggar

Om du använder Azure-brandväggen kan du skapa en program regel med hjälp av *AzureBackup* [Azure Firewall FQDN-taggen](../firewall/fqdn-tags.md). Detta ger all utgående åtkomst till Azure Backup.

#### <a name="allow-access-to-service-ip-ranges"></a>Tillåt åtkomst till tjänstens IP-intervall

Om du väljer att tillåta IP-adresser för åtkomst kan du se IP-intervallen i JSON-filen som finns [här](https://www.microsoft.com/download/confirmation.aspx?id=56519). Du måste tillåta åtkomst till IP-adresser som motsvarar Azure Backup, Azure Storage och Azure Active Directory.

#### <a name="allow-access-to-service-fqdns"></a>Tillåt åtkomst till tjänstens FQDN

Du kan också använda följande fullständiga domän namn för att ge åtkomst till de nödvändiga tjänsterna från dina servrar:

| Tjänst    | Domän namn som ska nås                             |
| -------------- | ------------------------------------------------------------ |
| Azure Backup  | `*.backup.windowsazure.com`                             |
| Azure Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | Tillåt åtkomst till FQDN i avsnitten 56 och 59 enligt [den här artikeln](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) |

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>Använda en HTTP-proxyserver för att dirigera trafik

När du säkerhetskopierar en SAP HANA databas som körs på en virtuell Azure-dator använder säkerhets kopierings tillägget på den virtuella datorn HTTPS-API: er för att skicka hanterings kommandon till Azure Backup och data till Azure Storage. Säkerhets kopierings tillägget använder också Azure AD för autentisering. Dirigera trafiken för säkerhetskopieringstillägget för dessa tre tjänster via HTTP-proxyn. Använd listan över IP-adresser och FQDN: er som nämns ovan för att tillåta åtkomst till de nödvändiga tjänsterna. Autentiserade proxyservrar stöds inte.

> [!NOTE]
> Det finns inget stöd för service nivå proxy. Det innebär att trafik via proxyn från bara några få eller valda tjänster (Azure Backup Services) inte stöds. Hela data eller trafik kan antingen dirigeras via proxy eller inte.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Identifiera databaserna

1. I valvet i **komma igång** väljer du **säkerhets kopiering**. I **var kör din arbets belastning?** väljer du **SAP HANA i virtuell Azure-dator**.
2. Välj **Starta identifiering**. Detta initierar identifiering av oskyddade virtuella Linux-datorer i valv regionen.

   * Efter identifieringen visas oskyddade virtuella datorer i portalen, listade efter namn och resurs grupp.
   * Om en virtuell dator inte visas som förväntat kontrollerar du om den redan har säkerhetskopierats i ett valv.
   * Flera virtuella datorer kan ha samma namn, men de tillhör olika resurs grupper.

3. I **välj Virtual Machines** väljer du länken för att ladda ned skriptet som ger behörighet till tjänsten Azure Backup för att få åtkomst till de SAP HANA virtuella datorerna för databas identifiering.
4. Kör skriptet på varje virtuell dator som är värd för SAP HANA databaser som du vill säkerhetskopiera.
5. När skriptet har körts på de virtuella datorerna väljer du de virtuella datorerna i **välj Virtual Machines**. Välj sedan **identifiera databaser**.
6. Azure Backup identifierar alla SAP HANA-databaser på den virtuella datorn. Under identifieringen registrerar Azure Backup den virtuella datorn med valvet och installerar ett tillägg på den virtuella datorn. Ingen agent är installerad på databasen.

    ![Identifiera SAP HANA databaser](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Konfigurera säkerhetskopiering  

Aktivera nu säkerhets kopiering.

1. I steg 2 väljer du **Konfigurera säkerhets kopiering**.

    ![Konfigurera säkerhetskopiering](./media/backup-azure-sap-hana-database/configure-backup.png)
2. I **Välj objekt att säkerhetskopiera** väljer du alla databaser som du vill skydda > **OK**.

    ![Välj objekt som ska säkerhets kopie ras](./media/backup-azure-sap-hana-database/select-items.png)
3. I **säkerhets kopierings policy**  >  **väljer du säkerhets kopierings princip**, skapar en ny säkerhets kopierings princip för databaserna enligt anvisningarna nedan.

    ![Välj säkerhets kopierings princip](./media/backup-azure-sap-hana-database/backup-policy.png)
4. När du har skapat principen väljer du **Aktivera säkerhets kopiering** på menyn **säkerhets kopiering** .

    ![Aktivera säkerhets kopiering](./media/backup-azure-sap-hana-database/enable-backup.png)
5. Spåra förloppet för säkerhets kopierings konfigurationen i området **meddelanden** i portalen.

### <a name="create-a-backup-policy"></a>Skapa en säkerhetskopieringspolicy

En säkerhets kopierings princip definierar när säkerhets kopieringar görs och hur länge de ska behållas.

* En policy skapas på valvnivå.
* Flera valv kan använda samma säkerhetskopieringspolicy, men du måste tillämpa säkerhetskopieringspolicyn på varje valv.

>[!NOTE]
>Azure Backup anpassas inte automatiskt för sommar tids ändringar vid säkerhets kopiering av en SAP HANA databas som körs på en virtuell Azure-dator.
>
>Ändra principen manuellt efter behov.

Ange princip inställningarna enligt följande:

1. I **Policynamn** anger du ett namn för den nya policyn.

   ![Ange princip namn](./media/backup-azure-sap-hana-database/policy-name.png)
1. I **Policy för fullständig säkerhetskopia** väljer du en **Säkerhetskopieringsfrekvens** och väljer **Dagligen** eller **Varje vecka**.
   * **Varje dag**: Välj den timme och den tidszon som säkerhets kopierings jobbet ska starta.
       * Du måste köra en fullständig säkerhets kopiering. Du kan inte inaktivera det här alternativet.
       * Klicka på **Fullständig säkerhetskopia** för att se principen.
       * Du kan inte skapa differentiella säkerhetskopior för dagliga fullständiga säkerhetskopior.
   * **Varje vecka**: Välj den veckodag, timme och tidszon som säkerhets kopierings jobbet körs i.

   ![Välj säkerhets kopierings frekvens](./media/backup-azure-sap-hana-database/backup-frequency.png)

1. Konfigurera inställningar för kvarhållning för fullständig säkerhets kopiering i **kvarhållningsintervall**.
    * Som standard är alla alternativ markerade. Rensa eventuella gränser för kvarhållningsintervall som du inte vill använda och ange de som du gör.
    * Den minsta Retentions perioden för någon typ av säkerhets kopiering (fullständig/differentiell/log) är sju dagar.
    * Återställningspunkter taggas för kvarhållning baserat på deras kvarhållningsintervall. Om du till exempel väljer en daglig fullständig säkerhetskopia utlöses endast en fullständig säkerhetskopia varje dag.
    * Säkerhets kopian för en angiven dag märks och bevaras baserat på vecko kvarhållningsintervallet och-inställningen.
    * De månatliga och årliga kvarhållningsintervallen fungerar på liknande sätt.

1. På menyn **Policy för fullständig säkerhetskopia** väljer du **OK** för att acceptera inställningarna.
1. Välj **differentiell säkerhets kopiering** för att lägga till en differentiell princip.
1. I **Policy för differentiell säkerhetskopia** väljer du **Aktivera** för att öppna kontrollerna för frekvens och kvarhållning.
    * Du kan endast utlösa en differentiell säkerhetskopia per dag.
    * Differentiella säkerhetskopior kan behållas i upp till 180 dagar. Om du behöver längre kvarhållning måste du använda fullständiga säkerhetskopior.

    ![Princip för differentiell säkerhets kopiering](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > Du kan välja antingen en differentiell eller en ökning som en daglig säkerhets kopia, men inte båda.
1. I **principen för stegvis säkerhets kopiering** väljer du **Aktivera** för att öppna kontrollerna frekvens och kvarhållning.
    * Högst kan du utlösa en stegvis säkerhets kopiering per dag.
    * Stegvisa säkerhets kopieringar kan behållas i högst 180 dagar. Om du behöver längre kvarhållning måste du använda fullständiga säkerhetskopior.

    ![Princip för stegvis säkerhets kopiering](./media/backup-azure-sap-hana-database/incremental-backup-policy.png)

1. Välj **OK** för att spara policyn och återgå till huvudmenyn **Säkerhetskopieringspolicy**.
1. Välj **logg säkerhets kopiering** för att lägga till en transaktions logg princip för säkerhets kopiering
    * I **logg säkerhets kopiering** väljer du **Aktivera**.  Detta kan inte inaktive ras eftersom SAP HANA hanterar alla logg säkerhets kopior.
    * Ange frekvens och bevarande kontroller.

    > [!NOTE]
    > Logg säkerhets kopior börjar bara att flyta efter en lyckad fullständig säkerhets kopiering har slutförts.

1. Välj **OK** för att spara policyn och återgå till huvudmenyn **Säkerhetskopieringspolicy**.
1. När du är klar med att definiera säkerhets kopierings principen väljer du **OK**.

> [!NOTE]
> Varje logg säkerhets kopia länkas till den tidigare fullständiga säkerhets kopian för att skapa en återställnings kedja. Den fullständiga säkerhets kopian kommer att behållas tills kvarhållning av den senaste logg säkerhets kopian har upphört att gälla. Detta kan betyda att den fullständiga säkerhets kopian behålls under en längre period för att se till att alla loggar kan återställas. Vi antar att en användare har en veckovis fullständig säkerhets kopiering, dagliga differentiella och 2 timmars loggar. Alla finns kvar i 30 dagar. Men den fullständiga veckan kan bara rensas/tas bort efter att nästa fullständiga säkerhets kopiering är tillgängligt, det vill säga efter 30 + 7 dagar. Till exempel sker en fullständig veckovis fullständig säkerhets kopiering på den 16 november. Enligt bevarande principen bör den behållas tills den 16 december. Den senaste logg säkerhets kopian för denna fullständiga aktivitet sker före nästa schemalagda, på Nov 22. Tills den här loggen är tillgänglig fram till Dec 22, kan den sextonde sexton fullständiga filen inte tas bort. Därför behålls den sextonde sexton fullständiga till och med Dec 22.

## <a name="run-an-on-demand-backup"></a>Köra en säkerhetskopiering på begäran

Säkerhets kopieringar körs enligt princip schemat. Du kan köra en säkerhets kopiering på begäran på följande sätt:

1. I menyn valv väljer du **säkerhets kopierings objekt**.
2. I **säkerhets kopierings objekt** väljer du den virtuella dator som kör SAP HANA databasen och väljer sedan **Säkerhetskopiera nu**.
3. I **Säkerhetskopiera nu** väljer du vilken typ av säkerhets kopiering du vill utföra. Välj sedan **OK**. Den här säkerhets kopian kommer att behållas enligt principen som är kopplad till det här säkerhets kopierings objektet.
4. Övervaka Portal meddelanden. Du kan övervaka jobb förloppet i valv instrument panelen > **säkerhets kopierings jobb**  >  **pågår**. Det kan ta en stund att skapa den första säkerhets kopieringen, beroende på databasens storlek.

Som standard är kvarhållning av säkerhets kopior på begäran 45 dagar.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Köra säkerhets kopiering av SAP HANA Studio på en databas med Azure Backup aktiverat

Om du vill ta en lokal säkerhets kopia (med HANA Studio) av en databas som säkerhets kopie ras med Azure Backup gör du följande:

1. Vänta tills alla säkerhets kopieringar eller loggar för databasen har slutförts. Kontrol lera statusen i SAP HANA Studio/cockpit.
1. Inaktivera logg säkerhets kopior och ange säkerhets kopierings katalogen till fil systemet för relevant databas.
1. Det gör du genom att dubbelklicka på **systemdb**  >  **konfiguration**  >  **Välj databas**  >  **filter (logg)**.
1. Ange **enable_auto_log_backup** till **Nej**.
1. Ange **log_backup_using_backint** till **false**.
1. Ange **catalog_backup_using_backint** till **false**.
1. Utför en fullständig säkerhets kopiering på begäran av databasen.
1. Vänta tills den fullständiga säkerhets kopieringen och katalog säkerhets kopieringen har slutförts.
1. Återställ tidigare inställningar tillbaka till dem för Azure:
    * Ange **enable_auto_log_backup** till **Ja**.
    * Ange **log_backup_using_backint** till **Sant**.
    * Ange **catalog_backup_using_backint** till **Sant**.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [återställer SAP HANA databaser som körs på virtuella Azure-datorer](./sap-hana-db-restore.md)
* Lär dig hur du [hanterar SAP HANA databaser som säkerhets kopie ras med Azure Backup](./sap-hana-db-manage.md)
