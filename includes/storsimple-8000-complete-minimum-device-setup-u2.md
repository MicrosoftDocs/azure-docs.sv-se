---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 867cdc97ff91d5932230b733dee4d7660d499c39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96027936"
---
#### <a name="to-complete-the-minimum-storsimple-device-setup"></a>Slutför den minimala StorSimple-enhetsinställningen

   > [!NOTE]
   > Du kan inte ändra namnet på en enhet när den minimala enhetsinstallationen har slutförts.
   
1. Markera och klicka på din enhet i tabellistan med enheter på bladet **Enheter**. Enheten har statusen **Redo för installation**. Bladet **Konfigurera enhet** öppnas.

     ![Nätverksgränssnitt för minimala StorSimple-enhetsinställningar](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig1.png)

2. Bladet **Konfigurera enhet**:
   
   1. Ange ett **eget namn** för din enhet. Standard-enhetsnamnet har information som enhetsmodell serienummer. Du kan tilldela ett eget namn på upp till 64 tecken för att hantera din enhet.
   2. Ange **tidszon** baserat på den geografiska plats där enheten ska distribueras. Enheten använder den här tidszonen för alla schemalagda åtgärder.
   3. Under **Inställningar för DATA 0**:

       1. DATA 0-nätverksgränssnittet visas som aktiverat med nätverksinställningarna (IP, undernät och gateway) som konfigurerats via guiden. DATA 0 aktiveras också automatiskt för molnet samt iSCSI.

       2. Ange de fasta IP-adresserna för Controller 0 och Controller 1. **De styrenhets-fästa IP-adresserna måste vara fria IP-adresser i undernätet som är åtkomliga från enhetens IP-adress.** Om DATA 0-gränssnittet har konfigurerats för IPv4, måste de fasta IP-adresserna anges i IPv4-format. Om du angav ett prefix för IPv6-konfigurationen fylls de fasta IP-adresserna i automatiskt i fälten.

            ![StorSimple minsta enhets konfiguration nätverks gränssnitt 2](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig2.png)

            De fasta IP-adresserna för styrenheten används för att hantera uppdateringarna till enheten och för skräpinsamling. Därför måste de fasta IP-adresserna vara dirigerbara och kunna ansluta till Internet. Du kan kontrollera att de fasta IP-adresserna för dina styrenheter är dirigerbara med hjälp av cmdleten [Test-HcsmConnection][Test]. Följande exempel visar att fasta styrenhets-IP-adresser dirigeras till Internet och har åtkomst till Microsoft Update-servrarna.

            ![Test-HcsmConnection visar dirigerbara IP-adresser](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig3.png)

1. Klicka på **OK**. Enhetskonfigurationen startar. Du meddelas när enhetskonfigurationen är klar. Enhetens status ändras till **Online** på bladet **Enheter**.

    ![StorSimple minimal enhets konfiguration nätverks gränssnitt 3](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig4.png)

<!--Link reference-->
[Test]: /previous-versions/windows/powershell-scripting/dn715782(v=wps.630)