---
title: Självstudie – diagnostisera kommunikations problem mellan nätverk med hjälp av Azure Portal
titleSuffix: Azure Network Watcher
description: I den här självstudien får du lära dig hur du diagnostiserar ett kommunikations problem mellan ett virtuellt Azure-nätverk som är anslutet till ett lokalt eller annat virtuellt nätverk, via en virtuell Azure-nätverksgateway med hjälp av Network Watcher funktion för VPN-diagnostik.
services: network-watcher
documentationcenter: na
author: damendo
Customer intent: I need to determine why resources in a virtual network can't communicate with resources in a different network.
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.custom: mvc
ms.openlocfilehash: 1a556ea66bb54f994349ce9ba4d12fb486827edf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98013306"
---
# <a name="tutorial-diagnose-a-communication-problem-between-networks-using-the-azure-portal"></a>Självstudier: Diagnostisera kommunikationsproblem mellan nätverk med hjälp av Azure Portal

En virtuell nätverksgateway ansluter ett virtuellt Azure-nätverk till ett lokalt nätverk eller till ett annat virtuellt nätverk. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Diagnostisera problem med en virtuell nätverksgateway med funktionen VPN-diagnostik i Network Watcher
> * Diagnostisera ett problem med en gatewayanslutning
> * Lösa ett problem med en gateway

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

Du måste ha en befintlig, aktiv VPN-gateway för att kunna använda VPN-diagnostik. Om du inte har en befintlig VPN-gateway att diagnostisera kan du distribuera en med hjälp av ett [PowerShell-skript](../vpn-gateway/scripts/vpn-gateway-sample-site-to-site-powershell.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Du kan köra PowerShell-skriptet från:
- **En lokal PowerShell-installation**: skriptet kräver Azure PowerShell- `Az` modulen. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera modulen läser du avsnittet [Installera Azure PowerShell](/powershell/azure/install-Az-ps). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.
- **Azure Cloud Shell**: Den senaste versionen av PowerShell är installerad och konfigurerad i [Azure Cloud Shell](https://shell.azure.com/powershell) och loggar in dig automatiskt i Azure.

Det tar ungefär en timme att skapa en VPN-gateway med skriptet. I de återstående stegen förutsätter vi att den gateway som du diagnostiserar är den som distribuerats med det här skriptet. Om du diagnostiserar en egen befintlig gateway i stället, kan resultatet variera.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="enable-network-watcher"></a>Aktivera Network Watcher

Om du redan har en aktiv nätverksbevakare i regionen USA, östra går du vidare till [Diagnostisera en gateway](#diagnose-a-gateway).

1. Välj **Alla tjänster** i portalen. I **filterrutan** anger du *Network Watcher*. När **Network Watcher** visas i resultatet markerar du det.
2. Välj **Regioner** för att expandera avsnittet och välj sedan **...** till höger om **USA, östra**, som du ser i följande bild:

    ![Aktivera Network Watcher](./media/diagnose-communication-problem-between-networks/enable-network-watcher.png)

3. Välj **Aktivera Network Watcher**.

## <a name="diagnose-a-gateway"></a>Diagnostisera en gateway

1. Välj **Alla tjänster** till vänster på portalen.
2. Börja skriva *network watcher* i **filterrutan**. När **Network Watcher** visas i sökresultatet väljer du posten.
3. Välj **VPN-diagnostik** under **DIAGNOSTISKA VERKTYG FÖR NÄTVERK**.
4. Välj **Lagringskonto** och välj sedan det lagringskonto som du vill skriva diagnostikinformation till.
5. Välj det lagringskonto som du vill använda i listan med **lagringskonton**. Om du inte har ett befintligt lagringskonto väljer du **+ Lagringskonto**, anger eller väljer nödvändig information och skapar sedan ett konto genom att välja **Skapa**. Om du har skapat en VPN-gateway med hjälp av skriptet i avsnittet [Krav](#prerequisites) kanske du vill skapa lagringskontot i samma resursgrupp, *TestRG1*, som gatewayen.
6. Välj den container som du vill använda i listan med **containrar** och välj sedan **Välj**. Om du inte har några containrar väljer du **+ Container**, anger ett namn för containern och väljer sedan **OK**.
7. Välj en gateway och välj sedan **Starta felsökning**. Som du ser i följande bild körs testet mot en gateway med namnet **Vnet1GW**:

    ![VPN-diagnostik](./media/diagnose-communication-problem-between-networks/vpn-diagnostics.png)

8. När testet körs visas **Körs** i kolumnen **FELSÖKNINGSSTATUS** där **Har inte startats** visas, som du ser i föregående bild. Testet kan ta flera minuter.
9. Visa status för ett slutfört test. Följande bild visar statusresultatet för ett slutfört diagnostiktest:

    ![Skärm bild som visar status resultatet för ett diagnostiktest, som är skadat i det här exemplet, inklusive Sammanfattning och detaljer.](./media/diagnose-communication-problem-between-networks/status.png)

    Som du ser är **FELSÖKNINGSSTATUS****Ej felfri**. Du kan också se en **sammanfattning** och **information** om problemet på fliken **Status**.
10. När du väljer fliken **Åtgärd** visar VPN-diagnostik ytterligare information. I exemplet som visas i följande bild meddelar VPN-diagnostik att du bör kontrollera hälsotillståndet för varje anslutning:

    ![Skärm bild som visar fliken åtgärd, som ger dig ytterligare information.](./media/diagnose-communication-problem-between-networks/action.png)

## <a name="diagnose-a-gateway-connection"></a>Diagnostisera en gatewayanslutning

En gateway är ansluten till andra nätverk via en gatewayanslutning. Både gatewayen och gatewayanslutningarna måste vara felfria för att kommunikationen mellan ett virtuellt nätverk och ett anslutet nätverk ska lyckas.

1. Utför steg 7 i [Diagnostisera en gateway](#diagnose-a-gateway) igen och välj en anslutning den här gången. I följande exempel testas en anslutning med namnet **VNet1toSite1**:

    ![Skärm bild som visar hur du startar fel sökning för en vald anslutning.](./media/diagnose-communication-problem-between-networks/connection.png)

    Testet körs i flera minuter.
2. När anslutningstestet är klart returneras resultat liknande dem i följande bilder på flikarna **Status** och **Åtgärd**:

    ![Anslutningsstatus](./media/diagnose-communication-problem-between-networks/connection-status.png)

    ![Anslutningsåtgärd](./media/diagnose-communication-problem-between-networks/connection-action.png)

    VPN-diagnostik beskriver vad som är fel på fliken **Status** och visar flera potentiella orsaker till problemet på fliken **Åtgärd**.

    Om den gateway som du testade var den som distribuerades med [skriptet](../vpn-gateway/scripts/vpn-gateway-sample-site-to-site-powershell.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) i avsnittet [Krav](#prerequisites), så är problemet som anges på fliken **Status** och de första två objekten på fliken **Åtgärder** precis vad problemet är. Skriptet konfigurerar en platshållar-IP-adress, 23.99.221.164, för den lokala VPN-gatewayenheten.

    För att lösa problemet måste du kontrollera att din lokala VPN-gateway är [korrekt konfigurerad](../vpn-gateway/vpn-gateway-about-vpn-devices.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) och ändra IP-adressen som konfigurerats av skriptet för den lokala nätverksgatewayen till den riktiga offentliga adressen för din lokala VPN-gateway.

## <a name="clean-up-resources"></a>Rensa resurser

Om du skapade en VPN-gateway med hjälp av skriptet i avsnittet [Krav](#prerequisites) endast i syfte att slutföra den här självstudiekursen, och du inte längre behöver den, tar du bort resursgruppen och alla resurser som den innehåller:

1. Ange *TestRG1* i rutan **Sök** längst upp på portalen. När du ser **TestRG1** i sökresultatet väljer du posten.
2. Välj **Ta bort resursgrupp**.
3. Skriv *TestRG1* för **TYPE THE RESOURCE GROUP NAME:** (Skriv resursgruppens namn) och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig hur du diagnostiserar problem med en virtuell nätverksgateway. Kanske vill du logga nätverkskommunikation till och från en virtuell dator, så att du kan granska loggen och se om det finns avvikelser. Information om hur du gör det finns i nästa självstudiekurs.

> [!div class="nextstepaction"]
> [Logga nätverkstrafik till och från en virtuell dator](network-watcher-nsg-flow-logging-portal.md)
