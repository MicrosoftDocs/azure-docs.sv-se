---
title: Hämta settingsfromo-appen med Azure-pipeline
description: Lär dig att använda Azure-pipelines för att hämta nyckel värden till ett konfigurations lager för appar
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 11/17/2020
ms.author: drewbat
ms.openlocfilehash: 1c01984f6a359c0fd1f5d06d26d97d4a84973f57
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056795"
---
# <a name="pull-settings-to-app-configuration-with-azure-pipelines"></a>Hämta inställningar till app-konfiguration med Azure-pipeline

Den [Azure App konfigurations](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task) aktiviteten hämtar nyckel värden från appens konfigurations Arkiv och anger dem som Azure pipeline-variabler, som kan användas av efterföljande uppgifter. Den här uppgiften kompletterar den [Azure App push](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) -aktivitet för konfiguration som skickar nyckel värden från en konfigurations fil till konfigurations arkivet för appen. Mer information finns i [push-inställningar för att konfigurera appar med Azure-pipeliner](push-kv-devops-pipeline.md).

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- Konfigurations Arkiv för app – skapa ett kostnads fritt i [Azure Portal](https://portal.azure.com).
- Azure DevOps-projekt – [skapa ett kostnads fritt](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Azure App konfigurations uppgift – Ladda ned kostnads fritt från [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task#:~:text=Navigate%20to%20the%20Tasks%20tab,the%20Azure%20App%20Configuration%20instance.).  

## <a name="create-a-service-connection"></a>Skapa en tjänst anslutning

Med en [tjänst anslutning](/azure/devops/pipelines/library/service-endpoints) kan du komma åt resurser i din Azure-prenumeration från ditt Azure DevOps-projekt.

1. I Azure DevOps går du till projektet som innehåller din mål-pipeline och öppnar **projekt inställningarna** längst ned till vänster.
1. Under **pipelines** väljer du **tjänst anslutningar**.
1. Om du inte har några befintliga tjänst anslutningar klickar du på knappen **skapa tjänst anslutning** mitt på skärmen. Annars klickar du på **ny tjänst anslutning** längst upp till höger på sidan.
1. Välj **Azure Resource Manager**.
![Skärm bild som visar hur du väljer Azure Resource Manager från List rutan ny tjänst anslutning.](./media/new-service-connection.png)
1. I dialog rutan **autentiseringsmetod** väljer du **tjänstens huvud namn (automatiskt)**.
    > [!NOTE]
    > **Hanterad identitets** autentisering stöds för närvarande inte för app Configuration-aktiviteten.
1. Fyll i din prenumeration och resurs. Ge din tjänst anslutning ett namn.

Nu när din tjänst anslutning har skapats hittar du namnet på tjänstens huvud namn som tilldelats den. Du lägger till en ny roll tilldelning till tjänstens huvud namn i nästa steg.

1. Gå till **Project Settings**  >  **service Connections**.
1. Välj den tjänst anslutning som du skapade i föregående avsnitt.
1. Välj **Hantera tjänstens huvud namn**.
1. Observera **visnings namnet** som visas.

## <a name="add-role-assignment"></a>Lägg till rolltilldelning

Tilldela den korrekta program konfigurations rollen till den tjänst anslutning som används i aktiviteten så att den kan komma åt appens konfigurations arkiv.

1. Navigera till konfigurations arkivet för mål programmet. En genom gång av hur du konfigurerar ett konfigurations lager för appar finns i [skapa ett konfigurations lager för appar](./quickstart-dotnet-core-app.md#create-an-app-configuration-store) i en av Azure App snabb starter för konfiguration.
1. Välj **åtkomst kontroll (IAM)** till vänster.
1. Klicka på knappen **Lägg till roll tilldelningar** på höger sida.
![Skärm bild som visar knappen Lägg till roll tilldelningar. ](./media/add-role-assignment-button.png) ..
1. Välj **app Configuration Data Reader** under **roll**. Med den här rollen kan aktiviteten läsa från appens konfigurations arkiv. 
1. Välj det tjänst huvud namn som är associerat med den tjänst anslutning som du skapade i föregående avsnitt.
![Skärm bild som visar dialog rutan Lägg till roll tilldelning.](./media/add-role-assignment-reader.png)

> [!NOTE]
> För att lösa Azure Key Vault referenser inom appens konfiguration måste även tjänst anslutningen beviljas behörighet att läsa hemligheter i de refererade Azure Key Vault.
  
## <a name="use-in-builds"></a>Använd i versioner

Det här avsnittet beskriver hur du använder Azure App konfigurations aktivitet i en pipeline för Azure DevOpss build.

1. Gå till sidan för att bygga pipeline genom att klicka på pipelines **pipelines**  >  . Om du vill bygga pipeline-dokumentation, se  [skapa din första pipeline](/azure/devops/pipelines/create-first-pipeline?tabs=net%2Ctfs-2018-2%2Cbrowser).
      - Om du skapar en ny pipeline för build går du till det sista steget i processen och väljer **Visa assistent** på höger sida av pipelinen på fliken **Granska** .
      ![Skärm bild som visar knappen Visa assistent för en ny pipeline.](./media/new-pipeline-show-assistant.png)
      - Om du använder en befintlig versions pipeline klickar du på knappen **Redigera** längst upp till höger.
      ![Skärm bild som visar knappen Redigera för en befintlig pipeline.](./media/existing-pipeline-show-assistant.png)
1. Sök efter aktiviteten **Azure App konfiguration** .
![Skärm bild som visar dialog rutan Lägg till uppgift med Azure App konfiguration i rutan Sök.](./media/add-azure-app-configuration-task.png)
1. Konfigurera de nödvändiga parametrarna för uppgiften för att hämta nyckel värden från App Configuration Store. Beskrivningar av parametrarna finns i avsnittet **parametrar** nedan och i knapp beskrivningar bredvid varje parameter.
      - Ange parametern för **Azure-prenumerationen** till namnet på den tjänst anslutning som du skapade i föregående steg.
      - Ange **namnet på appens konfiguration** till resurs namnet för appens konfigurations arkiv.
      - Lämna standardvärdena för de återstående parametrarna.
![Skärm bild som visar aktivitets parametrarna för app Configuration.](./media/azure-app-configuration-parameters.png)
1. Spara och köa en version. Build-loggen visar eventuella fel som uppstod under körningen av aktiviteten.

## <a name="use-in-releases"></a>Använd i versioner

Det här avsnittet beskriver hur du använder Azure App konfigurations aktivitet i en pipeline för Azure DevOps release.

1. Gå till sidan Frisläpp pipeline genom att välja **pipelines**-  >  **versioner**. Mer information om versions pipelinen finns i [versions pipeliner](/azure/devops/pipelines/release).
1. Välj en befintlig versions pipeline. Om du inte har en sådan, klickar du på **ny pipeline** för att skapa en ny.
1. Klicka på knappen **Redigera** i det övre högra hörnet för att redigera lanserings pipelinen.
1. I list rutan **aktiviteter** väljer du den **fas** som du vill lägga till aktiviteten i. Mer information om faser hittar du [här](/azure/devops/pipelines/release/environments).
![Skärm bild som visar det valda steget i list rutan aktiviteter.](./media/pipeline-stage-tasks.png)
1. Klicka på **+** bredvid det jobb som du vill lägga till en ny aktivitet till.
![Skärm bild som visar plus knappen bredvid jobbet.](./media/add-task-to-job.png)
1. Sök efter aktiviteten **Azure App konfiguration** .
![Skärm bild som visar dialog rutan Lägg till uppgift med Azure App konfiguration i rutan Sök.](./media/add-azure-app-configuration-task.png)
1. Konfigurera de nödvändiga parametrarna i uppgiften för att hämta nyckel värden från appens konfigurations lager. Beskrivningar av parametrarna finns i avsnittet **parametrar** nedan och i knapp beskrivningar bredvid varje parameter.
      - Ange parametern för **Azure-prenumerationen** till namnet på den tjänst anslutning som du skapade i föregående steg.
      - Ange **namnet på appens konfiguration** till resurs namnet för appens konfigurations arkiv.
      - Lämna standardvärdena för de återstående parametrarna.
1. Spara och köa en version. I versions loggen visas eventuella fel som påträffas under körningen av aktiviteten.

## <a name="parameters"></a>Parametrar

Följande parametrar används av Azure App konfigurations åtgärden:

- **Azure-prenumeration**: en listruta som innehåller dina tillgängliga Azure Service-anslutningar. Om du vill uppdatera och uppdatera din lista över tillgängliga Azure-tjänst anslutningar trycker du på knappen **Uppdatera Azure-prenumeration** till höger om text rutan.
- **Konfigurations namn för app**: en listruta som läser in dina tillgängliga konfigurations lager under den valda prenumerationen. Om du vill uppdatera och uppdatera listan över tillgängliga konfigurations lager trycker du på knappen **Uppdatera appens konfigurations namn** till höger om text rutan.
- **Nyckel filter**: filtret kan användas för att välja vilka nyckel värden som begärs från Azure App-konfigurationen. Värdet * väljer alla nyckel värden. Mer information finns i [läsa nyckel värden](concept-key-value.md#query-key-values).
- **Label**: anger vilken etikett som ska användas när du väljer nyckel värden från appens konfigurations arkiv. Om ingen etikett anges hämtas nyckel värden med etiketten No. Följande tecken är inte tillåtna:, *.
- **Trimning av nyckel prefix**: anger ett eller flera prefix som ska trimmas från konfigurations nycklar för appar innan du anger dem som variabler. Flera prefix kan avgränsas med ett nytt rad avstånd.

## <a name="use-key-values-in-subsequent-tasks"></a>Använd nyckel värden i efterföljande uppgifter

De nyckel värden som hämtas från App-konfigurationen anges som pipeline-variabler, som är tillgängliga som miljövariabler. Nyckeln för miljövariabeln är nyckeln till det nyckel värde som hämtas från App-konfigurationen efter att prefixet har beskurits, om det har angetts.

Om till exempel en efterföljande aktivitet kör ett PowerShell-skript, kan det förbruka ett nyckel värde med nyckeln "myBuildSetting" som detta:
```powershell
echo "$env:myBuildSetting"
```
Värdet skrivs då till-konsolen.

> [!NOTE]
> Azure Key Vault referenser i appens konfiguration kommer att matchas och anges som [hemliga variabler](/azure/devops/pipelines/process/variables#secret-variables). I Azure-pipeliner maskeras de hemliga variablerna från loggen. De skickas inte till uppgifter som miljövariabler och måste i stället skickas som indata. 

## <a name="troubleshooting"></a>Felsökning

Om ett oväntat fel inträffar kan fel söknings loggar aktive ras genom att ställa in pipeline-variabeln `system.debug` till `true` .

## <a name="faq"></a>Vanliga frågor

**Hur gör jag för att skriva min konfiguration från flera nycklar och etiketter?**

Det finns tillfällen när konfigurationen kan behöva bestå av flera etiketter, till exempel standard och dev. Flera konfigurations uppgifter för appar kan användas i en pipeline för att implementera det här scenariot. De nyckel värden som hämtas av en uppgift i ett senare steg ersätter alla värden från föregående steg. I det tidigare exemplet kan en uppgift användas för att välja nyckel värden med standard etiketten medan en andra aktivitet kan välja nyckel värden med dev-etiketten. Nycklarna med dev-etiketten åsidosätter samma nycklar med standard etiketten.
