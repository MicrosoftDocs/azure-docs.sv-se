---
title: Självstudie för att förbereda Azure Portal, data Center miljö för att distribuera Azure Stack Edge Pro | Microsoft Docs
description: Den första självstudien om hur du distribuerar Azure Stack Edge Pro innebär att du förbereder Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/16/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 9ceba84cb3bbe52dc5ba51d0f4945f5bad0a5034
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103574004"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro"></a>Självstudie: Förbered för att distribuera Azure Stack Edge Pro  

Det här är den första självstudien i serien med distributions kurser som krävs för att distribuera Azure Stack Edge Pro fullständigt. I den här självstudien beskrivs hur du förbereder Azure Portal för att distribuera en Azure Stack Edge-resurs. 

Du måste ha administratörsbehörighet för att utföra installationen och konfigurationen. Portalförberedelserna tar mindre än tio minuter.  

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Skapa en ny resurs
> * Hämta aktiveringsnyckeln

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="get-started"></a>Kom igång

För att distribuera Azure Stack Edge Pro, se följande självstudier i den angivna ordningen.

| **#** | **I det här steget** | **Använd de här dokumenten** |
| --- | --- | --- | 
| 1. |**[Förbered Azure Portal för Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md)** |Skapa och konfigurera din Azure Stack Edge-resurs innan du installerar en fysisk enhet i Azure Stack Box Edge. |
| 2. |**[Installera Azure Stack Edge Pro](azure-stack-edge-deploy-install.md)**|Packa upp, racka och kablar Azure Stack fysiska enheten för Edge Pro.  |
| 3. |**[Anslut, konfigurera och aktivera Azure Stack Edge Pro](azure-stack-edge-deploy-connect-setup-activate.md)** |Anslut till det lokala webbgränssnittet, slutför installationen av enheten och aktivera enheten. Enheten är redo att konfigurera SMB- eller NFS-resurser.  |
| 4. |**[Överföra data med Azure Stack Edge Pro](azure-stack-edge-deploy-add-shares.md)** |Lägg till resurser och anslut till resurser via SMB eller NFS. |
| 5. |**[Transformera data med Azure Stack Edge Pro](azure-stack-edge-deploy-configure-compute.md)** |Konfigurera Compute-moduler på enheten för att transformera data när de flyttas till Azure. |

Nu kan du börja konfigurera Azure-portalen.

## <a name="prerequisites"></a>Förutsättningar

Följande är konfigurations kraven för din Azure Stack Edge-resurs, din Azure Stack Edge-enhet och data Center nätverket.

### <a name="for-the-azure-stack-edge-resource"></a>För Azure Stack Edge-resursen

Innan du börjar ska du kontrollera att:

* Din Microsoft Azure-prenumeration är aktiverad för en Azure Stack Edge-resurs. Se till att du har använt en prenumeration som stöds, till exempel [Microsoft Enterprise-avtal (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](/partner-center/azure-plan-lp)eller [Microsoft Azure-sponsring](https://azure.microsoft.com/offers/ms-azr-0036p/). Prenumerationer enligt principen betala per användning stöds inte.

* Du har ägar-eller deltagar åtkomst på resurs grupps nivå för Azure Stack Edge/Data Box Gateway, IoT Hub och Azure Storage resurser.

  * Du bör vara **ägare** på prenumerations nivå för att bevilja deltagar åtkomst. Om du vill ge deltagare åtkomst till någon annan går du till **alla tjänster** för  >  **prenumerations**  >  **åtkomst kontroll (IAM)** i Azure Portal och lägger till  >    >  **roll tilldelning**. Mer information finns i [Självstudier: ge en användare åtkomst till Azure-resurser med hjälp av Azure Portal](../role-based-access-control/quickstart-assign-role-user-portal.md).

  * Om du vill skapa en Azure Stack gräns-/Data Box Gateway-resurs, bör du ha behörighet som deltagare (eller högre) som är begränsade till resurs grupps nivå. Du måste också kontrol lera att `Microsoft.DataBoxEdge` resurs leverantören är registrerad. Information om hur du registrerar en resurs leverantör finns i [Registrera resurs leverantör](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Om du vill skapa en IoT Hub resurs måste du kontrol lera att Microsoft. providers-providern är registrerad. Information om hur du registrerar den finns i [Registrera resursprovider](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Om du vill skapa en lagrings konto resurs måste du igen med deltagar-eller högre åtkomst omfång på resurs grupps nivå. Azure Storage är som standard en registrerad resurs leverantör.
* Du har administratörs-eller användar åtkomst till Azure Active Directory Graph API. Mer information finns i [Azure Active Directory Graph API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
* Du har ditt Microsoft Azure lagringskonto med autentiseringsuppgifter.
* Du blockeras inte av någon Azure-princip som kon figurer ATS av system administratören. Mer information om principer finns i [snabb start: skapa en princip tilldelning för att identifiera icke-kompatibla resurser](../governance/policy/assign-policy-portal.md).

### <a name="for-the-azure-stack-edge-pro-device"></a>För Azure Stack Edge Pro-enhet

Innan du distribuerar en fysisk enhet kontrollerar du att:

* Du har granskat säkerhets informationen som ingick i försändelse paketet.
* Du har en 1U-plats som är tillgänglig i ett standardiserat 19-tums rack i ditt data Center för rack montering av enheten.
* Du har tillgång till en plan, stabil och jämn arbetsyta där enheten kan stå på ett säkert sätt.
* Platsen där du tänker konfigurera enheten har standardnätström från en oberoende källa eller en strömfördelare på racket (PDU) med en avbrottsfri kraftfälla (UPS).
* Du har tillgång till en fysisk enhet.

### <a name="for-the-datacenter-network"></a>För datacenternätverket

Innan du börjar ska du kontrollera att:

* Nätverket i data centret konfigureras enligt nätverks kraven för din Azure Stack Edge Pro-enhet. Mer information finns i [Azure Stack Edge Pro system-krav](azure-stack-edge-system-requirements.md).

* För normala drift villkor för Azure Stack Edge Pro har du:

  * Minst 10 Mbit/s Ladda ned bandbredd för att se till att enheten förblir uppdaterad.
  * Minst 20 Mbit/s dedikerad uppladdning och nedladdnings bandbredd för överföring av filer.

## <a name="create-new-resource-for-existing-device"></a>Skapa en ny resurs för en befintlig enhet

Om du är en befintlig Azure Stack Edge Pro-kund använder du följande procedur för att skapa en ny resurs om du behöver ersätta eller återställa den befintliga enheten.

Om du är en ny kund rekommenderar vi att du utforskar med Azure Stack Edge Pro-GPU-enheter för dina arbets belastningar. Mer information finns i [Azure Stack Edge Pro med GPU](azure-stack-edge-gpu-overview.md). Information om hur du beställer en Azure Stack Edge Pro med GPU-enhet finns i [skapa en ny resurs för Azure Stack Edge Pro-GPU](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource).

Om du vill skapa en ny Azure Stack Edge Pro-resurs för en befintlig enhet utför du följande steg i Azure Portal.

1. Använd dina Microsoft Azure autentiseringsuppgifter för att logga in på:

    - Azure Portal på denna URL: [https://portal.azure.com](https://portal.azure.com) .
    - Eller, Azure Government portalen på denna URL: [https://portal.azure.us](https://portal.azure.us) . Mer information finns i [ansluta till Azure Government med hjälp av portalen](../azure-government/documentation-government-get-started-connect-with-portal.md).

1. Välj **+ Skapa en resurs**. Sök efter och välj **Azure Stack Edge**. Välj sedan **Skapa**.

1. Välj prenumerationen för Azure Stack Edge Pro-enhet och land för att skicka enheten till i **leverera till**.

   ![Välj prenumeration och leverans land för din enhet](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-01.png)


1. I listan över enhets typer som visas väljer du **Azure Stack Edge Pro-FPGA**. Välj sedan **Välj**. 

   Enhets typen **Azure Stack Edge Pro-FPGA** visas bara om du har en befintlig enhet. Om du behöver beställa en ny enhet går du till [skapa en ny resurs för Azure Stack Edge Pro-GPU](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource).

   ![Sök Azure Stack Edge-tjänst](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-02.png)

1. På fliken **Grunder**:

   1. Ange eller Välj följande **projekt information**.
    
       |Inställning  |Värde  |
       |---------|---------|
       |Prenumeration    |Värdet fylls i automatiskt baserat på den tidigare markeringen. Prenumerationen är kopplad till ditt faktureringskonto. |
       |Resursgrupp  |Välj en befintlig grupp eller skapa en ny grupp.<br>Lär dig mer om [Azures resurs grupper](../azure-resource-manager/management/overview.md).     |

   1. Ange eller Välj följande **instans information**.

       |Inställning  |Värde  |
       |---------|---------|
       |Namn   | Ett eget namn som identifierar resursen.<br>Namnet innehåller mellan 2 och 50 tecken, inklusive bokstäver, siffror och bindestreck.<br> Namnet börjar och slutar med en bokstav eller en siffra.        |
       |Region     |För en lista över alla regioner där Azure Stack Edge-resursen är tillgänglig, se [Azure-produkter tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Om du använder Azure Government är alla myndigheter tillgängliga som de visas i Azure- [regionerna](https://azure.microsoft.com/global-infrastructure/regions/).<br> Välj den plats som är närmast den geografiska region där du vill distribuera enheten.|

   1. Välj **Granska + skapa**.

    ![Projekt-och instans information](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-03.png)

1. På fliken **Granska + skapa** granskar du **användningsvillkor**, **pris information** och information om resursen. Välj sedan **Skapa**.

    ![Granska Azure Stack gräns resurs information och sekretess villkor](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-04.png)

1. Det tar några minuter att skapa resursen. När resursen har skapats och distribuerats får du ett meddelande. Välj **Gå till resurs**.

   ![Gå till Azure Stack Edge-resursen](media/azure-stack-edge-deploy-prep/data-box-edge-resource-01.png)

När ordern har placerats, granskar Microsoft order och kontakter som du (via e-post) med leverans information.

![Meddelande om granskning av Azure Stack Edge Pro-beställning](media/azure-stack-edge-deploy-prep/data-box-edge-resource-02.png)


## <a name="get-the-activation-key"></a>Hämta aktiveringsnyckeln

När Azure Stack Edge-resursen är igång måste du hämta aktiverings nyckeln. Den här nyckeln används för att aktivera och ansluta din Azure Stack Edge Pro-enhet med resursen. Du kan hämta den här nyckeln nu när du befinner dig på Azure-portalen.

1. Gå till den resurs som du har skapat och välj **Översikt**. Du får ett meddelande om vilken inverkan din beställning bearbetas.

    ![Välj översikt](media/azure-stack-edge-deploy-prep/data-box-edge-select-device-setup.png)

2. När beställningen har bearbetats och enheten är på väg, uppdateras **översikten** . Godkänn standardvärdet för Azure Key Vault eller ange ett nytt **namn** . Välj **generera aktiverings nyckel**. Välj kopieringsikonen för att kopiera nyckeln och spara den för senare användning.

    ![Hämta aktiveringsnyckeln](media/azure-stack-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
>
> * Aktiveringsnyckeln upphör att gälla tre dagar efter att den skapats.
> * Om nyckeln har upphört att gälla genererar du en ny nyckel. Den äldre nyckeln är inte giltig.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig om Azure Stack Edge Pro-ämnen som:

> [!div class="checklist"]
>
> * Skapa en ny resurs
> * Hämta aktiveringsnyckeln

Gå vidare till nästa självstudie och lär dig hur du installerar Azure Stack Edge Pro.

> [!div class="nextstepaction"]
> [Installera Azure Stack Edge Pro](./azure-stack-edge-deploy-install.md)