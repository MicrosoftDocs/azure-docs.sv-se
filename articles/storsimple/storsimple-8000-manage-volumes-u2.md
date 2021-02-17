---
title: Hantera StorSimple-volymer (uppdatering 3)
description: Förklarar hur du lägger till, ändrar, övervakar och tar bort StorSimple-volymer och hur du kopplar från dem om det behövs.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 12/09/2017
ms.author: alkohli
ms.openlocfilehash: 3d649b54b544c120198963f8094764e9590e20b2
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547638"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-volumes-update-3-or-later"></a>Använda tjänsten StorSimple Enhetshanteraren för att hantera volymer (uppdatering 3 eller senare)

## <a name="overview"></a>Översikt

I den här självstudien beskrivs hur du använder tjänsten StorSimple Enhetshanteraren för att skapa och hantera volymer på StorSimple 8000-serie enheter som kör uppdatering 3 och senare.

Tjänsten StorSimple Enhetshanteraren är ett tillägg i Azure Portal som gör att du kan hantera din StorSimple-lösning från ett enda webb gränssnitt. Använd Azure Portal för att hantera volymer på alla enheter. Du kan också skapa och hantera StorSimple-tjänster, hantera enheter, säkerhets kopierings principer och säkerhets kopierings katalogen samt visa aviseringar.

## <a name="volume-types"></a>Volym typer

StorSimple-volymer kan vara:

* **Lokalt fästa volymer**: data i dessa volymer förblir på den lokala StorSimple-enheten hela tiden.
* **Skiktade volymer**: data i dessa volymer kan spilla till molnet.

En lagrings volym är en typ av nivå volym. Den större segment storleken för deduplicering som används för lagrings volymer gör att enheten kan överföra större data segment till molnet.

