---
title: Uppdateringar av basavbildningar – uppgifter
description: Lär dig mer om basavbildningar för programcontaineravbildningar och hur en uppdatering av basavbildningen kan utlösa en Azure Container Registry uppgift.
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: fc501cc1db654c11675e5a4f0d19a5a56b63a165
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781189"
---
# <a name="about-base-image-updates-for-acr-tasks"></a>Om basavbildningsuppdateringar för ACR-uppgifter

Den här artikeln innehåller bakgrundsinformation om uppdateringar av ett programs basavbildning och hur dessa uppdateringar kan utlösa en Azure Container Registry uppgift.

## <a name="what-are-base-images"></a>Vad är basavbildningar?

Dockerfiles som definierar de flesta containeravbildningar anger en överordnad avbildning som avbildningen baseras på, vilket ofta kallas dess *basavbildning*. Basavbildningar innehåller vanligtvis operativsystemet, till exempel [Alpine Linux][base-alpine] eller [Windows Nano Server][base-windows], där resten av containerns lager tillämpas. De kan även innehålla programramverk som [Node.js][base-node] eller [.NET Core][base-dotnet]. Dessa basavbildningar baseras vanligtvis på offentliga överordnade avbildningar. Flera av dina programavbildningar kan dela en gemensam basavbildning.

En basavbildning uppdateras ofta av avbildningens underhållare med nya funktioner och förbättringar av operativsystem eller ramverk i avbildningen. Säkerhetskorrigeringar är en annan vanlig orsak till att en basavbildning uppdateras. När dessa överordnade uppdateringar sker måste du även uppdatera basavbildningarna så att de innehåller den kritiska korrigeringen. Varje programavbildning måste sedan återskapas för att inkludera dessa överordnade korrigeringar som nu ingår i basavbildningen.

I vissa fall, till exempel ett privat utvecklingsteam, kan en basavbildning ange mer än operativsystem eller ramverk. En basavbildning kan till exempel vara en komponentavbildning för delad tjänst som behöver spåras. Medlemmar i ett team kan behöva spåra basavbildningen för testning eller regelbundet uppdatera avbildningen när de utvecklar programavbildningar.

## <a name="maintain-copies-of-base-images"></a>Underhålla kopior av basavbildningar

För allt innehåll i dina register som är beroende av basinnehåll som underhålls i ett offentligt register, till exempel Docker Hub, rekommenderar vi att du kopierar innehållet till ett Azure-containerregister eller ett annat privat register. Se sedan till att du skapar dina programavbildningar genom att referera till de privata basavbildningarna. Azure Container Registry ger en [bildimportfunktion](container-registry-import-images.md) för att enkelt kopiera innehåll från offentliga register eller andra Azure-containerregister. I nästa avsnitt beskrivs hur du använder ACR-uppgifter för att spåra uppdateringar av basavbildningar när du skapar programuppdateringar. Du kan spåra basavbildningsuppdateringar i dina egna Azure-containerregister och eventuellt i överordnade offentliga register.

## <a name="track-base-image-updates"></a>Spåra basavbildningsuppdateringar

I ACR Tasks finns möjligheten att automatiskt skapa avbildningar när en containers basavbildning uppdateras. Du kan använda den här möjligheten för att underhålla och uppdatera kopior av offentliga basavbildningar i dina Azure-containerregister och sedan återskapa programavbildningar som är beroende av basavbildningar.

ACR-uppgifter identifierar basavbildningsberoenden när en containeravbildning byggs. Därför kan den identifiera när en programavbildnings basavbildning uppdateras. Med en förkonfigurerad bygguppgift kan ACR-uppgifter automatiskt återskapa varje programavbildning som refererar till basavbildningen. Med den här automatiska identifieringen och återskapande ACR-uppgifter sparar du den tid och arbetsinsats som normalt krävs för att manuellt spåra och uppdatera varje programavbildning som refererar till den uppdaterade basavbildningen.

## <a name="base-image-locations"></a>Basavbildningsplatser

För avbildningsbyggen från en Dockerfile identifierar en ACR-uppgift beroenden på basavbildningar på följande platser:

