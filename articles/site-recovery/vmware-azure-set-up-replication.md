---
title: Konfigurera replikeringsprinciper för katastrof återställning i VMware med Azure Site Recovery | Microsoft Docs
description: Beskriver hur du konfigurerar replikeringsinställningar för haveri beredskap i VMware till Azure med Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 45921bdf802a649b7b802f44d2842a543e44f02b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84699608"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery"></a>Konfigurera och hantera replikeringsprinciper för haveriberedskap för VMware

Den här artikeln beskriver hur du konfigurerar en replikeringsprincip när du replikerar virtuella VMware-datorer till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).

## <a name="create-a-policy"></a>Skapa en princip

1. Välj **Hantera**  >  **Site Recovery infrastruktur**.
2. I **för VMware och fysiska datorer** väljer du **replikeringsprinciper**.
3. Klicka på **+ replikeringsprincip** och ange namnet på principen.
4. I **tröskelvärdet för RPO** anger du RPO (mål för återställningspunkt)-gränsen. Aviseringar genereras när kontinuerlig replikering överskrider den här gränsen.
5. I **Återställningspunkt för kvarhållning** anger du kvarhållningsperioden (i antal timmar) för varje återställningspunkt. Skyddade datorer kan återställas till valfri punkt inom en kvarhållningsperiod. Upp till 24 timmars kvarhållning stöds för datorer som har replikerats till Premium Storage. Upp till 72 timmar stöds för standard lagring.
6. I **frekvensen för programkonsekventa ögonblicks bilder** väljer du i list rutan hur ofta (i timmar) återställnings punkter som innehåller programkonsekventa ögonblicks bilder ska skapas. Om du vill inaktivera generering av program konsekvens punkter väljer du "off"-värdet i list rutan.
7. Klicka på **OK**. Principen bör skapas inom 30 till 60 sekunder.

När du skapar en replikeringsprincip skapas en matchande princip för återställning efter fel automatiskt med suffixet "failback". När du har skapat principen kan du redigera den genom att välja den > **Redigera inställningar**.

## <a name="associate-a-configuration-server"></a>Associera en konfigurations Server

Koppla replikeringsprincipen till din lokala konfigurations Server.

1. Klicka på **associera** och Välj konfigurations servern.

    ![Associera konfigurationsserver](./media/vmware-azure-set-up-replication/associate1.png)
2. Klicka på **OK**. Konfigurationsservern bör associeras inom en till två minuter.

    ![Associering av konfigurationsserver](./media/vmware-azure-set-up-replication/associate2.png)

## <a name="edit-a-policy"></a>Redigera en princip

Du kan ändra en replikeringsprincip när du har skapat den.

- Ändringar i principen tillämpas på alla datorer som använder principen.
- Om du vill associera replikerade datorer med en annan replikeringsprincip måste du inaktivera och återaktivera skyddet för de aktuella datorerna.

Redigera en princip på följande sätt:
1. Välj **Hantera**  >  principer för replikering av **Site Recovery infrastruktur**  >  .
2. Välj den replikeringsprincip som du vill ändra.
3. Klicka på **Redigera inställningar** och uppdatera tröskelvärdena för återställnings punktens tröskel/återställnings punkts antal timmar/för programkonsekventa ögonblicks bilder vid behov.
4. Om du vill inaktivera genereringen av program konsekvens punkter väljer du "off" i list rutan för fältet **app-konsekvent ögonblicks bild frekvens**.
5. Klicka på **Spara**. Principen bör uppdateras om 30 till 60 sekunder.



## <a name="disassociate-or-delete-a-replication-policy"></a>Ta bort kopplingen eller ta bort en replikeringsprincip

1. Välj replikeringsprincip.
    a. Om du vill koppla bort principen från konfigurations servern kontrollerar du att inga replikerade datorer använder principen. Klicka **sedan på koppla** bort.
    b. Om du vill ta bort principen kontrollerar du att den inte är associerad med en konfigurations Server. Klicka sedan på **ta bort**. Det bör ta 30-60 sekunder att ta bort.
2. Klicka på **OK**.
