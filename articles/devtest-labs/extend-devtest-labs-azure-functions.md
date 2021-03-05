---
title: Utöka Azure DevTest Labs med Azure Functions | Microsoft Docs
description: Lär dig hur du utökar Azure DevTest Labs med hjälp av Azure Functions.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 7a51f0935540df18cfb8805902bbe2c4ec365291
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203682"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>Använda Azure Functions för att utöka DevTest Labs
Du kan använda Azure Functions för att stödja ytterligare scenarier utöver de som redan stöds av DevTest Labs. Azure Functions kan användas för att utöka de inbyggda funktionerna i tjänsten för att möta dina verksamhets specifika behov. I följande lista finns några möjliga scenarier. Den här artikeln visar hur du implementerar ett av dessa exempel scenarier.

- Tillhandahålla en sammanfattning på den översta nivån för virtuella datorer i labbet
- [Konfigurera ett labb för användning med en fjärrskrivbords-gateway](configure-lab-remote-desktop-gateway.md)
- Kompatibilitetsrapport på sidan intern support
- Gör det möjligt för användare att slutföra åtgärder som kräver ökad behörighet i prenumerationen
- [Starta arbets flöden baserat på DevTest Labs-händelser](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>Översikt
[Azure Functions](../azure-functions/functions-overview.md) är en server lös data behandlings plattform i Azure. Genom att använda Azure Functions i en lösning med DevTest Labs kan vi utöka de befintliga funktionerna med vår egen anpassade kod. Mer information om Azure Functions finns i [Azure Functions dokumentation](../azure-functions/functions-overview.md). För att illustrera hur Azure Functions kan hjälpa dig att uppfylla dina krav eller slutföra scenarier i DevTest Labs, använder den här artikeln ett exempel på att tillhandahålla en toppnivå Sammanfattning av virtuella datorer i labbet på följande sätt:

**Exempel krav/scenario**: användarna kan se information om alla virtuella datorer i ett labb, inklusive operativ system, ägare och eventuella artefakter som används.  Dessutom är det ett enkelt sätt att tillämpa en artefakt för att **tillämpa Windows-uppdateringar** som inte har tillämpats nyligen.

För att slutföra scenariot kommer du att använda två funktioner som beskrivs i följande diagram:  

![Övergripande flöde](./media/extend-devtest-labs-azure-functions/flow.png)

Käll koden för dessa exempel funktioner finns i [DevTest Labs GitHub-lagringsplatsen](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions) (både C#-och PowerShell-implementeringar är tillgängliga).

- **UpdateInternalSupportPage**: den här funktionen frågar DevTest Labs och uppdaterar den interna support sidan direkt med information om de virtuella datorerna.
- **ApplyWindowsUpdateArtifact**: den här funktionen tillämpar **Windows Update** -ARTEFAKTEN för en virtuell dator i ett labb.

## <a name="how-it-works"></a>Så här fungerar det
När användarna väljer den **interna support** sidan i DevTest Labs har de en förifylld sida med information om virtuella datorer, labb ägare och support kontakter.  

När du väljer knappen **Välj här för att uppdatera** anropar sidan den första Azure-funktionen: **UpdateInternalSupportPage**. Funktionen frågar DevTest Labs efter information och skriver sedan om den **interna support** sidan med den nya informationen.

Det finns ytterligare en åtgärd som kan vidtas för virtuella datorer där Windows Update artefakter inte har använts nyligen, det finns en knapp för att tillämpa Windows-uppdateringar på den virtuella datorn. När du väljer ***kör Windows Update** -knappen för en virtuell dator anropar sidan den andra Azure-funktionen: **ApplyWindowsUpdateArtifact**. Den här funktionen kontrollerar om den virtuella datorn körs och använder [Windows Update](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates) artefakten direkt.

## <a name="step-by-step-walkthrough"></a>Steg för steg-genom gång
Det här avsnittet innehåller stegvisa instruktioner för hur du konfigurerar de Azure-resurser som krävs för att uppdatera den **interna support** sidan. Den här genom gången innehåller ett exempel på att utöka DevTest Labs. Du kan använda det här mönstret för andra scenarier.

### <a name="step-1-create-a-service-principal"></a>Steg 1: skapa ett huvud namn för tjänsten 
Det första steget är att hämta ett huvud namn för tjänsten med behörighet till den prenumeration som innehåller labbet. Tjänstens huvud namn måste använda lösenordsbaserad autentisering. Det kan göras med [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli), [Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps?view=azps-2.5.0)eller [Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md). Om du redan har ett huvud namn för tjänsten kan du hoppa över det här steget.

Anteckna **program-ID**, **nyckel** och **klient-ID** för tjänstens huvud namn. Du kommer att behöva dem senare i den här genom gången. 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>Steg 2: Hämta exemplet och öppna i Visual Studio 2019
Ladda ned en kopia av [C# Azure Functions exemplet](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp) lokalt (antingen genom att klona lagrings platsen eller hämta lagrings platsen [härifrån).](https://github.com/Azure/azure-devtestlab/archive/master.zip)  

1. Öppna exempel lösningen med Visual Studio 2019.  
1. Installera arbets belastningen **Azure Development** för Visual Studio om du inte redan har den installerad. Det kan installeras via **verktyg**  ->  -menyn **Hämta verktyg och funktioner** ).

    ![Arbets belastning för Azure-utveckling](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. Skapa lösningen. Välj **build** och sedan meny alternativ för **lösning** .

### <a name="step-3-deploy-the-sample-to-azure"></a>Steg 3: Distribuera exemplet till Azure
I Visual Studio, i fönstret **Solution Explorer** högerklickar du på projektet **AzureFunctions** och väljer sedan **publicera**. Följ guiden för att slutföra publiceringen till antingen en ny eller en befintlig Azure-Funktionsapp. Detaljerad information om hur du utvecklar och distribuerar Azure Functions med hjälp av Visual Studio finns i [utveckla Azure Functions med Visual Studio](../azure-functions/functions-develop-vs.md).

![Dialog rutan publicera](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>Steg 4: samla in program inställningar
När funktionerna har publicerats måste du hämta URL: er för dessa funktioner från Azure Portal. 

1. Navigera till [Azure Portal](https://portal.azure.com). 
1. Hitta Function-appen.
1. På sidan **Function Apps** väljer du funktionen. 
1. Välj **Hämta funktions webb adress** som visas i följande bild. 

    ![URL: er för Azure Functions](./media/extend-devtest-labs-azure-functions/function-url.png)
4. Kopiera och spara URL: en. Upprepa de här stegen för den andra Azure-funktionen. 

Du behöver också ytterligare information om tjänstens huvud namn, till exempel program-ID, nyckel och klient-ID.


### <a name="step-5--update-application-settings"></a>Steg 5: uppdatera program inställningar
När du har publicerat Azure-funktionen i Visual Studio väljer du **redigera Azure App Service inställningar** under **åtgärder**. Uppdatera följande program inställningar (fjärran sluten):

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- WindowsUpdateAllowedDays (standardvärdet 7)
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![Programinställningar](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>Steg 6: testa Azure-funktionen
Det sista steget i den här genom gången är att testa Azure-funktionen.  

1. Navigera till funktionen **UpdateInternalSupportPage** i Function-appen som skapats i steg 3. 
1. Välj **testa** på höger sida av sidan. 
1. Ange i Route-egenskaperna (LABNAME, RESOURCEGROUPNAME och SUBSCRIPTIONID).
1. Välj **Kör** för att köra funktionen.  

    Den här funktionen kommer att uppdatera den interna support sidan för det angivna labbet. Den innehåller också en knapp för användarna att direkt anropa funktionen nästa gången

    ![Test funktion](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>Nästa steg
Azure Functions kan förbättra funktionerna i DevTest Labs utöver vad som redan är inbyggt och hjälpa kunder att uppfylla sina unika krav för sina team. Det här mönstret kan utökas & utökas ytterligare för att omfatta ännu mer.  Mer information om DevTest Labs finns i följande artiklar: 

- [Referens arkitektur för DevTest Labs Enterprise](devtest-lab-reference-architecture.md)
- [Vanliga frågor och svar](devtest-lab-faq.md)
- [Skala upp DevTest Labs](devtest-lab-guidance-scale.md)
- [Automatisera DevTest Labs med PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)








