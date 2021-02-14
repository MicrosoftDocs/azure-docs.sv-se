---
title: Koncept – åtkomst och identitet i Azure Kubernetes Services (AKS)
description: Lär dig mer om åtkomst och identitet i Azure Kubernetes service (AKS), inklusive Azure Active Directory-integrering, Kubernetes rollbaserad åtkomst kontroll (Kubernetes RBAC) och roller och bindningar.
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
author: palma21
ms.author: jpalma
ms.openlocfilehash: 8d69033dedc3a45263b087c9b9ee5b156af460be
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361068"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Åtkomst och identitetsalternativ för Azure Kubernetes Service (AKS)

Det finns olika sätt att autentisera, kontrol lera åtkomst/auktorisera och säkra Kubernetes-kluster. Med Kubernetes-rollbaserad åtkomst kontroll (Kubernetes RBAC) kan du ge användare, grupper och tjänst konton åtkomst till enbart de resurser de behöver. Med Azure Kubernetes service (AKS) kan du ytterligare förbättra strukturen för säkerhet och behörighet genom att använda Azure Active Directory och Azure RBAC. De här metoderna hjälper dig att skydda kluster åtkomsten och ger endast de behörigheter som krävs för utvecklare och operatörer.

Den här artikeln beskriver de viktigaste begreppen som hjälper dig att autentisera och tilldela behörigheter i AKS.

## <a name="aks-service-permissions"></a>AKS tjänst behörigheter

När du skapar ett kluster, skapar eller ändrar AKS de resurser som krävs för att skapa och köra klustret, till exempel virtuella datorer och nätverkskort, å användarens vägnar som skapar klustret. Den här identiteten är distinkt från klustrets identitets behörighet, som skapas när klustret skapas.

### <a name="identity-creating-and-operating-the-cluster-permissions"></a>Identitet som skapar och driver kluster behörigheter

Följande behörigheter krävs av identiteten för att skapa och driva klustret.

| Behörighet | Anledning |
|---|---|
| Microsoft. Compute/diskEncryptionSets/Read | Krävs för att läsa disk krypterings uppsättningens ID. |
| Microsoft. Compute/proximityPlacementGroups/Write | Krävs för att uppdatera närhets placerings grupper. |
| Microsoft. Network/applicationGateways/Read <br/> Microsoft. Network/applicationGateways/Write <br/> Microsoft.Network/virtualNetworks/subnets/join/action | Krävs för att konfigurera programgatewayer och delta i under nätet. |
| Microsoft.Network/virtualNetworks/subnets/join/action | Krävs för att konfigurera nätverks säkerhets gruppen för under nätet när du använder ett anpassat VNET.|
| Microsoft.Network/publicIPAddresses/join/action <br/> Microsoft. Network/publicIPPrefixes/JOIN/åtgärd | Krävs för att konfigurera de utgående offentliga IP-adresserna på Standard Load Balancer. |
| Microsoft. OperationalInsights/arbets ytor/sharedkeys/Read <br/> Microsoft. OperationalInsights/arbets ytor/läsa <br/> Microsoft. OperationsManagement/lösningar/Skriv <br/> Microsoft. OperationsManagement/Solutions/Read <br/> Microsoft. ManagedIdentity/userAssignedIdentities/Assign/åtgärd | Krävs för att skapa och uppdatera Log Analytics-arbetsytor och Azure-övervakning för behållare. |

### <a name="aks-cluster-identity-permissions"></a>AKS-kluster identitets behörigheter

Följande behörigheter används av AKS-kluster identiteten, som skapas och associeras med AKS-klustret när klustret skapas. Varje behörighet används av följande orsaker:

