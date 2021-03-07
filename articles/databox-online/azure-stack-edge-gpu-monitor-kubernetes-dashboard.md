---
title: Övervaka din Azure Stack Edge Pro-enhet via Kubernetes-instrumentpanelen | Microsoft Docs
description: Beskriver hur du kommer åt och använder Kubernetes-instrumentpanelen för att övervaka din Azure Stack Edge Pro-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 19b3595228c29814e42af88696972fd81b156190
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443054"
---
# <a name="use-kubernetes-dashboard-to-monitor-your-azure-stack-edge-pro-gpu-device"></a>Använd Kubernetes-instrumentpanelen för att övervaka din Azure Stack Edge Pro GPU-enhet

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Den här artikeln beskriver hur du kommer åt och använder Kubernetes-instrumentpanelen för att övervaka din Azure Stack Edge Pro GPU-enhet. Om du vill övervaka din enhet kan du använda diagram i Azure Portal, Visa Kubernetes-instrumentpanelen eller köra `kubectl` kommandon via enhetens PowerShell-gränssnitt. 

Den här artikeln fokuserar bara på de övervaknings aktiviteter som kan utföras på Kubernetes-instrumentpanelen.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
>
> * Öppna Kubernetes-instrumentpanelen på din enhet
> * Visa moduler som har distribuerats på din enhet
> * Hämta IP-adress för program som distribuerats på din enhet
> * Visa behållar loggar för moduler som har distribuerats på din enhet


## <a name="about-kubernetes-dashboard"></a>Om Kubernetes-instrumentpanel

Kubernetes Dashboard är ett webbaserat användargränssnitt som du kan använda till att felsöka dina containerbaserade program. Kubernetes-instrumentpanelen är ett UI-baserat alternativ till Kubernetes- `kubectl` kommandoraden. Mer information finns i [Kubernetes Dashboard](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/). 

På din Azure Stack Edge Pro-enhet kan du använda Kubernetes-instrumentpanelen i *skrivskyddat* läge för att få en översikt över de program som körs på din Azure Stack Edge Pro-enhet, Visa status för Kubernetes kluster resurser och se eventuella fel som har inträffat på enheten.

## <a name="access-dashboard"></a>Åtkomst instrument panel

Kubernetes-instrumentpanelen är *skrivskyddad* och körs på den Kubernetes huvud noden på port 31000. Följ de här stegen för att få åtkomst till instrument panelen: 

1. I enhetens lokala användar gränssnitt, gå till **enhet** och gå sedan till **enhetens slut punkter**. 
1. Välj **Hämta konfiguration** för att ladda ned en `kubeconfig` som gör det möjligt att komma åt instrument panelen. Spara `config.json` filen på det lokala systemet.
1. Välj instrument panelens URL för Kubernetes för att öppna instrument panelen i en webbläsare.

    ![Kubernetes Dashboard-URL på enhets sidan i lokalt användar gränssnitt](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-url-local-ui-1.png)

1. På **instrument panelen för Kubernetes-instrument panelen** :
    
    1. Välj **kubeconfig**. 
        ![Välj alternativet kubeconfig](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-1.png) 
    1. Välj ellipsen **...** Bläddra och peka på den `kubeconfig` som du laddade ned tidigare på det lokala systemet. Välj **Logga in**.
        ![Bläddra till kubeconfig-fil](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-2.png)    

6. Nu kan du Visa Kubernetes-instrumentpanelen för din Azure Stack Edge Pro-enhet i skrivskyddat läge.

    ![Kubernetes instrument panels huvud sida](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-main-page-1.png)

## <a name="view-module-status"></a>Visa status för modul

Compute-moduler är behållare som har en affärs logik implementerad. Du kan använda instrument panelen för att kontrol lera om en Compute-modul har distribuerats på Azure Stack Edge Pro-enhet.

Om du vill visa status för modulen följer du de här stegen på instrument panelen:

1. Gå till **namn område** i det vänstra fönstret på instrument panelen. Filtrera efter namn området där IoT Edge moduler visas, i det här fallet **iotedge**.
1. I den vänstra rutan går du till **arbets belastningar > distributioner**.
1. I den högra rutan visas alla moduler som har distribuerats på enheten. I det här fallet distribuerades en GettingStartedWithGPU-modul på Azure Stack Edge Pro. Du kan se att modulen har distribuerats.

    ![Visa modul distribution](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-module-deployment-1.png)

 
## <a name="get-ip-address-for-services-or-modules"></a>Hämta IP-adress för tjänster eller moduler

Du kan använda instrument panelen för att hämta IP-adresserna till tjänsterna eller modulerna som du vill exponera utanför Kubernetes-klustret. 

Du tilldelar IP-intervallet för de här externa tjänsterna via det lokala webb gränssnittet på enheten på sidan **Compute Network Settings** . När du har distribuerat IoT Edge moduler kanske du vill hämta IP-adressen som tilldelats till en speciell modul eller tjänst. 

Följ dessa steg på instrument panelen för att hämta IP-adressen:

1. Gå till **namn område** i det vänstra fönstret på instrument panelen. Filtrera efter det namn område där en extern tjänst distribueras, i det här fallet **iotedge**.
1. I den vänstra rutan går du till **identifiering och belastnings utjämning > tjänster**.
1. I den högra rutan visas alla tjänster som körs i `iotedge` namn området på din Azure Stack Edge Pro-enhet.

    ![Hämta IP för externa tjänster](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-get-ip-external-service-1.png)

## <a name="view-container-logs"></a>Visa containerloggar

Det finns instanser där du behöver Visa behållar loggarna. Du kan använda instrument panelen för att hämta loggar för en speciell behållare som du har distribuerat i ditt Kubernetes-kluster.

Följ de här stegen på instrument panelen för att Visa behållar loggarna:

1. Gå till **namn område** i det vänstra fönstret på instrument panelen. Filtrera efter det namn område där IoT Edge moduler distribueras, i det här fallet **iotedge**.
1. I den vänstra rutan går du till **arbets belastningar > poddar**.
1. I den högra rutan visas alla poddar som körs på enheten. Identifiera Pod som kör den modul som du vill visa loggarna för. Välj den lodräta ellipsen för den Pod som du har identifierat och välj **loggar** på snabb menyn.

    ![Visa behållar loggar 1](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)

1. Loggarna visas i ett logg visnings program som är inbyggt i instrument panelen. Du kan också hämta loggarna.

    ![Visa behållar loggar 2](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)
    

## <a name="view-cpu-memory-usage"></a>Visa CPU, minnes användning

Kubernetes-instrumentpanelen för Azure Stack Edge Pro-enhet har också ett [mått Server tillägg](https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/) som samlar in processor-och minnes användningen för Kubernetes-resurser.
 
Du kan till exempel Visa CPU och minne som används för distributioner i alla namn områden. 

![Visa CPU-och minnes användning för alla distributioner](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-all-1.png)

Du kan också filtrera efter en speciell namnrymd. I följande exempel kan du bara Visa processor-och minnes förbrukning för Azure Arc-distributioner.  

![Visa CPU-och minnes användning för Azure Arc-distributioner](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-azure-arc-1.png)

Kubernetes Metrics-servern tillhandahåller pipelines för automatisk skalning, till exempel [horisontell Pod autoskalning](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/).


## <a name="next-steps"></a>Nästa steg

Lär dig hur du [felsöker enhets problem](azure-stack-edge-gpu-troubleshoot.md).
