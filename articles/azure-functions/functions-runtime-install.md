---
title: Körmiljö för Azure Functions installation
description: Så här installerar du Körmiljö för Azure Functions Preview 2
author: apwestgarth
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 73b9533fa00c783e1cfb85270198f5c00c66afd5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86540391"
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>Installera Körmiljö för Azure Functions för hands version 2

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Följ dessa steg om du vill installera Körmiljö för Azure Functions Preview 2:

1. Se till att datorn uppfyller minimi kraven.
1. Hämta [körmiljö för Azure Functions för hands versions installation](https://aka.ms/azafrv2).
1. Avinstallera Körmiljö för Azure Functions för hands version 1.
1. Installera Körmiljö för Azure Functions för hands version 2.
1. Slutför konfigurationen av Körmiljö för Azure Functions för hands version 2.
1. Skapa din första funktion i Körmiljö för Azure Functions för hands version

## <a name="prerequisites"></a>Förutsättningar

Innan du installerar Körmiljö för Azure Functions för hands versionen måste du ha följande resurser tillgängliga:

1. En dator som kör Microsoft Windows Server 2016 eller Microsoft Windows 10 Creators Update (Professional eller Enterprise Edition).
1. En SQL Server instans som körs i nätverket.  Den minsta version som krävs är SQL Server Express.

## <a name="uninstall-previous-version"></a>Avinstallera tidigare version

Om du tidigare har installerat Körmiljö för Azure Functions för hands versionen måste du avinstallera innan du installerar den senaste versionen.  Avinstallera Körmiljö för Azure Functions för hands versionen genom att ta bort programmet i Lägg till/ta bort program i Windows.

## <a name="install-the-azure-functions-runtime-preview"></a>Installera Körmiljö för Azure Functions för hands version

Installations programmet för förhands granskning Körmiljö för Azure Functions vägleder dig genom installationen av Körmiljö för Azure Functions för hands versions hantering och arbets roller.  Det går att installera hanterings-och arbets rollen på samma dator.  Men när du lägger till fler Function-appar måste du distribuera fler arbets roller på ytterligare datorer för att kunna skala dina funktioner till flera arbetare.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Installera hanterings-och arbets rollen på samma dator

1. Kör installations programmet för Körmiljö för Azure Functions för hands version.

    ![Körmiljö för Azure Functions för hands versions installation][1]

1. Klicka på **Nästa**.
1. När du har läst villkoren i **licens avtalet** markerar du **kryss rutan** för att godkänna villkoren och klickar sedan på **Nästa** för att gå vidare.
1. Välj de roller som du vill installera på den här datorn **funktions hanterings roll** och/eller **Functions Worker-roll** och klicka på **Nästa**.

    ![Körmiljö för Azure Functions för hands version-roll val][3]

    > [!NOTE]
    > Du kan installera **Functions Worker-rollen** på många andra datorer. Det gör du genom att följa dessa anvisningar och bara välja **Functions Worker-roll** i installations programmet.

1. Klicka på **Nästa** om du vill att **installations guiden för körmiljö för Azure Functions** startar installations processen på datorn.
1. När du är klar startar installations guiden **körmiljö för Azure Functions** konfigurations verktyget.

    ![Körmiljö för Azure Functions för hands versions installation slutförd][6]

    > [!NOTE]
    > Om du installerar på **Windows 10** och **behållar** funktionen inte har Aktiver **körmiljö för Azure Functions** ATS tidigare uppmanas du att starta om datorn för att slutföra installationen.

## <a name="configure-the-azure-functions-runtime"></a>Konfigurera Körmiljö för Azure Functions

För att slutföra installationen av Körmiljö för Azure Functions måste du slutföra konfigurationen.

1. Konfigurations verktyget för **körmiljö för Azure Functions** visar vilka roller som är installerade på datorn.

    ![Körmiljö för Azure Functions för hands versions konfiguration][7]

1. Klicka på fliken **databas** , ange anslutnings information för din SQL Server instans, inklusive att ange en [huvud nyckel för databasen](/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine)och klicka på **Använd**.  Anslutning till en SQL Server instans krävs för att Körmiljö för Azure Functions ska kunna skapa en databas för att stödja körnings miljön.

    ![Körmiljö för Azure Functions förhandsgranska databas konfiguration][8]

1. Klicka på fliken **autentiseringsuppgifter** .  Här måste du skapa två nya autentiseringsuppgifter för användning med en fil resurs som är värd för alla dina funktions program.  Ange kombinationer av **användar namn** och **lösen ord** för **fil resursens ägare** och för **fil resurs användaren** och klicka sedan på **Använd**.

    ![Körmiljö för Azure Functions för hands versions inloggning][9]

1. Klicka på fliken **fil resurs** .  Här måste du ange information om fil resursens plats.  Fil resursen kan skapas åt dig eller så kan du använda en befintlig fil resurs och klicka på **Använd**.  Om du väljer en ny fil resurs plats måste du ange en katalog som ska användas av Körmiljö för Azure Functions.

    ![Körmiljö för Azure Functions för hands versions fil resurs][10]

1. Klicka på fliken **IIS** .  Den här fliken visar information om de webbplatser i IIS som Körmiljö för Azure Functions konfigurations verktyget skapar.  Du kan ange ett anpassat DNS-namn här för Körmiljö för Azure Functions Preview-portalen.  Klicka på **tillämpa** för att slutföra.

    ![Körmiljö för Azure Functions förhandsgranska IIS][11]

1. Klicka på fliken **tjänster** .  På den här fliken visas statusen för tjänsterna i Körmiljö för Azure Functions konfigurations verktyget.  Om  **Azure Functions Host Activation Service** inte körs efter den inledande konfigurationen klickar du på **Starta tjänst**.

    ![Körmiljö för Azure Functions för hands versions konfiguration slutförd][12]

1. Bläddra till **körmiljö för Azure Functions Portal** som `https://<machinename>.<domain>/` .

    ![Körmiljö för Azure Functions för hands versions portalen][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Skapa din första funktion i Körmiljö för Azure Functions för hands version

Skapa din första funktion i Körmiljö för Azure Functions för hands version

1. Bläddra till **körmiljö för Azure Functions Portal** till `https://<machinename>.<domain>` exempel `https://mycomputer.mydomain.com` .

1. Du uppmanas att **Logga** in, om distribuerat i en domän använder ditt domän konto användar namn och lösen ord, annars använder du ditt lokala konto användar namn och lösen ord för att logga in på portalen.

    ![Körmiljö för Azure Functions förhandsgranska Portal inloggning][14]

1. Du måste skapa en prenumeration för att kunna skapa Function-appar.  Klicka på **+** alternativet bredvid prenumerationerna i det övre vänstra hörnet i portalen.

    ![Körmiljö för Azure Functions för hands versions Portal prenumerationer][15]

1. Välj **DefaultPlan**, ange ett namn för din prenumeration och klicka på **skapa**.

    ![Körmiljö för Azure Functions prenumerations plan och namn för Preview-portalen][16]

1. Alla dina funktions-appar visas i den vänstra rutan i portalen.  Om du vill skapa en ny Funktionsapp väljer du rubrik **funktionen appar** och klickar på **+** alternativet.

1. Ange ett namn för din Function-app, Välj rätt prenumeration, Välj vilken version av Azure Functions runtime du vill program vara mot och klicka på **skapa**

    ![Körmiljö för Azure Functions Preview Portal ny function-app][17]

1. Din nya Function-app visas i den vänstra rutan i portalen.  Välj Functions och klicka sedan på **ny funktion** överst i mittenfönstret i portalen.

    ![Körmiljö för Azure Functions för hands versions mallar][18]

1. Välj funktionen timer-utlösare i namnet på den högra utfällbara filen och ändra schemat till `*/5 * * * * *` (detta cron-uttryck gör att din timer-funktion körs var femte sekund) och klickar på **skapa**

    ![Körmiljö för Azure Functions förhandsgranska ny konfiguration av timer-funktionen][19]

1. Din funktion har nu skapats.  Du kan visa körnings loggen för din Function-app genom att expandera rutan **logg** längst ned i portalen.

    ![Körmiljö för Azure Functions för hands versions funktionen körs][20]

<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-Progress.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer6-InstallComplete.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[13]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png
[14]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Login.png
[15]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions.png
[16]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions1.png
[17]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewFunctionApp.png
[18]: ./media/functions-runtime-install/AzureFunctionsRuntime_v1FunctionsTemplates.png
[19]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewTimerFunction.png
[20]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_RunningV2Function.png
