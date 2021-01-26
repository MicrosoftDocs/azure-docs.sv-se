---
title: Logga support ärende för Azure Stack Edge Pro, Azure Data Box Gateway | Microsoft Docs
description: Lär dig hur du loggar support förfrågan om problem som rör Azure Stack Edge Pro eller Data Box Gateway beställningar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/07/2021
ms.author: alkohli
ms.openlocfilehash: 411757f4ef4e33ccbc8a69ca2b40598b51c6d13b
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790841"
---
# <a name="open-a-support-ticket-for-azure-stack-edge-pro-and-azure-data-box-gateway"></a>Öppna ett support ärende för Azure Stack Edge Pro och Azure Data Box Gateway

Den här artikeln gäller Azure Stack Edge Pro och Azure Data Box Gateway som hanteras av Azure Stack Edge Pro/Azure Data Box Gateway-tjänsten. Om du stöter på problem med tjänsten kan du skapa en tjänstbegäran för teknisk support. Den här artikeln vägleder dig genom:

* Så här skapar du en support förfrågan.
* Så här hanterar du en livs cykel för support begär Anden från portalen.

## <a name="create-a-support-request"></a>Skapa en supportbegäran

Gör följande för att skapa en supportbegäran:

1. Gå till din Azure Stack Edge Pro-eller Data Box Gateways ordning. Gå till avsnittet **support + fel sökning** och välj sedan **ny supportbegäran**.

2. I **ny supportbegäran**, på fliken **grundläggande** , vidtar du följande steg:

    1. I list rutan **ärende typ** väljer du **teknisk**.
    2. Välj din **prenumeration**.
    3. Under **tjänst** kontrollerar du **Mina tjänster**. Välj **Azure Stack Edge Pro och data Box Gateway** i list rutan.
    4. Välj din **resurs**. Detta motsvarar namnet på din order.
    5. Ge en kort **Sammanfattning** av problemet som du upplever. 
    6. Välj **problem typ**.
    7. Välj en motsvarande **problem under typ** baserat på den problem typ du har valt.
    8. Välj **Nästa: lösningar >>**.

        ![Grunder](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)

3. Gör så här på fliken **information** :

    1. Ange start datum och start tid för problemet.
    2. Ange en **Beskrivning** av problemet.
    3. I **fil uppladdning** väljer du mappikonen för att bläddra bland andra filer som du vill ladda upp.
    4. Kontrol lera **delning av diagnostikinformation**.
    5. En **Support plan** fylls i automatiskt baserat på din prenumeration.
    6. Välj **allvarlighets grad** i list rutan.
    7. Ange en **önskad kontakt metod**.
    8. **Svars tiden** väljs automatiskt utifrån prenumerations planen.
    9. Ange det språk som du föredrar för support.
    10. I **kontakt uppgifterna** anger du ditt namn, din e-postadress, telefonnummer, valfri kontakt, land/region. Microsoft Support använder den här informationen för att kontakta dig för ytterligare information, diagnos och lösning. 
    11. Välj **Nästa: granska + skapa >>**.

        ![Problem](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-2.png)

4. På fliken **Granska + skapa** granskar du informationen som rör support ärendet. Välj **Skapa**. 

    ![Problem 2](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-3.png)

    När du har skapat support ärendet kontaktar en support tekniker så snart som möjligt för att fortsätta med din begäran.

## <a name="get-hardware-support"></a>Få support för maskin vara

Den här informationen gäller endast för Azure Stack enhet. Processen för att rapportera maskin varu problem är följande:

1. Öppna ett support ärende från Azure Portal för ett maskin varu problem. Under **problem typ** väljer du **Azure Stack maskin vara**. Välj under **typen av problem** som **maskin varu fel**.

    ![Maskin varu problem](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-hardware-issue-1.png)

    När du har skapat support ärendet kontaktar en support tekniker så snart som möjligt för att fortsätta med din begäran.

2. Om Microsoft Support bestämmer att detta är ett maskin varu problem inträffar en av följande åtgärder:

    * En fält ersättnings enhet (FRU) för den felande maskin varu delen skickas. För närvarande är strömförsörjnings enheter och solid state-enheter de enda FRUs som stöds.
    * Endast FRUs ersätts inom nästa arbets dag, allt annat kräver att en fullständig system ersättning (FSR) skickas.

3. Om det fastställs att en FRU-byte krävs vid en lokal tid (måndag till fredag) skickas en tekniker på plats nästa arbets dag till din plats för att utföra en överlappande FRU-ersättning. En fullständig system ersättning tar vanligt vis mycket längre tid på grund av att delarna levereras från vår fabrik och kan omfattas av transport-och tull fördröjningar.

## <a name="manage-a-support-request"></a>Hantera en support förfrågan

När du har skapat ett supportärende kan du hantera ärendets livscykel på portalen.

### <a name="to-manage-your-support-requests"></a>Så här hanterar du dina support ärenden

1. Öppna sidan hjälp och support genom att gå till **bläddra > hjälp + support**.

    ![Hantera support förfrågningar](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-manage-support-request-1.png)

2. En lista över de **senaste support förfrågningarna** visas i **Hjälp + Support**.

    <!--[Manage support requests](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)--> 

3. Välj och klicka på en support förfrågan. Du kan visa status och information för den här begäran. Klicka på **+ nytt meddelande** om du vill följa upp den här begäran.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [felsöker problem som rör Azure Stack Edge Pro](azure-stack-edge-troubleshoot.md).
Lär dig hur du [felsöker problem som rör data Box Gateway](../databox-gateway/data-box-gateway-troubleshoot.md).