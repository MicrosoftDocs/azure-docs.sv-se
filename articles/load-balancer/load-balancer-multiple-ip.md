---
title: Belastnings utjämning på flera IP-konfigurationer – Azure Portal
titleSuffix: Azure Load Balancer
description: I den här artikeln lär du dig mer om belastnings utjämning för primära och sekundära IP-konfigurationer med hjälp av Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: se0dec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 3d28946aad263af635a0139e68d424a77a1eab25
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2021
ms.locfileid: "103417832"
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>Belastnings utjämning på flera IP-konfigurationer med hjälp av Azure Portal

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [CLI](load-balancer-multiple-ip-cli.md)

I den här artikeln ska vi visa hur du använder Azure Load Balancer med flera IP-adresser på en sekundär nätverks gränssnitts styrenhet (NIC). Följande diagram illustrerar vårt scenario:

![Lastbalanseringsscenario](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

I vårt scenario använder vi följande konfiguration:

- Två virtuella datorer (VM) som kör Windows.
- Varje virtuell dator har ett primärt och ett sekundärt nätverkskort.
- Varje sekundärt nätverkskort har två IP-konfigurationer.
- Varje virtuell dator är värd för två webbplatser: contoso.com och fabrikam.com.
- Varje webbplats är kopplad till en IP-konfiguration på det sekundära NÄTVERKSKORTet.
- Azure Load Balancer används för att exponera två frontend-IP-adresser, en för varje webbplats. Klient dels adresserna används för att distribuera trafik till respektive IP-konfiguration för varje webbplats.
- Samma port nummer används för både frontend-IP-adresser och backend-poolens IP-adresser.

## <a name="prerequisites"></a>Förutsättningar

Vårt scenario exempel förutsätter att du har en resurs grupp med namnet **contosofabrikam** som är konfigurerad på följande sätt:

- Resurs gruppen innehåller ett virtuellt nätverk med namnet **myVNet**.
- **MyVNet** -nätverket innehåller två virtuella datorer med namnet **VM1** och **VM2**.
- VM1 och VM2 finns i samma tillgänglighets uppsättning med namnet **myAvailset**. 
- VM1 och VM2 var och en har ett primärt nätverkskort med namnet **VM1NIC1** respektive **VM2NIC1**. 
- VM1 och VM2 har ett sekundärt nätverkskort med namnet **VM1NIC2** respektive **VM2NIC2**.

Mer information om hur du skapar virtuella datorer med flera nätverkskort finns i [skapa en virtuell dator med flera nätverkskort med hjälp av PowerShell](../virtual-machines/windows/multiple-nics.md).

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>Utför belastnings utjämning på flera IP-konfigurationer

Utför följande steg för att uppnå det scenario som beskrivs i den här artikeln.

### <a name="step-1-configure-the-secondary-nics"></a>Steg 1: Konfigurera sekundära nätverkskort

Lägg till IP-konfigurationen för det sekundära NÄTVERKSKORTet för varje virtuell dator i det virtuella nätverket:  

1. Bläddra till Azure Portal: https://portal.azure.com . Logga in med ditt Azure-konto.

2. I det övre vänstra hörnet på skärmen väljer du ikonen **resurs grupp** . Välj sedan den resurs grupp där de virtuella datorerna finns (till exempel **contosofabrikam**). I fönstret **resurs grupper** visas alla resurser och nätverkskort för de virtuella datorerna.

3. Lägg till IP-konfigurationen för det sekundära NÄTVERKSKORTet för varje virtuell dator:

    1. Välj det sekundära NÄTVERKSKORTet som du vill konfigurera.
    
    2. Välj **IP-konfigurationer**. I nästa ruta, längst upp, väljer du **Lägg till**.

    3. Lägg till en andra IP-konfiguration till NÄTVERKSKORTet under **Lägg till IP-konfigurationer**: 

        1. Ange ett namn för den sekundära IP-konfigurationen. (Till exempel för VM1 och VM2 namnger du IP-konfigurationen **VM1NIC2-ipconfig2** respektive **VM2NIC2-ipconfig2**.)

        2. För inställningen **privat IP-adress**, **allokering** väljer du **statisk**.

        3. Välj **OK**.

När den andra IP-konfigurationen för det sekundära NÄTVERKSKORTet har slutförts visas den under inställningarna för **IP-konfigurationer** för det nätverkskort som anges.

### <a name="step-2-create-the-load-balancer"></a>Steg 2: Skapa lastbalanseraren

Skapa belastningsutjämnaren för konfigurationen:

1. Bläddra till Azure Portal: https://portal.azure.com . Logga in med ditt Azure-konto.

2. I det övre vänstra hörnet på skärmen väljer du **skapa en resurs**  >  **nätverk**  >  **Load Balancer**. Välj sedan **skapa**.

3. Under **skapa belastningsutjämnare** anger du ett namn för belastningsutjämnaren. I det här scenariot använder vi namnet **mylb**.

4. Under **offentlig IP-adress** skapar du en ny offentlig IP-adress med namnet **PublicIP1**.

5. Under **resurs grupp** väljer du den befintliga resurs gruppen för dina virtuella datorer (till exempel **contosofabrikam**). Välj platsen där du vill distribuera belastningsutjämnaren till och välj sedan **OK**.

Belastningsutjämnaren börjar distribuera. Det kan ta några minuter innan distributionen har slutförts. När distributionen är klar visas belastningsutjämnaren som en resurs i resurs gruppen.

### <a name="step-3-configure-the-front-end-ip-pool"></a>Steg 3: Konfigurera klient delens IP-pool

För varje webbplats (contoso.com och fabrikam.com) konfigurerar du klient delens IP-pool på belastningsutjämnaren:

1. Välj **fler tjänster** i portalen. I rutan Filter anger du **offentlig IP-adress** och väljer sedan **offentliga IP-adresser**. I nästa ruta, längst upp, väljer du **Lägg till**.

2. Konfigurera två offentliga IP-adresser (**PublicIP1** och **PublicIP2**) för båda webbplatserna (contoso.com och fabrikam.com):

   1. Ange ett namn för din klient-IP-adress.

   2. För **resurs grupp** väljer du den befintliga resurs gruppen för dina virtuella datorer (till exempel **contosofabrikam**).

   3. För **plats** väljer du samma plats som de virtuella datorerna.

   4. Välj **OK**.

      När de offentliga IP-adresserna har skapats visas de under **offentliga IP-** adresser.

3. <a name="step3-3"></a>Välj **fler tjänster** i portalen. I rutan Filter skriver du **Load Balancer** och väljer sedan **Load Balancer**. 

4. Välj den belastningsutjämnare (**mylb**) som du vill lägga till klient DELENS IP-pool till.

5. Under **Inställningar** väljer du **IP-konfiguration för klient delen**. I nästa ruta, längst upp, väljer du **Lägg till**.

6. Ange ett namn på klient delens IP-adress (till exempel **contosofe** eller **fabrikamfe**).

7. <a name="step3-7"></a>Välj **IP-adress**. Under **Välj offentlig IP-adress** väljer du IP-adresserna för klient delen (**PublicIP1** eller **PublicIP2**).

8. Skapa den andra IP-adressen för klient sidan genom att upprepa <a href="#step3-3">steg 3</a> till <a href="#step3-7">steg 7</a> i det här avsnittet.

När klient delens pool har kon figurer ATS visas IP-adresserna under **konfigurations** inställningarna för belastningsutjämnarens klient del. 
    
### <a name="step-4-configure-the-back-end-pool"></a>Steg 4: Konfigurera backend-poolen

För varje webbplats (contoso.com och fabrikam.com) konfigurerar du backend-adresspoolen på belastningsutjämnaren:
        
1. Välj **fler tjänster** i portalen. I rutan Filter skriver du **Load Balancer** och väljer sedan **Load Balancer**.

2. Välj den belastningsutjämnare (**mylb**) som du vill lägga till backend-poolen till.

3. Under **Inställningar** väljer du **backend-pooler**. Ange ett namn för backend-poolen (till exempel **contosopool** eller **fabrikampool**). I nästa ruta, längst upp, väljer du **Lägg till**. 

4. För **kopplad till** väljer du **tillgänglighets uppsättning**.

5. För **tillgänglighets uppsättning** väljer du **myAvailset**.

6. Lägg till mål nätverkets IP-konfigurationer för båda virtuella datorerna: 

    ![Konfigurera Server dels pooler för belastningsutjämnare](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. För **virtuell mål dator** väljer du den virtuella dator som du vill lägga till i backend-poolen (till exempel **VM1** eller **VM2**).

    2. För **IP-konfiguration för nätverk** väljer du IP-konfigurationen för det sekundära nätverkskortet för den virtuella dator som du valde i föregående steg (till exempel **VM1NIC2-ipconfig2** eller **VM2NIC2-ipconfig2**).

7. Välj **OK**.

När backend-poolen har kon figurer ATS visas adresserna under inställningarna för belastningsutjämnarens **Server dels** inställningar.

### <a name="step-5-configure-the-health-probe"></a>Steg 5: Konfigurera hälso avsökningen

Konfigurera en hälso avsökning för belastningsutjämnaren:

1. Välj **fler tjänster** i portalen. I rutan Filter skriver du **Load Balancer** och väljer sedan **Load Balancer**.

2. Välj den belastningsutjämnare (**mylb**) som du vill lägga till hälso avsökningen i.

3. Under **Inställningar** väljer du **hälso avsökning**. I nästa ruta, längst upp, väljer du **Lägg till**. 

4. Ange ett namn på hälso avsökningen (till exempel **http**). Välj **OK**.

### <a name="step-6-configure-load-balancing-rules"></a>Steg 6: Konfigurera belastnings Utjämnings regler

Konfigurera belastnings Utjämnings reglerna för varje webbplats (contoso.com och fabrikam.com):
    
1. <a name="step6-1"></a>Under **Inställningar** väljer du **belastnings Utjämnings regler**. I nästa ruta, längst upp, väljer du **Lägg till**. 

2. I **namn** anger du ett namn för belastnings Utjämnings regeln (till exempel **HTTPc** för contoso.com eller **HTTPf** för fabrikam.com).

3. För **IP-adress för klient** del väljer du den IP-adress för klient delen som du skapade tidigare (till exempel **contosofe** eller **fabrikamfe**).

4. Behåll standardvärdet **80** för **port** -och **backend-porten**.

5. För **flytande IP (direkt Server retur)** väljer du **inaktive rad**.

6. <a name="step6-6"></a>Välj **OK**.

7. Skapa den andra belastnings Utjämnings regeln genom att upprepa <a href="#step6-1">steg 1</a> till <a href="#step6-6">steg 6</a> i det här avsnittet.

När reglerna har kon figurer ATS visas de under inställningarna för belastnings **Utjämnings regler** .

### <a name="step-7-configure-dns-records"></a>Steg 7: Konfigurera DNS-poster

Som det sista steget konfigurerar du dina DNS-resursposter så att de pekar på respektive frontend-IP-adress för belastningsutjämnaren. Du kan vara värd för dina domäner i Azure DNS. Mer information om hur du använder Azure DNS med Load Balancer finns i [använda Azure DNS med andra Azure-tjänster](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om hur du kombinerar tjänster för belastnings utjämning i Azure med [hjälp av belastnings Utjämnings tjänster i Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Lär dig hur du kan använda olika typer av loggar för att hantera och felsöka belastningsutjämnare i [Azure Monitor loggar för Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
