---
title: Filtrera nätverks trafik – självstudie – Azure Portal
titlesuffix: Azure Virtual Network
description: I den här självstudien får du lära dig hur du filtrerar nätverks trafik till ett undernät, med en nätverks säkerhets grupp, med hjälp av Azure Portal.
services: virtual-network
author: KumudD
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.service: virtual-network
ms.topic: tutorial
ms.date: 03/06/2021
ms.author: kumud
ms.openlocfilehash: 746e44c85d4dd9a662556a73f1e4ab0701d31400
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102435932"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-the-azure-portal"></a>Självstudie: filtrera nätverks trafik med en nätverks säkerhets grupp med hjälp av Azure Portal

Du kan använda en nätverks säkerhets grupp för att filtrera inkommande och utgående nätverks trafik från ett undernät i ett virtuellt nätverk.

Nätverkssäkerhetsgrupper innehåller säkerhetsregler som filtrerar nätverkstrafik efter IP-adress, port och protokoll. Säkerhetsregler tillämpas på resurser som har distribuerats i ett undernät. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en nätverkssäkerhetsgrupp och säkerhetsregler
> * Skapa ett virtuellt nätverk och associera en nätverkssäkerhetsgrupp med ett undernät
> * Distribuera virtuella datorer (VM) i ett undernät
> * Testa trafikfilter

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. Välj **skapa en resurs** i det övre vänstra hörnet i portalen.

2. I rutan Sök anger du **Virtual Network**. Välj **Virtual Network** i Sök resultaten.

3. På sidan **Virtual Network** väljer du **skapa**.

4. I **Skapa virtuellt nätverk** anger eller väljer du den här informationen på fliken **grundläggande** :

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** |   |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **Skapa ny**.  </br> Ange **myResourceGroup**. </br> Välj **OK**. |
    | **Instansinformation** |   |
    | Name | Ange **myVNet**. |
    | Region | Välj **(US) USA, östra**. |

5. Välj fliken **Granska + skapa** eller Välj knappen blå **granskning + skapa** längst ned på sidan.

6. Välj **Skapa**.

## <a name="create-application-security-groups"></a>Skapa programsäkerhetsgrupper

En programsäkerhetsgrupp gör att du kan gruppera ihop servrar med liknande funktioner, till exempel webbservrar.

1. Välj **skapa en resurs** i det övre vänstra hörnet i portalen.

2. I rutan Sök anger du **program säkerhets grupp**. Välj **program säkerhets grupp** i Sök resultaten.

3. På sidan **program säkerhets grupp** väljer du **skapa**.

4. I **skapa en program säkerhets grupp** anger eller väljer du den här informationen på fliken **grundläggande** :

    | Inställning | Värde |
    | ------- | ----- |
    |**Projektinformation** |  |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. |
    | **Instansinformation** |  |
    | Name | Ange **myAsgWebServers**. |
    | Region | Välj **(US) USA, östra**. | 

5. Välj fliken **Granska + skapa** eller Välj knappen blå **granskning + skapa** längst ned på sidan.

6. Välj **Skapa**.

7. Upprepa steg 4 igen och ange följande värden:

    | Inställning | Värde |
    | ------- | ----- |
    |**Projektinformation** |  |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. |
    | **Instansinformation** |  |
    | Name | Ange **myAsgMgmtServers**. |
    | Region | Välj **(US) USA, östra**. |

8. Välj fliken **Granska + skapa** eller Välj knappen blå **granskning + skapa** längst ned på sidan.

9. Välj **Skapa**.

## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

En nätverks säkerhets grupp skyddar nätverks trafiken i det virtuella nätverket.

1. Välj **skapa en resurs** i det övre vänstra hörnet i portalen.

2. I rutan Sök anger du **nätverks säkerhets grupp**. Välj **nätverks säkerhets grupp** i Sök resultaten.

3. På sidan **nätverks säkerhets grupp** väljer du **skapa**.

