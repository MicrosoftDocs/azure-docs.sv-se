---
title: Så här felsöker du container Insights | Microsoft Docs
description: Den här artikeln beskriver hur du kan felsöka och lösa problem med behållar insikter.
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 60a6e76d43d954b27336b9631c48328aeff0b69b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708313"
---
# <a name="troubleshooting-container-insights"></a>Felsöka behållar insikter

När du konfigurerar övervakning av ditt Azure Kubernetes service-kluster (AKS) med behållar insikter kan du stöta på ett problem som förhindrar data insamling eller rapporterings status. I den här artikeln beskrivs några vanliga problem och fel söknings steg.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Auktoriseringsfel vid registrering eller uppdatering

När du aktiverar behållar insikter eller uppdaterar ett kluster för att stödja insamling av mått, kan det hända att du får ett fel som liknar följande – *klientens <användarens identitet> "med objekt-ID" <användarens objectId> "har inte behörighet att utföra åtgärden" Microsoft. Authorization/roleAssignments/Write "över omfattning*

Under onboarding-eller uppdaterings processen görs försök att tilldela **övervaknings mått utgivar** roll tilldelningen på kluster resursen. Användaren som initierar processen för att aktivera behållar insikter eller uppdateringen för att stödja insamling av mått måste ha åtkomst till **Microsoft. Authorization/roleAssignments/Write-** behörigheten för kluster resurs omfånget i AKS. Endast medlemmar i de inbyggda rollerna **ägare** och **användar åtkomst administratör** beviljas åtkomst till den här behörigheten. Om dina säkerhets principer kräver att du tilldelar detaljerade nivå behörigheter rekommenderar vi att du visar [anpassade roller](../../role-based-access-control/custom-roles.md) och tilldelar dem till de användare som behöver den.

Du kan också bevilja rollen manuellt från Azure Portalen genom att utföra följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. I Azure Portal klickar du på **Alla tjänster** längst upp till vänster. Skriv **Kubernetes** i listan över resurser. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Azure-Kubernetes**.
3. I listan över Kubernetes-kluster väljer du en i listan.
2. I den vänstra menyn klickar du på **åtkomst kontroll (IAM)**.
3. Välj **+ Lägg** till för att lägga till en roll tilldelning och välj **utgivar rollen övervaknings mått** och välj **AKS** i rutan **Välj** typ för att filtrera resultaten för bara de kluster tjänstens huvud namn som definierats i prenumerationen. Välj den från listan som är speciell för klustret.
4. Välj **Spara** för att slutföra tilldelningen av rollen.

## <a name="container-insights-is-enabled-but-not-reporting-any-information"></a>Container Insights är aktiverat men rapporterar ingen information

Om behållar insikter har Aktiver ATS och kon figurer ATS, men du inte kan visa statusinformation eller inga resultat returneras från en logg fråga, diagnostiserar du problemet genom att följa dessa steg:

1. Kontrol lera agentens status genom att köra kommandot:

    `kubectl get ds omsagent --namespace=kube-system`

    Utdata bör likna följande exempel, vilket indikerar att det har distribuerats korrekt:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```
2. Om du har Windows Server-noder kontrollerar du statusen för agenten genom att köra kommandot:

    `kubectl get ds omsagent-win --namespace=kube-system`

    Utdata bör likna följande exempel, vilket indikerar att det har distribuerats korrekt:

    ```
    User@aksuser:~$ kubectl get ds omsagent-win --namespace=kube-system
    NAME                   DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                   AGE
    omsagent-win           2         2         2         2            2           beta.kubernetes.io/os=windows   1d
    ```
3. Kontrol lera distributions statusen med agent version *06072018* eller senare med hjälp av kommandot:

    `kubectl get deployment omsagent-rs -n=kube-system`

    Utdata bör likna följande exempel, vilket indikerar att det har distribuerats korrekt:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

4. Kontrol lera status för Pod för att kontrol lera att den körs med kommandot: `kubectl get pods --namespace=kube-system`

    Utdata bör likna följande exempel med statusen *körs* för omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system
    NAME                                READY     STATUS    RESTARTS   AGE
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d
    omsagent-484hw                      1/1       Running   0          1d
    omsagent-fkq7g                      1/1       Running   0          1d
    omsagent-win-6drwq                  1/1       Running   0          1d
    ```

