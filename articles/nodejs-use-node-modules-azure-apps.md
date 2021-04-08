---
title: Arbeta med Node.js moduler
description: Lär dig hur du arbetar med Node.js-moduler när du använder Azure App Service eller Cloud Services.
services: ''
documentationcenter: nodejs
author: rloutlaw
manager: rloutlaw
editor: ''
ms.assetid: c0e6cd3d-932d-433e-b72d-e513e23b4eb6
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: routlaw
ms.openlocfilehash: f61f08f32ebb2b721846d3c3017405af99421104
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95542226"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>Använda Node.js-moduler med Azure-program
Det här dokumentet ger vägledning om hur du använder Node.js moduler med program som finns på Azure. Det ger vägledning om hur du säkerställer att programmet använder en specifik version av en modul samt hur du använder inbyggda moduler med Azure.

Om du redan är bekant med att använda Node.js-moduler, **package.jspå** och **npm-shrinkwrap.js** filer, ger följande information en snabb översikt över vad som beskrivs i den här artikeln:

* Azure App Service förstår **package.jspå** och **npm-shrinkwrap.js** filer och kan installera moduler baserat på poster i dessa filer.

* Azure Cloud Services förväntar sig att alla moduler ska installeras i utvecklings miljön och att katalogen **Node- \_ moduler** ska ingå som en del av distributions paketet. Det går att aktivera stöd för att installera moduler med **package.jspå** eller **npm-shrinkwrap.jspå** filer på Cloud Services; den här konfigurationen kräver dock anpassning av de standard skript som används av moln tjänst projekt. Ett exempel på hur du konfigurerar den här miljön finns i [Start uppgift i Azure så här kör du NPM installera för att undvika distribution av Node-moduler](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [!NOTE]
> Azure Virtual Machines diskuteras inte i den här artikeln eftersom distributions miljön i en virtuell dator är beroende av det operativ system som den virtuella datorn har.
>
>

## <a name="nodejs-modules"></a>Node.js moduler
Moduler är inbelastnings bara JavaScript-paket som tillhandahåller specifika funktioner för ditt program. Moduler installeras vanligt vis med kommando rads verktyget **NPM** , men vissa moduler (t. ex. http-modulen) tillhandahålls som en del av kärnan Node.js-paketet.

När moduler är installerade lagras de i katalogen Node- **\_ moduler** i roten i din program katalog struktur. Varje modul i katalogen **Node- \_ moduler** underhåller sin egen katalog som innehåller alla moduler som den är beroende av, och det här beteendet upprepas för varje modul, oavsett hur beroende kedjan är. I den här miljön kan varje modul installeras för att ha sina egna versions krav för de moduler som är beroende av, men det kan leda till en stor katalog struktur.

Om du distribuerar **noden för Node- \_ moduler** som en del av programmet ökar storleken på distributionen jämfört med att använda en **package.jspå** eller **npm-shrinkwrap.jspå** filen, men det garanterar dock att de versioner av modulerna som används i produktionen är desamma som modulerna som används i utvecklingen.

### <a name="native-modules"></a>Inbyggda moduler
De flesta moduler är bara JavaScript-filer med oformaterad text, men vissa moduler är plattformsspecifika binära avbildningar. Dessa moduler kompileras vid installations tiden, vanligt vis med python och Node-Gyp. Eftersom Azure Cloud Services förlitar sig på mappen **\_ moduler** som distribueras som en del av programmet, bör alla inbyggda moduler som ingår i de installerade modulerna fungera i en moln tjänst så länge den har installerats och kompilerats i ett Windows Development-system.

Azure App Service har inte stöd för alla inbyggda moduler, och kan Miss kan Miss Missing när moduler kompileras med specifika krav. Vissa populära moduler som MongoDB har valfria inbyggda beroenden och fungerar bra utan dem, men två lösningar har visat sig uppfylla varandra med nästan alla inbyggda moduler som är tillgängliga idag:

* Kör **NPM-installationen** på en Windows-dator som har alla förutsättningar för inbyggd modul installerad. Distribuera sedan mappen skapade **Node- \_ moduler** som en del av programmet för att Azure App Service.

  * Innan du kompilerar kontrollerar du att installationen av den lokala Node.js har en matchande arkitektur och att versionen är så nära som möjligt för den som används i Azure (de aktuella värdena kan kontrol leras vid körning från egenskaper **process. båge** och **process. version**).

* Azure App Service kan konfigureras för att köra anpassade bash eller Shell-skript under distributionen, vilket ger dig möjlighet att köra anpassade kommandon och exakt konfigurera hur **NPM-installationen** körs. En video som visar hur du konfigurerar miljön finns i avsnittet om [distributions skript för anpassad webbplats med kudu](https://azure.microsoft.com/resources/videos/custom-web-site-deployment-scripts-with-kudu/).

### <a name="using-a-packagejson-file"></a>Använda en package.jspå filen

**package.jsi** filen är ett sätt att ange de toppnivå beroenden som programmet kräver, så att värd plattformen kan installera beroenden, i stället för att du ska inkludera mappen **\_ moduler** som en del av distributionen. När programmet har distribuerats används **installations kommandot NPM** för att parsa **package.jspå** filen och installera alla beroenden som anges.

Under utvecklingen kan du använda parametrarna **--Save**, **--Save-dev** eller **--Save-optional** när du installerar moduler för att lägga till en post för modulen till **package.jsi** filen automatiskt. Mer information finns i [NPM-install](https://docs.npmjs.com/cli/install).

Ett möjligt problem med filen **package.js** är att den bara anger versionen för toppnivå beroenden. Varje modul som installeras kan eller kan inte ange vilken version av moduler den är beroende av, och därför kan det hända att du får en annan beroende kedja än den som används i utvecklingen.

> [!NOTE]
> Om din <b>package.jspå</b> fil refererar till en inbyggd modul när du distribuerar till Azure App Service kan du se ett fel som liknar följande exempel när du publicerar programmet med git:
>
> NPM fel! module-name@0.6.0 installera: ' Node-Gyp konfigurera build '
>
> NPM fel! "cmd"/c "" Node-Gyp Configure Build "misslyckades med 1
>
>

### <a name="using-a-npm-shrinkwrapjson-file"></a>Använda en npm-shrinkwrap.jspå filen
**npm-shrinkwrap.js** filen är ett försök att adressera de versions begränsningar för modul som **package.jss i** filen. Även om **package.jsi** filen bara innehåller versioner för modulerna på den översta nivån, innehåller **npm-shrinkwrap.jsi** filen versions kraven för den fullständiga beroende kedjan för modulen.

När programmet är redo för produktion kan du låsa versions kraven och skapa en **npm-shrinkwrap.jspå** en fil med hjälp av kommandot **NPM shrinkwrap** . Det här kommandot använder de versioner som för närvarande är installerade i **noden \_ moduler** och registrerar dessa versioner till **npm-shrinkwrap.jspå** filen. När programmet har distribuerats till värd miljön används **NPM install** -kommandot för att parsa **npm-shrinkwrap.jspå** filen och installera alla beroenden som anges. Mer information finns i [NPM-shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> Om din <b>npm-shrinkwrap.jspå</b> fil refererar till en inbyggd modul när du distribuerar till Azure App Service kan du se ett fel som liknar följande exempel när du publicerar programmet med git:
>
> NPM fel! module-name@0.6.0 installera: ' Node-Gyp konfigurera build '
>
> NPM fel! "cmd"/c "" Node-Gyp Configure Build "misslyckades med 1
>
>

## <a name="next-steps"></a>Nästa steg
Nu när du förstår hur du använder Node.js-moduler med Azure kan du lära dig hur du [anger Node.js version](https://github.com/squillace/nodejs-microservice), [skapar och distribuerar en Node.js-webbapp](app-service/quickstart-nodejs.md)och [hur du använder Azure Command-Line-gränssnittet för Mac och Linux](https://azure.microsoft.com/blog/using-windows-azure-with-the-command-line-tools-for-mac-and-linux/).

Mer information finns i [Node.js Developer Center](/azure/developer/javascript/).

[specify the Node.js version]: ./app-service/overview.md
[How to use the Azure Command-Line Interface for Mac and Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo