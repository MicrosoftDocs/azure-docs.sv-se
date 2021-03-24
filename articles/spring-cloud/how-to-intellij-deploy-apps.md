---
title: 'Självstudie: använda IntelliJ för att distribuera Azure våren Cloud-program'
description: Använd IntelliJ för att distribuera program till Azure våren Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: 64ff778f9735c690f4622402ef2e3124185fa3ea
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879194"
---
# <a name="use-intellij-to-deploy-azure-spring-cloud-applications"></a>Använda IntelliJ för att distribuera Azure Spring Cloud-program

**Den här artikeln gäller för:** ✔️ Java

IntelliJ-plugin-programmet för Azure våren Cloud stöder program distribution från IntelliJ-idén.  

Innan du kör det här exemplet kan du prova den [grundläggande snabb](spring-cloud-quickstart.md)starten.

## <a name="prerequisites"></a>Förutsättningar
* [IntelliJ idé, community/Ultimate-utgåva, version 2020.1/2020.2](https://www.jetbrains.com/idea/download/#section=windows)

## <a name="install-the-plug-in"></a>Installera plugin-programmet
Du kan lägga till Azure Toolkit for IntelliJ idé 3.43.0 från IntelliJ **plugin** -gränssnittet.

1. Starta IntelliJ.  Om du har öppnat ett projekt tidigare stänger du projektet för att Visa välkomst dialog rutan. Välj **Konfigurera** från länk nedre högra och klicka sedan på **plugin** -program för att öppna dialog rutan konfiguration av plugin-programmet och välj **Installera plugin-program från disk**.

    ![Välj Konfigurera](media/spring-cloud-intellij-howto/configure-plugin-1.png)

1. Sök efter Azure Toolkit for IntelliJ.  Klicka på **Installera**.

    ![Installera plugin-program](media/spring-cloud-intellij-howto/install-plugin.png)

1. Klicka på **starta om IDE**.

## <a name="tutorial-procedures"></a>Själv studie procedurer
Följande procedurer distribuerar ett Hello World-program med hjälp av IntelliJ-idén.

* Öppna GS-våren-Boot-projekt
* Distribuera till Azure våren Cloud
* Visa strömmande loggar

## <a name="open-gs-spring-boot-project"></a>Öppna GS-våren-Boot-projekt

1. Ladda ned och zippa upp käll databasen för den här själv studie kursen eller klona den med git: git-kloning https://github.com/spring-guides/gs-spring-boot.git 
1. CD till GS-Spring-boot\complete.
1. Öppna dialog rutan **Välkommen** IntelliJ, Välj **Importera projekt** för att öppna import guiden.
1. Välj `gs-spring-boot\complete` mapp.

    ![Importera projekt](media/spring-cloud-intellij-howto/import-project-1.png)

## <a name="deploy-to-azure-spring-cloud"></a>Distribuera till Azure våren Cloud
För att kunna distribuera till Azure måste du logga in med ditt Azure-konto och välja din prenumeration.  Information om inloggning finns i [installation och inloggning](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Högerklicka på projektet i IntelliJ Project Explorer och välj **Azure**  ->  **Deploy till Azure våren Cloud**.

    ![Distribuera till Azure 1](media/spring-cloud-intellij-howto/deploy-to-azure-1.png)

1. Godkänn namnet på appen i fältet **namn** . **Namnet** avser konfigurationen, inte appens namn. Användarna behöver vanligt vis inte ändra det.
1. Acceptera identifieraren från projektet för **artefakten**.
1. Välj **app:** klicka sedan på **skapa app...**.

    ![Distribuera till Azure 2](media/spring-cloud-intellij-howto/deploy-to-azure-2.png)

1. Ange **ett namn på appen** och klicka sedan på **OK**.

    ![Distribuera till Azure OK](media/spring-cloud-intellij-howto/deploy-to-azure-2a.png)

1. Starta distributionen genom att klicka på knappen **Kör** . 

    ![Distribuera till Azure 3](media/spring-cloud-intellij-howto/deploy-to-azure-3.png)

1. Plugin-programmet kör kommandot `mvn package` på projektet och skapar sedan den nya appen och distribuerar jar som genereras av `package` kommandot.

1. Om App-URL: en inte visas i utdatafönstret hämtar du den från Azure Portal. Navigera från resurs gruppen till instansen av Azure våren Cloud.  Klicka sedan på **appar**.  Appen som körs visas i listan.

    ![Hämta test-URL](media/spring-cloud-intellij-howto/get-test-url.png)

1. Navigera till URL: en i webbläsaren.

    ![Navigera i webbläsare 2](media/spring-cloud-intellij-howto/navigate-in-browser-2.png)

## <a name="show-streaming-logs"></a>Visa strömmande loggar
Hämta loggarna:
1. Välj **Azure Explorer** och sedan **våren Cloud**.
1. Högerklicka på appen som körs.
1. Välj **strömmande loggar** från den nedrullningsbara listan.

    ![Välj strömmande loggar](media/spring-cloud-intellij-howto/streaming-logs.png)

1. Välj instans.

    ![Välj instans](media/spring-cloud-intellij-howto/select-instance.png)

1. Direkt uppspelnings loggen visas i fönstret utdata.

    ![Strömmande logg utdata](media/spring-cloud-intellij-howto/streaming-log-output.png)

## <a name="next-steps"></a>Nästa steg
* [Förbered våren-program för Azure våren Cloud](how-to-prepare-app-deployment.md)
* [Läs mer om Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/)
