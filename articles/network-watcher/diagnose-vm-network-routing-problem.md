---
title: 'Självstudie: diagnostisera ett problem med Routning av VM-nätverk – Azure Portal'
titleSuffix: Azure Network Watcher
description: I den här självstudien får du lära dig hur du diagnostiserar problem med nätverksroutning för virtuella datorer med funktionen för nästa hopp i Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: damendo
ms.custom: mvc
ms.openlocfilehash: bfcf9568f37a26f234a417a86eebf1b0bde67ab0
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98014509"
---
# <a name="tutorial-diagnose-a-virtual-machine-network-routing-problem-using-the-azure-portal"></a>Självstudie: Diagnostisera problem med nätverksroutning i Azure Portal

När du distribuerar en virtuell dator (VM) skapas flera standardvägar i Azure för den. Du kan skapa egna vägar som ersätter Azures standardvägar. Ibland kan en egen väg leda till att en virtuell dator inte kan kommunicera med andra resurser. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en virtuell dator
> * Testa kommunikationen till en URL med funktionen för nästa hopp i Network Watcher
> * Testa kommunikationen till en IP-adress
> * Diagnostisera ett routningsproblem och lär dig hur du kan lösa det

Om du vill kan du diagnostisera problem med nätverksroutning via [Azures CLI](diagnose-vm-network-routing-problem-cli.md) eller [Azure PowerShell](diagnose-vm-network-routing-problem-powershell.md).

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-vm"></a>Skapa en virtuell dator

