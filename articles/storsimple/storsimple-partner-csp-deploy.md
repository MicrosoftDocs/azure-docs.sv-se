---
title: Distribuera virtuell StorSimple-matris för Cloud Solution Provider-program
description: Lär dig hur en CSP-partner kan lägga till en kund eller en ny prenumeration på en befintlig kund och sedan skapa en tjänst för att distribuera en virtuell StorSimple-matris i CSP.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/08/2017
ms.author: alkohli
ms.openlocfilehash: dc0cf718ee51e23fb749bdf57d5344977de009d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021868"
---
# <a name="deploy-storsimple-virtual-array-for-cloud-solution-provider-program"></a>Distribuera virtuell StorSimple-matris för Cloud Solution Provider-program

## <a name="overview"></a>Översikt

StorSimple virtuella matris kan distribueras av CSP-partner (Cloud Solution Provider) för sina kunder. En CSP-partner kan skapa en StorSimple Enhetshanteraren-tjänst. Den här tjänsten kan sedan användas för att distribuera och hantera StorSimple virtuella matriser och associerade resurser, volymer och säkerhets kopior.

Den här artikeln beskriver hur en CSP-partner kan lägga till en kund eller en ny prenumeration på en befintlig kund och sedan skapa en tjänst för att distribuera en virtuell StorSimple-matris i CSP.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrol lera att:

- Du är registrerad under CSP-programmet.
- Du har giltiga inloggnings uppgifter för [partner Center](https://partnercenter.microsoft.com/) . Med autentiseringsuppgifterna kan du logga in på Partner portalen för att lägga till nya kunder, söka efter kunder eller navigera till ett kund konto från partnerns instrument panel. CSP: n kan fungera som StorSimple-administratör för kundens räkning i Azure Portal.
                             
## <a name="add-a-customer"></a>Lägg till en kund

Om du lägger till en kund skapas en prenumeration automatiskt. Om du vill lägga till en kund (och automatiskt skapa en prenumeration) utför du följande steg i Partner portalen.

1. Gå till [partner Center](https://partnercenter.microsoft.com/) och logga in med dina AUTENTISERINGSUPPGIFTER för CSP. Klicka på **instrument panel**.

     ![Instrument panel i Partner Center](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. Klicka på **kunder** i det vänstra fönstret. I den högra rutan klickar du på **Lägg till kunder**. Ange information om kunden. Klicka på **Nästa: prenumerationer** för att skapa en kund prenumeration.

    ![Lägg till kund](./media/storsimple-partner-csp-deploy/image2.png)

3.  Välj **Microsoft Azure** erbjudande. Bläddra till slutet av sidan och klicka på **Granska**.

    ![Granska prenumerations information](./media/storsimple-partner-csp-deploy/image3.png)
                              
4. Granska informationen och klicka på **Skicka**.

    ![Skicka prenumeration](./media/storsimple-partner-csp-deploy/image4.png)

5. Spara bekräftelse informationen för framtida bruk.

    ![Spara bekräftelse](./media/storsimple-partner-csp-deploy/image5.png)

6. Hitta eller navigera till kunden som du nyss lade till. Klicka på **företags namnet** om du vill öka detalj nivån.

    ![Sök efter kunden](./media/storsimple-partner-csp-deploy/image6.png)  

7. I den vänstra rutan väljer du **tjänst hantering**. I den högra rutan under **Administrera tjänster** klickar du på **Microsoft Azure-hanteringsportal** för att logga in som Azure-administratör för din kund.

    ![Logga in på Azure Portal](./media/storsimple-partner-csp-deploy/image9.png)

8. Skapa en StorSimple Enhetshanteraren genom att klicka på **+ ny** och söka efter eller navigera till **StorSimple-serien för virtuella enheter**. Mer information finns i [distribuera en StorSimple Enhetshanteraren-tjänst](storsimple-virtual-array-manage-service.md).

    ![Skapa StorSimple Enhetshanteraren-tjänst](./media/storsimple-partner-csp-deploy/image8.png)


## <a name="add-a-subscription"></a>Lägga till en prenumeration

I vissa fall kan du ha en befintlig kund och du måste lägga till en prenumeration. Om du vill lägga till en prenumeration på en befintlig kund utför du följande steg i Partner portalen.

1. Gå till [partner Center](https://partnercenter.microsoft.com/) och logga in med dina AUTENTISERINGSUPPGIFTER för CSP. Klicka på **instrument panel**.

     ![Instrument panel i Partner Center](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. Klicka på **kunder** i det vänstra fönstret. Hitta eller navigera till kunden som du vill lägga till en prenumeration på. Klicka på ![ ikonen Expandera kontroll ikon ](./media/storsimple-partner-csp-deploy/expand_pane_icon.png) för att expandera raden för företags namnet för din kund. I detalj klickar du på **Lägg till prenumerationer**.

    ![Kunder](./media/storsimple-partner-csp-deploy/image10.png)

3. Markera **Microsoft Azure** för de **främsta erbjudandena** i prenumerationen och klicka på **Skicka**. Detta skapar en ny prenumeration.

    ![Lägg till ny prenumeration](./media/storsimple-partner-csp-deploy/image11.png)

6. När en ny prenumeration har skapats klickar du på **<--kunder** i den vänstra rutan för att gå tillbaka till sidan **kunder** . Sök efter kunden som du precis har skapat en prenumeration för. Klicka på **företags namnet** om du vill öka detalj nivån.

    ![Sök efter kunden](./media/storsimple-partner-csp-deploy/image6.png)  

7. I den vänstra rutan väljer du **tjänst hantering**. I den högra rutan under **Administrera tjänster** klickar du på **Microsoft Azure-hanteringsportal** för att logga in som Azure-administratör för din kund.

    ![Logga in på Azure Portal](./media/storsimple-partner-csp-deploy/image9.png)

8. Skapa en StorSimple Enhetshanteraren genom att klicka på **+ ny** och söka efter eller navigera till **StorSimple-serien för virtuella enheter**. Mer information finns i [distribuera en StorSimple Enhetshanteraren-tjänst](storsimple-virtual-array-manage-service.md).

    ![Skapa StorSimple Enhetshanteraren-tjänst](./media/storsimple-partner-csp-deploy/image8.png)

## <a name="next-steps"></a>Nästa steg

- Om du har fler frågor om StorSimple i CSP går du till [StorSimple i CSP: vanliga frågor och svar](storsimple-partner-csp-faq.md).
- Om du är redo att distribuera din StorSimple går du till [distribuera din StorSimple i CSP](storsimple-partner-csp-deploy.md).
