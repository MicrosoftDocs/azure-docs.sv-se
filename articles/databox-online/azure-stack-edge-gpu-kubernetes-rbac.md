---
title: Förstå Kubernetes-rollbaserad åtkomst kontroll på Azure Stack Edge Pro-enhet | Microsoft Docs
description: Beskriver hur Kubernetes-rollbaserad åtkomst kontroll sker på en Azure Stack Edge Pro-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 1b38a9807e05385a378fa6103710fb6b393c7b1f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443156"
---
# <a name="kubernetes-role-based-access-control-on-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes-rollbaserad åtkomst kontroll på din Azure Stack Edge Pro GPU-enhet

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

När du konfigurerar Compute-rollen på din Azure Stack Edge Pro-enhet skapas ett Kubernetes-kluster. Du kan använda Kubernetes-rollbaserad åtkomst kontroll (Kubernetes RBAC) om du vill begränsa åtkomsten till kluster resurserna på enheten.

Den här artikeln innehåller en översikt över det Kubernetes RBAC-system som tillhandahålls av Kubernetes, och hur är Kubernetes RBAC implementerad på din Azure Stack Edge Pro-enhet. 

## <a name="kubernetes-rbac"></a>Kubernetes RBAC

Med Kubernetes RBAC kan du tilldela användare eller grupper av användare behörighet att göra saker som att skapa eller ändra resurser, eller Visa loggar från att köra program arbets belastningar. Dessa behörigheter kan begränsas till ett enda namn område eller beviljas i hela klustret. 

När du konfigurerar Kubernetes-klustret skapas en enskild användare som motsvarar det här klustret och kallas för kluster administratörs användaren.  En `kubeconfig` fil är kopplad till kluster administratörs användaren. `kubeconfig`Filen är en textfil som innehåller all konfigurations information som krävs för att ansluta till klustret för att autentisera användaren.

## <a name="namespaces-types"></a>Typer av namn områden

Kubernetes-resurser, till exempel poddar och distributioner, grupperas logiskt i ett namn område. Dessa grupperingar ger ett sätt att logiskt dela upp ett Kubernetes-kluster och begränsa åtkomsten till att skapa, Visa eller hantera resurser. Användare kan bara interagera med resurser inom de tilldelade namn områdena.

Namn områden är avsedda att användas i miljöer med många användare som sprids över flera team eller projekt. Mer information finns i [Kubernetes-namnområden](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/).

Din Azure Stack Edge Pro-enhet har följande namn rymder:

- **System namn område** – det här namn området är där kärn resurser finns, till exempel nätverks funktioner som DNS och proxy eller Kubernetes-instrumentpanelen. Du distribuerar vanligt vis inte dina egna program till det här namn området. Använd det här namn området för att felsöka problem med Kubernetes-kluster. 

    Det finns flera system namn rymder på enheten och namnen som motsvarar dessa system namn rymder är reserverade. Här är en lista över reserverade system namn rymder: 
    - kube-system
    - metallb-system
    - DBE – namnrymd
    - standard
    - Kubernetes-instrument panel
    - Kube-nod-Lease
    - kube-public


    Se till att inte använda reserverade namn för användar namn rymder som du skapar. 
<!--- **default namespace** - This namespace is where pods and deployments are created by default when none is provided and you have admin access to this namespace. When you interact with the Kubernetes API, such as with `kubectl get pods`, the default namespace is used when none is specified.-->

- **Användar namn område** – det här är de namn områden som du kan skapa via **Kubectl** eller via PowerShell-gränssnittet på enheten för att distribuera program lokalt.
 
- **IoT Edge namnrymd** – du ansluter till det här `iotedge` namn området för att hantera program som distribueras via IoT Edge.

- **Azure Arc-namnrymd** – du ansluter till det här `azure-arc` namn området för att hantera program som distribueras via Azure Arc. Med Azure ARC kan du också distribuera program i andra användar namn områden. 

## <a name="namespaces-and-users"></a>Namn områden och användare

I den verkliga världen är det viktigt att dela upp klustret i flera namn områden. 

- **Flera användare**: om du har flera användare kommer flera namn områden att tillåta dessa användare att distribuera sina program och tjänster i sina egna namn rymder i isoleringen från varandra. 
- **Enskild användare**: även om det finns en enskild användare, tillåter flera namn områden att användaren kör flera versioner av programmen i samma Kubernetes-kluster.