* Samma Azure-containerregister där aktiviteten körs
* Ett annat privat Azure-containerregister i samma eller en annan region 
* En offentlig lagringsplatsen i Docker Hub 
* En offentlig lagringsplatsen i Microsoft Container Registry

Om basavbildningen som anges i -instruktionen finns på någon av dessa platser lägger ACR-uppgiften till en hook för att säkerställa att avbildningen återskapas varje gång basen `FROM` uppdateras.

## <a name="base-image-notifications"></a>Basavbildningsmeddelanden

Tiden mellan när en basavbildning uppdateras och när den beroende aktiviteten utlöses beror på basavbildningens plats:

* **Basavbildningar** från en offentlig lagringsplatsen i Docker Hub eller MCR – För basavbildningar i offentliga databaser söker en ACR-uppgift efter avbildningsuppdateringar med ett slumpmässigt intervall på mellan 10 och 60 minuter. Beroende uppgifter körs på motsvarande sätt.
* **Basavbildningar från ett Azure-containerregister** – För basavbildningar i Azure-containerregister utlöser en ACR-uppgift omedelbart en körning när dess basavbildning uppdateras. Basavbildningen kan finnas i samma ACR där aktiviteten körs eller i en annan ACR i vilken region som helst.

## <a name="additional-considerations"></a>Annat som är bra att tänka på

* **Basavbildningar för programavbildningar** – För närvarande spårar en ACR-uppgift endast basavbildningsuppdateringar för *programavbildningar (runtime).* Den spårar inte basavbildningsuppdateringar för mellanliggande *(buildtime)* avbildningar som används i Dockerfiles i flera steg.  

* **Aktiverad som standard** – När du skapar en ACR-uppgift med kommandot  [az acr task create][az-acr-task-create] aktiveras aktiviteten som standard för utlösare av en basavbildningsuppdatering. Det innebär att `base-image-trigger-enabled` egenskapen är inställd på Sant. Om du vill inaktivera det här beteendet i en uppgift uppdaterar du egenskapen till False. Kör till exempel följande [az acr task update-kommando:][az-acr-task-update]

  ```azurecli
  az acr task update --registry myregistry --name mytask --base-image-trigger-enabled False
  ```

* **Utlösare** för att spåra beroenden – Om du vill att en ACR-uppgift ska kunna fastställa och spåra en containeravbildnings beroenden – som innehåller dess basavbildning – måste du först utlösa uppgiften för att skapa avbildningen minst en **gång.** Du kan till exempel utlösa uppgiften manuellt med [kommandot az acr task run.][az-acr-task-run]

* **Stabil tagg för basavbildning** – Om du vill utlösa en uppgift vid uppdatering av basavbildningen måste basavbildningen ha *en stabil* tagg, till exempel `node:9-alpine` . Den här taggningen är typisk för en basavbildning som uppdateras med os- och ramverkskorrigeringar till en senaste stabila version. Om basavbildningen uppdateras med en ny versionstagg utlöses ingen uppgift. Mer information om bildtaggning finns i vägledningen [om bästa praxis.](container-registry-image-tag-version.md) 

* **Andra uppgiftsutlösare** – I en aktivitet som utlöses av basavbildningsuppdateringar kan du också aktivera utlösare baserat på källkods-commit eller [ett schema](container-registry-tasks-scheduled.md). [](container-registry-tutorial-build-task.md) En basavbildningsuppdatering kan också utlösa [en uppgift i flera steg.](container-registry-tasks-multi-step.md)

## <a name="next-steps"></a>Nästa steg

Se följande självstudier för scenarier för att automatisera programavbildningsbyggen när en basavbildning har uppdaterats:

* [Automatisera containeravbildningsbyggen när en basavbildning uppdateras i samma register](container-registry-tutorial-base-image-update.md)

* [Automatisera containeravbildningsbyggen när en basavbildning uppdateras i ett annat register](container-registry-tutorial-base-image-update.md)


<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-pack-build]: /cli/azure/acr/pack#az_acr_pack_build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-update]: /cli/azure/acr/task#az_acr_task_update
[az-login]: /cli/azure/reference-index#az_login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
