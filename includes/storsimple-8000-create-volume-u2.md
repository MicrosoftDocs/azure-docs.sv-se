---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 400c5f0b2281679c3ec913e94643ffec6082d9e6
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552587"
---
#### <a name="to-create-a-volume"></a>Skapa en volym
1. Välj din enhet i tabellistan med enheter på bladet **Enheter**. Klicka på **+ Lägg till volymen**.<!--Note to reviewer. No screen updates. Had to rename screens to be able to correct a formatting error.-->

    ![Lägg till en ny volym](./media/storsimple-8000-create-volume-u2/create-volume-01.png)

2. På bladet **Lägg till en volym**:
   
   1. Fältet **Välj enhet** fylls i automatiskt med din aktuella enhet.

   2. I listrutan väljer du den volymcontainer som du vill lägga till en volym i. 

   3. Anger du ett **namn** för volymen. Du kan byta namn på en volym när den har skapats.

   4. Välj **Typ** för volymen i listrutan. För arbetsbelastningar som kräver lokala garantier, låg latens och hög prestanda, väljer du en **lokalt fäst** volym. För all övrig data, väljer du en **nivåindelad** volym. Om du använder volymen för arkiveringsdata, markerar du **Använd volymen för arkiveringsdata med låg åtkomstfrekvens**.
      
       En nivåindelad volym är tunt etablerad och kan skapas snabbt. Om du väljer **Använd volymen för arkiveringsdata med låg åtkomstfrekvens** för nivåindelad volym avsedd för arkivdata så ändras segmentstorleken för deduplicering för din volym till 512 KB. Om fältet inte är markerat använder den motsvarande nivåindelade volymen en segmentstorlek på 64 kB. En större segmentstorlek för deduplicering låter enheten snabba på överföring av segmentstorleken tillåter enheten att påskynda överföringen av stora mängder arkiveringsdata till molnet.
       
       En lokalt fäst volym etableras tjockt, vilket försäkrar att primärdata på volymen är lokalt belägen för enheten och inte läcker över till molnet.  Om du skapar en lokalt fäst volym letar enheten efter ledigt utrymme på de lokala nivåerna för att etablera volymen med önskad storlek. Att skapa en lokalt fäst volym kan innebära att läcka befintlig data från enheten till molnet och volymen kan ta lång tid att skapa. Den totala tiden beror på den etablerade volymens storlek, tillgänglig nätverksbandbredd och den data som finns på din enhet.

   5. Ange **etableringskapacitet** för volymen. Anteckna kapaciteten som finns tillgänglig baserat på den volymtyp som valts. Den angivna volymstorleken får inte överskrida det tillgängliga utrymmet.
      
       Du kan etablera lokalt fästa volymer upp till 8,5 TB, eller nivåindelade volymer upp till 200 TB på 8100-enheten. På den större 8600-enheten kan du etablera lokalt fästa volymer upp till 22,5 TB, eller nivåindelade volymer upp till 500 TB. Eftersom det krävs lokalt utrymme på enheten för att hålla arbetsuppsättningen med nivåindelade volymer påverkar skapandet av fästa volymer mängden utrymme som finns tillgängligt för att etablera nivåindelade volymer. Därför minskar utrymmet som är tillgängligt för att skapa nivåindelade volymer om du skapar en lokalt fixerad volym. Likaså om du skapar en nivåindelad volym minskar det tillgängliga utrymmet för att skapa en lokalt fäst volym.
      
       Om du etablerar en lokalt fäst volym på 8,5 TB (största tillåtna storleken) på din 8100-enhet har du använt upp allt lokalt tillgängligt utrymme för enheten. I så fall kan du inte skapa fler nivåindelade volymer eftersom det inte finns något lokalt utrymme ledigt på enheten för att lagra arbetsuppsättningen för den nivåindelade volymen. Befintliga nivåindelade volymer påverkar också tillgängligt utrymme. Om du exempelvis har en 8100-enhet som redan har nivåindelade volymer på 106 TB finns det bara 4 TB utrymme tillgängligt för lokalt fästa volymer.
 
      1. Klicka på pilen i fältet **Anslutna värdar**. 

         ![Anslutna värdar](./media/storsimple-8000-create-volume-u2/create-volume-02.png)

      1. På bladet **Anslutna värdar** väljer du en befintlig ACR eller lägger till en ny ACR genom att utföra följande steg:

         1. Ange ett **namn** för din ACR.
         2. Som **iSCSI-initierarnamn**, anger du det iSCSI-kvalificerade namnet (IQN) för din Windows-värd. Om du inte har en IQN, går du till [Hämta IQN för en Windows Server-värd](#get-the-iqn-of-a-windows-server-host).

      1. Klicka på **Skapa**. En volym skapas med de angivna inställningarna.

         ![Klicka på Skapa](./media/storsimple-8000-create-volume-u2/create-volume-03.png)

         > [!NOTE]
         > Tänk på att volymen som du har skapat här inte är skyddad. Du måste skapa och associera principer för säkerhetskopiering med den här volymen för att skapa schemalagda säkerhetskopieringar. 

