---
title: Begränsa åtkomsten till PaaS-resurser – självstudier – Azure Portal
description: I den här självstudien får du lära dig att begränsa nätverksåtkomst till Azure-resurser, som Azure Storage och Azure SQL Database, med tjänstslutpunkter för virtuellt nätverk och Azure-portalen.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/11/2020
ms.author: kumud
ms.openlocfilehash: cb3a4b6a726ee9163582b15586c65fc750712c63
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97368301"
---
# <a name="tutorial-restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>Självstudie: Begränsa nätverksåtkomst till PaaS-resurser med tjänstslutpunkter för virtuellt nätverk och Azure-portalen

Med tjänstslutpunkter för virtuellt nätverk kan du begränsa nätverksåtkomsten till vissa Azure-tjänsters resurser till ett undernät för virtuella datorer. Du kan också ta bort resursernas internetåtkomst. Tjänstslutpunkterna möjliggör direktanslutning från ditt virtuella nätverk till Azure-tjänster som stöds, så att du kan använda det privata adressutrymmet i det virtuella nätverket för åtkomst till Azure-tjänsterna. Trafik till Azure-resurser genom tjänstslutpunkterna finns alltid kvar i Microsoft Azure-stamnätverket. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk med ett undernät
> * Lägga till ett undernät och aktivera en tjänstslutpunkt
> * Skapa en Azure-resurs och tillåt nätverksåtkomst till den från enbart ett undernät
> * Distribuera en virtuell dator (VM) till varje undernät
> * Bekräfta åtkomst till en resurs från ett undernät
> * Bekräfta att åtkomst nekas för en resurs från ett undernät och internet

Om du vill kan du slutföra den här självstudien med [Azure CLI](tutorial-restrict-network-access-to-resources-cli.md) eller [Azure PowerShell](tutorial-restrict-network-access-to-resources-powershell.md).

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. Klicka på **+ Skapa en resurs** längst upp till vänster på Azure Portal.
2. Välj **nätverk** och välj sedan **virtuella nätverk**.
3. Klicka på **+ Lägg till** och ange följande information: 

   |Inställning|Värde|
   |----|----|
   |Prenumeration| Välj din prenumeration|
   |Resursgrupp | Välj **Skapa ny** och skriv *myResourceGroup*.|
   |Name| Ange *myVirtualNetwork* |
   |Region| Välj **(US) USA, östra** |

   ![Ange grundläggande information om ditt virtuella nätverk](./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png)

4. Klicka på **Nästa: IP-adresser >**
   
   |Inställning|Värde|
   |----|----|
   |IPv4Address utrymme| Lämna som standard |
   |Namn på undernät| Klicka på **standard** och ändra namnet från "standard" till "offentlig"|
   |Adress intervall för under nätet| Lämna som standard|

5. Klicka på **Nästa: säkerhets >** 
   
   |Inställning|Värde|
   |----|----|   
   |BastionHost| Inaktivera|
   |DDoS-skydd| Inaktivera|
   |Brandvägg| Inaktivera|

6. När du är klar klickar du på **Granska och skapa**.
7. Om verifierings kontrollerna godkänns klickar du på **skapa**.
8. Vänta tills distributionen är klar och klicka sedan på **gå till resurs** eller gå vidare till nästa avsnitt. 

## <a name="enable-a-service-endpoint"></a>Aktivera en tjänstslutpunkt

Tjänstslutpunkter är aktiverade per tjänst och undernät. Så här skapar du ett undernät och aktiverar en tjänst slut punkt för under nätet:

1. Om du inte redan är på sidan virtuell nätverks resurs kan du söka efter det nyligen skapade nätverket i rutan **Sök efter resurser, tjänster och dokument** högst upp i portalen, ange *myVirtualNetwork* och välja den från listan.
2. I menyn **Inställningar** (vänster) väljer du **undernät** och väljer sedan **+ undernät**, som visas:

    ![Lägga till undernät](./media/tutorial-restrict-network-access-to-resources/add-subnet.png) 

