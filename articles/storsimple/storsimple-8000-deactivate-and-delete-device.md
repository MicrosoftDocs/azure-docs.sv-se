---
title: Inaktivera och ta bort en enhet med StorSimple 8000-serien | Microsoft Docs
description: Lär dig hur du inaktiverar och tar bort en StorSimple-enhet som är ansluten till en StorSimple Enhetshanteraren-tjänst.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2018
ms.author: alkohli
ms.openlocfilehash: 07d108306fdca9bfe8f793b61660550e43151d71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017193"
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>Inaktivera och ta bort en StorSimple-enhet

## <a name="overview"></a>Översikt

Den här artikeln beskriver hur du inaktiverar och tar bort en StorSimple-enhet som är ansluten till en StorSimple Enhetshanteraren-tjänst. Vägledningen i den här artikeln gäller endast StorSimple 8000-seriens enheter, inklusive StorSimple-molnet. Om du använder en virtuell StorSimple-matris går du till [inaktivera och ta bort en StorSimple virtuell matris](storsimple-virtual-array-deactivate-and-delete-device.md).

Inaktive ringen är en anslutning mellan enheten och motsvarande StorSimple Enhetshanteraren-tjänst. Du kanske vill ta en StorSimple enhet ur drift (till exempel om du ersätter eller uppgraderar enheten eller om du inte längre använder StorSimple). I så fall måste du inaktivera enheten innan du kan ta bort den.

När du inaktiverar en enhet är alla data som lagrats lokalt på enheten inte längre tillgängliga. Endast data som är kopplade till enheten som lagrades i molnet kan återställas.

> [!WARNING]
> Inaktive ringen är en PERMANENT åtgärd och kan inte ångras. En inaktive rad enhet kan inte registreras med StorSimple-Enhetshanteraren tjänsten om den inte återställs till fabriks inställningarna.
>
> Fabriks återställnings processen tar bort alla data som lagrats lokalt på enheten. Därför måste du ta en moln ögonblicks bild av alla dina data innan du inaktiverar en enhet. Med den här ögonblicks bilden av molnet kan du återställa alla data i ett senare skede.

> [!NOTE]
>
> - Innan du inaktiverar en StorSimple fysisk enhet eller en moln installation måste du se till att data från den borttagna volym behållaren verkligen tas bort från enheten. Du kan övervaka moln förbruknings diagrammen och när du ser minskningen av moln användningen på grund av de säkerhets kopior som du har tagit bort, kan du fortsätta att inaktivera enheten. Om du inaktiverar enheten innan den här anfangen sker placeras data i lagrings kontot och avgifter debiteras.
>
> - Stoppa eller ta bort klienter och värdar som är beroende av enheten innan du inaktiverar en StorSimple fysisk enhet eller moln installation.
>
> - Om lagrings kontona eller behållarna i lagrings kontot som är kopplade till volym behållarna redan har tagits bort innan data tas bort från enheten, visas ett fel meddelande om att data inte kan tas bort. Vi rekommenderar att du tar bort data på enheten innan du tar bort lagrings kontot eller behållare där. I den här situationen måste du dock fortsätta med enhets inaktive ring och borttagning förutsatt att data redan har tagits bort från lagrings kontot.

När du har läst den här självstudien kommer du att kunna:

- Inaktivera en enhet och ta bort data.
- Inaktivera en enhet och behåll data.

## <a name="deactivate-and-delete-data"></a>Inaktivera och ta bort data

Om du är intresse rad av att ta bort enheten helt och inte vill behålla data på enheten utför du följande steg.

### <a name="to-deactivate-the-device-and-delete-the-data"></a>Inaktivera enheten och ta bort data

1. Innan du inaktiverar en enhet måste du ta bort alla volym containrar (och volymerna) som är associerade med enheten. Du kan bara ta bort volym behållare efter att du har tagit bort de tillhör ande säkerhets kopiorna. Se anteckningen i översikten ovan innan du inaktiverar en StorSimple fysisk enhet eller en moln installation.

