---
title: Visa och hantera aviseringar för enhet med StorSimple 8000-serien
description: Beskriver StorSimple-aviserings villkor och allvarlighets grad, hur du konfigurerar varnings meddelanden och hur du använder tjänsten StorSimple Enhetshanteraren för att hantera aviseringar.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: 36ed87a75d590a8647e5347b3e90e6f5159dc6b5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016853"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-storsimple-alerts"></a>Använd tjänsten StorSimple Enhetshanteraren för att visa och hantera StorSimple-aviseringar

## <a name="overview"></a>Översikt

Med bladet **aviseringar** i StorSimple Enhetshanteraren-tjänsten kan du granska och rensa StorSimple-relaterade aviseringar i real tid. Från det här bladet kan du centralt övervaka hälso problemen för dina StorSimple-enheter och den övergripande Microsoft Azure StorSimples lösningen.

I den här självstudien beskrivs vanliga aviserings villkor, allvarlighets grader för aviseringar och hur du konfigurerar aviserings meddelanden. Dessutom innehåller den snabb referens tabeller för varningar, som gör att du snabbt kan hitta en speciell avisering och svara på lämpligt sätt.

![Sidan aviseringar](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="common-alert-conditions"></a>Vanliga aviserings villkor

Din StorSimple-enhet genererar aviseringar som svar på olika villkor. Följande är de vanligaste typerna av aviserings villkor:

* **Maskin varu problem** – de här aviseringarna anger om maskin varans hälso tillstånd. De meddelar dig om uppgraderingar av inbyggd program vara behövs, om ett nätverks gränssnitt har problem eller om det är problem med en av dina data enheter.
* **Anslutnings problem** – dessa aviseringar inträffar när det är svårt att överföra data. Kommunikations problem kan uppstå under överföring av data till och från Azure Storage-kontot eller på grund av bristande anslutning mellan enheterna och tjänsten StorSimple Enhetshanteraren. Kommunikations problem är några av de svåra att åtgärda eftersom det finns så många fel punkter. Först bör du först kontrol lera att nätverks anslutningen och Internet åtkomst är tillgängliga innan du fortsätter till mer avancerad fel sökning. Om du behöver hjälp med fel sökning går du till [Felsöka med cmdleten Test-Connection](storsimple-8000-troubleshoot-deployment.md).
* **Prestanda problem** – de här aviseringarna orsakas när systemet inte fungerar optimalt, till exempel när det är under en tung belastning.

Dessutom kan du se aviseringar relaterade till säkerhet, uppdateringar eller jobb haverier.

## <a name="alert-severity-levels"></a>Allvarlighets nivåer för aviseringar

Aviseringar har olika allvarlighets grader, beroende på vilken effekt som aviserings situationen kommer att ha och behovet av svar på aviseringen. Allvarlighetsgraderna är:

* **Kritisk** – den här aviseringen är som svar på ett villkor som påverkar systemets prestanda. Åtgärd krävs för att säkerställa att StorSimple-tjänsten inte avbryts.
* **Varning** – det här tillståndet kan bli kritiskt om det inte är löst. Du bör undersöka situationen och vidta alla åtgärder som krävs för att ta bort problemet.
* **Information** – den här aviseringen innehåller information som kan vara användbar vid spårning och hantering av systemet.

## <a name="configure-alert-settings"></a>Konfigurera aviseringsinställningar

Du kan välja om du vill få ett meddelande via e-post om aviserings villkoren för var och en av dina StorSimple-enheter. Dessutom kan du identifiera andra aviserings mottagare genom att ange deras e-postadresser i rutan **andra e-postmottagare** , avgränsade med semikolon.

> [!NOTE]
> Du kan ange högst 20 e-postadresser per enhet.

När du har aktiverat e-postavisering för en enhet kommer medlemmar i meddelande listan att få ett e-postmeddelande varje gången en kritisk varning inträffar. Meddelandena skickas från *StorSimple-Alerts-noreply \@ mail.windowsazure.com* och beskriver varnings villkoret. Mottagare kan klicka på **Avbryt** för att ta bort sig själva från listan med e-postaviseringar.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Så här aktiverar du e-postavisering om aviseringar för en enhet
1. Gå till StorSimple Device Manager-tjänsten. I listan med enheter väljer du och klickar på den enhet som du vill konfigurera.
2. Gå till **Inställningar**  >  **Allmänt** för enheten.

   ![Varnings blad](./media/storsimple-8000-manage-alerts/configure-alerts-email2.png)
   
2. I bladet **allmänna inställningar** går du till **aviserings inställningar** och anger följande:
   
   1. I fältet **skicka e-postavisering** väljer du **Ja**.
   2. I fältet **Administratörer för e-posttjänst** väljer du **Ja** om du vill att tjänst administratören och alla medadministratörer ska få aviserings meddelanden.
   3. I fältet **andra e-postmottagare** anger du e-postadresserna till alla andra mottagare som ska få aviserings meddelanden. Ange namn i formatet *någon \@ Somewhere.com*. Använd semikolon för att avgränsa e-postadresserna. Du kan konfigurera högst 20 e-postadresser per enhet. 
      
3. Skicka ett test-e-postmeddelande genom att klicka på **Skicka test-e-post**. Tjänsten StorSimple Enhetshanteraren visar status meddelanden när det vidarebefordrar test meddelandet.

    ![Aviserings inställningar](./media/storsimple-8000-manage-alerts/configure-alerts-email3.png)

4. Du ser ett meddelande när test-e-postmeddelandet skickas. 
   
    ![Aviseringar om test av e-postmeddelande har skickats](./media/storsimple-8000-manage-alerts/configure-alerts-email4.png)
   
   > [!NOTE]
   > Om test aviserings meddelandet inte kan skickas visar tjänsten StorSimple Enhetshanteraren ett lämpligt fel meddelande. Vänta några minuter och försök sedan att skicka ett test meddelande igen. 

5. När du har slutfört konfigurationen klickar du på **Spara**. Klicka på **Ja** när du uppmanas att bekräfta åtgärden.

     ![Aviseringar testa e-postmeddelande skickat 2](./media/storsimple-8000-manage-alerts/configure-alerts-email5.png)

## <a name="view-and-track-alerts"></a>Visa och spåra aviseringar

Bladet StorSimple Enhetshanteraren tjänst Sammanfattning ger dig en snabb överblick över antalet aviseringar på dina enheter, ordnat efter allvarlighets grad.

![Instrument panel för aviseringar](./media/storsimple-8000-manage-alerts/device-summary4.png)

Om du klickar på allvarlighets grad öppnas **aviserings** bladet. Resultaten innehåller bara de aviseringar som matchar allvarlighets graden.

Om du klickar på en avisering i listan får du ytterligare information om aviseringen, inklusive den senaste gången aviseringen rapporterades, antalet förekomster av aviseringen på enheten och den rekommenderade åtgärden för att lösa aviseringen. Om det är en maskin varu varning kommer den också att identifiera maskin varu komponenten.

![Exempel på maskin varu varning](./media/storsimple-8000-manage-alerts/configure-alerts-email14.png)

Du kan kopiera aviserings informationen till en textfil om du behöver skicka informationen till Microsoft Support. När du har följt rekommendationen och löst aviserings villkoret lokalt bör du ta bort aviseringen från enheten genom att välja aviseringen i bladet **aviseringar** och klicka på **Rensa**. Om du vill ta bort flera aviseringar markerar du varje avisering, klickar på en kolumn förutom kolumnen **avisering** och klickar sedan på **Rensa** när du har valt att alla aviseringar ska rensas. Observera att vissa aviseringar rensas automatiskt när problemet löses eller när systemet uppdaterar aviseringen med ny information.

När du klickar på **Rensa** har du möjlighet att ange kommentarer om aviseringen och de steg som du har vidtagit för att lösa problemet. Vissa händelser raderas av systemet om en annan händelse utlöses med ny information. I så fall visas följande meddelande.

![Rensa aviserings meddelande](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Sortera och granska aviseringar

Det kan vara mer effektivt att köra rapporter om aviseringar så att du kan granska och ta bort dem i grupper. Dessutom kan **aviserings** bladet Visa upp till 250 aviseringar. Om du har överskridit det antalet aviseringar visas inte alla aviseringar i standardvyn. Du kan kombinera följande fält för att anpassa vilka aviseringar som visas:

* **Status** – du kan visa antingen **aktiva** eller **rensade** aviseringar. Aktiva aviseringar aktive ras fortfarande i systemet, medan rensade aviseringar antingen har rensats manuellt av en administratör eller program mässigt rensas eftersom systemet har uppdaterat varnings villkoret med ny information.
* **Allvarlighets grad** – du kan visa aviseringar för alla allvarlighets nivåer (kritisk, varning, information) eller bara en viss allvarlighets grad, till exempel endast kritiska aviseringar.
* **Källa** – du kan visa aviseringar från alla källor eller begränsa aviseringarna till de som kommer från antingen tjänsten eller en eller flera av enheterna.
* **Tidsintervall** – genom att ange **från** -och **till** -datum och tidsstämplar kan du titta på aviseringar under den tids period som du är intresse rad av.

![Aviserings lista](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="alerts-quick-reference"></a>Snabb referens för varningar

I följande tabeller visas några av de Microsoft Azure StorSimple aviseringar som du kan stöta på, samt ytterligare information och rekommendationer som är tillgängliga. StorSimple enhets aviseringar ingår i någon av följande kategorier:

* [Aviseringar om moln anslutning](#cloud-connectivity-alerts)
* [Kluster varningar](#cluster-alerts)
* [Aviseringar om haveri beredskap](#disaster-recovery-alerts)
* [Maskin varu aviseringar](#hardware-alerts)
* [Aviseringar om jobb haveri](#job-failure-alerts)
* [Lokalt fästa volym aviseringar](#locally-pinned-volume-alerts)
* [Nätverks aviseringar](#networking-alerts)
* [Prestanda varningar](#performance-alerts)
* [Säkerhetsaviseringar](#security-alerts)
* [Aviseringar om support paket](#support-package-alerts)
* [Meddelanden om kabinett miljö](#enclosure-environment-alerts)

### <a name="cloud-connectivity-alerts"></a>Aviseringar om moln anslutning

| Aviserings text | Händelse | Mer information/rekommenderade åtgärder |
|:--- |:--- |:--- |
| Det går inte att upprätta en anslutning till <*molnets autentiseringsuppgifter*>. |Det går inte att ansluta till lagrings kontot. |Det verkar som om det kan finnas problem med anslutningen till enheten. Kör `Test-HcsmConnection` cmdleten från Windows PowerShell-gränssnittet för StorSimple på enheten för att identifiera och åtgärda problemet. Om inställningarna är korrekta kan problemet vara med autentiseringsuppgifterna för det lagrings konto som aviseringen genererades för. I det här fallet använder du `Test-HcsStorageAccountCredential` cmdleten för att avgöra om det finns problem som du kan lösa.<ul><li>Kontrol lera nätverks inställningarna.</li><li>Kontrol lera autentiseringsuppgifterna för lagrings kontot.</li></ul> |
| Vi har inte tagit emot något pulsslag från enheten under det senaste <*antalet*> minuter. |Det går inte att ansluta till enheten. |Det verkar som om det finns problem med anslutningen till enheten. Använd `Test-HcsmConnection` cmdleten från Windows PowerShell-gränssnittet för StorSimple på enheten för att identifiera och åtgärda problemet eller kontakta nätverks administratören. |

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>StorSimple beteende när det inte går att ansluta till molnet

Vad händer om det inte går att använda moln anslutning för min StorSimple-enhet som körs i produktion?

Om det inte går att ansluta till en StorSimple produktions enhet kan följande inträffa, beroende på enhetens status:

* **För lokala data på din enhet**: under en viss tid kommer inga avbrott och läsningar fortsätta att hanteras. Men eftersom antalet utestående IOs ökar och överskrider en gräns kan läsningarna börja fungera.

    Beroende på hur mycket data som finns på enheten fortsätter skrivningarna att ske under de första timmarna efter avbrott i moln anslutningen. Skrivningarna saktar ned och slutligen slutar att fungera om moln anslutningen avbryts under flera timmar. (Det finns temporär lagring på enheten för data som ska flyttas till molnet. Det här fältet töms när data skickas. Om anslutningen Miss lyckas kommer inte data i det här lagrings utrymmet att skickas till molnet och det går inte att utföra IO.)
* **För data i molnet**: för de flesta moln anslutnings fel returneras ett fel. När anslutningen har återställts återupptas IOs utan att användaren måste ta volymen online. I sällsynta fall kan det krävas åtgärder från användaren att ta tillbaka volymen online från Azure Portal.
* **För moln ögonblicks bilder pågår**: åtgärden provas några gånger inom 4-5 timmar och om anslutningen inte återställs kommer moln ögonblicks bilderna att Miss par.

### <a name="cluster-alerts"></a>Kluster varningar

| Aviserings text | Händelse | Mer information/rekommenderade åtgärder |
|:--- |:--- |:--- |
| Enheten växlade över till <*enhets namnet*>. |Enheten är i underhålls läge. |Enheten växlades över på grund av att underhålls läget skulle gå in eller avslutas. Detta är normalt och ingen åtgärd krävs. När du har bekräftat den här aviseringen avmarkerar du den från sidan aviseringar. |
| Enheten växlade över till <*enhets namnet*>. |Enhetens inbyggda program vara eller program vara har precis uppdaterats. |Det uppstod en redundanskluster på grund av en uppdatering. Detta är normalt och ingen åtgärd krävs. När du har bekräftat den här aviseringen avmarkerar du den från sidan aviseringar. |
| Enheten växlade över till <*enhets namnet*>. |Styrenheten stängdes av eller startades om. |Enheten avbröts på grund av att den aktiva styrenheten stängdes av eller startades om av en administratör. Ingen åtgärd krävs. När du har bekräftat den här aviseringen avmarkerar du den från sidan aviseringar. |
| Enheten växlade över till <*enhets namnet*>. |Planerad redundansväxling. |Kontrol lera att det var en planerad redundansväxling. När du har vidtagit en lämplig åtgärd tar du bort den här aviseringen från aviserings sidan. |
| Enheten växlade över till <*enhets namnet*>. |Oplanerad redundansväxling. |StorSimple är byggd för att automatiskt återställa från oplanerad redundans. Om du ser ett stort antal aviseringar kontaktar du Microsoft Support. |
| Enheten växlade över till <*enhets namnet*>. |Annan/okänd orsak. |Om du ser ett stort antal aviseringar kontaktar du Microsoft Support. När problemet har lösts tar du bort den här aviseringen från aviserings sidan. |
| En kritisk enhets tjänst rapporterar status som misslyckad. |Datapath-tjänstens haveri. |Kontakta Microsoft Support om du behöver hjälp. |
| Virtuell IP-adress för nätverks gränssnitt <*data #*> rapporterar status som misslyckad. |Annan/okänd orsak. |Ibland kan tillfälliga förhållanden orsaka dessa aviseringar. I så fall kommer den här aviseringen att rensas automatiskt efter en stund. Kontakta Microsoft Support om problemet kvarstår. |
| Virtuell IP-adress för nätverks gränssnitt <*data #*> rapporterar status som misslyckad. |Gränssnitts namn: <*data #*> IP-adressen `<IP address>` kan inte anslutas på grund av att en dubblett-IP-adress har identifierats i nätverket. |Se till att den duplicerade IP-adressen tas bort från nätverket eller konfigurera om gränssnittet med en annan IP-adress. |

### <a name="disaster-recovery-alerts"></a>Aviseringar om haveri beredskap

| Aviserings text | Händelse | Mer information/rekommenderade åtgärder |
|:--- |:--- |:--- |
| Det gick inte att återställa alla inställningar för den här tjänsten under återställnings åtgärder. Enhetens konfigurations data är i ett inkonsekvent tillstånd för vissa enheter. |Inkonsekventa data upptäcktes efter haveri beredskap. |Krypterade data på tjänsten är inte synkroniserade med det på enheten. Auktorisera enhetens <*enhets namn*> från StorSimple Enhetshanteraren för att starta synkroniseringsprocessen. Använd Windows PowerShell-gränssnittet för StorSimple för att köra `Restore-HcsmEncryptedServiceData` cmdleten på enhets <*enhets namn*>, och ange det gamla lösen ordet som inmatat för denna cmdlet för att återställa säkerhets profilen. Kör sedan `Invoke-HcsmServiceDataEncryptionKeyChange` cmdleten för att uppdatera krypterings nyckeln för tjänst data. När du har vidtagit en lämplig åtgärd tar du bort den här aviseringen från aviserings sidan. |

### <a name="hardware-alerts"></a>Maskin varu aviseringar

| Aviserings text | Händelse | Mer information/rekommenderade åtgärder |
|:--- |:--- |:--- |
| Maskin varu komponent <*komponent-ID*> rapporterar status som <*status*>. | |Ibland kan tillfälliga förhållanden orsaka dessa aviseringar. I så fall kommer den här aviseringen att rensas automatiskt efter en stund. Kontakta Microsoft Support om problemet kvarstår. |
| Passiv styrenhet fungerar inte. |Den passiva (sekundära) styrenheten fungerar inte. |Enheten fungerar, men en av dina styrenheter fungerar inte. Försök att starta om kontrollanten. Kontakta Microsoft Support om problemet inte är löst. |

### <a name="job-failure-alerts"></a>Aviseringar om jobb haveri

| Aviserings text | Händelse | Mer information/rekommenderade åtgärder |
|:--- |:--- |:--- |
| Det gick inte att säkerhetskopiera <*käll volym grupps-ID*>. |Säkerhets kopierings jobbet misslyckades. |Anslutnings problem kan hindra säkerhets kopieringen från att slutföras. Om det inte finns några anslutnings problem kan du ha nått maximalt antal säkerhets kopior. Ta bort eventuella säkerhets kopior som inte längre behövs och försök igen. När du har vidtagit en lämplig åtgärd tar du bort den här aviseringen från aviserings sidan. |
| Kloning av <*käll-ID för säkerhets kopierings element*> till <det inte gick att> på *mål volymen* . |Klonings jobbet misslyckades. |Uppdatera säkerhets kopierings listan för att kontrol lera att säkerhets kopian fortfarande är giltig. Om säkerhets kopieringen är giltig är det möjligt att problem med moln anslutningen hindrar klonings åtgärden från att slutföras. Om det inte finns några anslutnings problem kan du ha nått lagrings gränsen. Ta bort eventuella säkerhets kopior som inte längre behövs och försök igen. När du har vidtagit lämpliga åtgärder för att lösa problemet tar du bort den här aviseringen från aviserings sidan. |
| Det gick inte att återställa <*källans ID för säkerhets kopierings element*>. |Återställnings jobbet misslyckades. |Uppdatera säkerhets kopierings listan för att kontrol lera att säkerhets kopian fortfarande är giltig. Om säkerhets kopieringen är giltig är det möjligt att problem med moln anslutningen hindrar återställnings åtgärden från att slutföras. Om det inte finns några anslutnings problem kan du ha nått lagrings gränsen. Ta bort eventuella säkerhets kopior som inte längre behövs och försök igen. När du har vidtagit lämpliga åtgärder för att lösa problemet tar du bort den här aviseringen från aviserings sidan. |

### <a name="locally-pinned-volume-alerts"></a>Lokalt fästa volym aviseringar

| Aviserings text | Händelse | Mer information/rekommenderade åtgärder |
|:--- |:--- |:--- |
| Det gick inte att skapa en lokal volym <*volym namnet*>. |Det gick inte att skapa volymen. <*Fel meddelande som motsvarar den misslyckade felkoden*>. |Anslutnings problem kan förhindra att åtgärden för att skapa utrymme slutförs. Lokalt fästa volymer är tjockt etablerade och processen för att skapa utrymme innebär att volymer på nivå av data spill i molnet. Om det inte finns några anslutnings problem kan du ha förbrukat det lokala utrymmet på enheten. Kontrol lera om det finns utrymme på enheten innan du försöker igen. |
| Det gick inte att utöka den lokala volymens <*volym namn*>. |Volym ändrings jobbet misslyckades på grund av <*fel meddelande som motsvarar felkoden som returnerades*>. |Anslutnings problem kan hindra volym expansions åtgärden från att slutföras. Lokalt fästa volymer är tjockt etablerade och processen för att utöka det befintliga utrymmet innebär att det finns spill i nivå volymer i molnet. Om det inte finns några anslutnings problem kan du ha förbrukat det lokala utrymmet på enheten. Kontrol lera om det finns utrymme på enheten innan du försöker igen. |
| Det gick inte att konvertera volymens <*volym namn*>. |Volym konverterings jobbet för att konvertera volym typen från lokalt fäst till nivå misslyckades. |Det gick inte att slutföra konverteringen av volymen från typen lokalt fäst till nivå. Kontrol lera att det inte finns några anslutnings problem som hindrar åtgärden från att slutföras. Fel sökning av anslutnings problem finns i [fel sökning med cmdleten Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Den ursprungliga lokalt fästa volymen har nu marker ATS som en nivå volym eftersom vissa data från den lokalt fästa volymen har spillat i molnet under konverteringen. Den resulterande nivån volym tar fortfarande upp lokalt utrymme på enheten som inte kan återtas för framtida lokala volymer.<br>Lös eventuella anslutnings problem, rensa aviseringen och konvertera volymen tillbaka till den ursprungliga lokalt fasta volym typen för att säkerställa att alla data görs tillgängliga lokalt. |
| Det gick inte att konvertera volymens <*volym namn*>. |Volym konverterings jobbet för att konvertera volym typen från nivå till lokalt fästes misslyckades. |Det gick inte att slutföra konverteringen av volymen från typ skiktad till lokalt fäst. Kontrol lera att det inte finns några anslutnings problem som hindrar åtgärden från att slutföras. Fel sökning av anslutnings problem finns i [fel sökning med cmdleten Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Den ursprungliga nivå volymen är nu markerad som en lokalt fixerad volym som en del av konverterings processen fortsätter att ha data som finns i molnet, medan det tjockt allokerade utrymmet på enheten för volymen inte längre kan återtas för framtida lokala volymer.<br>Lös eventuella anslutnings problem, rensa aviseringen och konvertera volymen tillbaka till den ursprungliga volym typen för att säkerställa att lokalt utrymme som är tjockt allokerat på enheten kan frigöras. |
| Nära användning av lokalt utrymme för lokala ögonblicks bilder av <*volym grupp namn*> |Lokala ögonblicks bilder för säkerhets kopierings principen kan snart få slut på utrymme och bli ogiltiga för att undvika skrivfel på värden. |Vanliga lokala ögonblicks bilder tillsammans med en hög data omsättning i de volymer som är associerade med den här säkerhets kopierings princip gruppen gör att det lokala utrymmet på enheten kan förbrukas snabbt. Ta bort eventuella lokala ögonblicks bilder som inte längre behövs. Uppdatera även dina lokala ögonblicks bild scheman för den här säkerhets kopierings principen för att ta mindre frekventa lokala ögonblicks bilder och se till att moln ögonblicks bilder tas regelbundet. Om dessa åtgärder inte vidtas kan det lokala utrymmet för de här ögonblicks bilderna snart bli förbrukat och systemet tar bort dem automatiskt för att säkerställa att värden skriver fortsätter att bearbetas korrekt. |
| Lokala ögonblicks bilder för <*volym grupp namn*> har ogiltig förklarats. |De lokala ögonblicks bilderna för <*volym grupp namn*> har ogiltig förklarats och sedan tagits bort eftersom de överskred det lokala utrymmet på enheten. |För att säkerställa att detta inte återkommer i framtiden granskar du de lokala ögonblicks bild scheman för den här säkerhets kopierings principen och tar bort eventuella lokala ögonblicks bilder som inte längre behövs. Vanliga lokala ögonblicks bilder tillsammans med en hög data omsättning i de volymer som är associerade med den här säkerhets kopierings princip gruppen kan leda till att det lokala utrymmet på enheten förbrukas snabbt. |
| Det gick inte att återställa <*källans ID för säkerhets kopierings element*>. |Återställnings jobbet misslyckades. |Om du har fäst lokalt eller en blandning av lokalt fästa volymer och skiktade volymer i den här säkerhets kopierings principen uppdaterar du säkerhets kopierings listan för att kontrol lera att säkerhets kopian fortfarande är giltig. Om säkerhets kopieringen är giltig är det möjligt att problem med moln anslutningen hindrar återställnings åtgärden från att slutföras. De lokalt fästa volymer som återställdes som en del av den här ögonblicks bild gruppen har inga data som hämtats till enheten, och om du har en blandning av skiktade och lokalt fästa volymer i den här ögonblicks bild gruppen kommer de inte att synkroniseras med varandra. Slutför återställnings åtgärden genom att ta volymerna i den här gruppen offline på värden och försök utföra återställnings åtgärden igen. Observera att eventuella ändringar av volym data som utfördes under återställnings processen går förlorade. |

### <a name="networking-alerts"></a>Nätverks aviseringar

| Aviserings text | Händelse | Mer information/rekommenderade åtgärder |
|:--- |:--- |:--- |
| Det gick inte att starta StorSimple-tjänst (er). |Datapath-fel |Kontakta Microsoft Support om problemet kvarstår. |
| En duplicerad IP-adress har identifierats för ' Data0 '. | |Systemet har upptäckt en konflikt för IP-adressen 10.0.0.1. Nätverks resursen "Data0" på enheten *\<device1>* är offline. Se till att den här IP-adressen inte används av någon annan entitet i det här nätverket. Om du vill felsöka nätverks problem går du till [Felsöka med cmdleten Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Kontakta nätverks administratören om du behöver hjälp med att lösa problemet. Kontakta Microsoft Support om problemet kvarstår. |
| IPv4-adressen (eller IPv6) för ' Data0 ' är offline. | |Nätverks resursen "Data0" med IP-adressen 10.0.0.1. " och prefixlängden ' 22 ' på enheten *\<device1>* är offline. Se till att växel portarna som det här gränssnittet är anslutna till fungerar. Om du vill felsöka nätverks problem går du till [Felsöka med cmdleten Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
| Det gick inte att ansluta till Autentiseringstjänsten. |Datapath-fel |Det går inte att hitta URLthat som används för att autentisera. Se till att brand Väggs reglerna innehåller de URL-mönster som anges för StorSimple-enheten. Mer information om URL-mönster i Azure Portal finns i https: \/ /aka.MS/SS-8000-Network-reqs. Om du använder Azure Government Cloud går du till URL-mönstren i https: \/ /aka.MS/ss8000-gov-Network-reqs.|

### <a name="performance-alerts"></a>Prestanda varningar

| Aviserings text | Händelse | Mer information/rekommenderade åtgärder |
|:--- |:--- |:--- |
| Enhets belastningen har överskridit <*tröskelvärdet*>. |Långsammare än förväntat svars tider. |Din enhet rapporterar användning under tung inläsning/utdata. Detta kan leda till att enheten inte fungerar som den ska. Granska de arbets belastningar som du har kopplat till enheten och kontrol lera om det finns några som kan flyttas till en annan enhet eller som inte längre behövs.|
| Det gick inte att starta StorSimple-tjänst (er). |Datapath-fel |Kontakta Microsoft Support om problemet kvarstår. |

### <a name="security-alerts"></a>Säkerhetsaviseringar

| Aviserings text | Händelse | Mer information/rekommenderade åtgärder |
|:--- |:--- |:--- |
| Microsoft Support-sessionen har påbörjats. |Support session från tredje part. |Bekräfta att åtkomsten är auktoriserad. När du har vidtagit en lämplig åtgärd tar du bort den här aviseringen från aviserings sidan. |
| Lösen ordet för <*element*> upphör att gälla om <*tids*>. |Lösen ordets giltighets tid närmar sig. |Ändra lösen ordet innan det upphör att gälla. |
| Säkerhets konfigurations information saknas för <*element-ID*>. | |Volymerna som är associerade med den här volym behållaren kan inte användas för att replikera StorSimple-konfigurationen. För att säkerställa att dina data lagras säkert, rekommenderar vi att du tar bort volym behållaren och alla volymer som är associerade med volym containern. När du har vidtagit en lämplig åtgärd tar du bort den här aviseringen från aviserings sidan. |
| <*antalet*> inloggnings försök misslyckades för <*element-ID*>. |Flera misslyckade inloggnings försök. |Enheten kan vara utsatt för en attack eller en behörig användare försöker ansluta med ett felaktigt lösen ord.<ul><li>Kontakta dina auktoriserade användare och kontrol lera att de här försöken kommer från en legitim källa. Om du fortsätter att se ett stort antal misslyckade inloggnings försök bör du inaktivera fjärrhantering och kontakta nätverks administratören. När du har vidtagit en lämplig åtgärd tar du bort den här aviseringen från aviserings sidan.</li><li>Kontrol lera att Snapshot Manager instanserna är konfigurerade med rätt lösen ord. När du har vidtagit en lämplig åtgärd tar du bort den här aviseringen från aviserings sidan.</li></ul>Mer information finns i [ändra ett förfallet enhets lösen ord](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password). |
| Ett eller flera fel inträffade när krypterings nyckeln för tjänst data ändrades. | |Fel inträffade när krypterings nyckeln för tjänst data ändrades. När du har åtgärdat fel villkoren kan du köra `Invoke-HcsmServiceDataEncryptionKeyChange` cmdleten från Windows PowerShell-gränssnittet för StorSimple på enheten för att uppdatera tjänsten. Kontakta Microsoft-supporten om problemet kvarstår. Ta bort den här aviseringen från aviserings sidan när du har löst problemet. |

### <a name="support-package-alerts"></a>Aviseringar om support paket

| Aviserings text | Händelse | Mer information/rekommenderade åtgärder |
|:--- |:--- |:--- |
| Det gick inte att skapa support paketet. |StorSimple kunde inte generera paketet. |Försök igen. Kontakta Microsoft Support om problemet kvarstår. När du har löst problemet tar du bort den här aviseringen från aviserings sidan. |

### <a name="enclosure-environment-alerts"></a>Meddelanden om kabinett miljö

| Aviserings text | Händelse | Mer information/rekommenderade åtgärder |
|:--- |:--- |:--- |
| Maskin varu komponentens sensor för omgivande temperatur rapporterar status som misslyckad.  | Kabinett typ: huvud hölje | Den här aviseringen utlöses när omgivningen utanför temperatur runt StorSimple är över ett acceptabelt intervall. Kontrol lera omgivningens yttre temperatur eller luft flödet från nätventilationen i data centret. När temperaturen återgår till normal, rensas aviseringen automatiskt när en stund har förflutit. Kontakta Microsofts supportavdelning om problemet kvarstår.   |

## <a name="next-steps"></a>Nästa steg

Läs mer om [StorSimple-fel och fel sökning av problem med enhets distribution](storsimple-8000-troubleshoot-deployment.md).