Om det behövs kan du ändra volym typen från lokal till nivå eller från nivå till lokal. Mer information finns i [ändra volym typ](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Lokalt fixerade volymer

Lokalt fästa volymer är helt etablerade volymer som inte hanterar data till molnet, vilket säkerställer lokala garantier för primära data, oberoende av moln anslutningen. Data på lokalt fästa volymer dedupliceras och komprimeras inte. ögonblicks bilder av lokalt fästa volymer dedupliceras dock. 

Lokalt fästa volymer är helt etablerade. Därför måste du ha tillräckligt med utrymme på enheten när du skapar dem. Du kan etablera lokalt fästa volymer upp till en maximal storlek på 8 TB på StorSimple 8100-enheten och 20 TB på 8600-enheten. StorSimple reserverar det återstående lokala utrymmet på enheten för ögonblicks bilder, metadata och data bearbetning. Du kan öka storleken på en lokalt fäst volym till maximalt tillgängligt utrymme, men du kan inte minska storleken på en volym när den har skapats.

När du skapar en lokalt fäst volym minskas det tillgängliga utrymmet för att skapa skiktade volymer. Omvänt är också sant: om du har befintliga skiktade volymer kommer utrymmet som är tillgängligt för att skapa lokalt fästa volymer att vara lägre än de maximala gränser som anges ovan. Mer information om lokala volymer finns i [vanliga frågor och svar om lokalt fästa volymer](storsimple-8000-local-volume-faq.md).

### <a name="tiered-volumes"></a>Nivåindelade volymer

Skiktade volymer är tunt allokerade volymer där data som används ofta är lokala på enheten och mindre ofta använda data sker automatiskt på nivå av molnet. Tunn allokering är en virtualiseringsteknik där tillgängligt lagrings utrymme verkar överskrida fysiska resurser. I stället för att reservera tillräckligt med lagrings utrymme i förväg, använder StorSimple tunn etablering för att allokera tillräckligt med utrymme för att uppfylla de aktuella kraven. Den elastiska typen av moln lagring underlättar den här metoden eftersom StorSimple kan öka eller minska moln lagringen för att möta föränderliga krav.

Om du använder den skiktade volymen för arkivering av data markerar du kryss rutan **Använd den här volymen för lagrings data som används mindre ofta** för att ändra deduplicerad segment storlek för volymen till 512 kB. Om du inte väljer det här alternativet kommer motsvarande volym på nivå att använda en segment storlek på 64 KB. En större segmentstorlek för deduplicering låter enheten snabba på överföring av segmentstorleken tillåter enheten att påskynda överföringen av stora mängder arkiveringsdata till molnet.


### <a name="provisioned-capacity"></a>Etablerad kapacitet

Se följande tabell för den högsta etablerade kapaciteten för varje enhet och typ av volym. (Observera att lokalt fästa volymer inte är tillgängliga på en virtuell enhet.)

| Typ | Maximal volym storlek på nivå | Maximal storlek för lokalt fixerad volym |
| --- | --- | --- |
| **Fysiska enheter** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Virtuella enheter** | | |
| 8010 |30 TB |Ej tillämpligt |
| 8020 |64 TB |Ej tillämpligt |

## <a name="the-volumes-blade"></a>Bladet volymer

Med bladet **volymer** kan du hantera lagrings volymer som är etablerade på Microsoft Azure StorSimple enhet för dina initierare (servrar). Den visar listan över volymer på de StorSimple-enheter som är anslutna till din tjänst.

 ![Sidan volymer](./media/storsimple-8000-manage-volumes-u2/volumes-list.png)

En volym består av en serie attribut:

* **Volym namn** – ett beskrivande namn som måste vara unikt och som hjälper dig att identifiera volymen. Det här namnet används också i övervaknings rapporter när du filtrerar på en angiven volym. När volymen har skapats går det inte att byta namn på den.
* **Status** – kan vara online eller offline. Om en volym är offline är den inte synlig för initierare (servrar) som har behörighet att använda volymen.
* **Kapacitet** – anger den totala mängden data som kan lagras av initieraren (servern). Lokalt fästa volymer är helt etablerade och finns på StorSimple-enheten. Skiktade volymer är tunt etablerade och data dedupliceras. Med tunt allokerade volymer är enheten inte förallokerad fysisk lagrings kapacitet internt eller i molnet enligt konfigurerad volym kapacitet. Volym kapaciteten allokeras och konsumeras på begäran.
* **Typ** – anger om volymen är i **nivå** av (standard) eller **lokalt fäst**.

Följ anvisningarna i den här självstudien för att utföra följande uppgifter:

* Lägg till en volym 
* Ändra en volym 
* Ändra volym typ
* Ta bort en volym 
* Koppla från en volym 
* Övervaka en volym 

## <a name="add-a-volume"></a>Lägg till en volym

Du har [skapat en volym](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) under distributionen av enheten StorSimple 8000-serien. Att lägga till en volym är en liknande procedur.

#### <a name="to-add-a-volume"></a>Lägga till en volym

1. Välj din enhet i tabellistan med enheter på bladet **Enheter**. Klicka på **+ Lägg till volymen**.

    ![Lägg till en ny volym](./media/storsimple-8000-manage-volumes-u2/add-volume-01.png)

2. På bladet **Lägg till en volym**:
   
    1. Fältet **Välj enhet** fylls i automatiskt med din aktuella enhet.

    2. I listrutan väljer du den volymcontainer som du vill lägga till en volym i.

    3.  Anger du ett **namn** för volymen. När volymen har skapats kan du inte byta namn på volymen.

    4. Välj **Typ** för volymen i listrutan. För arbetsbelastningar som kräver lokala garantier, låg latens och hög prestanda, väljer du en **lokalt fäst** volym. För all övrig data, väljer du en **nivåindelad** volym. Om du använder volymen för arkiveringsdata, markerar du **Använd volymen för arkiveringsdata med låg åtkomstfrekvens**.
      
       En nivåindelad volym är tunt etablerad och kan skapas snabbt. Om du väljer **Använd volymen för arkiveringsdata med låg åtkomstfrekvens** för nivåindelad volym avsedd för arkivdata så ändras segmentstorleken för deduplicering för din volym till 512 KB. Om fältet inte är markerat använder den motsvarande nivåindelade volymen en segmentstorlek på 64 kB. En större segmentstorlek för deduplicering låter enheten snabba på överföring av segmentstorleken tillåter enheten att påskynda överföringen av stora mängder arkiveringsdata till molnet.
       
       En lokalt fäst volym etableras tjockt, vilket försäkrar att primärdata på volymen är lokalt belägen för enheten och inte läcker över till molnet.  Om du skapar en lokalt fäst volym letar enheten efter ledigt utrymme på de lokala nivåerna för att etablera volymen med önskad storlek. Att skapa en lokalt fäst volym kan innebära att läcka befintlig data från enheten till molnet och volymen kan ta lång tid att skapa. Den totala tiden beror på den etablerade volymens storlek, tillgänglig nätverksbandbredd och den data som finns på din enhet.

    5. Ange **etableringskapacitet** för volymen. Anteckna kapaciteten som finns tillgänglig baserat på den volymtyp som valts. Den angivna volymstorleken får inte överskrida det tillgängliga utrymmet.
      
       Du kan etablera lokalt fästa volymer upp till 8,5 TB, eller nivåindelade volymer upp till 200 TB på 8100-enheten. På den större 8600-enheten kan du etablera lokalt fästa volymer upp till 22,5 TB, eller nivåindelade volymer upp till 500 TB. Eftersom det krävs lokalt utrymme på enheten för att hålla arbetsuppsättningen med nivåindelade volymer påverkar skapandet av fästa volymer mängden utrymme som finns tillgängligt för att etablera nivåindelade volymer. Därför minskar utrymmet som är tillgängligt för att skapa nivåindelade volymer om du skapar en lokalt fixerad volym. Likaså om du skapar en nivåindelad volym minskar det tillgängliga utrymmet för att skapa en lokalt fäst volym.
      
       Om du etablerar en lokalt fäst volym på 8,5 TB (största tillåtna storleken) på din 8100-enhet har du använt upp allt lokalt tillgängligt utrymme för enheten. I så fall kan du inte skapa fler nivåindelade volymer eftersom det inte finns något lokalt utrymme ledigt på enheten för att lagra arbetsuppsättningen för den nivåindelade volymen. Befintliga nivåindelade volymer påverkar också tillgängligt utrymme. Om du exempelvis har en 8100-enhet som redan har nivåindelade volymer på 106 TB finns det bara 4 TB utrymme tillgängligt för lokalt fästa volymer.

    6. I fältet **anslutna värdar** klickar du på pilen och väljer sedan de ACR som du vill ansluta. På bladet **anslutna värdar** väljer du en befintlig ACR eller lägger till en ny ACR. Om du väljer en ny ACR anger du ett **namn** för din ACR, anger iSCSI- **kvalificerat namn** (IQN) för Windows-värden. Om du inte har en IQN, går du till Hämta IQN för en Windows Server-värd.<!--Please verify: Is new ACR capability still available? Are the labels and controls the same?--> 

        ![Lägg till en ansluten värd](./media/storsimple-8000-manage-volumes-u2/add-volume-02.png)<!--New graphic. Source: add-volume-connected host-->

   7. När du är klar med inställningarna klickar du på **skapa**. 

      En volym skapas med de angivna inställningarna. Din nya volym är redo att användas.

> [!NOTE]
> Om du skapar en lokalt fixerad volym och sedan skapar en ny lokalt fixerad volym omedelbart, körs volymens skapande jobb i tur och ordning. Det första jobbet för att skapa volymen måste slutföras innan nästa volym skapande jobb kan påbörjas.

## <a name="modify-a-volume"></a>Ändra en volym

Ändra en volym när du behöver expandera den eller ändra de värdar som har åtkomst till volymen.

> [!IMPORTANT]
> * Om du ändrar volym storleken på enheten måste volymens storlek också ändras på värden.
> * De åtgärder för värd sidan som beskrivs här gäller för Windows Server 2012 (2012R2). Procedurer för Linux eller andra värd operativ system är olika. Läs anvisningarna för värd operativ systemet när du ändrar volymen på en värd som kör ett annat operativ system.

#### <a name="to-modify-a-volume"></a>Ändra en volym

1. Gå till StorSimple Device Manager-tjänsten och klicka sedan på **Enheter**. Välj den enhet som har den volym som du vill ändra i list rutan för enheterna. Klicka på **inställningar > volymer**.

    ![Gå till bladet volymer](./media/storsimple-8000-manage-volumes-u2/modify-volume-02.png)

2. Välj volymen i tabell listan över volymer och högerklicka för att anropa snabb menyn. Välj **ta offline** för att ta den volym som du vill ändra offline.

    ![Välj och koppla från volymen](./media/storsimple-8000-manage-volumes-u2/modify-volume-04.png)

3. I bladet **ta offline** granskar du effekten av att ta volymen offline och markera motsvarande kryss ruta. Se till att motsvarande volym på värden är offline först. Information om hur du kopplar från en volym på värd servern som är ansluten till StorSimple finns i avsnittet om vilka operativ system som är detaljerade. Klicka på **Koppla från**.

    ![Granska effekten av att ta volymen offline](./media/storsimple-8000-manage-volumes-u2/modify-volume-05.png)

4. När volymen är offline (som visas i volym status) markerar du volymen och högerklickar för att anropa snabb menyn. Välj **ändra volym**.

    ![Välj Ändra volym](./media/storsimple-8000-manage-volumes-u2/modify-volume-09.png)


5. På bladet **ändra volym** kan du göra följande ändringar:
   
   1. Det går inte att redigera volym **namnet** .
   2. Konvertera **typen** från lokalt fäst till skiktad eller från nivå till lokalt fast (se [ändra volym typ](#change-the-volume-type) för mer information).
   3. Öka den **tillhandahållna kapaciteten**. Den **tillhandahållna kapaciteten** kan bara ökas. Det går inte att krympa en volym när den har skapats.
   4. Under **anslutna värdar** kan du ändra ACR. Om du vill ändra en ACR måste volymen vara offline.

       ![Granska effekten av att ta volymen offline 2](./media/storsimple-8000-manage-volumes-u2/modify-volume-11.png)<!--Legacy screen doesn't match step. New graphic needed?-->

6. Klicka på **Spara** för att spara dina ändringar. Klicka på **Ja** när du uppmanas att bekräfta åtgärden. I Azure Portal visas ett uppdaterings volym meddelande. Ett meddelande visas när volymen har uppdaterats.

    ![Granska effekten av att ta volymen offline 3](./media/storsimple-8000-manage-volumes-u2/modify-volume-05.png)<!--Updated graphic. Source: modify-volume-save-->

7. Om du expanderar en volym utför du följande steg på Windows-värddatorn:
   
   1. Gå till **dator hantering**  -> **disk hantering**.
   2. Högerklicka på **disk hantering** och välj **Genomsök diskar**.
   3. I listan över diskar väljer du den volym som du har uppdaterat, högerklickar på och väljer sedan **Utöka volym**. Guiden utöka volym startar. Klicka på **Nästa**.
   4. Slutför guiden och acceptera standardvärdena. När guiden har slutförts bör volymen Visa den ökade storleken.
      
      > [!NOTE]
      > Om du expanderar en lokalt fixerad volym och sedan expanderar en annan lokalt fixerad volym efteråt körs volym expansions jobben i tur och ordning. Det första volym expansions jobbet måste slutföras innan nästa volym expansions jobb kan börja.
      

## <a name="change-the-volume-type"></a>Ändra volym typ

Du kan ändra volym typen från nivå till lokalt fäst eller lokalt fäst i nivå. Den här konverteringen bör dock inte vara en frekvent förekomst.

### <a name="tiered-to-local-volume-conversion-considerations"></a>På nivå av lokal volym konvertering

Några orsaker till att konvertera en volym från nivå till lokalt fäst är:

* Lokala garantier gällande data tillgänglighet och prestanda
* Eli minering av moln fördröjningar och problem med moln anslutning.

Detta är vanligt vis små befintliga volymer som du vill komma åt ofta. En lokalt fäst volym är helt etablerad när den skapas. 

Om du konverterar en nivå volym till en lokalt fäst volym, verifierar StorSimple att du har tillräckligt med utrymme på enheten innan konverteringen påbörjas. Om det inte finns tillräckligt med utrymme visas ett fel meddelande och åtgärden avbryts. 

> [!NOTE]
> Innan du påbörjar en konvertering från nivå till lokalt fäst bör du se till att du tar hänsyn till utrymmes kraven för dina andra arbets belastningar. 

Konvertering från en nivå till en lokalt fäst volym kan påverka enhetens prestanda negativt. Dessutom kan följande faktorer öka den tid det tar att slutföra konverteringen:

* Det finns inte tillräckligt med bandbredd.
* Det finns ingen aktuell säkerhets kopia.

Minimera effekterna som dessa faktorer kan ha:

* Granska dina bandbredds begränsnings principer och se till att en dedikerad 40 Mbit/s-bandbredd är tillgänglig.
* Schemalägg konverteringen för tider med låg belastning.
* Ta en ögonblicks bild av molnet innan du påbörjar konverteringen.

Om du konverterar flera volymer (med stöd för olika arbets belastningar) bör du prioritera volym konverteringen så att högre prioritets volymer konverteras först. Du bör till exempel konvertera volymer som är värdar för virtuella datorer (VM) eller volymer med SQL-arbetsbelastningar innan du konverterar volymer med arbets belastningar för fil resurser.

### <a name="local-to-tiered-volume-conversion-considerations"></a>Överväganden vid konvertering av lokala till skiktade volymer

Du kanske vill ändra en lokalt fäst volym till en nivå volym om du behöver ytterligare utrymme för att etablera andra volymer. När du konverterar den lokalt fästa volymen till skiktad ökar den tillgängliga kapaciteten på enheten med storleken på den utgivna kapaciteten. Om anslutnings problem förhindrar konverteringen av en volym från den lokala typen till nivå typen, kommer den lokala volymen att visa egenskaper för en nivå volym tills konverteringen är klar. Detta beror på att vissa data kan ha spillat i molnet. Dessa spillade data fortsätter att uppta lokalt utrymme på enheten som inte kan frigöras förrän åtgärden startas om och slutförs.

> [!NOTE]
> Det kan ta en stund att konvertera en volym och du kan inte avbryta en konvertering när den har startats. Volymen är online under konverteringen och du kan göra säkerhets kopior, men du kan inte expandera eller återställa volymen när konverteringen sker.


#### <a name="to-change-the-volume-type"></a>Ändra volym typ

1. Gå till StorSimple Device Manager-tjänsten och klicka sedan på **Enheter**. Välj den enhet som har den volym som du vill ändra i list rutan för enheterna. Klicka på **inställningar > volymer**.

    ![Gå till volymer blad 2](./media/storsimple-8000-manage-volumes-u2/modify-volume-02.png)

3. Välj volymen i tabell listan över volymer och högerklicka för att anropa snabb menyn. Välj **ändra**.

    ![Välj Ändra från snabb menyn](./media/storsimple-8000-manage-volumes-u2/change-volume-type-02.png)

4. På bladet **ändra volym** ändrar du volym typen genom att välja den nya typen i list rutan **typ** .
   
   * Om du ändrar typen till **lokalt fäst** kommer StorSimple att kontrol lera om det finns tillräckligt med kapacitet.
   * Om du ändrar typen till **skiktad** och volymen kommer att användas för arkivering av data, markerar du kryss rutan **Använd den här volymen för lagrings data som används mindre ofta** .
   * Om du konfigurerar en lokalt fixerad volym som en nivå eller _vice versa_ visas följande meddelande.
   
     ![Ändra volym typ meddelande](./media/storsimple-8000-manage-volumes-u2/change-volume-type-03.png)

7. Klicka på **Spara** för att spara ändringarna. När du uppmanas att bekräfta klickar du på **Ja** för att starta konverterings processen. 

    ![Spara och bekräfta](./media/storsimple-8000-manage-volumes-u2/modify-volume-11.png)

8. I Azure Portal visas ett meddelande om hur jobbet skapas som uppdaterar volymen. Klicka på meddelandet för att övervaka status för volym konverterings jobbet.

    ![Jobb för volym konvertering](./media/storsimple-8000-manage-volumes-u2/change-volume-type-05.png)

## <a name="take-a-volume-offline"></a>Koppla från en volym

Du kan behöva koppla från en volym när du planerar att ändra eller ta bort volymen. När en volym är offline är den inte tillgänglig för Läs-och Skriv behörighet. Du måste koppla från volymen på värden och enheten.

#### <a name="to-take-a-volume-offline"></a>Koppla från en volym

1. Kontrol lera att volymen i fråga inte används innan du tar den offline.
2. Ta volymen offline på värden först. Detta eliminerar eventuell risk för skadade data på volymen. Mer information om de olika stegen finns i anvisningarna för värd operativ systemet.
3. När värden är offline tar du volymen på enheten offline genom att utföra följande steg:
   
    1. Gå till StorSimple Device Manager-tjänsten och klicka sedan på **Enheter**. Välj den enhet som har den volym som du vill ändra i list rutan för enheterna. Klicka på **inställningar > volymer**.

        ![Gå till volymer blad 3](./media/storsimple-8000-manage-volumes-u2/modify-volume-02.png)

    2. Välj volymen i tabell listan över volymer och högerklicka för att anropa snabb menyn. Välj **ta offline** för att ta den volym som du vill ändra offline.

        ![Välj och koppla från volymen](./media/storsimple-8000-manage-volumes-u2/modify-volume-04.png)

3. I bladet **ta offline** granskar du effekten av att ta volymen offline och markera motsvarande kryss ruta. Klicka på **Koppla från**. 

    ![Granska effekten av att ta volymen offline 4](./media/storsimple-8000-manage-volumes-u2/modify-volume-05.png)
      
      Du får ett meddelande när volymen är offline. Volym statusen uppdateras också till offline.
      
4. Om du väljer volymen och högerklicka, blir alternativet **online** tillgängligt på snabb menyn när en volym är offline.

> [!NOTE]
> Kommandot **ta offline** skickar en begäran till enheten för att ta volymen offline. Om värdarna fortfarande använder volymen, resulterar detta i brutna anslutningar, men det går inte att ta bort volymen offline.

## <a name="delete-a-volume"></a>Ta bort en volym

> [!IMPORTANT]
> Du kan bara ta bort en volym om den är offline.

Utför följande steg för att ta bort en volym.

#### <a name="to-delete-a-volume"></a>Ta bort en volym

1. Gå till StorSimple Device Manager-tjänsten och klicka sedan på **Enheter**. Välj den enhet som har den volym som du vill ändra i list rutan för enheterna. Klicka på **inställningar > volymer**.

    ![Gå till volym blad 4](./media/storsimple-8000-manage-volumes-u2/modify-volume-02.png)

3. Kontrol lera status för den volym som du vill ta bort. Om volymen som du vill ta bort inte är offline tar du den offline först. Följ stegen i koppla från [en volym](#take-a-volume-offline).
4. När volymen är offline väljer du volymen, högerklickar för att anropa snabb menyn och väljer sedan **ta bort**.

    ![Välj Ta bort från snabb menyn](./media/storsimple-8000-manage-volumes-u2/delete-volume-01.png)

5. I bladet **ta bort** granskar och markerar du kryss rutan mot effekten av att ta bort en volym. När du tar bort en volym försvinner alla data som finns på volymen. 

    ![Spara och bekräfta ändringar](./media/storsimple-8000-manage-volumes-u2/delete-volume-02.png)

6. När volymen har tagits bort, uppdateras tabell listan över volymer för att indikera borttagningen.

    ![Uppdaterad volym lista](./media/storsimple-8000-manage-volumes-u2/delete-volume-03.png)
   
   > [!NOTE]
   > Om du tar bort en lokalt fäst volym kanske det tillgängliga utrymmet för nya volymer inte uppdateras omedelbart. StorSimple-Enhetshanteraren tjänsten uppdaterar det lokala utrymmet som är tillgängligt regelbundet. Vi rekommenderar att du väntar några minuter innan du försöker skapa den nya volymen.
   >
   > Om du tar bort en lokalt fäst volym och sedan tar bort en annan lokalt fixerad volym efteråt körs volym borttagnings jobben i tur och ordning. Det första volym borttagnings jobbet måste slutföras innan nästa volym borttagnings jobb kan börja.

## <a name="monitor-a-volume"></a>Övervaka en volym

Med volym övervakning kan du samla in I/O-relaterad statistik för en volym. Övervakning är aktiverat som standard för de första 32-volymer som du skapar. Övervakning av ytterligare volymer är inaktive rad som standard. 

> [!NOTE]
> Övervakning av klonade volymer är inaktiverat som standard.


Utför följande steg för att aktivera eller inaktivera övervakning av en volym.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Aktivera eller inaktivera volym övervakning

1. Gå till StorSimple Device Manager-tjänsten och klicka sedan på **Enheter**. Välj den enhet som har den volym som du vill ändra i list rutan för enheterna. Klicka på **inställningar > volymer**.
2. Välj volymen i tabell listan över volymer och högerklicka för att anropa snabb menyn. Välj **ändra**.
3. I bladet **ändra volym** för **övervakning** väljer du **Aktivera** eller **inaktivera** för att aktivera eller inaktivera övervakning.

    ! [Inaktivera övervakning] (./Media/StorSimple-8000-Manage-Volumes-U2/Monitor-Volume-0 1.png) 

4. Klicka på **Spara** och bekräfta genom att klicka på **Ja**. I Azure Portal visas ett meddelande om att uppdatera volymen och sedan ett meddelande om att det lyckades efter att volymen har uppdaterats.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [klonar en StorSimple volym](storsimple-8000-clone-volume-u2.md).
* Lär dig hur du [använder tjänsten StorSimple Enhetshanteraren för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

