---
title: Köra Jupyter-anteckningsböcker i din arbets yta
titleSuffix: Azure Machine Learning
description: Lär dig hur du kör en Jupyter-anteckningsbok utan att lämna din arbets yta i Azure Machine Learning Studio.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 01/19/2021
ms.openlocfilehash: 953b987e908736df703c354067ee27fc27d56073
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220716"
---
# <a name="run-jupyter-notebooks-in-your-workspace"></a>Köra Jupyter-anteckningsböcker i din arbets yta

Lär dig hur du kör dina Jupyter-anteckningsböcker direkt i din arbets yta i Azure Machine Learning Studio. Även om du kan starta [Jupyter](https://jupyter.org/) eller [JupyterLab](https://jupyterlab.readthedocs.io)kan du även redigera och köra dina antecknings böcker utan att lämna arbets ytan.

Information om hur du skapar och hanterar filer, inklusive antecknings böcker, finns i [skapa och hantera filer i din arbets yta](how-to-manage-files.md).

> [!IMPORTANT]
> Funktioner som marker ATS som (för hands version) tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://aka.ms/AMLFree) innan du börjar.
* En Machine Learning-arbetsyta. Se [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

## <a name="edit-a-notebook"></a>Redigera en bärbar dator

Om du vill redigera en antecknings bok öppnar du en antecknings bok i avsnittet **användarfiler** i din arbets yta. Klicka på cellen som du vill redigera.  Om du inte har några antecknings böcker i det här avsnittet kan du läsa [skapa och hantera filer i din arbets yta](how-to-manage-files.md).

Du kan redigera antecknings boken utan att ansluta till en beräknings instans.  När du vill köra cellerna i antecknings boken väljer eller skapar du en beräknings instans.  Om du väljer en stoppad beräknings instans kommer den automatiskt att starta när du kör den första cellen.

När en beräknings instans körs kan du också använda kod komplettering, som drivs av [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense), i valfri python-anteckningsbok.

Du kan också starta Jupyter eller JupyterLab från antecknings bokens verktygsfält.  Azure Machine Learning tillhandahåller inte uppdateringar och korrigerar buggar från Jupyter eller JupyterLab eftersom de är produkter med öppen källkod utanför gränserna för Microsoft Support.

## <a name="focus-mode"></a>Fokusläge

Använd fokus läge för att expandera den aktuella vyn så att du kan fokusera på dina aktiva flikar. Fokus läge döljer fil Utforskaren i antecknings boken.

1. I verktygsfältet i terminalfönstret väljer du **fokus läge** för att aktivera fokus läge. Beroende på fönstrets bredd kan verktyget finnas under meny alternativet **...** i verktygsfältet.
1. I fokus läge går du tillbaka till standardvyn genom att välja **standardvy**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/focusmode.gif" alt-text="Växla fokus läge/standardvy":::

## <a name="code-completion-intellisense"></a>Kod komplettering (IntelliSense)

[IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) är ett stöd för kod komplettering som innehåller många funktioner: list medlemmar, parameter information, snabb information och fullständig ord. Med bara några tangenttryckningar kan du:
* Läs mer om den kod som du använder
* Håll koll på de parametrar som du skriver
* Lägg till anrop i egenskaper och metoder 

### <a name="insert-code-snippets-preview"></a>Infoga kod avsnitt (förhands granskning)

Använd **CTRL + blank steg** för att utlösa IntelliSense-kodfragment.  Bläddra genom förslagen eller börja skriva för att hitta den kod som du vill infoga.  När du infogar kod går du till TABB-och-argumenten för att anpassa koden för eget bruk.

:::image type="content" source="media/how-to-run-jupyter-notebooks/insert-snippet.gif" alt-text="Infoga ett kodfragment":::

Samma kodfragment är tillgängliga när du öppnar antecknings boken i VS Code. En fullständig lista över tillgängliga kodfragment finns i [Azure Machine Learning vs](https://github.com/Azure/azureml-snippets/blob/main/snippets/snippets.md)kodfragment.

Du kan bläddra och söka i listan över kodfragment med hjälp av antecknings bokens verktygsfält för att öppna kod avsnitts panelen.

:::image type="content" source="media/how-to-run-jupyter-notebooks/open-snippet-panel.png" alt-text="Öppna kodfragments panel verktyget i verktygsfältet för bärbara datorer":::

Från fönstret kod avsnitt kan du också skicka en begäran om att lägga till nya kodfragment.

:::image type="content" source="media/how-to-run-jupyter-notebooks/propose-new-snippet.png" alt-text="Med kod avsnitts panelen kan du föreslå ett nytt kodfragment":::

## <a name="clean-your-notebook-preview"></a>Rensa din bärbara dator (för hands version)

I samband med att du skapar en antecknings bok slutar du normalt med celler som du använde för data utforskning eller fel sökning. Med funktionen *samla in* kan du skapa en ren bärbar dator utan dessa främmande celler.

1. Kör alla dina Notebook-celler.
1. Välj den cell som innehåller den kod som du vill att den nya bärbara datorn ska köra. Till exempel den kod som skickar ett experiment eller kanske koden som registrerar en modell.
1. Välj den **insamlings** ikon som visas i cell verktygsfältet.
    :::image type="content" source="media/how-to-run-jupyter-notebooks/gather.png" alt-text="Skärm bild: Välj ikonen samla in":::
1. Ange namnet på den nya "insamlade" notebook-anteckningsboken.  

Den nya antecknings boken innehåller bara kod celler, med alla celler som krävs för att skapa samma resultat som den cell som du har valt för att samla in.

## <a name="save-and-checkpoint-a-notebook"></a>Spara och Checkpoint en bärbar dator

Azure Machine Learning skapar en kontroll punkts fil när du skapar en *ipynb* -fil.

I verktygsfältet Notebook väljer du menyn och sedan **&gt; Spara och kontrol kontroll punkt** för att manuellt spara antecknings boken och lägger till en kontroll punkts fil som är associerad med antecknings boken.

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="Skärm bild av verktyget Spara i anteckningsbok-verktygsfältet":::

Alla antecknings böcker sparas automatiskt var 30: e sekund. Spara automatiskt uppdateringar endast den ursprungliga *ipynb* -filen, inte kontroll punkts filen.
 
Välj **kontroll punkter** i Notebook-menyn för att skapa en namngiven kontroll punkt och återställa antecknings boken till en sparad kontroll punkt.

## <a name="export-a-notebook"></a>Exportera en bärbar dator

I verktygsfältet Notebook väljer du menyn och **exporterar sedan som** för att exportera antecknings boken som vilken typ som stöds:

* Notebook-fil
* Python
* HTML
* LaTeX

:::image type="content" source="media/how-to-run-jupyter-notebooks/export-notebook.png" alt-text="Exportera en bärbar dator till din dator":::

Den exporterade filen sparas på din dator.

## <a name="run-a-notebook-or-python-script"></a>Köra ett Notebook-eller Python-skript

Om du vill köra en bärbar dator eller ett Python-skript ansluter du först till en [beräknings instans](concept-compute-instance.md)som körs.

* Om du inte har en beräknings instans använder du följande steg för att skapa ett:

    1. I verktygsfältet för antecknings boken eller skript, till höger om List rutan Compute, väljer du **+ ny beräkning**. Beroende på din skärm storlek kan detta finnas i en **...** -meny.
        :::image type="content" source="media/how-to-run-jupyter-notebooks/new-compute.png" alt-text="Skapa en ny beräkning":::
    1. Namnge beräkningen och välj en storlek för den **virtuella datorn**. 
    1. Välj **Skapa**.
    1. Beräknings instansen är automatiskt ansluten till filen.  Nu kan du köra Notebook-cellerna eller python-skriptet med verktyget till vänster om beräknings instansen.

* Om du har en stoppad beräknings instans väljer du  **Starta beräkning** till höger om List rutan beräkna. Beroende på din skärm storlek kan detta finnas i en **...** -meny.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/start-compute.png" alt-text="Starta beräknings instans":::

Endast du kan se och använda de beräknings instanser som du skapar.  Dina **användarfiler** lagras separat från den virtuella datorn och delas mellan alla beräknings instanser i arbets ytan.

### <a name="view-logs-and-output"></a>Visa loggar och utdata

Använd [Notebook-widgetar](/python/api/azureml-widgets/azureml.widgets) för att visa förloppet för körningen och loggarna. En widget är asynkron och innehåller uppdateringar tills inlärningen är klar. Azure Machine Learning-widgetar stöds också i Jupyter och JupterLab.

:::image type="content" source="media/how-to-run-jupyter-notebooks/jupyter-widget.png" alt-text="Skärm bild: widgeten Jupyter Notebook ":::

## <a name="explore-variables-in-the-notebook"></a>Utforska variabler i antecknings boken

I verktygsfältet Notebook använder du **variabel Explorer** -verktyget för att visa namn, typ, längd och exempel värden för alla variabler som har skapats i din bärbara dator.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer.png" alt-text="Skärm bild: verktyget variabel Explorer":::

Välj verktyget för att Visa variabel Explorer-fönstret.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer-window.png" alt-text="Skärm bild: fönstret variabel Utforskaren":::

## <a name="navigate-with-a-toc"></a>Navigera med en innehålls förteckning

I verktygsfältet Notebook använder du verktyget  **innehålls** förteckning för att visa eller dölja innehålls förteckningen.  Starta en markdown-cell med en rubrik för att lägga till den i innehålls förteckningen. Klicka på en post i tabellen om du vill bläddra till den cellen i antecknings boken.  

:::image type="content" source="media/how-to-run-jupyter-notebooks/table-of-contents.png" alt-text="Skärm bild: innehålls förteckning i antecknings boken":::

## <a name="change-the-notebook-environment"></a>Ändra Notebook-miljön

I verktygsfältet Notebook kan du ändra den miljö som antecknings boken körs på.  

Dessa åtgärder ändrar inte antecknings läget eller värdena för några variabler i antecknings boken:

|Action  |Resultat  |
|---------|---------| --------|
|Stoppa kärnan     |  Stoppar eventuell cell som körs. Om du kör en cell startas kerneln om automatiskt. |
|Navigera till en annan arbets yta-sektion     |     Celler som körs stoppas. |

Dessa åtgärder återställer antecknings bokens tillstånd och återställer alla variabler i antecknings boken.

|Action  |Resultat  |
|---------|---------| --------|
| Ändra kernel | Notebook använder ny kernel |
| Ändra beräkning    |     Antecknings boken använder automatiskt den nya beräkningen. |
| Återställ beräkning | Startar igen när du försöker köra en cell |
| Stoppa beräkning     |    Inga celler kommer att köras  |
| Öppna antecknings boken i Jupyter eller JupyterLab     |    Antecknings boken öppnas på en ny flik.  |

## <a name="add-new-kernels"></a>Lägg till nya kärnor

[Använd terminalen](how-to-access-terminal.md#add-new-kernels) för att skapa och lägga till nya kärnor i din beräknings instans. Antecknings boken hittar automatiskt alla Jupyter-kernels som är installerade på den anslutna beräknings instansen.

Använd List rutan kernel till höger för att ändra till någon av de installerade kernelerna.  


### <a name="status-indicators"></a>Status indikatorer

En indikator bredvid List rutan **Compute** visar dess status.  Statusen visas också i själva List rutan.  

|Färg |Beräknings status |
|---------|---------| 
| Green | Beräkning som körs |
| Red |Det gick inte att beräkna | 
| Svart | Beräkning stoppad |
|  Ljusblå |Beräkning skapa, starta, starta om, konfigurera |
|  Mörkgrå |Ta bort beräkning, stoppa |

En indikator bredvid List rutan **kernel** visar dess status.

|Färg |Kernel-status |
|---------|---------|
|  Green |Kernel-ansluten, inaktiv, upptagen|
|  Mörkgrå |Kernel inte ansluten |

## <a name="find-compute-details"></a>Hitta beräknings information

Hitta information om dina beräknings instanser på **beräknings** sidan i [Studio](https://ml.azure.com).

## <a name="useful-keyboard-shortcuts"></a>Användbara kortkommandon
Azure Machine Learning Studio antecknings böcker liknar Jupyter-anteckningsböcker har ett spärrat användar gränssnitt. Tangent bordet skiljer sig åt beroende på vilket läge som Notebook-cellen finns i. Azure Machine Learning Studio Notebooks har stöd för följande två lägen för en specifik kod cell: kommando läge och redigerings läge.

### <a name="command-mode-shortcuts"></a>Kortkommandon för kommando läge

En cell är i kommando läge när ingen text markör visas som anger att du skriver. När en cell är i kommando läge kan du redigera antecknings boken som helhet men inte skriva in enskilda celler. Ange kommando läge genom att trycka `ESC` eller använda musen för att välja utanför cellens redigerings området.  Den vänstra kant linjen i den aktiva cellen är blå och heldragen och dess **körnings** knapp är blå.

   :::image type="content" source="media/how-to-run-jupyter-notebooks/command-mode.png" alt-text="Notebook-cell i kommando läge ":::

| Genvägar                      | Beskrivning                          |
| ----------------------------- | ------------------------------------|
| Ange                         | Växla till redigeringsläge             |        
| Skift + Retur                 | Kör cell, Välj nedan         |     
| Kontroll/kommando + retur       | Kör cell                            |
| ALT + RETUR                   | Kör cell, Infoga tecken cell under    |
| Kontroll/kommando + ALT + RETUR | Kör cell, infoga markdown cell under|
| Alt + R                       | Kör alla      |                       
| Y                             | Konvertera cell till kod    |                         
| M                             | Konvertera cell till markdown  |                       
| Upp/K                          | Markera cell ovanför    |               
| Ned/J                        | Markera cell under    |               
| A                             | Infoga tecken cell ovan  |            
| B                             | Infoga kod cell under   |           
| CTRL + Kommando + Skift + A   | Infoga markdown cell ovanför    |      
| Kontroll/kommando + Skift + B   | Infoga markdown cell under   |       
| X                             | Klipp ut markerad cell    |               
| C                             | Kopiera markerad cell   |               
| SHIFT + V                     | Klistra in markerad cell ovanför           |
| V                             | Klistra in markerad cell under    |       
| D D                           | Ta bort markerad cell|                
| O                             | Växla utdata         |              
| SKIFT + O                     | Växla utdata-rullning   |          
| Jag                           | Avbryt kernel |                   
| 0 0                           | Starta om kernel |                     
| Skift + blank steg                 | Rulla uppåt  |                         
| Space                         | Rulla nedåt|
| Flik                           | Ändra fokus till nästa fokus objekt (när TABB-trap är inaktive rad)|
| Kontroll/kommando + S           | Spara antecknings boken |                      
| 1                             | Ändra till H1|                       
| 2                             | Ändra till H2|                        
| 3                             | Ändra till H3|                        
| 4                             | Ändra till H4 |                       
| 5                             | Ändra till H5 |                       
| 6                             | Ändra till H6 |                       

### <a name="edit-mode-shortcuts"></a>Genvägar för redigerings läge

Redigerings läget anges av en text markör där du ombeds ange redigerings ytan. När en cell är i redigerings läge kan du skriva i cellen. Ange redigerings läge genom att trycka `Enter` eller använda musen för att välja en cells redigerings yta. Den vänstra kant linjen i den aktiva cellen är grön och streckad och dess **körnings** knapp är grön. Du ser också markör frågan i cellen i redigerings läge.

   :::image type="content" source="media/how-to-run-jupyter-notebooks/edit-mode.png" alt-text="Notebook-cell i redigerings läge":::

Med följande kortkommandon kan du enkelt navigera och köra kod i Azure Machine Learning antecknings böcker i redigerings läge.

| Genvägar                      | Beskrivning|                                     
| ----------------------------- | ----------------------------------------------- |
| Escape                        | Ange kommando läge|  
| Kontroll/kommando + blank steg       | Aktivera IntelliSense |
| Skift + Retur                 | Kör cell, Välj nedan |                         
| Kontroll/kommando + retur       | Kör cell  |                                      
| ALT + RETUR                   | Kör cell, Infoga tecken cell under  |              
| Kontroll/kommando + ALT + RETUR | Kör cell, infoga markdown cell under  |          
| Alt + R                       | Kör alla celler     |                              
| Upp                            | Flytta markören uppåt eller bakåt i cellen    |             
| Ned                          | Flytta markören nedåt eller nästa cell |                  
| Kontroll/kommando + S           | Spara antecknings boken   |                                
| Kontroll/kommando + upp          | Gå till cell start   |                             
| Kontroll/kommando + ned        | Gå till cell slut |                                 
| Flik                           | Kod komplettering eller indrag (om TABB-trap är aktiverat) |
| Kontroll/kommando + M           | Aktivera/inaktivera TABB-trap  |                       
| Kontroll/kommando +]           | Uppgiften |                                         
| Kontroll/kommando + [           | Dra ut  |                                        
| Kontroll/kommando + A           | Välj alla|                                      
| Kontroll/kommando + Z           | Ångra |                                           
| CTRL + kommando + Shift + Z   | Gör om |                                           
| Kontroll/kommando + Y           | Gör om |                                           
| Kontroll/kommando + start        | Gå till cell start|                                
| Kontroll/kommando + slut         | Gå till cell slut   |                               
| Kontroll/kommando + vänster        | Gå ett ord till vänster |                               
| Kontroll/kommando + höger       | Gå ett ord till höger |                              
| Kontroll/kommando + Backsteg   | Ta bort ord före |                             
| Kontroll/kommando + ta bort      | Ta bort ord efter |                              
| Kontroll/kommando +/           | Växla kommentar i cell

## <a name="troubleshooting"></a>Felsökning

* Om du inte kan ansluta till en bärbar dator kontrollerar du att WebSocket-kommunikation **inte** är inaktiverat. För att Compute instance Jupyter-funktionen ska fungera måste WebSocket-kommunikationen vara aktive rad. Se till att nätverket tillåter WebSocket-anslutningar till *. instances.azureml.net och *. instances.azureml.ms. 
* När beräknings instansen distribueras i en privat länk arbets yta kan den bara nås [från det virtuella nätverket](./how-to-secure-training-vnet.md#compute-instance). Om du använder en anpassad DNS-eller Hosts-fil lägger du till en post för < instans-namn >. < region >. instances.azureml.ms med privat IP-adress för arbets ytans privata slut punkt. Mer information finns i den [anpassade DNS-](./how-to-custom-dns.md?tabs=azure-cli) artikeln.

    
## <a name="next-steps"></a>Nästa steg

* [Kör ditt första experiment](tutorial-1st-experiment-sdk-train.md)
* [Säkerhetskopiera fil lagringen med ögonblicks bilder](../storage/files/storage-snapshots-files.md)
* [Arbeta i säkra miljöer](./how-to-secure-training-vnet.md#compute-instance)