1. Klicka på **+ Skapa en resurs** längst upp till vänster på Azure Portal.
2. Välj **Compute** och välj sedan **Windows Server 2016 Datacenter** eller **Ubuntu Server 17.10 VM**.
3. Ange eller Välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **OK**:

    |Inställning|Värde|
    |---|---|
    |Namn|myVm|
    |Användarnamn| Ange ett valfritt användarnamn.|
    |Lösenord| Ange ett valfritt lösenord. Lösen ordet måste vara minst 12 tecken långt och uppfylla de [definierade komplexitets kraven](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Prenumeration| Välj din prenumeration.|
    |Resursgrupp| Välj **Skapa ny** och skriv **myResourceGroup**.|
    |Plats| Välj **USA, östra**|

4. Välj en storlek för den virtuella datorn och sedan **Välj**.
5. Acceptera standardinställningarna under **Inställningar** och välj **OK**.
6. Under **Skapa** i **sammanfattningen** väljer du **Skapa** för att starta VM-distributionen. Det tar några minuter att distribuera den virtuella datorn. Vänta tills distributionen av den virtuella datorn är klar innan du fortsätter med nästa steg.

## <a name="test-network-communication"></a>Testa nätverkskommunikationen

Om du vill testa nätverkskommunikationen med Network Watcher måste du först aktivera en nätverksbevakare i minst en Azure-region. Därefter använder du funktionen för nästa hopp i Network Watcher till att testa kommunikationen.

### <a name="enable-network-watcher"></a>Aktivera nätverksbevakare

Om du redan har aktiverat en nätverksbevakare i minst en region går du vidare till [Använda funktionen för nästa hopp](#use-next-hop).

1. Välj **Alla tjänster** i portalen. I **filterrutan** anger du *Network Watcher*. När **Network Watcher** visas i resultatet markerar du det.
2. Välj **Regioner** för att expandera avsnittet och välj sedan **...** till höger om **USA, östra**, som du ser i följande bild:

    ![Aktivera Network Watcher](./media/diagnose-vm-network-traffic-filtering-problem/enable-network-watcher.png)

3. Välj **Aktivera Network Watcher**.

### <a name="use-next-hop"></a>Använda funktionen för nästa hopp

Azure skapar automatiskt vägar till olika standardmål. Du kan skapa egna vägar som ersätter standardvägarna. Ibland kan egna vägar göra att kommunikationen misslyckas. Använd funktionen för nästa hopp i Network Watcher till att avgöra vilken väg Azure använder när trafiken dirigeras.

1. Välj **Nästa hopp** i Azure Portal, under **Network Watcher**.
2. Välj din prenumeration, ange eller välj följande värden och välj sedan **Nästa hopp**, som du ser i bilden nedan:

    |Inställning                  |Värde                                                   |
    |---------                |---------                                               |
    | Resursgrupp          | Välj myResourceGroup                                 |
    | Virtuell dator         | Välj myVm                                            |
    | Nätverksgränssnitt       | myvm – namnet på ditt nätverksgränssnitt kan vara ett annat.   |
    | Källans IP-adress       | 10.0.0.4                                               |
    | Mål-IP-adress  | 13.107.21.200 – en av adresserna för <www.bing.com>. |

    ![Nästa hopp](./media/diagnose-vm-network-routing-problem/next-hop.png)

    Efter några sekunder ser du i resultatet att nästa hopptyp är **Internet** och att **Id för routningstabell** är **System Route** (Systemväg). Resultatet visar att det finns en giltig systemväg till målet.

3. Ändra **Mål-IP-adress** till *172.31.0.100* och välj **Nästa hopp** igen. I resultatet som returneras ser du att **Ingen** är **Nästa hopptyp** och att **Id för routningstabell** också är **System Route** (Systemväg). Resultatet visar att det visserligen finns en giltig systemväg till målet, men att det inte finns något nästa hopp för att dirigera trafiken till målet.

## <a name="view-details-of-a-route"></a>Visa information om en väg

1. Du kan analysera routningen vidare genom att granska gällande vägar för nätverksgränssnittet. Skriv *myvm* i sökrutan överst i portalen (eller namnet på det nätverksgränssnitt du kontrollerar). Välj **myvm** bland sökresultaten.
2. Välj **Gällande routningar** under **SUPPORT + FELSÖKNING**, som du ser i följande bild:

    ![Gällande routningar](./media/diagnose-vm-network-routing-problem/effective-routes.png)

    När du körde testet med 13.107.21.200 i [Använda funktionen för nästa hopp](#use-next-hop) användes routningen med adressprefixet 0.0.0.0/0 till att dirigera trafik till adressen, eftersom ingen annan väg innehåller adressen. Som standard dirigeras alla adresser som inte anges inom adressprefixet till en annan väg till internet.

    När du körde testet med 172.31.0.100 såg du däremot i resultatet att det inte fanns någon typ av nästa hopp. Som du ser i föregående bild är dock **Nästa hopptyp****Ingen**, även om det finns en standardväg till prefixet 172.16.0.0/12 som innehåller adressen 172.31.0.100. Azure skapar en standardväg till 172.16.0.0/12 men anger inte en nästa hopptyp förrän det finns någon anledning till det. Om du till exempel lägger till adressintervallet 172.16.0.0/12 i adressutrymmet för det virtuella nätverket så ändrar Azure **Nästa hopptyp** till **Virtuellt nätverk** för vägen. Då skulle **Virtuellt nätverk** visas som **Nästa hopptyp**.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, skalningsuppsättningen och alla resurser som den innehåller:

1. Skriv *myResourceGroup* i **sökrutan** överst i portalen. När du ser **myResourceGroup** i sökresultatet väljer du den.
2. Välj **Ta bort resursgrupp**.
3. Skriv *myResourceGroup* i **SKRIV RESURSGRUPPSNAMNET:** och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en virtuell dator och diagnostiserat nätverksroutningen från den virtuella datorn. Du har lärt dig att Azure skapar flera standardvägar och testat routning till två olika mål. Läs mer om [routning i Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) och hur du [skapar egna vägar](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

För utgående anslutningar från virtuella datorer kan du även bestämma svarstiden, tillåten och nekad nätverkstrafik mellan den virtuella datorn och en slutpunkt samt vilken väg som används till en slutpunkt med funktionen för [felsökning av anslutningar](network-watcher-connectivity-portal.md) i Network Watcher. Lär dig hur du kan övervaka kommunikationen mellan en virtuell dator och en slutpunkt, som en IP-adress eller URL, med funktionen för anslutningsövervakning i Network Watcher.

> [!div class="nextstepaction"]
> [Övervaka en nätverksanslutning](connection-monitor.md)