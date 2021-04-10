---
title: Använda Azure AD i Azure Kubernetes-tjänsten
description: Lär dig hur du använder Azure AD i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 02/1/2021
ms.author: miwithro
ms.openlocfilehash: b7918ecc31fe152bd25153ac8c899ce3ff8fdacb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640604"
---
# <a name="aks-managed-azure-active-directory-integration"></a>AKS-hanterad Azure Active Directory-integrering

AKS-hanterad Azure AD-integrering är utformad för att förenkla Azure AD-integration, där användare tidigare behövde skapa en klient app, en server app och krävde att Azure AD-klienten ska bevilja Läs behörighet för katalogen. I den nya versionen hanterar AKS Resource Provider klient-och Server apparna åt dig.

## <a name="azure-ad-authentication-overview"></a>Översikt över Azure AD-autentisering

Kluster administratörer kan konfigurera Kubernetes-rollbaserad åtkomst kontroll (Kubernetes RBAC) baserat på användarens identitet eller katalog grupp medlemskap. Azure AD-autentisering tillhandahålls för AKS-kluster med OpenID Connect. OpenID Connect är ett identitets lager som byggts ovanpå OAuth 2,0-protokollet. Mer information om OpenID Connect finns i [Open ID Connect-dokumentationen][open-id-connect].

