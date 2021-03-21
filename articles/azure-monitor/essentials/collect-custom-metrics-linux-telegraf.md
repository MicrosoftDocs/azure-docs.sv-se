---
title: Samla in anpassade mått för virtuella Linux-datorer med InfluxData-agenten för teleympkvistar
description: Anvisningar om hur du distribuerar InfluxData-på en virtuell Linux-dator i Azure och konfigurerar agenten för att publicera mått till Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.openlocfilehash: 204124240c6831ebb2c1df436736f475c48d98a8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048944"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Samla in anpassade mått för en virtuell Linux-dator med InfluxData-agenten för teleympkvistar

Genom att använda Azure Monitor kan du samla in anpassade mått via din programtelemetri, en agent som körs på dina Azure-resurser eller till och med externa övervaknings system. Sedan kan du skicka dem direkt till Azure Monitor. Den här artikeln innehåller anvisningar om hur du distribuerar [InfluxData](https://www.influxdata.com/) -agenten på en virtuell Linux-dator i Azure och konfigurerar agenten för att publicera mått till Azure Monitor. 

## <a name="influxdata-telegraf-agent"></a>InfluxDatain ympkvistar-agent 

[Teleympkvistar](https://docs.influxdata.com/telegraf/) är en plugin-driven agent som möjliggör insamling av mått från över 150 olika källor. Beroende på vilka arbets belastningar som körs på den virtuella datorn kan du konfigurera agenten för att utnyttja specialiserade plugin-program för att samla in mått. Exempel är MySQL, NGINX och Apache. Genom att använda plugin-program för utdata kan agenten skriva till destinationer som du väljer. Teleympkvistar-agenten har integrerats direkt med Azure Monitor anpassade mått REST API. Det stöder ett plugin-program för Azure Monitor-utdata. Genom att använda det här plugin-programmet kan agenten samla in arbets belastnings mått på den virtuella Linux-datorn och skicka dem som anpassade mått till Azure Monitor. 

 ![Översikt över Telegraph-agent](./media/collect-custom-metrics-linux-telegraf/telegraf-agent-overview.png)

> [!NOTE]  
> Anpassade mått stöds inte i alla regioner. De regioner som stöds visas [här](./metrics-custom-overview.md#supported-regions)

## <a name="send-custom-metrics"></a>Skicka anpassade mått 

I den här självstudien distribuerar vi en virtuell Linux-dator som kör operativ systemet Ubuntu 16,04 LTS. Teleympkvistar-agenten stöds för de flesta Linux-operativsystem. Både Debian-och RPM-paket är tillgängliga tillsammans med unpaketerade Linux-binärfiler på [InfluxData nedladdnings Portal](https://portal.influxdata.com/downloads). I den här [installations guiden för netympkvistar](https://docs.influxdata.com/telegraf/v1.8/introduction/installation/) finns ytterligare installationsinstruktioner och alternativ. 

Logga in på [Azure-portalen](https://portal.azure.com).

> [!NOTE]  
> Om du vill migrera klassiska aviserings regler och använda en befintlig virtuell Linux-dator kontrollerar du att virtuellt-datorn har en tilldelad identitet inställd **på**.

Skapa en ny virtuell Linux-dator: 

1. Välj alternativet **skapa en resurs** i det vänstra navigerings fönstret. 
1. Sök efter **virtuell dator**.  
1. Välj **Ubuntu 16,04 LTS** och välj **skapa**. 
1. Ange ett VM-namn som **MyTelegrafVM**.  
1. Lämna disk typen **SSD**. Ange sedan ett **användar namn**, till exempel **azureuser**. 
1. För **Autentiseringstyp** väljer du **lösen ord**. Ange ett lösen ord som du kommer att använda senare för att använda SSH i den här virtuella datorn. 
1. Välj att **skapa en ny resurs grupp**. Ange sedan ett namn, till exempel **myResourceGroup**. Välj din **plats**. Välj sedan **OK**. 

    ![Skapa en virtuell Ubuntu-dator](./media/collect-custom-metrics-linux-telegraf/create-vm.png)

1. Välj en storlek för den virtuella datorn. Du kan till exempel filtrera efter **Beräkningstyp** eller **Disktyp**. 

    ![Översikt över virtuella datorers storlek Telegraph-agent](./media/collect-custom-metrics-linux-telegraf/vm-size.png)

1. På sidan **Inställningar** i **nätverks**  >  **nätverks säkerhets grupp**  >  **väljer du offentliga inkommande portar**, väljer **http** och **SSH (22)**. Lämna resten av standardinställningarna och välj **OK**. 

1. På sammanfattningssidan klickar du på **Skapa** för att starta distributionen av den virtuella datorn. 

1. Den virtuella datorn fästs på Azure Portals instrumentpanel. När distributionen är klar öppnas den virtuella dator sammanfattningen automatiskt. 

1. I fönstret virtuell dator går du till fliken **identitet** . Se till att den virtuella datorn har en tilldelad identitet inställd **på**. 
 
    ![För hands version av netympkvistar VM-identitet](./media/collect-custom-metrics-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Anslut till VM:en 

Skapa en SSH-anslutning med den virtuella datorn. Välj **Anslut**-knappen på översiktssidan för din virtuella dator. 

![Översikts sida för virtuella datorer i netympkvistar](./media/collect-custom-metrics-linux-telegraf/connect-VM-button2.png)

På sidan **Anslut till den virtuella datorn** behåller du standardalternativen för att ansluta med DNS-namn via port 22. I **Logga in med lokalt konto för virtuell dator** visas ett anslutnings kommando. Klicka på knappen för att kopiera kommandot. Följande exempel visar hur SSH-anslutningskommandot ser ut: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Klistra in SSH-anslutningen i ett gränssnitt, till exempel Azure Cloud Shell eller bash på Ubuntu i Windows, eller Använd en SSH-klient som du väljer för att skapa anslutningen. 

## <a name="install-and-configure-telegraf"></a>Installera och konfigurera teleympkvistar 

Om du vill installera Debian-paketet för teleympkvistar på den virtuella datorn kör du följande kommandon från SSH-sessionen: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
I konfigurations filen för teleympkvistar definieras den andra operationen. Som standard installeras en exempel konfigurations fil på sökvägen **/etc/telegraf/telegraf.conf**. I exempel konfigurations filen visas alla möjliga plugin-program för indata och utdata. Vi kommer dock att skapa en anpassad konfigurations fil och låta agenten använda den genom att köra följande kommandon: 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]  
> Föregående kod aktiverar endast två plugin-program för inmatade program: **CPU** och **MEM**. Du kan lägga till fler plugin-program för indatamängd, beroende på vilken arbets belastning som körs på datorn. Exempel är Docker, MySQL och NGINX. En fullständig lista över plugin-program för inmatade program finns i avsnittet **ytterligare konfiguration** . 

Om du vill att agenten ska börja använda den nya konfigurationen tvingar vi agenten att stoppa och starta genom att köra följande kommandon: 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
Agenten samlar nu in mått från var och en av de angivna plugin-programmen och genererar dem till Azure Monitor. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Rita dina teleympkvistar-mått i Azure Portal 

1. Öppna [Azure-portalen](https://portal.azure.com). 

1. Gå till fliken ny **övervakare** . Välj sedan **mått**.  

     ![Övervaka-mått (för hands version)](./media/collect-custom-metrics-linux-telegraf/metrics.png)

1. Välj din virtuella dator i resurs väljaren.

     ![Mått diagram](./media/collect-custom-metrics-linux-telegraf/metric-chart.png)

1. Välj namn området för **teleympkvistar/CPU** och välj **usage_system** mått. Du kan välja att filtrera efter dimensionerna för det här måttet eller dela upp dem.  

     ![Välj namn område och mått](./media/collect-custom-metrics-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>Ytterligare konfiguration 

Föregående genom gång innehåller information om hur du konfigurerar teleympkvistar-agenten för att samla in mått från några få grundläggande plugin-program för indata. Teleympkvistar-agenten har stöd för över 150 inmatade plugin-program, med vissa stöd för ytterligare konfigurations alternativ. InfluxData har publicerat en [lista över plugin](https://docs.influxdata.com/telegraf/v1.15/plugins/inputs/) -program som stöds och instruktioner om [hur du konfigurerar dem](https://docs.influxdata.com/telegraf/v1.15/administration/configuration/).  

I den här genom gången använde du dessutom din teleympkvistar-agent för att generera mått om den virtuella dator som agenten distribueras på. Teleympkvistar-agenten kan också användas som insamlare och vidarebefordrare av mått för andra resurser. Information om hur du konfigurerar agenten för att generera mått för andra Azure-resurser finns i [Azure Monitor anpassad mått utmatning för teleympkvistar](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md).  

## <a name="clean-up-resources"></a>Rensa resurser 

När de inte längre behövs kan du ta bort resurs gruppen, den virtuella datorn och alla relaterade resurser. Det gör du genom att välja resurs gruppen för den virtuella datorn och välja **ta bort**. Bekräfta sedan namnet på den resurs grupp som ska tas bort. 

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [anpassade mått](./metrics-custom-overview.md).