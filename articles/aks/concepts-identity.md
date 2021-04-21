---
title: Begrepp – åtkomst och identitet i Azure Kubernetes Services (AKS)
description: Lär dig mer om åtkomst och identitet i Azure Kubernetes Service (AKS), inklusive Azure Active Directory-integration, Kubernetes rollbaserad åtkomstkontroll (Kubernetes RBAC) och roller och bindningar.
services: container-service
ms.topic: conceptual
ms.date: 03/24/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: b10d31cf069bc4f28a1597ec12160fa6ed98b8ce
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789563"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Åtkomst och identitetsalternativ för Azure Kubernetes Service (AKS)

Du kan autentisera, auktorisera, skydda och kontrollera åtkomst till Kubernetes-kluster på flera olika sätt. 
* Med kubernetes rollbaserad åtkomstkontroll (Kubernetes RBAC) kan du endast ge användare, grupper och tjänstkonton åtkomst till de resurser som de behöver. 
* Med Azure Kubernetes Service (AKS) kan du ytterligare förbättra säkerhets- och behörighetsstrukturen via Azure Active Directory och Azure RBAC. 

Kubernetes RBAC och AKS hjälper dig att skydda klusteråtkomsten och ger endast de minsta nödvändiga behörigheterna till utvecklare och operatörer.

Den här artikeln beskriver huvudbegreppen som hjälper dig att autentisera och tilldela behörigheter i AKS.

## <a name="aks-service-permissions"></a>AKS-tjänstbehörigheter

När du skapar ett kluster genererar eller ändrar AKS resurser som behövs (till exempel virtuella datorer och nätverkskort) för att skapa och köra klustret för användarens räkning. Den här identiteten skiljer sig från klustrets identitetsbehörighet, som skapas när klustret skapas.

### <a name="identity-creating-and-operating-the-cluster-permissions"></a>Identitet som skapar och använder klusterbehörigheterna

Följande behörigheter krävs av identiteten som skapar och använder klustret.

| Behörighet | Anledning |
|---|---|
| `Microsoft.Compute/diskEncryptionSets/read` | Krävs för att läsa ID för diskkrypteringsuppsättning. |
| `Microsoft.Compute/proximityPlacementGroups/write` | Krävs för att uppdatera närhetsplaceringsgrupper. |
| `Microsoft.Network/applicationGateways/read` <br/> `Microsoft.Network/applicationGateways/write` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Krävs för att konfigurera programgatewayer och ansluta till undernätet. |
| `Microsoft.Network/virtualNetworks/subnets/join/action` | Krävs för att konfigurera nätverkssäkerhetsgruppen för undernätet när du använder ett anpassat VNET.|
| `Microsoft.Network/publicIPAddresses/join/action` <br/> `Microsoft.Network/publicIPPrefixes/join/action` | Krävs för att konfigurera utgående offentliga IP-adresser på Standard Load Balancer. |
| `Microsoft.OperationalInsights/workspaces/sharedkeys/read` <br/> `Microsoft.OperationalInsights/workspaces/read` <br/> `Microsoft.OperationsManagement/solutions/write` <br/> `Microsoft.OperationsManagement/solutions/read` <br/> `Microsoft.ManagedIdentity/userAssignedIdentities/assign/action` | Krävs för att skapa och uppdatera Log Analytics-arbetsytor och Azure-övervakning för containrar. |

### <a name="aks-cluster-identity-permissions"></a>Behörigheter för AKS-klusteridentitet

Följande behörigheter används av AKS-klusteridentiteten, som skapas och associeras med AKS-klustret. Varje behörighet används av skälen nedan:

