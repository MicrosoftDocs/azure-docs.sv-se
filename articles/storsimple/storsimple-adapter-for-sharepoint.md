---
title: Installera StorSimple Adapter för SharePoint | Microsoft Docs
description: Beskriver hur du installerar och konfigurerar eller tar bort StorSimple-adaptern för SharePoint i en SharePoint-servergrupp.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 36c20b75-f2e5-4184-a6b5-9c5e618f79b2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/06/2017
ms.author: alkohli
ms.openlocfilehash: 639efab3a9eaef400b3fbe3d0b134d02f458b865
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94966300"
---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>Installera och konfigurera StorSimple Adapter för SharePoint
## <a name="overview"></a>Översikt
StorSimple-adaptern för SharePoint är en komponent som gör att du kan tillhandahålla Microsoft Azure StorSimple flexibel lagring och data skydd till SharePoint-servergrupper. Du kan använda kortet för att flytta BLOB-innehåll (Binary Large Object) från SQL Server innehålls databaser till Microsoft Azure StorSimple hybrid moln lagrings enhet.

StorSimple-adaptern för SharePoint fungerar som en RBS-Provider (Remote BLOB Storage) och använder SQL Server Remote BLOB Storage-funktionen för att lagra ostrukturerat SharePoint-innehåll (i form av BLOBBAR) på en fil server som backas upp av en StorSimple-enhet.

> [!NOTE]
> StorSimple-adaptern för SharePoint stöder SharePoint Server 2010 Remote BLOB Storage (RBS). Den har inte stöd för SharePoint Server 2010 external BLOB Storage (EBS).


* Hämta StorSimple-adaptern för SharePoint genom att gå till [StorSimple Adapter för SharePoint][1] i Microsoft Download Center.
* Information om hur du planerar för RBS-och RBS-begränsningar finns i [välja att använda RBS i SharePoint 2013][2] eller [Planera för RBS (SharePoint Server 2010)][3].

