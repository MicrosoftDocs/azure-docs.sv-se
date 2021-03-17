---
title: Använd Azure Application Gateway för att skydda dina webbappar på Azure VMware-lösningen
description: Konfigurera Azure Application Gateway för att på ett säkert sätt exponera dina webbappar som körs på Azure VMware-lösningen.
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: f92027ee46fdaae275939acaea10e144b6bde101
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103601924"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>Använd Azure Application Gateway för att skydda dina webbappar på Azure VMware-lösningen

[Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) är en belastningsutjämnare för Layer 7-webbtrafik som låter dig hantera trafik till dina webb program. Det erbjuds både i Azure VMware Solution v 1.0 och v 2.0. Båda versionerna har testats med webbappar som körs på en Azure VMware-lösning.

Funktionerna omfattar: 
- Cookie-baserad session tillhörighet
- URL-baserad routning
- Brandvägg för webbaserade program (WAF)

En fullständig lista över funktioner finns i [Azure Application Gateway-funktioner](../application-gateway/features.md). 

Den här artikeln visar hur du använder Application Gateway framför en webb Server grupp för att skydda en webbapp som körs på Azure VMware-lösningen. 

## <a name="topology"></a>Topologi
Diagrammet visar hur Application Gateway används för att skydda virtuella Azure IaaS-datorer (VM: ar), skalnings uppsättningar för virtuella Azure-datorer eller lokala servrar. Application Gateway hanterar virtuella datorer i Azure VMware-lösningen som lokala servrar. 

![Diagram som visar hur Application Gateway skyddar virtuella Azure IaaS-datorer (VM: ar), skalnings uppsättningar för virtuella Azure-datorer eller lokala servrar.](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-protects.png)

> [!IMPORTANT]
> Azure Application Gateway är för närvarande den enda metod som stöds för att exponera webb program som körs på virtuella datorer med Azure VMware-lösningar.

Diagrammet visar test scenariot som används för att verifiera Application Gateway med webb program för Azure VMware-lösningar.

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="Diagram som visar test scenariot som används för att verifiera Application Gateway med webb program för Azure VMware-lösningar." border="false":::

Application Gateway-instansen distribueras på hubben i ett dedikerat undernät. Den har en offentlig Azure-IP-adress. Det rekommenderas att du aktiverar [Azure DDoS Protection standard](../ddos-protection/ddos-protection-overview.md) för det virtuella nätverket. Webb servern finns i ett privat moln i Azure VMware-lösningen bakom NSX t0 och T1-gatewayer. Azure VMware-lösningen använder [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) för att aktivera kommunikation med hubb och lokala system.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration.
- Ett privat moln för VMware-lösningar i Azure distribueras och körs.

## <a name="deployment-and-configuration"></a>Distribution och konfiguration

1. Sök efter **Application Gateway** i Azure Portal och välj **skapa Application Gateway**.

2. Ange grundläggande information som i följande bild. Välj sedan **Nästa: frontend->**. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/create-app-gateway.png" alt-text="Skärm bild som visar sidan Skapa Application Gateway i Azure Portal.":::

3. Välj IP-adress typen frontend. För offentlig väljer du en befintlig offentlig IP-adress eller skapar en ny. Välj **Nästa:>**.

    > [!NOTE]
    > Endast standard-och WAF-SKU: er (Web Application Firewall) stöds för privata klient versioner.

4. Lägg till en backend-pool för de virtuella datorer som körs på Azures infrastruktur för VMware-lösning. Ange information om webb servrar som körs i det privata molnet för Azure VMware-lösningen och välj **Lägg till**.  Välj sedan **Nästa: konfigurations>**.

5. På fliken **konfiguration** väljer du **Lägg till en regel för routning**.

6. Ange information om lyssnaren på fliken **lyssnare** . Om du väljer HTTPS måste du ange ett certifikat, antingen från en PFX-fil eller ett befintligt Azure Key Vault certifikat. 

7. Välj fliken **Server dels mål** och välj den backend-pool som du skapade tidigare. I fältet **http-inställningar** väljer du **Lägg till ny**.

8. Konfigurera parametrarna för HTTP-inställningarna. Välj **Lägg till**.

9. Om du vill konfigurera Sök vägsbaserade regler väljer **du Lägg till flera mål för att skapa en Sök vägs baserad regel**. 

10. Lägg till en Sök vägs-baserad regel och välj **Lägg till**. Upprepa om du vill lägga till fler Sök vägs regler. 

11. När du är färdig med att lägga till Sök vägs baserade regler väljer du **Lägg till** igen. Välj sedan **Nästa: taggar>**. 

12. Lägg till taggar och välj sedan **Nästa: granska + skapa>**.

13. En verifiering körs på din Application Gateway; om det lyckas väljer du **skapa** för att distribuera.

## <a name="configuration-examples"></a>Konfigurations exempel

Nu ska vi konfigurera Application Gateway med virtuella Azure VMware-lösningar som backend-pooler för följande användnings fall: 

