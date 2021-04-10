---
title: Självstudie för att förbereda Azure Portal, data Center miljö för att distribuera Azure Stack Edge Mini R-enhet | Microsoft Docs
description: Den första självstudien om att distribuera Azure Stack Edge Mini R-enhet förutsätter att du förbereder Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: alkohli
ms.openlocfilehash: 7042d3ede872c5dc854675334b964430671d1306
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065675"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-mini-r"></a>Självstudie: Förbered för att distribuera Azure Stack Edge Mini R

Den här själv studie kursen är den första i serien med distributions kurser som krävs för att distribuera en Azure Stack Edge Mini R-enhet fullständigt. I den här självstudien beskrivs hur du förbereder Azure Portal för att distribuera en Azure Stack Edge-resurs.

Du måste ha administratörsbehörighet för att utföra installationen och konfigurationen. Portalförberedelserna tar mindre än tio minuter.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en ny resurs
> * Hämta aktiveringsnyckeln

### <a name="get-started"></a>Kom igång

Om du vill distribuera Azure Stack Edge Mini R, se följande självstudier i den angivna ordningen.

| Steg | Beskrivning |
| --- | --- |
| **Tgärder** |De här stegen måste utföras i förberedelser inför den kommande distributionen. |
| **[Check lista för distributions konfiguration](#deployment-configuration-checklist)** |Använd den här checklistan för att samla in och registrera information före och under distributionen. |
| **[Distributions krav](#prerequisites)** |Dessa krav verifierar att miljön är klar för distribution. |
|  | |
|**Distributions självstudier** |De här självstudierna krävs för att distribuera din Azure Stack Edge-Mini R-enhet i produktion. |
|**[1. Förbered Azure Portal för enhet](azure-stack-edge-mini-r-deploy-prep.md)** |Skapa och konfigurera din Azure Stack Edge-resurs innan du installerar den fysiska enheten. |
|**[2. Installera enheten](azure-stack-edge-mini-r-deploy-install.md)**|Kontrol lera och kabelansluta din fysiska enhet.  |
|**[3. Anslut till enheten](azure-stack-edge-mini-r-deploy-connect.md)** |När enheten har installerats ansluter du till enhetens lokala webb gränssnitt.  |
|**[4. Konfigurera nätverks inställningar](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)** |Konfigurera nätverket inklusive inställningar för beräknings nätverk och webbproxy för enheten.   |
|**[5. Konfigurera enhets inställningar](azure-stack-edge-mini-r-deploy-set-up-device-update-time.md)** |Tilldela ett enhets namn och en DNS-domän, konfigurera uppdaterings Server och enhets tid. |
|**[6. Konfigurera säkerhets inställningar](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md)** |Konfigurera certifikat med hjälp av egna certifikat, konfigurera VPN och konfigurera kryptering vid vila för din enhet.   |
|**[7. Aktivera enheten](azure-stack-edge-mini-r-deploy-activate.md)** |Använd aktiverings nyckeln från tjänsten för att aktivera enheten. Enheten är redo att konfigurera SMB-eller NFS-resurser eller ansluta via REST. |
|**[8. Konfigurera Compute](azure-stack-edge-gpu-deploy-configure-compute.md)** |Konfigurera beräknings rollen på enheten. Ett Kubernetes-kluster skapas också. |

Nu kan du börja konfigurera Azure-portalen.

## <a name="deployment-configuration-checklist"></a>Checklista för distributionskonfiguration

Innan du distribuerar enheten måste du samla in information för att konfigurera program varan på din Azure Stack Edge-Mini R-enhet. Att förbereda en del av den här informationen i förväg bidrar till att effektivisera processen att distribuera enheten i din miljö. Använd den [Azure Stack Edge Mini R Deployment Configuration check lista](azure-stack-edge-mini-r-deploy-checklist.md) för att anteckna konfigurations informationen när du distribuerar enheten.

## <a name="prerequisites"></a>Förutsättningar

Följande är konfigurations kraven för din Azure Stack Edge-resurs, din Azure Stack Edge-enhet och data Center nätverket.

### <a name="for-the-azure-stack-edge-resource"></a>För Azure Stack Edge-resursen

[!INCLUDE [Azure Stack Edge resource prerequisites](../../includes/azure-stack-edge-gateway-resource-prerequisites.md)]

### <a name="for-the-azure-stack-edge-device"></a>För Azure Stack Edge-enheten

Innan du distribuerar en fysisk enhet kontrollerar du att:

- Du har granskat säkerhets informationen för den här enheten i [säkerhets rikt linjer för din Azure Stack Edge-enhet](azure-stack-edge-mini-r-safety.md).
[!INCLUDE [Azure Stack Edge device prerequisites](../../includes/azure-stack-edge-gateway-device-prerequisites.md)] 

### <a name="for-the-datacenter-network"></a>För datacenternätverket

Innan du börjar ska du kontrollera att:

- Nätverket i data centret konfigureras enligt nätverks kraven för din Azure Stack Edge-enhet. Mer information finns i [Azure Stack Edge Mini R system-krav](azure-stack-edge-mini-r-system-requirements.md).

- För normala drift villkor för Azure Stack Edge har du:

    - Minst 10 Mbit/s Ladda ned bandbredd för att se till att enheten förblir uppdaterad.
    - Minst 20 Mbit/s dedikerad överföring och nedladdning av bandbredd för överföring av filer.

## <a name="create-a-new-resource"></a>Skapa en ny resurs

Om du har en befintlig Azure Stack Edge-resurs för att hantera din fysiska enhet kan du hoppa över det här steget och gå till [Hämta aktiverings nyckeln](#get-the-activation-key).

### <a name="portal"></a>[Portal](#tab/azure-portal)

För att skapa en Azure Stack Edge-resurs, utför följande steg i Azure Portal.

1. Använd dina Microsoft Azure autentiseringsuppgifter för att logga in på Azure Portal på denna URL: [https://portal.azure.com](https://portal.azure.com) .


2. Välj **+ Skapa en resurs** i fönstret till vänster. Sök efter och välj **Azure Stack gräns/data Box Gateway**. Välj **Skapa**. 

3. Välj den prenumeration som du vill använda för Azure Stack Edge Pro-enheten. Välj det land där du vill skicka den här fysiska enheten. Välj **Visa enheter**.

    ![Skapa en resurs 1](media/azure-stack-edge-mini-r-deploy-prep/create-resource-1.png)


4. Välj enhets typ. Under **Azure Stack kant** väljer du **Azure Stack Edge Mini R** och väljer sedan **Välj**. Om du ser några problem eller inte kan välja enhets typ går du till [Felsöka beställnings problem](azure-stack-edge-troubleshoot-ordering.md).

    [![Skapa en resurs 2](media/azure-stack-edge-mini-r-deploy-prep/create-resource-2.png)](media/azure-stack-edge-mini-r-deploy-prep/create-resource-2.png#lightbox)


5. På fliken **grundläggande** anger eller väljer du följande **projekt information**.
    
    |Inställning  |Värde  |
    |---------|---------|
    |Prenumeration    |Prenumerationen fylls i automatiskt baserat på den tidigare markeringen. Prenumerationen är kopplad till ditt faktureringskonto. |
    |Resursgrupp  |Välj en befintlig grupp eller skapa en ny grupp.<br>Lär dig mer om [Azures resurs grupper](../azure-resource-manager/management/overview.md).     |


6. Ange eller Välj följande **instans information**.

    |Inställning  |Värde  |
    |---------|---------|
    |Namn   | Ett eget namn som identifierar resursen.<br>Namnet innehåller mellan 2 och 50 tecken, inklusive bokstäver, siffror och bindestreck.<br> Namnet börjar och slutar med en bokstav eller en siffra.        |
    |Region     |För en lista över alla regioner där Azure Stack Edge-resursen är tillgänglig, se [Azure-produkter tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Om du använder Azure Government är alla myndigheter tillgängliga som de visas i Azure- [regionerna](https://azure.microsoft.com/global-infrastructure/regions/).<br> Välj den plats som är närmast den geografiska region där du vill distribuera enheten.|

    ![Skapa en resurs 4](media/azure-stack-edge-mini-r-deploy-prep/create-resource-4.png)


7. Välj **Nästa: leverans adress**.

   - Om du redan har en enhet väljer du kombinations rutan där **jag redan har en enhet**.

     ![Skapa en resurs 5](media/azure-stack-edge-mini-r-deploy-prep/create-resource-5.png)

   - Om det här är den nya enhet som du beställer anger du kontakt namn, företag, adress att leverera enheten till och kontakt information.

     ![Skapa en resurs 6](media/azure-stack-edge-mini-r-deploy-prep/create-resource-6.png)

8. Välj **Nästa: Taggar**. Du kan också ange taggar för att kategorisera resurser och konsolidera fakturering. Välj **Nästa: Granska + skapa**.

9. På fliken **Granska + skapa** granskar du **pris informationen**, **användningsvillkor** och informationen för resursen. Välj kombinations rutan för **Jag har granskat sekretess villkoren**.

    ![Skapa en resurs 7](media/azure-stack-edge-mini-r-deploy-prep/create-resource-7.png)

    Du får även ett meddelande om att en Hanterad tjänstidentitet (MSI) är aktive rad för att kunna autentisera till moln tjänster när du skapar en resurs. Den här identiteten finns så länge resursen finns.

10. Välj **Skapa**.

    Det tar några minuter att skapa resursen. En MSI skapas också som låter Azure Stack Edge-enheten kommunicera med resurs leverantören i Azure.
    
    När resursen har skapats och distribuerats får du ett meddelande. Välj **Gå till resurs**.
    
    ![Gå till Azure Stack Edge Pro-resursen](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-1.png)
    
När ordern har placerats, granskar Microsoft ordern och når dig (via e-post) med leverans information.

> [!NOTE]
> Om du vill skapa flera beställningar samtidigt eller klona en befintlig order kan du använda [skripten i Azure-exempel](https://github.com/Azure-Samples/azure-stack-edge-order). Mer information finns i README-filen.

Om du stöter på problem under beställnings processen går du till [Felsöka beställnings problem](azure-stack-edge-troubleshoot-ordering.md).

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Förbered din miljö för Azure CLI om det behövs.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Om du vill skapa en Azure Stack Edge-resurs kör du följande kommandon i Azure CLI.

1. Skapa en resurs grupp med kommandot [AZ Group Create](/cli/azure/group#az_group_create) eller Använd en befintlig resurs grupp:

   ```azurecli
   az group create --name myasepgpu1 --location eastus
   ```

1. Om du vill skapa en enhet använder du kommandot [AZ databoxedge Device Create](/cli/azure/databoxedge/device#az_databoxedge_device_create) :

   ```azurecli
   az databoxedge device create --resource-group myasepgpu1 \
      --device-name myasegpu1 --location eastus --sku EdgeMR_Mini
   ```

   Välj den plats som är närmast den geografiska region där du vill distribuera enheten. Regionen lagrar bara metadata för enhets hantering. Faktiska data kan lagras i valfritt lagrings konto.

   För en lista över alla regioner där Azure Stack Edge-resursen är tillgänglig, se [Azure-produkter tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Om du använder Azure Government är alla myndigheter tillgängliga som de visas i Azure- [regionerna](https://azure.microsoft.com/global-infrastructure/regions/).

1. Om du vill skapa en order kör du kommandot [AZ databoxedge order Create](/cli/azure/databoxedge/order#az_databoxedge_order_create) :

   ```azurecli
   az databoxedge order create --resource-group myasepgpu1 \
      --device-name myasegpu1 --company-name "Contoso" \
      --address-line1 "1020 Enterprise Way" --city "Sunnyvale" \
      --state "California" --country "United States" --postal-code 94089 \
      --contact-person "Gus Poland" --email-list gus@contoso.com --phone 4085555555
   ```

Det tar några minuter att skapa resursen. Kör kommandot [AZ databoxedge order show](/cli/azure/databoxedge/order#az_databoxedge_order_show) för att se ordningen:

```azurecli
az databoxedge order show --resource-group myasepgpu1 --device-name myasegpu1 
```

När du har placerat en order, granskar Microsoft ordern och kontaktar dig via e-post med leverans information.

---

## <a name="get-the-activation-key"></a>Hämta aktiveringsnyckeln

När Azure Stack Edge-resursen är igång måste du hämta aktiverings nyckeln. Den här nyckeln används för att aktivera och ansluta din Azure Stack Edge Mini R-enhet med resursen. Du kan hämta den här nyckeln nu när du befinner dig på Azure-portalen.

1. Välj den resurs du skapade och välj **Översikt**.

   ![Välj enhets konfiguration](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-2.png)

2. Ange ett namn för Azure Key Vault på panelen **Aktivera** eller godkänn standard namnet. Namnet på nyckel valvet kan vara mellan 3 och 24 tecken. 

    Ett nyckel valv skapas för varje Azure Stack Edge-resurs som aktive ras med din enhet. Med nyckel valvet kan du lagra och komma åt hemligheter. Till exempel lagras kanal integritets nyckeln (CIK) för tjänsten i nyckel valvet.

    När du har angett ett nyckel valvs namn väljer du **generera aktiverings nyckel** för att skapa en aktiverings nyckel.

    [![Hämta aktiveringsnyckeln](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-3.png)](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-3.png#lightbox)

    Vänta några minuter medan nyckel valvet och aktiverings nyckeln skapas. Välj kopieringsikonen för att kopiera nyckeln och spara den för senare användning.

> [!IMPORTANT]
> - Aktiveringsnyckeln upphör att gälla tre dagar efter att den skapats.
> - Om nyckeln har upphört att gälla genererar du en ny nyckel. Den äldre nyckeln är inte giltig.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig mer om Azure Stack gräns ämnen som:

> [!div class="checklist"]
> * Skapa en ny resurs
> * Hämta aktiveringsnyckeln

Gå vidare till nästa självstudie och lär dig hur du installerar Azure Stack Edge.

> [!div class="nextstepaction"]
> [Installera Azure Stack Edge](./azure-stack-edge-mini-r-deploy-install.md)
