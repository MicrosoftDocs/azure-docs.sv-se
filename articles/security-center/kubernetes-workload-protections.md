---
title: Arbets belastnings skydd för dina Kubernetes-arbetsbelastningar
description: Lär dig hur du använder Azure Security Centers uppsättning säkerhets rekommendationer för arbets belastnings skydd i Kubernetes
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/16/2021
ms.author: memildin
ms.openlocfilehash: 6d0e660ecce1d45dab4d6003cdba391ba2eb4ee9
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095603"
---
# <a name="protect-your-kubernetes-workloads"></a>Skydda dina Kubernetes-arbetsbelastningar

Den här sidan beskriver hur du använder Azure Security Centers uppsättning säkerhets rekommendationer som är reserverade för Kubernetes-arbetsbelastnings skydd.

Lär dig mer om de här funktionerna i [metod tips för arbets belastnings skydd med Kubernetes-åtkomstkontroll](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

Security Center innehåller fler säkerhets funktioner för behållare om du aktiverar Azure Defender. Specifikt:

- Sök igenom behållar register för sårbarheter med [Azure Defender för behållar register](defender-for-container-registries-introduction.md)
- Få aviseringar om hot identifiering i real tid för dina K8s-kluster [Azure Defender för Kubernetes](defender-for-kubernetes-introduction.md)

> [!TIP]
> En lista över *alla* säkerhets rekommendationer som kan visas för Kubernetes-kluster och-noder finns i [avsnittet Compute](recommendations-reference.md#recs-compute) i referens tabellen rekommendationer.



## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmän tillgänglighet (GA)|
|Priset|Kostnadsfri|
|Nödvändiga roller och behörigheter:|**Ägare** eller **säkerhets administratör** för att redigera en tilldelning<br>**Läsare** för att Visa rekommendationerna|
|Miljö krav:|Kubernetes v-1.14 (eller högre) krävs<br>Ingen PodSecurityPolicy-resurs (gammal PSP-modell) i klustren<br>Windows-noder stöds inte|
|Moln|![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Ja](./media/icons/yes-icon.png) National/suverän (US Gov, Kina gov, andra gov)|
|||


## <a name="set-up-your-workload-protection"></a>Konfigurera ditt arbets belastnings skydd

Azure Security Center innehåller en samling rekommendationer som är tillgängliga när du har installerat **Azure policy-tillägget för Kubernetes**.

### <a name="step-1-deploy-the-add-on"></a>Steg 1: Distribuera tillägget

Konfigurera rekommendationerna genom att installera  **Azure policy-tillägget för Kubernetes**. 

- Du kan distribuera tillägget automatiskt enligt beskrivningen i [Aktivera automatisk etablering av Log Analytics agent och tillägg](security-center-enable-data-collection.md#auto-provision-mma). När automatisk etablering för tillägget har angetts till "på" aktive ras tillägget som standard i alla befintliga och framtida kluster (som uppfyller installations kraven för tillägg).

    :::image type="content" source="media/defender-for-kubernetes-usage/policy-add-on-auto-provision.png" alt-text="Använda Security Centers automatiska etablerings verktyg för att installera princip tillägg för Kubernetes":::

- Så här distribuerar du tillägget manuellt:

    1. På sidan rekommendationer söker du efter rekommendationen "**Azure policy tillägg för Kubernetes ska installeras och aktive ras i klustren**". 

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes.png" alt-text="Rekommendation * * Azure Policy tillägg för Kubernetes bör installeras och aktive ras i klustren * *":::

        > [!TIP]
        > Rekommendationen ingår i fem olika säkerhets kontroller och det spelar ingen roll vilket du väljer i nästa steg.

    1. Från någon av säkerhets kontrollerna väljer du rekommendationen för att se vilka resurser du kan installera tillägget på.
    1. Välj lämpligt kluster och **åtgärda**.

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes-details.png" alt-text="Rekommendations informations sidan för * * Azure Policy tillägg för Kubernetes bör installeras och aktive ras i klustren * *":::

### <a name="step-2-view-and-configure-the-bundle-of-13-recommendations"></a>Steg 2: Visa och konfigurera paketet med 13 rekommendationer

1. Ungefär 30 minuter efter att installationen har slutförts, visar Security Center hälso status för klustrets hälso status för följande rekommendationer, var och en i den relevanta säkerhets kontrollen som visas:

    > [!TIP]
    > Vissa rekommendationer har parametrar som måste anpassas via Azure Policy för att kunna använda dem effektivt. Om du till exempel vill dra nytta av rekommendations **behållar avbildningarna ska endast distribueras från betrodda register**, måste du definiera dina betrodda register.
    > 
    > Om du inte anger de nödvändiga parametrarna för de rekommendationer som kräver konfiguration, visas dina arbets belastningar som felaktiga.

    | Rekommendations namn                                                         | Säkerhets kontroll                         | Konfiguration krävs |
    |-----------------------------------------------------------------------------|------------------------------------------|------------------------|
    | CPU-och minnes gränser för containern ska tillämpas                          | Skydda program mot DDoS-attacker | Inga                     |
    | Privilegierade behållare bör undvikas                                     | Hantera åtkomst och behörigheter            | Inga                     |
    | Oföränderligt (skrivskyddat) rot fil system ska tillämpas för behållare     | Hantera åtkomst och behörigheter            | Inga                     |
    | Container med behörighets eskalering bör undvikas                       | Hantera åtkomst och behörigheter            | Inga                     |
    | Att köra behållare som rot användare bör undvikas                           | Hantera åtkomst och behörigheter            | Inga                     |
    | Behållare som delar känsliga värd namn områden bör undvikas              | Hantera åtkomst och behörigheter            | Inga                     |
    | Minst privilegierade Linux-funktioner bör tillämpas för behållare       | Hantera åtkomst och behörigheter            | **Ja**                |
    | Användningen av Pod HostPath-volym monteringar bör begränsas till en känd lista    | Hantera åtkomst och behörigheter            | **Ja**                |
    | Behållare ska bara lyssna på tillåtna portar                              | Begränsa obehörig nätverks åtkomst     | **Ja**                |
    | Tjänsterna ska bara lyssna på tillåtna portar                                | Begränsa obehörig nätverks åtkomst     | **Ja**                |
    | Användning av värd nätverk och portar ska begränsas                     | Begränsa obehörig nätverks åtkomst     | **Ja**                |
    | Åsidosättning eller inaktive ring av behållare AppArmor-profilen bör vara begränsad | Åtgärda säkerhetskonfigurationer        | **Ja**                |
    | Behållar avbildningar ska endast distribueras från betrodda register            | Åtgärda sårbarheter                | **Ja**                |
    |||


1. För rekommendationer med parametrar måste anpassas anger du parametrarna:

    1. Från Security Center menyn väljer du **säkerhets princip**.
    1. Välj relevant prenumeration.
    1. I avsnittet **Security Center standard princip** väljer du **Visa gällande princip**.
    1. Välj "ASC default".
    1. Öppna fliken **parametrar** och ändra värdena efter behov.
    1. Välj **Granska och spara**.
    1. Välj **Spara**.


1. För att genomdriva någon av rekommendationerna 

    1. Öppna sidan rekommendations information och välj **neka**:

        :::image type="content" source="./media/defender-for-kubernetes-usage/enforce-workload-protection-example.png" alt-text="Neka alternativ för Azure Policy parameter":::

        Då öppnas fönstret där du anger omfånget. 

    1. När du har angett omfånget väljer du **ändra till neka**.

1. Så här ser du vilka rekommendationer som gäller för dina kluster:

    1. Öppna Security Center [till gångs inventerings](asset-inventory.md) sidan och Använd resurs typs filtret för att **Kubernetes tjänster**.

    1. Välj ett kluster för att undersöka och granska tillgängliga rekommendationer som är tillgängliga för det. 

1. När du visar en rekommendation från arbets belastnings skydds uppsättningen visas antalet påverkade poddar ("Kubernetes-komponenter") som visas tillsammans med klustret. Om du vill visa en lista över den aktuella poddar väljer du klustret och väljer sedan **vidta åtgärd**.

    :::image type="content" source="./media/defender-for-kubernetes-usage/view-affected-pods-for-recommendation.gif" alt-text="Visa den berörda poddar för en K8s-rekommendation"::: 

1. Om du vill testa genomförandet använder du de två Kubernetes-distributionerna nedan:

    - En är för en felfri distribution som är kompatibel med paketet med rekommendationer för skydd av arbets belastning.
    - Den andra är en icke-kompatibel distribution, som inte är kompatibel med *någon* av rekommendationerna.

    Distribuera exempel. yaml-filerna som de är, eller Använd dem som en referens för att reparera din egen arbets belastning (steg VIII)  


## <a name="healthy-deployment-example-yaml-file"></a>Felfritt distributions exempel. yaml-fil

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-healthy-deployment
  labels:
    app: redis
spec:
  replicas: 3
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
      annotations:
        apparmor.security.beta.kubernetes.io/pod: runtime/default
        container.apparmor.security.beta.kubernetes.io/redis: runtime/default
    spec:
      containers:
      - name: redis
        image: healthyClusterRegistry.azurecr.io/redis:latest
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: 100m
            memory: 250Mi
        securityContext:
          privileged: false
          readOnlyRootFilesystem: true
          allowPrivilegeEscalation: false
          runAsNonRoot: true
          runAsUser: 1000
---
apiVersion: v1
kind: Service
metadata:
  name: redis-healthy-service
spec:
  type: LoadBalancer
  selector:
    app: redis
  ports:
  - port: 80
    targetPort: 80
```

## <a name="unhealthy-deployment-example-yaml-file"></a>Exempel på felaktig distributions exempel. yaml-fil

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-unhealthy-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:      
      labels:
        app: nginx
    spec:
      hostNetwork: true
      hostPID: true 
      hostIPC: true
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 9001
          hostPort: 9001
        securityContext:
          privileged: true
          readOnlyRootFilesystem: false
          allowPrivilegeEscalation: true
          runAsUser: 0
          capabilities:
            add:
              - NET_ADMIN
        volumeMounts:
        - mountPath: /test-pd
          name: test-volume
          readOnly: true
      volumes:
      - name: test-volume
        hostPath:
          # directory location on host
          path: /tmp
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-unhealthy-service
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
  - port: 6001
    targetPort: 9001
```



## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du konfigurerar Kubernetes-arbetsbelastnings skydd. 

För annat relaterat material, se följande sidor: 

- [Security Center rekommendationer för beräkning](recommendations-reference.md#recs-compute)
- [Aviseringar för AKS-kluster nivå](alerts-reference.md#alerts-akscluster)
- [Aviseringar för värd nivå för behållare](alerts-reference.md#alerts-containerhost)