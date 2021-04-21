---
title: Alternativ för registerautentisering
description: Autentiseringsalternativ för ett privat Azure-containerregister, inklusive inloggning med en Azure Active Directory identitet, med hjälp av tjänstens huvudnamn och med valfria administratörsautentiseringsuppgifter.
ms.topic: article
ms.date: 03/15/2021
ms.openlocfilehash: 7ff55d569e2659262ce9f323e4db2ea7ed671d20
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784289"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Autentisera med ett Azure-containerregister

Det finns flera sätt att autentisera med ett Azure-containerregister, där vart och ett kan användas i ett eller flera registeranvändningsscenarier.

Rekommenderade sätt är att autentisera till ett register direkt [via](#individual-login-with-azure-ad)enskild inloggning , eller så kan dina program och containerdirigeringar utföra obevakad eller "huvudlös" autentisering med hjälp av ett Azure Active Directory-tjänsthuvudnamn (Azure AD). [](#service-principal)

## <a name="authentication-options"></a>Autentiseringsalternativ

I följande tabell visas tillgängliga autentiseringsmetoder och vanliga scenarier. Mer information finns i länkat innehåll.

| Metod                               | Så här autentiserar du                                           | Scenarier                                                            | Azure RBAC (rollbaserad åtkomstkontroll)                             | Begränsningar                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Individuell AD-identitet](#individual-login-with-azure-ad)                | `az acr login` i Azure CLI                             | Interaktiv push/pull av utvecklare, testare                                    | Yes                              | AD-token måste förnyas var tredje timme     |
| [AD-tjänstens huvudnamn](#service-principal)                  | `docker login`<br/><br/>`az acr login` i Azure CLI<br/><br/> Registerinloggningsinställningar i API:er eller verktyg<br/><br/> [Kubernetes pull-hemlighet](container-registry-auth-kubernetes.md)                                           | Obevakad push från CI/CD-pipeline<br/><br/> Obevakad pull till Azure eller externa tjänster  | Yes                              | Standardvärdet för SP-lösenord är 1 år       |                                                           
| [Integrera med AKS](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | Bifoga register när AKS-kluster skapas eller uppdateras  | Obevakad pull till AKS-kluster                                                  | Nej, endast pull-åtkomst             | Endast tillgängligt med AKS-kluster            |
| [Hanterad identitet för Azure-resurser](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` i Azure CLI                                       | Obevakad push från Azure CI/CD-pipeline<br/><br/> Obevakad pull till Azure-tjänster<br/><br/>   | Yes                              | Använd endast från utvalda Azure-tjänster som [stöder hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)              |
| [Administratörsanvändare](#admin-account)                            | `docker login`                                          | Interaktiv push/pull av enskild utvecklare eller testare<br/><br/>Portaldistribution av avbildning från registret till Azure App Service eller Azure Container Instances                      | Nej, hämta och push-åtkomst alltid  | Enskilt konto per register, rekommenderas inte för flera användare         |
| [Åtkomsttoken för lagringsplats](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login` i Azure CLI   | Interaktiv push/pull till lagringsplatsen av enskild utvecklare eller testare<br/><br/> Obevakad push/pull till lagringsplatsen av enskilda system eller externa enheter                  | Yes                              | För närvarande inte integrerat med AD-identitet  |

## <a name="individual-login-with-azure-ad"></a>Individuell inloggning med Azure AD

När du arbetar direkt med registret, till exempel hämta avbildningar till och push-pusha avbildningar från en utvecklingsarbetsdator till ett register som du har skapat, autentiserar du med hjälp av din enskilda Azure-identitet. Logga in på [Azure CLI med](/cli/azure/install-azure-cli) az [login](/cli/azure/reference-index#az_login)och kör sedan kommandot [az acr login:](/cli/azure/acr#az_acr_login)

```azurecli
az login
az acr login --name <acrName>
```

När du loggar in med använder CLI den token som skapades när du `az acr login` körde `az login` för att sömlöst autentisera sessionen med registret. För att slutföra autentiseringsflödet måste Docker CLI och Docker-daemon installeras och köras i din miljö. `az acr login` använder Docker-klienten för att Azure Active Directory en token i `docker.config` filen. När du har loggat på det här sättet cachelagras dina autentiseringsuppgifter och efterföljande kommandon i sessionen `docker` kräver inte något användarnamn eller lösenord.

> [!TIP]
> Använd även för att autentisera en enskild identitet när du vill skicka eller hämta andra artefakter än Docker-avbildningar till registret, till `az acr login` exempel [OCI-artefakter](container-registry-oci-artifacts.md).  

För registeråtkomst är den token som används av giltig i 3 timmar, så vi rekommenderar att du alltid loggar in i registret `az acr login` innan du kör ett  `docker` kommando. Om din token upphör att gälla kan du uppdatera den med hjälp av kommandot igen för `az acr login` att återauktorera. 

Med `az acr login` Azure-identiteter får [du rollbaserad åtkomstkontroll i Azure (Azure RBAC).](../role-based-access-control/role-assignments-portal.md) I vissa fall kanske du vill logga in i ett register med din egen enskilda identitet i Azure AD eller konfigurera andra Azure-användare med specifika [Azure-roller och behörigheter.](container-registry-roles.md) För scenarier mellan tjänster eller för att hantera behoven i en arbetsgrupp eller ett utvecklingsarbetsflöde där du inte vill hantera enskild åtkomst kan du också logga in med en hanterad identitet [för Azure-resurser.](container-registry-authentication-managed-identity.md)

### <a name="az-acr-login-with---expose-token"></a>az acr login with --expose-token

I vissa fall kan du behöva autentisera med `az acr login` när Docker-daemonen inte körs i din miljö. Du kan till exempel behöva köra i ett skript i Azure Cloud Shell, som tillhandahåller Docker CLI men inte kör `az acr login` Docker-daemonen.

I det här scenariot `az acr login` kör du först med `--expose-token` parametern . Det här alternativet exponerar en åtkomsttoken i stället för att logga in via Docker CLI.

```azurecli
az acr login --name <acrName> --expose-token
```

Utdata visar åtkomsttoken, förkortat här:

```console
{
  "accessToken": "eyJhbGciOiJSUzI1NiIs[...]24V7wA",
  "loginServer": "myregistry.azurecr.io"
}
``` 
För registerautentisering rekommenderar vi att du lagrar autentiseringsuppgifterna för token på en säker plats och följer rekommenderade metoder för att hantera [autentiseringsuppgifter för Docker-inloggning).](https://docs.docker.com/engine/reference/commandline/login/) Lagra till exempel tokenvärdet i en miljövariabel:

```bash
TOKEN=$(az acr login --name <acrName> --expose-token --output tsv --query accessToken)
```

Kör sedan `docker login` och skicka som användarnamn och använd `00000000-0000-0000-0000-000000000000` åtkomsttoken som lösenord:

```console
docker login myregistry.azurecr.io --username 00000000-0000-0000-0000-000000000000 --password $TOKEN
```

## <a name="service-principal"></a>Tjänstens huvudnamn

Om du tilldelar [ett huvudnamn](../active-directory/develop/app-objects-and-service-principals.md) för tjänsten till registret kan programmet eller tjänsten använda det för headless-autentisering. Tjänstens huvudnamn tillåter rollbaserad åtkomstkontroll [i Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) till ett register och du kan tilldela flera tjänsthuvudnamn till ett register. Med flera tjänsthuvudnamn kan du definiera olika åtkomst för olika program.

Tillgängliga roller för ett containerregister är:

* **AcrPull**: pull

* **AcrPush:** pull och push

* **Ägare:** hämta, push-pusha och tilldela roller till andra användare

En fullständig lista över roller finns i Azure Container Registry [roller och behörigheter](container-registry-roles.md).

CLI-skript för att skapa ett huvudnamn för tjänsten för autentisering med ett Azure-containerregister och mer vägledning finns i [Azure Container Registry autentisering med tjänstens huvudnamn.](container-registry-auth-service-principal.md)

## <a name="admin-account"></a>Administratörskonto

Varje containerregister innehåller ett administratörsanvändarkonto, vilket är inaktiverat som standard. Du kan aktivera administratörsanvändaren och hantera autentiseringsuppgifterna i Azure Portal eller med hjälp av Azure CLI eller andra Azure-verktyg. Administratörskontot har fullständig behörighet till registret.

Administratörskontot krävs för närvarande för att vissa scenarier ska kunna distribuera en avbildning från ett containerregister till vissa Azure-tjänster. Administratörskontot behövs till exempel när du använder Azure Portal för att distribuera en containeravbildning från ett register direkt [till Azure Container Instances](../container-instances/container-instances-using-azure-container-registry.md#deploy-with-azure-portal) eller Azure Web Apps [for Containers](container-registry-tutorial-deploy-app.md).

> [!IMPORTANT]
> Administratörskontot är utformat för att en enskild användare ska kunna komma åt registret, främst i testsyfte. Vi rekommenderar inte att du delar autentiseringsuppgifterna för administratörskontot mellan flera användare. Alla användare som autentiseras med administratörskontot visas som en enskild användare med push-åtkomst och pull-åtkomst till registret. Om du ändrar eller inaktiverar det här kontot inaktiveras registeråtkomst för alla användare som använder dess autentiseringsuppgifter. Enskild identitet rekommenderas för användare och tjänstens huvudnamn för scenarier utan huvudnamn.
>

Administratörskontot får två lösenord, som båda kan återskapas. Med två lösenord kan du upprätthålla anslutningen till registret genom att använda ett lösenord medan du återskapar det andra. Om administratörskontot är aktiverat kan du skicka användarnamnet och lösenordet till kommandot när `docker login` du tillfrågas om grundläggande autentisering till registret. Exempel:

```
docker login myregistry.azurecr.io 
```

Rekommenderade metoder för att hantera inloggningsuppgifter finns i [kommandoreferensen för docker-inloggning.](https://docs.docker.com/engine/reference/commandline/login/)

Om du vill aktivera administratörsanvändaren för ett befintligt register kan du använda parametern för `--admin-enabled` [kommandot az acr update](/cli/azure/acr#az_acr_update) i Azure CLI:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Du kan aktivera administratörsanvändaren i Azure Portal genom att navigera i registret, välja **Åtkomstnycklar** under **INSTÄLLNINGAR** och sedan **Aktivera** under **Administratörsanvändare.**

![Aktivera användargränssnittet för administratörsanvändare i Azure Portal][auth-portal-01]

## <a name="next-steps"></a>Nästa steg

* [Push-skicka din första avbildning med Hjälp av Azure CLI](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
