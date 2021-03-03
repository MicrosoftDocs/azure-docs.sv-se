---
title: 'Snabb start: samla in data från en hybrid Linux-dator med Azure Monitor'
description: I den här snabb starten får du lära dig hur du distribuerar Log Analytics agent för Linux-datorer som körs utanför Azure och aktiverar data insamling med Azure Monitor loggar.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/24/2019
ms.author: bwren
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: dbe51930ec92ec4f89738dc5d543003f45acebf9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733830"
---
# <a name="quickstart-collect-data-from-a-linux-computer-in-a-hybrid-environment-with-azure-monitor"></a>Snabb start: samla in data från en Linux-dator i en hybrid miljö med Azure Monitor

[Azure Monitor](../overview.md) kan samla in data direkt från fysiska eller virtuella Linux-datorer i din miljö till en Log Analytics arbets yta för detaljerad analys och korrelation. Genom att installera [Log Analytics-agenten](../agents/log-analytics-agent.md) kan Azure Monitor samla in data från ett Data Center eller en annan moln miljö. Den här snabb starten visar hur du konfigurerar och samlar in data från Linux-servern med några enkla steg. Information om virtuella Azure Linux-datorer finns i [samla in data om Azure Virtual Machines](./quick-collect-azurevm.md).  

Information om vilka konfigurationer som stöds finns i konfiguration av [operativ system](../agents/agents-overview.md#supported-operating-systems) och [nätverks brand vägg](../agents/log-analytics-agent.md#network-requirements)som stöds.
 
Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Skapa en arbetsyta

1. Välj **Alla tjänster** i Azure-portalen. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics arbets ytor**.

    ![Hitta Log Analytics arbets yta i Azure Portal](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Välj **skapa** och välj sedan alternativ för följande objekt:

   * Ange ett namn för den nya **Log Analytics-arbetsytan**, som *DefaultLAWorkspace*.  
   * Välj en **prenumeration** att länka till genom att välja från den listrutan om standardvalet inte är lämpligt.
   * För **Resursgrupp** väljer du en befintlig resursgrupp som innehåller en eller flera virtuella datorer i Azure.  
   * Välj den **plats** där dina virtuella datorer distribueras.  Mer information finns i avsnittet om [tillgängliga regioner för Log Analytics](https://azure.microsoft.com/regions/services/).
   * Om du skapar en arbetsyta i en ny prenumeration som skapats efter 2 april 2018 används prisplanen *Per GB* automatiskt och alternativet för att välja en prisnivå är inte tillgängligt.  Om du skapar en arbetsyta för en befintlig prenumeration som skapats före 2 april eller en prenumeration som var bunden till en befintlig EA-registrering, väljer du önskad prisnivå.  Mer information om de olika nivåerna finns i [prisinformation om Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Skapa en Log Analytics arbets yta i Azure Portal](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. När du har angett nödvändig information i fönstret **Log Analytics arbets yta** väljer du **OK**.  

När informationen har verifierats och arbetsytan skapas, kan du spåra förloppet under **Meddelanden** på menyn. 

## <a name="obtain-workspace-id-and-key"></a>Hämta arbetsytans id och nyckel

Innan du installerar Log Analytics-agenten för Linux behöver du arbetsytans id och nyckel för Log Analytics-arbetsytan. Den här informationen krävs av agent omslutnings skriptet för att konfigurera agenten korrekt och se till att den kan kommunicera med Azure Monitor.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. I det övre vänstra hörnet av Azure Portal väljer du **alla tjänster**. I rutan Sök anger du **Log Analytics**. När du skriver filtreras listan baserat på dina inaktuella inaktuella inaktuella inaktuella. Välj **Log Analytics arbets ytor**.

2. I listan med Log Analytics arbets ytor väljer du den arbets yta som du skapade tidigare. (Du kan ha namngett det **DefaultLAWorkspace**.)

3. Välj **agent hantering**:
 
4. Välj sedan **Linux-servrar**.

5. Värdet till höger om **arbetsyte-ID** och **primär nyckel**. Kopiera och klistra in båda två i det redigeringsprogram du föredrar.

## <a name="install-the-agent-for-linux"></a>Installera agenten för Linux

Med följande steg konfigurerar du installationen av agenten för Log Analytics i Azure- och Azure Government-molnet.  

>[!NOTE]
>Log Analytics-agenten för Linux kan inte konfigureras att rapportera till fler än en Log Analytics-arbetsyta.  

Om Linux-datorn behöver kommunicera via en proxyserver till Log Analytics kan du ange proxykonfigurationen på kommandoraden genom att inkludera `-p [protocol://][user:password@]proxyhost[:port]`.  Egenskapen *proxyhost* accepterar ett fullständigt domännamn eller en fullständig IP-adress för proxyservern. 

Exempelvis: `https://user01:password@proxy01.contoso.com:30443`

1. Om du vill konfigurera Linux-datorn att ansluta till en Log Analytics arbets yta kör du följande kommando för att ange arbetsyte-ID och primär nyckel som kopierats tidigare. Med följande kommando laddar du ned agenten, verifierar dess kontrollsumma och installerar den. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Följande kommando innehåller proxy- `-p` parametern och exempel-syntaxen när autentisering krävs av proxyservern:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Om du vill konfigurera Linux-datorn så att den ansluter till Log Analytics arbets yta i Azure Government molnet kör du följande kommando som tillhandahåller arbetsyte-ID och primär nyckel som kopierats tidigare. Med följande kommando laddar du ned agenten, verifierar dess kontrollsumma och installerar den. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Följande kommando innehåller proxy- `-p` parametern och exempel-syntaxen när autentisering krävs av proxyservern:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```

3. Starta om agenten genom att köra följande kommando: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Samla in data om händelser och prestanda

Azure Monitor kan samla in händelser från Linux syslog och prestanda räknare som du anger för analys och rapportering på längre sikt. Det kan också vidta åtgärder när ett visst villkor upptäcks. Följ dessa steg om du vill konfigurera insamling av händelser från Linux Syslog och flera vanliga prestandaräknare till att börja med.  

1. Välj **Alla tjänster** i Azure-portalen. I listan över resurser skriver du Log Analytics. När du skriver filtreras listan baserat på dina inaktuella inaktuella inaktuella inaktuella. Välj **Log Analytics arbets ytor** och i listan över Log Analytics arbets ytor, väljer du den arbets yta som du söker efter och väljer **Avancerade inställningar** för arbets ytan **Log Analytics** .

2. Välj **data** och välj sedan **syslog**.  

3. Du lägger till syslog genom att skriva in namnet på loggen. Ange **syslog** och välj sedan plus tecknet **+** .  

4. Avmarkera allvarlighetsgraderna **Info**, **Meddelande** och **Felsök** i tabellen. 

5. Spara konfigurationen genom att välja **Spara** längst upp på sidan.

6. Välj **Linux-prestandadata** för att aktivera insamling av prestandaräknare på en Linux-dator. 

7. När du först konfigurerar Linux-prestandaräknare för en ny Log Analytics-arbetsyta har du möjlighet att snabbt skapa flera vanliga räknare. De listas med en kryssruta bredvid varje.

    ![Standard prestanda räknare för Linux som valts i Azure Monitor](media/quick-collect-azurevm/linux-perfcounters-azure-monitor.png)

    Välj **Använd konfigurationen nedan till mina datorer** och välj sedan **Lägg till de valda prestanda räknarna**. De läggs till med en förinställning av provintervall på tio sekunder.  

8. Spara konfigurationen genom att välja **Spara** längst upp på sidan.

## <a name="view-data-collected"></a>Visa data som samlas in

Nu när du har aktiverat insamling av data kan du köra en enkel loggsökning för att se vissa data från måldatorn.  

1. I den valda arbets ytan väljer du **loggar** i den vänstra rutan.

2. På sidan loggar fråga skriver `Perf` du i Frågeredigeraren och väljer **Kör**.
 
    ![Log Analytics loggs ökning](media/quick-collect-linux-computer/log-analytics-portal-queryexample.png)

    Frågan i följande bild returnerade till exempel 10 000 prestanda poster. Ditt resultatet blir mycket mindre.

    ![Resultat av loggsökning i Log Analytics](media/quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du ta bort agenten från Linux-datorn och ta bort Log Analytics-arbetsytan.  

Kör följande kommando för att ta bort agenten på Linux-datorn. Argumentet *--purge* tar bort agenten och dess konfiguration fullständigt.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

Om du vill ta bort arbets ytan väljer du arbets ytan Log Analytics som du skapade tidigare och på resurs sidan väljer du **ta bort**.

![Ta bort Log Analytics-resurs](media/quick-collect-linux-computer/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Nästa steg

Nu när du kan samla in funktions- och prestandadata från din lokala Linux-dator kan du enkelt kan börja utforska, analysera och vidta åtgärder på data som du samlar in *utan kostnad*.  

Om du vill lära dig hur du visar och analyserar data kan du fortsätta till självstudiekursen.

> [!div class="nextstepaction"]
> [Visa eller analysera data i Log Analytics](../logs/log-analytics-tutorial.md)