---
title: ta med fil
description: inkludera fil
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 10/18/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: 69259023484457249acd4c7b4a65c4cbe3e660ca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96184762"
---
## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Ett Recovery Service-valv är en hanteringsenhet som lagrar återställningspunkter skapade över tid och tillhandahåller ett gränssnitt för säkerhetskopieringsrelaterade åtgärder. Dessa inkluderar säkerhetskopieringar på begäran, återställningar och att skapa säkerhetskopieringsprinciper.

Om du vill skapa ett Recovery Services-valv följer du stegen nedan.

1. Logga in på din prenumeration i [Azure-portalen](https://portal.azure.com/).

1. På menyn till vänster väljer du **Alla tjänster**.

    ![Välj Alla tjänster](./media/backup-create-rs-vault/click-all-services.png)

1. I dialogrutan **Alla tjänster** anger du *Recovery Services*. Listan med resurser filtreras enligt din inmatning. I resurslistan väljer du **Recovery Services-valv**.

    ![Ange och välja Recovery Services-valv](./media/backup-create-rs-vault/all-services.png)

    Listan över Recovery Services-valv i prenumerationen visas.

1. På instrumentpanelen **Recovery Services-valv** väljer du **Lägg till**.

    ![Lägg till ett Recovery Services-valv](./media/backup-create-rs-vault/add-button-create-vault.png)

    Dialogrutan **Recovery Services-valv** öppnas. Ange värden för **Namn**, **Prenumeration**, **Resursgrupp** och **Plats**.

    ![Konfigurera Recovery Services-valvet](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Namn**: Ange ett eget namn som identifierar valvet. Namnet måste vara unikt för Azure-prenumerationen. Ange ett namn som innehåller minst 2 tecken, men inte fler än 50. Namnet måste börja med en bokstav och får endast innehålla bokstäver, siffror och bindestreck.
   - **Prenumeration**: Välj den prenumeration som ska användas. Om du bara är medlem i en prenumeration ser du det namnet. Om du inte är säker på vilken prenumeration du ska använda, använder du standardprenumerationen (den föreslagna). Du kan bara välja mellan flera alternativ om ditt arbets- eller skolkonto är associerat med mer än en Azure-prenumeration.
   - **Resursgrupp**: Använd en befintlig resursgrupp eller skapa en ny. Om du vill se en lista med tillgängliga resursgrupper i prenumerationen, väljer du **Använd befintliga** och en resurs i listrutan. Skapa en ny resursgrupp genom att välja **Skapa ny** och ange namnet. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../articles/azure-resource-manager/management/overview.md).
   - **Plats**: Välj ett geografiskt område för valvet. Om du ska skapa ett valv som skyddar datakällor *måste* valvet finnas i samma region som datakällan.

      > [!IMPORTANT]
      > Om du inte är säker på var datakällan finns, stänger du dialogrutan. Gå till listan med resurser i portalen. Om du har datakällor i flera regioner, skapar du ett Recovery Services-valv i varje region. Skapa valvet på den första platsen innan du skapar ett valv för en annan plats. Du behöver inte ange lagringskonton för att lagra säkerhetskopieringsdata. Recovery Services-valvet och Azure Backup hanterar detta automatiskt.
      >
      >

1. När du har angett värdena väljer du **Granska och skapa**.

    ![Skärm bild som visar knappen granska + skapa i processen skapa en Recovery Services valv.](./media/backup-create-rs-vault/review-and-create.png)

1. När du är redo att skapa Recovery Services-valvet väljer du **Skapa**.

    ![Skapa Recovery Services-valvet](./media/backup-create-rs-vault/click-create-button.png)

    Det kan ta en stund innan Recovery Services-valvet har skapats. Övervaka statusmeddelandena i området **Meddelanden** i portalens övre högra hörn. När valvet har skapats visas det i listan med Recovery Services-valv. Om du inte ser valvet väljer du **Uppdatera**.

     ![Uppdatera listan med säkerhetskopieringsvalv](./media/backup-create-rs-vault/refresh-button.png)

>[!IMPORTANT]
> Vi rekommenderar starkt att du granskar standardinställningarna i **Lagringsreplikeringstyp** och **Säkerhetsinställningar** innan du konfigurerar säkerhetskopior i valvet. Mer information finns i avsnittet [Ange lagringsredundans](../articles/backup/backup-create-rs-vault.md#set-storage-redundancy).