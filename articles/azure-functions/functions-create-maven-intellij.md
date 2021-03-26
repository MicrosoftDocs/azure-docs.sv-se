---
title: Skapa en Java-funktion i Azure Functions med IntelliJ
description: Lär dig hur du använder IntelliJ för att skapa en enkel HTTP-utlöst Java-funktion som du sedan publicerar för att köra i en miljö utan server i Azure.
author: yucwan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: yucwan
ms.custom: mvc, devcenter, devx-track-java
ms.openlocfilehash: 45fb62b446e6b589dc0cb9287a8aebe7f4e699b1
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543674"
---
# <a name="create-your-first-java-function-in-azure-using-intellij"></a>Skapa din första Java-funktion i Azure med IntelliJ

Den här artikeln:
- Så här skapar du en HTTP-utlöst Java-funktion i ett IntelliJ-idé projekt.
- Steg för att testa och felsöka projektet i Integrated Development Environment (IDE) på din egen dator.
- Instruktioner för att distribuera-funktions projektet till Azure Functions

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Ställt in din utvecklingsmiljö

Om du vill skapa och publicera Java-funktioner i Azure med IntelliJ installerar du följande program vara:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
+ Ett [Java Development Kit (JDK) för Azure som stöds](/azure/developer/java/fundamentals/java-jdk-long-term-support) för Java 8
+ [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition eller Community Edition installerat
+ [Maven 3.5.0 +](https://maven.apache.org/download.cgi)
+ Senaste [funktionens kärn verktyg](https://github.com/Azure/azure-functions-core-tools)


## <a name="installation-and-sign-in"></a>Installation och inloggning

1. Välj **Plugins** (Plugin-program) i IntelliJ IDEA-dialogrutan Settings/Preferences (Inställningar, Ctrl+Alt+S). Leta sedan reda på **Azure Toolkit for IntelliJ** på **Marketplace** och klicka på **Install** (Installera). När installationen är färdig klickar du på **Restart** (Starta om) för att aktivera plugin-programmet. 

    ![Plugin-programmet Azure Toolkit for IntelliJ på Marketplace][marketplace]

2. Logga in på Azure-kontot genom att öppna **Azure Explorer** i sidofältet och sedan klicka på ikonen **Azure Sign In** (Azure-inloggning) i fältet högst upp (eller på IDEA-menyn **Tools/Azure/Azure Sign in** (Verktyg/Azure/Azure-inloggning)).
    ![Kommandot för Azure-inloggning i IntelliJ][intellij-azure-login]

3. I fönstret **Azure Sign In** (Azure-inloggning) väljer du **Device Login** (Enhetsinloggning) och klickar sedan på **Sign in** (Logga in) ([fler inloggningsalternativ](/azure/developer/java/toolkit-for-intellij/sign-in-instructions)).

   ![Azure-inloggningsfönstret med enhetsinloggning markerat][intellij-azure-popup]

4. Klicka på **Copy&Open** (Kopiera och öppna) i dialogrutan **Azure Device Login** (Azure-enhetsinloggning).

   ![Dialogrutan för Azure-inloggning][intellij-azure-copycode]

5. Klistra in enhetskoden (som kopierades när du klickade på **Copy&Open** (Kopiera och öppna) i det senaste steget) i webbläsaren och klicka sedan på **Next** (Nästa).

   ![Webbläsare med enhetsinloggning][intellij-azure-link-ms-account]

6. I dialog rutan **Välj prenumerationer** väljer du de prenumerationer som du vill använda och klickar sedan på **Välj**.

   ![Dialogrutan Select Subscriptions (Välj prenumerationer)][intellij-azure-login-select-subs]
   
## <a name="create-your-local-project"></a>Skapa ditt lokala projekt

I det här avsnittet använder du Azure Toolkit for IntelliJ för att skapa ett lokalt Azure Functions-projekt. Senare i den här artikeln ska du publicera funktionskoden till Azure. 

1. Öppna välkomst dialog rutan IntelliJ, Välj *Skapa nytt projekt* för att öppna guiden Nytt projekt, Välj *Azure Functions*.

    ![Skapa Function-projekt](media/functions-create-first-java-intellij/create-functions-project.png)

1. Välj *http-utlösare* och klicka sedan på *Nästa* och följ guiden för att gå igenom alla konfigurationer på följande sidor. bekräfta din projekt plats och klicka sedan på *Slutför*. Intellj-idén kommer sedan att öppna det nya projektet.

    ![Skapa funktion projekt Slutför](media/functions-create-first-java-intellij/create-functions-project-finish.png)

## <a name="run-the-project-locally"></a>Köra projektet lokalt

1. Navigera till `src/main/java/org/example/functions/HttpTriggerFunction.java` för att se koden som genereras. Bredvid rad *17* ser du att det finns en grön *körnings* knapp, klickar på den och väljer *Kör "Azure-Function-tent..."*, så ser du att din funktions App körs lokalt med några få loggar.

    ![Lokalt kör projekt](media/functions-create-first-java-intellij/local-run-functions-project.png)

    ![Lokalt projekt som körs på utdata](media/functions-create-first-java-intellij/local-run-functions-output.png)

1. Du kan prova funktionen genom att öppna den utskrivna slut punkten från webbläsaren, t `http://localhost:7071/api/HttpTrigger-Java?name=Azure` . ex..

    ![Test resultat för lokal körnings funktion](media/functions-create-first-java-intellij/local-run-functions-test.png)

1. Loggen skrivs också ut i din idé, nu kan du stoppa appen genom att klicka på *stopp* knappen.

    ![Test logg för lokal körnings funktion](media/functions-create-first-java-intellij/local-run-functions-log.png)

## <a name="debug-the-project-locally"></a>Felsöka projektet lokalt

1. Om du vill felsöka funktions koden i projektet lokalt väljer du knappen *Felsök* i verktygsfältet. Om du inte ser verktygsfältet aktiverar du det genom att välja **Visa**  >  **utseende**-  >  **verktygsfältet**.

    ![Knappen lokal fel söknings funktion](media/functions-create-first-java-intellij/local-debug-functions-button.png)

1. Klicka på rad *20* i filen om du `src/main/java/org/example/functions/HttpTriggerFunction.java` vill lägga till en Bryt punkt, komma åt slut punkten `http://localhost:7071/api/HttpTrigger-Java?name=Azure` igen. du hittar Bryt punkten. du kan prova fler fel söknings funktioner som *steg*, *titta*, *utvärdering*. Stoppa felsökningssessionen genom att klicka på stopp knappen.

    ![Lokal fel söknings funktion app Break](media/functions-create-first-java-intellij/local-debug-functions-break.png)

## <a name="deploy-your-project-to-azure"></a>Distribuera ditt projekt till Azure

1. Högerklicka på projektet i IntelliJ Project Explorer och välj *Azure-> distribuera till Azure Functions*

    ![Distribuera projektet till Azure](media/functions-create-first-java-intellij/deploy-functions-to-azure.png)

1. Om du inte har några Funktionsapp än klickar du på *+* *funktions* raden. Skriv in namnet på appens funktion och välj rätt plattform. Här kan vi bara acceptera standard. Klicka på *OK* så markeras den nya Function-appen som du nyss skapade automatiskt. Klicka på *Kör* för att distribuera dina funktioner.

    ![Skapa Function-app i Azure](media/functions-create-first-java-intellij/deploy-functions-create-app.png)

    ![Distribuera Function-appen till Azure-loggen](media/functions-create-first-java-intellij/deploy-functions-log.png)

## <a name="manage-function-apps-from-idea"></a>Hantera Function-appar från idé

1. Du kan hantera dina funktions program med *Azure Explorer* i din idé, klicka på *Funktionsapp* så visas alla dina funktions program här.

    ![Visa funktions program i Utforskaren](media/functions-create-first-java-intellij/explorer-view-functions.png)

1. Klicka för att välja en av dina funktions program och högerklicka och välj *Visa egenskaper* för att öppna informations sidan. 

    ![Visa egenskaper för Function-app](media/functions-create-first-java-intellij/explorer-functions-show-properties.png)

1. Högerklicka på *HttpTrigger-Java-* Function-appen och välj *funktionen utlösare* så ser du att webbläsaren öppnas med Utlösar-URL: en.

    ![Skärm bild som visar en webbläsare med U R L.](media/functions-create-first-java-intellij/explorer-trigger-functions.png)

## <a name="add-more-functions-to-the-project"></a>Lägg till fler funktioner i projektet

1. Högerklicka på paketet *org. exempel. Functions* och välj *New-> Azure Function-klass*. 

    ![Lägg till funktioner till projekt posten](media/functions-create-first-java-intellij/add-functions-entry.png)

1. Fyll i klass namnet *HttpTest* och välj *HttpTrigger* i guiden skapa funktions klass. Klicka på *OK* för att skapa. på det här sättet kan du skapa nya funktioner som du vill.

    ![Skärm bild som visar dialog rutan skapa funktions klass.](media/functions-create-first-java-intellij/add-functions-trigger.png)
    
    ![Lägga till funktioner i projektets utdata](media/functions-create-first-java-intellij/add-functions-output.png)

## <a name="cleaning-up-functions"></a>Rensar funktioner

1. Ta bort funktioner i Azure Explorer
      
      ![Skärm bild som visar ta bort markerade från en snabb meny.](media/functions-create-first-java-intellij/delete-function.png)
      

## <a name="next-steps"></a>Nästa steg

Du har skapat ett Java-projekt med en HTTP-utlöst funktion, kört den på din lokala dator och distribuerat den till Azure. Nu kan du utöka din funktion efter...

> [!div class="nextstepaction"]
> [Lägga till en Azure Storage utgående bindning för kö](./functions-add-output-binding-storage-queue-java.md)


[marketplace]:./media/functions-create-first-java-intellij/marketplace.png
[intellij-azure-login]: media/functions-create-first-java-intellij/intellij-azure-login.png
[intellij-azure-popup]: media/functions-create-first-java-intellij/intellij-azure-login-popup.png
[intellij-azure-copycode]: media/functions-create-first-java-intellij/intellij-azure-login-copyopen.png
[intellij-azure-link-ms-account]: media/functions-create-first-java-intellij/intellij-azure-login-linkms-account.png
[intellij-azure-login-select-subs]: media/functions-create-first-java-intellij/intellij-azure-login-selectsubs.png