3. Under **Lägg till undernät** väljer du eller anger följande information och väljer sedan **OK**:

    |Inställning|Värde|
    |----|----|
    |Namn| Privat |
    |Adressintervall| Lämna som standard|
    |Tjänstslutpunkter| Välj **Microsoft. Storage**|
    |Principer för tjänstslutpunkter | Lämna standardvärdet som 0 |

> [!CAUTION]
> Se [Ändra undernätsinställningar](virtual-network-manage-subnet.md#change-subnet-settings) innan du aktiverar en tjänstslutpunkt för ett befintligt undernät som innehåller resurser.

4. Klicka på **Spara** och stäng sedan under näts fönstret till höger. Det nyligen skapade under nätet ska visas i listan.

## <a name="restrict-network-access-for-a-subnet"></a>Begränsa nätverksåtkomst för ett undernät

Som standard kan alla virtuella dator instanser i ett undernät kommunicera med alla resurser. Du kan begränsa kommunikationen till och från alla resurser i ett undernät genom att skapa en nätverks säkerhets grupp och associera den till under nätet:

1. Välj **Alla tjänster** i det övre vänstra hörnet i Azure-portalen.
2. Välj **nätverk** och välj sedan (eller Sök efter) **nätverks säkerhets grupper**.
3. På sidan **nätverks säkerhets grupper** klickar du på **+ Lägg till**.
4. Ange följande information 

    |Inställning|Värde|
    |----|----|
    |Prenumeration| Välj din prenumeration|
    |Resursgrupp | Välj *myResourceGroup* i listan|
    |Name| Ange **myNsgPrivate** |
    |Location| Välj **USA, östra** |

5. Klicka på **Granska + skapa** och klicka på **skapa** när validerings kontrollen har slutförts.
6. När nätverks säkerhets gruppen har skapats klickar du på **gå till resurs** eller söker efter *myNsgPrivate*.
7. Under **Inställningar** till vänster väljer du **utgående säkerhets regler**.
8. Välj **+ Lägg till**.
9. Skapa en regel som tillåter utgående kommunikation till Azure Storage-tjänsten. Välj eller ange följande information och välj **Lägg till**:

    |Inställning|Värde|
    |----|----|
    |Källa| Välj **VirtualNetwork** |
    |Källportintervall| * |
    |Mål | Välj **service tag**|
    |Måltjänsttagg | Välj **Lagring**|
    |Målportintervall| Lämna standard som *8080* |
    |Protokoll|Valfri|
    |Åtgärd|Tillåt|
    |Prioritet|100|
    |Namn|Byt namn till **Tillåt-lagring – alla**|

10. Skapa en annan säkerhetsregel för utgående kommunikation som nekar utgående kommunikation till internet. Den här regeln åsidosätter en standardregel i alla nätverkssäkerhetsgrupper som tillåter utgående internetkommunikation. Slutför stegen 6-9 från ovan med följande värden:

    |Inställning|Värde|
    |----|----|
    |Källa| Välj **VirtualNetwork** |
    |Källportintervall| * |
    |Mål | Välj **service tag**|
    |Måltjänsttagg| Välj **Internet**|
    |Målportintervall| * |
    |Protokoll|Valfri|
    |Åtgärd|**Ändra standard till *neka*** |
    |Prioritet|110|
    |Name|Ändra till *neka – Internet – alla*|

11. Skapa en *inkommande säkerhets regel* som tillåter att Remote Desktop Protocol (RDP)-trafik till under nätet var som helst. Regeln åsidosätter en standardsäkerhetsregel som nekar all inkommande trafik från internet. Fjärrskrivbordsanslutningar tillåts i undernätet så att anslutningen kan testas i ett senare steg. 
12. Under **Inställningar** väljer du **inkommande säkerhets regler**.
13. Välj **+ Lägg till** och Använd följande värden:

    |Inställning|Värde|
    |----|----|
    |Källa| Valfri |
    |Källportintervall| * |
    |Mål | Välj **VirtualNetwork**|
    |Målportintervall| Ändra till *3389* |
    |Protokoll|Valfri|
    |Åtgärd|Tillåt|
    |Prioritet|120|
    |Name|Ändra till *Tillåt-RDP – alla*|

   >[!WARNING] 
   > RDP-port 3389 exponeras för Internet. Detta rekommenderas endast för testning. För *produktions miljöer* rekommenderar vi att du använder en VPN-eller privat anslutning.

1.  Under **Inställningar** väljer du **Undernät**.
2.  Klicka på **+ Associera**.
3.  Under **virtuellt nätverk** väljer du **myVirtualNetwork**.
4.  Under **undernät** väljer du **privat** och väljer sedan **OK**.

## <a name="restrict-network-access-to-a-resource"></a>Begränsa nätverksåtkomst till en resurs

De steg som krävs för att begränsa nätverks åtkomsten till resurser som skapats via Azure-tjänster, som är aktiverade för tjänst slut punkter varierar mellan olika tjänster. Läs dokumentationen för enskilda tjänster för specifika åtgärder för varje tjänst. Resten av den här självstudiekursen innehåller steg för att begränsa nätverksåtkomsten för ett Azure Storage-konto, som ett exempel.

### <a name="create-a-storage-account"></a>Skapa ett lagringskonto

1. Klicka på **+ Skapa en resurs** längst upp till vänster på Azure Portal.
2. Ange "lagrings konto" i Sök fältet och välj det på den nedrullningsbara menyn.
3. Klicka på **+ Lägg till**.
4. Ange följande information:

    |Inställning|Värde|
    |----|----|
    |Prenumeration| Välj din prenumeration|
    |Resursgrupp| Välj *myResourceGroup*|
    |Lagrings konto namn| Ange ett namn som är unikt i alla Azure-platser, mellan 3–24 tecken långt och som endast innehåller siffror och gemener.|
    |Location| Välj **(US) USA, östra** |
    |Prestanda|Standard|
    |Typ av konto| StorageV2 (generell användning v2)|
    |Replikering| Lokalt redundant lagring (LRS)|

5. Välj **skapa + granska** och klicka på **skapa** när validerings kontrollerna har slutförts. 

>[!NOTE] 
> Det kan ta några minuter att slutföra distributionen.

6. När lagrings kontot har skapats klickar **du på gå till resurs**

### <a name="create-a-file-share-in-the-storage-account"></a>Skapa en filresurs i lagringskontot

1. Gå till översikts sidan för ditt lagrings konto.
2. Välj app-ikonen för **fil resurser** och klicka sedan på **+ fil resurs**.

    |Inställning|Värde|
    |----|----|
    |Namn| My-File-Share|
    |Kvot| Ange till maximalt |

   ![Lagringskonto](./media/tutorial-restrict-network-access-to-resources/storage-account.png) 

3. Klicka på **Skapa**.
4. Fil resursen bör visas i Azure-fönstret, om du inte klickar på **Uppdatera**

### <a name="restrict-network-access-to-a-subnet"></a>Begränsa nätverksåtkomst till ett undernät

Som standard godkänner lagringskonton nätverksanslutningar från klienter i alla nätverk, inklusive internet. Du kan begränsa nätverks åtkomsten från Internet och alla andra undernät i alla virtuella nätverk (förutom det *privata* under nätet i det virtuella *myVirtualNetwork* -nätverket.) Begränsa nätverks åtkomst till ett undernät:

1. Under **Inställningar** för ditt (unika namngivna) lagrings konto väljer du **nätverk**.
2. Välj **Valda nätverk**.
3. Välj **+ Lägg till befintligt virtuellt nätverk**.
4. Under **Lägg till nätverk** väljer du följande värden och sedan **Lägg till**:

    |Inställning|Värde|
    |----|----|
    |Prenumeration| Välj din prenumeration|
    |Virtuella nätverk| **myVirtualNetwork**|
    |Undernät| **Privat**|

    ![Skärm bild som visar fönstret Lägg till nätverk där du kan ange de angivna värdena.](./media/tutorial-restrict-network-access-to-resources/virtual-networks.png)

5. Klicka på **Lägg till** och sedan direkt på ikonen **Spara** för att spara ändringarna.
6. Under **Inställningar** för lagrings kontot väljer du **åtkomst nycklar**, som du ser i följande bild:

      ![Skärm bild som visar åtkomst nycklar som valts från inställningar där du kan hämta en nyckel.](./media/tutorial-restrict-network-access-to-resources/storage-access-key.png)

7. Klicka på **Visa nycklar** och anteckna **nyckel** värden, eftersom du manuellt måste ange KEY1 i ett senare steg när du mappar fil resursen till en enhets beteckning i en virtuell dator.

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Om du vill testa nätverksåtkomsten till ett lagringskonto distribuerar du en virtuell dator till varje undernät.

### <a name="create-the-first-virtual-machine"></a>Skapa din första virtuella dator

1. Från Sök resurser. . ." fältet söker du efter **virtuella datorer**.
2. Välj **+ Lägg till > virtuell dator**. 
3. Ange följande information:

   |Inställning|Värde|
   |----|----|
   |Prenumeration| Välj din prenumeration|
   |Resursgrupp| Välj * * myResourceGroup, som skapades tidigare.|
   |Namn på virtuell dator| Ange *myVmPublic*|
   |Region | (USA) USA, östra
   |Alternativ för tillgänglighet| Tillgänglighetszon|
   |Tillgänglighetszon | 1 |
   |Bild | Windows Server 2019 Data Center – gen1 |
   |Storlek | Välj den virtuella dator instans storlek som du vill använda |
   |Användarnamn|Ange ett valfritt användarnamn.|
   |Lösenord| Ange ett valfritt lösenord. Lösen ordet måste vara minst 12 tecken långt och uppfylla de [definierade komplexitets kraven](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
   |Offentliga inkommande portar | Tillåt valda portar |
   |Välj inkommande portar | Lämna standard inställningen till *RDP (3389)* |

   ![Välja ett virtuellt nätverk](./media/tutorial-restrict-network-access-to-resources/virtual-machine-settings.png)
  
4. Välj fliken **nätverk** och välj sedan **myVirtualNetwork**. 
5. Välj det *offentliga* under nätet.
6. Under **nätverkskort nätverks säkerhets grupp** väljer du **Avancerat**. Portalen skapar automatiskt en nätverkssäkerhetsgrupp som tillåter port 3389. Den behöver du öppna för att ansluta till den virtuella datorn i ett senare steg. 

   ![Ange grundläggande information om en virtuell dator](./media/tutorial-restrict-network-access-to-resources/virtual-machine-basics.png)

7. Välj **Granska och skapa** och sedan **skapa** och vänta tills distributionen har slutförts.
8. Klicka på **gå till resurs**, eller öppna sidan **hem > virtuella datorer** och välj den virtuella dator som du nyss skapade *myVmPublic*, som ska startas.

### <a name="create-the-second-virtual-machine"></a>Skapa den andra virtuella datorn

1. Slutför steg 1-8 igen, men i steg 3 namnger du den virtuella datorn *myVmPrivate* och anger **offentlig inkommande port** till "ingen". 
2. I steg 4-5 väljer du det **privata** under nätet.

>[!NOTE]
> Inställningarna för **nätverks säkerhets gruppen** och **offentliga inkommande portar** ska spegla avbildningen som visas nedan, inklusive det blå bekräftelse fönstret som säger: "all offentlig Internet trafik kommer att blockeras som standard".

   ![Skapa en privat virtuell dator](./media/tutorial-restrict-network-access-to-resources/create-private-virtual-machine.png)

3. Välj **Granska och skapa** och sedan **skapa** och vänta tills distributionen har slutförts. 

>[!WARNING]
> Fortsätt inte till nästa steg förrän distributionen är klar.

4. Vänta tills bekräftelse fönstret visas och klicka på **gå till resurs**.

   ![Skapa bekräftelse fönster för en privat virtuell dator](./media/tutorial-restrict-network-access-to-resources/create-vm-confirmation-window.png)

## <a name="confirm-access-to-storage-account"></a>Bekräfta åtkomst till lagringskontot

1. När den virtuella datorn *myVmPrivate* har skapats klickar **du på gå till resurs**. 
2. Anslut till den virtuella datorn genom att välja **Connect > RDP**.
3. När du har valt knappen **Anslut** skapas en Remote Desktop Protocol-fil (. RDP). Klicka på **Ladda ned RDP-fil** för att ladda ned till datorn.  
4. Öppna den nedladdade RDP-filen. Välj **Anslut** om du uppmanas att göra det. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn. Du kan behöva välja **fler alternativ** och sedan **använda ett annat konto** för att ange de autentiseringsuppgifter som du angav när du skapade den virtuella datorn. I fältet e-post anger du de autentiseringsuppgifter för "administratörs konto: användar namn" som du angav tidigare. 
5. Välj **OK**.
6. Du kan få en certifikatvarning under inloggningen. Om du ser varningen väljer du **Ja** eller **Fortsätt** för att fortsätta med anslutningen. Du bör se den virtuella datorn som visas:

   ![Visa privat virtuell dator som kör](./media/tutorial-restrict-network-access-to-resources/virtual-machine-running.png)

7. Öppna en PowerShell CLI-instans i fönstret VM.
8. Med hjälp av skriptet nedan mappar du Azure-filresursen till enhet Z med PowerShell. Innan du kör kommandona som följer ersätter `<storage-account-key>` du och båda `<storage-account-name>` fälten med värdena du angav och "d tidigare i [skapa ett lagrings konto](#create-a-storage-account).

   ```powershell
   $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
   $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
   New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
   ```

   PowerShell returnerar utdata som liknar följande exempelutdata:

   ```powershell
   Name           Used (GB)     Free (GB) Provider      Root
   ----           ---------     --------- --------      ----
   Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
   ```

   Azure-fildelningen har mappats till enhet Z.

9.   Stäng fjärrskrivbordssessionen för den virtuella datorn *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Bekräfta att åtkomst till lagringskontot nekas

1. I rutan **Sök efter resurser, tjänster och dokument** högst upp i portalen skriver du *myVmPublic*.
2. När **myVmPublic** visas i sökresultaten väljer du det.
3. Slutför steg 1-8 ovan i [Bekräfta åtkomst till lagrings kontot](#confirm-access-to-storage-account) för den virtuella *myVmPublic* -datorn.

   Efter en kort stund visas felet `New-PSDrive : Access is denied`. Åtkomst nekas eftersom den virtuella datorn *myVmPublic* distribueras i det *offentliga* undernätet. Det *offentliga* undernätet har inte en tjänstslutpunkt aktiverat för Azure Storage. Lagringskontot tillåter endast nätverksåtkomst från det *privata* undernätet, inte det *offentliga* undernätet.

4. Stäng fjärrskrivbordssessionen för den virtuella datorn *myVmPublic*.
5. Gå tillbaka till det unika namnet lagrings konto som du skapade tidigare i Azure Portal.
6. Under fil tjänst väljer du **fil resurser**, mappen *My-File-Share*, som skapades tidigare.
7. Du bör få följande fel meddelande:

   ![Fel för nekad åtkomst](./media/tutorial-restrict-network-access-to-resources/access-denied-error.png)
   
>[!NOTE] 
> Åtkomsten nekas eftersom din dator inte är i det *privata* undernätet i det virtuella nätverket för *MyVirtualNetwork*.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, skalningsuppsättningen och alla resurser som den innehåller:

1. Skriv *myResourceGroup* i **sökrutan** överst i portalen. När du ser **myResourceGroup** i sökresultatet väljer du den.
2. Välj **Ta bort resursgrupp**.
3. Skriv *myResourceGroup* i **SKRIV RESURSGRUPPSNAMNET:** och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du aktiverat en tjänstslutpunkt för ett undernät för ett virtuellt nätverk. Du har lärt dig att du kan aktivera tjänstslutpunkter för resurser som distribueras från flera Azure-tjänster. Du har skapat ett Azure Storage-konto och begränsat nätverksåtkomst för lagringskontot till enbart resurser inom ett undernät för ett virtuellt nätverk. Om du vill veta mer om tjänstslutpunkter går du till [Översikt över tjänstslutpunkter](virtual-network-service-endpoints-overview.md) och [Hantera undernät](virtual-network-manage-subnet.md).

Om du har flera virtuella nätverk i ditt konto kanske du vill ansluta två virtuella nätverk så att resurserna i vart och ett kan kommunicera med varandra. Om du vill lära dig mer om att ansluta virtuella nätverk går du vidare till nästa självstudie.

> [!div class="nextstepaction"]
> [Ansluta virtuella nätverk](./tutorial-connect-virtual-networks-portal.md)
