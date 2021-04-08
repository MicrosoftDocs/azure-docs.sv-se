---
title: Skicka e-post när dörren öppnas med Azure Functions
description: Övervaka magnet sensorn för att identifiera när en dörr öppnas och Använd Azure Functions för att skicka ett e-postmeddelande.
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: e3649ac786c07459695a1e6ca6ad52620e23d59f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92148792"
---
# <a name="door-monitor----using-azure-functions-and-sendgrid-send-email-when-a-door-is-opened"></a>Dörr övervakare – använda Azure Functions och SendGrid, skicka e-post när en dörr öppnas           

MXChip IoT-DevKit innehåller en inbyggd magnetisk sensor. I det här projektet kan du identifiera förekomsten eller frånvaron av ett närliggande starkt magnet fält – i det här fallet, som kommer från en liten, permanent magnet.

## <a name="what-you-learn"></a>Detta får du får lära dig

I det här projektet får du lära dig:
- Använda MXChip IoT DevKits magnetiska sensor för att identifiera förflyttning av en närliggande magnet.
- Så här använder du SendGrid-tjänsten för att skicka ett meddelande till din e-postadress.

> [!NOTE]
> Utför följande uppgifter för en praktisk användning av projektet:
> - Montera en magnet till en dörrs kant.
> - Montera DevKit på dörren Jamb nära magneten. När du öppnar eller stänger dörren utlöses sensorn, vilket innebär att du får ett e-postmeddelande om händelsen.

## <a name="what-you-need"></a>Detta behöver du

Slutför [komma igång guiden](iot-hub-arduino-iot-devkit-az3166-get-started.md) för att:

* Låt din DevKit vara ansluten till Wi-Fi
* Förbereda utvecklingsmiljön

En aktiv Azure-prenumeration. Om du inte har någon kan du registrera dig via någon av följande metoder:

