---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 3467a5d5daa300f82c7b81641ab7e262259d9285
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95560825"
---
### <a name="upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-storsomple-adapter-for-sharepoint"></a>Uppgradera SharePoint 2010 till SharePoint 2013 och installera StorSomple adapter för SharePoint
> [!IMPORTANT]
> Filer som tidigare har flyttats till extern lagring via RBS är inte tillgängliga förrän uppgraderingen är färdig och RBS-funktionen aktive ras igen. Om du vill begränsa användar påverkan utför du en uppgradering eller ominstallation under ett planerat underhålls fönster.
> 
> 

#### <a name="to-upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-adapter"></a>Uppgradera SharePoint 2010 till SharePoint 2013 och installera sedan kortet
1. I SharePoint 2010-servergruppen noterar du BLOB Store-sökvägen för de externa BLOBBARna och innehålls databaserna som RBS är aktiverat för. 
2. Installera och konfigurera den nya SharePoint 2013-gruppen. 
3. Flytta databaser, program och webbplats samlingar från SharePoint 2010-gruppen till den nya SharePoint 2013-servergruppen. Anvisningar finns i [Översikt över uppgraderings processen till SharePoint 2013](/SharePoint/upgrade-and-update/overview-of-the-upgrade-process).
4. Installera StorSimple Adapter för SharePoint på den nya server gruppen. Gå till [Installera StorSimple Adapter för SharePoint](#install-the-storsimple-adapter-for-sharepoint) för procedurer.
5. Med hjälp av informationen som du antecknade i steg 1 aktiverar du RBS för samma uppsättning innehålls databaser och anger samma BLOB Store-sökväg som användes i SharePoint 2010-installationen. Gå till [Konfigurera RBS](#configure-rbs) för procedurer. När du har slutfört det här steget bör tidigare utgångna filer vara tillgängliga från den nya server gruppen. 

### <a name="upgrade-the-storsimple-adapter-for-sharepoint"></a>Uppgradera StorSimple-adaptern för SharePoint
> [!IMPORTANT]
> Du bör schemalägga att uppgraderingen ska ske under ett planerat underhålls fönster av följande orsaker:
> 
> * Tidigare externt innehåll kommer inte att vara tillgängligt förrän kortet har installerats om.
> * Allt innehåll som laddas upp till platsen efter att du har avinstallerat den tidigare versionen av StorSimple Adapter för SharePoint, men innan du installerar den nya versionen kommer att lagras i innehålls databasen. Du måste flytta innehållet till StorSimple-enheten när du har installerat det nya kortet. Du kan använda Microsoft `RBS Migrate()` PowerShell-cmdleten som ingår i SharePoint för att migrera innehållet. Mer information finns i [Migrera innehåll till eller från RBS](/previous-versions/office/sharepoint-foundation-2010/ff628255(v=office.14)). 
> 
> 

#### <a name="to-upgrade-the-storsimple-adapter-for-sharepoint"></a>Så här uppgraderar du StorSimple Adapter för SharePoint
1. Avinstallera den tidigare versionen av StorSimple Adapter för SharePoint.
   
   > [!NOTE]
   > Detta kommer automatiskt att inaktivera RBS i innehålls databaserna. Befintliga BLOBar är dock kvar på StorSimple-enheten. Eftersom RBS är inaktiverat och BLOBBARna inte har migrerats tillbaka till innehålls databaserna kommer alla begär Anden för dessa BLOBBAR att Miss kopie ras. 
   > 
   > 
2. Installera det nya StorSimple-kortet för SharePoint. Det nya kortet identifierar automatiskt de innehålls databaser som tidigare har Aktiver ATS eller inaktiverats för RBS och kommer att använda de tidigare inställningarna.