---
title: Översikt över Azure Notebooks för hands version
description: Kör Jupyter Notebooks i molnet med den kostnads fria Azure Notebooks för hands versions tjänsten, där ingen konfiguration eller konfiguration krävs.
ms.topic: overview
ms.date: 04/05/2019
ms.openlocfilehash: ef0a033c5125b3a359d281be30dd0ec2a2aa1747
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109495"
---
# <a name="overview-of-azure-notebooks-preview"></a>Översikt över Azure Notebooks för hands version

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Azure Notebooks är en kostnadsfri värdbaserad tjänst som används för att utveckla och köra Jupyter Notebooks i molnet utan installation. [Jupyter](https://jupyter.org/) (tidigare ipython) är ett projekt med öppen källkod som gör att du enkelt kan kombinera markdown text, körbar kod, beständiga data, grafik och visualiseringar på en enda, delbar arbets yta, som kallas *Notebook* (avbildnings uppsättning Jupyter.org):

[![Exempel på Jupyter-anteckningsböcker](https://jupyter.org/assets/jupyterpreview.png)](https://jupyter.org/assets/jupyterpreview.png#lightbox)

Tack vare den här kraftfulla kombinationen av kod, grafik och förklarande text har Jupyter blivit populärt för många användningsområden, däribland datavetenskapsinstruktion, datarensning och transformering, numerisk simulering, statistisk modellering och utveckling av maskininlärningsmodeller.

## <a name="hassle-free-experience"></a>Problemfri upplevelse

Med Azure Notebooks kommer du igång snabbt med prototyper, datavetenskap, akademisk forskning eller att lära dig programmera i Python:

- Dataforskare har omedelbar åtkomst till en fullständig Anaconda-miljö utan installation.
- Lärare kan tillhandahålla en problemfri Python-miljö för studenter.
- Föredragshållare kan ge en ”like talk” eller ett webbseminarium utan att be deltagarna att tillbringa 45 minuter med att installera programvara.
- Utvecklare eller hobbyanvändare kan använda Notebooks som ett snabbt anteckningsblock för kod.

Notebooks blir ännu mer kraftfullt när personer samarbetar via en webbläsartillgänglig molntjänst såsom Azure Notebooks (i förhandsversion). I molnet behöver användarna inte installera Jupyter lokalt eller underhålla en miljö. Molnet gör det även enkelt att dela notebook-filer (och associerade datafiler) med andra behöriga användare. Därmed slipper de krånglet med att dela notebook-filer via externa medel såsom lagringsplatser för källkontroll. Med Azure Notebooks kan användarna dessutom kopiera (eller ”klona”) notebook-filer till sitt eget konto för ändring eller experimentering, vilket är särskilt användbart i instruktionssyfte.

Eftersom Azure Notebooks är en allmän plattform för skrivande, körning och delning av kod kan du använda den för många olika scenarier:

- Lär dig ett nytt programmeringsspråk – prova någon av [självstudierna på startsidan](https://notebooks.azure.com/Microsoft/projects/samples/html/Introduction%20to%20Python.ipynb)
- Lär dig datavetenskap – prova på [Jake VanderPlas bok](https://notebooks.azure.com/jakevdp/projects/PythonDataScienceHandbook)
- [Undervisa en kurs](https://notebooks.azure.com/garth-wells/projects/CUED-IA-Computing-Michaelmas) för hundratals studenter
- Håll ett webbseminarium online eller vid en konferens utan att lägga tid på installation 
- Gör så att GitHub-användare direkt kan läsa in och köra notebook-filer genom att [skapar en GitHub-startsymbol](https://notebooks.azure.com/help/projects/sharing/create-a-github-badge)
- Visa [PowerPoint-liknande bildspel](https://notebooks.azure.com/help/jupyter-notebooks/slides) där koden i bilderna är körbar!

Kort sagt hjälper Azure Notebooks dig att utföra ditt arbete effektivare och därmed uppnå mer.

> [!Note]
> Mer information om Jupyter i sig finns på [jupyter.org](https://jupyter.org/) och i [Jupyter-dokumentationen](https://jupyter-notebook.readthedocs.io/en/latest/).

## <a name="pricing-and-quotas"></a>Priser och kvoter

Azure Notebooks är en kostnadsfri tjänst, men varje projekt är begränsat till 4 GB minne och 1 GB data för att förhindra missbruk. Legitima användare som överskrider dessa gränser får en Captcha-utmaning för att fortsätta köra notebook-filer.

Släpp alla gränser genom att logga in på Azure Notebooks med ett konto med Azure Active Directory (till exempel ett företagskonto). Om kontot är associerat med en Azure-prenumeration kan du ansluta till valfria Azure Data Science Virtual Machine-instanser i den prenumerationen. 

Notebook-servrar garanterar att de finns i högst 8 timmar. I de flesta fall omfattas din behållare inte av denna gräns och fortsätter att köras efter den här tiden, men långvariga sessioner kan ibland stängas av för system stabilitet.

## <a name="available-kernels-and-environments"></a>Tillgängliga kernels och miljöer

För varje notebook-fil väljer du den kernel (det vill säga körningsmiljö) som används för att köra kodceller. Azure Notebooks stöder följande kernels:

- Python 2.7 + Anaconda2-5.3.0
- Python 3.6 + Anaconda3-5.3.0
- Python 3.5 + Anaconda3-4.2.0 (kommer att bli inaktuell)
- R 3.4.1 + Microsoft R Open 3.4.1
- F# 4.1.9

Azure Notebooks innehåller även extra paket utöver de grundläggande distributionerna. Python-kernlarna innehåller till exempel biblioteken numpy, pandas, scikit-learn, matplotlib och bokeh.

Du kan även anpassa ett projekt för att skapa en miljö för alla de notebook-filer i projektet.

Utöver de grundläggande distributionerna levereras Azure Notebooks förinstallerat med många extra paket som är användbara för dataexperter. Du kan även installera egna paket med hjälp av den typiska processen för varje språk.

## <a name="pre-configured-jupyter-extensions"></a>Förkonfigurerade Jupyter-tillägg

Azure Notebooks är förkonfigurerat med följande Jupyter-tillägg:

- [RISE](https://github.com/damianavila/RISE): Ett Jupyter-tillägg för bildspel (kallas även live_reveal).
- [JupyterLab](https://github.com/jupyterlab/jupyterlab): En fullständig beräkningsmiljö för att arbeta med Jupyter-notebooks.
- [Altair](https://github.com/ellisonbg/altair): Ett bibliotek för visualisering av deklarativ statistisk för Python.
- [BQPlot](https://github.com/bloomberg/bqplot): Ett interaktivt ritningsramverk för Jupyter Notebooks.
- [IpyWidgets](https://github.com/jupyter-widgets/ipywidgets): Interaktiva HTML-widgetar för Jupyter Notebooks.

## <a name="issues-and-getting-help"></a>Problem och få hjälp

Eftersom Azure Notebooks fortfarande är i förhandsversion kan det uppstå tillfälliga avbrott i tjänsten som kan vara mer frekventa eller långvariga än för andra Azure-tjänster. Vissa funktioner kan vara ofullständiga eller innehålla buggar.

För närvarande rekommenderar inte att du använder förhandsversionen av Azure Notebooks för affärskritiska program eller känsliga notebook-filer och data.

Om du vill diskutera frågor om Azure Notebooks kan du rapportera ett problem på [GitHub-lagringsplatsen](https://github.com/Microsoft/AzureNotebooks/issues).

## <a name="next-steps"></a>Nästa steg  

- Snabbstarter:

  - [Logga in och ange ett användar-ID](quickstart-sign-in-azure-notebooks.md)
  - [Exportera en Jupyter Notebook](quickstart-export-jupyter-notebook-project.md)
