---
title: Använda Azure AD i Azure Kubernetes Service
description: Lär dig hur du använder Azure AD i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 02/1/2021
ms.author: miwithro
ms.openlocfilehash: 3db9f8d895b4c13b5f969859f422e7b566722ffc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783079"
---
# <a name="aks-managed-azure-active-directory-integration"></a>AKS-hanterad Azure Active Directory integrering

AKS-hanterad Azure AD-integrering är utformad för att förenkla Integrering med Azure AD, där användare tidigare krävdes för att skapa en klientapp, en serverapp och krävde att Azure AD-klienten skulle bevilja läsbehörighet för katalog. I den nya versionen hanterar AKS-resursprovidern klient- och serverapparna åt dig.

## <a name="azure-ad-authentication-overview"></a>Översikt över Azure AD-autentisering

Klusteradministratörer kan konfigurera Kubernetes rollbaserad åtkomstkontroll (Kubernetes RBAC) baserat på en användares identitet eller kataloggruppsmedlemskap. Azure AD-autentisering tillhandahålls till AKS-kluster med OpenID Connect. OpenID Connect är ett identitetslager som bygger på OAuth 2.0-protokollet. Mer information om hur OpenID Connect finns i [dokumentationen för Open ID Connect.][open-id-connect]

Läs mer om Azure AD-integreringsflödet i [dokumentationen Azure Active Directory om integrationsbegrepp.](concepts-identity.md#azure-ad-integration)

## <a name="limitations"></a>Begränsningar 

* AKS-hanterad Azure AD-integrering kan inte inaktiveras
* Det finns inte stöd för att ändra ett AKS-hanterat Azure AD-integrerat kluster till äldre AAD
* RBAC-aktiverade kluster som inte är Kubernetes stöds inte för AKS-hanterad Azure AD-integrering
* Det går inte att ändra den Azure AD-klient som är associerad med AKS-hanterad Azure AD-integrering

## <a name="prerequisites"></a>Förutsättningar

* Azure CLI version 2.11.0 eller senare
* Kubectl med lägst version [1.18.1 eller](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) [kubelogin](https://github.com/Azure/kubelogin)
* Om du använder [helm ,](https://github.com/helm/helm)lägsta version av helm 3.3.

> [!Important]
> Du måste använda Kubectl med lägst version 1.18.1 eller kubelogin. Skillnaden mellan mindre versioner av Kubernetes och kubectl får inte vara mer än 1 version. Om du inte använder rätt version ser du autentiseringsproblem.

Använd följande kommandon för att installera kubectl och kubelogin:

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
kubelogin --version
```

Följ [dessa anvisningar](https://kubernetes.io/docs/tasks/tools/install-kubectl/) för andra operativsystem.

## <a name="before-you-begin"></a>Innan du börjar

För klustret behöver du en Azure AD-grupp. Den här gruppen behövs som administratörsgrupp för klustret för att bevilja klusteradministratörsbehörigheter. Du kan använda en befintlig Azure AD-grupp eller skapa en ny. Registrera objekt-ID:t för din Azure AD-grupp.

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

Om du vill skapa en ny Azure AD-grupp för dina klusteradministratörer använder du följande kommando:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Skapa ett AKS-kluster med Azure AD aktiverat

Skapa ett AKS-kluster med hjälp av följande CLI-kommandon.

Skapa en Azure-resursgrupp:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Skapa ett AKS-kluster och aktivera administrationsåtkomst för din Azure AD-grupp

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Ett lyckat skapande av ett AKS-hanterat Azure AD-kluster har följande avsnitt i svarstexten
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

## <a name="access-an-azure-ad-enabled-cluster"></a>Få åtkomst till ett Azure AD-aktiverat kluster

Du behöver den [inbyggda Azure Kubernetes Service](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) klusteranvändaren för att kunna göra följande.

Hämta autentiseringsuppgifterna för användaren för att få åtkomst till klustret:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
Följ instruktionerna för att logga in.

Använd kommandot kubectl get nodes för att visa noder i klustret:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
Konfigurera [rollbaserad åtkomstkontroll i Azure (Azure RBAC)](./azure-ad-rbac.md) för att konfigurera ytterligare säkerhetsgrupper för dina kluster.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Felsöka åtkomstproblem med Azure AD

> [!Important]
> Stegen som beskrivs nedan kringgår den normala Azure AD-gruppautentiseringen. Använd dem endast i nödfall.

Om du blockeras permanent genom att inte ha åtkomst till en giltig Azure AD-grupp med åtkomst till klustret kan du fortfarande hämta administratörsautentiseringsuppgifterna för att komma åt klustret direkt.

Om du vill göra dessa steg måste du ha åtkomst till [den inbyggda Azure Kubernetes Service klusteradministratören.](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role)

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="enable-aks-managed-azure-ad-integration-on-your-existing-cluster"></a>Aktivera AKS-hanterad Azure AD-integrering i ditt befintliga kluster

Du kan aktivera AKS-hanterad Azure AD-integrering på ditt befintliga Kubernetes RBAC-aktiverade kluster. Se till att ange att administratörsgruppen ska ha åtkomst till klustret.

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad --aad-admin-group-object-ids <id-1> [--aad-tenant-id <id>]
```

En lyckad aktivering av ett AKS-hanterat Azure AD-kluster innehåller följande avsnitt i svarstexten

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

Ladda ned användarautentiseringsuppgifter igen för att få åtkomst till klustret genom att följa stegen [här.][access-cluster]

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>Uppgradera till AKS-hanterad Azure AD-integrering

Om klustret använder äldre Azure AD-integrering kan du uppgradera till AKS-hanterad Azure AD-integrering.

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

En lyckad migrering av ett AKS-hanterat Azure AD-kluster innehåller följande avsnitt i svarstexten

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

Om du vill komma åt klustret följer du stegen [här][access-cluster].

## <a name="non-interactive-sign-in-with-kubelogin"></a>Icke-interaktiv inloggning med kubelogin

Det finns vissa icke-interaktiva scenarier, till exempel pipelines för kontinuerlig integrering, som för närvarande inte är tillgängliga med kubectl. Du kan använda [`kubelogin`](https://github.com/Azure/kubelogin) för att komma åt klustret med icke-interaktiv inloggning för tjänstens huvudnamn.

## <a name="use-conditional-access-with-azure-ad-and-aks"></a>Använda villkorlig åtkomst med Azure AD och AKS

När du integrerar Azure AD med ditt AKS-kluster kan du också använda [villkorlig åtkomst för][aad-conditional-access] att styra åtkomsten till klustret.

> [!NOTE]
> Villkorsstyrd åtkomst i Azure AD är Azure AD Premium funktion.

Utför följande steg för att skapa ett exempel på en princip för villkorlig åtkomst som ska användas med AKS:

1. Längst upp i Azure Portal du efter och väljer Azure Active Directory.
1. I menyn för Azure Active Directory på vänster sida väljer du *Företagsprogram*.
1. På menyn för Företagsprogram till vänster väljer du *Villkorlig åtkomst.*
1. På menyn för Villkorlig åtkomst till vänster väljer du Principer *och* sedan *Ny princip.*
    :::image type="content" source="./media/managed-aad/conditional-access-new-policy.png" alt-text="Lägga till en princip för villkorlig åtkomst":::
1. Ange ett namn för principen, till exempel *aks-policy*.
1. Välj *Användare och grupper* och välj sedan Välj användare och grupper under *Inkludera.*  Välj de användare och grupper där du vill tillämpa principen. I det här exemplet väljer du samma Azure AD-grupp som har administrationsåtkomst till klustret.
    :::image type="content" source="./media/managed-aad/conditional-access-users-groups.png" alt-text="Välja användare eller grupper för att tillämpa principen för villkorlig åtkomst":::
1. Välj *Molnappar eller åtgärder* och välj *sedan* Välj appar *under Inkludera.* Sök efter *Azure Kubernetes Service* och välj *Azure Kubernetes Service AAD Server*.
    :::image type="content" source="./media/managed-aad/conditional-access-apps.png" alt-text="Välja Azure Kubernetes Service AD Server för att tillämpa principen för villkorlig åtkomst":::
1. Under *Åtkomstkontroller* väljer du *Bevilja*. Välj *Bevilja åtkomst* och sedan *Kräv att enheten är markerad som kompatibel.*
    :::image type="content" source="./media/managed-aad/conditional-access-grant-compliant.png" alt-text="Välja att endast tillåta kompatibla enheter för principen för villkorlig åtkomst":::
1. Under *Aktivera princip* väljer du *På* och sedan *Skapa.*
    :::image type="content" source="./media/managed-aad/conditional-access-enable-policy.png" alt-text="Aktivera principen för villkorlig åtkomst":::

Hämta autentiseringsuppgifterna för att få åtkomst till klustret, till exempel:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

Följ anvisningarna för att logga in.

Använd kommandot `kubectl get nodes` för att visa noder i klustret:

```azurecli-interactive
kubectl get nodes
```

Följ instruktionerna för att logga in igen. Observera att det finns ett felmeddelande om att du har loggat in, men administratören kräver att enheten som begär åtkomst ska hanteras av Azure AD för att få åtkomst till resursen.

I Azure Portal navigerar du till Azure Active Directory företagsprogram *och* under *Aktivitet* väljer *du Inloggningar.* Lägg märke till en post högst upp med *statusen* *Misslyckades* och *villkorsstyrd åtkomst* *till Lyckades.* Välj posten och sedan *Villkorlig åtkomst* i *Information.* Observera att din princip för villkorlig åtkomst visas.

:::image type="content" source="./media/managed-aad/conditional-access-sign-in-activity.png" alt-text="Det gick inte att logga in på grund av principen för villkorsstyrd åtkomst":::

## <a name="configure-just-in-time-cluster-access-with-azure-ad-and-aks"></a>Konfigurera just-in-time-klusteråtkomst med Azure AD och AKS

Ett annat alternativ för klusteråtkomstkontroll är att använda Privileged Identity Management (PIM) för just-in-time-begäranden.

>[!NOTE]
> PIM är en Azure AD Premium funktion som kräver en Premium P2 SKU. Mer information om Azure AD-SKU:er finns i [prisguiden.][aad-pricing]

Om du vill integrera just-in-time-åtkomstbegäranden med ett AKS-kluster med hjälp av AKS-hanterad Azure AD-integrering slutför du följande steg:

1. Längst upp i Azure Portal du efter och väljer Azure Active Directory.
1. Anteckna klientorganisations-ID:t, som resten av instruktionerna kallas i en `<tenant-id>` :::image type="content" source="./media/managed-aad/jit-get-tenant-id.png" alt-text="webbläsare, Azure Portal-skärmen för Azure Active Directory"::: visas med klientens ID markerat.
1. I menyn för Azure Active Directory till vänster under Hantera väljer *du* *Grupper och* sedan *Ny grupp.*
    :::image type="content" source="./media/managed-aad/jit-create-new-group.png" alt-text="Visar skärmen Azure Portal Active Directory-grupper med alternativet Ny grupp markerat.":::
1. Kontrollera att en Grupptyp för *säkerhet* har valts och ange ett gruppnamn, till exempel *myJITGroup.* Under *Azure AD Roles can be assigned to this group (Preview) (Azure AD-roller kan* tilldelas till den här gruppen (förhandsversion) ) väljer du *Ja.* Välj slutligen *Skapa*.
    :::image type="content" source="./media/managed-aad/jit-new-group-created.png" alt-text="Visar Azure Portal nya skärmen för att skapa grupper.":::
1. Du kommer tillbaka till *sidan* Grupper. Välj den nya gruppen och anteckna objekt-ID:t, som i resten av instruktionerna kallas `<object-id>` .
    :::image type="content" source="./media/managed-aad/jit-get-object-id.png" alt-text="Visar Azure Portal för den nyss skapade gruppen med objekt-ID:t":::
1. Distribuera ett AKS-kluster med AKS-hanterad Azure AD-integrering med hjälp av `<tenant-id>` `<object-id>` värdena och från tidigare:
    ```azurecli-interactive
    az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <object-id> --aad-tenant-id <tenant-id>
    ```
1. När du Azure Portal i menyn för  Aktivitet till vänster väljer du Privilegierad åtkomst *(förhandsversion)* och sedan *Aktivera privilegierad åtkomst.*
    :::image type="content" source="./media/managed-aad/jit-enabling-priv-access.png" alt-text="Sidan Azure Portal privilegierad åtkomst (förhandsversion) visas med &quot;Aktivera privilegierad åtkomst&quot; markerat":::
1. Välj *Lägg till tilldelningar för* att börja bevilja åtkomst.
    :::image type="content" source="./media/managed-aad/jit-add-active-assignment.png" alt-text="Skärmen Azure Portal privilegierad åtkomst (förhandsversion) efter aktiveringen visas. Alternativet &quot;Lägg till tilldelningar&quot; är markerat.":::
1. Välj en roll *som medlem* och välj de användare och grupper som du vill bevilja klusteråtkomst till. Dessa tilldelningar kan ändras när som helst av en gruppadministratör. När du är redo att gå vidare väljer du *Nästa.*
    :::image type="content" source="./media/managed-aad/jit-adding-assignment.png" alt-text="Skärmen Azure Portal Lägg till tilldelningar Medlemskap visas, där en exempelanvändare har valts för att läggas till som medlem. Alternativet &quot;Nästa&quot; är markerat.":::
1. Välj tilldelningstypen *Aktiv,* önskad varaktighet och ange en motivering. När du är redo att fortsätta väljer du *Tilldela*. Mer information om tilldelningstyper finns [i Tilldela behörighet för en privilegierad åtkomstgrupp (förhandsversion) i Privileged Identity Management][aad-assignments].
    :::image type="content" source="./media/managed-aad/jit-set-active-assignment-details.png" alt-text="Skärmen Azure Portal Lägg till tilldelningar visas på skärmen Lägg till tilldelningar. Tilldelningstypen &quot;Aktiv&quot; har valts och en exempelberättigande har getts. Alternativet Tilldela är markerat.":::

När tilldelningarna har gjorts kontrollerar du att just-in-time-åtkomst fungerar genom att komma åt klustret. Exempel:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

Följ stegen för att logga in.

Använd kommandot `kubectl get nodes` för att visa noder i klustret:

```azurecli-interactive
kubectl get nodes
```

Observera autentiseringskravet och följ stegen för att autentisera. Om det lyckas bör du se utdata som liknar följande:

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

Kontrollera att administratören för säkerhetsgruppen har gett ditt konto en *aktiv* tilldelning.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure RBAC-integrering för Kubernetes-auktorisering][azure-rbac-integration]
* Läs mer [om Azure AD-integrering med Kubernetes RBAC][azure-ad-rbac].
* Använd [kubelogin för](https://github.com/Azure/kubelogin) att komma åt funktioner för Azure-autentisering som inte är tillgängliga i kubectl.
* Läs mer om [identitetsbegreppen IKS och Kubernetes.][aks-concepts-identity]
* Använd [Azure Resource Manager (ARM) för att ][aks-arm-template] skapa AKS-hanterade Azure AD-aktiverade kluster.

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
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-group-create]: /cli/azure/group#az_group_create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az_ad_user_show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
[aad-assignments]: ../active-directory/privileged-identity-management/groups-assign-member-owner.md#assign-an-owner-or-member-of-a-group
