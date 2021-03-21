---
title: Samla in resurs loggar från en Azure-resurs och analysera med Azure Monitor
description: Självstudie för att konfigurera diagnostikinställningar för att samla in resurs loggar från en Azure-resurs till en Log Analytics arbets yta där de kan analyseras med en logg fråga.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: 43a9c88bc64b688bfd1171e331232bb254b0eebe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102032882"
---
# <a name="tutorial-collect-and-analyze-resource-logs-from-an-azure-resource"></a>Självstudie: samla in och analysera resurs loggar från en Azure-resurs

Resurs loggar ger insikt i den detaljerade driften av en Azure-resurs och är användbara för övervakning av deras hälsa och tillgänglighet. Azure-resurser genererar resurs loggar automatiskt, men du måste konfigurera var de ska samlas in. Den här självstudien vägleder dig genom processen att skapa en diagnostisk inställning för att samla in resurs loggar för en resurs i din Azure-prenumeration och analysera den med en logg fråga.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en Log Analytics arbets yta i Azure Monitor
> * Skapa en diagnostisk inställning för att samla in resurs loggar 
> * Skapa en enkel logg fråga för att analysera loggar


## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du en Azure-resurs för att övervaka. Du kan använda vilken resurs som helst i din Azure-prenumeration som stöder diagnostikinställningar. För att avgöra om en resurs stöder diagnostikinställningar går du till dess meny i Azure Portal och kontrollerar att det finns ett **diagnostiskt inställnings** alternativ i avsnittet **övervakning** på menyn.


## <a name="log-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com) .


## <a name="create-a-workspace"></a>Skapa en arbetsyta
En Log Analytics arbets yta i Azure Monitor samlar in och indexerar loggdata från olika källor och möjliggör avancerad analys med hjälp av ett kraftfullt frågespråk. Log Analytics arbets ytan måste finnas innan du skapar en diagnostisk inställning för att skicka data till den. Du kan använda en befintlig arbets yta i din Azure-prenumeration eller skapa en med hjälp av följande procedur. 

> [!NOTE]
> Även om du kan arbeta med data i Log Analytics arbets ytor på **Azure Monitor** -menyn kan du skapa och hantera arbets ytor i menyn **Log Analytics arbets ytor** .

1. Från **alla tjänster** väljer du **Log Analytics arbets ytor**.
2. Klicka på **Lägg till** längst upp på skärmen och ange följande information för arbets ytan:
   - **Log Analytics arbets yta**: namn för den nya arbets ytan. Det här namnet måste vara globalt unikt för alla Azure Monitor prenumerationer.
   - **Prenumeration**: Välj den prenumeration där du vill lagra arbets ytan. Detta behöver inte vara samma prenumeration som den resurs som övervakas.
   - **Resurs grupp**: Välj en befintlig resurs grupp eller klicka på **Skapa ny** för att skapa en ny. Detta behöver inte vara samma resurs grupp som den resurs som övervakas.
   - **Plats**: Välj en Azure-region eller skapa en ny. Det behöver inte vara samma plats som den resurs som övervakas.
   - **Pris nivå**: Välj *betala per* användning som pris nivå. Du kan ändra den här pris nivån senare. Klicka på länken **Log Analytics priser** om du vill veta mer om olika pris nivåer.

    ![Ny arbetsyta](media/tutorial-resource-logs/new-workspace.png)

3. Klicka på **OK** för att skapa arbets ytan.

## <a name="create-a-diagnostic-setting"></a>Skapa en diagnostisk inställning
[Diagnostiska inställningar](../essentials/diagnostic-settings.md) definierar var resurs loggar ska skickas för en viss resurs. En enda diagnostisk inställning kan ha flera [mål](../essentials/diagnostic-settings.md#destinations), men vi använder bara en Log Analytics arbets yta i den här självstudien.

1. Under avsnittet **övervakning** på resurs menyn väljer du **diagnostikinställningar**.
2. Du bör ha ett meddelande om att inga diagnostikinställningar har definierats. Klicka på **Lägg till diagnostisk inställning**.

    ![Diagnostikinställningar](media/tutorial-resource-logs/diagnostic-settings.png)

3. Varje diagnostisk inställning har tre grundläggande delar:
 
   - **Namn**: detta har ingen betydande inverkan och bör helt enkelt vara beskrivande.
   - **Mål**: en eller flera destinationer för att skicka loggarna. Alla Azure-tjänster delar samma uppsättning tre möjliga destinationer. Varje diagnostisk inställning kan definiera ett eller flera mål, men inte fler än ett mål av en viss typ. 
   - **Kategorier**: kategorier av loggar som ska skickas till varje mål. Uppsättningen kategorier varierar för varje Azure-tjänst.

4. Välj **Skicka till Log Analytics arbets yta** och välj sedan den arbets yta som du skapade.
5. Välj de kategorier som du vill samla in. I dokumentationen för varje tjänst finns en definition av de tillgängliga kategorierna.

    ![Diagnostisk inställning](media/tutorial-resource-logs/diagnostic-setting.png)

6. Spara diagnostikinställningar genom att klicka på **Spara** .

    
 
 ## <a name="use-a-log-query-to-retrieve-logs"></a>Använd en logg fråga för att hämta loggar
Data hämtas från en Log Analytics-arbetsyta med hjälp av en logg fråga som skrivits i KQL (Query Language). Insikter och lösningar i Azure Monitor kommer att tillhandahålla logg frågor för att hämta data för en viss tjänst, men du kan arbeta direkt med logg frågor och deras resultat i Azure Portal med Log Analytics. 

1. Under avsnittet **övervakning** på resurs menyn väljer du **loggar**.
2. Log Analytics öppnas med ett tomt frågefönster med omfånget angivet till din resurs. Alla frågor kommer bara att innehålla poster från den resursen.

    > [!NOTE]
    > Om du öppnade loggar från Azure Monitor-menyn, ställs området in på arbets ytan Log Analytics. I det här fallet inkluderar alla frågor alla poster i arbets ytan.
   
    ![Skärm bild som visar loggar för en Logic app som visar en ny fråga med namnet på den logiska appen markerad.](media/tutorial-resource-logs/logs.png)

4. Tjänsten som visas i exemplet skriver resurs loggar till tabellen **AzureDiagnostics** , men andra tjänster kan skriva till andra tabeller. Se [tjänster, scheman och kategorier som stöds för Azures resurs loggar](../essentials/resource-logs-schema.md) för tabeller som används av olika Azure-tjänster.

    > [!NOTE]
    > Flera tjänster skriver resurs loggar till AzureDiagnostics-tabellen. Om du startar Log Analytics från Azure Monitor-menyn måste du lägga till en `where` instruktion med `ResourceProvider` kolumnen för att ange en viss tjänst. När du startar Log Analytics från en resurs meny är omfånget inställt på endast poster från den här resursen, så den här kolumnen är inte obligatorisk. Se tjänstens dokumentation för exempel frågor.


5. Skriv in en fråga och klicka på **Kör** för att granska resultaten. 
6. I [Kom igång med logg frågor i Azure Monitor](../logs/get-started-queries.md) finns en själv studie kurs om hur du skriver logg frågor.

    ![Logg fråga](media/tutorial-resource-logs/log-query-1.png)




## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du samlar in resurs loggar i en Log Analytics arbets yta, fyller du i en själv studie kurs om hur du skriver logg frågor för att analysera data.

> [!div class="nextstepaction"]
> [Kom igång med loggfrågor i Azure Monitor](../logs/get-started-queries.md)