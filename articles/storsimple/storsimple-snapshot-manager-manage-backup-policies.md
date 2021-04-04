---
title: Principer för StorSimple Snapshot Manager säkerhets kopiering | Microsoft Docs
description: Beskriver hur du använder snapin-modulen StorSimple Snapshot Manager MMC för att skapa och hantera säkerhets kopierings principer som styr schemalagda säkerhets kopieringar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 745aeb9844f1abad075d34b34735563200ea618b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90054950"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>Använd StorSimple Snapshot Manager för att skapa och hantera säkerhets kopierings principer
## <a name="overview"></a>Översikt
En säkerhets kopierings princip skapar ett schema för säkerhets kopiering av volym data lokalt eller i molnet. När du skapar en säkerhets kopierings princip kan du också ange en bevarande princip. (Du kan behålla högst 64 ögonblicks bilder.) Mer information om säkerhets kopierings principer finns i [säkerhets kopierings typer](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies) i [StorSimple 8000-serien: en hybrid moln lösning](storsimple-overview.md).

I den här självstudien beskrivs hur du:

* Skapa en säkerhetskopieringspolicy
* Redigera en princip för säkerhets kopiering
* Ta bort en princip för säkerhets kopiering

## <a name="create-a-backup-policy"></a>Skapa en säkerhetskopieringspolicy
Använd följande procedur för att skapa en ny säkerhets kopierings princip.

#### <a name="to-create-a-backup-policy"></a>Så här skapar du en säkerhets kopierings princip
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager.
2. I fönstret **omfattning** högerklickar du på **säkerhets kopierings principer** och klickar på **skapa säkerhets kopierings princip**.

    ![Skapa en säkerhetskopieringspolicy](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    Dialog rutan **skapa en princip** visas.

    ![Skapa en princip – fliken Allmänt](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. På fliken **Allmänt** fyller du i följande information:

   1. I text rutan **namn** anger du ett namn för principen.
   2. I text rutan **volym grupp** skriver du namnet på den volym grupp som är kopplad till principen.
   3. Välj antingen **lokal ögonblicks bild** eller **moln ögonblicks bild**.
   4. Välj det antal ögonblicks bilder som ska behållas. Om du väljer **alla** kommer 64 ögonblicks bilder att bevaras (max).
4. Klicka på fliken **Schemalägg** .

    ![Skapa en princip – fliken schemaläggning](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. På fliken **Schemaläggning** slutför du följande information:

   1. Klicka på kryss rutan **Aktivera** för att schemalägga nästa säkerhets kopiering.
   2. Under **Inställningar** väljer du **en tid**, **varje dag**, **varje vecka** eller **varje månad**.
   3. I text rutan **Starta** klickar du på kalender ikonen och väljer ett start datum.
   4. Under **Avancerade inställningar** kan du ange valfria upprepnings scheman och slutdatum.
   5. Klicka på **OK**.

När du har skapat en säkerhets kopierings princip visas följande information i **resultat** fönstret:

* **Namn** – namnet på säkerhets kopierings policyn.
* **Typ** – lokal ögonblicks bild eller moln ögonblicks bild.
* **Volym grupp** – den volym grupp som är associerad med principen.
* **Kvarhållning** – antalet sparade ögonblicks bilder. det maximala värdet är 64.
* **Skapat** – det datum då den här principen skapades.
* **Aktive rad** – oavsett om principen gäller för närvarande: **Sant** anger att den är aktive rad. **Falskt** anger att den inte är aktiv.

## <a name="edit-a-backup-policy"></a>Redigera en princip för säkerhets kopiering
Använd följande procedur för att redigera en befintlig säkerhets kopierings princip.

#### <a name="to-edit-a-backup-policy"></a>Så här redigerar du en säkerhets kopierings princip
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager.
2. I fönstret **omfattning** klickar du på noden **säkerhets kopierings principer** . Alla säkerhets kopierings principer visas i **resultat** fönstret.
3. Högerklicka på den princip som du vill redigera och klicka sedan på **Redigera**.

    ![Redigera en princip för säkerhets kopiering](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. När fönstret **skapa en princip** visas anger du dina ändringar och klickar sedan på **OK**.

## <a name="delete-a-backup-policy"></a>Ta bort en princip för säkerhets kopiering
Använd följande procedur för att ta bort en säkerhets kopierings princip.

#### <a name="to-delete-a-backup-policy"></a>Ta bort en princip för säkerhets kopiering
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager.
2. I fönstret **omfattning** klickar du på noden **säkerhets kopierings principer** . Alla säkerhets kopierings principer visas i **resultat** fönstret.
3. Högerklicka på den säkerhets kopierings princip som du vill ta bort och klicka sedan på **ta bort**.
4. Klicka på **Ja** när bekräftelse meddelandet visas.

    ![Bekräfta borttagning av säkerhets kopierings princip](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använder StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Lär dig hur du [använder StorSimple-Snapshot Manager för att visa och hantera säkerhets kopierings jobb](storsimple-snapshot-manager-manage-backup-jobs.md).
