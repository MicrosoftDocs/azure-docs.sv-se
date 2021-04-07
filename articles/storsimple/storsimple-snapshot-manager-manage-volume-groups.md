---
title: StorSimple Snapshot Manager volym grupper | Microsoft Docs
description: Beskriver hur du använder snapin-modulen StorSimple Snapshot Manager MMC för att skapa och hantera volym grupper.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 7a232414-6a28-4b81-bd7b-cf61e28b33d7
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: df2d74701e21c3773d96564f1b06d80ddb9fce9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98209227"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Använd StorSimple Snapshot Manager för att skapa och hantera volym grupper
## <a name="overview"></a>Översikt
Du kan använda noden **volym grupper** i fönstret **omfattning** för att tilldela volymer till volym grupper, Visa information om en volym grupp, schemalägga säkerhets kopior och redigera volym grupper.

Volym grupper är pooler med relaterade volymer som används för att säkerställa att säkerhets kopiorna är programkonsekventa. Mer information finns i [volymer och volym grupper](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) och [integrering med Windows tjänsten Volume Shadow Copy](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

> [!IMPORTANT]
> * Alla volymer i en volym grupp måste komma från en enda moln tjänst leverantör.
> * När du konfigurerar volym grupper ska du inte blanda CSV: er (Cluster-Shared Volumes) och icke-CSV: er i samma volym grupp. StorSimple Snapshot Manager har inte stöd för en blandning av CSV: er och icke-CSV: er i samma ögonblicks bild.

![Noden volym grupper](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Bild 1: StorSimple Snapshot Manager volym grupps nod** 

I den här självstudien beskrivs hur du kan använda StorSimple-Snapshot Manager för att:

* Visa information om dina volym grupper
* Skapa en volym grupp
* Säkerhetskopiera en volym grupp
* Redigera en volym grupp
* Ta bort en volym grupp

Alla dessa åtgärder är också tillgängliga i **Åtgärds** fönstret.

## <a name="view-volume-groups"></a>Visa volym grupper
Om du klickar på noden **volym grupper** visar **resultat** fönstret följande information om varje volym grupp, beroende på vilka kolumn val du gör. (Kolumnerna i **resultat** fönstret kan konfigureras. Högerklicka på noden **volymer** , Välj **Visa** och välj sedan **Lägg till/ta bort kolumner**.)

| Resultat kolumn | Beskrivning |
|:--- |:--- |
| Name |Kolumnen **Name** innehåller namnet på volym gruppen. |
| Program |I kolumnen **program** visas antalet VSS-skrivare som är installerade och körs på Windows-värden. |
| Markerat |Den **markerade** kolumnen visar antalet volymer som finns i volym gruppen. Noll (0) anger att inget program är associerat med volymerna i volym gruppen. |
| Inför |Den **importerade** kolumnen visar antalet importerade volymer. När värdet är **True** anger den här kolumnen att en volym grupp importerades från Azure Portal och inte skapades i StorSimple Snapshot Manager. |

> [!NOTE]
> StorSimple Snapshot Manager volym grupper visas också på fliken **principer för säkerhets kopiering** i Azure Portal.
> 
> 

## <a name="create-a-volume-group"></a>Skapa en volym grupp
Använd följande procedur för att skapa en volym grupp.

#### <a name="to-create-a-volume-group"></a>Så här skapar du en volym grupp
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager.
2. I fönstret **omfattning** högerklickar du på **volym grupper** och klickar sedan på **Skapa volym grupp**.
   
    ![Skapa volym grupp](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    Dialog rutan **skapa en volym grupp** visas.
   
    ![Dialog rutan skapa en volym grupp](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. Ange följande information:
   
   1. I rutan **namn** anger du ett unikt namn för den nya volym gruppen.
   2. I rutan **program** väljer du program som är associerade med de volymer som du ska lägga till i volym gruppen.
      
       I rutan **program** visas endast de program som använder StorSimple-volymer och som har VSS-skrivare aktiverade för dem. En VSS-skrivare är bara aktive rad om alla volymer som skrivaren är medveten om är StorSimple-volymer. Om program rutan är tom installeras inga program som använder Azure StorSimple-volymer och VSS-skrivare som stöds. (För närvarande stöder Azure StorSimple Microsoft Exchange och SQL Server.) Mer information om VSS-skrivare finns i [integrering med Windows tjänsten Volume Shadow Copy](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).
      
       Om du väljer ett program väljs alla volymer som är kopplade till det automatiskt. Om du väljer volymer som är associerade med ett särskilt program, väljs programmet automatiskt i rutan **program** . 
   3. I rutan **volymer** väljer du StorSimple-volymer som ska läggas till i volym gruppen. 
      
      * Du kan inkludera volymer med en eller flera partitioner. (Flera partition volymer kan vara dynamiska diskar eller grundläggande diskar med flera partitioner.) En volym som innehåller flera partitioner behandlas som en enskild enhet. Om du bara lägger till en av partitionerna i en volym grupp läggs alla andra partitioner automatiskt till i den volym gruppen samtidigt. När du har lagt till en partition med flera partitioner i en volym grupp fortsätter den flera partition volymen att behandlas som en enda enhet.
      * Du kan skapa tomma volym grupper genom att inte tilldela dem några volymer. 
      * Blanda inte kluster delade volymer (CSV: er) och icke-CSV: er i samma volym grupp. StorSimple Snapshot Manager har inte stöd för en blandning av CSV-volymer och volymer som inte är CSV i samma ögonblicks bild.
4. Spara volym gruppen genom att klicka på **OK** .

## <a name="back-up-a-volume-group"></a>Säkerhetskopiera en volym grupp
Använd följande procedur för att säkerhetskopiera en volym grupp.

#### <a name="to-back-up-a-volume-group"></a>Säkerhetskopiera en volym grupp
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager.
2. I fönstret **omfattning** expanderar du noden **volym grupper** , högerklickar på ett volym grupp namn och klickar sedan på **gör säkerhets kopiering**.
   
    ![Säkerhetskopiera volym gruppen omedelbart](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. I dialog rutan **skapa säkerhets kopia** väljer du **lokal ögonblicks bild** eller **moln ögonblicks bild** och klickar sedan på **skapa**.
   
    ![Dialog rutan för säkerhets kopiering](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. Bekräfta att säkerhets kopieringen körs genom att expandera noden **jobb** och klicka sedan på **Kör**. Säkerhets kopian ska visas.
5. Visa den slutförda ögonblicks bilden genom att expandera noden **säkerhets kopierings katalog** , expandera volym gruppens namn och klicka på **lokal ögonblicks bild** eller **moln ögonblicks bild**. Säkerhets kopian visas i listan om den har slutförts.

## <a name="edit-a-volume-group"></a>Redigera en volym grupp
Använd följande procedur för att redigera en volym grupp.

#### <a name="to-edit-a-volume-group"></a>Redigera en volym grupp
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager.
2. I fönstret **omfattning** expanderar du noden **volym grupper** , högerklickar på ett volym grupp namn och klickar sedan på **Redigera**.
3. Dialog rutan **skapa en volym grupp** visas. Du kan ändra poster för **namn**, **program** och **volymer** .
4. Spara ändringarna genom att klicka på **OK**.

## <a name="delete-a-volume-group"></a>Ta bort en volym grupp
Använd följande procedur för att ta bort en volym grupp. 

> [!WARNING]
> Detta tar även bort alla säkerhets kopior som är associerade med volym gruppen.
> 
> 

#### <a name="to-delete-a-volume-group"></a>Ta bort en volym grupp
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager.
2. I fönstret **omfattning** expanderar du noden **volym grupper** , högerklickar på ett volym grupp namn och klickar sedan på **ta bort**.
3. Dialog rutan **ta bort volym grupp** visas. Skriv **Confirm** i text rutan och klicka sedan på **OK**.
   
    Den borttagna volym gruppen försvinner från listan i **resultat** fönstret och alla säkerhets kopior som är associerade med den volym gruppen tas bort från säkerhets kopierings katalogen.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använder StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Lär dig hur du [använder StorSimple Snapshot Manager för att skapa och hantera säkerhets kopierings principer](storsimple-snapshot-manager-manage-backup-policies.md).