| Behörighet | Anledning |
|---|---|
| Microsoft. Network/belastningsutjämnare/Delete <br/> Microsoft. Network/belastningsutjämnare/Read <br/> Microsoft. Network/belastningsutjämnare/Write | Krävs för att konfigurera belastningsutjämnaren för en LoadBalancer-tjänst. |
| Microsoft. Network/publicIPAddresses/Delete <br/> Microsoft.Network/publicIPAddresses/read <br/> Microsoft.Network/publicIPAddresses/write | Krävs för att hitta och konfigurera offentliga IP-adresser för en LoadBalancer-tjänst. |
| Microsoft.Network/publicIPAddresses/join/action | Krävs för att konfigurera offentliga IP-adresser för en LoadBalancer-tjänst. |
| Microsoft. Network/networkSecurityGroups/Read <br/> Microsoft. Network/networkSecurityGroups/Write | Krävs för att skapa eller ta bort säkerhets regler för en LoadBalancer-tjänst. |
| Microsoft. Compute/disks/Delete <br/> Microsoft.Compute/disks/read <br/> Microsoft.Compute/disks/write <br/> Microsoft. Compute/locations/DiskOperations/Read | Krävs för att konfigurera AzureDisks. |
| Microsoft. Storage/storageAccounts/Delete <br/> Microsoft. Storage/storageAccounts/Listnycklar/åtgärd <br/> Microsoft. Storage/storageAccounts/Read <br/> Microsoft. Storage/storageAccounts/Write <br/> Microsoft. Storage/Operations/Read | Krävs för att konfigurera lagrings konton för AzureFile eller AzureDisk. |
| Microsoft. Network/routeTables/Read <br/> Microsoft. Network/routeTables/routes/Delete <br/> Microsoft. Network/routeTables/routes/Read <br/> Microsoft. Network/routeTables/routes/Write <br/> Microsoft. Network/routeTables/Write | Krävs för att konfigurera routningstabeller och vägar för noder. |
| Microsoft. Compute/virtualMachines/Read | Krävs för att hitta information om virtuella datorer i en VMAS, till exempel zoner, fel domän, storlek och data diskar. |
| Microsoft. Compute/virtualMachines/Write | Krävs för att bifoga AzureDisks till en virtuell dator i en VMAS. |
| Microsoft. Compute/virtualMachineScaleSets/Read <br/> Microsoft. Compute/virtualMachineScaleSets/virtualMachines/Read <br/> Microsoft. Compute/virtualMachineScaleSets/virtualmachines/instanceView/Read | Krävs för att hitta information om virtuella datorer i en skalnings uppsättning för virtuella datorer, till exempel zoner, fel domän, storlek och data diskar. |
| Microsoft. Network/networkInterfaces/Write | Krävs för att lägga till en virtuell dator i en VMAS till en backend-adresspool för belastnings utjämning. |
| Microsoft. Compute/virtualMachineScaleSets/Write | Krävs för att lägga till en skalnings uppsättning för en virtuell dator till en server dels adress för belastningsutjämnare och skala ut noder i en skalnings uppsättning för virtuella datorer. |
| Microsoft. Compute/virtualMachineScaleSets/virtualmachines/Write | Krävs för att bifoga AzureDisks och lägga till en virtuell dator från en skalnings uppsättning för virtuella datorer till belastningsutjämnaren. |
| Microsoft. Network/networkInterfaces/Read | Krävs för att söka efter interna IP-adresser och fjärrpooler för belastningsutjämnare för virtuella datorer i en VMAS. |
| Microsoft. Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/Read | Krävs för att söka efter interna IP-adresser och Server delar för belastningsutjämnare för en virtuell dator i en skalnings uppsättning för virtuella datorer. |
| Microsoft. Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipconfigurations/publicipaddresses/Read | Krävs för att hitta offentliga IP-adresser för en virtuell dator i en skalnings uppsättning för virtuella datorer. |
| Microsoft. Network/virtualNetworks/Read <br/> Microsoft.Network/virtualNetworks/subnets/read | Krävs för att kontrol lera om det finns ett undernät för den interna belastningsutjämnaren i en annan resurs grupp. |
| Microsoft. Compute/ögonblicks bilder/ta bort <br/> Microsoft. Compute/ögonblicks bilder/läsa <br/> Microsoft. Compute/ögonblicks bilder/skrivning | Krävs för att konfigurera ögonblicks bilder för AzureDisk. |
| Microsoft. Compute/locations/tillåtna storlekar/Read <br/> Microsoft. Compute/locations/Operations/Read | Krävs för att hitta storlekar på virtuella datorer för att hitta AzureDisk volym gränser. |

