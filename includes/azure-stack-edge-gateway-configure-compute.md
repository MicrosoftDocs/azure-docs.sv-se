---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/07/2021
ms.author: alkohli
ms.openlocfilehash: c51577882e75facb1d8eb03c7cfab82467c5ec51
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99524966"
---
Om du vill konfigurera Compute på Azure Stack Edge Pro skapar du en IoT Hub resurs via Azure Portal.

1. I Azure Portal i Azure Stack Edge-resurs går du till **Översikt** och väljer **IoT Edge**.

   ![Kom igång med Compute](./media/azure-stack-edge-gateway-configure-compute/configure-compute-1.png)

2. I **aktivera IoT Edge tjänst** väljer du **Lägg till**.

   ![Konfigurera beräkning](./media/azure-stack-edge-gateway-configure-compute/configure-compute-2.png)

3. Ange följande information på bladet **Konfigurera Edge Compute** :
   
    |Fält  |Värde  |
    |---------|---------|
    |Prenumeration     |Välj en prenumeration för din IoT Hub-resurs. Du kan använda samma prenumeration som används av Azure Stack Edge-resursen.         |
    |Resursgrupp     |Välj en resurs grupp för din IoT Hub-resurs. Du kan använda samma resurs grupp som används av Azure Stack Edge-resursen.         |
    |IoT Hub     | Välj från **ny** eller **befintlig**. <br> Som standard används nivån Standard (S1) till att skapa en IoT-resurs. Om du vill använda en IoT-resurs på kostnadsfri nivå skapar du en sådan och väljer sedan den befintliga resursen. <br> I båda fallen använder IoT Hub-resursen samma prenumeration och resursgrupp som Azure Stack Edge-resursen använder.     |
    |Name     |Acceptera standard namnet eller ange ett namn för din IoT Hub-resurs.         |

   ![Kom igång med Compute 2](./media/azure-stack-edge-gateway-configure-compute/configure-compute-3.png)

4. När du är klar med inställningarna väljer du **Granska + skapa**. Granska inställningarna för din IoT Hub resurs och välj **skapa**.

   Det tar flera minuter att skapa en resurs för en IoT Hub resurs. När resursen har skapats visar **översikten** IoT Edges tjänsten körs nu.

   ![Kom igång med Compute 3](./media/azure-stack-edge-gateway-configure-compute/configure-compute-4.png)

5. För att bekräfta att Edge Compute-rollen har kon figurer ATS, går du till **IoT Edge > egenskaper**.

   ![Kom igång med Compute 4](./media/azure-stack-edge-gateway-configure-compute/configure-compute-5.png)

   När Edge-beräkningsrollen har konfigurerats på Edge-enheten så skapas två enheter: en IoT-enhet och en IoT Edge-enhet. Bägge enheter kan visas i IoT Hub-resursen. En IoT Edge runtime körs också på den här IoT Edge enheten. För tillfället är det bara Linux-plattformen tillgänglig för din IoT Edge-enhet.

Det kan ta 20-30 minuter att konfigurera beräkning, eftersom de virtuella datorerna och ett Kubernetes-kluster skapas bakom.

När du har konfigurerat Compute i Azure Portal finns ett Kubernetes-kluster och en standard användare som är associerad med IoT-namnrymden (ett system namn område som styrs av Azure Stack Edge Pro).