2. Inaktivera enheten på följande sätt:

   1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**. På bladet **enheter** väljer du den enhet som du vill inaktivera, högerklickar på och klickar sedan på **inaktivera**.

        ![Inaktivera StorSimple-enhet](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Skriv enhets namnet som ska bekräftas i bladet **inaktive ring** och klicka sedan på **inaktivera**. Inaktive rings processen startar och tar några minuter att slutföra.

        ![Inaktivera StorSimple-enhet 2](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. Efter inaktive ringen kan du ta bort enheten helt. Om du tar bort en enhet tas den bort från listan över enheter som är anslutna till tjänsten. Tjänsten kan sedan inte längre hantera den borttagna enheten. Använd följande steg för att ta bort enheten:
   
   1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**. Välj den inaktiverade enhet som du vill ta bort på bladet **enheter** , högerklicka på och klicka sedan på **ta bort**.

        ![Inaktivera StorSimple-enhet 3](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. I bladet **ta bort** skriver du namnet på enheten som ska bekräftas och klickar sedan på **ta bort**. Det tar några minuter att slutföra borttagningen.

        ![Inaktivera StorSimple-enhet 4](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. När borttagningen har slutförts meddelas du. Enhets listan uppdateras också för att återspegla borttagningen.

## <a name="deactivate-and-retain-data"></a>Inaktivera och spara data

Om du är intresse rad av att ta bort enheten men vill behålla data, utför du följande steg:

### <a name="to-deactivate-a-device-and-retain-the-data"></a>Så här inaktiverar du en enhet och behåller data

1. Inaktivera enheten. Alla volym containrar och ögonblicks bilder av enheten finns kvar.
   
   1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**. På bladet **enheter** väljer du den enhet som du vill inaktivera, högerklickar på och klickar sedan på **inaktivera**.

         ![Inaktivera StorSimple-enhet 5](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Skriv enhets namnet som ska bekräftas i bladet **inaktive ring** och klicka sedan på **inaktivera**. Inaktive rings processen startar och tar några minuter att slutföra.

         ![Inaktivera StorSimple-enhet 6](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. Du kan nu redundansväxla volym behållarna och de associerade ögonblicks bilderna. För procedurer går du till [redundans och haveri beredskap för din StorSimple-enhet](storsimple-8000-device-failover-disaster-recovery.md).
3. När du har inaktiverat och redundans kan du ta bort enheten helt. Om du tar bort en enhet tas den bort från listan över enheter som är anslutna till tjänsten. Tjänsten kan sedan inte längre hantera den borttagna enheten. Utför följande steg för att ta bort enheten:
   
   1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**. Välj den inaktiverade enhet som du vill ta bort på bladet **enheter** , högerklicka på och klicka sedan på **ta bort**.

       ![Inaktivera StorSimple Device 7](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. I bladet **ta bort** skriver du namnet på enheten som ska bekräftas och klickar sedan på **ta bort**. Det tar några minuter att slutföra borttagningen.

       ![Inaktivera StorSimple-enhet 8](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. När borttagningen har slutförts meddelas du. Enhets listan uppdateras också för att återspegla borttagningen.

## <a name="deactivate-and-delete-a-cloud-appliance"></a>Inaktivera och ta bort en molninstallation

För en StorSimple Cloud Appliance avaktiverar du från portalen, frigör och tar bort den virtuella datorn och de resurser som skapades när den etablerades. När molninstallationen har inaktiverats kan den inte återställas till sitt tidigare tillstånd.

![Inaktivera StorSimple Cloud Appliance](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

Inaktive ring av resultat i följande åtgärder:

* StorSimple Cloud Appliance tas bort från tjänsten.
* Den virtuella datorn för StorSimple Cloud Appliance har tagits bort.
* OS-disken och data diskarna som skapas för StorSimple Cloud Appliance bevaras. Om du inte använder dessa entiteter bör du ta bort dem manuellt.
* Den värdbaserade tjänsten och Virtual Network som skapades under etableringen behålls. Om du inte använder dessa entiteter bör du ta bort dem manuellt.
* Moln ögonblicks bilder som skapats av StorSimple Cloud Appliance bevaras.

När moln installationen är inaktive rad kan du ta bort den från listan över enheter. Välj den inaktiverade enheten, högerklicka på och klicka sedan på **ta bort**. StorSimple meddelar dig när enheten har tagits bort och listan över enheter uppdateras.

## <a name="next-steps"></a>Nästa steg

* Om du vill återställa den inaktiverade enheten till fabriks inställningarna väljer du [Återställ enheten till fabriks inställningarna](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md)om du behöver teknisk hjälp.
* Om du vill veta mer om hur du använder tjänsten StorSimple Enhetshanteraren går du till [använda StorSimple Enhetshanteraren-tjänsten för att administrera StorSimple-enheten](storsimple-8000-manager-service-administration.md).