| Behörighet | Anledning |
|---|---|
| `Microsoft.ContainerService/managedClusters/*`  <br/> | Krävs för att skapa användare och driva klustret
| `Microsoft.Network/loadBalancers/delete` <br/> `Microsoft.Network/loadBalancers/read` <br/> `Microsoft.Network/loadBalancers/write` | Krävs för att konfigurera lastbalanserare för en LoadBalancer-tjänst. |
| `Microsoft.Network/publicIPAddresses/delete` <br/> `Microsoft.Network/publicIPAddresses/read` <br/> `Microsoft.Network/publicIPAddresses/write` | Krävs för att hitta och konfigurera offentliga IP-adresser för en LoadBalancer-tjänst. |
| `Microsoft.Network/publicIPAddresses/join/action` | Krävs för att konfigurera offentliga IP-adresser för en LoadBalancer-tjänst. |
| `Microsoft.Network/networkSecurityGroups/read` <br/> `Microsoft.Network/networkSecurityGroups/write` | Krävs för att skapa eller ta bort säkerhetsregler för en LoadBalancer-tjänst. |
| `Microsoft.Compute/disks/delete` <br/> `Microsoft.Compute/disks/read` <br/> `Microsoft.Compute/disks/write` <br/> `Microsoft.Compute/locations/DiskOperations/read` | Krävs för att konfigurera AzureDisks. |
| `Microsoft.Storage/storageAccounts/delete` <br/> `Microsoft.Storage/storageAccounts/listKeys/action` <br/> `Microsoft.Storage/storageAccounts/read` <br/> `Microsoft.Storage/storageAccounts/write` <br/> `Microsoft.Storage/operations/read` | Krävs för att konfigurera lagringskonton för AzureFile eller AzureDisk. |
| `Microsoft.Network/routeTables/read` <br/> `Microsoft.Network/routeTables/routes/delete` <br/> `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` <br/> `Microsoft.Network/routeTables/write` | Krävs för att konfigurera vägtabeller och vägar för noder. |
| `Microsoft.Compute/virtualMachines/read` | Krävs för att hitta information för virtuella datorer i en VMAS, till exempel zoner, feldomän, storlek och datadiskar. |
| `Microsoft.Compute/virtualMachines/write` | Krävs för att koppla AzureDisks till en virtuell dator i en VMAS. |
| `Microsoft.Compute/virtualMachineScaleSets/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/instanceView/read` | Krävs för att hitta information för virtuella datorer i en VM-skalningsuppsättning, till exempel zoner, feldomän, storlek och datadiskar. |
| `Microsoft.Network/networkInterfaces/write` | Krävs för att lägga till en virtuell dator i en VMAS till en serveradresspool för lastbalanserare. |
| `Microsoft.Compute/virtualMachineScaleSets/write` | Krävs för att lägga till en VM-skalningsuppsättning till en lastbalanserare för serveradresspooler och skala ut noder i en VM-skalningsuppsättning. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/write` | Krävs för att koppla AzureDisks och lägga till en virtuell dator från en VM-skalningsuppsättning till lastbalanseraren. |
| `Microsoft.Network/networkInterfaces/read` | Krävs för att söka efter interna IP-adresser och lastbalanseringsadresspooler för virtuella datorer i en VMAS. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read` | Krävs för att söka efter interna IP-adresser och lastbalanseringsadresspooler för en virtuell dator i en VM-skalningsuppsättning. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipconfigurations/publicipaddresses/read` | Krävs för att hitta offentliga IP-adresser för en virtuell dator i en VM-skalningsuppsättning. |
| `Microsoft.Network/virtualNetworks/read` <br/> `Microsoft.Network/virtualNetworks/subnets/read` | Krävs för att kontrollera om det finns ett undernät för den interna lastbalanseraren i en annan resursgrupp. |
| `Microsoft.Compute/snapshots/delete` <br/> `Microsoft.Compute/snapshots/read` <br/> `Microsoft.Compute/snapshots/write` | Krävs för att konfigurera ögonblicksbilder för AzureDisk. |
| `Microsoft.Compute/locations/vmSizes/read` <br/> `Microsoft.Compute/locations/operations/read` | Krävs för att hitta storlekar på virtuella datorer för att hitta AzureDisk-volymbegränsningar. |

### <a name="additional-cluster-identity-permissions"></a>Ytterligare klusteridentitetsbehörigheter

När du skapar ett kluster med specifika attribut behöver du följande ytterligare behörigheter för klusteridentiteten. Eftersom dessa behörigheter inte tilldelas automatiskt måste du lägga till dem i klusteridentiteten när den har skapats.

| Behörighet | Anledning |
|---|---|
| `Microsoft.Network/networkSecurityGroups/write` <br/> `Microsoft.Network/networkSecurityGroups/read` | Krävs om du använder en nätverkssäkerhetsgrupp i en annan resursgrupp. Krävs för att konfigurera säkerhetsregler för en LoadBalancer-tjänst. |
| `Microsoft.Network/virtualNetworks/subnets/read` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Krävs om du använder ett undernät i en annan resursgrupp, till exempel ett anpassat VNET. |
| `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` | Krävs om du använder ett undernät som är associerat med en vägtabell i en annan resursgrupp, till exempel ett anpassat VNET med en anpassad vägtabell. Krävs för att kontrollera om det redan finns ett undernät för undernätet i den andra resursgruppen. |
| `Microsoft.Network/virtualNetworks/subnets/read` | Krävs om du använder en intern lastbalanserare i en annan resursgrupp. Krävs för att kontrollera om det redan finns ett undernät för den interna lastbalanseraren i resursgruppen. |
| `Microsoft.Network/privatednszones/*` | Krävs om du använder en privat DNS-zon i en annan resursgrupp, till exempel en anpassad privateDNSZone. |

## <a name="kubernetes-rbac"></a>Kubernetes RBAC

Kubernetes RBAC ger detaljerad filtrering av användaråtgärder. Med den här kontrollmekanismen:
* Du tilldelar användare eller användargrupper behörighet att skapa och ändra resurser eller visa loggar från att köra programarbetsbelastningar. 
* Du kan begränsa behörigheterna till ett enda namnområde eller över hela AKS-klustret. 
* Du skapar *roller* för att definiera behörigheter och tilldelar sedan dessa roller till användare med *rollbindningar*.

Mer information finns i Använda [Kubernetes RBAC-auktorisering.][kubernetes-rbac]

### <a name="roles-and-clusterroles"></a>Roller och klusterroller

#### <a name="roles"></a>Roller
Innan du tilldelar behörigheter till användare med Kubernetes RBAC definierar du användarbehörigheter som en *roll.* Bevilja behörigheter inom ett namnområde med hjälp av roller. 

> [!NOTE]
> Kubernetes-roller *beviljar* behörigheter; De nekar *inte behörigheter.*

Om du vill bevilja behörigheter över hela klustret eller till klusterresurser utanför ett visst namnområde kan du i stället använda *ClusterRoles*.

#### <a name="clusterroles"></a>ClusterRoles

En ClusterRole beviljar och tillämpar behörigheter på resurser i hela klustret, inte ett specifikt namnområde.

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings och ClusterRoleBindings

När du har definierat roller för att bevilja behörigheter till resurser tilldelar du dessa Kubernetes RBAC-behörigheter med *en RoleBinding*. Om ditt AKS-kluster [integreras med Azure Active Directory (Azure AD)](#azure-ad-integration)beviljar roleBindings behörigheter till Azure AD-användare för att utföra åtgärder i klustret. Se hur du [i Kontrollera åtkomst till klusterresurser med kubernetes rollbaserad åtkomstkontroll och Azure Active Directory identiteter](azure-ad-rbac.md).

#### <a name="rolebindings"></a>RoleBindings

Tilldela roller till användare för ett visst namnområde med hjälp av RoleBindings. Med RoleBindings kan du logiskt åtse ett enda AKS-kluster, vilket endast gör det möjligt för användare att komma åt programresurserna i deras tilldelade namnområde. 

Om du vill binda roller över hela klustret eller till klusterresurser utanför ett visst namnområde använder du *i stället ClusterRoleBindings*.

#### <a name="clusterrolebinding"></a>ClusterRoleBinding

Med en ClusterRoleBinding binder du roller till användare och tillämpar på resurser i hela klustret, inte ett specifikt namnområde. Med den här metoden kan du ge administratörer eller supporttekniker åtkomst till alla resurser i AKS-klustret.


> [!NOTE]
> Microsoft/AKS utför alla klusteråtgärder med användarmedgivande under en inbyggd Kubernetes-roll och `aks-service` inbyggd rollbindning `aks-service-rolebinding` . 
> 
> Den här rollen gör det möjligt för AKS att felsöka och diagnostisera klusterproblem, men kan inte ändra behörigheter, skapa roller eller rollbindningar eller andra åtgärder med hög behörighet. Rollåtkomst aktiveras endast under aktiva supportärenden med JIT-åtkomst (just-in-time). Läs mer om [AKS-supportprinciper.](support-policies.md)


### <a name="kubernetes-service-accounts"></a>Kubernetes-tjänstkonton

*Tjänstkonton* är en av de primära användartyperna i Kubernetes. Kubernetes-API:et innehåller och hanterar tjänstkonton. Autentiseringsuppgifter för tjänstkontot lagras som Kubernetes-hemligheter, så att de kan användas av auktoriserade poddar för att kommunicera med API-servern. De flesta API-begäranden tillhandahåller en autentiseringstoken för ett tjänstkonto eller ett normalt användarkonto.

Vanliga användarkonton ger mer traditionell åtkomst för mänskliga administratörer eller utvecklare, inte bara tjänster och processer. Kubernetes tillhandahåller ingen identitetshanteringslösning för att lagra vanliga användarkonton och lösenord, men du kan integrera externa identitetslösningar i Kubernetes. För AKS-kluster är den här integrerade identitetslösningen Azure AD.

Mer information om identitetsalternativen i Kubernetes finns i [Kubernetes-autentisering.][kubernetes-authentication]

## <a name="azure-ad-integration"></a>Azure AD-integrering

Förbättra din AKS-klustersäkerhet med Azure AD-integrering. Azure AD bygger på årtionden av företagsidentitetshantering och är en molnbaserad katalog- och identitetshanteringstjänst för flera innehavare som kombinerar viktiga katalogtjänster, åtkomsthantering för program och identitetsskydd. Med Azure AD kan du integrera lokala identiteter i AKS-kluster för att tillhandahålla en enda källa för kontohantering och säkerhet.

![Azure Active Directory med AKS-kluster](media/concepts-identity/aad-integration.png)

Med Azure AD-integrerade AKS-kluster kan du ge användare eller grupper åtkomst till Kubernetes-resurser inom ett namnområde eller i klustret. 

1. För att få `kubectl` en konfigurationskontext kör en användare [kommandot az aks get-credentials.][az-aks-get-credentials] 
1. När en användare interagerar med AKS-klustret med uppmanas de att logga in med `kubectl` sina autentiseringsuppgifter för Azure AD. 

Den här metoden ger en enda källa för hantering av användarkonton och autentiseringsuppgifter för lösenord. Användaren kan bara komma åt de resurser som definierats av klusteradministratören.

Azure AD-autentisering tillhandahålls till AKS-kluster med OpenID Connect. OpenID Connect är ett identitetslager som bygger på OAuth 2.0-protokollet. Mer information om OpenID Connect finns i dokumentationen [för Open ID Connect.][openid-connect] Inifrån Kubernetes-klustret används [Webhook-tokenautentisering][webhook-token-docs] för att verifiera autentiseringstoken. Webhook-tokenautentisering konfigureras och hanteras som en del av AKS-klustret.

### <a name="webhook-and-api-server"></a>Webhook och API-server

![Webhook- och API-serverautentiseringsflöde](media/concepts-identity/auth-flow.png)

Som du ser i bilden ovan anropar API-servern AKS-webhookservern och utför följande steg:

1. `kubectl`använder Azure AD-klientprogrammet för att logga in användare med [beviljandeflödet för OAuth 2.0-enhetsauktorisering.](../active-directory/develop/v2-oauth2-device-code.md)
2. Azure AD tillhandahåller en access_token, id_token och en refresh_token.
3. Användaren gör en begäran till `kubectl` med en access_token från `kubeconfig` .
4. `kubectl` skickar access_token API Server.
5. API-servern konfigureras med Auth WebHook Server för att utföra verifieringen.
6. Webhook-autentiseringsservern bekräftar att JSON Web Token signatur är giltig genom att kontrollera den offentliga signeringsnyckeln för Azure AD.
7. Serverprogrammet använder användarautentiseringsuppgifter för att fråga gruppmedlemskap för den inloggade användaren från MS Graph API.
8. Ett svar skickas till API-servern med användarinformation som användarens huvudnamnsanspråk (UPN) för åtkomsttoken och användarens gruppmedlemskap baserat på objekt-ID:t.
9. API:et utför ett auktoriseringsbeslut baserat på Kubernetes Role/RoleBinding.
10. När auktoriserats returnerar API-servern ett svar till `kubectl` .
11. `kubectl` ger feedback till användaren.
 
Lär dig hur du integrerar AKS med Azure AD med vår [AKS-hanterade Azure AD-integreringsguide.](managed-aad.md)

## <a name="azure-role-based-access-control"></a>Rollbaserad Azure-åtkomstkontroll

Rollbaserad åtkomstkontroll i Azure (RBAC) är ett auktoriseringssystem som bygger [Azure Resource Manager](../azure-resource-manager/management/overview.md) som ger dig en mer fullständig åtkomsthantering av Azure-resurser.

| RBAC-system | Description |
|---|---|
| Kubernetes RBAC | Utformad för att fungera på Kubernetes-resurser i ditt AKS-kluster. |
| Azure RBAC | Utformad för att fungera med resurser i din Azure-prenumeration. |

Med Azure RBAC skapar du en *rolldefinition* som beskriver vilka behörigheter som ska tillämpas. Sedan tilldelar du en användare eller grupp den här rolldefinitionen via *en rolltilldelning* för ett visst *omfång*. Omfånget kan vara en enskild resurs, en resursgrupp eller i hela prenumerationen.

Mer information finns i [Vad är rollbaserad åtkomstkontroll i Azure (Azure RBAC)?][azure-rbac]

Det finns två åtkomstnivåer som krävs för att fullständigt driva ett AKS-kluster: 
* [Få åtkomst till AKS-resursen i din Azure-prenumeration.](#azure-rbac-to-authorize-access-to-the-aks-resource) 
  * Kontrollera skalning eller uppgradering av klustret med hjälp av AKS-API:erna.
  * Hämta `kubeconfig` .
* Åtkomst till Kubernetes-API:et. Den här åtkomsten styrs av antingen:
  * [Kubernetes RBAC](#kubernetes-rbac) (traditionellt).
  * [Integrera Azure RBAC med AKS för Kubernetes-auktorisering](#azure-rbac-for-kubernetes-authorization-preview).

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>Azure RBAC för att auktorisera åtkomst till AKS-resursen

Med Azure RBAC kan du ge dina användare (eller identiteter) detaljerad åtkomst till AKS-resurser i en eller flera prenumerationer. Du kan till exempel använda rollen [Azure Kubernetes Service för att](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role) skala och uppgradera klustret. Under tiden har en annan användare [med Azure Kubernetes Service-klusteradministratörsrollen](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) endast behörighet att hämta administratörsrollen `kubeconfig` .

Du kan också ge användaren den allmänna [deltagarrollen.](../role-based-access-control/built-in-roles.md#contributor) Med den allmänna deltagarrollen kan användarna utföra ovanstående behörigheter och alla åtgärder som är möjliga på AKS-resursen, förutom att hantera behörigheter.

[Använd Azure RBAC för att definiera åtkomst till Kubernetes-konfigurationsfilen i AKS](control-kubeconfig-access.md).

### <a name="azure-rbac-for-kubernetes-authorization-preview"></a>Azure RBAC för Kubernetes-auktorisering (förhandsversion)

Med Azure RBAC-integrering använder AKS en Kubernetes Authorization-webhookserver så att du kan hantera Azure AD-integrerade Resursbehörigheter och tilldelningar för Kubernetes-kluster med hjälp av Azure-rolldefinitioner och rolltilldelningar.

![Azure RBAC för Kubernetes-auktoriseringsflöde](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

Som du ser i diagrammet ovan följer alla begäranden till Kubernetes-API:et samma [Azure Active Directory autentiseringsflöde](#azure-ad-integration)när du använder Azure RBAC-integrering. 

Om identiteten som gör begäran finns i Azure AD teamindelar Azure Kubernetes RBAC för att auktorisera begäran. Om identiteten finns utanför Azure AD (d.v.s. ett Kubernetes-tjänstkonto) aviserar auktoriseringen den normala Kubernetes RBAC.

I det här scenariot använder du Azure RBAC-mekanismer och API:er för att tilldela användare inbyggda roller eller skapa anpassade roller, precis som med Kubernetes-roller. 

Med den här funktionen ger du inte bara användare behörigheter till AKS-resursen över prenumerationer, utan du konfigurerar även rollen och behörigheterna för i vart och ett av dessa kluster som styr Kubernetes API-åtkomst. Du kan till exempel bevilja rollen `Azure Kubernetes Service RBAC Viewer` i prenumerationsomfånget. Rollmottagaren kommer att kunna visa och hämta alla Kubernetes-objekt från alla kluster utan att ändra dem.

> [!IMPORTANT]
> Du måste aktivera Azure RBAC för Kubernetes-auktorisering innan du använder den här funktionen. Mer information och stegvisa anvisningar finns i guiden Använda [Azure RBAC för Kubernetes-auktorisering.](manage-azure-rbac.md)

#### <a name="built-in-roles"></a>Inbyggda roller

AKS tillhandahåller följande fyra inbyggda roller. De liknar de [inbyggda Kubernetes-rollerna med](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) några skillnader, till exempel stöd för CRD:er. Se den fullständiga listan över åtgärder som tillåts av varje [inbyggd Azure-roll.](../role-based-access-control/built-in-roles.md)

| Roll                                | Beskrivning  |
|-------------------------------------|--------------|
| Azure Kubernetes Service RBAC Viewer  | Tillåter skrivskyddade åtkomst för att se de flesta objekt i ett namnområde. <br> Tillåter inte visning av roller eller rollbindningar.<br> Tillåter inte visning av `Secrets` . Läsning av innehållet ger åtkomst till autentiseringsuppgifter i namnområdet, vilket skulle tillåta API-åtkomst som alla i namnområdet (en form av `Secrets` `ServiceAccount` `ServiceAccount` behörighetseskalering).  |
| Azure Kubernetes Service RBAC Writer | Tillåter läs-/skrivåtkomst till de flesta objekt i ett namnområde. <br> Tillåter inte visning eller ändring av roller eller rollbindningar. <br> Tillåter åtkomst till och körning av poddar som alla ServiceAccount i namnområdet, så den kan användas för att få API-åtkomstnivåer för alla `Secrets` ServiceAccount i namnområdet. |
| Azure Kubernetes Service RBAC-administratör  | Tillåter administratörsåtkomst som är avsedd att beviljas inom ett namnområde. <br> Tillåter läs-/skrivåtkomst till de flesta resurser i ett namnområde (eller klusteromfång), inklusive möjligheten att skapa roller och rollbindningar i namnområdet. <br> Tillåter inte skrivåtkomst till resurskvoten eller till själva namnområdet. |
| Azure Kubernetes Service RBAC-klusteradministratör  | Tillåter superanvändaråtkomst att utföra åtgärder på alla resurser. <br> Ger fullständig kontroll över alla resurser i klustret och i alla namnområden. |


## <a name="summary"></a>Sammanfattning

Visa tabellen för en snabb sammanfattning av hur användare kan autentisera till Kubernetes när Azure AD-integrering har aktiverats. I samtliga fall är användarens kommandosekvens:
1. Kör `az login` för att autentisera till Azure.
1. Kör `az aks get-credentials` för att ladda ned autentiseringsuppgifter för klustret till `.kube/config` .
1. Kör `kubectl` kommandon. 
   * Det första kommandot kan utlösa webbläsarbaserad autentisering för att autentisera till klustret, enligt beskrivningen i följande tabell.

I Azure Portal hittar du:
* Det *rollbe beviljande* (Azure RBAC-rollbe bevilja) som refereras till i den **andra kolumnen visas Access Control** fliken. 
* Klusteradministratören för Azure AD-gruppen visas på **fliken** Konfiguration.
  * Finns även med `--aad-admin-group-object-ids` parameternamn i Azure CLI.


| Description        | Rolls beviljande krävs| Klusteradministratör för Azure AD-grupper | När du ska använda detta |
| -------------------|------------|----------------------------|-------------|
| Äldre administratörsinloggning med klientcertifikat| **Administratörsroll för Azure Kubernetes.** Den här rollen kan användas med flaggan , som laddar ned ett äldre `az aks get-credentials` `--admin` klusteradministratörscertifikat [(inte Azure AD)](control-kubeconfig-access.md) till användarens `.kube/config` . Det här är det enda syftet med administratörsrollen för Azure Kubernetes.|saknas|Om du blockeras permanent genom att du inte har åtkomst till en giltig Azure AD-grupp med åtkomst till klustret.| 
| Azure AD med manuella (kluster)Rollbindningar| **Azure Kubernetes-användarroll .** Rollen "Användare" `az aks get-credentials` kan användas utan `--admin` flaggan . (Det här är det enda syftet med "Azure Kubernetes-användarroll".) Resultatet i ett Azure AD-aktiverat kluster är [](control-kubeconfig-access.md) nedladdningen av en tom post i , som utlöser webbläsarbaserad autentisering när den används `.kube/config` för första gången av `kubectl` .| Användaren finns inte i någon av dessa grupper. Eftersom användaren inte finns i klusteradministratörsgrupper styrs deras rättigheter helt av rollbindningar eller ClusterRoleBindings som har ställts in av klusteradministratörer. Rollbindningarna (klustret) [nominerar Azure AD-användare eller Azure AD-grupper](azure-ad-rbac.md) som sina `subjects` . Om inga sådana bindningar har ställts in kan användaren inte skriva ut några `kubectl` kommandon.|Om du vill ha mer information om åtkomstkontroll och inte använder Azure RBAC för Kubernetes-auktorisering. Observera att den användare som uppsättningar bindningarna måste logga in med någon av de andra metoderna som anges i den här tabellen.|
| Azure AD efter medlem i administratörsgruppen| Samma som ovan|Användaren är medlem i någon av de grupper som anges här. AKS genererar automatiskt en ClusterRoleBinding som binder alla grupper i listan till `cluster-admin` Kubernetes-rollen. Så användare i dessa grupper kan köra alla `kubectl` kommandon som `cluster-admin` .|Om du enkelt vill ge användarna fullständiga administratörsrättigheter och inte _använder_ Azure RBAC för Kubernetes-auktorisering.|
| Azure AD med Azure RBAC för Kubernetes-auktorisering|Två roller: <br> Först Azure **Kubernetes-användarrollen** (som ovan). <br> För det andra, en av "Azure Kubernetes Service **RBAC**..." roller som anges ovan eller ditt eget anpassade alternativ.|Fältet administratörsroller på fliken Konfiguration är irrelevant när Azure RBAC för Kubernetes-auktorisering är aktiverat.|Du använder Azure RBAC för Kubernetes-auktorisering. Den här metoden ger dig en mer fullständig kontroll utan att du behöver konfigurera RoleBindings eller ClusterRoleBindings.|

## <a name="next-steps"></a>Nästa steg

- Information om hur du kommer igång med Azure AD och Kubernetes RBAC finns [i Integrera Azure Active Directory med AKS][aks-aad].
- Associerade metodtips finns i [Metodtips för autentisering och auktorisering i AKS.][operator-best-practices-identity]
- Information om hur du kommer igång med Azure RBAC för Kubernetes-auktorisering finns i Använda Azure RBAC för att auktorisera åtkomst i [Azure Kubernetes Service-klustret (AKS).](manage-azure-rbac.md)
- Information om hur du kommer igång med `kubeconfig` att skydda filen finns i Begränsa [åtkomsten till klusterkonfigurationsfilen](control-kubeconfig-access.md)

Mer information om grundläggande Kubernetes- och AKS-begrepp finns i följande artiklar:

- [Kubernetes/AKS-kluster och arbetsbelastningar][aks-concepts-clusters-workloads]
- [Kubernetes-/AKS-säkerhet][aks-concepts-security]
- [Virtuella Kubernetes-/AKS-nätverk][aks-concepts-network]
- [Kubernetes/AKS-lagring][aks-concepts-storage]
- [Kubernetes/AKS-skala][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
[upgrade-per-cluster]: ../azure-monitor/containers/container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli
