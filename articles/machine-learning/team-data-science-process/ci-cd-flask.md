---
title: Skapa en CI/CD-pipeline med Azure-pipeline – team data science process
description: Skapa en kontinuerlig integrering och kontinuerlig leverans av en pipeline för artificiell intelligens (AI)-program med Docker och Kubernetes.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: af7900faff18d526686b80f23b9de3b3bce5ec5c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93309604"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>Skapa CI/CD-pipelines för AI-appar med hjälp av Azure-pipeline, Docker och Kubernetes

Ett AI-program (artificiell intelligens) är program kod inbäddad med en modell med förtränad Machine Learning (ML). Det finns alltid två strömmar av arbete för ett AI-program: data experter skapar ML-modellen och apparna utvecklar appen och visar den för slutanvändare att använda. Den här artikeln beskriver hur du implementerar en pipeline för kontinuerlig integrering och kontinuerlig leverans (CI/CD) för ett AI-program som bäddar in ML-modellen i käll koden för appen. Exempel koden och självstudien använder en webb program för python-flaskan och hämtar en förtränad modell från ett privat Azure Blob Storage-konto. Du kan också använda ett AWS S3-lagrings konto.

> [!NOTE]
> Följande process är en av flera olika sätt att göra CI/CD. Det finns alternativ för det här verktyget och kraven.

## <a name="source-code-tutorial-and-prerequisites"></a>Källkod, självstudier och krav

Du kan ladda ned [käll koden](https://github.com/Azure/DevOps-For-AI-Apps) och en [detaljerad självstudie](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) från GitHub. Följ anvisningarna i själv studie kursen om hur du implementerar en CI/CD-pipeline för ditt eget program.

Om du vill använda den nedladdade käll koden och självstudien behöver du följande krav: 

- [Käll kods databasen](https://github.com/Azure/DevOps-For-AI-Apps) är förgrenad till ditt GitHub-konto
- En [Azure DevOps-organisation](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Azure CLI](/cli/azure/install-azure-cli)
- Ett [Azure Container Service för Kubernetes-kluster (AKS)](/previous-versions/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) för att köra kommandon och hämta konfiguration från AKS-klustret 
- Ett [Azure Container Registry-konto (ACR)](../../container-registry/container-registry-get-started-portal.md)

## <a name="cicd-pipeline-summary"></a>Sammanfattning av CI/CD-pipeline

Varje ny git-incheckning avslutar den bygga pipelinen. Bygget tar på ett säkert sätt den senaste ML-modellen från ett Blob Storage-konto och paketerar den med app-koden i en enda behållare. Den här kopplingen av program utvecklingen och data vetenskaps workstreams garanterar att produktions programmet alltid kör den senaste koden med den senaste ML-modellen. Om appen klarar testningen lagrar pipelinen på ett säkert sätt build-avbildningen i en Docker-behållare i ACR. Pipelinen för release distribuerar sedan behållaren med hjälp av AKS. 

## <a name="cicd-pipeline-steps"></a>Steg för CI/CD-pipeline

Följande diagram och steg beskriver CI/CD-pipeline-arkitekturen:

![Arkitektur för CI/CD-pipeline](./media/ci-cd-flask/architecture.png)

1. Utvecklare arbetar på program koden i den IDE du väljer.
2. Utvecklarna genomför koden till Azure databaser, GitHub eller någon annan git Source Control-Provider. 
3. Data forskare arbetar separat tillsammans med att utveckla sin ML-modell.
4. Data forskare publicerar den färdiga modellen till en modell databas, i det här fallet ett Blob Storage-konto. 
5. Azure-pipeliner försätter en version utifrån git-incheckning.
6. Den bygga pipelinen hämtar den senaste ML-modellen från Blob Storage och skapar en behållare.
7. Pipelinen skickar build-avbildningen till lagrings platsen för den privata avbildningen i ACR.
8. Lanserings pipeline avslutar baserat på den slutförda versionen.
9. Pipelinen hämtar den senaste avbildningen från ACR och distribuerar den över Kubernetes-klustret på AKS.
10. Användar förfrågningar för appen går via DNS-servern.
11. DNS-servern skickar begär anden till en belastningsutjämnare och skickar tillbaka svar till användarna.

## <a name="see-also"></a>Se även

- [TDSP (Team data science process)](./index.yml)
- [Azure Machine Learning (AML)](../index.yml)
- [Azure DevOps](https://azure.microsoft.com/services/devops/)
- [Azure Kubernetes Services (AKS)](../../aks/intro-kubernetes.md)