I resten av den här översikten beskrivs kortfattat rollen för StorSimple-kortet för SharePoint och de kapacitets-och prestanda begränsningar för SharePoint som du bör känna till innan du installerar och konfigurerar kortet. När du har granskat den här informationen går du till [StorSimple Adapter för SharePoint-installation](#storsimple-adapter-for-sharepoint-installation) för att börja konfigurera kortet.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>StorSimple-Adapter för SharePoint-förmåner
På en SharePoint-webbplats lagras innehåll som ostrukturerade BLOB-data i en eller flera innehålls databaser. De här databaserna finns som standard på datorer som kör SQL Server och finns i SharePoint-servergruppen. BLOBBAR kan snabbt öka i storlek, vilket förbrukar stora mängder lokalt lagrings utrymme. Därför kanske du vill hitta en annan, billigare lagrings lösning. SQL Server tillhandahåller en teknik som kallas fjärrBlob Storage (RBS) som gör att du kan lagra BLOB-innehåll i fil systemet utanför SQL Server-databasen. Med RBS kan BLOBBAR finnas i fil systemet på den dator som kör SQL Server, eller så kan de lagras i fil systemet på en annan serverdator.

RBS kräver att du använder en RBS-Provider, till exempel StorSimple Adapter för SharePoint, för att aktivera RBS i SharePoint. StorSimple-adaptern för SharePoint fungerar med RBS, vilket gör att du kan flytta BLOBBAR till en server som säkerhets kopie ras av Microsoft Azure StorSimple systemet. Microsoft Azure StorSimple lagrar sedan BLOB-data lokalt eller i molnet, baserat på användning. BLOBBAR som är mycket aktiva (kallas vanligt vis nivå 1 eller heta data) finns lokalt. Mindre aktiva data och Arkiv data finns i molnet. När du har aktiverat RBS i en innehålls databas lagras alla nya BLOB-innehåll som skapats i SharePoint på StorSimple-enheten och inte i innehålls databasen.

Microsoft Azure StorSimple implementering av RBS ger följande fördelar:

* Genom att flytta BLOB-innehåll till en separat server kan du minska inläsningen av frågan på SQL Server, vilket kan förbättra SQL Server svars tiderna. 
* I Azure StorSimple används deduplicering och komprimering för att minska data storleken.
* Azure StorSimple tillhandahåller data skydd i form av lokala och molnbaserade ögonblicks bilder. Om du placerar själva databasen på StorSimple-enheten kan du också säkerhetskopiera innehålls databasen och BLOBarna tillsammans på ett kraschat sätt. (Det går bara att flytta innehålls databasen till enheten för enheten StorSimple 8000-serien. Den här funktionen stöds inte för serien 5000 eller 7000.)
* Azure StorSimple innehåller funktioner för haveri beredskap, inklusive redundans, fil-och volym återställning (inklusive test återställning) och snabb återställning av data.
* Du kan använda data återställnings program, till exempel Kroll Ontrack PowerControls, med StorSimple-ögonblicksbilder av BLOB-data för att utföra återställning på objekt nivå av SharePoint-innehåll. (Den här data återställnings program varan är ett separat köp.)
* StorSimple-adaptern för SharePoint ansluter till Central administrations portalen för SharePoint, så att du kan hantera hela SharePoint-lösningen från en central plats.

Att flytta BLOB-innehåll till fil systemet kan ge andra kostnads besparingar och-fördelar. Att använda RBS kan till exempel minska behovet av dyrbar lagring på nivå 1 och, eftersom den krymper innehålls databasen, kan du minska antalet databaser som krävs i SharePoint-servergruppen. Andra faktorer, t. ex. databas storleks gränser och mängden icke-RBS-innehåll, kan dock också påverka lagrings kraven. Mer information om kostnaderna och fördelarna med att använda RBS finns i [Planera för RBS (SharePoint Foundation 2010)][4] och [välja att använda rbs i SharePoint 2013][5].

### <a name="capacity-and-performance-limits"></a>Kapacitets-och prestanda begränsningar
Innan du funderar på att använda RBS i din SharePoint-lösning bör du vara medveten om de testade prestanda-och kapacitets gränserna för SharePoint Server 2010 och SharePoint Server 2013 och hur dessa gränser relaterar till acceptabla prestanda. Mer information finns i [program gränser och begränsningar för SharePoint 2013](/SharePoint/install/software-boundaries-and-limits-0).

Granska följande innan du konfigurerar RBS:

* Se till att den totala storleken på innehållet (storleken på en innehålls databas plus storleken på associerade, externa BLOBBAR) inte överskrider storleks gränsen för RBS som stöds av SharePoint. Den här gränsen är 200 GB. 
  
    **För att mäta innehålls databas och BLOB-storlek**
  
  1. Kör den här frågan på den centrala administrationen WFE. Starta SharePoint Management Shell och ange sedan följande Windows PowerShell-kommando för att hämta storleken på innehålls databaserna:
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      Det här steget hämtar storleken på innehålls databasen på disken.
  2. Kör någon av följande SQL-frågor i SQL Management Studio i rutan SQL Server på varje innehålls databas och Lägg till resultatet till det tal som hämtades i steg 1.
     
     Ange följande på SharePoint 2013-innehålls databaser:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     Ange följande på SharePoint 2010-innehålls databaser:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     Det här steget hämtar storleken på de BLOBBAR som har frigjorts.
* Vi rekommenderar att du lagrar all BLOB-och databas innehåll lokalt på StorSimple-enheten. StorSimple-enheten är ett kluster med två noder för hög tillgänglighet. Att placera innehålls databaser och BLOBBAR på StorSimple-enheten ger hög tillgänglighet.
  
    Använd traditionella metoder för SQL Server migrering för att flytta innehålls databasen till StorSimple-enheten. Flytta bara databasen när allt BLOB-innehåll från databasen har flyttats till fil resursen via RBS. Om du väljer att flytta innehålls databasen till StorSimple-enheten rekommenderar vi att du konfigurerar lagring av innehålls databaser på enheten som en primär volym.
* I Microsoft Azure StorSimple, om du använder skiktade volymer, finns det inget sätt att garantera att innehåll som lagras lokalt på StorSimple-enheten inte är i nivå av Microsoft Azure moln lagring. Därför rekommenderar vi att du använder StorSimple lokalt fästa volymer tillsammans med SharePoint RBS. Detta säkerställer att allt BLOB-innehåll förblir lokalt på StorSimple-enheten och inte flyttas till Microsoft Azure.
* Om du inte lagrar innehålls databaserna på StorSimple-enheten använder du traditionella SQL Server metod tips för hög tillgänglighet som stöder RBS. SQL Server klustring stöder RBS, medan SQL Server spegling inte gör det. 

> [!WARNING]
> Om du inte har aktiverat RBS rekommenderar vi inte att du flyttar innehålls databasen till StorSimple-enheten. Det här är en testad konfiguration.

## <a name="storsimple-adapter-for-sharepoint-installation"></a>StorSimple Adapter för SharePoint-installation
Innan du kan installera StorSimple-adaptern för SharePoint måste du konfigurera StorSimple-enheten och se till att SharePoint-servergruppen och SQL Server instansiering uppfyller alla krav. I den här självstudien beskrivs konfigurations krav samt procedurer för att installera och uppgradera StorSimple-adaptern för SharePoint.

## <a name="configure-prerequisites"></a>Konfigurera krav
Innan du kan installera StorSimple-adaptern för SharePoint bör du kontrol lera att StorSimple-enheten, SharePoint-servergruppen och SQL Server instansiering uppfyller följande krav.

### <a name="system-requirements"></a>Systemkrav
StorSimple-adaptern för SharePoint fungerar med följande maskin-och program vara:

* Operativ system som stöds – Windows Server 2008 R2 SP1, Windows Server 2012 eller Windows Server 2012 R2
* SharePoint-versioner som stöds – SharePoint Server 2010 eller SharePoint Server 2013
* SQL Server-versioner som stöds – SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition eller SQL Server 2012 Enterprise Edition
* StorSimple-enheter som stöds – StorSimple 8000-serien, StorSimple 7000-serien eller StorSimple 5000-serien.

### <a name="storsimple-device-configuration-prerequisites"></a>Krav för StorSimple-enhets konfiguration
StorSimple-enheten är en blockerande enhet och därför kräver en fil server där data kan finnas. Vi rekommenderar att du använder en separat server i stället för en befintlig server från SharePoint-servergruppen. Den här fil servern måste finnas i samma lokala nätverk (LAN) som SQL Server datorn som är värd för innehålls databaserna.

> [!TIP]
> * Om du konfigurerar SharePoint-servergruppen för hög tillgänglighet bör du även distribuera fil servern för hög tillgänglighet.
> * Om du inte lagrar innehålls databasen på StorSimple-enheten använder du rekommenderade metod tips för hög tillgänglighet som stöder RBS. SQL Server klustring stöder RBS, medan SQL Server spegling inte gör det. 


Kontrol lera att din StorSimple-enhet är korrekt konfigurerad och att lämpliga volymer som stöder din SharePoint-distribution är konfigurerade och tillgängliga från din SQL Server dator. Gå till [distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md) om du inte har distribuerat och konfigurerat StorSimple-enheten ännu. Notera IP-adressen för StorSimple-enheten. du kommer att behöva det under StorSimple Adapter för SharePoint-installation.

Se dessutom till att volymen som ska användas för BLOB-externalization uppfyller följande krav:

* Volymen måste vara formaterad med en storlek på 64 KB-allokeringsenhet.
* Din front webb (WFE) och program servrar måste kunna få åtkomst till volymen via en Universal Naming Convention (UNC)-sökväg.
* SharePoint-servergruppen måste vara konfigurerad att skriva till volymen.

> [!NOTE]
> När du har installerat och konfigurerat kortet måste alla BLOB-externalization gå via StorSimple-enheten (enheten visar volymerna för att SQL Server och hantera lagrings nivåerna). Du kan inte använda andra mål för BLOB-externalization.


Om du planerar att använda StorSimple Snapshot Manager för att ta ögonblicks bilder av BLOB-och databas data, måste du installera StorSimple Snapshot Manager på databas servern så att den kan använda tjänsten SQL Writer för att implementera Windows tjänsten Volume Shadow Copy (VSS).

> [!IMPORTANT]
> StorSimple Snapshot Manager stöder inte VSS-skrivaren för SharePoint och kan inte ta programkonsekventa ögonblicks bilder av SharePoint-data. I ett SharePoint-scenario tillhandahåller StorSimple Snapshot Manager endast kraschbaserade säkerhets kopieringar.


## <a name="sharepoint-farm-configuration-prerequisites"></a>Konfigurations krav för SharePoint-servergrupp
Kontrol lera att SharePoint-servergruppen är korrekt konfigurerad, enligt följande:

* Kontrol lera att SharePoint-servergruppen är i felfritt tillstånd och kontrol lera följande:
* Alla SharePoint WFE-och program servrar som är registrerade i Server gruppen körs och kan pingas från den server där du kommer att installera StorSimple-adaptern för SharePoint.
* SharePoint-tidstjänsten (SPTimerV3 eller SPTimerV4) körs på varje WFE-Server och program Server.
* Både SharePoint-tidstjänsten och IIS-programpoolen som den centrala administrations platsen för SharePoint körs under har administratörs behörighet.
* Se till att förbättrad säkerhets kontext i Internet Explorer (IE ESC) är inaktiverat. Följ dessa steg om du vill inaktivera Internet Explorer ESC:
  
  1. Stäng alla instanser av Internet Explorer.
  2. Starta Serverhanteraren.
  3. Klicka på **lokal server** i den vänstra rutan.
  4. I den högra rutan, bredvid **förbättrad säkerhets konfiguration i IE**, klickar du på **på**.
  5. Klicka på **av** under **Administratörer**.
  6. Klicka på **OK**.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Krav för Remote BLOB Storage (RBS)
Kontrol lera att du använder en version av SQL Server som stöds. Endast följande versioner stöds och kan använda RBS:

* SQL Server 2008 Enterprise Edition
* SQL Server 2008 R2 Enterprise Edition
* SQL Server 2012 Enterprise Edition

BLOBBAR kan bara väljas på de volymer som StorSimple-enheten presenterar för SQL Server. Det finns inte stöd för andra mål för BLOB-externalization.

När du har slutfört alla nödvändiga konfigurations steg går du till [Installera StorSimple Adapter för SharePoint](#install-the-storsimple-adapter-for-sharepoint).

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>Installera StorSimple Adapter för SharePoint
Använd följande steg för att installera StorSimple-adaptern för SharePoint. Om du installerar om program varan kan du läsa mer i [uppgradera eller installera om StorSimple-adaptern för SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). Tiden som krävs för installationen beror på det totala antalet SharePoint-databaser i SharePoint-servergruppen.

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>Konfigurera RBS
När du har installerat StorSimple Adapter för SharePoint konfigurerar du RBS enligt beskrivningen i följande procedur.

> [!TIP]
> StorSimple-adaptern för SharePoint ansluter till sidan Central administration av SharePoint, vilket gör att RBS kan aktive ras eller inaktive ras för varje innehålls databas i SharePoint-servergruppen. Att aktivera eller inaktivera RBS i innehålls databasen gör att en IIS-återställning, som, beroende på din grupp konfiguration, kan tillfälligt störa tillgänglighet för SharePoint-webbfrontend (WFE). (Faktorer som användning av en belastningsutjämnare på klient sidan, aktuell server belastning och så vidare, kan begränsa eller eliminera detta avbrott.) För att skydda användare från avbrott rekommenderar vi att du aktiverar eller inaktiverar RBS endast under ett planerat underhålls fönster.


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>Konfigurera skräp insamling
När objekt tas bort från en SharePoint-webbplats raderas de inte automatiskt från RBS Store-volymen. I stället tar ett asynkront bakgrunds underhålls program bort överblivna BLOBBAR från fil arkivet. System administratörer kan schemalägga processen så att den körs regelbundet eller så kan de starta när det behövs.

Detta underhålls program (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) installeras automatiskt på alla SharePoint WFE-servrar och program servrar när du aktiverar RBS. Programmet installeras på följande plats: *startenhet*: \Program\microsoft SQL Remote Blob Storage 10.50 \ kvarhållare \

Information om hur du konfigurerar och använder underhålls programmet finns i [underhålla RBS i SharePoint Server 2013][8].

> [!IMPORTANT]
> RBS-underhållar programmet är resurs krävande. Du bör schemalägga att den bara ska köras under perioder med låg aktivitet i SharePoint-servergruppen.


### <a name="delete-orphaned-blobs-immediately"></a>Ta bort överblivna BLOBBAR direkt
Om du behöver ta bort överblivna BLOBBAR direkt kan du använda följande instruktioner. Observera att dessa instruktioner är ett exempel på hur detta kan göras i en SharePoint 2013-miljö med följande komponenter:

* Innehålls databasens namn är WSS_Content.
* SQL Server namnet är SHRPT13-SQL12\SHRPT13.
* Webb program namnet är SharePoint – 80.

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>Uppgradera eller installera om StorSimple Adapter för SharePoint
Använd följande procedur för att uppgradera SharePoint Server och sedan installera om StorSimple Adapter för SharePoint eller om du bara vill uppgradera eller installera om kortet i en befintlig SharePoint-servergrupp.

> [!IMPORTANT]
> Granska följande information innan du försöker uppgradera SharePoint-programvaran och/eller uppgradera eller installera om StorSimple-adaptern för SharePoint:
> 
> * Filer som tidigare har flyttats till extern lagring via RBS är inte tillgängliga förrän ominstallationen är färdig och RBS-funktionen aktive ras igen. Om du vill begränsa användar påverkan utför du en uppgradering eller ominstallation under ett planerat underhålls fönster.
> * Tiden som krävs för uppgraderingen/ominstallationen kan variera beroende på det totala antalet SharePoint-databaser i SharePoint-servergruppen.
> * När uppgraderingen/ominstallationen är klar måste du aktivera RBS för innehålls databaserna. Mer information finns i [Konfigurera RBS](#configure-rbs) .
> * Om du konfigurerar RBS för en SharePoint-grupp som har ett stort antal databaser (större än 200) kan det hända att **SharePoint Central administrations** sidan tar slut. Om detta inträffar uppdaterar du sidan. Detta påverkar inte konfigurations processen.


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>StorSimple Adapter för SharePoint-borttagning
Följande procedurer beskriver hur du flyttar BLOBarna tillbaka till SQL Server innehålls databaser och sedan avinstallerar StorSimple Adapter för SharePoint. 

> [!IMPORTANT]
> Du måste flytta BLOBarna tillbaka till innehålls databaserna innan du avinstallerar kort program varan.


### <a name="before-you-begin"></a>Innan du börjar
Samla in följande information innan du flyttar tillbaka data till SQL Server innehålls databaser och påbörja borttagnings processen för kortet:

* Namnen på alla databaser som RBS är aktiverat för
* UNC-sökvägen för det konfigurerade BLOB Store

### <a name="move-the-blobs-back-to-the-content-databases"></a>Flytta BLOBarna tillbaka till innehålls databaserna
Innan du avinstallerar StorSimple-adaptern för SharePoint-programvara måste du migrera alla BLOBar som har utväxlats tillbaka till SQL Server innehålls databaserna. Om du försöker avinstallera StorSimple-adaptern för SharePoint innan du flyttar alla BLOBBAR tillbaka till innehålls databaserna visas följande varnings meddelande.

![Varnings meddelande](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>Flytta BLOBarna tillbaka till innehålls databaserna
1. Ladda ned vart och ett av de externa objekten.
2. Öppna sidan **Central administration av SharePoint** och bläddra till **Systeminställningar**.
3. Under **Azure-StorSimple** klickar du på **Konfigurera StorSimple-kort**.
4. På sidan **Konfigurera StorSimple-kort** klickar du på knappen **inaktivera** under var och en av innehålls databaserna som du vill ta bort från den externa blob-lagringen. 
5. Ta bort objekten från SharePoint och ladda sedan upp dem igen.

Du kan också använda Microsoft PowerShell- `RBS Migrate()` cmdleten som ingår i SharePoint. Mer information finns i [Migrera innehåll till eller från RBS](/previous-versions/office/sharepoint-foundation-2010/ff628255(v=office.14)).

När du har flyttat BLOBarna tillbaka till innehålls databasen går du till nästa steg: [Avinstallera kortet](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>Avinstallera kortet
När du har flyttat BLOBarna tillbaka till SQL Server innehålls databaser kan du använda något av följande alternativ för att avinstallera StorSimple-adaptern för SharePoint.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>Använda installations programmet för att avinstallera kortet
1. Använd ett konto med administratörs behörighet för att logga in på webb server delen (WFE).
2. Dubbelklicka på StorSimple Adapter för SharePoint Installer. Installations guiden startar.
   
    ![Installations guide](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. Klicka på **Nästa**. Följande sida visas.
   
    ![Ta bort sida i installations guiden](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. Klicka på **ta bort** för att välja borttagnings processen. Följande sida visas.
   
    ![Bekräftelse sida för installations guiden](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. Klicka på **ta bort** för att bekräfta borttagningen. Sidan följande förlopp visas.
   
    ![Sidan förlopp för installations guiden](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. När borttagningen är klar visas sidan Slutför. Klicka på **Slutför** för att stänga installations guiden.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Använda kontroll panelen för att avinstallera kortet
1. Öppna kontroll panelen och klicka sedan på **program och funktioner**.
2. Välj **StorSimple Adapter för SharePoint** och klicka sedan på **Avinstallera**.

## <a name="next-steps"></a>Nästa steg
[Läs mer om StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: /SharePoint/administration/rbs-planning
[3]: /previous-versions/office/sharepoint-server-2010/ff628583(v=office.14)
[4]: /previous-versions/office/sharepoint-foundation-2010/ff628569(v=office.14)
[5]: /SharePoint/administration/rbs-planning
[8]: /SharePoint/administration/maintain-rbs