Läs mer om Azure AD-integrerings flödet i [dokumentationen för Azure Active Directory integrations begrepp](concepts-identity.md#azure-active-directory-integration).

## <a name="limitations"></a>Begränsningar 

* AKS-hanterad Azure AD-integrering kan inte inaktive ras
* Det finns inte stöd för att ändra ett AKS-hanterat Azure AD-integrerat kluster till äldre AAD
* icke-Kubernetes RBAC-aktiverade kluster stöds inte för AKS-hanterad Azure AD-integrering
* Det finns inte stöd för att ändra Azure AD-klienten som är associerad med AKS-hanterad Azure AD-integrering

## <a name="prerequisites"></a>Förutsättningar

* Azure CLI-version 2.11.0 eller senare
* Kubectl med en lägsta version av [1.18.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) eller [kubelogin](https://github.com/Azure/kubelogin)
* Om du använder [Helm](https://github.com/helm/helm), den lägsta versionen av Helm 3,3.

> [!Important]
> Du måste använda Kubectl med en lägsta version av 1.18.1 eller kubelogin. Skillnaden mellan lägre versioner av Kubernetes och kubectl får inte vara mer än 1 version. Om du inte använder rätt version kommer du att märka problem med autentiseringen.

Använd följande kommandon för att installera kubectl och kubelogin:

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
kubelogin --version
```

Använd [de här anvisningarna](https://kubernetes.io/docs/tasks/tools/install-kubectl/) för andra operativ system.

## <a name="before-you-begin"></a>Innan du börjar

För ditt kluster behöver du en Azure AD-grupp. Den här gruppen krävs som administratörs grupp för klustret för att ge kluster administratörs behörighet. Du kan använda en befintlig Azure AD-grupp eller skapa en ny. Registrera objekt-ID för din Azure AD-grupp.

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

Om du vill skapa en ny Azure AD-grupp för kluster administratörerna använder du följande kommando:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Skapa ett AKS-kluster med Azure AD aktiverat

Skapa ett AKS-kluster med hjälp av följande CLI-kommandon.

Skapa en Azure-resurs grupp:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Skapa ett AKS-kluster och aktivera administrations åtkomst för din Azure AD-grupp

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

En lyckad skapande av ett AKS Azure AD-kluster har följande avsnitt i svars texten
```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

När klustret har skapats kan du börja komma åt det.

## <a name="access-an-azure-ad-enabled-cluster"></a>Åtkomst till ett Azure AD-aktiverat kluster

Du behöver den inbyggda rollen [Azure Kubernetes service Cluster-användare](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) för att utföra följande steg.

Hämta användarautentiseringsuppgifter för att få åtkomst till klustret:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
Följ anvisningarna för att logga in.

Använd kommandot kubectl get Nodes för att Visa noder i klustret:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
Konfigurera [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](./azure-ad-rbac.md) för att konfigurera ytterligare säkerhets grupper för dina kluster.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Fel sökning av åtkomst problem med Azure AD

> [!Important]
> Stegen som beskrivs nedan kringgår normal Azure AD-gruppautentisering. Använd dem endast i nödfall.

Om du är permanent blockerad genom att inte ha åtkomst till en giltig Azure AD-grupp med åtkomst till klustret kan du fortfarande få administratörs behörighet för att få åtkomst till klustret direkt.

För att utföra de här stegen måste du ha till gång till den inbyggda rollen [Azure Kubernetes service Cluster admin](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) .

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="enable-aks-managed-azure-ad-integration-on-your-existing-cluster"></a>Aktivera AKS-hanterad Azure AD-integrering på ditt befintliga kluster

Du kan aktivera AKS-hanterad Azure AD-integrering på ditt befintliga Kubernetes RBAC-aktiverade kluster. Se till att du anger administratörs gruppen för att behålla åtkomsten till klustret.

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad --aad-admin-group-object-ids <id-1> [--aad-tenant-id <id>]
```

En lyckad aktivering av ett AKS-hanterat Azure AD-kluster har följande avsnitt i svars texten

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Hämta autentiseringsuppgifter för användare igen för att få åtkomst till klustret genom att följa stegen [här][access-cluster].

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>Uppgradera till AKS-hanterad Azure AD-integrering

Om klustret använder äldre Azure AD-integrering kan du uppgradera till AKS-hanterad Azure AD-integrering.

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

En lyckad migrering av ett AKS-hanterat Azure AD-kluster har följande avsnitt i svars texten

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Följ stegen [nedan][access-cluster]om du vill ha åtkomst till klustret.

## <a name="non-interactive-sign-in-with-kubelogin"></a>Icke-interaktiv inloggning med kubelogin

Det finns vissa icke-interaktiva scenarier, t. ex. kontinuerliga integrerings pipeliner, som inte är tillgängliga med kubectl. Du kan använda [`kubelogin`](https://github.com/Azure/kubelogin) för att få åtkomst till klustret med icke-interaktiv inloggning för tjänstens huvud namn.

## <a name="use-conditional-access-with-azure-ad-and-aks"></a>Använda villkorlig åtkomst med Azure AD och AKS

När du integrerar Azure AD med ditt AKS-kluster kan du också använda [villkorlig åtkomst][aad-conditional-access] för att kontrol lera åtkomsten till klustret.

> [!NOTE]
> Villkorlig åtkomst för Azure AD är en Azure AD Premium-funktion.

Utför följande steg för att skapa en exempel princip för villkorlig åtkomst som ska användas med AKS:

1. Sök efter och välj Azure Active Directory överst i Azure Portal.
1. I menyn för Azure Active Directory på vänster sida väljer du *företags program*.
1. Välj *villkorlig åtkomst* på menyn för företags program på vänster sida.
1. I menyn för villkorlig åtkomst på den vänstra sidan väljer du *principer* och sedan *ny princip*.
    :::image type="content" source="./media/managed-aad/conditional-access-new-policy.png" alt-text="Lägga till en princip för villkorlig åtkomst":::
1. Ange ett namn för principen, till exempel *AKS-policy*.
1. Välj *användare och grupper* och välj sedan *Välj användare och grupper* under *Inkludera* . Välj de användare och grupper där du vill tillämpa principen. I det här exemplet väljer du samma Azure AD-grupp som har administrations åtkomst till klustret.
    :::image type="content" source="./media/managed-aad/conditional-access-users-groups.png" alt-text="Välja användare eller grupper för att tillämpa principen för villkorlig åtkomst":::
1. Välj *molnappar eller åtgärder* och klicka sedan på *ta med* Välj *appar*. Sök efter *Azure Kubernetes-tjänsten* och välj *Azure Kubernetes service AAD-Server*.
    :::image type="content" source="./media/managed-aad/conditional-access-apps.png" alt-text="Välja Azure Kubernetes service AD server för att tillämpa principen för villkorlig åtkomst":::
1. Under *Åtkomstkontroller* väljer du *Bevilja*. Välj *bevilja åtkomst* och *Kräv att enheten ska markeras som kompatibel*.
    :::image type="content" source="./media/managed-aad/conditional-access-grant-compliant.png" alt-text="Välja att bara tillåta kompatibla enheter för principen för villkorlig åtkomst":::
1. Under *Aktivera princip* väljer du *på* sedan *skapa*.
    :::image type="content" source="./media/managed-aad/conditional-access-enable-policy.png" alt-text="Aktivera principen för villkorlig åtkomst":::

Hämta användarautentiseringsuppgifter för att komma åt klustret, till exempel:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

Följ anvisningarna för att logga in.

Använd `kubectl get nodes` kommandot för att Visa noder i klustret:

```azurecli-interactive
kubectl get nodes
```

Följ instruktionerna för att logga in igen. Observera att det finns ett fel meddelande om att du har loggat in, men administratören kräver att enheten som begär åtkomst hanteras av Azure AD för att få åtkomst till resursen.

I Azure Portal navigerar du till Azure Active Directory, väljer *företags program* under *aktivitet* Välj *inloggningar*. Observera att en post överst med *statusen* *misslyckades* och en *villkorlig åtkomst* till *lyckades*. Markera posten och välj *villkorlig åtkomst* i *detalj*. Observera att den villkorliga åtkomst principen visas.

:::image type="content" source="./media/managed-aad/conditional-access-sign-in-activity.png" alt-text="Inloggnings posten misslyckades på grund av en princip för villkorlig åtkomst":::

## <a name="configure-just-in-time-cluster-access-with-azure-ad-and-aks"></a>Konfigurera just-in-Time-kluster-åtkomst med Azure AD och AKS

Ett annat alternativ för kluster åtkomst kontroll är att använda Privileged Identity Management (PIM) för just-in-Time-begäranden.

>[!NOTE]
> PIM är en Azure AD Premium-funktion som kräver Premium P2 SKU. Mer information om Azure AD SKU: er finns i [prissättnings guiden][aad-pricing].

Utför följande steg för att integrera just-in-Time-begäranden med ett AKS-kluster med AKS-hanterad Azure AD-integrering:

1. Sök efter och välj Azure Active Directory överst i Azure Portal.
1. Anteckna klient-ID: t, som hänvisas till i resten av de här instruktionerna som `<tenant-id>` :::image type="content" source="./media/managed-aad/jit-get-tenant-id.png" alt-text="i en webbläsare, Azure Portal skärmen för Azure Active Directory visas med klientens ID markerat.":::
1. På menyn för Azure Active Directory till vänster går du till gruppen *Hantera* Välj *grupper* och sedan *ny grupp*.
    :::image type="content" source="./media/managed-aad/jit-create-new-group.png" alt-text="Visar skärmen Azure Portal Active Directory grupper med alternativet &quot;ny grupp&quot; markerat.":::
1. Se till att en grupp typ av *säkerhet* är markerad och ange ett grupp namn, till exempel *myJITGroup*. Under *Azure AD-roller kan tilldelas den här gruppen (för hands version) väljer du* *Ja*. Välj slutligen *skapa*.
    :::image type="content" source="./media/managed-aad/jit-new-group-created.png" alt-text="Visar Azure Portalens skärm för att skapa en ny grupp.":::
1. Du kommer tillbaka till sidan *grupper* . Välj den nya gruppen och anteckna det objekt-ID som refereras till i resten av dessa instruktioner som `<object-id>` .
    :::image type="content" source="./media/managed-aad/jit-get-object-id.png" alt-text="Visar Azure Portal skärmen för den just skapade gruppen och markerar objekt-ID: t":::
1. Distribuera ett AKS-kluster med AKS-hanterad Azure AD-integrering genom att använda- `<tenant-id>` och- `<object-id>` värden från tidigare:
    ```azurecli-interactive
    az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <object-id> --aad-tenant-id <tenant-id>
    ```
1. Gå tillbaka till Azure Portal och välj *privilegie rad åtkomst (för hands version)* på menyn för *aktivitet* på vänster sida och välj *Aktivera privilegie rad åtkomst*.
    :::image type="content" source="./media/managed-aad/jit-enabling-priv-access.png" alt-text="Sidan Azure Portal privilegie rad åtkomst (för hands version) visas, med alternativet Aktivera privilegie rad åtkomst":::
1. Välj *Lägg till tilldelningar* för att börja bevilja åtkomst.
    :::image type="content" source="./media/managed-aad/jit-add-active-assignment.png" alt-text="Azure Portalens skärm för privilegie rad åtkomst (för hands version) efter aktivering visas. Alternativet att lägga till tilldelningar är markerat.":::
1. Välj en roll för *medlem* och välj de användare och grupper som du vill bevilja åtkomst till klustret. Tilldelningarna kan ändras när som helst av en grupp administratör. När du är redo att gå vidare väljer du *Nästa*.
    :::image type="content" source="./media/managed-aad/jit-adding-assignment.png" alt-text="Skärmen för att lägga till medlemskap i Azure Portal på medlemskap visas, där en exempel användare har marker ATS för att läggas till som medlem. Alternativet Next är markerat.":::
1. Välj en tilldelnings typ som *aktiv*, önskad varaktighet och ge en motivering. När du är redo att fortsätta väljer du *tilldela*. Mer information om tilldelnings typer finns [i tilldela berättigande till en privilegie rad åtkomst grupp (för hands version) i Privileged Identity Management][aad-assignments].
    :::image type="content" source="./media/managed-aad/jit-set-active-assignment-details.png" alt-text="Skärmen Azure Portal för att lägga till inställningen Lägg till tilldelning visas. Tilldelnings typen aktiv har valts och en exempel justering har angetts. Alternativet Assign är markerat.":::

När tilldelningarna har gjorts kontrollerar du att åtkomsten till klustret är just-in-Time-åtkomst. Exempel:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

Följ stegen för att logga in.

Använd `kubectl get nodes` kommandot för att Visa noder i klustret:

```azurecli-interactive
kubectl get nodes
```

Observera autentiseringskrav och följ de steg som krävs för att autentisera. Om det lyckas bör du se utdata som liknar följande:

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-61156405-vmss000000   Ready    agent   6m36s   v1.18.14
aks-nodepool1-61156405-vmss000001   Ready    agent   6m42s   v1.18.14
aks-nodepool1-61156405-vmss000002   Ready    agent   6m33s   v1.18.14
```

### <a name="troubleshooting"></a>Felsökning

Om `kubectl get nodes` returnerar ett fel som liknar följande:

```output
Error from server (Forbidden): nodes is forbidden: User "aaaa11111-11aa-aa11-a1a1-111111aaaaa" cannot list resource "nodes" in API group "" at the cluster scope
```

Se till att administratören för säkerhets gruppen har gett ditt konto en *aktiv* tilldelning.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Azure RBAC-integrering för Kubernetes-auktorisering][azure-rbac-integration]
* Lär dig mer om [Azure AD-integrering med KUBERNETES RBAC][azure-ad-rbac].
* Använd [kubelogin](https://github.com/Azure/kubelogin) för att få åtkomst till funktioner för Azure-autentisering som inte är tillgängliga i kubectl.
* Lär dig mer om [AKS och Kubernetes Identity Concepts][aks-concepts-identity].
* Använd [Azure Resource Manager arm-mallar ][aks-arm-template] för att skapa AKS-hanterade Azure AD-kluster.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[aad-pricing]: https://azure.microsoft.com/pricing/details/active-directory/

<!-- LINKS - Internal -->
[aad-conditional-access]: ../active-directory/conditional-access/overview.md
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
[aad-assignments]: ../active-directory/privileged-identity-management/groups-assign-member-owner.md#assign-an-owner-or-member-of-a-group
