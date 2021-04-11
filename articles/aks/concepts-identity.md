---
title: Koncept – åtkomst och identitet i Azure Kubernetes Services (AKS)
description: Lär dig mer om åtkomst och identitet i Azure Kubernetes service (AKS), inklusive Azure Active Directory-integrering, Kubernetes rollbaserad åtkomst kontroll (Kubernetes RBAC) och roller och bindningar.
services: container-service
ms.topic: conceptual
ms.date: 03/24/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: 76871565e0bb4ca1811d46531d07b89181d07e19
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105926"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Åtkomst och identitetsalternativ för Azure Kubernetes Service (AKS)

Du kan autentisera, auktorisera, skydda och kontrol lera åtkomsten till Kubernetes-kluster på flera olika sätt. 
* Med Kubernetes-rollbaserad åtkomst kontroll (Kubernetes RBAC) kan du ge användare, grupper och tjänst konton åtkomst till enbart de resurser de behöver. 
* Med Azure Kubernetes service (AKS) kan du ytterligare förbättra strukturen för säkerhet och behörigheter via Azure Active Directory och Azure RBAC. 

Kubernetes RBAC och AKS hjälper dig att skydda kluster åtkomsten och ger endast de behörigheter som krävs för utvecklare och operatörer.

Den här artikeln beskriver de viktigaste begreppen som hjälper dig att autentisera och tilldela behörigheter i AKS.

## <a name="aks-service-permissions"></a>AKS tjänst behörigheter

När du skapar ett kluster, genererar eller ändrar AKS de resurser som krävs (t. ex. virtuella datorer och nätverkskort) för att skapa och köra klustret åt användaren. Den här identiteten är distinkt från klustrets identitets behörighet, som skapas när klustret skapas.

### <a name="identity-creating-and-operating-the-cluster-permissions"></a>Identitet som skapar och driver kluster behörigheter

Följande behörigheter krävs av identiteten för att skapa och driva klustret.

| Behörighet | Anledning |
|---|---|
| `Microsoft.Compute/diskEncryptionSets/read` | Krävs för att läsa disk krypterings uppsättningens ID. |
| `Microsoft.Compute/proximityPlacementGroups/write` | Krävs för att uppdatera närhets placerings grupper. |
| `Microsoft.Network/applicationGateways/read` <br/> `Microsoft.Network/applicationGateways/write` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Krävs för att konfigurera programgatewayer och delta i under nätet. |
| `Microsoft.Network/virtualNetworks/subnets/join/action` | Krävs för att konfigurera nätverks säkerhets gruppen för under nätet när du använder ett anpassat VNET.|
| `Microsoft.Network/publicIPAddresses/join/action` <br/> `Microsoft.Network/publicIPPrefixes/join/action` | Krävs för att konfigurera de utgående offentliga IP-adresserna på Standard Load Balancer. |
| `Microsoft.OperationalInsights/workspaces/sharedkeys/read` <br/> `Microsoft.OperationalInsights/workspaces/read` <br/> `Microsoft.OperationsManagement/solutions/write` <br/> `Microsoft.OperationsManagement/solutions/read` <br/> `Microsoft.ManagedIdentity/userAssignedIdentities/assign/action` | Krävs för att skapa och uppdatera Log Analytics-arbetsytor och Azure-övervakning för behållare. |

### <a name="aks-cluster-identity-permissions"></a>AKS-kluster identitets behörigheter

Följande behörigheter används av AKS-kluster identiteten, som skapas och associeras med AKS-klustret. Varje behörighet används av följande orsaker:

| Behörighet | Anledning |
|---|---|
| `Microsoft.ContainerService/managedClusters/*`  <br/> | Krävs för att skapa användare och driva klustret
| `Microsoft.Network/loadBalancers/delete` <br/> `Microsoft.Network/loadBalancers/read` <br/> `Microsoft.Network/loadBalancers/write` | Krävs för att konfigurera belastningsutjämnaren för en LoadBalancer-tjänst. |
| `Microsoft.Network/publicIPAddresses/delete` <br/> `Microsoft.Network/publicIPAddresses/read` <br/> `Microsoft.Network/publicIPAddresses/write` | Krävs för att hitta och konfigurera offentliga IP-adresser för en LoadBalancer-tjänst. |
| `Microsoft.Network/publicIPAddresses/join/action` | Krävs för att konfigurera offentliga IP-adresser för en LoadBalancer-tjänst. |
| `Microsoft.Network/networkSecurityGroups/read` <br/> `Microsoft.Network/networkSecurityGroups/write` | Krävs för att skapa eller ta bort säkerhets regler för en LoadBalancer-tjänst. |
| `Microsoft.Compute/disks/delete` <br/> `Microsoft.Compute/disks/read` <br/> `Microsoft.Compute/disks/write` <br/> `Microsoft.Compute/locations/DiskOperations/read` | Krävs för att konfigurera AzureDisks. |
| `Microsoft.Storage/storageAccounts/delete` <br/> `Microsoft.Storage/storageAccounts/listKeys/action` <br/> `Microsoft.Storage/storageAccounts/read` <br/> `Microsoft.Storage/storageAccounts/write` <br/> `Microsoft.Storage/operations/read` | Krävs för att konfigurera lagrings konton för AzureFile eller AzureDisk. |
| `Microsoft.Network/routeTables/read` <br/> `Microsoft.Network/routeTables/routes/delete` <br/> `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` <br/> `Microsoft.Network/routeTables/write` | Krävs för att konfigurera routningstabeller och vägar för noder. |
| `Microsoft.Compute/virtualMachines/read` | Krävs för att hitta information om virtuella datorer i en VMAS, till exempel zoner, fel domän, storlek och data diskar. |
| `Microsoft.Compute/virtualMachines/write` | Krävs för att bifoga AzureDisks till en virtuell dator i en VMAS. |
| `Microsoft.Compute/virtualMachineScaleSets/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/instanceView/read` | Krävs för att hitta information om virtuella datorer i en skalnings uppsättning för virtuella datorer, till exempel zoner, fel domän, storlek och data diskar. |
| `Microsoft.Network/networkInterfaces/write` | Krävs för att lägga till en virtuell dator i en VMAS till en backend-adresspool för belastnings utjämning. |
| `Microsoft.Compute/virtualMachineScaleSets/write` | Krävs för att lägga till en skalnings uppsättning för en virtuell dator till en server dels adress för belastningsutjämnare och skala ut noder i en skalnings uppsättning för virtuella datorer. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/write` | Krävs för att bifoga AzureDisks och lägga till en virtuell dator från en skalnings uppsättning för virtuella datorer till belastningsutjämnaren. |
| `Microsoft.Network/networkInterfaces/read` | Krävs för att söka efter interna IP-adresser och fjärrpooler för belastningsutjämnare för virtuella datorer i en VMAS. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read` | Krävs för att söka efter interna IP-adresser och Server delar för belastningsutjämnare för en virtuell dator i en skalnings uppsättning för virtuella datorer. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipconfigurations/publicipaddresses/read` | Krävs för att hitta offentliga IP-adresser för en virtuell dator i en skalnings uppsättning för virtuella datorer. |
| `Microsoft.Network/virtualNetworks/read` <br/> `Microsoft.Network/virtualNetworks/subnets/read` | Krävs för att kontrol lera om det finns ett undernät för den interna belastningsutjämnaren i en annan resurs grupp. |
| `Microsoft.Compute/snapshots/delete` <br/> `Microsoft.Compute/snapshots/read` <br/> `Microsoft.Compute/snapshots/write` | Krävs för att konfigurera ögonblicks bilder för AzureDisk. |
| `Microsoft.Compute/locations/vmSizes/read` <br/> `Microsoft.Compute/locations/operations/read` | Krävs för att hitta storlekar på virtuella datorer för att hitta AzureDisk volym gränser. |

### <a name="additional-cluster-identity-permissions"></a>Ytterligare kluster identitets behörigheter

När du skapar ett kluster med vissa attribut behöver du följande ytterligare behörigheter för kluster identiteten. Eftersom dessa behörigheter inte tilldelas automatiskt, måste du lägga till dem i kluster identiteten när den har skapats.

| Behörighet | Anledning |
|---|---|
| `Microsoft.Network/networkSecurityGroups/write` <br/> `Microsoft.Network/networkSecurityGroups/read` | Krävs om du använder en nätverks säkerhets grupp i en annan resurs grupp. Krävs för att konfigurera säkerhets regler för en LoadBalancer-tjänst. |
| `Microsoft.Network/virtualNetworks/subnets/read` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Krävs om du använder ett undernät i en annan resurs grupp, till exempel ett anpassat VNET. |
| `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` | Krävs om du använder ett undernät som är associerat med en routningstabell i en annan resurs grupp, till exempel ett anpassat VNET med en anpassad väg tabell. Krävs för att kontrol lera om det redan finns ett undernät för under nätet i den andra resurs gruppen. |
| `Microsoft.Network/virtualNetworks/subnets/read` | Krävs om du använder en intern belastningsutjämnare i en annan resurs grupp. Krävs för att kontrol lera om det redan finns ett undernät för den interna belastnings utjämningen i resurs gruppen. |
| `Microsoft.Network/privatednszones/*` | Krävs om du använder en privat DNS-zon i en annan resurs grupp, till exempel en anpassad privateDNSZone. |

## <a name="kubernetes-rbac"></a>Kubernetes RBAC

Kubernetes RBAC ger detaljerad filtrering av användar åtgärder. Med den här kontrollen:
* Du tilldelar användare eller användar grupper behörighet att skapa och ändra resurser eller Visa loggar från att köra program arbets belastningar. 
* Du kan begränsa behörigheter till ett enda namn område eller över hela AKS-klustret. 
* Du skapar *roller* för att definiera behörigheter och tilldelar sedan dessa roller till användare med *roll bindningar*.

Mer information finns i [använda KUBERNETES RBAC-auktorisering][kubernetes-rbac].

### <a name="roles-and-clusterroles"></a>Roller och ClusterRoles

#### <a name="roles"></a>Roller
Innan du tilldelar behörigheter till användare med Kubernetes RBAC definierar du användar behörigheter som en *roll*. Bevilja behörigheter inom ett namn område med hjälp av roller. 

> [!NOTE]
> Kubernetes roller *beviljar* behörigheter; de *nekar* inte behörigheter.

Om du vill bevilja behörigheter över hela klustret eller till kluster resurser utanför ett angivet namn område, kan du i stället använda *ClusterRoles*.

#### <a name="clusterroles"></a>ClusterRoles

En ClusterRole beviljar och tillämpar behörigheter för resurser över hela klustret, inte ett särskilt namn område.

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings och ClusterRoleBindings

När du har definierat roller för att bevilja behörigheter till resurser tilldelar du dessa Kubernetes RBAC-behörigheter med en *RoleBinding*. Om ditt AKS [-kluster integreras med Azure Active Directory (Azure AD)](#azure-ad-integration)ger RoleBindings behörighet till Azure AD-användare för att utföra åtgärder i klustret. Se hur [du styr åtkomsten till kluster resurser med Kubernetes-rollbaserad åtkomst kontroll och Azure Active Directory identiteter](azure-ad-rbac.md).

#### <a name="rolebindings"></a>RoleBindings

Tilldela roller till användare för ett angivet namn område med hjälp av RoleBindings. Med RoleBindings kan du logiskt särskilja ett enda AKS-kluster, så att endast användare får åtkomst till program resurserna i sitt tilldelade namn område. 

Om du vill binda roller över hela klustret, eller till kluster resurser utanför ett angivet namn område, använder du i stället *ClusterRoleBindings*.

#### <a name="clusterrolebinding"></a>ClusterRoleBinding

Med en ClusterRoleBinding kan du binda roller till användare och tillämpa dem på resurser i hela klustret, inte ett särskilt namn område. Med den här metoden kan du ge administratörer eller support tekniker åtkomst till alla resurser i AKS-klustret.


> [!NOTE]
> Microsoft/AKS utför alla kluster åtgärder med användar medgivande under en inbyggd Kubernetes-roll `aks-service` och inbyggd roll bindning `aks-service-rolebinding` . 
> 
> Med den här rollen kan AKS felsöka och diagnostisera kluster problem, men det går inte att ändra behörigheter eller skapa roller eller roll bindningar eller andra åtgärder med hög behörighet. Roll åtkomst är bara aktiverat under aktiva support biljetter med JIT-åtkomst (just-in-Time). Läs mer om [stöd principer för AKS](support-policies.md).


### <a name="kubernetes-service-accounts"></a>Kubernetes tjänst konton

*Tjänst konton* är en av de primära användar typerna i Kubernetes. Kubernetes-API: et innehåller och hanterar tjänst konton. Autentiseringsuppgifterna för tjänst kontot lagras som Kubernetes hemligheter, så att de kan användas av auktoriserade poddar för att kommunicera med API-servern. De flesta API-begäranden tillhandahåller en autentiseringstoken för ett tjänst konto eller ett vanligt användar konto.

Vanliga användar konton ger mer traditionell åtkomst till personal eller utvecklare, inte bara tjänster och processer. Även om Kubernetes inte tillhandahåller en identitets hanterings lösning för att lagra vanliga användar konton och lösen ord, kan du integrera externa identitets lösningar i Kubernetes. Denna integrerade identitets lösning är Azure AD för AKS-kluster.

Mer information om identitets alternativen i Kubernetes finns i Kubernetes- [autentisering][kubernetes-authentication].

## <a name="azure-ad-integration"></a>Azure AD-integrering

Förbättra säkerheten för AKS-klustret med Azure AD-integrering. Azure AD bygger på årtionden av företags identitets hantering och är en molnbaserad katalog-och identitets hanterings tjänst för flera innehavare som kombinerar kärn katalog tjänster, program åtkomst hantering och identitets skydd. Med Azure AD kan du integrera lokala identiteter i AKS-kluster för att tillhandahålla en enda källa för konto hantering och säkerhet.

![Azure Active Directory integrering med AKS-kluster](media/concepts-identity/aad-integration.png)

Med Azure AD-integrerade AKS-kluster kan du ge användare eller grupper åtkomst till Kubernetes-resurser inom ett namn område eller över klustret. 

1. För att hämta en `kubectl` konfigurations kontext kör en användare kommandot [AZ AKS get-credentials][az-aks-get-credentials] . 
1. När en användare interagerar med AKS-klustret med `kubectl` , uppmanas de att logga in med sina autentiseringsuppgifter för Azure AD. 

Den här metoden ger en enda källa för användar konto hantering och autentiseringsuppgifter för lösen ord. Användaren kan bara komma åt resurserna som definieras av kluster administratören.

Azure AD-autentisering tillhandahålls för AKS-kluster med OpenID Connect. OpenID Connect är ett identitets lager som byggts ovanpå OAuth 2,0-protokollet. Mer information om OpenID Connect finns i [Open ID Connect-dokumentationen][openid-connect]. Från inifrån Kubernetes-klustret används [webhook-token-autentisering][webhook-token-docs] för att verifiera autentiseringstoken. Webhook-token-autentisering konfigureras och hanteras som en del av AKS-klustret.

### <a name="webhook-and-api-server"></a>Webhook och API-Server

![Webhook-och API-serverautentisering](media/concepts-identity/auth-flow.png)

Som du ser i bilden ovan anropar API-servern AKS-webhook-servern och utför följande steg:

1. `kubectl` använder Azure AD-klientprogrammet för att logga in användare med [OAuth 2,0-enhetens Authorization Grant-flöde](../active-directory/develop/v2-oauth2-device-code.md).
2. Azure AD tillhandahåller en access_token, id_token och en refresh_token.
3. Användaren gör en begäran till `kubectl` med en access_token från `kubeconfig` .
4. `kubectl` skickar access_token till API-servern.
5. API-servern konfigureras med auth-webhook-servern för att utföra verifiering.
6. Autentiserings-webhook-servern bekräftar att JSON Web Token signaturen är giltig genom att kontrol lera Azure AD offentlig signerings nyckel.
7. Serverprogrammet använder användarspecifika autentiseringsuppgifter för att fråga grupp medlemskap för den inloggade användaren från MS-Graph API.
8. Ett svar skickas till API-servern med användar information som User Principal Name-anspråk (UPN) för åtkomsttoken och grupp medlemskapet för användaren baserat på objekt-ID: t.
9. API: et utför ett auktoriserings beslut baserat på Kubernetes-rollen/RoleBinding.
10. När den är auktoriserad returnerar API-servern ett svar till `kubectl` .
11. `kubectl` ger feedback till användaren.
 
Lär dig att integrera AKS med Azure AD med vår [AKS-hanterade guide för Azure AD-integrering](managed-aad.md).

## <a name="azure-role-based-access-control"></a>Rollbaserad Azure-åtkomstkontroll

Rollbaserad åtkomst kontroll (RBAC) i Azure är ett auktoriserings system som bygger på [Azure Resource Manager](../azure-resource-manager/management/overview.md) som ger detaljerad åtkomst hantering av Azure-resurser.

| RBAC-system | Beskrivning |
|---|---|
| Kubernetes RBAC | Utformad för att fungera med Kubernetes-resurser i ditt AKS-kluster. |
| Azure RBAC | Utformad för att fungera med resurser i din Azure-prenumeration. |

Med Azure RBAC skapar du en *roll definition* som beskriver de behörigheter som ska tillämpas. Sedan tilldelar du en användare eller grupp den här roll definitionen via en *roll tilldelning* för ett visst *omfång*. Omfattningen kan vara en enskild resurs, en resurs grupp eller över prenumerationen.

Mer information finns i [Vad är Azures rollbaserad åtkomst kontroll (Azure RBAC)?][azure-rbac]

Det finns två åtkomst nivåer som krävs för att fullständigt kunna använda ett AKS-kluster: 
* [Få åtkomst till AKS-resursen i din Azure-prenumeration](#azure-rbac-to-authorize-access-to-the-aks-resource). 
  * Kontroll av skalning eller uppgradering av klustret med hjälp av AKS-API: er.
  * Hämta din `kubeconfig` .
* Åtkomst till Kubernetes-API: et. Den här åtkomsten styrs av antingen:
  * [KUBERNETES RBAC](#kubernetes-rbac) (traditionellt).
  * [Integrera Azure RBAC med AKS för Kubernetes-auktorisering](#azure-rbac-for-kubernetes-authorization-preview).

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>Azure RBAC för att ge åtkomst till AKS-resursen

Med Azure RBAC kan du ange dina användare (eller identiteter) med detaljerad åtkomst till AKS-resurser för en eller flera prenumerationer. Du kan till exempel använda [rollen Azure Kubernetes service Contributor](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role) för att skala och uppgradera klustret. Under tiden har en annan användare med [rollen Azure Kubernetes service Cluster admin](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) bara behörighet att hämta administratören `kubeconfig` .

Alternativt kan du ge användaren rollen som allmän [deltagare](../role-based-access-control/built-in-roles.md#contributor) . Med rollen allmän deltagare kan användare utföra ovanstående behörigheter och alla åtgärder som är möjliga på AKS-resursen, förutom att hantera behörigheter.

[Använd Azure RBAC för att definiera åtkomst till konfigurations filen Kubernetes i AKS](control-kubeconfig-access.md).

### <a name="azure-rbac-for-kubernetes-authorization-preview"></a>Azure RBAC för Kubernetes-auktorisering (för hands version)

Med Azure RBAC-integrering använder AKS en Kubernetes-auktoriserings-webhook-server så att du kan hantera Azure AD-integrerade Kubernetes kluster resurs behörigheter och tilldelningar med hjälp av roll definition och roll tilldelningar i Azure.

![Azure RBAC för Kubernetes-auktoriseringsarkiv](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

Som du ser i ovanstående diagram när du använder Azure RBAC-integrering, kommer alla begär anden till Kubernetes-API: et att följa samma autentiseringsschema som beskrivs i [avsnittet Azure Active Directory-integration](#azure-ad-integration). 

Om identiteten som gör begäran finns i Azure AD, kommer Azure att samar Kubernetes RBAC för att auktorisera begäran. Om identiteten finns utanför Azure AD (t. ex. ett Kubernetes-tjänstkonto), kommer auktoriseringen att förhindra den normala Kubernetes RBAC.

I det här scenariot använder du Azure RBAC-mekanismer och API: er för att tilldela användare inbyggda roller eller skapa anpassade roller, precis som du skulle göra med Kubernetes-roller. 

Med den här funktionen ger du inte bara användare behörighet till AKS-resursen över prenumerationer, men du kan även konfigurera rollen och behörigheterna i vart och ett av dessa kluster som kontrollerar Kubernetes API-åtkomst. Du kan till exempel bevilja `Azure Kubernetes Service RBAC Viewer` rollen i prenumerations omfånget. Roll mottagaren kommer att kunna visa och hämta alla Kubernetes-objekt från alla kluster utan att ändra dem.

> [!IMPORTANT]
> Du måste aktivera Azure RBAC för Kubernetes-auktorisering innan du använder den här funktionen. Mer information och stegvisa anvisningar finns i [använda Azure RBAC för Kubernetes Authorization](manage-azure-rbac.md) instruktions guide.

#### <a name="built-in-roles"></a>Inbyggda roller

AKS tillhandahåller följande fyra inbyggda roller. De liknar de [inbyggda rollerna Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) med några få skillnader, t. ex. stöd för CRDs. Se den fullständiga listan över åtgärder som tillåts av varje [inbyggd Azure-roll](../role-based-access-control/built-in-roles.md).

| Roll                                | Beskrivning  |
|-------------------------------------|--------------|
| RBAC-visningsprogrammet i Azure Kubernetes service  | Tillåter skrivskyddad åtkomst för att se de flesta objekt i ett namn område. <br> Tillåter inte visning av roller eller roll bindningar.<br> Tillåter inte visning `Secrets` . Om du läser `Secrets` innehållet aktive RAS åtkomst till `ServiceAccount` autentiseringsuppgifter i namn området, vilket möjliggör API-åtkomst `ServiceAccount` i namn området (en form av behörighets eskalering).  |
| RBAC-skrivare för Azure Kubernetes service | Tillåter Läs-/skriv åtkomst till de flesta objekt i ett namn område. <br> Tillåter inte att du visar eller ändrar roller eller roll bindningar. <br> Tillåter åtkomst till `Secrets` och kör poddar som en ServiceAccount i namn området, så att den kan användas för att få åtkomst nivåer för API: er för alla ServiceAccount i namn området. |
| RBAC-administratör för Azure Kubernetes-tjänsten  | Tillåter administratörs åtkomst, som är avsedd att beviljas inom ett namn område. <br> Tillåter Läs-/skriv åtkomst till de flesta resurser i ett namn område (eller ett kluster omfång), inklusive möjligheten att skapa roller och roll bindningar i namn området. <br> Tillåter inte skriv åtkomst till resurs kvot eller själva namn området. |
| Azure Kubernetes service RBAC-kluster administratör  | Ger åtkomst till superanvändare för att utföra alla åtgärder på en resurs. <br> Ger fullständig kontroll över alla resurser i klustret och i alla namn områden. |


## <a name="summary"></a>Sammanfattning

Visa tabellen för en snabb översikt över hur användare kan autentiseras för Kubernetes när Azure AD-integrering är aktiverat. I samtliga fall är användarens kommandosekvens:
1. Kör `az login` för att autentisera till Azure.
1. Kör `az aks get-credentials` för att ladda ned autentiseringsuppgifter för klustret till `.kube/config` .
1. Kör `kubectl` kommandon. 
   * Det första kommandot kan utlösa webbläsarbaserad autentisering för att autentisera till klustret, enligt beskrivningen i följande tabell.

I Azure Portal kan du hitta:
* *Roll tilldelningen* (Azure RBAC-rollens beviljande) som refereras till i den andra kolumnen visas på fliken **Access Control** . 
* Azure AD-gruppen kluster admin visas på fliken **konfiguration** .
  * Finns också med parameter namn `--aad-admin-group-object-ids` i Azure CLI.


| Beskrivning        | Roll beviljande krävs| Kluster administratör Azure AD-grupp (er) | När du ska använda detta |
| -------------------|------------|----------------------------|-------------|
| Äldre Administratörs inloggning med klient certifikat| **Administratörs roll för Azure-Kubernetes**. Den här rollen kan `az aks get-credentials` användas med `--admin` -flaggan, som laddar ned ett [äldre kluster administratörs certifikat (inte Azure AD)](control-kubeconfig-access.md) till användarens `.kube/config` . Detta är det enda syftet med administratörs rollen för Azure-Kubernetes.|saknas|Om du har blockerat permanent genom att inte ha åtkomst till en giltig Azure AD-grupp med åtkomst till klustret.| 
| Azure AD med manuellt (kluster) RoleBindings| **Användar rollen Azure-Kubernetes**. Rollen "användare" tillåter `az aks get-credentials` användning utan `--admin` flaggan. (Detta är det enda syftet med användar rollen Azure-Kubernetes.) Resultatet, i ett Azure AD-aktiverat kluster, är hämtningen av [en tom post](control-kubeconfig-access.md) till `.kube/config` , vilket utlöser webbläsarbaserad autentisering när den används första gången `kubectl` .| Användaren finns inte i någon av dessa grupper. Eftersom användaren inte finns i några kluster administratörs grupper, kommer deras rättigheter att kontrol leras helt av alla RoleBindings eller ClusterRoleBindings som har kon figurer ATS av kluster administratörer. RoleBindings (kluster) [nominerade Azure AD-användare eller Azure AD-grupper](azure-ad-rbac.md) som deras `subjects` . Om inga sådana bindningar har kon figurer ATS kommer användaren inte att kunna Excute några `kubectl` kommandon.|Om du vill ha detaljerad åtkomst kontroll och du inte använder Azure RBAC för Kubernetes-auktorisering. Observera att den användare som konfigurerar bindningarna måste logga in med någon av de andra metoderna som anges i den här tabellen.|
| Azure AD av medlem i administratörs grupp| Samma som ovan|Användaren är medlem i en av de grupper som anges här. AKS genererar automatiskt en ClusterRoleBinding som binder alla listade grupper till `cluster-admin` Kubernetes-rollen. Användare i dessa grupper kan köra alla `kubectl` kommandon som `cluster-admin` .|Om du enkelt vill ge användare fullständiga administratörs rättigheter och _inte_ använder Azure RBAC för Kubernetes-auktorisering.|
| Azure AD med Azure RBAC för Kubernetes-auktorisering|Två roller: <br> För det första, **användar rollen Azure-Kubernetes** (som ovan). <br> För det andra är en av "Azure Kubernetes-tjänsten **RBAC**..." roller som anges ovan eller ditt eget alternativ.|Fältet admin-roller på fliken konfiguration är irrelevant när Azure RBAC för Kubernetes-auktorisering har Aktiver ATS.|Du använder Azure RBAC för Kubernetes-auktorisering. Den här metoden ger dig detaljerad kontroll, utan att behöva ställa in RoleBindings eller ClusterRoleBindings.|

## <a name="next-steps"></a>Nästa steg

- För att komma igång med Azure AD och Kubernetes RBAC, se [integrera Azure Active Directory med AKS][aks-aad].
- För associerade metod tips, se [metod tips för autentisering och auktorisering i AKS][operator-best-practices-identity].
- Information om hur du kommer igång med Azure RBAC för Kubernetes-auktorisering finns i [använda Azure RBAC för att auktorisera åtkomst i Azure Kubernetes service-klustret (AKS)](manage-azure-rbac.md).
- För att komma igång med att skydda `kubeconfig` filen, se [begränsa åtkomsten till kluster konfigurations filen](control-kubeconfig-access.md)

Mer information om kärn Kubernetes-och AKS-koncept finns i följande artiklar:

- [Kubernetes/AKS-kluster och arbets belastningar][aks-concepts-clusters-workloads]
- [Kubernetes/AKS-säkerhet][aks-concepts-security]
- [Kubernetes/AKS virtuella nätverk][aks-concepts-network]
- [Kubernetes/AKS-lagring][aks-concepts-storage]
- [Kubernetes/AKS-skala][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
[upgrade-per-cluster]: ../azure-monitor/containers/container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli
