---
title: Node.js program med Socket.io – Azure
description: Använd den här självstudien för att lära dig att vara värd för en socket. I/o-baserade chatt-program på Azure. Socket.IO tillhandahåller real tids kommunikation för en node.js-Server och-klienter.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: abc02769d7d978e14975d90ae0f98547bdc4faf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743329"
---
# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service-classic"></a>Bygg ett Node.js chatt-program med Socket.IO på en Azure Cloud Service (klassisk)

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Socket.IO tillhandahåller kommunikation i real tid mellan node.js Server och klienter. Den här självstudien vägleder dig genom att vara värd för en socket. IO-baserat chat-program på Azure. Mer information om Socket.IO finns i [socket.io](https://socket.io).

En skärm bild av det färdiga programmet är nedan:

![Ett webbläsarfönster som visar tjänsten som finns på Azure][completed-app]

## <a name="prerequisites"></a>Förutsättningar
Se till att följande produkter och versioner är installerade för att kunna slutföra exemplet i den här artikeln:

* Installera [Visual Studio](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)
* Installera [Node.js](https://nodejs.org/download/)
* Installera [python-version 2.7.10](https://www.python.org/)

## <a name="create-a-cloud-service-project"></a>Skapa ett moln tjänst projekt
Följande steg skapar det moln tjänst projekt som ska vara värd för Socket.IO-programmet.

1. Från **Start-menyn** eller **Start skärmen** söker du efter **Windows PowerShell**. Högerklicka slutligen på **Windows PowerShell** och välj **Kör som administratör**.

    ![Azure PowerShell ikon][powershell-menu]
2. Skapa en katalog som heter **c: \\ Node**.

    ```powershell
    PS C:\> md node
    ```

3. Ändra kataloger till katalogen **c: \\ Node**

    ```powershell
    PS C:\> cd node
    ```

4. Ange följande kommandon för att skapa en ny lösning med namnet `chatapp` och en arbets roll med namnet `WorkerRole1` :

    ```powershell
    PS C:\node> New-AzureServiceProject chatapp
    PS C:\Node> Add-AzureNodeWorkerRole
    ```

    Följande svar visas:

    ![Resultatet av New-azureservice och Add-azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>Ladda ned chatt-exemplet
För det här projektet kommer vi att använda chatt-exemplet från [socket.io GitHub-lagringsplatsen]. Utför följande steg för att ladda ned exemplet och lägga till det i projektet som du skapade tidigare.

1. Skapa en lokal kopia av lagrings platsen med hjälp av knappen **klona** . Du kan också använda **zip** -knappen för att ladda ned projektet.

   ![Ett webbläsarfönster https://github.com/LearnBoost/socket.io/tree/master/examples/chat som visar att ikonen för hämtning av zip är markerad](./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png)
2. Navigera till katalog strukturen för den lokala lagrings platsen tills du kommer till **exempel \\ chatt** -katalogen. Kopiera innehållet i den här katalogen till katalogen **C: \\ Node \\ ChatApp \\ WorkerRole1** som skapades tidigare.

   ![Explorer, som visar innehållet i exemplen i \\ Chat-katalogen som extraherats från arkivet][chat-contents]

   De markerade objekten i skärm bilden ovan är filerna som kopieras från **exemplen i \\ Chat** -katalogen
3. I katalogen **C: \\ Node \\ ChatApp \\ WorkerRole1** tar du bort **server.js** -filen och byter sedan namn på **app.js** filen till **server.js**. Detta tar bort standard **server.jss** filen som skapades tidigare av cmdleten **Add-AzureNodeWorkerRole** och ersätter den med program filen från Chat-exemplet.

### <a name="modify-serverjs-and-install-modules"></a>Ändra Server.js och installera moduler
Innan du testar programmet i Azure-emulatorn måste vi göra några mindre ändringar. Utför följande steg i server.js-filen:

1. Öppna **server.js** -filen i Visual Studio eller någon text redigerare.
2. Hitta avsnittet **beroenden för modulen** i början av server.js och ändra raden som innehåller **Sio = Kräv (... //.. lib//socket. IO)** till **Sio = Kräv (socket. IO)** som visas nedan:

    ```js
    var express = require('express')
      , stylus = require('stylus')
      , nib = require('nib')
    //, sio = require('..//..//lib//socket.io'); //Original
      , sio = require('socket.io');                //Updated
      var port = process.env.PORT || 3000;         //Updated
    ```

3. Se till att programmet lyssnar på rätt port genom att öppna server.js i anteckningar eller din favorit redigerare och sedan ändra följande rad genom att ersätta **3000** med **process. kuvert. port** enligt nedan:

    ```js
    //app.listen(3000, function () {            //Original
    app.listen(process.env.port, function () {  //Updated
      var addr = app.address();
      console.log('   app listening on http://' + addr.address + ':' + addr.port);
    });
    ```

När du har sparat ändringarna i **server.js** använder du följande steg för att installera nödvändiga moduler och testar sedan programmet i Azure-emulatorn:

1. Använd **Azure PowerShell**, ändra kataloger till katalogen **C: \\ Node \\ ChatApp \\ WorkerRole1** och Använd följande kommando för att installera modulerna som krävs för det här programmet:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> npm install
    ```

   Då installeras modulerna som anges i package.jspå filen. När kommandot är klart bör du se utdata som liknar följande:

   ![Utdata från kommandot NPM install][The-output-of-the-npm-install-command]
2. Eftersom det här exemplet ursprungligen var en del av Socket.IO GitHub-lagringsplatsen och direkt refererade till Socket.IO-biblioteket efter relativ sökväg, refererades Socket.IO inte till i package.jspå filen, så vi måste installera det genom att utfärda följande kommando:

   ```powershell
   PS C:\node\chatapp\WorkerRole1> npm install socket.io --save
    ```

### <a name="test-and-deploy"></a>Testa och distribuera
1. Starta emulatorn genom att utfärda följande kommando:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch
    ```

   > [!NOTE]
   > Om det uppstår problem med att starta emulatorn, t. ex.: Start-AzureEmulator: ett oväntat fel inträffade.  Information: ett oväntat fel inträffade i kommunikations objekt, system. ServiceModel. Channels. asynkrona servicechannel, kan inte användas för kommunikation eftersom det är i fel tillstånd.
   >
   > Installera om AzureAuthoringTools v 2.7.1 och AzureComputeEmulator v 2,7 – Se till att versions matchningarna.

2. Öppna en webbläsare och gå till **http://127.0.0.1** .
3. När webbläsarfönstret öppnas anger du ett smek namn och trycker sedan på RETUR.
   På så sätt kan du skicka meddelanden som ett speciellt smek namn. Om du vill testa funktionen för flera användare öppnar du ytterligare webb läsar fönster med samma URL och anger olika smek namn.

   ![Två webb läsar fönster som visar Chat-meddelanden från Användare1 och användare2](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)
4. När du har testat programmet stoppar du emulatorn genom att utfärda följande kommando:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator
    ```

5. Använd cmdleten **Publish-AzureServiceProject** om du vill distribuera programmet till Azure. Exempel:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch
    ```

   > [!IMPORTANT]
   > Se till att du använder ett unikt namn, annars Miss kan publicerings processen. När distributionen har slutförts öppnas webbläsaren och navigerar till den distribuerade tjänsten.
   >
   > Om du får ett fel meddelande om att det angivna prenumerations namnet inte finns i den importerade publicerings profilen måste du hämta och importera publicerings profilen för din prenumeration innan du distribuerar till Azure. Se avsnittet **distribuera appen till Azure** i [utveckla och distribuera ett Node.js program till en Azure-moln tjänst](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   >
   >

   ![Ett webbläsarfönster som visar tjänsten som finns på Azure][completed-app]

   > [!NOTE]
   > Om du får ett fel meddelande om att det angivna prenumerations namnet inte finns i den importerade publicerings profilen måste du hämta och importera publicerings profilen för din prenumeration innan du distribuerar till Azure. Se avsnittet **distribuera appen till Azure** i [utveckla och distribuera ett Node.js program till en Azure-moln tjänst](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   >
   >

Ditt program körs nu på Azure och kan vidarebefordra chatt meddelanden mellan olika klienter med Socket.IO.

> [!NOTE]
> För enkelhetens skull är det här exemplet begränsat till att chatta mellan användare som är anslutna till samma instans. Det innebär att om moln tjänsten skapar två arbets Rolls instanser, kan användarna bara chatta med andra som är anslutna till samma arbets Rolls instans. Om du vill skala programmet så att det fungerar med flera roll instanser kan du använda en teknik som Service Bus för att dela Socket.IO Store-tillstånd över instanser. Exempel finns i Service Bus köer och ämnen användnings exempel i [Azure SDK för Node.js GitHub-lagringsplatsen](https://github.com/WindowsAzure/azure-sdk-for-node).
>
>

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig hur du skapar ett Basic chat-program som finns i en Azure-moln tjänst. Information om hur du kan vara värd för det här programmet på en Azure-webbplats finns i [bygga ett Node.js chatt-program med socket.io på en Azure-webbplats][chatwebsite].

Mer information finns även i [Node.js Developer Center](/azure/developer/javascript/).

[chatwebsite]: ./cloud-services-nodejs-develop-deploy-app.md

[Azure SLA]: https://www.windowsazure.com/support/sla/
[Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
[completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
[Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Socket.IO GitHub-lagringsplats]: https://github.com/LearnBoost/socket.io/tree/0.9.14
[Azure Considerations]: #windowsazureconsiderations
[Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
[Summary and Next Steps]: #summary
[powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

[chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
[chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png


[chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
[The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png