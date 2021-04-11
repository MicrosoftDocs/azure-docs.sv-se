---
title: Självstudie – övervaka nätverkskommunikation med hjälp av Azure Portal
description: I den här självstudien får du lära dig hur du övervakar nätverkskommunikation mellan två virtuella datorer med Azure Network Watchers funktion för anslutnings övervakning.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.custom: mvc
ms.openlocfilehash: 4f9f3e2af9abc054d3b1d5ce2bd1f1b27f7bf958
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066049"
---
# <a name="tutorial-monitor-network-communication-between-two-virtual-machines-using-the-azure-portal"></a>Självstudie: Övervaka nätverkskommunikationen mellan två virtuella datorer i Azure Portal

> [!NOTE]
> Den här självstudien beskriver anslutnings övervakaren (klassisk). Prova den nya och förbättrade [anslutnings övervakaren](connection-monitor-overview.md) för att få förbättrad anslutnings övervakning

> [!IMPORTANT]
> Från och med 1 juli 2021 kommer du inte att kunna lägga till nya anslutnings övervakare i anslutnings övervakaren (klassisk), men du kan fortsätta att använda befintliga anslutnings Övervakare som skapats före 1 juli 2021. Om du vill minimera avbrott i tjänsten för dina aktuella arbets belastningar [migrerar du från anslutnings övervakaren (klassisk) till den nya anslutnings övervakaren](migrate-to-connection-monitor-from-connection-monitor-classic.md) i Azure Network Watcher före den 29 februari 2024.

Det kan vara mycket viktigt att kommunikationen mellan en virtuell dator (VM) och en slutpunkt, som en annan virtuell dator, fungerar ordentligt. Ibland görs konfigurationsändringar som kan bryta kommunikationen. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa två virtuella datorer
> * Övervaka kommunikationen mellan virtuella datorer med funktionen för anslutningsövervakning i Network Watcher
> * Generera aviseringar om mått från anslutningsövervakare
> * Diagnostisera ett kommunikationsproblem mellan två virtuella datorer och lär dig hur du kan lösa det

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.



## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-vms"></a>Skapa VM:ar

Skapa två virtuella datorer.

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

1. Klicka på **+ Skapa en resurs** längst upp till vänster på Azure Portal.
2. Välj **Compute** och sedan ett operativsystem. I den här självstudien används **Windows Server 2016 Datacenter**.
3. Ange eller Välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **OK**:

    |Inställning|Värde|
    |---|---|
    |Namn|myVm1|
    |Användarnamn| Ange ett valfritt användarnamn.|
    |Lösenord| Ange ett valfritt lösenord. Lösen ordet måste vara minst 12 tecken långt och uppfylla de [definierade komplexitets kraven](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Prenumeration| Välj din prenumeration.|
    |Resursgrupp| Välj **Skapa ny** och skriv **myResourceGroup**.|
    |Location| Välj **USA, östra**|

4. Välj en storlek för den virtuella datorn och sedan **Välj**.
5. Under **Inställningar** väljer du **Tillägg**. Välj **Lägg till tillägg** och välj **Network Watcher Agent for Windows**, så som visas i följande bild:

    ![Network Watcher-agenttillägget](./media/connection-monitor/nw-agent-extension.png)

6. Under **Network Watcher Agent for Windows** väljer du **Skapa**, under **Installera tillägg** väljer du **OK** och under **Tillägg** väljer du **OK**.
7. Behåll standardinställningarna för återstående **inställningar** och välj **OK**.
8. Under **Skapa** i **sammanfattningen** väljer du **Skapa** för att starta VM-distributionen.

### <a name="create-the-second-vm"></a>Skapa den andra virtuella datorn

Utför stegen i [Skapa den första virtuella datorn](#create-the-first-vm) igen med följande ändringar:

|Steg|Inställning|Värde|
|---|---|---|
| 1 | Välj en version av **Ubuntu Server** |                                                                         |
| 3 | Name                                  | myVm2                                                                   |
| 3 | Autentiseringstyp                   | Klistra in den offentliga SSH-nyckeln eller välj **Lösenord** och ange ett lösenord. |
| 3 | Resursgrupp                        | Välj **Använd befintlig** och sedan **myResourceGroup**.                 |
| 6 | Tillägg                            | **Network Watcher agent för Linux**                                             |

Det tar några minuter att distribuera den virtuella datorn. Vänta tills distributionen av den virtuella datorn är klar innan du fortsätter med nästa steg.

## <a name="create-a-connection-monitor"></a>Skapa en anslutningsövervakare

Skapa en anslutningsövervakare för övervakning av kommunikationen via TCP-port 22 från *myVm1* till *myVm2*.

1. Välj **Alla tjänster** till vänster på portalen.
2. Börja skriva *network watcher* i **filterrutan**. När **Network Watcher** visas i sökresultatet väljer du posten.
3. Under **ÖVERVAKNING** väljer du **Anslutningsövervakare**.
4. Välj **+ Lägg till**.
5. Ange eller välj information för anslutningen du vill övervaka och välj sedan **Lägg till**. I exemplet som visas i följande bild övervakas anslutningen från den virtuella datorn *myVm1* till den virtuella datorn *myVm2* VM via port 22:

    | Inställning                  | Värde               |
    | ---------                | ---------           |
    | Namn                     | myVm1-myVm2(22)     |
    | Källa                   |                     |
    | Virtuell dator          | myVm1               |
    | Mål              |                     |
    | Välj en virtuell dator |                     |
    | Virtuell dator          | myVm2               |
    | Port                     | 22                  |

    ![Lägg till anslutningsövervakare](./media/connection-monitor/add-connection-monitor.png)

## <a name="view-a-connection-monitor"></a>Visa en anslutningsövervakare

1. Utför steg 1–3 i [Skapa en anslutningsövervakare](#create-a-connection-monitor) om du vill visa en anslutningsövervakare. Du kan se en lista med befintliga anslutningsövervakare, så som visas i följande bild:

    ![Anslutningsövervakare](./media/connection-monitor/connection-monitors.png)

2. Välj övervakaren med namnet **myVm1-myVm2(22)** om du vill visa information om den, så som visas i föregående bild:

    ![Information om övervakare](./media/connection-monitor/vm-monitor.png)

    Notera följande information:

    | Objekt                     | Värde                      | Information                                                     |
    | ---------                | ---------                  |--------                                                     |
    | Status                   | Nåbar                  | Visar om slutpunkten kan nås eller inte.|
    | GENOMSN. TIDSFÖRDRÖJNING          | Visar anslutningens tidsfördröjning i millisekunder. Anslutningsövervakaren söker av anslutningen var 60:e sekund så att du kan se svarstiderna över tid.                                         |
    | Hopp                     | Anslutningsövervakaren visar hoppen mellan de två slutpunkterna. I det här exemplet ligger anslutningen mellan de två virtuella datorerna i samma virtuella nätverk, så det sker bara ett hopp till IP-adressen 10.0.0.5. Om något befintligt system eller en egen väg dirigerar trafiken mellan de två virtuella datorerna via en VPN-gateway eller en virtuell nätverksenhet visas fler hopp.                                                                                                                         |
    | STATUS                   | En grön bockmarkering vid respektive slutpunkt anger att tillståndet är felfritt.    ||

## <a name="generate-alerts"></a>Generera aviseringar

Aviseringar skapas av aviseringsregler i Azure Monitor och kan automatiskt köra sparade frågor eller anpassade loggsökningar med jämna mellanrum. En genererad avisering kan automatiskt köra en eller flera åtgärder, till exempel att meddela någon eller starta en annan process. När du anger en aviseringsregel avgör den resurs som du väljer som mål listan över tillgängliga mått som du kan använda för att generera aviseringar.

1. I Azure-portalen väljer du **övervakningstjänsten** och sedan **Aviseringar** > **Ny aviseringsregel**.
2. Klicka på **Välj mål** och välj sedan de resurser som du vill ha som mål. Välj **Prenumeration** och ange **Resurstyp** till att filtrera ned till den anslutningsövervakare som du vill använda.

    ![aviseringsskärm med mål som valts](./media/connection-monitor/set-alert-rule.png)
1. När du har valt en resurs som ska vara mål väljer du **Lägg till villkor**. Network Watcher har [mått som du kan skapa aviseringar om](../azure-monitor/alerts/alerts-metric-near-real-time.md#metrics-and-dimensions-supported). Ange **Tillgängliga signaler** till måtten ProbesFailedPercent och AverageRoundtripMs:

    ![aviseringssida med signaler som valts](./media/connection-monitor/set-alert-signals.png)
1. Fyll i information om aviseringar såsom namn på aviseringsregel, beskrivning och allvarlighetsgrad. Du kan även lägga till en åtgärdsgrupp till aviseringen för att automatisera och anpassa aviseringssvaret.

## <a name="view-a-problem"></a>Visa ett problem

Som standard tillåter Azure kommunikation via alla portar mellan virtuella datorer i samma virtuella nätverk. Senare kanske du eller någon annan i organisationen åsidosätter standardreglerna i Azure och därmed orsakar ett kommunikationsfel. Gör så här för att skapa ett kommunikationsproblem och visa sedan anslutningsövervakaren igen:

1. Skriv *myResourceGroup* i sökrutan högst upp i portalen. När du ser **myResourceGroup** bland sökresultaten markerar du posten.
2. Välj nätverkssäkerhetsgruppen **myVm2-nsg**.
3. Välj **Ingående säkerhetsregler** och sedan **Lägg till**, så som visas i följande bild:

    ![Ingående säkerhetsregler](./media/connection-monitor/inbound-security-rules.png)

4. Standardregeln som tillåter kommunikation mellan alla virtuella datorer i ett virtuellt nätverk heter **AllowVnetInBound**. Skapa en regel med högre prioritet (lägre nummer) än regeln **AllowVnetInBound** som nekar inkommande kommunikation via port 22. Välj eller ange följande information, acceptera standardvärdena för resten av inställningarna och välj sedan **Lägg till**:

    | Inställning                 | Värde          |
    | ---                     | ---            |
    | Målportintervall | 22             |
    | Åtgärd                  | Neka           |
    | Prioritet                | 100            |
    | Namn                    | DenySshInbound |

5. Eftersom anslutningsövervakaren gör avsökningar var 60:e sekund ska du vänta några minuter. Välj **Network Watcher** och **Anslutningsövervakare** till vänster i portalen, och välj sedan övervakaren **myVm1-myVm2(22)** igen. Nu blir resultatet ett annat, så som visas i följande bild:

    ![Övervaka information om fel](./media/connection-monitor/vm-monitor-fault.png)

    Du ser ett rött utropstecken i statuskolumnen för nätverksgränssnittet **myvm2529**.

6. Välj 10.0.0.5 om du vill se varför statusen har ändrats. Anslutningsövervakaren anger att orsaken till kommunikationsfelet är: *Traffic blocked due to the following network security group rule: UserRule_DenySshInbound* (Trafiken blockeras på grund av följande regel för nätverkssäkerhetsgruppen).

    Om du inte hade vetat att någon implementerat den säkerhetsregel du skapade i steg 4 så skulle du se att det är den här regeln som orsakar kommunikationsproblemet i anslutningsövervakaren. Då kan du ändra, åsidosätta eller ta bort regeln för att återställa kommunikationen mellan de virtuella datorerna.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, skalningsuppsättningen och alla resurser som den innehåller:

1. Skriv *myResourceGroup* i **sökrutan** överst i portalen. När du ser **myResourceGroup** i sökresultatet väljer du den.
2. Välj **Ta bort resursgrupp**.
3. Skriv *myResourceGroup* i **SKRIV RESURSGRUPPSNAMNET:** och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du övervakar en anslutning mellan två virtuella datorer. Du har sett att en regel för en nätverkssäkerhetsgrupp förhindrade kommunikationen till en virtuell dator. Om du vill se alla möjliga svar som en anslutningsövervakare kan returnera kan du läsa om [svarstyper](network-watcher-connectivity-overview.md#response). Du kan också övervaka en anslutning mellan en virtuell dator, ett fullständigt kvalificerat domännamn, en URI eller en IP-adress.

Någon gång kanske du ser att resurser i ett virtuellt nätverk inte kan kommunicera med resurser i andra nätverk som är anslutna via en virtuell nätverksgateway i Azure. I nästa självstudiekurs får du lära dig mer om att diagnostisera problem med en virtuell nätverksgateway.

> [!div class="nextstepaction"]
> [Diagnostisera kommunikationsproblem mellan nätverk](diagnose-communication-problem-between-networks.md)
