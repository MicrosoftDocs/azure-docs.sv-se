---
title: Distribuera StorSimple Enhetshanteraren-tjänsten | Microsoft Docs
description: Förklarar hur du skapar och tar bort StorSimple-Enhetshanteraren tjänsten i Azure Portal och beskriver hur du hanterar tjänst registrerings nyckeln.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 28499494-8c4d-4a7f-9d44-94b2b8a93c93
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 92041be4d3d5ef4d980c17fa0a5870b740c1784a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95995033"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>Distribuera StorSimple Enhetshanteraren-tjänsten för StorSimple Virtual Array

## <a name="overview"></a>Översikt

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Tjänsten StorSimple Enhetshanteraren körs i Microsoft Azure och ansluter till flera StorSimple-enheter. När du har skapat tjänsten kan du använda den för att hantera enheterna från Microsoft Azure-portalen som körs i en webbläsare. På så sätt kan du övervaka alla enheter som är anslutna till StorSimple Enhetshanteraren-tjänsten från en enda central plats, vilket minimerar administrations belastningen.

Vanliga uppgifter som rör en StorSimple Enhetshanteraren tjänst är:

* Skapa en tjänst
* Ta bort en tjänst
* Hämta nyckel för tjänstregistrering
* Återskapa tjänst registrerings nyckeln

I den här självstudien beskrivs hur du utför vart och ett av föregående uppgifter. Informationen i den här artikeln gäller endast virtuella StorSimple-matriser. Mer information om StorSimple 8000-serien finns i [distribuera en StorSimple Manager-tjänst](./storsimple-8000-manage-service.md).

## <a name="create-a-service"></a>Skapa en tjänst

Om du vill skapa en tjänst måste du ha:

* En prenumeration med en Enterprise-avtal
* Ett aktivt Microsoft Azure lagrings konto
* Den fakturerings information som används för åtkomst hantering

Du kan också välja att generera ett lagrings konto när du skapar tjänsten.

En enskild tjänst kan hantera flera enheter. En enhet kan dock inte omfatta flera tjänster. Ett stort företag kan ha flera tjänst instanser för att arbeta med olika prenumerationer, organisationer eller till och med distributions platser.

> [!NOTE]
> Du behöver separata instanser av StorSimple Enhetshanteraren-tjänsten för att hantera StorSimple 8000-seriens enheter och StorSimple virtuella matriser.


Utför följande steg för att skapa en tjänst.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>Ta bort en tjänst

Innan du tar bort en tjänst ser du till att inga anslutna enheter använder den. Om tjänsten används inaktiverar du de anslutna enheterna. Åtgärden inaktivera kommer att påverka anslutningen mellan enheten och tjänsten, men behåll enhets data i molnet.

> [!IMPORTANT]
> När en tjänst har tagits bort går det inte att ångra åtgärden. Alla enheter som använder tjänsten måste återställas fabriks återställning innan den kan användas med en annan tjänst. I det här scenariot går det inte att förlora lokala data på enheten, och konfigurationen.
 

Utför följande steg för att ta bort en tjänst.

#### <a name="to-delete-a-service"></a>Ta bort en tjänst

1. Gå till **Alla resurser**. Sök efter din StorSimple Enhetshanteraren-tjänst. Välj den tjänst som du vill ta bort.
   
    ![Välj den tjänst som ska tas bort](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Gå till instrument panelen för tjänsten för att se till att det inte finns några enheter anslutna till tjänsten. Om det inte finns några enheter som är registrerade med den här tjänsten visas även ett informations meddelande för-resultatet. Klicka på **Ta bort**.
   
    ![Ta bort tjänst](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. När du uppmanas att bekräfta klickar du på **Ja** i bekräftelse meddelandet. 
   
    ![Bekräfta borttagning av tjänst](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. Det kan ta några minuter för tjänsten att tas bort. När tjänsten har tagits bort kommer du att meddelas.
   
    ![Lyckad borttagning av tjänst](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

Listan över tjänster kommer att uppdateras.

 ![Uppdaterad lista över tjänster](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>Hämta nyckel för tjänstregistrering
När du har skapat en tjänst måste du registrera din StorSimple-enhet med tjänsten. För att registrera din första StorSimple-enhet behöver du tjänst registrerings nyckeln. Om du vill registrera ytterligare enheter med en befintlig StorSimple-tjänst behöver du både registrerings nyckeln och krypterings nyckeln för tjänst data (som genereras på den första enheten under registreringen). Mer information om krypterings nyckeln för tjänst data finns i [StorSimple Security](./storsimple-8000-security.md). Du kan hämta registrerings nyckeln genom att gå till bladet **nycklar** för din tjänst.

Utför följande steg för att hämta tjänst registrerings nyckeln.

#### <a name="to-get-the-service-registration-key"></a>Hämta tjänst registrerings nyckeln
1. I bladet **StorSimple Enhetshanteraren** går du till **hanterings &gt;** **nycklar**.
   
   ![Bladet Nycklar](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. I bladet **nycklar** visas en tjänst registrerings nyckel. Kopiera registrerings nyckeln med hjälp av kopierings ikonen. 

Behåll tjänst registrerings nyckeln på en säker plats. Du behöver den här nyckeln, samt krypterings nyckeln för tjänst data, för att registrera ytterligare enheter med tjänsten. När du har hämtat tjänst registrerings nyckeln måste du konfigurera enheten via Windows PowerShell för StorSimple-gränssnittet.

## <a name="regenerate-the-service-registration-key"></a>Återskapa tjänst registrerings nyckeln
Du måste återskapa en tjänst registrerings nyckel om du måste utföra nyckel rotation eller om listan över tjänst administratörer har ändrats. När du återskapar nyckeln används den nya nyckeln bara för att registrera efterföljande enheter. De enheter som redan har registrerats påverkas inte av den här processen.

Utför följande steg för att återskapa en tjänst registrerings nyckel.

#### <a name="to-regenerate-the-service-registration-key"></a>Återskapa tjänst registrerings nyckeln
1. I bladet **StorSimple Enhetshanteraren** går du till **hanterings &gt;** **nycklar**.
   
   ![Bladet nycklar igen](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. Klicka på **Återskapa** på bladet **nycklar** .
   
   ![Klicka på återskapa](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. I bladet **Återskapa tjänst registrerings nyckel** granskar du den åtgärd som krävs när nycklarna återskapas. Alla efterföljande enheter som är registrerade med den här tjänsten kommer att använda den nya registrerings nyckeln. Klicka på **Återskapa** för att bekräfta. Du får ett meddelande när registreringen är klar.
   
   ![Bekräfta återskapande nyckel](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. En ny tjänst registrerings nyckel kommer att visas.
   
    ![Ytterligare bekräfta omgenerering av nyckel](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Kopiera den här nyckeln och spara den för att registrera nya enheter med tjänsten.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [kommer igång](storsimple-virtual-array-deploy1-portal-prep.md) med en virtuell StorSimple-matris.
* Lär dig hur du [administrerar din StorSimple-enhet](storsimple-ova-web-ui-admin.md).