---
title: CI/CD-arbetsflöde med GitOps-Azure Arc-aktiverad Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: conceptual
author: tcare
ms.author: tcare
description: Den här artikeln innehåller en konceptuell översikt över ett CI/CD-arbetsflöde med GitOps
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes service, behållare, CI, CD, Azure DevOps
ms.openlocfilehash: a51a9f2b32f1088cec390dc4d74300a38f37b160
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121787"
---
# <a name="cicd-workflow-using-gitops---azure-arc-enabled-kubernetes"></a>CI/CD-arbetsflöde med GitOps-Azure Arc-aktiverad Kubernetes

Moderna Kubernetes-distributioner House flera program, kluster och miljöer. Med GitOps kan du enkelt hantera dessa komplexa installations program och spåra det önskade läget för Kubernetes-miljöerna med git. Genom att använda vanliga git-verktyg för att spåra kluster status kan du öka ansvars perioden, under lätta fel undersökningen och aktivera automatisering för att hantera miljöer.

Den här konceptuella översikten förklarar GitOps som en verklighet i hela livs cykeln för program ändringar med Azure Arc, Azure databaser och Azure pipelines. [Hoppa till ett exempel](#example-workflow) på en enda program ändring till GitOps-styrda Kubernetes-miljöer.

## <a name="architecture"></a>Arkitektur

Överväg att ett program distribueras till en eller flera Kubernetes-miljöer.

![GitOps CI/CD-arkitektur](./media/gitops-arch.png)

### <a name="application-repo"></a>Program lagrings platsen
Programmet lagrings platsen innehåller den program kod som utvecklare arbetar med under sin inre slinga. Programmets distributionsmall är Live i den här lagrings platsen i ett allmänt formulär, som Helm eller Kustomize. Miljöbaserade värden lagras inte. Ändringar i den här lagrings platsen anropar en PR-eller CI-pipeline som startar distributions processen.
### <a name="container-registry"></a>Container Registry
Behållar registret innehåller alla de första och tredje parts avbildningarna som används i Kubernetes-miljöerna. Tagga program avbildningar från första part med läsliga Taggar och git-incheckning som används för att bygga avbildningen. Cachelagra avbildningar från tredje part för säkerhet, hastighet och återhämtning. Ange en plan för tids testning och integrering av säkerhets uppdateringar. Mer information finns i [ACR konsumera och underhålla offentlig innehålls](https://docs.microsoft.com/azure/container-registry/tasks-consume-public-content) guide för ett exempel.
### <a name="pr-pipeline"></a>PR pipeline
Pull till program lagrings platsen är gated på en lyckad körning av PR-pipeline. Den här pipelinen kör de grundläggande kvalitets grindarna, t. ex. försäljnings-och enhets test på program koden. Pipelinen testar programmet och bevarar Dockerfiles-och Helm-mallar som används för distribution till en Kubernetes-miljö. Docker-avbildningar bör skapas och testas, men inte pushas. Se till att pipeline-varaktigheten är relativt kort för snabb iteration.
### <a name="ci-pipeline"></a>CI-pipeline
I pipeline för program-CI körs alla steg för att utföra pipeline och utökar test-och distributions kontrollerna. Pipelinen kan köras för varje genomförande eller vid en vanlig takt med en grupp av incheckningar. I det här skedet utför du program testning som är för lång för en PR-pipeline. Skicka Docker-avbildningar till Container Registry efter att du har skapat förberedelser inför distribution. Den ersatta mallen kan vara luddfri med en uppsättning test värden. Avbildningar som används vid tjänst körningen bör vara luddfri, byggd och testas i det här läget. I CI-versionen publiceras artefakter för CD-steget för att förbereda för distribution.
### <a name="flux"></a>Flux
Flöde är en tjänst som körs i varje kluster och som ansvarar för att underhålla det önskade läget. Tjänsten avsöker ofta GitOps-lagrings platsen för ändringar i klustret och tillämpar dem.
### <a name="cd-pipeline"></a>CD-pipeline
CD-pipelinen utlöses automatiskt av lyckade CI-versioner. Den använder tidigare publicerade mallar, byter ut miljö värden och öppnar en PR till GitOps-lagrings platsen för att begära en ändring av det önskade läget för ett eller flera Kubernetes-kluster. Kluster administratörer granskar status ändrings PR och godkänner sammanslagningen till GitOps-lagrings platsen. Pipelinen väntar sedan på att PR-processen ska slutföras, vilket gör att flödet kan hämta tillstånds ändringen.
### <a name="gitops-repo"></a>GitOps lagrings platsen
GitOps-lagrings platsen representerar det aktuella önskade läget för alla miljöer i kluster. Alla ändringar av den här lagrings platsen hämtas av flödes tjänsten i varje kluster och distribueras. Pull skapas med ändringar i det önskade läget, granskas och sammanfogas. Dessa pull innehåller ändringar i både distributionsmall och de resulterande åter givnings Kubernetes manifesten. Manifest med låg nivå som återges ger en mer noggrann granskning av förändringar vanligt vis osett på mallnivå.
### <a name="kubernetes-clusters"></a>Kubernetes-kluster
Minst ett Azure Arc-aktiverade Kubernetes-kluster hanterar de olika miljöer som programmet behöver. Till exempel kan ett enskilt kluster betjäna både en utvecklings-och miljö kontroll miljö via olika namn områden. Ett andra kluster kan ge enklare separering av miljöer och mer detaljerad kontroll.
## <a name="example-workflow"></a>Exempel arbets flöde
Som programutvecklare, Alice:
* Skriver program kod.
* Anger hur programmet ska köras i en Docker-behållare.
* Definierar de mallar som kör behållaren och beroende tjänster i ett Kubernetes-kluster.

Alice vet att programmet behöver kunna köra i flera miljöer, men hon känner inte till de specifika inställningarna för varje miljö.

Anta att Alice vill göra en program ändring som ändrar den Docker-avbildning som används i mallen för program distribution.

1. Alice ändrar distributions mal len, skickar den till en fjärran sluten gren i programmet lagrings platsen och öppnar en PR för granskning.
2. Alice ber hennes team att granska ändringen.
    * PR pipeline kör verifieringen.
    * När en lyckad pipeline har körts, loggar det ut och ändringen slås samman.
3. CI-pipeline validerar Alices ändringar och slutförs.
    * Ändringen är säker att distribuera till klustret och artefakterna sparas i körningen av CI-pipeline.
4. Alices ändrings sammanslagningar och utlöser CD-pipelinen.
    * CD-pipeline hämtar de artefakter som lagras av Alices pipeline-körning.
    * CD-pipelinen byter ut mallarna efter miljöbaserade värden och steg för steg alla ändringar mot det befintliga klustrets tillstånd i GitOps-lagrings platsen.
    * CD-pipeline skapar en PR till GitOps-lagrings platsen med önskade ändringar i kluster tillstånd.
5. Alices team granskar och godkänner hennes PR.
    * Ändringen slås samman i mål grenen som motsvarar miljön.
6. I minuter uppmärksammar flödes ändringar i GitOps-lagrings platsen och drar Karins ändringar.
    * På grund av Docker-avbildnings ändringen kräver programmet Pod en uppdatering.
    * Flödet tillämpar ändringen i klustret.
7. Alice testar program slut punkten för att kontrol lera att distributionen har slutförts.
   > [!NOTE]
   > För att fler miljöer ska kunna distribueras kan CD-pipeline upprepas genom att skapa en PR för nästa miljö och upprepa steg 4-7. Processen många behöver extra godkännande för riskier-distributioner eller miljöer, till exempel en säkerhetsrelaterad ändring eller produktions miljö.
8.  När alla miljöer har tagit emot lyckade distributioner slutförs pipelinen.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om att skapa anslutningar mellan klustret och en git-lagringsplats som en [konfigurations resurs med Azure Arc-aktiverade Kubernetes](./conceptual-configurations.md)
