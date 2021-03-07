---
title: Använd en IoT Edge-modul för att distribuera beräknings belastningen på Azure Stack Edge Pro med GPU | Microsoft Docs
description: Lär dig hur du kör en beräknings arbets belastning med hjälp av en redan skapad IoT Edge-modul på din Azure Stack Edge Pro GPU-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/22/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 6c067fb5f39e82bb1601ce7b4d9dc5e2ce4ac624
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102440147"
---
# <a name="tutorial-run-a-compute-workload-with-iot-edge-module-on-azure-stack-edge-pro-gpu"></a>Självstudie: köra en beräknings arbets belastning med IoT Edge modul på Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

I den här självstudien beskrivs hur du kör en beräknings arbets belastning med hjälp av en IoT Edge modul på din Azure Stack Edge Pro GPU-enhet. När du har konfigurerat beräkningen kommer enheten att transformera data innan de skickas till Azure.

Den här proceduren kan ta cirka 10 till 15 minuter att slutföra.


I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera beräkning
> * Lägg till resurser
> * Lägga till en beräkningsmodul
> * Verifiera datatransformering och överföring

 
## <a name="prerequisites"></a>Förutsättningar

Innan du ställer in en beräknings roll på din Azure Stack Edge Pro GPU-enhet ser du till att:

- Du har aktiverat din Azure Stack Edge Pro-enhet enligt beskrivningen i [aktivera din Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).
- Du har en IoT Edge-modul som du kan köra på dina data. I den här självstudien använde vi en `filemove2` modul som flyttar data från den lokala Edge-resursen på enheten till Edge-resurs från den plats där data ska Azure Storage kontot.


## <a name="configure-compute"></a>Konfigurera beräkning

[!INCLUDE [configure-compute](../../includes/azure-stack-edge-gateway-configure-compute.md)]


## <a name="add-shares"></a>Lägg till resurser

För den enkla distributionen i den här självstudien behöver du två resurser: en Edge-resurs och en annan Edge-lokal resurs.

1. Gör så här om du vill lägga till en Edge-resurs på enheten:

    1. I din Azure Stack Edge-resurs går du till **Cloud Storage gateway > resurser**.
    2. I kommando fältet väljer du **+ Lägg till resurs**.
    3. På bladet **Lägg till resurs** anger du resurs namnet och väljer resurs typ.
    4. Om du vill montera Edge-resursen markerar du kryss rutan för att **använda resurs med Edge Compute**.
    5. Välj **lagrings kontot**, **lagrings tjänsten**, en befintlig användare och välj sedan **skapa**.

        ![Lägg till en Edge-resurs](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-1.png) 


    > [!NOTE]
    > För att montera NFS-resurs till Compute måste Compute-nätverket konfigureras på samma undernät som virtuell NFS-IP-adress. Mer information om hur du konfigurerar Compute Network finns i [Aktivera Compute Network på Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

    Edge-resursen skapas och du får ett meddelande om att det har skapats. Resurslistan kan vara uppdaterad men du måste vänta tills resursen har skapats.

2. Om du vill lägga till en Edge-lokal resurs på enheten upprepar du alla steg i föregående steg och markerar kryss rutan för att **Konfigurera som Edge-lokal resurs**. Data i den lokala resursen förblir på enheten.

    ![Lägg till en lokal Edge-resurs](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-2.png)

    Om du skapade en lokal NFS-resurs så använder du följande kommandoalternativ för fjärrsynkronisering (rsync) för att kopiera filer till resursen:

    `rsync <source file path> < destination file path>`

    Mer information om `rsync` kommandot finns i [ `Rsync` dokumentationen](https://www.computerhope.com/unix/rsync.htm).
 
3. Gå till **Cloud Storage gateway >-resurser** för att se den uppdaterade listan över resurser.

    ![Uppdaterad lista över resurser](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Lägg till en modul

Du kan lägga till en anpassad eller en fördefinierad modul. Enheten ingår inte i förväg inbyggda eller anpassade moduler. Om du vill lära dig hur du skapar en anpassad modul går du till [utveckla en C#-modul för din Azure Stack Edge Pro-enhet](azure-stack-edge-j-series-create-iot-edge-module.md).

I det här avsnittet lägger du till en anpassad modul till den IoT Edge enhet som du skapade i [utveckla en C#-modul för din Azure Stack Edge Pro](azure-stack-edge-j-series-create-iot-edge-module.md). Den här anpassade modulen tar filer från en lokal Edge-resurs på gräns enheten och flyttar dem till en gräns (moln) resurs på enheten. Molnresursen pushar sedan filerna till det Azure-lagringskonto som är associerat med molnresursen.

Gör så här om du vill lägga till en modul:

1. Gå till **IoT Edge > moduler**. I kommando fältet väljer du **+ Lägg till modul**. 

2. Ange följande värden i bladet **Lägg till modul** :

    
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

    ![Lägg till och konfigurera modul](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-1.png)

3. Välj **Lägg till**. Modulen läggs till. Sidan **IoT Edge > moduler** uppdateringar visar att modulen har distribuerats. Körnings status för modulen som du har lagt till ska *köras*.

    ![Modulen har distribuerats](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Verifiera datatransformering och överföring

Det sista steget är att se till att modulen kör och bearbetar data som förväntat. Körningsstatusen för modulen bör vara aktiv för din IoT Edge-enhet i IoT Hub-resursen.

Kontrol lera att modulen körs och bearbetar data som förväntat genom att göra följande:


1. I Utforskaren ansluter du till både de lokala resurserna och kant resurserna som du skapade tidigare. Se stegen 

    ![Anslut till lokala och yttre moln resurser](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-1.png) 
 
1. Lägg till data i den lokala resursen.

    ![Fil kopierad till Edge-lokal resurs](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-2.png) 
 
   Data flyttas till molnresursen.

    ![Filen har flyttats till gräns moln resursen](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-3.png)  

   Data pushas sedan från molnresursen till lagringskontot. Om du vill visa data kan du använda Storage Explorer eller Azure Storage i portalen.

    ![Verifiera data i lagrings kontot](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-4.png)
 
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
> [Använd lokalt webb gränssnitt för att administrera Azure Stack Edge Pro](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md)
