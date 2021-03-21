---
title: Distribuera Micro Focus Enterprise Server 5,0 till AKS | Microsoft Docs
description: Revara värd för dina IBM z/OS-arbetsbelastningar med hjälp av Micro Focus-utveckling och test miljö på Azure Virtual Machines (VM).
services: virtual-machines
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: 9e5b3857c2252a939080206fb1f92cc422f326fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564364"
---
# <a name="deploy-micro-focus-enterprise-server-50-to-aks"></a>Distribuera Micro Focus Enterprise Server 5,0 till AKS

I en annan [artikel](./run-enterprise-server-container.md)beskrivs stegen för att köra Micro Focus Enterprise Server 5,0 i en Docker-behållare. Nu ska jag visa dig hur du tar det ett steg ytterligare och distribuerar Docker-avbildningen som du skapade i Azure Kubernetes service (AKS).

Azure Kubernetes-tjänsten är en hanterad Orchestration-tjänst baserad på Kubernetes. Det gör att du kan distribuera, skala och hantera Docker-behållare (och andra behållarebaserade program) i ett kluster med behållar värdar.

Det här är en tre stegs process. Du måste:

1.  Skapa en Azure Container Registry för att lagra Docker-avbildningen.

2.  Skapa ett Azure Kubernetes-kluster för att köra Docker-avbildningen.

3.  Kör appen.

På så sätt kan du skala ut (och skala ned) dina stordator modernisering-arbetsbelastningar i Azure, med tanke på den faktiska fördelen med moln plattformen.

Redo? Då sätter vi igång!

## <a name="create-the-azure-container-registry"></a>Skapa Azure Container Registry

Från Azure Portal väljer du **skapa en resurs** i det övre vänstra hörnet. Från Marketplace-instrumentpanelen väljer du **behållare och** sedan **container Registry**. Då går du till fönstret **skapa container Registry** där du måste fylla i **register namnet**, **Azure-prenumerationen**, **resurs gruppen** och **platsen**. **Register namnet** måste lösas, så det måste vara unikt. Välj den **resurs grupp** som du använde i föregående blogg inlägg och på samma **plats**. Välj **Aktivera** för **Administratörs användare** och **Basic** för **SKU: n**. När allt är ifyllt väljer du **skapa**.

![Skapa container Registry-gränssnitt](media/deploy-image-1.png)

När registret har distribuerats väljer **du gå till resurs**. Då går du till huvud bladet för Azure Container Registry. En bra funktion finns här **Snabbstart** meny alternativet. Välj den så visas anvisningar för vad du behöver göra för att skicka och hämta avbildningar till och från registret. Vi går vidare:

1.  **Installera Docker** – du behöver inte bekymra dig om detta eftersom det redan är gjort.

2.  **Kör bas avbildningen "Hello-World"** – igen, men du behöver inte göra det.

3.  **Logga in i behållar registret** – du måste göra detta från den virtuella datorn (VM). Kopiera kommandot till Urklipp eller anteckningar.

    För det register som jag har skapat är kommandot: **Docker login acrmf50.azurecr.io**

4.  **Push till registret** – du måste göra detta för mikrofokus-avbildningen och när du har loggat in på den virtuella datorn.

5.  **Hämta från registret** – det är inte relevant för den här genom gången, men du bör känna till om du behöver köra en annan Docker-avbildning.

Innan du lämnar portalen måste du hämta autentiseringsuppgifterna för registret så att du kan logga in. Avsluta **Snabbstart** bladet och välj **åtkomst nycklar** på register menyn. Kopiera **användar namnet** och ett av **lösen orden** (det finns två) till Urklipp eller anteckningar. Du kommer att behöva dem senare för att logga in.

Nu när du vet vad du behöver göra är att logga in på den virtuella datorn.

## <a name="rdp-to-the-virtual-machine-you-used-to-create-the-docker-image"></a>RDP till den virtuella dator som du använde för att skapa Docker-avbildningen

Eftersom du redan har skapat Docker-avbildningen på en Windows 2016-Server måste du logga in på den virtuella datorn. Från den här virtuella datorn kan du push-överföra avbildningen till de Azure Container Registry som du nyss skapade. Navigera till den virtuella datorn i Azure Portal och välj sedan **Översikt** och **Anslut**. Detta ansluter dig till den virtuella datorn via RDP (Remote Desktop Protocol). Du måste använda autentiseringsuppgifterna från när du skapade den virtuella datorn.

## <a name="log-in-and-push-the-image-to-the-registry"></a>Logga in och skicka avbildningen till registret

När du har loggat in öppnar du en kommando tolk och initierar följande Docker-kommandon:

-   **Docker-avbildningar** – här visas en lista över alla aktuella installerade avbildningar på den virtuella datorn. Anteckna **mikrofokus/es-acctdemo** eftersom det är den som du kommer att arbeta med.

-   **Docker-inloggning acrmf50.azurecr.io** – det korrekta formatet här är *Docker \<registry name\> -inloggning*. Ersätt det namn som du använde när du skapade registret.

    -   Du behöver det **användar namn** och **lösen ord** som du kopierade från Azure Portal. Det bör visas något i stil med följande bild.

    ![Skärm bild av en administratörs kommando tolk](media/deploy-image-2.png)

-   **Docker-tagg mikrofocus/es-acctdemo acrmf50.azurecr.io/es-acctdemo** – det här märkordet är en lämplig avbildning med namnet på lagrings platsen. **Obs!** om namnet \<microfocus/es-acctdemo\> inte fungerar försöker du med det fullständiga avbildnings-ID: t. När du har kört kommandot skriver du **Docker-avbildningar – No-TRUNC**. Du bör se något som liknar nästa bild. Observera att bilden är korrekt märkt.

    ![Sidan Välj administratörs kommando tolk](media/deploy-image-3.png)

-   **Docker push-acrmf50.azurecr.io/es-acctdemo** – det här avslutar den faktiska push-åtgärden till din lagrings plats. Eftersom storleken är 15 GB tar det ett par minuter att köra. Om allt går till höger bör du se något som liknar följande bild.

    ![Skärm för administratörs kommando tolken](media/deploy-image-4.png)

Gå nu tillbaka till Azure Portal, specifikt till **lagrings platsen**. På menyn för **lagrings platsen** väljer du **Arkiv** och du bör se **es-acctdemo** listad. Skapa nu AKS-klustret.

## <a name="create-the-azure-kubernetes-aks-cluster"></a>Skapa ett Azure Kubernetes-kluster (AKS)

Från Azure Portal väljer du **skapa en resurs** och sedan **behållare/Kubernetes-tjänsten** från **Marketplace** -menyn. Sedan måste du fylla i bladet **skapa Kubernetes-kluster** . Se till att behålla klustret i samma region och resurs grupp som du har använt. Du kan acceptera resten av standardvärdena förutom **antalet noder,** vilket bara behöver vara 1. När du är färdig väljer du **Granska + skapa**.

![Skärmen skapa Kubernetes-kluster](media/deploy-image-5.png)

När den är klar placeras **Kubernetes-tjänstens** artefakter i den **resurs grupp** som du valde på bladet. Det faktiska klustret kommer dock att ha en egen resurs grupp som skapas under distributionen. Om du väljer **resurs grupper** från menyn till vänster kan du hitta dem baserat på namngivnings konventionen. Här är en bild av mina – det är den sista i listan.

![Skärm bild av resurs grupper](media/deploy-image-6.png)

**Kör avbildningen**

Nu är det dags att hämta avbildningen och köra den i AKS. Det enklaste sättet att göra detta från Azure Portal är att använda Cloud Shell. Du hittar ikonen längst upp till höger i Azure Portal. Observera att i den här genom gången använder jag bash-gränssnittet.

![Skärm bild av Cloud Shells ikonen](media/deploy-image-7.png)

När gränssnittet har lästs in skriver du följande kommando:

**kubectl kör es-acctdemo--image acrmf50.azurecr.io/es-acctdemo--port = 9040**

Detta hämtar avbildningen från **acrmf50.azurecr.io** -lagringsplatsen och läser in den i AKS. Sedan körs avbildningen med port 9040 öppen. Du kan återkalla detta var porten du hade öppen för din Docker-avbildning. Du behöver åtkomst till Enterprise Server.

Kubernetes bör svara med ett meddelande om att distributionen har skapats.

![Skärm bild av ett distributions meddelande](media/deploy-image-8.jpg)

Om du vill se om behållaren faktiskt körs skriver du: **kubectl get poddar**.

Du bör se es-acctdemo som en pod som körs, som i följande bild.

![Skärm bild es – acctdemo som en pod som körs](media/deploy-image-9.png)

Grattis! Du kör nu Enterprise Server i Azure Kubernetes-tjänsten. I nästa artikel visar vi hur du kommer åt den administrativa konsolen i företags servern och hur du utnyttjar Kubernetes för att skala ut distributionen.