## <a name="error-messages"></a>Felmeddelanden

I tabellen nedan sammanfattas kända fel som kan uppstå när du använder behållar insikter.

| Felmeddelanden  | Action |
| ---- | --- |
| Fel meddelande `No data for selected filters`  | Det kan ta lite tid att upprätta övervakningsdataflödet för kluster som skapats nyligen. Tillåt minst 10 till 15 minuter innan data visas för klustret. |
| Fel meddelande `Error retrieving data` | Även om Azure Kubernetes service-klustret konfigurerar för hälso-och prestanda övervakning upprättas en anslutning mellan klustret och Azure Log Analytics-arbetsytan. En Log Analytics arbets yta används för att lagra alla övervaknings data för klustret. Det här felet kan inträffa när din Log Analytics-arbetsyta har tagits bort. Kontrol lera att arbets ytan har tagits bort och om den var måste du återaktivera övervakning av klustret med behållar insikter och ange en befintlig eller skapa en ny arbets yta. Om du vill aktivera igen måste du [inaktivera](container-insights-optout.md) övervakning av klustret och [Aktivera](container-insights-enable-new-cluster.md) behållar insikter igen. |
| `Error retrieving data` När du har lagt till behållar insikter via AZ AKS cli | När Aktivera övervakning används `az aks cli` kanske behållar insikter inte distribueras korrekt. Kontrol lera om lösningen har distribuerats. Verifiera genom att gå till din Log Analytics arbets yta och se om lösningen är tillgänglig genom att välja **lösningar** i rutan till vänster. För att lösa det här problemet måste du distribuera om lösningen genom att följa anvisningarna för [hur du distribuerar behållar insikter](container-insights-onboard.md) |

Vi har angett ett [fel söknings skript](https://github.com/microsoft/Docker-Provider/tree/ci_dev/scripts/troubleshoot)för att hjälpa till att diagnostisera problemet.

## <a name="container-insights-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>Container Insights-agenten ReplicaSet poddar har inte schemalagts för icke-Azure Kubernetes-kluster

Container Insights-agenten ReplicaSet poddar har ett beroende på följande Node-väljare på Worker-noder (eller agent) för schemaläggning:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Om dina arbetsnoder inte har några Node-etiketter, kommer agenten ReplicaSet poddar inte att bli schemalagd. Läs mer i [Kubernetes tilldela etikett väljare](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) för instruktioner om hur du lägger till etiketten.

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Prestanda diagram visar inte CPU eller minne för noder och behållare i ett icke-Azure-kluster

Poddar för container Insights använder cAdvisor-slutpunkten på Node-agenten för att samla in prestanda måtten. Kontrol lera att den behållar agenten på noden är konfigurerad att tillåtas att `cAdvisor port: 10255` öppnas på alla noder i klustret för att samla in prestanda mått.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-container-insights"></a>Icke-Azure Kubernetes-kluster visas inte i behållar insikter

Om du vill visa ett icke-Azure Kubernetes-kluster i behållar insikter krävs Läs åtkomst på Log Analytics arbets ytan som stöder den här insikten och i ContainerInsights för container Insights **-lösning (*arbets yta*)**.

## <a name="next-steps"></a>Nästa steg

När övervakning har Aktiver ATS för att fånga hälso mått för både AKS-klusternoder och poddar är dessa hälso mått tillgängliga i Azure Portal. Information om hur du använder behållar insikter finns i [Visa Azure Kubernetes service Health](container-insights-analyze.md).
