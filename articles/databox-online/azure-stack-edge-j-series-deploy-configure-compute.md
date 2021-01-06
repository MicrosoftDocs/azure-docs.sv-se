---
title: Självstudie för att filtrera, analysera data med Compute på Azure Stack Edge Pro med GPU | Microsoft Docs
description: Lär dig hur du konfigurerar Compute-rollen på Azure Stack Edge Pro GPU-enhet och använder den för att transformera data innan du skickar dem till Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 28b5c107fb35c7bda9b1680050b92004436b98ff
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935507"
---
# <a name="tutorial-transform-data-with-azure-stack-edge-pro"></a>Självstudie: transformera data med Azure Stack Edge Pro

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

I den här självstudien beskrivs hur du konfigurerar en beräknings roll på din Azure Stack Edge Pro-enhet. När du har konfigurerat Compute-rollen kan Azure Stack Edge Pro omvandla data innan de skickas till Azure.

Den här proceduren kan ta cirka 10 till 15 minuter att slutföra.


I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera beräkning
> * Lägg till resurser
> * Lägga till en beräkningsmodul
> * Verifiera datatransformering och överföring

 
## <a name="prerequisites"></a>Krav

Innan du ställer in en beräknings roll på din Azure Stack Edge Pro-enhet ser du till att:
- Du har aktiverat din Azure Stack Edge Pro-enhet enligt beskrivningen i [aktivera din Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).


## <a name="configure-compute"></a>Konfigurera beräkning

Om du vill konfigurera Compute på Azure Stack Edge Pro skapar du en IoT Hub resurs.

1. I Azure Portal i Azure Stack Edge-resurs går du till **Översikt** och väljer **IoT Edge**.

   ![Kom igång med Compute](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-1.png)

2. I **aktivera IoT Edge tjänst** väljer du **Lägg till**.

   ![Konfigurera beräkning](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-2.png)

3. I **skapa IoT Edge tjänst** anger du inställningarna för din IoT Hub-resurs:

   |Fält   |Värde    |
   |--------|---------|
   |Prenumeration      | Prenumeration som används av Azure Stack Edge-resursen. |
   |Resursgrupp    | Resurs grupp som används av Azure Stack Edge-resursen. |
   |IoT Hub           | Välj från **Skapa ny** eller **Använd befintlig**. <br> Som standard används nivån Standard (S1) till att skapa en IoT-resurs. Om du vill använda en IoT-resurs på kostnadsfri nivå skapar du en sådan och väljer sedan den befintliga resursen. <br> I varje fall använder IoT Hub resursen samma prenumeration och resurs grupp som används av Azure Stack Edge-resursen.     |
   |Namn              | Om du inte vill använda det standard namn som angetts för en ny IoT Hub resurs anger du ett annat namn. |

    ![Kom igång med Compute 2](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-3.png)

4. När du är klar med inställningarna väljer du **Granska + skapa**. Granska inställningarna för din IoT Hub resurs och välj **skapa**.

   Det tar flera minuter att skapa en resurs för en IoT Hub resurs. När resursen har skapats visar **översikten** IoT Edges tjänsten körs nu.

    ![Kom igång med Compute 3](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-4.png)

5. För att bekräfta att Edge Compute-rollen har kon figurer ATS, väljer du **Egenskaper**.

   ![Kom igång med Compute 4](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-5.png)

   När Edge-beräkningsrollen har konfigurerats på Edge-enheten så skapas två enheter: en IoT-enhet och en IoT Edge-enhet. Bägge enheter kan visas i IoT Hub-resursen. En IoT Edge runtime körs också på den här IoT Edge enheten. För tillfället är det bara Linux-plattformen tillgänglig för din IoT Edge-enhet.


## <a name="add-shares"></a>Lägg till resurser

För den enkla distributionen i den här självstudien behöver du två resurser: en Edge-resurs och en annan Edge-lokal resurs.

