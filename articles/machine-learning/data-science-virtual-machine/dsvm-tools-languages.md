---
title: Språk som stöds
titleSuffix: Azure Data Science Virtual Machine
description: De program språk som stöds och relaterade verktyg som är förinstallerade på Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 42110eb896a751080044247932770f37617174c9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100516438"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Språk som stöds på Data Science Virtual Machine 

Data Science Virtual Machine (DSVM) innehåller flera färdiga språk och utvecklingsverktyg för att skapa dina AI-program (artificiell intelligens). Här är några av de mest viktiga.

## <a name="python-windows-server-2016-edition"></a>Python (Windows Server 2016 Edition)

| Kategori | Värde |
| ------------- | ------------- |
| Språk versioner som stöds | Python 2,7 och 3,7 |
| DSVM-versioner som stöds      | Windows Server 2016     |
| Hur konfigureras den/installeras på DSVM?  | Två globala `conda` miljöer skapas: <br /> * `root` Miljön som finns på `/anaconda/` är python 3,7. <br/> * `python2` Miljön som finns på `/anaconda/envs/python2` är python 2,7.       |
| Länkar till exempel      | Exempel på Jupyter-anteckningsböcker för python ingår.     |
| Relaterade verktyg på DSVM      | PySpark, R, Julia.      |

> [!NOTE]
> Versioner av Windows Server 2016 som skapades före mars 2018 innehåller python 3,5 och python 2,7. Python 2,7 är Conda- **rot** miljön och **py37** är python 3,7-miljön.

### <a name="how-to-use-and-run-it"></a>Använda och köra den    