### <a name="additional-cluster-identity-permissions"></a>Ytterligare kluster identitets behörigheter

Följande ytterligare behörigheter krävs av kluster identiteten när du skapar ett kluster med vissa attribut. Dessa behörigheter tilldelas inte automatiskt, så du måste lägga till dessa behörigheter till kluster identiteten när de har skapats.

| Behörighet | Anledning |
|---|---|
| Microsoft. Network/networkSecurityGroups/Write <br/> Microsoft. Network/networkSecurityGroups/Read | Krävs om du använder en nätverks säkerhets grupp i en annan resurs grupp. Krävs för att konfigurera säkerhets regler för en LoadBalancer-tjänst. |
| Microsoft.Network/virtualNetworks/subnets/read <br/> Microsoft.Network/virtualNetworks/subnets/join/action | Krävs om du använder ett undernät i en annan resurs grupp, till exempel ett anpassat VNET. |
| Microsoft. Network/routeTables/routes/Read <br/> Microsoft. Network/routeTables/routes/Write | Krävs om du använder ett undernät som är associerat med en routningstabell i en annan resurs grupp, till exempel ett anpassat VNET med en anpassad väg tabell. Krävs för att kontrol lera om det redan finns ett undernät för under nätet i den andra resurs gruppen. |
| Microsoft.Network/virtualNetworks/subnets/read | Krävs om du använder en intern belastningsutjämnare i en annan resurs grupp. Krävs för att kontrol lera om det redan finns ett undernät för den interna belastnings utjämningen i resurs gruppen. |

## <a name="kubernetes-role-based-access-control-kubernetes-rbac"></a>Kubernetes-rollbaserad åtkomst kontroll (Kubernetes RBAC)

För att ge detaljerad filtrering av de åtgärder som användarna kan utföra använder Kubernetes Kubernetes-rollbaserad åtkomst kontroll (Kubernetes RBAC). Med den här kontrollen kan du tilldela användare eller grupper av användare behörighet att göra saker som att skapa eller ändra resurser, eller Visa loggar från att köra program arbets belastningar. Dessa behörigheter kan begränsas till ett enda namn område eller beviljas i hela AKS-klustret. Med Kubernetes RBAC skapar du *roller* för att definiera behörigheter och tilldelar sedan rollerna till användare med *roll bindningar*.

Mer information finns i [använda KUBERNETES RBAC-auktorisering][kubernetes-rbac].

### <a name="roles-and-clusterroles"></a>Roller och ClusterRoles

Innan du tilldelar behörigheter till användare med Kubernetes RBAC definierar du först dessa behörigheter som en *roll*. Kubernetes-roller *beviljar* behörigheter. Det finns inget begrepp för *neka* -behörighet.

Roller används för att bevilja behörigheter inom ett namn område. Om du behöver bevilja behörigheter över hela klustret, eller till kluster resurser utanför ett angivet namn område, kan du i stället använda *ClusterRoles*.

En ClusterRole fungerar på samma sätt för att bevilja behörigheter till resurser, men kan tillämpas på resurser i hela klustret, inte för en speciell namnrymd.

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings och ClusterRoleBindings