* Aktivera ett [kostnads fritt 30-dagars utvärderings Microsoft Azure konto](https://azure.microsoft.com/free/).
* Anspråk på din [Azure-kredit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) om du är en MSDN-eller Visual Studio-prenumerant.

## <a name="deploy-the-sendgrid-service-in-azure"></a>Distribuera SendGrid-tjänsten i Azure

[SendGrid](https://sendgrid.com/) är en molnbaserad distributions plattform för e-post. Den här tjänsten kommer att användas för att skicka e-postaviseringar.

> [!NOTE]
> Om du redan har distribuerat en SendGrid-tjänst kan du fortsätta direkt för att [distribuera IoT Hub i Azure](#deploy-iot-hub-in-azure).

### <a name="sendgrid-deployment"></a>SendGrid-distribution

Använd knappen **distribuera till Azure** för att etablera Azure-tjänster. Med den här knappen kan du snabbt och enkelt distribuera dina projekt med öppen källkod till Microsoft Azure.

Klicka på knappen **distribuera till Azure** nedan. 

[![Distribuera till Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Logga in nu om du inte redan har loggat in på ditt Azure-konto. 

Nu visas registrerings formuläret SendGrid.

![SendGrid-distribution](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

Slutför registrerings formuläret:

   * **Resurs grupp**: skapa en resurs grupp som värd för SendGrid-tjänsten eller Använd en befintlig. Se [använda resurs grupper för att hantera dina Azure-resurser](../azure-resource-manager/management/manage-resource-groups-portal.md).

   * **Namn**: namnet på din SendGrid-tjänst. Välj ett unikt namn som skiljer sig från andra tjänster som du kan ha.

   * **Lösen ord**: tjänsten kräver ett lösen ord som inte kommer att användas för något i det här projektet.

   * **E-post**: SendGrid-tjänsten skickar verifieringen till den här e-postadressen.

Markera alternativet **Fäst på instrument panelen** för att göra det här programmet lättare att hitta i framtiden och klicka sedan på **köp** för att skicka in inloggnings formuläret.
 
### <a name="sendgrid-api-key-creation"></a>Skapa SendGrid API-nyckel

När distributionen är klar klickar du på den och sedan på knappen **Hantera** . Sidan ditt SendGrid-konto visas där du måste verifiera din e-postadress.

![Hantera SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

På sidan SendGrid klickar du på **Inställningar**  >  **API-nycklar**  >  **skapa API-nyckel**.

![SendGrid skapa API först](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

På sidan **skapa API-nyckel** , ange **API-nyckelns namn** och klicka på **skapa & vy**.

![SendGrid skapa API Second](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

Din API-nyckel visas bara en tid. Se till att kopiera och lagra det på ett säkert sätt, eftersom det används i nästa steg.

## <a name="deploy-iot-hub-in-azure"></a>Distribuera IoT Hub i Azure

Följande steg etablerar andra Azure IoT-relaterade tjänster och distribuerar Azure Functions för projektet.

Klicka på knappen **distribuera till Azure** nedan. 

[![Distribuera till Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

Registrerings formuläret visas.

![IoTHub-distribution](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

Fyll i fälten i registrerings formuläret.

   * **Resurs grupp**: skapa en resurs grupp som värd för SendGrid-tjänsten eller Använd en befintlig. Se [använda resurs grupper för att hantera dina Azure-resurser](../azure-resource-manager/management/manage-resource-groups-portal.md).

   * **IoT Hub-namn**: namnet på din IoT Hub. Välj ett unikt namn som skiljer sig från andra tjänster som du kan ha.

   * **IoT Hub SKU**: F1 (begränsad till en per prenumeration) är kostnads fri. Du kan se mer pris information på [sidan med priser](https://azure.microsoft.com/pricing/details/iot-hub/).

   * **Från e-post**: det här fältet ska vara samma e-postadress som du använde när du installerade SendGrid-tjänsten.

Markera alternativet **Fäst på instrument panelen** för att göra det här programmet lättare att hitta i framtiden och klicka sedan på **köp** när du är redo att fortsätta till nästa steg.
 
## <a name="build-and-upload-the-code"></a>Bygg och ladda upp koden

Läs sedan in exempel koden i VS Code och etablera nödvändiga Azure-tjänster.

### <a name="start-vs-code"></a>Starta VS-kod

- Kontrol lera att din DevKit **inte** är ansluten till din dator.
- Starta VS Code.
- Anslut DevKit till din dator.

> [!NOTE]
> När du startar VS Code kan du få ett fel meddelande om att det inte går att hitta Arduino IDE-eller relaterat Board-paketet. Om du får det här felet stänger du VS Code, startar Arduino IDE igen och VS Code ska hitta Arduino IDE-sökvägen korrekt.

### <a name="open-arduino-examples-folder"></a>Öppna mappen Arduino exempel

Expandera avsnittet exempel på vänster sida **ARDUINO** , bläddra till **exempel för MXCHIP AZ3166 > AzureIoT** och välj **DoorMonitor**. Den här åtgärden öppnar ett nytt VS Code-fönster med en projektmapp i den.

![Mini-lösning – exempel](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

Du kan också öppna exempel appen från kommando paletten. Använd `Ctrl+Shift+P` (MacOS: `Cmd+Shift+P` ) för att öppna kommando-paletten, Skriv **Arduino** och leta upp och välj **Arduino: exempel**.

### <a name="provision-azure-services"></a>Etablera Azure-tjänster

I lösnings fönstret kör du moln etablerings aktiviteten:
- Typ `Ctrl+P` (MacOS: `Cmd+P` ).
- Ange `task cloud-provision` i den angivna text rutan.

I VS Code-terminalen guidar en interaktiv kommando rad dig genom etableringen av de nödvändiga Azure-tjänsterna. Markera alla objekt i den lista som du tidigare etablerade i [distribuera IoT Hub i Azure](#deploy-iot-hub-in-azure).

![Moln etablering](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Om sidan låser sig i inläsnings status när du försöker logga in på Azure läser du [avsnittet "sidan låser sig vid inloggning" i vanliga frågor och svar för IoT-DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) för att lösa problemet. 

### <a name="build-and-upload-the-device-code"></a>Bygg och ladda upp enhets koden

Ladda sedan upp koden för enheten.

#### <a name="windows"></a>Windows

1. Används `Ctrl+P` för att köra `task device-upload` .

2. Terminalen anger att du vill ange konfigurations läge. Det gör du genom att hålla ned knapp A och sedan trycka och släppa återställnings knappen. På skärmen visas identifierings numret för DevKit och Word- *konfigurationen*.

#### <a name="macos"></a>macOS

1. Placera DevKit i konfigurations läge: Håll ned knapp A och tryck sedan på knappen Återställ. Skärmen visar "konfiguration".

2. Klicka `Cmd+P` för att köra `task device-upload` .

#### <a name="verify-upload-and-run-the-sample-app"></a>Verifiera, ladda upp och köra exempel appen

Anslutnings strängen som hämtas från steget [etablera Azure-tjänster](#provision-azure-services) är nu inställd. 

VS Code börjar sedan verifiera och ladda upp Arduino-skissen till DevKit.

![Skärm bild som visar Visual Studio Code-verifiering och uppladdning av Arduino-skissen.](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

DevKit startas om och börjar köra koden.

> [!NOTE]
> Ibland kan fel meddelandet "Error: AZ3166: unknowed Package" visas. Det här felet uppstår när tavla-paketets index inte har uppdaterats på rätt sätt. För att lösa det här felet, se [avsnittet utveckling i vanliga frågor och svar om IoT-DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testa projektet

Programmet initieras först när DevKit är i närvaro av ett stabilt magnet fält.

Efter initieringen `Door closed` visas på skärmen. När det finns en ändring i magnet fältet ändras statusen till `Door opened` . Varje gången du ändrar dörrens tillstånd får du ett e-postmeddelande. (Det kan ta upp till fem minuter innan e-postmeddelandena tas emot.)

![Magneter nära sensorn: dörren är stängd](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "Magneter nära sensorn: dörren är stängd")

![Magnet har flyttats bort från sensorn: dörren öppnades](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "Magnet har flyttats bort från sensorn: dörren öppnades")

## <a name="problems-and-feedback"></a>Problem och feedback

Om det uppstår problem kan du läsa [vanliga frågor och svar om IoT-DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller ansluta med följande kanaler:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du ansluter en DevKit-enhet till din lösnings Accelerator för Azure IoT-fjärrövervakning och använde tjänsten SendGrid för att skicka ett e-postmeddelande. Här är det föreslagna nästa steg:[Översikt över Azure IoT Remote Monitoring Solution Accelerator](/azure/iot-suite/)