* Kör i en kommando tolk:

  Öppna en kommando tolk och Använd någon av följande metoder, beroende på vilken version av python du vill köra:

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.7
    activate 
    python --version 
    ```
    
* Använd i en IDE:

  Använd Python Tools for Visual Studio (PTVS), som installeras i Visual Studio Community Edition. Som standard är den enda miljö som ställs in automatiskt i PTVS python 3,6. 

    > [!NOTE]
    > För att peka PTVS på python 2,7 måste du skapa en anpassad miljö i PTVS. Om du vill ange den här miljöns sökväg i Visual Studio Community Edition går du till **verktyg**  ->  **python tools**  ->  **python-miljöer** och väljer **+ anpassad**. Ange sedan platsen till **c:\anaconda\envs\python2** och välj **Automatisk identifiering**.

* Använd i Jupyter:

  Öppna Jupyter och välj **ny** för att skapa en ny antecknings bok. Du kan ställa in kernel-typen som _python [Conda root]_ för python 3,7 och _python [Conda-miljö: Python2]_ för python 2,7.

* Installera python-paket:

  Standard-python-miljöerna på DSVM är globala miljöer som kan läsas av alla användare. Men endast administratörer kan skriva och installera globala paket. Om du vill installera paket i den globala miljön aktiverar du i rot-eller python2-miljön med hjälp av `activate` kommandot som administratör. Sedan kan du använda en paket hanterare som `conda` eller `pip` för att installera eller uppdatera paket.

## <a name="python-linux-edition"></a>Python (Linux-utgåva)

| Kategori | Värde |
| ------------- | ------------- |
| Språk versioner som stöds | Python 2,7 och 3,5 |
| DSVM-versioner som stöds      | Linux   |
| Hur konfigureras den/installeras på DSVM?  | Två globala `conda` miljöer skapas: <br /> * `root` miljön finns på `/anaconda/` Python 2,7. <br/> * `py35` miljön finns på `/anaconda/envs/py35` Python 3,5.       |
| Länkar till exempel      | Exempel på Jupyter-anteckningsböcker för python ingår.     |
| Relaterade verktyg på DSVM      | PySpark, R, Julia      |

### <a name="how-to-use-and-run-it"></a>Använda och köra den    

* Kör i en Terminal:

  Öppna terminalen och gör något av följande, beroende på vilken version av python du vill köra:

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* Använd i en IDE:

  Använd pycharm med, som är installerat i Visual Studio Community Edition. 

* Använd i Jupyter:

  Öppna Jupyter och välj **ny** för att skapa en ny antecknings bok. Du kan ställa in kernel-typen som **python [Conda root]** för python 2,7 och **python [Conda-miljö: Py35]** för python 3,5-miljön. 

* Installera python-paket:

  De förvalda python-miljöerna på DSVM är globala miljöer som kan läsas av alla användare. Men endast administratörer kan skriva och installera globala paket. Om du vill installera paket i den globala miljön aktiverar du den i rot-eller py35-miljön genom att använda `source activate` kommandot som administratör eller som en användare med sudo-behörigheter. Sedan kan du använda en paket hanterare som `conda` eller `pip` för att installera eller uppdatera paket.


## <a name="r"></a>R

| Kategori | Värde |
| ------------- | ------------- |
| Språk versioner som stöds | Microsoft R Open 3. x (100% kompatibel med CRAN-R)<br /> Microsoft R Server 9. x Developer Edition (en skalbar Enterprise-Ready R-plattform)|
| DSVM-versioner som stöds      | Linux, Windows     |
| Hur konfigureras den/installeras på DSVM?  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: `/usr/lib64/microsoft-r/3.3/lib64/R`    |
| Länkar till exempel      | Exempel på Jupyter-anteckningsböcker för R ingår.     |
| Relaterade verktyg på DSVM      | Sparker, python, Julia      |

### <a name="how-to-use-and-run-it"></a>Använda och köra den    

**Windows**:

* Kör i en kommando tolk:

  Öppna en kommandotolk och skriv `R`.

* Använd i en IDE:

  Använd RTools för Visual Studio (RTVS) som är installerat i Visual Studio Community Edition eller RStudio. Dessa är tillgängliga på Start-menyn eller som en Skriv bords ikon. 

* Använd i Jupyter

  Öppna Jupyter och välj **ny** för att skapa en ny antecknings bok. Du kan ange kernel-typ som **R** för att använda Jupyter R-kerneln (IRKernel).

* Installera R-paket:

  R installeras på DSVM i en global miljö som kan läsas av alla användare. Men endast administratörer kan skriva och installera globala paket. Om du vill installera paket i den globala miljön kör du R genom att använda någon av föregående metoder. Sedan kan du köra R Package Manager `install.packages()` för att installera eller uppdatera paket.

**Linux**:

* Kör i terminaler:

  Öppna en Terminal och kör `R` .  

* Använd i en IDE:

  Använd RStudio, som är installerat på Linux-DSVM.  

* Använd i Jupyter:

  Öppna Jupyter och välj **ny** för att skapa en ny antecknings bok. Du kan ange kernel-typ som **R** för att använda Jupyter R-kerneln (IRKernel). 

* Installera R-paket:

  R installeras på DSVM i en global miljö som kan läsas av alla användare. Men endast administratörer kan skriva och installera globala paket. Om du vill installera paket i den globala miljön kör du R genom att använda någon av föregående metoder. Sedan kan du köra R Package Manager `install.packages()` för att installera eller uppdatera paket.


## <a name="julia"></a>Julia

| Kategori | Värde |
| ------------- | ------------- |
| Språk versioner som stöds | 0,6 |
| DSVM-versioner som stöds      | Linux, Windows     |
| Hur konfigureras den/installeras på DSVM?  | Windows: installerat kl. `C:\JuliaPro-VERSION`<br /> Linux: installeras kl. `/opt/JuliaPro-VERSION`    |
| Länkar till exempel      | Exempel på Jupyter-anteckningsböcker för Julia ingår.     |
| Relaterade verktyg på DSVM      | Python, R      |

### <a name="how-to-use-and-run-it"></a>Använda och köra den    

**Windows**:

* Kör i kommando tolken

  Öppna en kommando tolk och kör `julia` .
* Använd i en IDE:

  Använd `Juno` med Julia IDE installerat på DSVM och tillgänglig som en genväg på Skriv bordet.

* Använd i Jupyter:

  Öppna Jupyter och välj **ny** för att skapa en ny antecknings bok. Du kan ställa in kernel-typen som **Julia-version**.

* Installera Julia-paket:

  Standard platsen för Julia är en global miljö som kan läsas av alla användare. Men endast administratörer kan skriva och installera globala paket. Om du vill installera paket i den globala miljön kör du Julia genom att använda någon av föregående metoder. Sedan kan du köra Julia Package Manager-kommandon som `Pkg.add()` för att installera eller uppdatera paket.


**Linux**:
* Kör i en Terminal:

  Öppna en Terminal och kör `julia` .
* Använd i en IDE:

  Använd `Juno` , med Julia IDE installerat på DSVM och tillgängligt som en genväg till en **program** meny.

* Använd i Jupyter:

  Öppna Jupyter och välj **ny** för att skapa en ny antecknings bok. Du kan ställa in kernel-typen som **Julia-version**.

* Installera Julia-paket:

  Standard platsen för Julia är en global miljö som kan läsas av alla användare. Men endast administratörer kan skriva och installera globala paket. Om du vill installera paket i den globala miljön kör du Julia genom att använda någon av föregående metoder. Sedan kan du köra Julia Package Manager-kommandon som `Pkg.add()` för att installera eller uppdatera paket.

## <a name="other-languages"></a>Andra språk

**C#**: tillgängligt i Windows och tillgängligt via Visual Studio Community Edition eller vid `Developer Command Prompt for Visual Studio` , där du kan köra `csc` kommandot.

**Java**: openjdk finns på både Linux-och Windows-versionerna av DSVM och anges på sökvägen. Om du vill använda Java skriver `javac` du `java` kommandot eller i en kommando tolk i Windows eller i bash-gränssnittet i Linux.

**Node.js**: Node.js finns i både Linux-och Windows-versionerna av DSVM och anges på sökvägen. För att få åtkomst till Node.js, skriver du `node` kommandot eller i `npm` en kommando tolk i Windows eller i bash-gränssnittet i Linux. I Windows är Visual Studio-tillägget för Node.js verktyg installerat för att tillhandahålla en grafisk IDE för att utveckla ditt Node.js-program.

**F #**: tillgängligt i Windows och kan nås via Visual Studio Community Edition eller vid en `Developer Command Prompt for Visual Studio` , där du kan köra `fsc` kommandot.