4. I **skapa nätverks säkerhets grupp** anger eller väljer du den här informationen på fliken **grundläggande** :

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** |   |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. |
    | **Instansinformation** |   |
    | Name | Ange **myNSG**. |
    | Location | Välj **(US) USA, östra**. | 

5. Välj fliken **Granska + skapa** eller Välj knappen blå **granskning + skapa** längst ned på sidan.

6. Välj **Skapa**.

## <a name="associate-network-security-group-to-subnet"></a>Associera nätverkssäkerhetsgrupp till undernät

I det här avsnittet ska du associera nätverks säkerhets gruppen med under nätet för det virtuella nätverk som vi skapade tidigare.

1. I rutan **Sök efter resurser, tjänster och dokument** högst upp i portalen börjar du skriva **myNsg**. Välj **myNsg** när det visas bland sökresultaten.

2. På sidan Översikt i **myNSG** väljer du **undernät** i **Inställningar**.

3. På sidan **Inställningar** väljer du **associera**:

    :::image type="content" source="./media/tutorial-filter-network-traffic/associate-nsg-subnet.png" alt-text="Koppla NSG till undernät." border="true":::

3. Under **associera undernät** väljer du **virtuellt nätverk** och väljer sedan **myVNet**. 

4. Välj **undernät**, Välj **standard** och välj sedan **OK**.

## <a name="create-security-rules"></a>Skapa säkerhetsregler

1. I **Inställningar** för **MyNSG** väljer du **inkommande säkerhets regler**.

2. I **inkommande säkerhets regler** väljer du **+ Lägg till**:

    :::image type="content" source="./media/tutorial-filter-network-traffic/add-inbound-rule.png" alt-text="Lägg till inkommande säkerhets regel." border="true":::

3. Skapa en säkerhetsregel som tillåter portarna 80 och 443 till programsäkerhetsgruppen **myAsgWebServers**. I **Lägg till inkommande säkerhets regel** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Källa | Lämna standardvärdet **.** |
    | Källportintervall | Lämna standardvärdet **(*)** |
    | Mål | Välj **program säkerhets grupp**. |
    | Säkerhets grupp för mål program | Välj **myAsgWebServers**. |
    | Tjänst | Lämna standardvärdet **anpassad**. |
    | Målportintervall | Ange **80 443**. |
    | Protokoll | Välj **TCP**. |
    | Action | Lämna standardvärdet **Allow**. |
    | Prioritet | Lämna standardvärdet **100**. |
    | Name | Ange **Allow-Web-all**. |

    :::image type="content" source="./media/tutorial-filter-network-traffic/inbound-security-rule.png" alt-text="Inkommande säkerhets regel." border="true":::

3. Slutför steg 2 igen med följande värden:

    | Inställning | Värde |
    | ------- | ----- |
    | Källa | Lämna standardvärdet **.** |
    | Källportintervall | Lämna standardvärdet **(*)** |
    | Mål | Välj **program säkerhets grupp**. |
    | Säkerhets grupp för mål program | Välj **myAsgMgmtServers**. |
    | Tjänst | Lämna standardvärdet **anpassad**. |
    | Målportintervall | Ange **3389**. |
    | Protokoll | Välj **TCP**. |
    | Action | Lämna standardvärdet **Allow**. |
    | Prioritet | Lämna standardvärdet **110**. |
    | Name | Ange **Allow-RDP-all**. |

    > [!CAUTION]
    > I den här artikeln visas RDP (port 3389) för Internet för den virtuella dator som är tilldelad **myAsgMgmtServers** program säkerhets grupp. 
    >
    > För produktions miljöer, i stället för att exponera port 3389 på Internet, rekommenderar vi att du ansluter till Azure-resurser som du vill hantera med hjälp av en VPN-, privat nätverks anslutning eller Azure-skydds.
    >
    > Mer information om Azure skydds finns i [Vad är Azure skydds?](../bastion/bastion-overview.md).

När du har slutfört steg 1–3 granskar du de regler som du skapat. Listan bör se ut som i följande exempel:

:::image type="content" source="./media/tutorial-filter-network-traffic/security-rules.png" alt-text="Säkerhets regler." border="true":::

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket.

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

1. Välj **skapa en resurs** i det övre vänstra hörnet i portalen.

2. Välj **Compute** och välj sedan **virtuell dator**.

3. I **skapa en virtuell dator** anger eller väljer du den här informationen på fliken **grundläggande** :

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** |  |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. |
    | **Instansinformation** |   |
    | Namn på virtuell dator | Ange **myVMWeb**. |
    | Region | Välj **(US) USA, östra**. |
    | Alternativ för tillgänglighet | Lämna standardvärdet ingen redundans krävs. |
    | Bild | Välj **Windows Server 2019 Data Center-gen1**. |
    | Azure Spot-instans | Låt standardvärdet vara omarkerat. |
    | Storlek | Välj **Standard_D2s_V3**. |
    | **Administratörs konto** |   |
    | Användarnamn | Ange ett användar namn. |
    | Lösenord | Ange ett lösen ord. |
    | Bekräfta lösenordet | Ange lösenordet igen. |
    | **Regler för inkommande portar** |   |
    | Offentliga inkommande portar | Välj **Ingen**. |

4. Välj fliken **Nätverk**.

5. På fliken **nätverk** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **Nätverksgränssnitt** |   |
    | Virtuellt nätverk | Välj **myVNet**. |
    | Undernät | Välj **Standard (10.0.0.0/24)**. |
    | Offentlig IP-adress | Lämna standardvärdet för en ny offentlig IP-adress. |
    | Nätverks säkerhets grupp för nätverkskort | Välj **Ingen**. | 

6. Välj fliken **Granska + skapa** eller Välj knappen blå **granskning + skapa** längst ned på sidan.

7. Välj **Skapa**.

### <a name="create-the-second-vm"></a>Skapa den andra virtuella datorn

Slutför steg 1-7 igen, men i steg 3 namnger du VM- **myVMMgmt**. Det tar några minuter att distribuera den virtuella datorn. 

Fortsätt inte till nästa steg förrän den virtuella datorn har distribuerats.

## <a name="associate-network-interfaces-to-an-asg"></a>Koppla nätverksgränssnitt till en ASG

När portalen skapade de virtuella datorerna skapade den ett nätverksgränssnitt för varje virtuell dator och anslöt nätverksgränssnittet till den virtuella datorn. 

Lägg till nätverksgränssnittet för varje virtuell dator till en av de programsäkerhetsgrupper som du skapade tidigare:

1. I rutan **Sök efter resurser, tjänster och dokument** högst upp i portalen börjar du skriva **myVMWeb**. När den virtuella **myVMWeb** -datorn visas i Sök resultaten väljer du den.

2. I **Inställningar** väljer du **nätverk**.  

3. Välj fliken **program säkerhets grupper** och välj sedan **Konfigurera program säkerhets grupper**.

    :::image type="content" source="./media/tutorial-filter-network-traffic/configure-app-sec-groups.png" alt-text="Konfigurera program säkerhets grupper." border="true":::

4. I **Konfigurera program säkerhets grupper väljer du** **myAsgWebServers**. Välj **Spara**.

    :::image type="content" source="./media/tutorial-filter-network-traffic/select-asgs.png" alt-text="Välj program säkerhets grupper." border="true":::

5. Slutför steg 1 och 2 igen, Sök efter den virtuella **myVMMgmt** -datorn och välj  **myAsgMgmtServers** -grupperna.

## <a name="test-traffic-filters"></a>Testa trafikfilter

1. Anslut till den virtuella **myVMMgmt** -datorn. Skriv **myVMMgmt** i rutan Sök högst upp i portalen. När **myVMMgmt** visas i Sök resultaten väljer du det. Välj knappen **Anslut**.

2. Välj **Hämta RDP-fil**.

