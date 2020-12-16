---
title: Använd käll kontroll integrering i Azure Automation-bakåtkompatibelt
description: Den här artikeln beskriver hur du använder käll kontrolls integrering.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2019
ms.topic: conceptual
ms.openlocfilehash: 4dedbcf58e76b8c969f8607db6922e87a85f08e5
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591881"
---
# <a name="use-source-control-integration-in-azure-automation---legacy"></a>Använd käll kontroll integrering i Azure Automation-bakåtkompatibelt

> [!NOTE]
> Det finns en ny upplevelse för käll kontroll. Läs mer om den nya upplevelsen i [käll kontroll (för hands version)](source-control-integration.md).

Med käll kontrolls integrering kan du koppla Runbooks i ditt Automation-konto till en GitHub-databas för käll kontroll. Med käll kontroll kan du enkelt samar beta med ditt team, spåra ändringar och återställa till tidigare versioner av dina runbooks. Med käll kontroll kan du till exempel synkronisera olika grenar i käll kontrollen till dina utvecklings-, test-eller produktions Automation-konton, vilket gör det enkelt att befordra kod som har testats i utvecklings miljön till ditt produktions Automation-konto.

Med käll kontroll kan du skicka kod från Azure Automation till käll kontroll eller hämta dina runbooks från käll kontrollen till Azure Automation. I den här artikeln beskrivs hur du konfigurerar käll kontroll i Azure Automations miljön. Vi börjar med att konfigurera Azure Automation för att få åtkomst till din GitHub-lagringsplats och gå igenom olika åtgärder som kan utföras med hjälp av käll kontroll integrering. 

> [!NOTE]
> Käll kontrollen stöder hämtning och överföring av [PowerShell-arbetsflöden med Runbooks](automation-runbook-types.md#powershell-workflow-runbooks) samt PowerShell- [Runbooks](automation-runbook-types.md#powershell-runbooks). [Grafiska runbooks](automation-runbook-types.md#graphical-runbooks) stöds inte ännu.

## <a name="configure-source-control"></a>Konfigurera käll kontroll

Det finns två enkla steg som krävs för att konfigurera käll kontroll för ditt Automation-konto och bara ett om du redan har ett GitHub-konto. 

### <a name="create-a-github-repository"></a>Skapa en GitHub-lagringsplats

Om du redan har ett GitHub-konto och en databas som du vill länka till Azure Automation ska du logga in på ditt befintliga konto och börja från steg 2 nedan. Annars navigerar du till [GitHub](https://github.com/), registrerar dig för ett nytt konto och [skapar en ny lagrings plats](https://help.github.com/articles/create-a-repo/).

### <a name="set-up-source-control"></a>Konfigurera käll kontroll

1. På sidan Automation-konto i Azure Portal klickar du på **käll kontroll** under **konto inställningar**.

2. Sidan käll kontroll öppnas där du kan konfigurera dina GitHub-konto uppgifter. Nedan visas en lista över de parametrar som du kan konfigurera:  

   | **Parameter** | **Beskrivning** |
   |:--- |:--- |
   | Välj källa |Välj källa. För närvarande stöds endast **GitHub** . |
   | Auktorisering |Klicka på knappen **auktorisera** för att ge Azure Automation åtkomst till din GitHub-lagringsplats. Om du redan är inloggad på ditt GitHub-konto i ett annat fönster används autentiseringsuppgifterna för kontot. När auktoriseringen är klar visar sidan ditt GitHub-användarnamn under **egenskapen Authorization**. |
   | Välj databas |Välj en GitHub-lagringsplats från listan över tillgängliga databaser. |
   | Välj gren |Välj en gren i listan över tillgängliga grenar. Endast **huvud** grenen visas om du inte har skapat några grenar. |
   | Sökväg till Runbook-mapp |Sökvägen till Runbook-mappen anger sökvägen i GitHub-lagringsplatsen som du vill skicka eller ta emot din kod från. Den måste anges i formatet **/FolderName/subfoldername**. Endast Runbooks i sökvägen till Runbook-mappen kommer att synkroniseras med ditt Automation-konto. Runbooks i undermapparna i sökvägen till Runbook-mappen kommer **inte** att synkroniseras. Används **/** för att synkronisera alla Runbooks under lagrings platsen. |
3. Om du till exempel har en lagrings plats med namnet **PowerShellScripts** som innehåller en mapp med namnet **RootFolder**, som innehåller en mapp med namnet **undermapp.** Du kan använda följande strängar för att synkronisera varje mappnivå:

   1. Om du vill synkronisera Runbooks från **lagrings platsen** för Runbook-mappar är Runbook-mappsökvägen **/** .
   2. För att synkronisera Runbooks från **RootFolder** är sökvägen till Runbook-mappen **/RootFolder**.
   3. För att synkronisera Runbooks från **undermappar** är sökvägen till Runbook-mappen **/RootFolder/SubFolder**.
4. När du har konfigurerat parametrarna visas de på sidan Konfigurera käll kontroll.  

    ![Sidan käll kontroll med inställningarna](media/source-control-integration-legacy/automation-SourceControlConfigure.png)
5. När du klickar på **OK** är käll kontroll integrering nu konfigurerad för ditt Automation-konto och bör uppdateras med din GitHub-information. Nu kan du klicka på den här delen för att visa all din käll kontroll för synkronisering av jobb historik.  

    ![Värden för den aktuella konfigurerade käll kontroll konfigurationen](media/source-control-integration-legacy/automation-RepoValues.png)
6. När du har konfigurerat käll kontroll skapas två [variabel till gångar](./shared-resources/variables.md) i ditt Automation-konto. Dessutom läggs ett auktoriserat program till i ditt GitHub-konto.

   * Variabeln **Microsoft. Azure. Automation. SourceControl. Connection** innehåller värdena för anslutnings strängen, som visas nedan.  

     | **Parameter** | **Värde** |
     |:--- |:--- |
     | `Name`  |Microsoft. Azure. Automation. SourceControl. Connection |
     | `Type`  |Sträng |
     | `Value` |{"Gren": \<*Your branch name*> , "RunbookFolderPath": \<*Runbook folder path*> , "ProviderType": \<*has a value 1 for GitHub*> , "databas": \<*Name of your repository*> , "username": \<*Your GitHub user name*> } |

   * Variabeln **Microsoft. Azure. Automation. SourceControl. OAuthToken** innehåller det säkra krypterade värdet för din OAuthToken.  

     |**Parameter**            |**Värde** |
     |:---|:---|
     | `Name`  | `Microsoft.Azure.Automation.SourceControl.OAuthToken` |
     | `Type`  | `Unknown(Encrypted)` |
     | `Value` | <`Encrypted OAuthToken`> |  

     ![Ett fönster som visar variabler för käll kontroll](media/source-control-integration-legacy/automation-Variables.png)  

   * **Kontroll av Automation-källa** läggs till som ett auktoriserat program till ditt GitHub-konto. Om du vill visa programmet går du till GitHub start sida och navigerar till **profil**  >  **inställnings**  >  **program**. Det här programmet tillåter Azure Automation att synkronisera din GitHub-lagringsplats med ett Automation-konto.  

     ![Program inställningar i GitHub](media/source-control-integration-legacy/automation-GitApplication.png)

## <a name="use-source-control-in-automation"></a>Använd käll kontroll i Automation

Med Runbook checka in kan du skicka de ändringar du har gjort till en Runbook i Azure Automation till lagrings platsen för käll kontroll. Nedan visas stegen för att checka in en Runbook:

1. Från ditt Automation-konto [skapar du en ny text Runbook](./learn/automation-tutorial-runbook-textual.md)eller [redigerar en befintlig, text-Runbook](automation-edit-textual-runbook.md). Denna Runbook kan vara antingen ett PowerShell-arbetsflöde eller en PowerShell-skript Runbook.  
2. När du har redigerat din Runbook sparar du den och klickar på **checka** in på sidan Redigera.  

    ![Ett fönster som visar knappen för att checka in till GitHub](media/source-control-integration-legacy/automation-CheckinButton.png)

     > [!NOTE] 
     > Checka in från Azure Automation skriver över den kod som för närvarande finns i käll kontrollen. Den git-motsvarande kommando rads instruktionen att checka in är **git Add + git commit + git-push**  

3. När du klickar på **checka in** uppmanas du att bekräfta genom att klicka på **Ja** för att fortsätta.  

    ![En dialog ruta som bekräftar incheckningen av käll kontrollen](media/source-control-integration-legacy/automation-CheckinMessage.png)
4. Checka in startar käll kontrollens Runbook: **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Denna Runbook ansluter till GitHub och skickar ändringar från Azure Automation till din lagrings plats. Om du vill visa den incheckade jobb historiken går du tillbaka till fliken **käll kontrolls integrering** och klickar för att öppna sidan synkronisering av databas. På den här sidan visas alla käll kontroll jobb. Välj det jobb som du vill visa och klicka på för att visa information.  

    ![Ett fönster som visar resultatet av ett synkroniseringsjobb](media/source-control-integration-legacy/automation-CheckinRunbook.png)

   > [!NOTE]
   > Runbooks med käll kontroll är särskilda Automation-runbooks som du inte kan visa eller redigera. Även om de inte visas i din Runbook-lista kan du se synkroniseringsjobb som visas i listan med jobb.

5. Namnet på den ändrade Runbook-flödet skickas som en indataparameter för den incheckade runbooken. Du kan [Visa jobb informationen](automation-runbook-execution.md#job-statuses) genom att expandera Runbook på sidan för synkronisering av databasen.  

    ![Ett fönster som visar InInformationen för ett synkroniseringsjobb](media/source-control-integration-legacy/automation-CheckinInput.png)
6. Uppdatera din GitHub-lagringsplats när jobbet har slutförts för att visa ändringarna.  Det bör finnas ett genomförande i lagrings platsen med ett bekräftelse meddelande: **uppdaterat *Runbook-namn* i Azure Automation.**  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Synkronisera Runbooks från käll kontroll till Azure Automation

Med knappen Synkronisera på sidan lagringsplats för lagring kan du hämta alla Runbooks i sökvägen för Runbook-mappen för din lagrings plats till ditt Automation-konto. Samma lagrings plats kan synkroniseras med fler än ett Automation-konto. Nedan visas stegen för att synkronisera en Runbook:

1. Från Automation-kontot där du ställer in käll kontroll öppnar du sidan för synkronisering av käll kontroll/databas och klickar på **Synkronisera**.  Du uppmanas att ange ett bekräftelse meddelande. Klicka på **Ja** om du vill fortsätta.  

    ![Knappen Synkronisera med meddelandet bekräftar att alla Runbooks kommer att synkroniseras](media/source-control-integration-legacy/automation-SyncButtonwithMessage.png)

2. Sync startar  **MicrosoftAzureAutomationAccountFromGitHubV1** Runbook, som ansluter till GitHub och hämtar ändringarna från lagrings platsen till Azure Automation. Du bör se ett nytt jobb på sidan för synkronisering av databasen för den här åtgärden. Om du vill visa information om synkroniseringsjobb klickar du på för att öppna sidan jobb information.  

    ![Ett fönster som visar synkroniseringsresultat för ett synkroniseringsjobb på en GitHub-lagringsplats](media/source-control-integration-legacy/automation-SyncRunbook.png)

    > [!NOTE]
    > En synkronisering från käll kontroll skriver över utkast versionen av Runbooks som för närvarande finns i ditt Automation-konto för **alla** Runbooks som för närvarande är i käll kontroll. Den git-likvärdiga kommando rads instruktionen som ska synkroniseras är **git pull**

![Ett fönster som visar alla loggar från ett inaktiverat synkroniseringsjobb för käll kontroll](media/source-control-integration-legacy/automation-AllLogs.png)

## <a name="disconnect-source-control"></a>Koppla från käll kontroll

Om du vill koppla från ditt GitHub-konto öppnar du sidan databas synkronisering och klickar på **Koppla från**. När du kopplar från käll kontrollen förblir Runbooks som synkroniserades tidigare fortfarande kvar i ditt Automation-konto, men sidan för synkronisering av databasen är inte aktive rad.  

  ![Ett fönster som visar knappen Koppla från för att koppla från käll kontrollen](media/source-control-integration-legacy/automation-Disconnect.png)

## <a name="next-steps"></a>Nästa steg

* För att integrera käll kontroll i Azure Automation, se [Azure Automation: käll kontroll integrering i Azure Automation](https://azure.microsoft.com/blog/azure-automation-source-control-13/).  
* Information om hur du integrerar Runbook-åtkomstkontroll med Visual Studio Online finns i [Azure Automation: integrera Runbook-källkod med Visual Studio Online](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/).  
