---
title: StorSimple Snapshot Manager användar gränssnitt | Microsoft Docs
description: Beskriver StorSimple-Snapshot Manager användar gränssnitt och förklarar hur du använder det för att hantera säkerhets kopierings jobb och säkerhets kopierings katalogen.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: c7d91892-2881-41a2-a7a2-908dc3646493
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: 35fa0fc134045585eebd21324352f9b4ff272e87
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94954247"
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>Använd StorSimple-Snapshot Manager användar gränssnitt för att hantera säkerhets kopierings jobb och säkerhets kopierings katalog

## <a name="overview"></a>Översikt
StorSimple Snapshot Manager har ett intuitivt användar gränssnitt som du kan använda för att skapa och hantera säkerhets kopior. I den här självstudien får du en introduktion till användar gränssnittet och sedan beskrivs hur du använder varje komponent. En detaljerad beskrivning av StorSimple Snapshot Manager finns i [Vad är StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>Konsol Beskrivning
Om du vill visa användar gränssnittet klickar du på ikonen StorSimple Snapshot Manager på Skriv bordet. Konsol fönstret visas, som du ser i följande bild.

![StorSimple Snapshot Managers fönster](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

Konsol fönstret innehåller fem huvud element. Klicka på lämplig länk för en fullständig beskrivning av varje element.

* [Menyrad](#menu-bar) 
* [Verktygsfältet](#tool-bar) 
* [Omfattnings fönster](#scope-pane) 
* [Resultat fönster](#results-pane) 
* [Åtgärds fönstret](#actions-pane) 

Dessutom stöder StorSimple-Snapshot Manager [tangent bords navigering och ett antal genvägar](#keyboard-navigation-and-shortcuts).

### <a name="console-accessibility"></a>Konsol tillgänglighet
Användar gränssnittet för StorSimple Snapshot Manager har stöd för de hjälpmedels funktioner som tillhandahålls av Windows-operativsystemet och Microsoft Management Console (MMC), samt vissa StorSimple Snapshot Manager – särskilda kortkommandon. 

* En beskrivning av hjälpmedels funktionerna i Windows finns i kortkommandon [för Windows](https://support.microsoft.com/kb/126449). 
* En beskrivning av hjälpmedels funktionerna i MMC finns i [hjälpmedel för mmc 3,0](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc766075(v=ws.11))
* En beskrivning av StorSimple Snapshot Manager hjälpmedels funktioner finns i [tangent bords navigering och genvägar](#keyboard-navigation-and-shortcuts).

## <a name="menu-bar"></a>Menyrad
Meny fältet överst i konsol fönstret innehåller [fil](#file-menu), [åtgärd](#action-menu), [Visa](#view-menu), [Favoriter](#favorites-menu), [fönster](#window-menu)och [Hjälp](#help-menu) menyer.

Klicka på ett objekt på Meny raden för att se en lista över tillgängliga kommandon på menyn. I följande exempel visas menyn **Visa** som valts på Meny raden.

![Visa-menyn har valts](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Arkivmenyn
Menyn **Arkiv** innehåller standard kommandon i Microsoft Management Console (MMC).

#### <a name="menu-access"></a>Meny åtkomst
Om du vill visa **Arkiv** -menyn klickar du på **fil** på Meny raden. Följande meny visas.

![Menyn StorSimple Snapshot Manager File](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Meny Beskrivning
I följande tabell beskrivs de objekt som visas på menyn **Arkiv** .

| Meny alternativ | Beskrivning |
|:--- |:--- |
| Ny |Klicka på **ny** för att skapa en ny konsol som baseras på StorSimple Snapshot Manager. |
| Öppna |Klicka på **Öppna** för att öppna en befintlig konsol. |
| Spara |Klicka på **Spara** för att spara den aktuella konsolen. |
| Spara som |Klicka på **Spara som** för att skapa en ny, döpt instans av den aktuella konsolen. Använd alternativet **Spara som** för att anpassa en vy och spara den för senare hämtning. Du kan till exempel skapa StorSimple Snapshot Manager snapin-moduler som pekar på vissa servrar. |
| Lägg till/ta bort snapin-modul |Klicka på **Lägg till/ta bort snapin-modulen** för att lägga till eller ta bort snapin-moduler och organisera noder i fönstret **omfattning** . Mer information finns [i lägga till, ta bort och ordna snapin-moduler och tillägg i MMC 3,0](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc722035(v=ws.11)). |
| Alternativ |Klicka på **alternativ** för att ändra konsol ikonen, Ange användar åtkomst lägen och behörigheter, eller ta bort konsolfiler för att öka det tillgängliga disk utrymmet. |
| Lista över fil Sök vägar |Klicka på en sökväg i den numrerade listan för att öppna en fil som du nyligen har öppnat. |
| Avsluta |Klicka på **Avsluta** för att stänga menyn **Arkiv** . |

### <a name="action-menu"></a>Åtgärds meny
Använd menyn **åtgärd** för att välja bland tillgängliga åtgärder. Vilka objekt som är tillgängliga beror på vilket val du gör i fönstret **omfattning** eller **resultat** .

#### <a name="menu-access"></a>Meny åtkomst
Gör något av följande om du vill visa **Åtgärds** menyn:

* Högerklicka på ett objekt i fönstret **omfång** eller **resultat** fönstret.
* Välj ett objekt i rutan **omfattning** eller **resultat** och klicka sedan på **åtgärd** på Meny raden. 

Om du till exempel väljer den översta noden i fönstret **omfattning** och högerklickar eller klickar på **åtgärd** i meny raden visas följande meny.

![Åtgärds menyn StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

**Åtgärds fönstret (** till höger i konsolen) innehåller samma åtgärds lista som **Åtgärds** menyn. Dessutom innehåller **Åtgärds** fönstret **Visa** meny alternativ som gör att du kan skapa en anpassad vy av **resultat** fönstret.

![Åtgärds fönstret med Visa-menyn öppen](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Meny Beskrivning
Följande tabell innehåller en alfabetisk lista över StorSimple-Snapshot Manager åtgärder. 

* I kolumnen **åtgärd** visas åtgärder som du kan utföra på noder och resultat. 
* **Navigerings** kolumnen förklarar hur du visar lämplig **Åtgärds** meny så att du kan välja åtgärden. Vissa åtgärder visas i flera **Åtgärds** menyer. Välj ett **navigerings** alternativ i punkt listan för dessa åtgärder. 
* I kolumnen **Beskrivning** beskrivs hur du använder varje åtgärd på **Åtgärds menyn eller åtgärds** fönstret och förklarar vad det gör.

> [!NOTE]
> Åtgärds **fönstret** och **Åtgärds** menyerna innehåller ytterligare alternativ, till exempel **Visa**, **nytt fönster**, **Uppdatera**, **Exportera lista** och **Hjälp**. De här alternativen är tillgängliga som en del av MMC och är inte bara för StorSimple Snapshot Manager. Tabellen innehåller beskrivningar av de här alternativen.
> 
> 

| Action | Navigering | Beskrivning |
|:--- |:--- |:--- |
| Autentisera |Klicka på noden **enheter** och högerklicka på en enhet i **resultat** fönstret. |Klicka på **autentisera** för att ange det lösen ord som du konfigurerade för enheten. |
| Klona |Expandera **säkerhets kopierings katalog**, expandera **moln ögonblicks bilder**, klicka på en säkerhets kopia och välj sedan en volym i **resultat** fönstret. |Klicka på **klona** för att skapa en kopia av en ögonblicks bild av molnet och lagra den på en plats som du anger. |
| Konfigurera en enhet |Högerklicka på noden **enheter** . |Klicka på **Konfigurera en enhet** om du vill konfigurera en enskild enhet eller flera enheter för att ansluta till Windows-värden. |
| Skapa säkerhets kopierings princip |Gör något av följande:<ul><li>Högerklicka på **säkerhets kopierings principer**.</li><li>Klicka på eller expandera **volym grupper** och högerklicka sedan på en volym grupp.</li><li>Klicka eller expandera **säkerhets kopierings katalogen** och högerklicka sedan på en volym grupp.</li></ul> |Klicka på **skapa säkerhets kopierings princip** om du vill konfigurera en schemalagd säkerhets kopiering för en volym grupp. |
| Skapa volym grupp |Gör något av följande:<ul><li>Klicka på noden **volymer** och högerklicka sedan på en volym i **resultat** fönstret.</li><li>Högerklicka på noden **volym grupper** .</li></ul> |Klicka på **Skapa volym grupp** för att tilldela volymer till en volym grupp. |
| Ta bort |Klicka på en nod eller ett resultat (detta objekt visas på många **Åtgärds**  **menyer och** åtgärds fönster.) |Klicka på **ta bort** för att ta bort den nod eller det resultat som du har valt. Bekräfta eller Avbryt borttagningen när dialog rutan Bekräfta visas. |
| Information |Klicka på noden **enheter** och högerklicka sedan på en enhet i **resultat** fönstret. |Klicka på **information** om du vill visa konfigurations information för en enhet. |
| Redigera |Klicka på **säkerhets kopierings principer** och högerklicka sedan på en princip i **resultat** fönstret. |Klicka på **Redigera** om du vill ändra schemat för säkerhets kopiering för en volym grupp. |
| Exportera lista |Klicka på en nod eller ett resultat (detta objekt visas på alla åtgärds **menyer och** **Åtgärds** fönster.) |Klicka på **Exportera lista** för att spara en lista i en fil med kommaavgränsade värden (CSV). Sedan kan du importera den här filen till ett kalkyl blads program för analys. |
| Help |Klicka på en nod eller ett resultat. (Det här objektet visas på alla åtgärds **menyer och** **Åtgärds** fönster.) |Klicka på **Hjälp** för att öppna onlinehjälpen i ett separat webbläsarfönster. |
| Nytt fönster från här |Klicka på en nod eller ett resultat (detta objekt visas på alla åtgärds **menyer och** **Åtgärds** fönster.) |Klicka på **nytt fönster** härifrån för att öppna ett nytt StorSimple Snapshot Manager-fönster. |
| Uppdatera |Klicka på en nod eller ett resultat (detta objekt visas på alla åtgärds **menyer och** **Åtgärds** fönster.) |Klicka på **Uppdatera** för att uppdatera den aktuella StorSimple Snapshot Manager fönstret. |
| Uppdatera enhet |Klicka på noden **enheter** och högerklicka på en enhet i **resultat** fönstret. |Klicka på **Uppdatera enhet** för att synkronisera en angiven ansluten enhet med StorSimple Snapshot Manager. |
| Uppdatera enheter |Högerklicka på noden **enheter** . |Klicka på **Uppdatera enheter** för att synkronisera listan över anslutna enheter med StorSimple Snapshot Manager. |
| Genomsök volymer på nytt |Högerklicka på noden **volymer** . |Klicka på **Genomsök volymer igen** för att uppdatera listan över volymer som visas i **resultat** fönstret. |
| Återställ |Expandera **säkerhets kopierings katalog**, expandera en volym grupp, expandera **lokala ögonblicks bilder** eller **moln ögonblicks bilder** och högerklicka sedan på en säkerhets kopia. |Klicka på **Återställ** för att ersätta de aktuella volym grupps data med data från den valda säkerhets kopian. |
| Ta säkerhets kopia |Gör något av följande:<ul><li>Expandera **volym grupper** och högerklicka sedan på en volym grupp.</li><li>Expandera **säkerhets kopierings katalogen** och högerklicka sedan på en volym grupp.</li></ul> |Klicka på **ta säkerhets kopiering** för att starta ett säkerhets kopierings jobb direkt. |
| Växla visning av importer |Högerklicka på den översta noden i rutan **omfång** (noden **StorSimple Snapshot Manager** i exemplen). |Klicka på **Växla importer visas** för att visa eller dölja volym grupperna och tillhör ande säkerhets kopior som har importer ATS från instrument panelen för StorSimple-Enhetshanteraren. |

### <a name="view-menu"></a>Visa-menyn
Använd menyn **Visa** för att skapa en anpassad vy av **resultat** fönstrets innehåll. **Visa** -menyn innehåller **kolumner för att lägga till/ta bort kolumner** och **Anpassa** alternativ.

#### <a name="menu-access"></a>Meny åtkomst
Du kan öppna menyn **Visa** på Meny raden eller i **Åtgärds** fönstret.

![StorSimple Snapshot Manager Visa-menyn](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Meny Beskrivning
I följande tabell beskrivs de objekt som visas på menyn **Visa** .

| Meny alternativ | Beskrivning |
|:--- |:--- |
| Lägg till/ta bort kolumner |Klicka på **Lägg till/ta bort kolumner** om du vill lägga till eller ta bort kolumner i **resultat** fönstret. |
| Anpassa |Klicka på **Anpassa** för att visa eller dölja objekt i StorSimple Snapshot Manager konsol fönstret. |

### <a name="favorites-menu"></a>Favoriter-menyn
Använd menyn **Favoriter** för att lägga till, ta bort och ordna sidvyer och aktiviteter som du använder ofta. 

#### <a name="menu-access"></a>Meny åtkomst
Du kan komma åt **Favoriter** -menyn på Meny raden.

![StorSimple Snapshot Manager Favoriter-menyn](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Meny Beskrivning
I följande tabell beskrivs de objekt som visas på **Favoriter** -menyn.

| Meny alternativ | Beskrivning |
|:--- |:--- |
| Lägg till i favoriter |Klicka på **Lägg till i favoriter** för att lägga till den aktuella vyn i listan över favoriter. |
| Ordna favoriter |Klicka på **Ordna Favoriter** för att ordna innehållet i mappen Favoriter. |

### <a name="window-menu"></a>Fönster-menyn
Använd menyn **fönster** för att lägga till och arrangera om StorSimple Snapshot Manager konsol fönster.

#### <a name="menu-access"></a>Meny åtkomst
Du kan komma åt menyn **fönster** på Meny raden.

![StorSimple Snapshot Manager fönster-menyn](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

I den numrerade listan längst ned på menyn visas de fönster som är öppna för tillfället. Klicka på ett fönster i listan för att visa fönstret i förgrunden. 

#### <a name="menu-description"></a>Meny Beskrivning
I följande tabell beskrivs de objekt som visas på menyn fönster.

| Meny alternativ | Beskrivning |
|:--- |:--- |
| Nytt fönster |Klicka på **nytt fönster** för att öppna ett nytt konsol fönster (förutom det befintliga fönstret). |
| Sprid |Klicka på **kaskad** för att visa öppna konsol fönster i ett sammanhängande format. |
| Panel vågrätt |Klicka på **panel vågrätt** för att visa öppna konsol fönster i ett panel format (eller rutnät). |
| Ordna ikoner |Om du har flera konsol fönster öppna och spridda över ditt skriv bord, minimerar du dem och klickar sedan på **Ordna ikoner** för att ordna dem i en vågrät rad längst ned på skärmen. |

### <a name="help-menu"></a>Hjälp-menyn
Använd **Hjälp** -menyn om du vill visa tillgänglig Onlinehjälp för StorSimple Snapshot Manager och MMC. Du kan också visa information om MMC-och StorSimple-Snapshot Manager program varu versioner som är installerade på systemet. 

Du kan komma åt **Hjälp** -menyn på Meny raden. Du kan också komma åt StorSimple-Snapshot Manager Hjälp ämnen från fönstret **åtgärder** .

![StorSimple Snapshot Manager Hjälp-menyn](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Meny Beskrivning
I följande tabell beskrivs de objekt som visas på Hjälp-menyn.

| Meny alternativ | Beskrivning |
|:--- |:--- |
| Hjälp om StorSimple Snapshot Manager |Klicka på **hjälp på StorSimple Snapshot Manager** för att öppna StorSimple Snapshot Manager hjälp i ett separat fönster. |
| Hjälp avsnitt |Klicka på **Hjälp avsnitt** om du vill öppna hjälpen för MMC i ett separat fönster. |
| TechCenter-webbplats |Klicka på **TechCenter-webbplats** för att öppna Start sidan för Microsoft TechNet Tech Center i ett separat fönster. |
| Om Microsoft Management Console |Klicka **om Microsoft Management Console** för att se vilken version av Microsoft Management Console som är installerad på datorn. |
| Om StorSimple Snapshot Manager |Klicka på **om StorSimple Snapshot Manager** för att se vilken version av snapin-modulen som är installerad på datorn. |

## <a name="tool-bar"></a>Verktygsfältet
Verktygsfältet, som finns under meny raden, innehåller navigerings-och aktivitets ikoner. Varje ikon är en genväg till en speciell uppgift.

### <a name="icon-descriptions"></a>Ikon beskrivningar
I följande tabell beskrivs de ikoner som visas i verktygsfältet. 

| Ikon | Beskrivning |
|:--- |:--- |
| ![Vänsterpil](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |Klicka på vänsterpilen för att återgå till föregående sida. |
| ![Högerpil](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |Klicka på högerpilen för att gå till nästa sida (om pilen är grå är åtgärden inte tillgänglig). |
| ![Uppåt-ikon](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |Klicka på upp-ikonen för att gå upp en nivå i konsol trädet ( **omfattnings** fönstret). |
| ![Visa/Dölj konsol träd](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |Klicka på ikonen Visa/Dölj konsol träd för att visa eller dölja **omfattnings** fönstret. |
| ![Exportera lista](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |Klicka på Exportera List-ikonen för att exportera en lista till en CSV-fil som du anger. |
| ![Hjälp ikon](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Klicka på hjälp ikonen för att öppna ett online-hjälp avsnitt för MMC. |
| ![Visa/Dölj åtgärds fönstret](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |Klicka på ikonen Visa/Dölj **Åtgärds** fönstret för att visa eller dölja **Åtgärds** fönstret. |

## <a name="scope-pane"></a>Omfattnings fönster
Fönstret **omfattning** är fönstret längst till vänster i STORSIMPLE Snapshot Manager UI. Den innehåller konsol trädet (eller noden) och är den primära navigerings mekanismen för StorSimple Snapshot Manager. 

### <a name="scope-pane-structure"></a>Struktur för omfattnings fönstret
Fönstret **omfattning** innehåller en serie objekt (noder) som är ordnade i en träd struktur. 

![Omfattnings fönster](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* Om du vill expandera eller komprimera en nod klickar du på pilen bredvid nodnamn.
* Om du vill visa status eller innehåll för en nod klickar du på namnet på noden. Informationen visas i **resultat** fönstret. 

Rutan **omfattning** innehåller följande noder: 

* [Noden enheter](#devices-node) 
* [Noden volymer](#volumes-node) 
* [Noden volym grupper](#volume-groups-node) 
* [Noden säkerhets kopierings principer](#backup-policies-node) 
* [Nod för säkerhets kopierings katalog](#backup-catalog-node) 
* [Noden jobb](#jobs-node) 

### <a name="scope-pane-tasks"></a>Åtgärder i omfattnings fönstret
Du kan använda fönstret **omfattning** för att slutföra en åtgärd på en speciell nod. Gör något av följande om du vill välja en uppgift:

* Högerklicka på noden och välj sedan aktiviteten i menyn som visas.
* Klicka på noden och sedan på **åtgärd** på Meny raden. Välj uppgiften i menyn som visas.
* Klicka på noden och välj sedan åtgärden i **Åtgärds** fönstret.

När du väljer en nod och använder någon av dessa metoder för att se en uppgifts lista visas bara de åtgärder som kan utföras på noden.

### <a name="devices-node"></a>Noden enheter
Noden **enheter** representerar de StorSimple-enheter och StorSimple-virtuella enheter som är anslutna till StorSimple Snapshot Manager. Välj den här noden för att ansluta och konfigurera en enhet och importera tillhör ande volymer, volym grupper och befintliga säkerhets kopior. Flera enheter kan anslutas till en enda värd.

* Om du vill expandera noden klickar du på pilen bredvid **enheter**.
* Om du vill se en meny med tillgängliga åtgärder högerklickar du på noden **enheter** eller högerklickar på någon av noderna som visas i den utökade vyn.
* Om du vill se en lista över konfigurerade enheter klickar du på **enheter** i fönstret **omfång** . Listan över enheter, tillsammans med information om varje enhet, visas i **resultat** fönstret.

### <a name="volumes-node"></a>Noden volymer
Noden **volymer** representerar de enheter som motsvarar de volymer som monteras av värden, inklusive de som identifierats via iSCSI och de som identifierats via en enhet. Använd den här noden för att visa listan över tillgängliga volymer och tilldela enskilda volymer till volym grupper.

* Om du vill expandera noden klickar du på pilen bredvid **volymer**.
* Om du vill se en meny med tillgängliga åtgärder högerklickar du på noden **volymer** eller högerklickar på någon av noderna som visas i den utökade vyn.
* Om du vill se en lista över volymer klickar du på **volymer** i fönstret **omfång** . Listan över volymer, tillsammans med information om varje volym, visas i **resultat** fönstret.

### <a name="volume-groups-node"></a>Noden volym grupper
Volym grupper kallas även för konsekvens grupper. Varje volym grupp är en pool med programrelaterade volymer som hjälper till att säkerställa programmets konsekvens under säkerhets kopierings åtgärder. Använd noden **volym grupper** för att konfigurera dessa grupper och för att utföra interaktiva säkerhets kopieringar eller skapa säkerhets kopierings scheman. 

* Om du vill expandera noden klickar du på pilen bredvid **volym grupper**.
* Om du vill se en meny med tillgängliga åtgärder högerklickar du på noden **volym grupper** eller högerklickar på någon av noderna som visas i den utökade vyn.
* Om du vill se en lista över volym grupper klickar du på **volym grupper** i fönstret **omfång** . Listan över volym grupper, tillsammans med information om varje volym grupp, visas i **resultat** fönstret.

### <a name="backup-policies-node"></a>Noden säkerhets kopierings principer
Säkerhets kopierings principer är jobb scheman för lokala och molnbaserade ögonblicks bilder. Använd noden **säkerhets kopierings principer** för att ange hur ofta en säkerhets kopia ska skapas och hur länge en säkerhets kopia ska behållas. 

* Om du vill expandera noden klickar du på pilen bredvid **säkerhets kopierings principer**.
* Om du vill se en meny med tillgängliga åtgärder högerklickar du på noden **säkerhets kopierings principer** eller högerklickar på någon av noderna som visas i den utökade vyn.
* Om du vill se en lista över säkerhets kopierings principer klickar du på **säkerhets kopierings principer** i fönstret **omfång** . Listan över säkerhets kopierings principer, tillsammans med information om varje princip, visas i **resultat** fönstret.

> [!NOTE]
> Du kan behålla högst 64 säkerhets kopieringar.


### <a name="backup-catalog-node"></a>Nod för säkerhets kopierings katalog
Noden **säkerhets kopierings katalog** innehåller listor över säkerhets kopior på plats och utanför platsen för Azure StorSimple-volymer. Den här noden är ordnad efter volym grupp och varje volym grupp behållare innehåller separata strukturer för lokala ögonblicks bilder (den **lokala ögonblicks bilden** s nod) och moln ögonblicks bilder (noden **moln ögonblicks bilder** ). När varje volym grupp behållare expanderas visas alla lyckade säkerhets kopieringar som har gjorts interaktivt eller av en konfigurerad princip.

* Om du vill expandera noden klickar du på pilen bredvid **säkerhets kopierings katalogen**.
* Om du vill se en meny med tillgängliga åtgärder högerklickar du på noden **säkerhets kopierings katalog** eller högerklickar på någon av noderna som visas i den utökade vyn.
* Om du vill se en lista över ögonblicks bilder av säkerhets kopior klickar du på **säkerhets kopierings katalog** i fönstret **omfattning** . Listan över ögonblicks bilder, tillsammans med information om varje ögonblicks bild, visas i **resultat** fönstret.

### <a name="local-snapshots-node"></a>Nod för lokala ögonblicks bilder
Noden **lokala ögonblicks bilder** visar lokala ögonblicks bilder för en speciell volym grupp. Noden finns under noden **säkerhets kopierings katalog** i fönstret **omfång** . Lokala ögonblicks bilder är tidpunkts kopior av volym data som lagras på Azure StorSimple-enheten. Den här typen av säkerhets kopiering kan vanligt vis skapas och återställas snabbt. Du kan använda en lokal ögonblicks bild som en lokal säkerhets kopia.

* Om du vill expandera noden klickar du på pilen bredvid **lokala ögonblicks bilder**.
* Om du vill se en meny med tillgängliga åtgärder högerklickar du på noden **lokala ögonblicks bilder** eller högerklickar på någon av noderna som visas i den utökade vyn.
* Om du vill se en lista över lokala ögonblicks bilder klickar du på **lokala ögonblicks bilder** i fönstret **omfång** . Listan över ögonblicks bilder, tillsammans med information om varje ögonblicks bild, visas i **resultat** fönstret.

### <a name="cloud-snapshots-node"></a>Noden moln ögonblicks bilder
I noden **moln ögonblicks bilder** visas moln ögonblicks bilder för en speciell volym grupp. Noden finns under noden **säkerhets kopierings katalog** i fönstret **omfång** . Moln ögonblicks bilder är tidpunkts kopior av volym data som lagras i molnet. En moln ögonblicks bild motsvarar en ögonblicks bild som replikeras på ett annat lagrings system utanför platsen. Moln ögonblicks bilder är särskilt användbara i scenarier med haveri beredskap.

* Om du vill expandera noden klickar du på pilen bredvid **moln ögonblicks bilder**.
* Om du vill se en meny med tillgängliga åtgärder högerklickar du på noden **moln ögonblicks bilder** eller högerklickar på någon av noderna som visas i den utökade vyn.
* Om du vill se en lista över ögonblicks bilder av molnet klickar du på **moln ögonblicks bilder** i fönstret **omfång** . Listan över ögonblicks bilder, tillsammans med information om varje ögonblicks bild, visas i **resultat** fönstret.

### <a name="jobs-node"></a>Noden jobb
**Jobb** -noden innehåller information om schemalagda, pågående och nyligen slutförda säkerhets kopierings jobb. 

* Om du vill expandera noden klickar du på pilen bredvid **jobb**.
* Om du vill se en meny med tillgängliga åtgärder högerklickar du på noden **jobb** eller högerklickar på någon av noderna som visas i den utökade vyn.
* Om du vill se en lista över schemalagda jobb expanderar du noden **jobb** och klickar sedan på **schemalagd**. Listan över tidigare konfigurerade jobb och information om varje jobb visas i **resultat** fönstret. 
* Om du vill se en lista över de senaste slutförda jobben expanderar du noden **jobb** och klickar sedan på de **senaste 24 timmarna**. En lista över jobb som har slutförts under de senaste 24 timmarna visas i **resultat** fönstret. **Resultat** fönstret innehåller också information om varje slutfört jobb.
* Om du vill se en lista över jobb som körs för tillfället expanderar du noden **jobb** och klickar sedan på **Kör**. En lista över jobb som körs och information om varje jobb visas i **resultat** fönstret.

## <a name="results-pane"></a>Resultat fönster
**Resultat** fönstret är mitten-fönstret i STORSIMPLE Snapshot Manager UI. Den innehåller listor och detaljerad statusinformation för den nod som du valde i fönstret **omfång** .

### <a name="example"></a>Exempel
Om du vill se följande exempel klickar du på noden **volym grupper** i fönstret **omfång** . I **resultat** fönstret visas en lista över volym grupper med information om varje grupp.

![Resultat fönster](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

Du kan konfigurera informationen som visas i **resultat** fönstret: Högerklicka på en nod i fönstret **omfång** , klicka på **Visa** och klicka sedan på **Lägg till/ta bort kolumner**.

## <a name="actions-pane"></a>Åtgärds fönstret
Fönstret **åtgärder** är det högra fönstret i STORSIMPLE Snapshot Manager UI. Den innehåller en meny med åtgärder som du kan utföra på den nod, vy eller data som du väljer i fönstret **omfattning** eller **resultat** . Fönstret **åtgärder** innehåller samma kommandon som de **Åtgärds** menyer som är tillgängliga för objekt i fönstret **omfattning** och **resultat** . En beskrivning av varje åtgärd finns i tabellen i **Åtgärds** menyn.

### <a name="examples"></a>Exempel
Om du vill se följande exempel i rutan **omfång** expanderar du noden **jobb** och klickar på **schemalagd**. I fönstret **åtgärder** visas tillgängliga åtgärder för den **schemalagda** noden.

![Exempel på schemalagda jobb i åtgärds fönstret](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

Om du vill se fler alternativ går du till fönstret **omfång** , expanderar noden **jobb** , klickar på **schemalagd** och klickar sedan på ett schemalagt jobb i **resultat** fönstret. I fönstret **åtgärder** visas tillgängliga åtgärder för det schemalagda jobbet, som du ser i följande exempel.

![Åtgärds rutan jobb i åtgärds exempel](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Tangent bords navigering och genvägar
StorSimple Snapshot Manager aktiverar hjälpmedels funktionerna i Windows operativ system och Microsoft Management Console (MMC). Den innehåller också vissa tangent bords navigerings funktioner och genvägar som är speciella för StorSimple-Snapshot Manager, enligt beskrivningen i följande avsnitt.

* [Tangent bords navigerings nycklar](#keyboard-navigation-keys) 
* [Kortkommandon för meny raden](#menu-bar-shortcut-keys) 
* [Kortkommandon för omfattnings fönstret](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Tangent bords navigerings nycklar
I följande tabell beskrivs de nycklar som du kan använda för att navigera i StorSimple Snapshot Manager användar gränssnitt. 

| Navigerings nyckel | Action |
|:--- |:--- |
| NEDPIL-tangenten |Använd nedåtpilen för att flytta lodrätt till nästa objekt på en meny eller i ett fönster. |
| Ange |Tryck på RETUR-tangenten för att slutföra en åtgärd och fortsätt sedan till nästa steg. Du kan till exempel trycka på RETUR för att välja **Nästa**, **OK** eller **skapa** och sedan gå till nästa steg i en guide. |
| Esc |Tryck på ESC-tangenten för att stänga en meny eller för att avbryta och stänga en sida. |
| F1 |Tryck på F1-tangenten om du vill visa ett hjälp avsnitt för det aktiva fönstret. |
| F5 |Tryck på F5 för att uppdatera en nod. |
| F6 |Tryck på F6-tangenten för att flytta från fönstret **omfång** till **resultat** fönstret. |
| F10 |Tryck på tangenten F10 för att gå till meny raden. |
| Vänsterpil |Använd vänsterpil-tangenten för att flytta vågrätt från meny rads alternativet till föregående alternativ. När du flyttar till föregående objekt i meny raden visas åtgärds menyn (eller kontexten) för föregående objekt. |
| Högerpil |Använd högerpilen för att flytta vågrätt från ett meny rads alternativ till nästa. När du flyttar till nästa objekt på Meny raden visas åtgärds menyn (eller kontexten) för det nya objektet. |
| TABB-nyckel |Använd tabbtangenten för att flytta till nästa-fönstret i-konsolen eller till nästa markering eller text ruta på en sida. |
| UPPIL-nyckel |Använd uppil-tangenten för att flytta lodrätt till föregående objekt på en meny eller i ett fönster. |

### <a name="menu-bar-shortcut-keys"></a>Kortkommandon för meny raden
I följande tabell beskrivs kortkommandona på Meny raden. När du har tryckt på kortkommandon och menyn öppnas kan du använda kortkommandon för menyer (de understrukna tangenterna på menyn). Om du vill ha mer information om meny raden går du till [meny raden](#menu-bar).

| Genvägar | Resultat | Snabb tangent för meny | Resultat |
|:--- |:--- |:--- |:--- |
| ALT + F |Öppnar **Arkiv** -menyn. |N |Öppnar en ny konsol instans. |
|  |O |Öppnar sidan **administrations verktyg** . | |
|  |S |Sparar StorSimple-Snapshot Manager-konsolen. | |
|  |A |Öppnar sidan **Spara som** . | |
|  |M |Öppnar sidan **Lägg till/ta bort snapin-modul** . | |
|  |P |Öppnar sidan **alternativ** . | |
|  |H |Öppnar direkt hjälpen. | |
| ALT + A |Öppnar **Åtgärds** menyn. |I |Aktiverar och inaktiverar alternativet för att importera visning. |
|  |W |Öppnar en ny StorSimple Snapshot Manager-konsol. | |
|  |F |Uppdaterar StorSimple Snapshot Manager-konsolen. | |
|  |L |Öppnar sidan **Exportera lista** . | |
|  |H |Öppnar direkt hjälpen. | |
| ALT + V |Öppnar **Visa** -menyn. |A |Öppnar sidan **Lägg till/ta bort kolumner** . |
|  |U |Öppnar sidan **Anpassa vy** . | |
| ALT+O |Öppnar **Favoriter** -menyn. |A |Öppnar sidan **Lägg till i favoriter** . |
|  |O |Öppnar sidan **Ordna Favoriter** . | |
| ALT + W |Öppnar menyn **fönster** . |N |Öppnar ett annat StorSimple Snapshot Manager-fönster. |
|  |C |Visar alla öppna konsol fönster i ett sammanhängande format. | |
|  |T |Visar alla öppna konsol fönster i ett rutnäts mönster. | |
|  |I |Ordnar ikonerna i en vågrät rad längst ned på skärmen. | |
| ALT + H |Öppnar **Hjälp** -menyn. |H |Öppnar direkt hjälpen. |
|  |T |Öppnar webb sidan Microsoft TechNet Tech Center. | |
|  |A |Öppnar sidan **om Microsoft Management Console** . | |

### <a name="scope-pane-shortcut-keys"></a>Kortkommandon för omfattnings fönstret
I följande tabeller visas kortkommandona för varje nod i **omfattnings** fönstret. 

* [Kortkommandon för enhets nod](#devices-node-shortcut-keys)
* [Kortkommandon för noden volymer](#volumes-node-shortcut-keys)
* [Snabbkommandon i noden volym grupper](#volume-groups-node-shortcut-keys)
* [Kortkommandon för noden säkerhets kopierings principer](#backup-policies-node-shortcut-keys)
* [Kortkommandon för noden katalog för säkerhets kopiering](#backup-catalog-node-shortcut-keys)
* [Kortkommandon för jobb nod](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Kortkommandon för enhets nod
| Snabb meny | Resultat |
|:--- |:--- |
| C |Öppnar sidan **Konfigurera en enhet** . |
| D |Uppdaterar listan över enheter och enhets information. |
| V |Öppnar **Visa** -menyn. |
| W |Öppnar en ny StorSimple-Snapshot Manager-konsol fokuserad på noden **information** . |
| F |Uppdaterar StorSimple Snapshot Manager-konsolen. |
| L |Öppnar sidan **Exportera lista** . |
| H |Öppnar direkt hjälpen. |

#### <a name="volumes-node-shortcut-keys"></a>Kortkommandon för noden volymer
| Snabb meny | Resultat |
|:--- |:--- |
| V |Uppdaterar listan över volymer. |
| V (tryck två gånger) |Öppnar **Visa** -menyn. |
| W |Öppnar en ny StorSimple-Snapshot Manager-konsol fokuserad på noden **volymer** . |
| F |Uppdaterar StorSimple Snapshot Manager-konsolen. |
| L |Öppnar sidan **Exportera lista** . |
| H |Öppnar direkt hjälpen. |

#### <a name="volume-groups-node-shortcut-keys"></a>Snabbkommandon i noden volym grupper
| Snabb meny | Resultat |
|:--- |:--- |
| G |Öppnar sidan **skapa en volym grupp** . |
| V |Öppnar **Visa** -menyn. |
| W |Öppnar en ny StorSimple-Snapshot Manager-konsol fokuserad på **volym grupper** -noden. |
| F |Uppdaterar StorSimple Snapshot Manager-konsolen. |
| L |Öppnar sidan **Exportera lista** . |
| H |Öppnar direkt hjälpen. |

#### <a name="backup-policies-node-shortcut-keys"></a>Kortkommandon för noden säkerhets kopierings principer
| Snabb meny | Resultat |
|:--- |:--- |
| B |Öppnar sidan **skapa en princip** . |
| V |Öppnar **Visa** -menyn. |
| W |Öppnar en ny StorSimple-Snapshot Manager-konsol fokuserad på **volym grupper** -noden. |
| F |Uppdaterar StorSimple Snapshot Manager-konsolen. |
| L |Öppnar sidan **Exportera lista** . |
| H |Öppnar direkt hjälpen. |

#### <a name="backup-catalog-node-shortcut-keys"></a>Kortkommandon för noden katalog för säkerhets kopiering
| Snabb meny | Resultat |
|:--- |:--- |
| W |Öppnar en ny StorSimple-Snapshot Manager-konsol fokuserad på **volym grupper** -noden. |
| F |Uppdaterar StorSimple Snapshot Manager-konsolen. |
| H |Öppnar direkt hjälpen. |

#### <a name="jobs-node-shortcut-keys"></a>Kortkommandon för jobb nod
| Snabb meny | Resultat |
|:--- |:--- |
| V |Öppnar **Visa** -menyn. |
| W |Öppnar en ny StorSimple-Snapshot Manager-konsol fokuserad på **jobb** -noden. |
| F |Uppdaterar StorSimple Snapshot Manager-konsolen. |
| L |Öppnar sidan **Exportera lista** . |
| H |Öppnar onlinehjälp |

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använder StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Lär dig hur du [använder StorSimple Snapshot Manager för att ansluta och hantera enheter](storsimple-snapshot-manager-manage-devices.md).