3. Öppna den nedladdade RDP-filen och välj **Anslut**. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn.

4. Välj **OK**.

5. Du kan få en certifikat varning under anslutnings processen. Om du får varningen väljer du **Ja** eller **Fortsätt** för att fortsätta med anslutningen.

    Anslutningen lyckas eftersom port 3389 tillåts inkommande från Internet till program säkerhets gruppen **myAsgMgmtServers** . 
    
    Nätverks gränssnittet för **myVMMgmt** är associerat med **myAsgMgmtServers** program säkerhets grupp och tillåter anslutningen.

6. Öppna en PowerShell-session på **myVMMgmt**. Anslut till **myVMWeb** med hjälp av följande exempel: 

    ```powershell
    mstsc /v:myVmWeb
    ```

    RDP-anslutningen från **myVMMgmt** till **myVMWeb** lyckas eftersom virtuella datorer i samma nätverk kan kommunicera med var och en av portarna som standard.
    
    Du kan inte skapa en RDP-anslutning till den virtuella **myVMWeb** -datorn från Internet. Säkerhets regeln för **myAsgWebServers** förhindrar anslutningar till port 3389 inkommande från Internet. Inkommande trafik från Internet nekas som standard till alla resurser.

7. Om du vill installera Microsoft IIS på den virtuella **myVMWeb** -datorn anger du följande kommando från en PowerShell-session på den virtuella **myVMWeb** -datorn:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

8. När IIS-installationen har slutförts kan du koppla från den virtuella **myVMWeb** -datorn, vilket gör att du lämnar den **myVMMgmt** virtuella datorn fjärr skrivbords anslutning.

9. Koppla från den virtuella **myVMMgmt** -datorn.

10. I rutan **Sök efter resurser, tjänster och dokument** överst i Azure Portal börjar du skriva **myVMWeb** från datorn. När **myVMWeb** visas i Sök resultaten väljer du det. Notera den **offentliga IP-adressen** för den virtuella datorn. Adressen som visas i följande exempel är 23.96.39.113, men adressen är annorlunda:

    :::image type="content" source="./media/tutorial-filter-network-traffic/public-ip-address.png" alt-text="Offentlig IP-adress." border="true":::
    
11. Du kan kontrol lera att du har åtkomst till **myVMWeb** -webbservern från Internet genom att öppna en webbläsare på datorn och bläddra till `http://<public-ip-address-from-previous-step>` . 

Välkomst skärmen i IIS visas, eftersom port 80 tillåts inkommande från Internet till program säkerhets gruppen **myAsgWebServers** . 

Nätverks gränssnittet som är kopplat till **myVMWeb** är associerat med **myAsgWebServers** program säkerhets grupp och tillåter anslutningen. 

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, skalningsuppsättningen och alla resurser som den innehåller:

1. Skriv **myResourceGroup** i **sökrutan** överst i portalen. När du ser **myResourceGroup** i sökresultatet väljer du den.
2. Välj **Ta bort resursgrupp**.
3. Skriv **myResourceGroup** i **SKRIV RESURSGRUPPSNAMNET:** och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här kursen får du:

* Skapade en nätverks säkerhets grupp och kopplat den till ett virtuellt nätverks under nät. 
* Skapade program säkerhets grupper för webb och hantering.
* Två virtuella datorer har skapats.
* Testat nätverks filtrering för program säkerhets gruppen.

Mer information om nätverkssäkerhetsgrupper finns i [Översikt över nätverkssäkerhetsgrupper](./network-security-groups-overview.md) och [Hantera en nätverkssäkerhetsgrupp](manage-network-security-group.md).

Azure dirigerar som standard trafik mellan undernät. Du kan i stället välja att exempelvis dirigera trafik mellan undernät via en virtuell dator, som fungerar som en brandvägg. 

Gå vidare till nästa självstudie om du vill veta hur du skapar en routningstabell.
> [!div class="nextstepaction"]
> [Skapa en routningstabell](./tutorial-create-route-table-portal.md)