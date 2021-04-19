---
title: 'Självstudie: Skapa en lastbalanserare med fler än en tillgänglighetsuppsättning i backend-poolen – Azure Portal'
titleSuffix: Azure Load Balancer
description: I den här självstudien distribuerar Azure Load Balancer med fler än en tillgänglighetsuppsättning i backend-poolen.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 04/16/2021
ms.custom: template-tutorial
ms.openlocfilehash: 21ff43217a7b2bd874a384f3b07a48d5223a1be2
ms.sourcegitcommit: 089c2bd1ac4861f43c4b89396d3d056a6eef4913
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107602358"
---
# <a name="tutorial-create-a-load-balancer-with-more-than-one-availability-set-in-the-backend-pool-using-the-azure-portal"></a>Självstudie: Skapa en lastbalanserare med fler än en tillgänglighetsuppsättning i backend-poolen med hjälp av Azure Portal

Som en del av en distribution med hög tillgänglighet grupperas virtuella datorer ofta i flera tillgänglighetsuppsättningar. 

Load Balancer har stöd för fler än en tillgänglighetsuppsättning med virtuella datorer i serverpoolen.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk
> * Skapa en NAT-gateway för utgående anslutning
> * Skapa en standard-SKU-Azure Load Balancer
> * Skapa fyra virtuella datorer och två tillgänglighetsuppsättningar
> * Lägga till virtuella datorer i tillgänglighetsuppsättningar i serverpoolen för lastbalanserare
> * Testa lastbalanseraren

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

I det här avsnittet skapar du ett virtuellt nätverk för lastbalanseraren och de andra resurserna som används i självstudien.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. I sökrutan högst upp i portalen anger du **Virtuellt nätverk**.

3. I sökresultaten väljer du **Virtuella nätverk**.

4. Välj **+ Skapa.**

5. På **fliken Grundläggande inställningar** i Skapa **virtuellt nätverk** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ------|
    | **Projektinformation** |   |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **Skapa ny**. </br> Ange **FörlBmultiAVS-rg** i **Namn.** |
    | **Instansinformation** |   |
    | Name | Ange **myVNet**. |
    | Region | Välj **USA, västra 2**. |

6. Välj fliken **IP-adresser** eller **knappen Nästa: IP-adresser** längst ned på sidan.

7. På fliken **IP-adresser** under **Undernätsnamn väljer** du **standard**.

8. I fönstret **Redigera undernät** under **Undernätsnamn anger** du **myBackendSubnet**.

9. Välj **Spara**.

10. Välj **fliken** Säkerhet eller **knappen Nästa:** Säkerhet längst ned på sidan.

11. På fliken **Säkerhet** går du till **BastionHost och** väljer **Aktivera**.

12. Ange eller välj följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Skyddsnamn | Ange **MyBastionHost.** |
    | Adressutrymme för AzureBastionSubnet | Ange **10.1.1.0/27**. |
    | Offentlig IP-adress | Välj **Skapa ny**. </br> Ange **myBastionIP** i **Namn**. |

13. Välj fliken **Granska +** skapa eller den blå knappen **Granska +** skapa längst ned på sidan.

14. Välj **Skapa**.

## <a name="create-nat-gateway"></a>Skapa NAT-gateway 

I det här avsnittet skapar du en NAT-gateway för utgående anslutning av de virtuella datorerna.

1. I sökrutan högst upp i portalen anger du **NAT-gateway .**

2. Välj **NAT-gatewayer** i sökresultatet.

3. Välj **+ Skapa.**

4. På fliken **Grundläggande inställningar** i **Skapa NAT-gateway (Network Address Translation)** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** |   |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **AlternativetLBmultiAVS-rg**. |
    | **Instansinformation** |   |
    | NAT-gatewaynamn | Ange **myNATgateway**. |
    | Region | Välj **USA, västra 2**. |
    | Tillgänglighetszon | Välj **Ingen**. |
    | Tidsgräns för inaktivitet (minuter) | Ange **15**. |

