---
title: Skapa datakontrollant i Azure Data Studio
description: Skapa datakontrollant i Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 2b35abf7f318d4db6166dd48c4f7bd35aaf4d98a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644416"
---
# <a name="create-data-controller-in-azure-data-studio"></a>Skapa datakontrollant i Azure Data Studio

Du kan skapa en datakontrollant med Azure Data Studio via distributions guiden och antecknings böckerna.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

- Du behöver åtkomst till ett Kubernetes-kluster och har kubeconfig-filen konfigurerad så att den pekar på det Kubernetes-kluster som du vill distribuera till.
- Du måste [Installera klient verktygen](install-client-tools.md) , inklusive **Azure Data Studio** Azure Data Studio tillägg som kallas **Azure Arc** och **[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]** .
- Du måste logga in på Azure i Azure Data Studio.  Gör så här: Skriv CTRL/Command + SHIFT + P för att öppna fönstret kommando text och skriv **Azure**.  Välj **Azure: Logga** in.   I panelen, som visas klickar du på +-ikonen längst upp till höger för att lägga till ett Azure-konto.

## <a name="use-the-deployment-wizard-to-create-azure-arc-data-controller"></a>Använd distributions guiden för att skapa data styrenheten för Azure Arc

Följ dessa steg om du vill skapa en Azure Arc-dataenhet med hjälp av distributions guiden.

1. I Azure Data Studio klickar du på fliken anslutningar i det vänstra navigerings fältet.
2. Klicka på knappen **...** överst på panelen anslutningar och välj **ny distribution...**
3. I guiden Ny distribution väljer du **Azure Arc data Controller** och klickar sedan på knappen **Välj** längst ned.
4. Se till att de nödvändiga verktygen är tillgängliga och uppfyller de versioner som krävs. **Klicka på nästa**.
5. Använd standard filen kubeconfig eller Välj en annan.  Klicka på **Nästa**.
6. Välj en Kubernetes-kluster kontext. Klicka på **Nästa**.
7. Välj en distributions konfigurations profil beroende på ditt mål Kubernetes-kluster. **Klicka på nästa**.
8. Använd säkerhets kontext begränsningar om du använder en "Red Hat OpenShift"-eller Red Hat OpenShift-plattform. Följ anvisningarna i [tillämpa en säkerhets kontext begränsning för Azure Arc-aktiverade data tjänster i OpenShift](how-to-apply-security-context-constraint.md).

   >[!IMPORTANT]
   >På Azure Red Hat OpenShift eller Red Hat OpenShift container Platform måste du tillämpa begränsningen för säkerhets kontext innan du skapar data kontrollen.

1. Välj önskad prenumeration och resurs grupp.
1. Välj en Azure-plats.
   
   Azure-platsen som väljs här är den plats i Azure där *metadata* om data styrenheten och databas instanserna som den hanterar kommer att lagras. Data styrenheten och databas instanserna kommer att faktiskt crewted i ditt Kubernetes-kluster, oavsett var de befinner sig.

10. Välj lämpligt anslutnings läge. Läs mer om [anslutnings lägen](./connectivity.md). **Klicka på nästa**.

    Om du väljer tjänstens huvud namn måste autentiseringsuppgifter för tjänsten för direkt anslutning anges enligt beskrivningen i [skapa tjänstens huvud namn](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal).

11. Ange ett namn för data styrenheten och för det namn område som data styrenheten ska skapas i.

    Data styrenheten och namn områdets namn kommer att användas för att skapa en anpassad resurs i Kubernetes-klustret så att de måste följa [namn konventionerna för Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#names).
    
    Om namn området redan finns kommer det att användas om namn området inte redan innehåller andra Kubernetes-objekt – poddar osv.  Om namn området inte finns skapas ett försök att skapa namn området.  Att skapa ett namn område i ett Kubernetes-kluster kräver administratörs behörighet för Kubernetes-kluster.  Om du inte har administratörs behörighet för Kubernetes kan du be din Kubernetes-kluster administratör att utföra de första stegen i artikeln [skapa en datakontrollant med Kubernetes-Native-verktyg](./create-data-controller-using-kubernetes-native-tools.md) som måste utföras av en Kubernetes-administratör innan du slutför den här guiden.


12. Välj den lagrings klass där data styrenheten ska distribueras. 
13.  Ange ett användar namn och lösen ord och bekräfta lösen ordet för användar kontot för data styrenhetens administratör. Klicka på **Nästa**.

14. Granska distributions konfigurationen.
15. Klicka på **distribuera** för att distribuera önskad konfiguration eller **skript till antecknings boken** för att granska distributions anvisningarna eller göra eventuella ändringar som krävs, till exempel lagrings klass namn eller tjänst typer. Klicka på **Kör allt** längst upp i antecknings boken.

## <a name="monitoring-the-creation-status"></a>Övervaka skapande status

Det tar några minuter att slutföra skapandet av kontrollanten. Du kan övervaka förloppet i ett annat terminalfönster med följande kommandon:

> [!NOTE]
>  Exempel kommandona nedan förutsätter att du har skapat en datakontrollant och ett Kubernetes-namnområde med namnet "Arc".  Om du har använt ett annat namn på namn område/data enhet kan du ersätta "Arc" med ditt namn.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Du kan också kontrol lera skapande status för alla specifika Pod genom att köra ett kommando som nedan.  Detta är särskilt användbart vid fel sökning av problem.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Fel sökning av problem med skapande

Om du stöter på problem med att skapa kan du läsa [fel söknings guiden](troubleshoot-guide.md).