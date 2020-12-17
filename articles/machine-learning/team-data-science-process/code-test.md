---
title: Testa data vetenskaps kod med Azure DevOps Services – team data science process
description: Data vetenskaps kod som testar i Azure med den data uppsättning för data behandling med en värdes DevOps med grupp data vetenskap och Azure Services
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=weig, previous-ms.author=weig
ms.openlocfilehash: ce37ee51e549a2bd6f2747f28af8c038c39d8f1f
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656843"
---
# <a name="data-science-code-testing-on-azure-with-the-team-data-science-process-and-azure-devops-services"></a>Data vetenskaps kod testning på Azure med team data science-processen och Azure DevOps-tjänster
Den här artikeln innehåller preliminära rikt linjer för testning av kod i ett data vetenskaps flöde. Sådan testning ger data experter ett systematiskt och effektivt sätt att kontrol lera kvaliteten och förväntat resultat av koden. Vi använder ett TDSP-projekt (Team data science process) [som använder den data uppsättning för den sexuella](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) värde som vi publicerade tidigare för att visa hur kod testning kan göras. 

## <a name="introduction-on-code-testing"></a>Introduktion till kod testning
"Enhets testning" är en värnar sedan länge-metod för program utveckling. Men för data vetenskap är det ofta inte uppenbart vad "enhets testning" innebär och hur du bör testa kod för olika faser i en data vetenskaps livs cykel, till exempel:

* Förberedelse av data
* Data kvalitets undersökning
* Modellering
* Modelldistribution 

Den här artikeln ersätter termen "enhets testning" med "kod testning". Det syftar på att testa som funktioner som hjälper till att utvärdera om kod för ett visst steg i en data vetenskaps livs cykel producerar resultat "som förväntat." Den person som skriver testet definierar vad som förväntas, beroende på resultatet av funktionen, till exempel, data kvalitets kontroll eller modellering.

Den här artikeln innehåller referenser som användbara resurser.

## <a name="azure-devops-for-the-testing-framework"></a>Azure-DevOps för test ramverket
I den här artikeln beskrivs hur du utför och automatiserar testning med hjälp av Azure-DevOps. Du kan välja att använda alternativa verktyg. Vi visar också hur du konfigurerar en automatisk version med hjälp av Azure DevOps och build-agenter. Vi använder Azure Data Science Virtual Machines (Dsvm) för att bygga agenter.

## <a name="flow-of-code-testing"></a>Flödes kod testning
Det totala arbets flödet för test kod i ett data vetenskaps projekt ser ut så här: 

![Flödes diagram över kod testning](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>Detaljerade steg

Använd följande steg för att konfigurera och köra kod testning och en automatiserad version med hjälp av en build-agent och en Azure-DevOps:

1. Skapa ett projekt i Visual Studio Desktop-programmet:

    ![Skärmen "Skapa nytt projekt" i Visual Studio](./media/code-test/create_project.PNG)

   När du har skapat ditt projekt hittar du det i Solution Explorer i den högra rutan:
    
    ![Steg för att skapa ett projekt](./media/code-test/create_python_project_in_vs.PNG)

    ![Solution Explorer](./media/code-test/solution_explorer_in_vs.PNG)

1. Mata in projekt koden i Azure DevOps Project Code-lagringsplatsen: 

    ![Projekt kod lagrings plats](./media/code-test/create_repo.PNG)

1. Anta att du har gjort en del förberedelse av data, till exempel data inmatning, funktions teknik och att skapa etikett kolumner. Du vill se till att din kod genererar de resultat som du förväntar dig. Här är en kod som du kan använda för att testa om data bearbetnings koden fungerar korrekt:

    * Kontrol lera att kolumn namnen är rätt:
    
      ![Kod för matchande kolumn namn](./media/code-test/check_column_names.PNG)

    * Kontrol lera att svars nivåerna är rätt:

      ![Kod för matchande nivåer](./media/code-test/check_response_levels.PNG)

    * Kontrol lera att svars procenten är rimlig:

      ![Kod för svars procent](./media/code-test/check_response_percentage.PNG)

    * Kontrol lera den saknade frekvensen för varje kolumn i data:
    
      ![Kod för saknad frekvens](./media/code-test/check_missing_rate.PNG)


1. När du har gjort data bearbetnings-och funktions teknik arbetet och du har tränat en bra modell bör du kontrol lera att den modell du tränat kan visa nya data uppsättningar korrekt. Du kan använda följande två tester för att kontrol lera förutsägelse nivåer och fördelning av etikett värden:

    * Kontrol lera förutsägelse nivåer:
    
      ![Kod för att kontrol lera förutsägelse nivåer](./media/code-test/check_prediction_levels.PNG)

    * Kontrol lera distributionen av förutsägelse värden:

      ![Kod för att kontrol lera förutsägelse värden](./media/code-test/check_prediction_values.PNG)

1. Sätt ihop alla test funktioner i ett Python-skript med namnet **test_funcs. py**:

    ![Python-skript för test funktioner](./media/code-test/create_file_test_func.PNG)


1. När du har för berett test koderna kan du konfigurera test miljön i Visual Studio.

   Skapa en python-fil med namnet **test1.py**. I den här filen skapar du en klass som innehåller alla tester du vill göra. I följande exempel visas sex test för beredd:
    
    ![Python-fil med en lista över tester i en klass](./media/code-test/create_file_test1_class.PNG)

1. Dessa tester kan identifieras automatiskt om du anger **codetest. testCase** efter ditt klass namn. Öppna test Utforskaren i den högra rutan och välj **Kör alla**. Alla tester körs sekventiellt och meddelar dig om testet lyckas eller inte.

    ![Köra testerna](./media/code-test/run_tests.PNG)

1. Checka in din kod till projektets lagrings plats med hjälp av Git-kommandon. Ditt senaste arbete visas snart i Azure-DevOps.

    ![Git-kommandon för att kontrol lera kod](./media/code-test/git_check_in.PNG)

    ![Senaste arbetet i Azure DevOps](./media/code-test/git_check_in_most_recent_work.PNG)

1. Konfigurera automatisk version och testning i Azure DevOps:

    a. I projekt databasen väljer du **build och release** och väljer sedan **+ ny** för att skapa en ny versions process.

    ![Val för att starta en ny versions process](./media/code-test/create_new_build.PNG)

    b. Följ anvisningarna för att välja käll kods plats, projekt namn, lagrings plats och gren information.
    
    ![Käll-, namn-, lagrings-och förgrenings information](./media/code-test/fill_in_build_info.PNG)

    c. Välj en mall. Eftersom det inte finns någon python-projektmall börjar du med att välja **tom process**. 

    ![Lista över mallar och knappen "tom process"](./media/code-test/start_empty_process_template.PNG)

    d. Namnge bygget och välj agenten. Du kan välja standardvärdet här om du vill använda en DSVM för att slutföra Bygg processen. Mer information om hur du ställer in agenter finns i [skapa och släppa agenter](/azure/devops/pipelines/agents/agents?view=vsts).
    
    ![Build-och agent-val](./media/code-test/select_agent.PNG)

    e. Välj **+** i det vänstra fönstret för att lägga till en uppgift för den här build-fasen. Eftersom vi ska köra python-skriptet **test1.py** för att slutföra alla kontroller, använder den här uppgiften ett PowerShell-kommando för att köra python-kod.
    
    ![Fönstret Lägg till aktiviteter med PowerShell valt](./media/code-test/add_task_powershell.PNG)

    f. I PowerShell-informationen fyller du i nödvändig information, till exempel namn och version av PowerShell. Välj **infogat skript** som typ. 
    
    I rutan under **infogat skript** kan du skriva python- **test1.py**. Kontrol lera att miljövariabeln är korrekt konfigurerad för python. Om du behöver en annan version eller kernel av python kan du uttryckligen ange sökvägen så som visas i bilden: 
    
    ![PowerShell-information](./media/code-test/powershell_scripts.PNG)

    ex. Klicka på **spara & kö** för att slutföra processen för att bygga pipelinen.

    ![Knappen Spara & kö](./media/code-test/save_and_queue_build_definition.PNG)

Varje gång som ett nytt genomförande skickas till kod lagrings platsen, kommer Bygg processen att starta automatiskt. Du kan definiera valfri gren. Processen kör **test1.py** -filen på agent datorn för att kontrol lera att allting som definieras i koden körs på rätt sätt. 

Om aviseringar ställs in korrekt får du ett meddelande via e-post när versionen är färdig. Du kan också kontrol lera Bygg status i Azure DevOps. Om det Miss lyckas kan du kontrol lera informationen om versionen och ta reda på vilken del som är bruten.

![E-postmeddelande om att build lyckades](./media/code-test/email_build_succeed.PNG)

![Azure DevOps-meddelande om att build lyckades](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>Nästa steg
* Se [lagrings platsen](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) för den enmängds inkomst förutsägelse för konkreta exempel på enhets test för data vetenskaps scenarier.
* Följ den föregående dispositionen och exemplen från det avförutsägande scenariot för en värde modell i dina egna data vetenskaps projekt.

## <a name="references"></a>Referenser
* [TDSP (Team Data Science Process)](./index.yml)
* [Test verktyg för Visual Studio](https://www.visualstudio.com/vs/features/testing-tools/)
* [Test resurser för Azure DevOps](https://www.visualstudio.com/team-services/)
* [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)