5. Välj fliken **Utgående IP** eller välj knappen **Nästa: Utgående IP** längst ned på sidan.

6. Välj **Skapa en ny offentlig IP-adress** bredvid Offentliga **IP-adresser** på **fliken Utgående IP.**

7. Ange **myPublicIP-nat** i **Namn.**

8. Välj **OK**.

9. Välj **fliken Undernät** eller välj **knappen Nästa: Undernät** längst ned på sidan.

10. Välj **myVNet** i den nedströmsmenyn under **Virtuellt nätverk**.

11. Markera kryssrutan bredvid **myBackendSubnet**.

12. Välj fliken **Granska +** skapa eller välj den blå knappen **Granska +** skapa längst ned på sidan.

13. Välj **Skapa**.

## <a name="create-load-balancer"></a>Skapa en lastbalanserare

I det här avsnittet skapar du en lastbalanserare för de virtuella datorerna.

1. I sökrutan högst upp i portalen anger du **Lastbalanserare.**

2. Välj **Lastbalanserare** i sökresultatet.

3. Välj **+ Skapa.**

4. På fliken **Grundläggande inställningar** i **Skapa lastbalanserare** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** |   |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **AlternativetLBmultiAVS-rg**. |
    | **Instansinformation** |   |
    | Name | Ange **myLoadBalancer**. |
    | Region | Välj **USA, västra 2**. |
    | Typ | Lämna standardvärdet **Offentlig.** |
    | SKU | Låt standardvärdet **Standard vara kvar.** |
    | Nivå | Lämna kvar standardvärdet **Regional**. |
    | **Offentlig IP-adress** |   |
    | Offentlig IP-adress | Låt standardvärdet **Skapa ny vara kvar.** |
    | Namn på offentlig IP-adress | Ange **myPublicIP-lb**. |
    | Tillgänglighetszon | Välj **Zonredundant**. |
    | Lägg till en offentlig IPv6-adress | Lämna standardvärdet **Nej.** |
    | Routningsprioritet | Låt Standardvärdet för **Microsoft-nätverket vara** kvar. |

5. Välj fliken **Granska +** skapa eller välj den blå knappen **Granska +** skapa längst ned på sidan.

6. Välj **Skapa**.

### <a name="configure-load-balancer-settings"></a>Konfigurera inställningar för lastbalanserare

I det här avsnittet skapar du en backend-pool för **myLoadBalancer**.

Du skapar en hälsoavsökning för att övervaka **HTTP** och **port 80.** Hälsoavsökningen övervakar hälsotillståndet för de virtuella datorerna i serverpoolen. 

Du skapar en belastningsutjämningsregel för **port 80 med** utgående SNAT inaktiverat. NAT-gatewayen som du skapade tidigare hanterar de virtuella datorernas utgående anslutning.

1. I sökrutan högst upp i portalen anger du **Lastbalanserare**.

2. Välj **Lastbalanserare** i sökresultatet.

3. Välj **myLoadBalancer**.

4. I **myLoadBalancer** väljer du **Backend-pooler** i **Inställningar**.

5. Välj **+ Lägg till** i **Backend-pooler**.

6. I **Lägg till backend-pool** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange **myBackendPool**. |
    | Virtuellt nätverk | Välj **myVNet.** |
    | Konfiguration av serverpool | Låt standardvärdet **NIC vara** kvar. |
    | IP-version | Låt standardvärdet **IPv4 vara** kvar. |

7. Välj **Lägg till**.

8. Välj **Hälsoavsökningar**.

9. Välj **+ Lägg till**.