### <a name="roles-and-rolebindings"></a>Roller och RoleBindings

Kubernetes har begreppet roll-och roll bindning som gör att du kan ge behörigheter till användare eller resurser på en namn områdes nivå och på kluster nivå. 

- **Roller**: du kan definiera behörigheter till användare som en **roll** och sedan använda **roller** för att bevilja behörigheter inom ett namn område. 
- **RoleBindings**: när du har definierat rollerna kan du använda **RoleBindings** för att tilldela roller för en viss namnrymd. 

Med den här metoden kan du logiskt särskilja ett enda Kubernetes-kluster, där användare bara kan komma åt program resurserna i sitt tilldelade namn område. 

## <a name="kubernetes-rbac-on-azure-stack-edge-pro"></a>Kubernetes RBAC på Azure Stack Edge Pro

I den aktuella implementeringen av Kubernetes RBAC kan du med Azure Stack Edge Pro vidta följande åtgärder från en begränsad PowerShell-körnings utrymme:

- Skapa namn områden.  
- Skapa ytterligare användare.
- Ge dig administratörs åtkomst till de namn områden som du har skapat. Tänk på att du inte har åtkomst till rollen kluster administratör eller en vy över resurserna i klustret.
- Hämta `kubeconfig` filen med information för att få åtkomst till Kubernetes-klustret.


Azure Stack Edge Pro-enheten har flera system namn rymder och du kan skapa användar namn områden med `kubeconfig` filer för att komma åt dessa namn områden. Användarna har fullständig kontroll över dessa namn områden och kan skapa eller ändra användare eller bevilja användare åtkomst. Endast kluster administratören har fullständig åtkomst till system namn rymder och resurser i hela klustret. En `aseuser` har skrivskyddad åtkomst till system namn rymder.

Här är ett diagram som illustrerar implementeringen av Kubernetes RBAC på Azure Stack Edge Pro-enhet.

![Kubernetes RBAC på Azure Stack Edge Pro-enhet](./media/azure-stack-edge-gpu-kubernetes-rbac/rbac-view-1.png)

I det här diagrammet har Alice, Bob och Klas endast åtkomst till tilldelade användar namn områden, som i det här fallet är `ns1` , `ns2` `ns3` respektive. I dessa namn områden har de administratörs åtkomst. Kluster administratören å andra sidan har administratörs behörighet för system namn rymder och resurser i hela klustret.

Som användare kan du skapa namn områden och användare, tilldela användare till namn områden eller ladda ned `kubeconfig` filer. Detaljerade steg-för-steg-anvisningar finns i [komma åt Kubernetes-kluster via kuebctl på din Azure Stack Edge Pro](azure-stack-edge-gpu-create-kubernetes-cluster.md).


När du arbetar med namn områden och användare på dina Azure Stack Edge Pro-enheter gäller följande villkor:

- Du får inte utföra några åtgärder, till exempel skapa användare, bevilja eller återkalla namn områdes åtkomst till användare, för alla system namn rymder. Exempel på system namn rymder är,,,, `kube-system` `metallb-system` `kubernetes-dashboard` `default` `kube-node-lease` , `kube-public` . System namn rymder innehåller också de namn områden som är reserverade för distributions typer som `iotedge` (IoT Edge namnrymd) och `azure-arc` (Azure Arc-namnrymd).
- Du kan skapa användar namn rymder och inom dessa namn områden, skapa ytterligare användare och bevilja eller återkalla namn områdes åtkomst till dessa användare.
- Du får inte skapa några namn rymder med namn som är identiska med de för alla system namn rymder. Namnen på system namn rymder är reserverade.  
- Du får inte skapa några användar namn rymder med namn som redan används av andra användar namn områden. Om du till exempel har skapat en `test-ns` som du har skapat kan du inte skapa en annan `test-ns` namnrymd.
- Du får inte skapa användare med namn som redan har reserver ATS. Till exempel `aseuser` är en reserverad användare och kan inte användas.


## <a name="next-steps"></a>Nästa steg

Information om hur du skapar en användare, skapar ett namn område och beviljar användar åtkomst till namn området finns i [komma åt ett Kubernetes-kluster via kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).

