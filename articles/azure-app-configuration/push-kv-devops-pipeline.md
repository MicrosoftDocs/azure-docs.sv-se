---
title: Push-inställningar till app-konfiguration med Azure-pipeline
description: Lär dig att använda Azure-pipelines för att skicka nyckel värden till ett konfigurations lager för appar
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 02/23/2021
ms.author: alkemper
ms.openlocfilehash: 7d343e07414dd1c3f9786c1684eb6f14d5f45e51
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101718190"
---
# <a name="push-settings-to-app-configuration-with-azure-pipelines"></a>Push-inställningar till app-konfiguration med Azure-pipeline

Med push-åtgärden för [Azure App konfiguration](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) skickas nyckel värden från en konfigurations fil till appens konfigurations arkiv. Den här aktiviteten möjliggör fullständig cirkel funktion i pipelinen eftersom du nu kan hämta inställningar från appens konfigurations Arkiv och push-inställningar till appens konfigurations arkiv.

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- Konfigurations resurs för app – skapa en kostnads fri i [Azure Portal](https://portal.azure.com).
- Azure DevOps-projekt – [skapa ett kostnads fritt](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Azure App konfiguration av push-uppgift – Ladda ned kostnads fritt från [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push).

## <a name="create-a-service-connection"></a>Skapa en tjänst anslutning

Med en [tjänst anslutning](/azure/devops/pipelines/library/service-endpoints) kan du komma åt resurser i din Azure-prenumeration från ditt Azure DevOps-projekt.

1. I Azure DevOps går du till projektet som innehåller din mål-pipeline och öppnar **projekt inställningarna** längst ned till vänster.
1. Under **pipelines** väljer du **tjänst anslutningar** och sedan **ny tjänst anslutning** längst upp till höger.
1. Välj **Azure Resource Manager**.
1. Välj **tjänstens huvud namn (automatiskt)**.
1. Fyll i din prenumeration och resurs. Ge din tjänst anslutning ett namn.

Nu när din tjänst anslutning har skapats hittar du namnet på tjänstens huvud namn som tilldelats den. Du lägger till en ny roll tilldelning till tjänstens huvud namn i nästa steg.

1. Gå till **Project Settings**  >  **service Connections**.
1. Välj den tjänst anslutning som du skapade i föregående avsnitt.
1. Välj **Hantera tjänstens huvud namn**.
1. Observera **visnings namnet** som visas.

## <a name="add-role-assignment"></a>Lägg till rolltilldelning

Tilldela rätt roll tilldelningar för program konfigurationen till de autentiseringsuppgifter som används i aktiviteten så att aktiviteten kan komma åt appens konfigurations arkiv.

1. Navigera till konfigurations arkivet för mål programmet. 
1. Välj **åtkomst kontroll (IAM)** till vänster.
1. Längst upp väljer du **+ Lägg till** och välj **Lägg till roll tilldelning**.
1. Under **roll** väljer du **konfigurations data ägare för appar**. Med den här rollen kan aktiviteten läsa från och skriva till appens konfigurations arkiv. 
1. Välj det tjänst huvud namn som är associerat med den tjänst anslutning som du skapade i föregående avsnitt.
  
## <a name="use-in-builds"></a>Använd i versioner

Det här avsnittet beskriver hur du använder push-aktiviteten Azure App konfiguration i en pipeline för Azure DevOpss build.

1. Gå till sidan för att bygga pipeline genom att klicka på pipelines **pipelines**  >  . Dokumentation för att bygga pipelines finns [här](/azure/devops/pipelines/create-first-pipeline?tabs=tfs-2018-2).
      - Om du skapar en ny pipeline för bygge väljer du **Visa assistenten** på höger sida av pipelinen och söker efter push-aktiviteten **Azure App konfiguration** .
      - Om du använder en befintlig versions pipeline går du till fliken **aktiviteter** när du redigerar pipelinen och söker efter push-aktiviteten **Azure App konfiguration** .
2. Konfigurera de nödvändiga parametrarna för uppgiften för att push-överför nyckel värden från konfigurations filen till appens konfigurations arkiv. Parametern för **konfigurations filens sökväg** börjar i roten på fil lagrings platsen.
3. Spara och köa en version. Build-loggen visar eventuella fel som uppstod under körningen av aktiviteten.

## <a name="use-in-releases"></a>Använd i versioner

Det här avsnittet beskriver hur du använder push-aktiviteten Azure App konfiguration i en pipeline för Azure DevOps-utgåvor.

1. Gå till sidan Frisläpp pipeline genom att välja **pipelines**-  >  **versioner**. Dokumentation för lanserings pipelines finns [här](/azure/devops/pipelines/release).
1. Välj en befintlig versions pipeline. Om du inte har någon väljer du **+ ny** för att skapa en ny.
1. Klicka på knappen **Redigera** i det övre högra hörnet för att redigera lanserings pipelinen.
1. Välj **scenen** för att lägga till aktiviteten. Mer information om faser hittar du [här](/azure/devops/pipelines/release/environments).
1. Välj **+** för det jobbet och Lägg sedan till åtgärden för att lägga till **Azure App konfiguration** under fliken **distribuera** .
1. Konfigurera de nödvändiga parametrarna i aktiviteten för att skicka dina nyckel värden från konfigurations filen till appens konfigurations arkiv. Förklaringar av parametrarna finns i avsnittet **parametrar** nedan och i knapp beskrivningar bredvid varje parameter.
1. Spara och köa en version. I versions loggen visas eventuella fel som påträffas under körningen av aktiviteten.

## <a name="parameters"></a>Parametrar

Följande parametrar används av app Configurations push-aktivitet:

- **Azure-prenumeration**: en listruta som innehåller dina tillgängliga Azure Service-anslutningar. Om du vill uppdatera och uppdatera din lista över tillgängliga Azure-tjänst anslutningar trycker du på knappen **Uppdatera Azure-prenumeration** till höger om text rutan.
- **Konfigurations namn för app**: en listruta som läser in dina tillgängliga konfigurations lager under den valda prenumerationen. Om du vill uppdatera och uppdatera listan över tillgängliga konfigurations lager trycker du på knappen **Uppdatera appens konfigurations namn** till höger om text rutan.
- **Sökväg till konfigurations fil**: sökvägen till konfigurations filen. Du kan bläddra igenom din versions artefakt för att välja en konfigurations fil. ( `...` knappen till höger om text rutan). De fil format som stöds är: yaml, JSON, Properties.
- **Avgränsare**: avgränsare som används för att förenkla. JSON-och. yml-filer.
- **Djup**: det djup som. JSON-och. yml-filerna kommer att förenkla.
- **Prefix**: en sträng som läggs till i början av varje nyckel som skickas till appens konfigurations arkiv.
- **Label**: en sträng som har lagts till i varje nyckel värde som etikett i appens konfigurations arkiv.
- **Innehålls typ**: en sträng som läggs till i varje nyckel värde som innehålls typen i appens konfigurations arkiv.
- **Taggar**: ett JSON-objekt i formatet `{"tag1":"val1", "tag2":"val2"}` , som definierar taggar som läggs till varje nyckel värde som skickas till appens konfigurations lager.
- **Ta bort alla andra Key-Values i Store med angivet prefix och etikett**: standardvärdet är **avmarkerat**.
  - **Markerad**: tar bort alla nyckel värden i appens konfigurations arkiv som matchar både det angivna prefixet och etiketten innan nya nyckel värden flyttas från konfigurations filen.
  - **Avmarkerad**: push-överför alla nyckel värden från konfigurations filen till appens konfigurations Arkiv och lämnar allt annat i appens konfigurations Arkiv intakt.

När du har fyllt i nödvändiga parametrar kör du pipelinen. Alla nyckel värden i den angivna konfigurations filen laddas upp till app-konfigurationen.

## <a name="troubleshooting"></a>Felsökning

Om ett oväntat fel inträffar kan fel söknings loggar aktive ras genom att ställa in pipeline-variabeln `system.debug` till `true` .

## <a name="faq"></a>Vanliga frågor

**Hur kan jag ladda upp flera konfigurationsfiler?**

Skapa flera instanser av push-aktiviteten för Azure App konfiguration inom samma pipeline för att skicka flera konfigurationsfiler till app Configuration Store.

**Varför får jag ett 409-fel när jag försöker skicka nyckel värden till mitt konfigurations Arkiv?**

Ett fel meddelande om 409 i konflikt inträffar om aktiviteten försöker ta bort eller skriva över ett nyckel värde som är låst i appens konfigurations arkiv.