10. I **Lägg till hälsoavsökning** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange **myHTTPProbe**. |
    | Protokoll | Välj **HTTP**. |
    | Port | Lämna standardvärdet **80**. |
    | Sökväg | Låt standardvärdet **/** vara . |
    | Intervall | Låt standardvärdet **vara 5** sekunder. |
    | Tröskelvärde för ej felfri | Lämna standardvärdet **2 på** varandra följande fel. |

11. Välj **Lägg till**.

12. Välj **Belastningsutjämningsregler.** 

13. Välj **+ Lägg till**.

14. Ange eller välj följande information i Lägg till **belastningsutjämningsregel:**

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange **myHTTPRule.** |
    | IP-version | Lämna standardvärdet **IPv4**. |
    | IP-adress för frontend | Välj **LoadBalancerFrontEnd**. |
    | Protokoll | Välj **tcp-standardvärdet**. |
    | Port | Ange **80**. |
    | Serverdelsport | Ange **80**. |
    | Serverdelspool | Välj **myBackendPool.** |
    | Hälsoavsökning | Välj **myHTTPProbe.** |
    | Sessionspermanens | Låt standardvärdet **Ingen vara kvar.** |
    | Tidsgräns för inaktivitet (minuter) | Ändra skjutreglaget till **15**. |
    | TCP-återställning | Välj **Aktiverad**. |
    | Flytande IP | Låt standardvärdet **Inaktiverad vara kvar.** |
    | Utgående källnätverksadressöversättning (SNAT) | Lämna standardvärdet **(Rekommenderas) Använd utgående regler för att ge medlemmar i backend-poolen åtkomst till Internet.** |

5. Välj **Lägg till**.

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

I det här avsnittet skapar du två tillgänglighetsgrupper med två virtuella datorer per grupp. Dessa datorer läggs till i lastbalanseringspoolens backend-pool när de skapas. 

### <a name="create-first-set-of-vms"></a>Skapa den första uppsättningen virtuella datorer

1. Välj **+ Skapa en** resurs i det övre vänstra avsnittet i portalen.

2. I **Ny** väljer du **Virtuell**  >  **beräkningsdator.**

3. På fliken **Grundläggande inställningar** i Skapa en **virtuell dator** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** |   |
    | Prenumeration | Välj din prenumeration |
    | Resursgrupp | Välj **AlternativetLBmultiAVS-rg**. |
    | **Instansinformation** |   |
    | Namn på virtuell dator | Ange **myVM1**. |
    | Region | Välj **USA, västra 2**. |
    | Alternativ för tillgänglighet | Välj **Tillgänglighetsuppsättning.** |
    | Tillgänglighetsuppsättning | Välj **Skapa ny**. </br> Ange **myAvailabilitySet1** i **Namn**. </br> Välj **OK**. |
    | Bild | Välj **Windows Server 2019 Datacenter – Gen1.** |
    | Azure Spot-instans | Lämna standardvärdet avmarkerat. |
    | Storlek | Välj en storlek för den virtuella datorn. |
    | **Administratörskonto** |   |
    | Användarnamn | Ange ett användarnamn. |
    | Lösenord | Ange ett lösenord. |
    | **Regler för inkommande portar** |   |
    | Offentliga inkommande portar | Välj **Ingen**. |

4. Välj **fliken Nätverk** eller välj **knappen Nästa: Diskar** och sedan **Nästa:** Nätverk längst ned på sidan.

5. På **fliken Nätverk** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **Nätverksgränssnitt** |   |
    | Virtuellt nätverk | Välj **myVNet**. |
    | Undernät | Välj **myBackendSubnet**. |
    | Offentlig IP-adress | Välj **Ingen**. |
    | Nätverkssäkerhetsgrupp för nätverkskort | Välj **Avancerat**. |
    | Konfigurera nätverkssäkerhetsgrupp | Välj **Skapa ny**. </br> I **Namn** anger du **myNSG**. </br> Välj **+Lägg till en regel för inkommande** **trafik i Regler för inkommande trafik.** </br> Välj **HTTP** för **Tjänst**. </br> Ange **myHTTPrule** som **namn.** </br> Välj **Lägg till**. </br> Välj **OK**. | 
    | **Belastningsutjämning** |   |
    | Placera den här virtuella datorn bakom en befintlig belastningsutjämningslösning? | Markera kryssrutan. |
    | **Inställningar för belastningsutjämning** |   |
    | Alternativ för belastningsutjämning | Välj **Azure-lastbalanserare.** |
    | Välj en lastbalanserare | Välj **myLoadBalancer**. |
    | Välj en backend-pool | Välj **myBackendPool**. |

6. Välj fliken **Granska +** skapa eller välj den blå knappen **Granska +** skapa längst ned på sidan.

7. Välj **Skapa**.

8. Upprepa steg 1 till sju för att skapa den andra virtuella datorn för uppsättningen. Ersätt inställningarna för den virtuella datorn med följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange **myVM2**. |
    | Tillgänglighetsuppsättning | Välj **myAvailabilitySet1.** |
    | Virtual Network | Välj **myVNet.** |
    | Undernät | Välj **myBackendSubnet**. |
    | Offentlig IP-adress | Välj **Ingen**. |
    | Nätverkssäkerhetsgrupp | Välj **myNSG.** |
    | Alternativ för belastningsutjämning | Välj **Azure Load Balancer**. |
    | Välj en lastbalanserare | Välj **myLoadBalancer**. |
    | Välj en backend-pool | Välj **myBackendPool**. |

### <a name="create-second-set-of-vms"></a>Skapa en andra uppsättning virtuella datorer

1. Välj **+ Skapa en** resurs i det övre vänstra avsnittet i portalen.

2. I **Ny** väljer du **Virtuell**  >  **beräkningsdator.**

3. På fliken **Grundläggande inställningar** i Skapa en **virtuell dator** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** |   |
    | Prenumeration | Välj din prenumeration |
    | Resursgrupp | Välj **AlternativetLBmultiAVS-rg**. |
    | **Instansinformation** |   |
    | Namn på virtuell dator | Ange **myVM3**. |
    | Region | Välj **USA, västra 2**. |
    | Alternativ för tillgänglighet | Välj **Tillgänglighetsuppsättning.** |
    | Tillgänglighetsuppsättning | Välj **Skapa ny**. </br> Ange **myAvailabilitySet2** i **Namn**. </br> Välj **OK**. |
    | Bild | Välj **Windows Server 2019 Datacenter – Gen1.** |
    | Azure Spot-instans | Lämna standardvärdet avmarkerat. |
    | Storlek | Välj en storlek för den virtuella datorn. |
    | **Administratörskonto** |   |
    | Användarnamn | Ange ett användarnamn. |
    | Lösenord | Ange ett lösenord. |
    | **Regler för inkommande portar** |   |
    | Offentliga inkommande portar | Välj **Ingen**. |

4. Välj **fliken Nätverk** eller välj **knappen Nästa: Diskar** och sedan **Nästa:** Nätverk längst ned på sidan.

5. På **fliken Nätverk** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **Nätverksgränssnitt** |   |
    | Virtuellt nätverk | Välj **myVNet**. |
    | Undernät | Välj **myBackendSubnet**. |
    | Offentlig IP-adress | Välj **Ingen**. |
    | Nätverkssäkerhetsgrupp för nätverkskort | Välj **Avancerat**. |
    | Konfigurera nätverkssäkerhetsgrupp | Välj **myNSG.** | 
    | **Belastningsutjämning** |   |
    | Placera den här virtuella datorn bakom en befintlig belastningsutjämningslösning? | Markera kryssrutan. |
    | **Inställningar för belastningsutjämning** |   |
    | Alternativ för belastningsutjämning | Välj **Azure Load Balancer**. |
    | Välj en lastbalanserare | Välj **myLoadBalancer**. |
    | Välj en backend-pool | Välj **myBackendPool**. |

6. Välj fliken **Granska +** skapa eller välj den blå knappen **Granska +** skapa längst ned på sidan.

7. Välj **Skapa**.

8. Upprepa steg 1 till sju för att skapa den andra virtuella datorn för uppsättningen. Ersätt inställningarna för den virtuella datorn med följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange **myVM4**. |
    | Tillgänglighetsuppsättning | Välj **myAvailabilitySet2.** |
    | Virtual Network | Välj **myVNet.** |
    | Nätverkssäkerhetsgrupp | Välj **myNSG.** |
    | Alternativ för belastningsutjämning | Välj **Azure Load Balancer**. |
    | Välj en lastbalanserare | Välj **myLoadBalancer**. |
    | Välj en backend-pool | Välj **myBackendPool**. |

## <a name="install-iis"></a>Installera IIS

I det här avsnittet använder du den värd Azure Bastion som du skapade tidigare för att ansluta till de virtuella datorerna och installera IIS.

1. I sökrutan högst upp i portalen anger du **Virtuell dator**.

2. Välj **Virtuella datorer** i sökresultatet.

3. Välj **myVM1.**

4. På sidan **Översikt** i myVM1 väljer du **Anslut**  >  **Bastion.**

5. Välj **Använd Bastion.**

6. Ange det **användarnamn** och **lösenord som** du skapade när du skapade den virtuella datorn.

7. Välj **Anslut**.

7. På serverdatorn går du till **Administrationsverktyg för Windows**  >  **Windows PowerShell**.

8. Kör följande kommandon i PowerShell-fönstret för att:

    * Installera IIS-servern
    * Ta bort iisstart.htm standardfilen
    * Lägg till en iisstart.htm fil som visar namnet på den virtuella datorn:

   ```powershell
    # Install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    Remove-Item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
8. Stäng Bastion-sessionen med **myVM1**.

9. Upprepa steg 1 till åtta **för myVM2,** **myVM3** och **myVM4.**

## <a name="test-the-load-balancer"></a>Testa lastbalanseraren

I det här avsnittet identifierar du den offentliga IP-adressen för lastbalanseraren. Du använder IP-adressen för att testa lastbalanseringsåtgärden.

1. I sökrutan högst upp i portalen anger du **Offentlig IP-adress.**

2. Välj **Offentliga IP-adresser** i sökresultatet.

3. Välj **myPublicIP-lb.**

4. Observera den offentliga IP-adress som **anges i IP-adressen** **på översiktssidan** i **myPublicIP-lb:**

    :::image type="content" source="./media/tutorial-multi-availability-sets-portal/find-public-ip.png" alt-text="Hitta den offentliga IP-adressen för lastbalanseraren." border="true":::

5. Öppna en webbläsare och ange den offentliga IP-adressen i adressfältet:

    :::image type="content" source="./media/tutorial-multi-availability-sets-portal/verify-load-balancer.png" alt-text="Testa lastbalanserare med webbläsare." border="true":::

6. Välj Uppdatera i webbläsaren för att se trafiken som balanserats mot de andra virtuella datorerna i serverpoolen.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort lastbalanseraren och de stödresurser som finns med följande steg:

1. I sökrutan överst i portalen anger du **Resursgrupp**.

2. Välj **Resursgrupper** i sökresultatet.

3. Välj **AlternativetLBmultiAVS-rg**.

4. På översiktssidan för **AlternativetLBmultiAVS-rg väljer** du **Ta bort resursgrupp.**

5. Ange **FörlBmultiAVS-rg** i **SKRIV RESURSGRUPPENS NAMN.**

6. Välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här kursen får du:

* Skapat ett virtuellt nätverk Azure Bastion värd.
* Skapat en Azure-Standard Load Balancer.
* Skapade två tillgänglighetsuppsättningar med två virtuella datorer per uppsättning.
* Installerat IIS och testat lastbalanseraren.

Gå vidare till nästa artikel om du vill lära dig hur du skapar en Azure Load Balancer:
> [!div class="nextstepaction"]
> [Skapa en lastbalanserare mellan regioner](tutorial-cross-region-portal.md)