1. Lägg till en Edge-resurs på enheten genom att utföra följande steg:

    1. I din Azure Stack Edge-resurs går du till **Edge compute > kom igång**.
    2. Välj **Lägg till** på panelen **Lägg till resurs (er)** .

        ![Lägg till resurs panel](./media/azure-stack-edge-j-series-deploy-configure-compute/add-share-1.png) 

    3. På bladet **Lägg till resurs** anger du resurs namnet och väljer resurs typ.
    4. Om du vill montera Edge-resursen markerar du kryss rutan för att **använda resurs med Edge Compute**.
    5. Välj **lagrings kontot**, **lagrings tjänsten**, en befintlig användare och välj sedan **skapa**.

        ![Lägg till en Edge-resurs](./media/azure-stack-edge-j-series-deploy-configure-compute/add-edge-share-1.png) 

    Om du har skapat en lokal NFS-resurs använder du följande `rsync` kommando alternativ för fjärrsynkronisering () för att kopiera filer till resursen:

    `rsync <source file path> < destination file path>`

    Mer information om `rsync` kommandot finns i [ `Rsync` dokumentationen](https://www.computerhope.com/unix/rsync.htm).

    > [!NOTE]
    > För att montera NFS-resurs till Compute måste Compute-nätverket konfigureras på samma undernät som virtuell NFS-IP-adress. Mer information om hur du konfigurerar Compute Network finns i [Aktivera Compute Network på Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

    Edge-resursen skapas och du får ett meddelande om att det har skapats. Resurslistan kan vara uppdaterad men du måste vänta tills resursen har skapats.

2. Lägg till en lokal Edge-resurs på gräns enheten genom att upprepa alla steg i föregående steg och markera kryss rutan för att **Konfigurera som Edge-lokal resurs**. Data i den lokala resursen förblir på enheten.

    ![Lägg till en lokal Edge-resurs](./media/azure-stack-edge-j-series-deploy-configure-compute/add-edge-share-2.png)

  
3. Välj **Lägg till resurs (er)** om du vill se den uppdaterade listan över resurser.

    ![Uppdaterad lista över resurser](./media/azure-stack-edge-j-series-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Lägg till en modul

Du kan lägga till en anpassad eller en fördefinierad modul. Det finns inga anpassade moduler på den här Edge-enheten. Om du vill lära dig hur du skapar en anpassad modul går du till [utveckla en C#-modul för din Azure Stack Edge Pro-enhet](azure-stack-edge-j-series-create-iot-edge-module.md).

I det här avsnittet lägger du till en anpassad modul till den IoT Edge enhet som du skapade i [utveckla en C#-modul för din Azure Stack Edge Pro](azure-stack-edge-j-series-create-iot-edge-module.md). Den här anpassade modulen tar filer från en lokal Edge-resurs på gräns enheten och flyttar dem till en gräns (moln) resurs på enheten. Molnresursen pushar sedan filerna till det Azure-lagringskonto som är associerat med molnresursen.

1. Gå till **Edge compute > kom igång**. På panelen **Lägg till moduler** väljer du scenario typen **enkel**. Välj **Lägg till**.
2. Ange följande värden i bladet **Konfigurera och Lägg till modul** :

    
    |Fält  |Värde  |
    |---------|---------|
    |Namn     | Ett unikt namn för modulen. Den här modulen är en Docker-behållare som du kan distribuera till den IoT Edge enhet som är kopplad till din Azure Stack Edge Pro.        |
    |Bild-URI     | Avbildnings-URI för motsvarande behållar avbildning för modulen.        |
    |Autentiseringsuppgifter krävs     | Om det här alternativet markeras används användar namn och lösen ord för att hämta moduler med en matchande URL.        |
    |Inmatad resurs     | Välj en inmatad resurs. Den lokala Edge-resursen är den ingående resursen i det här fallet. Modulen som används här flyttar filer från den lokala Edge-resursen till en Edge-resurs där de överförs till molnet.        |
    |Utgående resurs     | Välj en utgående resurs. Edge-resursen är den utgående resursen i det här fallet.        |
    |Utlösartyp     | Välj från **fil** eller **schema**. En filutlösare utlöses när en filhändelse inträffar, till exempel att en fil skrivs till den angivna resursen. En schemalagd utlösare aktiveras baserat på ett schema du definierar.         |
    |Utlösarens namn     | Ett unikt namn för utlösaren.         |
    |Miljövariabler| Valfri information som hjälper dig att definiera miljön där modulen ska köras.   |

    ![Lägg till och konfigurera modul](./media/azure-stack-edge-j-series-deploy-configure-compute/add-module-1.png)

3. Välj **Lägg till**. Modulen läggs till. Gå till **översikts** sidan. Panelen **moduler** uppdateras för att indikera att modulen har distribuerats. 

    ![Modulen har distribuerats](./media/azure-stack-edge-j-series-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Verifiera datatransformering och överföring

Det slutliga steget är att se till att modulen är ansluten och körs som förväntat. Körningsstatusen för modulen bör vara aktiv för din IoT Edge-enhet i IoT Hub-resursen.

Verifiera att modulen körs genom att göra följande:

1. Välj panelen **Lägg till modul** . Då går du till bladet **moduler** . Identifiera den modul som du har distribuerat i listan över moduler. Körnings status för modulen som du har lagt till ska *köras*.

    ![Visa distribuerad modul](./media/azure-stack-edge-j-series-deploy-configure-compute/add-module-3.png)
 
1. I Utforskaren ansluter du till både de lokala resurserna och kant resurserna som du skapade tidigare.

    ![Verifiera datatransformering-1](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-2.png) 
 
1. Lägg till data i den lokala resursen.

    ![Verifiera datatransformering-2](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-3.png) 
 
   Data flyttas till molnresursen.

    ![Verifiera datatransformering-3](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-4.png)  

   Data pushas sedan från molnresursen till lagringskontot. Om du vill visa data kan du använda Storage Explorer.

    <!--![Verify data transform](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-5.png)-->
 
Du har slutfört valideringsprocessen.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera beräkning
> * Lägg till resurser
> * Lägga till en beräkningsmodul
> * Verifiera datatransformering och överföring

Information om hur du administrerar din Azure Stack Edge Pro-enhet finns i:

> [!div class="nextstepaction"]
> [Använd lokalt webb gränssnitt för att administrera Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)
