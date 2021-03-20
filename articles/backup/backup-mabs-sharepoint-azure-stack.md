---
title: Säkerhetskopiera en SharePoint-grupp på Azure Stack
description: Använd Azure Backup Server för att säkerhetskopiera och återställa dina SharePoint-data på Azure Stack. Den här artikeln innehåller information om hur du konfigurerar SharePoint-servergruppen så att önskade data kan lagras i Azure. Du kan återställa skyddade SharePoint-data från disk eller från Azure.
ms.topic: conceptual
ms.date: 06/07/2020
ms.openlocfilehash: 1e237e63b92468fafff4f8f8f525d1388840d162
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89378329"
---
# <a name="back-up-a-sharepoint-farm-on-azure-stack"></a>Säkerhetskopiera en SharePoint-grupp på Azure Stack

Du säkerhetskopierar en SharePoint-grupp på Azure Stack till Microsoft Azure genom att använda Microsoft Azure Backup Server (MABS) på ett mycket sätt som du säkerhetskopierar andra data källor. Azure Backup ger flexibilitet i schemat för säkerhets kopiering för att skapa dagliga, vecko Visa eller årliga säkerhets kopierings punkter och ger dig bevarande princip alternativ för olika säkerhets kopierings punkter. Det ger också möjlighet att lagra lokala disk kopior för snabba återställnings mål (RTO) och för att lagra kopior till Azure för ekonomisk, långsiktig kvarhållning.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>SharePoint-versioner som stöds och relaterade skydds scenarier

Azure Backup för MABS har stöd för följande scenarier:

| Arbetsbelastning | Version | SharePoint-distribution | Skydd och återställning |
| --- | --- | --- | --- |
| SharePoint |SharePoint 2016, SharePoint 2013, SharePoint 2010 |SharePoint distribuerad som en Azure Stack virtuell dator <br> -------------- <br> SQL AlwaysOn | Skydda SharePoint-gruppens återställnings alternativ: återställnings grupp, databas och fil-eller List objekt från disk återställnings punkter.  Server grupp och databas återställning från Azure-återställnings punkter. |

## <a name="before-you-start"></a>Innan du börjar

Det finns några saker du behöver bekräfta innan du säkerhetskopierar en SharePoint-servergrupp till Azure.

### <a name="whats-not-supported"></a>Vad som inte stöds

* MABS som skyddar en SharePoint-grupp skyddar inte Sök index eller program tjänst databaser. Du måste konfigurera skyddet av dessa databaser separat.

* MABS tillhandahåller inte säkerhets kopior av SharePoint SQL Server-databaser som finns på SOFS-resurser (Scale-Out File Server).

### <a name="prerequisites"></a>Förutsättningar

