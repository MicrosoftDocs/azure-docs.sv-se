---
title: Övervaka Azure-tjänster och-program med Grafana
description: Dirigera Azure Monitor och Application Insights data så att du kan visa dem i Grafana.
ms.topic: conceptual
ms.date: 11/06/2017
ms.openlocfilehash: decd0353bcb2cf048fd5591ea8c895cf22944809
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048666"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Övervaka dina Azure-tjänster i Grafana
Nu kan du övervaka Azure-tjänster och-program från [Grafana](https://grafana.com/) med hjälp av [Azure Monitor data källans plugin](https://grafana.com/plugins/grafana-azure-monitor-datasource)-program. Plugin-programmet samlar in program prestanda data som samlas in av Azure Monitor, inklusive olika loggar och mått. Du kan sedan Visa dessa data på Grafana-instrumentpanelen.

Använd följande steg för att konfigurera en Grafana-Server och skapa instrument paneler för mått och loggar från Azure Monitor.

## <a name="set-up-a-grafana-server"></a>Konfigurera en Grafana-Server

### <a name="set-up-grafana-locally"></a>Konfigurera Grafana lokalt
Om du vill konfigurera en lokal Grafana-Server [laddar du ned och installerar Grafana i din lokala miljö](https://grafana.com/grafana/download). Om du vill använda plugin-programmets Azure Monitor-integrering installerar du Grafana version 5,3 eller senare.

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>Konfigurera Grafana på Azure via Azure Marketplace
1. Gå till Azure Marketplace och välj Grafana av Grafana Labs.

2. Fyll i namn och information. Skapa en ny resursgrupp. Håll koll på de värden som du väljer för VM-användarnamnet, VM-lösenordet och Grafana-serverns administratörs lösen ord.  

3. Välj storlek på virtuell dator och ett lagrings konto.

4. Konfigurera inställningarna för nätverks konfigurationen.

5. Visa sammanfattningen och välj **skapa** när du har accepterat användnings villkoren.

6. När distributionen är klar väljer **du gå till resurs grupp**. Du ser en lista över nyligen skapade resurser.

    ![Resurs grupps objekt för Grafana](media/grafana-plugin/grafana1.png)

    Om du väljer Nätverks säkerhets gruppen (*Grafana-NSG* i det här fallet) kan du se att port 3000 används för att få åtkomst till Grafana-servern.

7. Hämta den offentliga IP-adressen för din Grafana-Server – gå tillbaka till listan över resurser och välj **offentlig IP-adress**.

## <a name="sign-in-to-grafana"></a>Logga in på Grafana

1. Med hjälp av IP-adressen för servern öppnar du inloggnings sidan på *http:// \<IP address\> : 3000* eller *\<DNSName> \: 3000* i webbläsaren. Även om 3000 är standard porten, Observera att du kanske har valt en annan port under installationen. Du bör se en inloggnings sida för den Grafana-server som du har skapat.

    ![Grafana inloggnings skärm](./media/grafana-plugin/grafana-login-screen.png)

2. Logga in med användar namns *administratören* och det Grafana-Server administratörs lösen ord som du skapade tidigare. Om du använder en lokal installation är standard lösen ordet *administratör* och du uppmanas att ändra det vid din första inloggning.

## <a name="configure-data-source-plugin"></a>Konfigurera data källans plugin-program

När du har loggat in bör du se att Azure Monitor data källans plugin-program redan ingår.

![Grafana innehåller Azure Monitor-plugin](./media/grafana-plugin/grafana-includes-azure-monitor-plugin-dark.png)

1. Välj **Lägg till data källa** för att lägga till och konfigurera data källan Azure Monitor.

2. Välj ett namn för data källan och välj **Azure Monitor** som typ i list rutan.

3. Skapa ett huvud namn för tjänsten – Grafana använder ett Azure Active Directory tjänstens huvud namn för att ansluta till Azure Monitor-API: er och samla in data. Du måste skapa eller använda ett befintligt huvud namn för tjänsten för att hantera åtkomst till dina Azure-resurser.
    * Se [de här anvisningarna](../../active-directory/develop/howto-create-service-principal-portal.md) för att skapa ett huvud namn för tjänsten. Kopiera och spara ditt klient-ID (katalog-ID), klient-ID (program-ID) och klient hemlighet (program nyckel värde).
    * Se [tilldela program till roll](../../active-directory/develop/howto-create-service-principal-portal.md) för att tilldela rollen läsare till Azure Active Directory program i den prenumeration, resurs grupp eller resurs som du vill övervaka. 
    Log Analytics-API: t kräver [rollen Log Analytics läsare](../../role-based-access-control/built-in-roles.md#log-analytics-reader), som innehåller läsar rollens behörigheter och som läggs till i den.

4. Ange anslutnings informationen till de API: er som du vill använda. Du kan ansluta till alla eller en del av dem. 
    * Om du ansluter till både mått och loggar i Azure Monitor kan du återanvända samma autentiseringsuppgifter genom att välja **samma information som Azure Monitor API**.
    * När du konfigurerar plugin-programmet kan du ange vilket Azure-moln som du vill att plugin-programmet ska övervaka (offentlig, Azure amerikanska myndigheter, Azure Germany eller Azure Kina).
    * Om du använder Application Insights kan du även ta med ditt Application Insights-API och program-ID för att samla in Application Insightsbaserade mått. Mer information finns i [Hämta API-nyckel och program-ID](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

        > [!NOTE]
        > Vissa fält i data källan får samma namn som de korrelerade Azure-inställningarna:
        > * Klient-ID är Azure-katalog-ID
        > * Klient-ID är Azure Active Directory program-ID
        > * Klient hemlighet är det Azure Active Directory programmets nyckel värde

5. Om du använder Application Insights kan du även ta med ditt Application Insights-API och program-ID för att samla in Application Insightsbaserade mått. Mer information finns i [Hämta API-nyckel och program-ID](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

6. Välj **Spara** och Grafana kommer att testa autentiseringsuppgifterna för varje API. Du bör se ett meddelande som liknar det som följer.  
    ![Konfiguration av Grafana-datakälla godkändes](./media/grafana-plugin/grafana-data-source-config-approved-dark.png)

## <a name="build-a-grafana-dashboard"></a>Bygg en Grafana-instrumentpanel

1. Gå till start sidan för Grafana och välj **ny instrument panel**.

2. I den nya instrument panelen väljer du **grafen**. Du kan prova andra diagram alternativ, men den här artikeln använder *Graph* som exempel.

3. Ett tomt diagram visas på din instrument panel. Klicka på panelens rubrik och välj **Redigera** för att ange information om de data som du vill rita i diagrammet.
    ![Grafana nytt diagram](./media/grafana-plugin/grafana-new-graph-dark.png)

4. Välj den Azure Monitor data källa som du har konfigurerat.
   * Samla in Azure Monitor mått – Välj **Azure Monitor** i list rutan för tjänster. En lista över väljare visas, där du kan välja vilka resurser och mått som ska övervakas i det här diagrammet. Om du vill samla in mått från en virtuell dator använder du namn området **Microsoft. Compute/VirtualMachines**. När du har valt virtuella datorer och mått kan du börja visa data i instrument panelen.
     ![Grafana Graph config för Azure Monitor](./media/grafana-plugin/grafana-graph-config-for-azure-monitor-dark.png)
   * Samla in Azure Monitor loggdata – Välj **Azure-Log Analytics** i list rutan för tjänster. Välj den arbets yta som du vill fråga och Ange frågetexten. Du kan kopiera här alla logg frågor som du redan har eller skapa en ny. När du skriver in din fråga kommer IntelliSense att visa och föreslå alternativ för automatisk komplettering. Välj visualiserings typ, **tids serie** **tabell** och kör frågan.
    
     > [!NOTE]
     >
     > Standard frågan som medföljer plugin-programmet använder två makron: "$ __timeFilter () och $ __interval. 
     > Med de här makrona kan Grafana dynamiskt beräkna tidsintervallet och tiden när du zoomar in en del av ett diagram. Du kan ta bort dessa makron och använda ett standard tids filter, t. ex. *TimeGenerated > sedan (1H)*, men det innebär att grafen inte stöder funktionen zooma in.
    
     ![Grafana Graph config för Azure Log Analytics](./media/grafana-plugin/grafana-graph-config-for-azure-log-analytics-dark.png)

5. Följande är en enkel instrument panel med två diagram. Den ena till vänster visar CPU-procenten för två virtuella datorer. I diagrammet till höger visas transaktionerna i ett Azure Storage-konto uppdelat efter transaktions-API-typen.
    ![Exempel på Grafana av två diagram](media/grafana-plugin/grafana6.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Valfritt: övervaka dina anpassade mått på samma Grafana-Server

Du kan också installera multiympkvistar och InfluxDB för att samla in och rita både anpassade och agentbaserade mått samma Grafana-instans. Det finns många plugin-program för data källor som du kan använda för att ta med dessa mått tillsammans på en instrument panel.

Du kan också återanvända den här inställningen för att inkludera mått från din Prometheus-Server. Använd plugin-galleriet för Prometheus-data källa i Grafana.

Här är en referens artikel om hur du använder InfluxDB, Prometheus och Docker
 - [Så här övervakar du system mått med skal stacken på Ubuntu 16,04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [En övervaknings lösning för Docker-värdar, behållare och behållar tjänster](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Här är en bild av en fullständig Grafana-instrumentpanel som har mått från Azure Monitor och Application Insights.
![Grafana exempel mått](media/grafana-plugin/grafana8.png)

## <a name="advanced-grafana-features"></a>Avancerade Grafana-funktioner

### <a name="variables"></a>Variabler
Vissa värden för frågor kan väljas med hjälp av UI-listruta och uppdateras i frågan. Tänk på följande fråga som exempel:
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

Du kan konfigurera en variabel som visar alla tillgängliga **lösnings** värden och sedan uppdaterar din fråga för att använda den.
Om du vill skapa en ny variabel klickar du på knappen Inställningar för instrument panelen i det övre högra fältet, väljer **variabler** och sedan **ny**.
På sidan variabel definierar du data källan och frågan som ska köras för att hämta listan över värden.
![Grafana konfigurera variabel](./media/grafana-plugin/grafana-configure-variable-dark.png)

När du har skapat justerar du frågan för att använda de valda värdena och dina diagram kommer att svara på följande sätt:
```
Usage 
| where $__timeFilter(TimeGenerated) and Solution in ($Solutions)
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```
    
![Grafana använder variabler](./media/grafana-plugin/grafana-use-variables-dark.png)

### <a name="create-dashboard-playlists"></a>Skapa spelnings panels spelnings listor

En av de många användbara funktionerna i Grafana är spel panelens spelnings lista. Du kan skapa flera instrument paneler och lägga till dem i en spelnings lista och konfigurera ett intervall för varje instrument panel som ska visas. Välj **spela upp** för att visa instrument panelerna i cykeln. Du kanske vill visa dem i en stor vägg Övervakare för att tillhandahålla en status tavla för gruppen.

![Exempel på Grafana-spel](./media/grafana-plugin/grafana7.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du har konfigurerat en Grafana-miljö i Azure debiteras du när virtuella datorer körs oavsett om du använder dem eller inte. Du undviker ytterligare kostnader genom att rensa resurs gruppen som skapades i den här artikeln.

1. Klicka på **resurs grupper** på den vänstra menyn i Azure Portal och klicka sedan på **Grafana**.
2. På sidan resurs grupp klickar du på **ta bort**, skriver **Grafana** i text rutan och klickar sedan på **ta bort**.

## <a name="next-steps"></a>Nästa steg
* [Översikt över Azure Monitor mått](../data-platform.md)