- [Vara värd för flera platser](#hosting-multiple-sites)
- [Routning efter URL](#routing-by-url)

### <a name="hosting-multiple-sites"></a>Vara värd för flera platser

Den här proceduren visar hur du definierar backend-adresspooler med virtuella datorer som körs på ett privat moln i Azure VMware-lösningar på en befintlig Application Gateway. 

>[!NOTE]
>I den här proceduren förutsätter vi att du har flera domäner, så vi använder exempel på www.contoso.com och www.fabrikam.com.


1. Skapa två olika pooler för virtuella datorer i ditt privata moln. En representerar contoso och det andra Fabrikam. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool.png" alt-text="Skärm bild som visar en sammanfattning av en webb servers information i VSphere-klienten.":::

    Vi har använt Windows Server 2016 med rollen Internet Information Services (IIS) installerad. När de virtuella datorerna har installerats kör du följande PowerShell-kommandon för att konfigurera IIS på var och en av de virtuella datorerna. 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. I en befintlig Application Gateway-instans väljer du **Server dels grupper** på den vänstra menyn, väljer  **Lägg till** och anger den nya pooler. Välj **Lägg till** i den högra rutan.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-02.png" alt-text="Skärm bild av sidan för backend-pooler för att lägga till backend-pooler." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-02.png":::

3. I avsnittet **lyssnare** skapar du en ny lyssnare för varje webbplats. Ange informationen för varje lyssnare och välj **Lägg till**.

4. Välj **http-inställningar** till vänster och välj sedan **Lägg till** i den vänstra rutan. Fyll i informationen för att skapa en ny HTTP-inställning och välj **Spara**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-03.png" alt-text="Skärm bild av sidan med HTTP-inställningar för att skapa en ny HTTP-inställning." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-03.png":::

5. Skapa reglerna i avsnittet **Rules (regler** ) på den vänstra menyn. Koppla varje regel till motsvarande lyssnare. Välj **Lägg till**.

6. Konfigurera motsvarande backend-pool och HTTP-inställningar. Välj **Lägg till**.

7. Testa anslutningen. Öppna din önskade webbläsare och navigera till de olika webbplatser som finns i din Azure VMware-lösnings miljö, till exempel http://www.fabrikam.com .

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-07.png" alt-text="Skärm bild av webb sidan som visar lyckad test av anslutningen.":::

### <a name="routing-by-url"></a>Routning efter URL

Följande steg definierar Server dels adressernas pooler med virtuella datorer som körs i ett privat moln i Azure VMware-lösningen. Det privata molnet är på en befintlig Application Gateway. Du skapar sedan routningsregler som kontrollerar att webb trafiken kommer till rätt servrar i poolerna.

1. Skapa en pool för virtuella datorer som representerar webb server gruppen i ditt privata moln. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool.png" alt-text="Skärm bild av sidan i VMSphere-klienten som visar en sammanfattning av en annan virtuell dator.":::

    Windows Server 2016 med IIS-rollen installerad har använts för att illustrera den här självstudien. När de virtuella datorerna har installerats kör du följande PowerShell-kommandon för att konfigurera IIS för varje vägledning för virtuella datorer. 

    Den första virtuella datorn, contoso-Web-01, kommer att vara värd för huvud webbplatsen.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

    Den andra virtuella datorn, contoso-Web-02, kommer att vara värd för avbildnings webbplatsen.
 
    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "images" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\images\test.htm -Value $($env:computername)
    ```

    Den tredje virtuella datorn, contoso-Web-03, kommer att vara värd för video webbplatsen.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "video" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\video\test.htm -Value $($env:computername)
    ```

2. Lägg till tre nya backend-pooler i en befintlig Application Gateway-instans. 

   1. Välj **Serverdelspooler** på den vänstra menyn. 
   1. Välj **Lägg till** och ange information om den första poolen, **contoso-Web**. 
   1. Lägg till en virtuell dator som mål. 
   1. Välj **Lägg till**. 
   1. Upprepa den här processen för **contoso-images** och **contoso-video** och Lägg till en unik virtuell dator som mål. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-02.png" alt-text="Skärm bild av sidan Server dels pooler med tillägg av tre nya backend-pooler." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-02.png":::

3. I avsnittet **lyssnare** skapar du en ny lyssnare av typen Basic med Port 8080.

4. I det vänstra navigerings fönstret väljer du **http-inställningar** och väljer **Lägg till** i den vänstra rutan. Fyll i informationen för att skapa en ny HTTP-inställning och välj **Spara**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-04.png" alt-text="Skärm bild av sidan Lägg till HTTP-inställning med konfiguration av HTTP-inställningar.":::

5. Skapa reglerna i avsnittet **Rules (regler** ) på den vänstra menyn. Koppla varje regel till den tidigare skapade lyssnaren. Konfigurera sedan huvud server delen och HTTP-inställningarna. Välj **Lägg till**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-07.png" alt-text="Skärm bild av sidan Lägg till en regel för routning för att konfigurera routningsregler till ett Server dels mål.":::

6. Testa konfigurationen. Öppna programgatewayen på Azure Portal och kopiera den offentliga IP-adressen i **översikts** avsnittet. 

   1. Öppna ett nytt webbläsarfönster och ange URL: en `http://<app-gw-ip-address>:8080` . 

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-08.png" alt-text="Skärm bild av webb sidan som visar lyckade tester av konfigurationen.":::

   1. Ändra URL:en till `http://<app-gw-ip-address>:8080/images/test.htm`.

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-09.png" alt-text="Skärm bild av ett annat lyckat test med den nya URL: en.":::

   1. Ändra URL: en igen till `http://<app-gw-ip-address>:8080/video/test.htm` .

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-10.png" alt-text="Skärm bild av lyckad test med den sista URL: en.":::

## <a name="next-steps"></a>Nästa steg

Nu när du har använt Application Gateway för att skydda en webbapp som körs på Azure VMware-lösningen kanske du vill lära dig mer om:

- [Konfigurera Azure Application Gateway för olika scenarier](../application-gateway/configuration-overview.md).
- [Distribuera Traffic Manager för att balansera arbets belastningar för Azure VMware-lösningar](deploy-traffic-manager-balance-workloads.md).
- [Integrera Azure NetApp Files med Azure VMware Solution-baserade arbets belastningar](netapp-files-with-azure-vmware-solution.md).
- [Skydda Azure-resurser i virtuella nätverk](../ddos-protection/ddos-protection-overview.md).
