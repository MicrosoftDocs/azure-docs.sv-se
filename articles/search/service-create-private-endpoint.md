---
title: Skapa en privat slut punkt för en säker anslutning
titleSuffix: Azure Cognitive Search
description: Konfigurera en privat slut punkt i ett virtuellt nätverk för en säker anslutning till en Azure Kognitiv sökning-tjänst.
manager: nitinme
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 7445ac5d750ac29d3e6ce466a48e82efd1bcde40
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100545538"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search"></a>Skapa en privat slut punkt för en säker anslutning till Azure Kognitiv sökning

I den här artikeln använder du Azure Portal för att skapa en ny Azure Kognitiv sökning-tjänstinstans som inte kan nås via Internet. Sedan konfigurerar du en virtuell Azure-dator i samma virtuella nätverk och använder den för att få åtkomst till Sök tjänsten via en privat slut punkt.

Privata slut punkter tillhandahålls av en [privat Azure-länk](../private-link/private-link-overview.md), som en separat tjänst. Mer information om kostnader finns på sidan med [priser](https://azure.microsoft.com/pricing/details/private-link/).

Du kan skapa en privat slut punkt i Azure Portal, enligt beskrivningen i den här artikeln. Du kan också använda [hanterings REST API version 2020-03-13](/rest/api/searchmanagement/), [Azure POWERSHELL](/powershell/module/az.search)eller [Azure CLI](/cli/azure/search).

> [!NOTE]
> När tjänstens slut punkt är privat är vissa Portal funktioner inaktiverade. Du kan visa och hantera information om service nivå, men index, indexerare och färdigheter-information är dold av säkerhets skäl. Som ett alternativ till portalen kan du använda [vs Code-tillägget](https://aka.ms/vscode-search) för att interagera med de olika komponenterna i tjänsten.

## <a name="why-use-a-private-endpoint-for-secure-access"></a>Varför ska jag använda en privat slut punkt för säker åtkomst?

[Privata slut punkter](../private-link/private-endpoint-overview.md) för Azure kognitiv sökning tillåta en klient i ett virtuellt nätverk att på ett säkert sätt få åtkomst till data i ett Sök index över en [privat länk](../private-link/private-link-overview.md). Den privata slut punkten använder en IP-adress från det [virtuella nätverkets adress utrymme](../virtual-network/private-ip-addresses.md) för Sök tjänsten. Nätverks trafiken mellan klienten och Sök tjänsten passerar över det virtuella nätverket och en privat länk i Microsoft stamnät nätverket, vilket eliminerar exponering från det offentliga Internet. För en lista över andra PaaS-tjänster som stöder privat länk, se [avsnittet tillgänglighet](../private-link/private-link-overview.md#availability) i produkt dokumentationen.

Med privata slut punkter för Sök tjänsten kan du:

- Blockera alla anslutningar på den offentliga slut punkten för Sök tjänsten.
- Öka säkerheten för det virtuella nätverket genom att göra det möjligt att blockera exfiltrering av data från det virtuella nätverket.
- Anslut säkert till din Sök tjänst från lokala nätverk som ansluter till det virtuella nätverket med hjälp av [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoute](../expressroute/expressroute-locations.md) med privat peering.

## <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

I det här avsnittet ska du skapa ett virtuellt nätverk och ett undernät som är värd för den virtuella datorn som ska användas för att få åtkomst till Sök tjänstens privata slut punkt.

1. På fliken Azure Portal start väljer du **skapa en resurs**  >  **nätverk**  >  **virtuellt nätverk**.

1. I **Skapa virtuellt nätverk** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Prenumeration | Välj din prenumeration|
    | Resursgrupp | Välj **Skapa ny**, ange *myResourceGroup* och välj sedan **OK** |
    | Name | Ange *MyVirtualNetwork* |
    | Region | Välj önskad region |
    |||

1. Lämna standardinställningarna för resten av inställningarna. Klicka på **Granska + skapa** och sedan på **skapa**

## <a name="create-a-search-service-with-a-private-endpoint"></a>Skapa en Sök tjänst med en privat slut punkt

I det här avsnittet ska du skapa en ny Azure Kognitiv sökning-tjänst med en privat slut punkt. 

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa en resurs**  >  **Web**  >  **Azure-kognitiv sökning**.

1. I **nya search service – grunderna** anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | **PROJEKT INFORMATION** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade det i föregående avsnitt.|
    | **INSTANS INFORMATION** |  |
    | URL | Ange ett unikt namn. |
    | Location | Välj önskad region. |
    | Prisnivå | Välj **pris nivå för ändring** och välj önskad tjänst nivå. (Stöds inte på den **kostnads fria** nivån. Måste vara **Basic** eller högre.) |
    |||
  
1. Välj **Nästa: skala**.

1. Lämna värdena som standard och välj **Nästa: nätverk**.

1. I **ny search service-nätverk** väljer du **privat** för **slut punkts anslutning (data)**.

1. I **ny search service-nätverk** väljer du **+ Lägg till** under **privat slut punkt**. 

1. I **skapa privat slut punkt** anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade det i föregående avsnitt.|
    | Location | Välj **USA, västra**.|
    | Name | Ange *myPrivateEndpoint*.  |
    | Målunderresurs | Lämna standard **searchService**. |
    | **NÄTVERK** |  |
    | Virtuellt nätverk  | Välj *MyVirtualNetwork* från resurs gruppen *myResourceGroup*. |
    | Undernät | Välj *undernät*. |
    | **PRIVAT DNS-INTEGRATION** |  |
    | Integrera med privat DNS-zon  | Låt standardinställningen vara **Ja**. |
    | Privat DNS-zon  | Lämna standardvärdet * * (New) privatelink.search.windows.net * *. |
    |||

1. Välj **OK**. 

1. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** där Azure verifierar din konfiguration. 

1. När du ser ett meddelande som anger att **valideringen har slutförts** klickar du på **Skapa**. 

1. När etableringen av den nya tjänsten har slutförts bläddrar du till den resurs som du nyss skapade.

1. Välj **nycklar** på menyn till vänster innehåll.

1. Kopiera den **primära administratörs nyckeln** för senare när du ansluter till tjänsten.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa en resurs**  >  **beräknings**  >  **virtuell dator**.

1. I **Skapa en virtuell dator – grunder** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **PROJEKT INFORMATION** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade det i föregående avsnitt.  |
    | **INSTANS INFORMATION** |  |
    | Namn på virtuell dator | Ange *myVm*. |
    | Region | Välj **USA, västra** eller vilken region du använder. |
    | Alternativ för tillgänglighet | Lämna standard **ingen redundans för infrastruktur krävs**. |
    | Bild | Välj **Windows Server 2019 Data Center**. |
    | Storlek | Lämna standard **ds1 v2** som standard. |
    | **ADMINISTRATÖRSKONTO** |  |
    | Användarnamn | Ange ett användar namn som du väljer. |
    | Lösenord | Ange ett valfritt lösenord. Lösen ordet måste vara minst 12 tecken långt och uppfylla de [definierade komplexitets kraven](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Bekräfta lösenord | Ange lösenordet igen. |
    | **REGLER FÖR INKOMMANDE PORTAR** |  |
    | Offentliga inkommande portar | Lämna standardvärdet **Tillåt valda portar**. |
    | Välj inkommande portar | Lämna standardvärdet för **RDP (3389)**. |
    | **SPARA PENGAR** |  |
    | Har du redan en Windows-licens? | Lämna standardvärdet **Nej**. |
    |||

1. Välj **Nästa: diskar**.

1. I **Skapa en virtuell dator – diskar** lämnar du standardinställningarna och väljer **Nästa: Nätverk**.

1. I **Skapa en virtuell dator – Nätverk** väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Virtuellt nätverk | Lämna standard **MyVirtualNetwork**.  |
    | Adressutrymme | Lämna standard **10.1.0.0/24**.|
    | Undernät | Lämna standard **under nätet (10.1.0.0/24)**.|
    | Offentlig IP-adress | Lämna standardvärdet **(New) myVm-IP**. |
    | Offentliga inkommande portar | Välj **Tillåt valda portar**. |
    | Välj inkommande portar | Välj **HTTP** och **RDP**.|
    ||

   > [!NOTE]
   > IPv4-adresser kan uttryckas i [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) -format. Kom ihåg att undvika det reserverade IP-intervallet för privata nätverk, enligt beskrivningen i [RFC 1918](https://tools.ietf.org/html/rfc1918):
   >
   > - `10.0.0.0 - 10.255.255.255  (10/8 prefix)`
   > - `172.16.0.0 - 172.31.255.255  (172.16/12 prefix)`
   > - `192.168.0.0 - 192.168.255.255 (192.168/16 prefix)`

1. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** där Azure verifierar din konfiguration.

1. När du ser ett meddelande som anger att **valideringen har slutförts** klickar du på **Skapa**. 

## <a name="connect-to-the-vm"></a>Anslut till VM:en

Ladda ned och Anslut till VM- *myVm* på följande sätt:

1. I portalens sökfältet anger du *myVm*.

1. Välj knappen **Anslut**. När du har valt knappen **Anslut** öppnas **Anslut till den virtuella datorn**.

1. Välj **Hämta RDP-fil**. Azure skapar en Remote Desktop Protocol-fil (*. RDP*) och laddar ned den till datorn.

1. Öppna den nedladdade RDP *-filen.

    1. Välj **Anslut** om du uppmanas att göra det.

    1. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn.

        > [!NOTE]
        > Du kan behöva välja **fler alternativ**  >  **Använd ett annat konto** för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn.

1. Välj **OK**.

1. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** om du får en certifikatvarning.

1. När virtuella datorns skrivbord visas kan du minimera det att gå tillbaka till din lokala dator.  

## <a name="test-connections"></a>Test anslutningar

I det här avsnittet ska du verifiera privat nätverks åtkomst till Sök tjänsten och ansluta privat till med hjälp av den privata slut punkten.

När Sök tjänstens slut punkt är privat är vissa Portal funktioner inaktiverade. Du kan visa och hantera inställningar för service nivå, men Portal åtkomst till index data och andra komponenter i tjänsten, till exempel index, indexerare och färdigheter definitioner, är begränsad av säkerhets skäl.

1. Öppna PowerShell i fjärr skrivbordet för *myVM*.

1. Ange ' nslookup [search service Name]. search. Windows. net '

    Du får ett meddelande som liknar detta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```

1. Från den virtuella datorn ansluter du till Sök tjänsten och skapar ett index. Du kan följa den här [snabb](search-get-started-rest.md) starten för att skapa ett nytt Sök index i tjänsten med hjälp av REST API. Om du ställer in begär Anden från ett webb-API-testverktyg krävs Sök tjänstens slut punkt (https://[Sök tjänstens namn]. search. Windows. net) och den Admin-API-nyckel som du kopierade i föregående steg.

1. Att slutföra snabb starten från den virtuella datorn är din bekräftelse på att tjänsten är fullt fungerande.

1. Stäng fjärr skrivbords anslutningen till *myVM*. 

1. Du kan kontrol lera att tjänsten inte är tillgänglig på en offentlig slut punkt genom att öppna Postman på den lokala arbets stationen och försöka utföra de första uppgifterna i snabb starten. Om du får ett fel meddelande om att fjärrservern inte finns har du konfigurerat en privat slut punkt för Sök tjänsten.

## <a name="clean-up-resources"></a>Rensa resurser 
När du är klar med den privata slut punkten, Sök tjänsten och den virtuella datorn tar du bort resurs gruppen och alla resurser den innehåller:
1. Skriv *myResourceGroup*   i sökrutan längst upp i portalen och välj   *myResourceGroup*   från Sök resultaten. 
1. Välj **Ta bort resursgrupp**. 
1. Ange  *myResourceGroup*   för **Skriv resurs gruppens namn** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du skapat en virtuell dator i ett virtuellt nätverk och en Sök tjänst med en privat slut punkt. Du har anslutit till den virtuella datorn från Internet och kommunicerat på ett säkert sätt till Sök tjänsten med hjälp av en privat länk. Mer information om privata slut punkter finns i [Vad är Azures privata slut punkt?](../private-link/private-endpoint-overview.md).