När rollerna har definierats för att ge behörigheter till resurser tilldelar du dessa Kubernetes RBAC-behörigheter till en *RoleBinding*. Om ditt AKS [-kluster integreras med Azure Active Directory (Azure AD)](#azure-active-directory-integration), är bindningar som Azure AD-användare beviljar behörighet att utföra åtgärder i klustret, se hur [du kontrollerar åtkomst till kluster resurser med hjälp av Kubernetes-rollbaserad åtkomst kontroll och Azure Active Directory identiteter](azure-ad-rbac.md).

Roll bindningar används för att tilldela roller för en specifik namnrymd. Med den här metoden kan du logiskt särskilja ett enda AKS-kluster, där användare bara kan komma åt program resurserna i sitt tilldelade namn område. Om du behöver binda roller över hela klustret, eller till kluster resurser utanför ett angivet namn område, kan du i stället använda *ClusterRoleBindings*.

En ClusterRoleBinding fungerar på samma sätt för att binda roller till användare, men kan tillämpas på resurser i hela klustret, inte för en speciell namnrymd. Med den här metoden kan du ge administratörer eller support tekniker åtkomst till alla resurser i AKS-klustret.


> [!NOTE]
> Alla kluster åtgärder som utförs av Microsoft/AKS görs med användar medgivande under en inbyggd Kubernetes-roll `aks-service` och inbyggd roll bindning `aks-service-rolebinding` . Med den här rollen kan AKS felsöka och diagnostisera kluster problem, men det går inte att ändra behörigheter eller skapa roller eller roll bindningar eller andra åtgärder med hög behörighet. Roll åtkomst är bara aktiverat under aktiva support biljetter med JIT-åtkomst (just-in-Time). Läs mer om [stöd principer för AKS](support-policies.md).


### <a name="kubernetes-service-accounts"></a>Kubernetes tjänst konton

En av de primära användar typerna i Kubernetes är ett *tjänst konto*. Ett tjänst konto finns i och hanteras av Kubernetes-API: et. Autentiseringsuppgifterna för tjänst konton lagras som Kubernetes hemligheter, vilket gör att de kan användas av auktoriserade poddar för att kommunicera med API-servern. De flesta API-begäranden tillhandahåller en autentiseringstoken för ett tjänst konto eller ett vanligt användar konto.

Vanliga användar konton ger mer traditionell åtkomst till personal eller utvecklare, inte bara tjänster och processer. Kubernetes tillhandahåller inte en identitets hanterings lösning där vanliga användar konton och lösen ord lagras. I stället kan externa identitets lösningar integreras i Kubernetes. Denna integrerade identitets lösning är Azure Active Directory för AKS-kluster.

Mer information om identitets alternativen i Kubernetes finns i Kubernetes- [autentisering][kubernetes-authentication].

## <a name="azure-active-directory-integration"></a>Azure Active Directory-integrering

Säkerheten för AKS-kluster kan förbättras med integrering av Azure Active Directory (AD). Azure AD bygger på årtionden av företags identitets hantering och är en moln-och identitets hanterings tjänst för flera innehavare som kombinerar kärn katalog tjänster, program åtkomst hantering och identitets skydd. Med Azure AD kan du integrera lokala identiteter i AKS-kluster för att tillhandahålla en enda källa för konto hantering och säkerhet.

![Azure Active Directory integrering med AKS-kluster](media/concepts-identity/aad-integration.png)

Med Azure AD-integrerade AKS-kluster kan du ge användare eller grupper åtkomst till Kubernetes-resurser inom ett namn område eller över klustret. För att hämta en `kubectl` konfigurations kontext kan en användare köra kommandot [AZ AKS get-credentials][az-aks-get-credentials] . När en användare interagerar med AKS-klustret med `kubectl` , uppmanas de att logga in med sina autentiseringsuppgifter för Azure AD. Den här metoden ger en enda källa för användar konto hantering och autentiseringsuppgifter för lösen ord. Användaren kan bara komma åt resurserna som definieras av kluster administratören.

Azure AD-autentisering tillhandahålls för AKS-kluster med OpenID Connect. OpenID Connect är ett identitets lager som byggts ovanpå OAuth 2,0-protokollet. Mer information om OpenID Connect finns i [Open ID Connect-dokumentationen][openid-connect]. Från inifrån Kubernetes-klustret används [webhook-token-autentisering][webhook-token-docs] för att verifiera autentiseringstoken. Webhook-token-autentisering konfigureras och hanteras som en del av AKS-klustret.

### <a name="webhook-and-api-server"></a>Webhook och API-Server

![Webhook-och API-serverautentisering](media/concepts-identity/auth-flow.png)

Som du ser i bilden ovan anropar API-servern AKS-webhook-servern och utför följande steg:

1. Azure AD-klientprogrammet används av kubectl för att logga in användare med [OAuth 2,0-enhetens Authorization-flöde](../active-directory/develop/v2-oauth2-device-code.md).
2. Azure AD tillhandahåller en access_token, id_token och en refresh_token.
3. Användaren gör en begäran till kubectl med en access_token från kubeconfig.
4. Kubectl skickar access_token till API-servern.
5. API-servern konfigureras med auth-webhook-servern för att utföra verifiering.
6. Autentiserings-webhook-servern bekräftar att JSON Web Token signaturen är giltig genom att kontrol lera Azure AD offentlig signerings nyckel.
7. Serverprogrammet använder användarspecifika autentiseringsuppgifter för att fråga grupp medlemskap för den inloggade användaren från MS-Graph API.
8. Ett svar skickas till API-servern med användar information som User Principal Name-anspråk (UPN) för åtkomsttoken och grupp medlemskapet för användaren baserat på objekt-ID: t.
9. API: et utför ett auktoriserings beslut baserat på Kubernetes-rollen/RoleBinding.
10. När den är auktoriserad returnerar API-servern ett svar till kubectl.
11. Kubectl ger användaren feedback.
 
**Lär dig hur du integrerar AKS med AAD [här](managed-aad.md).**

## <a name="azure-role-based-access-control-azure-rbac"></a>Azure RBAC (rollbaserad åtkomstkontroll)

Azure RBAC är ett auktoriserings system som bygger på [Azure Resource Manager](../azure-resource-manager/management/overview.md) som ger detaljerad åtkomst hantering av Azure-resurser.

 Azure RBAC är utformat för att fungera med resurser i din Azure-prenumeration medan Kubernetes RBAC är utformad för att fungera med Kubernetes-resurser i ditt AKS-kluster. 

Med Azure RBAC skapar du en *roll definition* som beskriver de behörigheter som ska tillämpas. En användare eller grupp tilldelas sedan den här roll definitionen via en *roll tilldelning* för ett visst *omfång*, som kan vara en enskild resurs, en resurs grupp eller över prenumerationen.

Mer information finns i [Vad är Azures rollbaserad åtkomst kontroll (Azure RBAC)?][azure-rbac]

Det finns två åtkomst nivåer som krävs för att fullständigt kunna använda ett AKS-kluster: 
1. [Få åtkomst till AKS-resursen i din Azure-prenumeration](#azure-rbac-to-authorize-access-to-the-aks-resource). Med den här processen kan du styra sakernas skalning eller uppgradera klustret med hjälp av AKS-API: er samt hämta din kubeconfig.
2. Åtkomst till Kubernetes-API: et. Den här åtkomsten styrs antingen av [KUBERNETES RBAC](#kubernetes-role-based-access-control-kubernetes-rbac) (traditionellt) eller genom att [integrera Azure RBAC med AKS för Kubernetes-auktorisering](#azure-rbac-for-kubernetes-authorization-preview)

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>Azure RBAC för att ge åtkomst till AKS-resursen

Med Azure RBAC kan du ange dina användare (eller identiteter) med detaljerad åtkomst till AKS-resurser för en eller flera prenumerationer. Du kan till exempel ha [rollen Azure Kubernetes service Contributor](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role) som gör det möjligt att utföra åtgärder som att skala och uppgradera klustret. En annan användare kan ha [rollen som administratör för Azure Kubernetes service Cluster](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) som bara ger behörighet att hämta admin-kubeconfig.

Alternativt kan du ge användaren rollen som allmän [deltagare](../role-based-access-control/built-in-roles.md#contributor) , som omfattar ovanstående behörigheter och alla åtgärder som är möjliga för AKS-resursen, med undantag för att hantera behörigheter.

Se mer information om hur du använder Azure RBAC för att skydda åtkomsten till kubeconfig-filen som ger åtkomst till Kubernetes-API: et [här](control-kubeconfig-access.md).

### <a name="azure-rbac-for-kubernetes-authorization-preview"></a>Azure RBAC för Kubernetes-auktorisering (för hands version)

Med Azure RBAC-integrering använder AKS en Kubernetes-auktoriserings-webhook-server så att du kan hantera behörigheter och tilldelningar av Azure AD-integrerade K8s kluster resurser med roll definitions-och roll tilldelningar i Azure.

![Azure RBAC för Kubernetes-auktoriseringsarkiv](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

Som du ser i diagrammet ovan, när du använder Azure RBAC-integrering, kommer alla begär anden till Kubernetes-API: et att följa samma autentiseringspaket som beskrivs i [avsnittet Azure Active Directory-integration](#azure-active-directory-integration). 

Men efter det, i stället för att enbart förlita sig på Kubernetes RBAC för auktorisering, kommer begäran faktiskt att godkännas av Azure, så länge identiteten som gjorde begäran finns i AAD. Om identiteten inte finns i AAD, till exempel ett Kubernetes-tjänstkonto, kommer inte Azure RBAC att lanseras och det är den normala Kubernetes RBAC.

I det här scenariot kan du ge användarna en av de fyra inbyggda rollerna eller skapa anpassade roller på samma sätt som du gör med Kubernetes-roller, men i det här fallet med Azure RBAC-mekanismer och API: er. 

Med den här funktionen kan du till exempel inte bara ge användare behörighet till AKS-resursen över prenumerationer, men konfigurera och ge dem rollen och behörighet som de kommer att ha i vart och ett av dessa kluster som styr åtkomsten till Kubernetes-API: et. Du kan till exempel bevilja `Azure Kubernetes Service RBAC Viewer` rollen i prenumerations omfånget och dess mottagare kommer att kunna visa och hämta alla Kubernetes-objekt från alla kluster, men inte ändra dem.

> [!IMPORTANT]
> Observera att du måste aktivera Azure RBAC för Kubernetes-auktorisering innan du använder den här funktionen. Mer information och stegvisa anvisningar [finns här](manage-azure-rbac.md).

#### <a name="built-in-roles"></a>Inbyggda roller

AKS tillhandahåller följande fyra inbyggda roller. De liknar de [inbyggda rollerna Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) , men med några skillnader som stöd för CRDs. En fullständig lista över åtgärder som tillåts av varje inbyggd roll finns [här](../role-based-access-control/built-in-roles.md).

| Roll                                | Beskrivning  |
|-------------------------------------|--------------|
| RBAC-visningsprogrammet i Azure Kubernetes service  | Tillåter skrivskyddad åtkomst för att se de flesta objekt i ett namn område. Den tillåter inte visning av roller eller roll bindningar. Den här rollen tillåter inte visning `Secrets` , eftersom läsning av innehållet i hemligheter ger åtkomst till `ServiceAccount` autentiseringsuppgifter i namn området, vilket skulle tillåta API-åtkomst `ServiceAccount` i namn området (en form av behörighets eskalering)  |
| RBAC-skrivare för Azure Kubernetes service | Tillåter Läs-/skriv åtkomst till de flesta objekt i ett namn område. Den här rollen tillåter inte visning eller ändring av roller eller roll bindningar. Den här rollen tillåter dock åtkomst `Secrets` och körning av poddar som alla ServiceAccount i namn området, så att den kan användas för att få åtkomst nivåer för API: er för alla ServiceAccount i namn området. |
| RBAC-administratör för Azure Kubernetes-tjänsten  | Tillåter administratörs åtkomst, som är avsedd att beviljas inom ett namn område. Tillåter Läs-/skriv åtkomst till de flesta resurser i ett namn område (eller ett kluster omfång), inklusive möjligheten att skapa roller och roll bindningar i namn området. Den här rollen tillåter inte skriv åtkomst till resurs kvot eller själva namn området. |
| Azure Kubernetes service RBAC-kluster administratör  | Ger åtkomst till superanvändare för att utföra alla åtgärder på en resurs. Den ger fullständig kontroll över alla resurser i klustret och i alla namn områden. |


## <a name="summary"></a>Sammanfattning

I den här tabellen sammanfattas hur användare kan autentiseras för Kubernetes när Azure AD-integrering är aktiverat.  I samtliga fall är användarens kommandosekvens:
1. Kör `az login` för att autentisera till Azure.
1. Kör `az aks get-credentials` för att ladda ned autentiseringsuppgifter för klustret till `.kube/config` .
1. Kör `kubectl` kommandon (den första som kan utlösa webbläsarbaserad autentisering för att autentisera till klustret, enligt beskrivningen i följande tabell).

Det roll anslag som refereras till i den andra kolumnen är Azure RBAC-rollen som visas på fliken **Access Control** i Azure Portal. Azure AD-gruppen kluster admin visas på fliken **konfiguration** i portalen (eller med parameter namnet `--aad-admin-group-object-ids` i Azure CLI).

| Beskrivning        | Roll beviljande krävs| Kluster administratör Azure AD-grupp (er) | När du ska använda detta |
| -------------------|------------|----------------------------|-------------|
| Äldre Administratörs inloggning med klient certifikat| **Administratörs roll för Azure-Kubernetes**. Den här rollen kan `az aks get-credentials` användas med `--admin` -flaggan, som laddar ned ett [äldre kluster administratörs certifikat (inte Azure AD)](control-kubeconfig-access.md) till användarens `.kube/config` . Detta är det enda syftet med administratörs rollen för Azure-Kubernetes.|saknas|Om du har blockerat permanent genom att inte ha åtkomst till en giltig Azure AD-grupp med åtkomst till klustret.| 
| Azure AD med manuellt (kluster) RoleBindings| **Användar rollen Azure-Kubernetes**. Rollen "användare" tillåter `az aks get-credentials` användning utan `--admin` flaggan. (Detta är det enda syftet med användar rollen Azure-Kubernetes.) Resultatet, i ett Azure AD-aktiverat kluster, är hämtningen av [en tom post](control-kubeconfig-access.md) till `.kube/config` , vilket utlöser webbläsarbaserad autentisering när den används första gången `kubectl` .| Användaren finns inte i någon av dessa grupper. Eftersom användaren inte finns i några kluster administratörs grupper, kommer deras rättigheter att kontrol leras helt av alla RoleBindings eller ClusterRoleBindings som har kon figurer ATS av kluster administratörer. RoleBindings (kluster) [nominerade Azure AD-användare eller Azure AD-grupper](azure-ad-rbac.md) som deras `subjects` . Om inga sådana bindningar har kon figurer ATS kommer användaren inte att kunna Excute några `kubectl` kommandon.|Om du vill ha detaljerad åtkomst kontroll och du inte använder Azure RBAC för Kubernetes-auktorisering. Observera att den användare som konfigurerar bindningarna måste logga in med någon av de andra metoderna som anges i den här tabellen.|
| Azure AD av medlem i administratörs grupp| Samma som ovan|Användaren är medlem i en av de grupper som anges här. AKS genererar automatiskt en ClusterRoleBinding som binder alla listade grupper till `cluster-admin` Kubernetes-rollen. Användare i dessa grupper kan köra alla `kubectl` kommandon som `cluster-admin` .|Om du enkelt vill ge användare fullständiga administratörs rättigheter och _inte_ använder Azure RBAC för Kubernetes-auktorisering.|
| Azure AD med Azure RBAC för Kubernetes-auktorisering|Två roller: först, **Azure Kubernetes användar roll** (som ovan). För det andra är en av "Azure Kubernetes-tjänsten **RBAC**..." roller som anges ovan eller ditt eget alternativ.|Fältet admin-roller på fliken konfiguration är irrelevant när Azure RBAC för Kubernetes-auktorisering har Aktiver ATS.|Du använder Azure RBAC för Kubernetes-auktorisering. Den här metoden ger dig detaljerad kontroll, utan att behöva ställa in RoleBindings eller ClusterRoleBindings.|

## <a name="next-steps"></a>Nästa steg

- För att komma igång med Azure AD och Kubernetes RBAC, se [integrera Azure Active Directory med AKS][aks-aad].
- För associerade metod tips, se [metod tips för autentisering och auktorisering i AKS][operator-best-practices-identity].
- Information om hur du kommer igång med Azure RBAC för Kubernetes-auktorisering finns i [använda Azure RBAC för att auktorisera åtkomst i Azure Kubernetes service-klustret (AKS)](manage-azure-rbac.md).
- Information om hur du kommer igång med att skydda din kubeconfig-fil finns i [begränsa åtkomsten till kluster konfigurations filen](control-kubeconfig-access.md)

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
[upgrade-per-cluster]: ../azure-monitor/insights/container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli
