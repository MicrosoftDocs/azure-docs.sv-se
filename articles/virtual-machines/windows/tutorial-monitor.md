---
title: Självstudie – övervaka virtuella Windows-datorer i Azure
description: I den här självstudien får du lära dig hur du övervakar prestanda och identifierade program komponenter som körs på dina virtuella Windows-datorer.
author: mgoedtel
manager: carmonm
ms.service: virtual-machines-windows
ms.subservice: monitoring
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 09/27/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 3e7d93d7a1d0e804f340a7ce785ccc1165e93ee5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570102"
---
# <a name="tutorial-monitor-a-windows-virtual-machine-in-azure"></a>Självstudie: övervaka en virtuell Windows-dator i Azure

Azure Monitoring använder agenter för att samla in start-och prestanda data från virtuella Azure-datorer, lagra data i Azure Storage och göra dem tillgängliga via portalen, Azure PowerShell-modulen och Azure CLI. Avancerad övervakning levereras med Azure Monitor for VMs genom att samla in prestanda mått, identifiera program komponenter som är installerade på den virtuella datorn och innehåller prestanda diagram och beroende karta.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Aktivera startdiagnostik på en virtuell dator
> * Visa startdiagnostik
> * Visa statistik för en virtuell värddator
> * Aktivera Azure Monitor for VMs
> * Visa prestanda mått för virtuella datorer
> * Skapa en avisering

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

För att konfigurera övervaknings- och uppdateringshantering i Azure i den här självstudiekursen behöver du en virtuell Windows-dator i Azure. Ange först ett administratörsanvändarnamn och lösenord för den virtuella datorn med [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Skapa nu den virtuella datorn med hjälp av [New-AzVM](/powershell/module/az.compute/new-azvm). I följande exempel skapas en virtuell dator med namnet *myVM* på platsen *EastUS* (Östra USA). Resursgruppen *myResourceGroupMonitorMonitor* och ytterligare nätverksresurser som behövs skapas om de inte redan finns:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Det tar några minuter att skapa resurserna och den virtuella datorn.

## <a name="view-boot-diagnostics"></a>Visa startdiagnostik

När virtuella Windows-datorer startar samlar startdiagnostikagenten in skärmutdata som kan användas i felsökningssyfte. Den här funktionen är aktiverad som standard. De fångade skärmarna lagras i ett Azure Storage-konto, som också skapas som standard.

Du kan hämta startdiagnostikdata med kommandot [Get-AzureRmVMBootDiagnosticsData](/powershell/module/az.compute/get-azvmbootdiagnosticsdata). I följande exempel laddas startdiagnostik ned till roten på enhet *c:\*.

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName "myResourceGroupMonitor" -Name "myVM" -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Visa värdmått

En virtuell Windows-dator har en dedikerad virtuell värddator i Azure som den interagerar med. Mått samlas in automatiskt för värden och kan visas på Azure Portal.

1. I Azure Portal: Klicka på **Resource Groups**, välj **myResourceGroupMonitor** och välj sedan **myVM** i resurslistan.
2. Om du vill visa den virtuella värddatorns status klickar du på **Mått** på bladet Virtuell dator och väljer något av värdmåtten under **Tillgängliga mått**.

    ![Visa värdmått](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>Aktivera Avancerad övervakning

Så här aktiverar du övervakning av den virtuella Azure-datorn med Azure Monitor for VMs:

1. I Azure Portal: Klicka på **Resource Groups**, välj **myResourceGroupMonitor** och välj sedan **myVM** i resurslistan.

2. På sidan virtuell dator i avsnittet **övervakning** väljer du **insikter (för hands version)**.

3. Välj **Testa nu** på sidan **Insights (för hands version)** .

    ![Aktivera Azure Monitor for VMs för en virtuell dator](../../azure-monitor/vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png)

4. Om du har en befintlig Log Analytics arbets yta i samma prenumeration på sidan **Azure Monitor Insights-onboarding** väljer du den i list rutan.  

    I listan förväljs standard arbets ytan och den plats där den virtuella datorn distribueras i prenumerationen. 

    >[!NOTE]
    >Om du vill skapa en ny Log Analytics-arbetsyta för att lagra övervaknings data från den virtuella datorn, se [skapa en Log Analytics arbets yta](../../azure-monitor/logs/quick-create-workspace.md). Arbets ytan måste tillhöra en av de [regioner som stöds](../../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).

När du har aktiverat övervakning kan du behöva vänta flera minuter innan du kan visa prestanda måtten för den virtuella datorn.

![Aktivera Azure Monitor for VMs övervakning av distributions bearbetning](../../azure-monitor/vm/media/vminsights-enable-portal/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>Visa prestanda mått för virtuella datorer

Azure Monitor for VMs innehåller en uppsättning prestanda diagram som riktar sig mot flera nyckeltal (KPI: er) för att hjälpa dig att avgöra hur väl en virtuell dator fungerar. Utför följande steg för att komma åt från den virtuella datorn.

1. I Azure Portal: Klicka på **Resource Groups**, välj **myResourceGroupMonitor** och välj sedan **myVM** i resurslistan.

2. På sidan virtuell dator i avsnittet **övervakning** väljer du **insikter (för hands version)**.

3. Välj fliken **Prestanda**.

På den här sidan ingår inte bara prestanda användnings diagram, utan även en tabell som visar för varje logisk disk som identifieras, dess kapacitet, användning och total genomsnitt per mått.

## <a name="create-alerts"></a>Skapa aviseringar

Du kan skapa aviseringar baserat på specifika prestandamått. Aviseringar kan till exempel användas för att informera dig om när den genomsnittliga CPU-användning överskrider ett visst tröskelvärde eller när det lediga diskutrymmet understiger ett visst värde. Aviseringar kan visas i Azure Portal eller skickas via e-post. Du kan också utlösa Azure Automation-runbooks eller Azure Logic Apps när aviseringar skapas.

I följande exempel skapas en avisering för genomsnittlig CPU-användning.

1. I Azure Portal: Klicka på **Resource Groups**, välj **myResourceGroupMonitor** och välj sedan **myVM** i resurslistan.

2. Klicka på **Aviseringsregler** på bladet för den virtuella datorn och klicka sedan på **Lägg till metrisk varning** längst upp på aviseringsbladet.

3. Ange ett **namn** för aviseringen, till exempel *myAlertRule*

4. Om du vill utlösa en avisering när CPU-procenten har varit högre än 1,0 i fem minuter lämnar du alla andra standardinställningar valda.

5. Du kan också markera kryssrutan för *E-postägare, deltagare och läsare* om du vill skicka ett e-postmeddelande. Standardåtgärden är att visa en avisering i portalen.

6. Klicka på **OK**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du konfigurerat och visat prestanda för den virtuella datorn. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en resursgrupp och virtuell dator
> * Aktivera startdiagnostik på den virtuella datorn
> * Visa startdiagnostik
> * Visa värdmått
> * Aktivera Azure Monitor for VMs
> * Visa VM-mått
> * Skapa en avisering

Gå vidare till nästa kurs om du vill veta mer om Azure Security Center.

> [!div class="nextstepaction"]
> [Hantera säkerheten för virtuella datorer](../../security/fundamentals/overview.md)
