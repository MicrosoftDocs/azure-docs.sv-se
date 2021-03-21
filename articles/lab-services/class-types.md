---
title: Exempel på klass typer på Azure Lab Services | Microsoft Docs
description: Tillhandahåller vissa typer av klasser som du kan använda för att ställa in labb med Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5a90fb128f5954f3eb713714ff22ff40a3beab36
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627441"
---
# <a name="class-types-overview---azure-lab-services"></a>Översikt över klass typer – Azure Lab Services

Med Azure Lab Services kan du snabbt konfigurera klass rums labb miljöer i molnet. Artiklar i det här avsnittet innehåller information om hur du konfigurerar flera typer av labb med Azure Lab Services.

## <a name="arcgis"></a>ArcGIS
[ArcGIS](https://www.esri.com/en-us/arcgis/products/arcgis-solutions/overview) är en typ av geografiskt informations system (GIS).  Du kan ställa in ett labb som använder ArcGIS Desktops olika program, till exempel [ArcMap](https://desktop.arcgis.com/en/arcmap/latest/map/main/what-is-arcmap-.htm) för att göra, redigera och analysera 2D-kartor.

Detaljerad information om hur du konfigurerar den här typen av labb finns i [Konfigurera ett labb för ArcMap\ArcGIS Desktop](class-type-arcgis.md).

## <a name="big-data-analytics"></a>Stordataanalyser
Du kan ställa in ett GPU-labb för att lära en stor data analys klass. Med den här typen av klass kan studenter lära sig att hantera stora mängder data och använda algoritmer för maskin-och statistiska inlärning för att härleda data insikter. Ett viktigt mål för studenter är att lära sig att använda verktyg för data analys som Apache Hadoop programmets program varu paket med öppen källkod som innehåller verktyg för att lagra, hantera och bearbeta Big data. 

Detaljerad information om hur du konfigurerar den här typen av labb finns i [Konfigurera ett labb för Big data Analytics med Docker-distribution av HortonWorks Data Platform](class-type-big-data-analytics.md).

## <a name="database-management"></a>Databashantering
Databas koncept är en av de introduktions kurser som finns i de flesta av dator vetenskaps avdelningarna på hög skola. Du kan ställa in ett labb för en grundläggande hanterings klass för databaser i Azure Lab Services. Du kan till exempel konfigurera en mall för virtuella datorer i ett labb med en [MySQL](https://www.mysql.com/) -databasserver eller en [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) -Server.

Detaljerad information om hur du konfigurerar den här typen av labb finns i [Konfigurera ett labb för att lära databas hantering för Relations databaser](class-type-database-management.md).

## <a name="deep-learning-in-natural-language-processing"></a>Djupinlärning i bearbetning av naturliga språk
Du kan ställa in ett labb som fokuserar på djup inlärning i naturlig språk bearbetning (NLP) med hjälp av Azure Lab Services. NLP (Natural Language Processing) är en form av artificiell intelligens (AI) som gör det möjligt för datorer med översättning, tal igenkänning och andra funktioner för språk förståelse. Studenter som tar en NLP-klass får en virtuell Linux-dator (VM) för att lära sig hur man använder neurala-nätverks algoritmer för att utveckla djup inlärnings modeller som används för att analysera skrivna mänskliga språk.

Detaljerad information om hur du konfigurerar den här typen av labb finns i [Konfigurera ett labb som fokuserar på djup inlärning i naturlig språk bearbetning med hjälp av Azure Lab Services](class-type-deep-learning-natural-language-processing.md).

## <a name="ethical-hacking"></a>Etisk hackning
Du kan ställa in ett labb för en klass som fokuserar på data utredning sida av etiska hackning. Testning av inträngning, en övning som används av den etiska hackning-communityn, inträffar när någon försöker få åtkomst till systemet eller nätverket för att Visa sårbarheter som en skadlig angripare kan utnyttja.

I en etisk hackning-klass kan studenter lära sig moderna tekniker för att försvara mot sårbarheter. Varje elev får en virtuell Windows Server-värd dator som har två kapslade virtuella datorer – en virtuell dator med [Metasploitable3](https://github.com/rapid7/metasploitable3) -avbildning och en annan dator med [Kali Linux](https://www.kali.org/) -avbildning. Den virtuella Metasploitable-datorn används för att utnyttja användnings sätt.  Den virtuella datorn Kali Linux ger till gång till de verktyg som behövs för att köra kriminal tekniska-uppgifter.

Detaljerad information om hur du ställer in den här typen av labb finns i [Konfigurera ett labb för att lära dig etiska hackning-klass](class-type-ethical-hacking.md).

## <a name="matlab"></a>MATLAB
[MATLAB](https://www.mathworks.com/products/matlab.html), som står för mat ris labb, är programmerings plattform från [MathWorks](https://www.mathworks.com/).  Den kombinerar beräknings kraft och visualisering som gör det till ett populärt verktyg i områdena matematik, teknik, fysik och kemi.

Detaljerad information om hur du konfigurerar den här typen av labb finns i [Konfigurera ett labb för att lära sig MATLAB](class-type-matlab.md).

## <a name="networking-with-gns3"></a>Nätverk med GNS3
Du kan ställa in ett labb för en klass som fokuserar på att tillåta studenter att emulera, konfigurera, testa och felsöka virtuella och verkliga nätverk med [gns3](https://www.gns3.com/) -programvara. 

Detaljerad information om hur du konfigurerar den här typen av labb finns i [Konfigurera ett labb för att lära en nätverks klass](class-type-networking-gns3.md).

## <a name="project-lead-the-way-pltw"></a>Projektet leder till (PLTW)
[Projektet leder dig (PLTW)](https://www.pltw.org/) är en ideell organisation som tillhandahåller PreK-12-kurs hantering i den USA i dator vetenskap, teknik och biomedicinsk vetenskap.  I varje PLTW-klass använder studenter en mängd olika program vara som en del av deras praktiska inlärnings upplevelser.

För detaljerad information om hur du konfigurerar dessa typer av labb, se [Konfigurera labb för projekt som leder till att klasser](class-type-pltw.md).

## <a name="python-and-jupyter-notebooks"></a>Python-och Jupyter-anteckningsböcker
Du kan konfigurera en mall på Azure Lab Services med de verktyg som behövs för att lära eleverna hur de använder [Jupyter Notebooks](http://jupyter-notebook.readthedocs.io). Jupyter Notebooks är ett projekt med öppen källkod som gör att du enkelt kan kombinera text och körbara [python](https://www.python.org/) -källkod på en enda arbets yta som kallas för en bärbar dator. Att köra en bärbar dator resulterar i en linjär inspelning av indata och utdata.  Dessa utdata kan innehålla text, tabeller med information, punkt diagram med mera.

Detaljerad information om hur du konfigurerar den här typen av labb finns i [Konfigurera ett labb för att lära data vetenskap med python-och Jupyter-anteckningsböcker](class-type-jupyter-notebook.md).

## <a name="shell-scripting-on-linux"></a>Gränssnittsskript i Linux
Du kan ställa in ett labb för att lära sig använda Shell-skript i Linux. Skript är en användbar del av system administration som gör det möjligt för administratörer att undvika repetitiva uppgifter. I det här exempel scenariot täcker klassen traditionella bash-skript och förbättrade skript. Förbättrade skript är skript som kombinerar bash-kommandon och Ruby. Med den här metoden kan ruby skicka data runt-och bash-kommandon för att interagera med gränssnittet.

Studenter som använder dessa skript klasser får en virtuell Linux-dator för att lära sig grunderna i Linux och även bekanta dig med bash shell-skript. Den virtuella Linux-datorn har åtkomst till fjärr skrivbord och med text redigerarna [gedit](https://help.gnome.org/users/gedit/stable/) och [Visual Studio Code](https://code.visualstudio.com/) installerade.

Detaljerad information om hur du konfigurerar den här typen av labb finns i [Shell-skriptning i Linux](class-type-shell-scripting-linux.md).

## <a name="solidworks-computer-aided-design-cad"></a>SolidWorks datorstödda design (CAD)
Du kan konfigurera ett GPU-labb som ger tekniska studenter till gång till [SolidWorks](https://www.solidworks.com/).  SolidWorks är en 3D CAD-miljö för att utforma solida objekt.  Med SolidWorks kan ingenjörer enkelt skapa, visualisera, simulera och dokumentera sina design.

Detaljerad information om hur du konfigurerar den här typen av labb finns i [Konfigurera ett labb för teknik klasser med hjälp av SolidWorks](class-type-solidworks.md).

## <a name="sql-database-and-management"></a>SQL-databas och-hantering
Structured Query Language (SQL) är standard språket för Relations databas hantering, inklusive tillägg, åtkomst och hantering av innehåll i en databas.  Du kan ställa in ett labb för att lära databas koncept med hjälp av både [MySQL](https://www.mysql.com/) -databasservern och [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) -servern.

Detaljerad information om hur du konfigurerar den här typen av labb finns i [Konfigurera ett labb för att lära databas hantering för Relations databaser](class-type-database-management.md).

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Konfigurera ett labb som fokuserar på djup inlärning i naturlig språk bearbetning med hjälp av Azure Lab Services](class-type-deep-learning-natural-language-processing.md)
- [Gränssnittsskript i Linux](class-type-shell-scripting-linux.md)
- [Etisk hackning](class-type-ethical-hacking.md)