Innan du fortsätter kontrollerar du att du uppfyller alla [krav för att använda Microsoft Azure Backup](backup-azure-dpm-introduction.md#prerequisites-and-limitations) för att skydda arbets belastningar. Vissa uppgifter för krav är: skapa ett säkerhets kopierings valv, Hämta autentiseringsuppgifter för valvet, installera Azure Backup Agent och registrera Azure Backup Server med valvet.

Ytterligare krav och begränsningar:

* Som standard när du skyddar SharePoint kommer alla innehålls databaser (och SharePoint_Config och SharePoint_AdminContent * databaser) att skyddas. Om du vill lägga till anpassningar som sökindex, mallar eller programtjänstdatabaser, eller användarprofiltjänsten måste du konfigurera skydd för dessa separat. Se till att du aktiverar skydd för alla mappar som innehåller dessa typer av funktioner eller anpassningsfiler.

* Du kan inte skydda SharePoint-databaser som en SQL Server-datakälla. Du kan återställa enskilda databaser från en säkerhetskopia i servergruppen.

* Kom ihåg att MABS körs som **lokalt system**, och för att säkerhetskopiera SQL Server-databaser måste den ha sysadmin-behörighet för det kontot för SQL Server. Ange NT instans\system som **sysadmin** på den SQL Server du vill säkerhetskopiera.

* För varje 10 000 000-objekt i Server gruppen måste det finnas minst 2 GB utrymme på den volym där MABS-mappen finns. Detta utrymme krävs för kataloggenerering. För att du ska kunna använda MABS för att utföra en viss återställning av objekt (webbplats samlingar, webbplatser, listor, dokument bibliotek, mappar, enskilda dokument och list objekt) skapar katalog skapande en lista över de URL: er som finns i varje innehålls databas. Du kan visa listan över URL: er i fönstret återställnings Bart objekt i återställnings aktivitets avsnittet i MABS Administratörskonsol.

* I SharePoint-servergruppen, om du har SQL Server databaser som är konfigurerade med SQL Server alias, installerar du SQL Server-klient komponenterna på den klient webb server som MABS ska skydda.

* Skydd av objekt i programarkivet stöds inte med SharePoint 2013.

* MABS stöder inte skydd av fjärr-FILESTREAM. FILESTREAM bör vara en del av databasen.

## <a name="configure-backup"></a>Konfigurera säkerhetskopiering

Om du vill säkerhetskopiera SharePoint-servergruppen konfigurerar du skyddet för SharePoint genom att använda ConfigureSharePoint.exe och sedan skapa en skydds grupp i MABS.

1. **Kör ConfigureSharePoint.exe** – det här verktyget konfigurerar tjänsten SharePoint VSS Writer \(WSS\) och förser skyddsagenten med autentiseringsuppgifter för SharePoint-servergruppen. När du har distribuerat skydds agenten hittar du ConfigureSharePoint.exe-filen i `<MABS Installation Path\>\bin` mappen på klient \- webb servern.  Om du har flera WFE-servrar behöver du bara installera den på en av dem. Kör så här:

    * På WFE-servern går du till kommando tolken och navigerar till `\<MABS installation location\>\\bin\\` och kör `ConfigureSharePoint \[\-EnableSharePointProtection\] \[\-EnableSPSearchProtection\] \[\-ResolveAllSQLAliases\] \[\-SetTempPath <path>\]` , där:

        * **EnableSharePointProtection** aktiverar skydd av SharePoint-servergruppen, aktiverar VSS-skrivaren och registrerar identiteten för DCOM-programmet WssCmdletsWrapper att köras som en användare vars autentiseringsuppgifter har angetts med det här alternativet. Det här kontot ska vara en administratör för servergruppen och lokal administratör på klientwebbservern.

        * EnableSPSearchProtection\\ aktiverar skydd av WSS 3.0 SP Search genom att använda registernyckeln SharePointSearchEnumerationEnabled under HKLM\\Software\\Microsoft\\ Microsoft Data Protection Manager\\Agent\\2.0\- på klientwebbservern och registrerar identiteten för DCOM-programmet WssCmdletsWrapper för körning som en användare vars autentiseringsuppgifter har angetts med det här alternativet. Det här kontot ska vara en administratör för servergruppen och lokal administratör på klientwebbservern.

        * **ResolveAllSQLAliases** visar alla alias som rapporteras av VSS-skrivaren för SharePoint och matchar dem mot motsvarande SQL-server. Dessutom visas matchade instansnamn. Om servrarna är speglade visas också den speglade servern. Den rapporterar alla alias som inte matchas till en SQL Server.

        * **SetTempPath** ställer in miljövariablerna TEMP och TMP på den angivna sökvägen. Återställning på objekt nivå Miss lyckas om en stor webbplats samling, webbplats, lista eller objekt återställs och det inte finns tillräckligt med utrymme i den tillfälliga mappen Server grupp administratör. Med det här alternativet kan du ändra tillfälliga filers mappsökväg till en volym som har tillräckligt med utrymme för lagring av den platssamling eller plats som återställs.

    * Ange administratörsbehörigheterna för servergruppen. Det här kontot ska vara medlem av den lokala administratörsgruppen på WFE-servern Om Server grupps administratören inte är en lokal administratör ska du bevilja följande behörigheter på WFE-servern:

        * Ge **WSS_Admin_WPGs** gruppen fullständig behörighet till mappen Mabs ( `%Program Files%\Data Protection Manager\DPM\` ).

        * Bevilja **WSS_Admin_WPGs** gruppen Läs behörighet till register nyckeln Mabs ( `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager` ).

        När du har kört ConfigureSharePoint.exe måste du köra om den om det finns en ändring i administratörs uppgifterna för SharePoint-servergruppen.

1. Om du vill skapa en skydds grupp väljer du **skydds**  >  **åtgärder**  >  **skapa skydds grupp** för att öppna guiden **Skapa ny skydds grupp** i Mabs-konsolen.

1. I **Välj typ av skydds grupp** väljer du **servrar**.

1. I **Välj grupp medlemmar** expanderar du den server som innehåller WFE-rollen. Om det finns fler än en WFE-server väljer du den som du installerade ConfigureSharePoint.exe på.

    När du expanderar MABS frågor för SharePoint Server VSS för att se vilka data MABS kan skydda.  Om SharePoint-databasen är fjärr anslutning MABS ansluts till den. Om SharePoint-datakällor inte visas kontrollerar du att VSS-skrivaren körs på SharePoint-servern och alla fjärrSQL Serverer och kontrollerar att MABS-agenten är installerad på både SharePoint-servern och fjärrSQL Server. Se också till att SharePoint-databaser inte skyddas på andra platser som SQL Server databaser.

1. I **Välj data skydds metod** anger du hur du vill hantera kort och långsiktig \- säkerhets kopiering. Kortsiktig \- säkerhets kopiering är alltid till disk först, med alternativet att säkerhetskopiera från disken till Azure-molnet med Azure Backup \( för kort eller lång \- sikt \) .

1. I **Välj kortsiktiga \- mål** anger du hur du vill säkerhetskopiera till kortsiktig \- lagring på disk.   I **kvarhållningsintervall** anger du hur länge du vill behålla data på disken. I **Synkroniseringsfrekvens** anger du hur ofta du vill köra en stegvis säkerhets kopiering på disk. Om du inte vill ange ett intervall för säkerhets kopiering kan du kontrol lera precis innan en återställnings punkt så att MABS kör en fullständig snabb säkerhets kopiering precis innan varje återställnings punkt schemaläggs.

1. På sidan Granska diskallokering granskar du allokerat disk utrymme för lagringspoolen för skydds gruppen.

    **Total data storlek** är storleken på de data som du vill säkerhetskopiera och **disk utrymme som ska tillhandahållas på Mabs** är det utrymme som Mabs rekommenderar för skydds gruppen. MABS väljer den ideala säkerhets kopierings volymen baserat på inställningarna. Du kan dock redigera valen av säkerhetskopieringsvolym under **Disk allocation details (Diskallokeringsdetaljer)**. Välj önskad lagringsplats för arbetsbelastningarna i den nedrullningsbara menyn. Redigeringarna ändrar värdena för **Totalt lagringsutrymme** och **Ledigt lagringsutrymme** i fönstret **Tillgängligt disklagringsutrymme**. Underetablerat utrymme är mängden lagrings MABS som föreslår att du lägger till volymen, för att fortsätta med säkerhets kopieringar smidigt i framtiden.

1. I **Välj metod för skapande av replik** väljer du hur du vill hantera den första fullständiga datareplikeringen.  Om du väljer att replikera över nätverket rekommenderar vi att du väljer en tid med låg belastning. Överväg att replikera data offline med hjälp av ett flyttbart medium vid stora mängder data eller bristfälliga nätverksförhållanden.

1. I **Välj alternativ för konsekvenskontroll** väljer du hur du vill automatisera konsekvenskontroller. Du kan aktivera att en kontroll endast körs när replikdata blir inkonsekvent, eller enligt ett schema. Om du inte vill konfigurera automatisk konsekvens kontroll kan du när som helst köra en manuell kontroll genom att högerklicka på skydds gruppen i **skydds** delen i Mabs-konsolen och välja **utför konsekvens kontroll**.

1. Om du har valt att säkerhetskopiera till molnet med Azure Backup ser du till att de arbetsbelastningar som du vill säkerhetskopiera till Azure är valda på sidan **Ange onlineskyddsdata**.

1. I **Ange schema för onlinesäkerhetskopiering** anger du hur ofta stegvisa säkerhets kopieringar ska ske i Azure. Du kan schemalägga säkerhetskopieringar så att de körs varje dag/vecka/månad/år och ange vilken tid/datum de ska köras. Säkerhetskopieringar kan göras upp till två gånger per dag. Varje gång en säkerhets kopiering körs skapas en data återställnings punkt i Azure från kopian av säkerhetskopierade data som lagras på MABS-disken.

1. I **Ange bevarande princip för onlinenivå** kan du ange hur de återställnings punkter som skapas med säkerhets kopiorna per dag/vecka/månad/år ska behållas i Azure.

1. I **Välj online-replikering**, anger du hur den första fullständiga replikeringen av data ska ske. Du kan replikera via nätverket eller göra en offlinesäkerhetskopiering (offlineseeding). Vid offlinesäkerhetskopiering används funktionen Azure Import. [Läs mer](./backup-azure-backup-import-export.md).

1. På sidan  **Sammanfattning** granskar du inställningarna. När du har valt **Skapa grupp** sker inledande replikering av data. När den är klar visas skydds gruppens status som **OK** på sidan **status** . Säkerhetskopieringen sker sedan i enlighet med skyddsgruppens inställningar.

## <a name="monitoring"></a>Övervakning

När skydds gruppen har skapats sker den inledande replikeringen och MABS börjar säkerhetskopiera och synkronisera SharePoint-data. MABS övervakar den första synkroniseringen och efterföljande säkerhets kopieringar.  Du kan övervaka SharePoint-data på ett par sätt:

* Med standard övervakning av MABS kan du konfigurera aviseringar för proaktiv övervakning genom att publicera aviseringar och konfigurera meddelanden. Du kan skicka e-postmeddelanden med kritiska aviseringar, varnings- eller informationsaviseringar och status för skapade instanser av återställningar.

* Om du använder Operations Manager kan du publicera aviseringar centralt.

### <a name="set-up-monitoring-notifications"></a>Konfigurera övervakningsaviseringar

1. I Mabs administratörskonsol väljer du **övervaknings**  >  **Åtgärds**  >  **alternativ**.

2. Välj **SMTP-server**, ange Server namn, port och e-postadress som meddelanden ska skickas från. Adressen måste vara giltig.

3. I **autentiserad SMTP-server** anger du ett användar namn och lösen ord. Användar namnet och lösen ordet måste vara domän konto namnet för den person vars "från"-adress beskrivs i föregående steg. Annars Miss lyckas meddelande leveransen.

4. Testa inställningarna för SMTP-servern genom att välja **Skicka test-e-post**, ange den e-postadress som du vill att Mabs ska skicka test meddelandet till och välj sedan **OK**. Välj **alternativ**  >  **meddelanden** och välj de typer av aviseringar som mottagarna vill meddelas om. I **mottagare** anger du e-postadressen för varje mottagare som du vill att Mabs ska skicka kopior av meddelanden till.

### <a name="publish-operations-manager-alerts"></a>Publicera aviseringar för Operations Manager

1. I Mabs administratörskonsol väljer du **övervaknings**  >  **Åtgärds**  >  **alternativ**  >  **avisering publicering**  >  **publicera aktiva aviseringar**

2. När du har aktiverat **aviserings publicering** publiceras alla befintliga Mabs-aviseringar som kan kräva en användar åtgärd i händelse loggen för **Mabs-aviseringar** . Operations Manager agenten som är installerad på MABS-servern publicerar sedan aviseringarna till Operations Manager och fortsätter att uppdatera konsolen när nya aviseringar genereras.

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Återställa ett SharePoint-objekt från en disk med hjälp av MABS

I följande exempel har den återställda *SharePoint-objektet* tagits bort av misstag och måste återställas av misstag.
![MABS SharePoint-Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Öppna **Mabs-administratörskonsol**. Alla SharePoint-servergrupper som skyddas av MABS visas på fliken **skydd** .

    ![MABS SharePoint-Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Välj fliken **återställning** för att börja återställa objektet.

    ![MABS SharePoint-Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Du kan söka i SharePoint efter återställning av *SharePoint-objekt* med hjälp av en jokertecken sökning inom ett återställnings punkt intervall.

    ![MABS SharePoint-Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Välj lämplig återställnings punkt i Sök resultaten, högerklicka på objektet och välj sedan **Återställ**.
5. Du kan också bläddra igenom olika återställnings punkter och välja en databas eller ett objekt som ska återställas. Välj **datum > återställnings tid** och välj sedan rätt **databas > SharePoint-servergrupp > återställnings punkt > objekt**.

    ![MABS SharePoint-Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Högerklicka på objektet och välj sedan **Återställ** för att öppna **återställnings guiden**. Välj **Nästa**.

    ![Granska val av återställning](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Välj den typ av återställning du vill utföra och välj sedan **Nästa**.

    ![Återställnings typ](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > Valet av **Återställ till original** i exemplet återställer objektet till den ursprungliga SharePoint-webbplatsen.
   >
   >
8. Välj den **återställnings process** som du vill använda.

   * Välj **Återställ utan att använda en återställnings grupp** om SharePoint-servergruppen inte har ändrats och är samma som återställnings punkten som återställs.
   * Välj **Återställ med en återställnings grupp** om SharePoint-servergruppen har ändrats sedan återställnings punkten skapades.

     ![Återställningsprocessen](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Ange en mellanlagringsplats SQL Server instans plats för att återställa databasen tillfälligt och tillhandahålla en mellanlagrings fil resurs på MABS och servern som kör SharePoint för att återställa objektet.

    ![Location1 för mellanlagring](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS bifogar innehålls databasen som är värd för SharePoint-objektet till den tillfälliga SQL Server-instansen. Från innehålls databasen återställer den objektet och placerar det på mellanlagringsplatsen på MABS. Det återställda objektet som finns på mellanlagringsplatsen måste nu exporteras till mellanlagringsplatsen i SharePoint-servergruppen.

    ![Location2 för mellanlagring](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Välj **Ange återställnings alternativ** och tillämpa säkerhets inställningar på SharePoint-servergruppen eller tillämpa säkerhets inställningarna för återställnings punkten. Välj **Nästa**.

    ![Återställnings alternativ](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Du kan välja att begränsa användningen av nätverks bandbredd. Detta minimerar påverkan på produktions servern under produktions timmar.
    >
    >
11. Granska sammanfattnings informationen och välj sedan **Återställ** för att påbörja återställningen av filen.

    ![Återställnings Sammanfattning](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Klicka nu på fliken **övervakning** i **Mabs-administratörskonsol** för att visa **status** för återställningen.

    ![Återställnings status](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Filen har återställts. Du kan uppdatera SharePoint-webbplatsen för att kontrol lera den återställda filen.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-mabs"></a>Återställa en SharePoint-databas från Azure med hjälp av MABS

1. Du återställer en SharePoint-innehållstyp genom att bläddra igenom olika återställnings punkter (se tidigare) och välja den återställnings punkt som du vill återställa.

    ![MABS SharePoint-Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Dubbelklicka på SharePoint-återställnings punkten för att Visa tillgänglig information om SharePoint-katalogen.

   > [!NOTE]
   > Eftersom SharePoint-servergruppen är skyddad för långsiktig kvarhållning i Azure, finns ingen katalog information (metadata) på MABS-servern. När en innehålls databas för SharePoint-databasen måste återställas måste du därför katalogisera SharePoint-servergruppen igen.
   >
   >
3. Välj **ny katalog**.

    ![MABS SharePoint-Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Fönstret status för **moln Omkatalogisering** öppnas.

    ![MABS SharePoint-Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    När fil förteckningen har slutförts ändras statusen till *lyckades*. Välj **Stäng**.

    ![MABS SharePoint-Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Välj det SharePoint-objekt som visas på fliken MABS **återställning** för att hämta struktur för innehålls databasen. Högerklicka på objektet och välj sedan **Återställ**.

    ![MABS SharePoint-Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Följ återställnings stegen tidigare i den här artikeln för att återställa en SharePoint-innehålls databas från disken.

## <a name="switching-the-front-end-web-server"></a>Byta Front-End webb server

Om du har fler än en front webb server och vill växla till den server som MABS använder för att skydda Server gruppen, följer du anvisningarna:

I följande procedur används exemplet på en Server grupp med två frontend-webbservrar, *server1* och *Server2*. MABS använder *server1* för att skydda Server gruppen. Ändra den frontend-webbserver som MABS använder till *Server2* så att du kan ta bort *server1* från Server gruppen.

> [!NOTE]
> Om den klient webb server som MABS använder för att skydda Server gruppen inte är tillgänglig kan du använda följande procedur för att ändra frontend-webbservern genom att starta i steg 4.

### <a name="to-change-the-front-end-web-server-that-mabs-uses-to-protect-the-farm"></a>Ändra den klient webb server som MABS använder för att skydda Server gruppen

1. Stoppa SharePoint VSS Writer-tjänsten på *server1* genom att köra följande kommando i en kommando tolk:

    ```CMD
    stsadm -o unregisterwsswriter
    ```

1. Öppna Registereditorn på *server1* och navigera till följande nyckel:

   **HKLM\System\CCS\Services\VSS\VssAccessControl**

1. Kontrol lera alla värden som anges i under nyckeln VssAccessControl. Om en post har värde data på 0 och en annan VSS-skrivare körs under de associerade konto uppgifterna, ändra värdet till 1.

1. Installera en skydds agent på *Server2*.

   > [!WARNING]
   > Du kan bara växla frontend-frontend-servrar om båda servrarna finns i samma domän.

1. På *Server2*, i en kommando tolk, ändra katalogen till `_MABS installation location_\bin\` och kör **ConfigureSharePoint**. Mer information om ConfigureSharePoint finns i [Konfigurera säkerhets kopiering](#configure-backup).

1. Välj den skydds grupp som server gruppen tillhör och välj sedan **ändra skydds grupp**.

1. I guiden Ändra grupp, på sidan **Välj grupp medlemmar** , expandera *Server2* och välj server gruppen och slutför sedan guiden.

   En konsekvens kontroll kommer att starta.

1. Om du utför steg 6 kan du nu ta bort volymen från skydds gruppen.

## <a name="next-steps"></a>Nästa steg

* Se artikeln [säkerhetskopiera filer och program](backup-mabs-files-applications-azure-stack.md) .
* Se [säkerhets kopierings SQL Server i Azure Stack](backup-mabs-sql-azure-stack.md) artikeln.
