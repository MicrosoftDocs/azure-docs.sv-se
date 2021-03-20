---
title: Autentisera med tjänstens huvudnamn
description: Ge åtkomst till avbildningar i ditt privata behållar register med hjälp av ett Azure Active Directory tjänstens huvud namn.
ms.topic: article
ms.date: 10/04/2019
ms.openlocfilehash: 8d49628576a1c337efaea3e5286fef00e39def17
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86259140"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Azure Container Registry autentisering med tjänstens huvud namn

Du kan använda ett Azure Active Directory (Azure AD) tjänstens huvud namn för att tillhandahålla behållar avbildning `docker push` och `pull` åtkomst till behållar registret. Genom att använda ett huvud namn för tjänsten kan du ge åtkomst till "konsol löst" tjänster och program.

## <a name="what-is-a-service-principal"></a>Vad är ett huvudnamn för tjänsten?

Azure AD *-tjänstens huvud namn* ger åtkomst till Azure-resurser i din prenumeration. Du kan tänka på ett huvud namn för tjänsten som användar identitet för en tjänst, där "tjänst" är alla program, tjänster eller plattformar som behöver åtkomst till resurserna. Du kan konfigurera ett huvud namn för tjänsten med enbart åtkomst rättigheter för de resurser som du anger. Konfigurera sedan ditt program eller din tjänst att använda tjänstens huvud namn för att få åtkomst till dessa resurser.

I samband med Azure Container Registry kan du skapa en Azure AD-tjänstens huvud namn med pull, push och pull eller andra behörigheter till ditt privata register i Azure. En fullständig lista finns i [Azure Container Registry roller och behörigheter](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Varför ska jag använda ett huvud namn för tjänsten?

Genom att använda ett Azure AD-tjänstens huvud namn kan du ge begränsad åtkomst till ditt privata behållar register. Skapa olika tjänst huvud namn för var och en av dina program eller tjänster, var och en med anpassade åtkomst rättigheter till registret. Eftersom du kan undvika att dela autentiseringsuppgifter mellan tjänster och program kan du rotera autentiseringsuppgifter eller återkalla åtkomsten för enbart tjänstens huvud namn (och därmed det program) du väljer.

Du kan till exempel konfigurera ditt webb program så att det använder ett huvud namn för tjänsten som bara ger avbildnings `pull` åtkomst, medan ditt build-system använder ett huvud namn för tjänsten som ger det till gång till både `push` och `pull` . Om utvecklingen av dina program ändringar händer kan du rotera dess huvud namn för tjänsten utan att påverka build-systemet.

## <a name="when-to-use-a-service-principal"></a>När du ska använda ett huvud namn för tjänsten

Du bör använda ett huvud namn för tjänsten för att ge register åtkomst i **konsolbaserade scenarier**. Det vill säga alla program, tjänster eller skript som måste push-överföra eller hämta behållar avbildningar på ett automatiserat eller på annat sätt obevakat sätt. Exempel:

  * *Hämta*: Distribuera behållare från ett register till Orchestration-system, inklusive KUBERNETES, DC/OS och Docker Swarm. Du kan också hämta från behållar register till relaterade Azure-tjänster som [Azure Kubernetes service (AKS)](../aks/cluster-container-registry-integration.md), [Azure Container instances](container-registry-auth-aci.md), [App Service](../app-service/index.yml), [batch](../batch/index.yml), [Service Fabric](../service-fabric/index.yml)och andra.

  * *Push*: Bygg behållar avbildningar och skicka dem till ett register med kontinuerlig integrering och distributions lösningar som Azure-pipeliner eller Jenkins.

För individuell åtkomst till ett register, till exempel när du hämtar en behållar avbildning manuellt till din utvecklings arbets Station, rekommenderar vi att du använder din egen [Azure AD-identitet](container-registry-authentication.md#individual-login-with-azure-ad) i stället för register åtkomst (till exempel med [AZ ACR login][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Exempelskript

Du kan hitta föregående exempel skript för Azure CLI på GitHub, samt versioner för Azure PowerShell:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Autentisera med tjänstens huvud namn

När du har ett huvud namn för tjänsten som du har beviljat åtkomst till behållar registret kan du konfigurera dess autentiseringsuppgifter för åtkomst till "automatiskt" tjänster och program, eller ange dem med hjälp av `docker login` kommandot. Använd följande värden:

* **Användar namn** – program-ID för tjänstens huvud namn (kallas även *klient-ID*)
* **Lösen** ord för tjänstens huvud namn (kallas även *klient hemlighet*)

Varje värde är ett GUID för formuläret `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` . 

> [!TIP]
> Du kan återskapa lösen ordet för ett huvud namn för tjänsten genom att köra kommandot [AZ AD SP reset-credentials](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) .
>

### <a name="use-credentials-with-azure-services"></a>Använd autentiseringsuppgifter med Azure-tjänster

Du kan använda autentiseringsuppgifter för tjänstens huvud namn från valfri Azure-tjänst som autentiserar med ett Azure Container Registry.  Använd autentiseringsuppgifterna för tjänstens huvud namn i stället för registrets administratörsautentiseringsuppgifter för en mängd olika scenarier.

Använd till exempel autentiseringsuppgifterna för att hämta en avbildning från ett Azure Container Registry till [Azure Container instances](container-registry-auth-aci.md).

### <a name="use-with-docker-login"></a>Använd med Docker-inloggning

Du kan köra `docker login` med ett huvud namn för tjänsten. I följande exempel skickas ID: t för tjänstens huvud namn i miljö variabeln `$SP_APP_ID` och lösen ordet i variabeln `$SP_PASSWD` . Metod tips för att hantera Docker-autentiseringsuppgifter finns i kommando referensen [Docker login](https://docs.docker.com/engine/reference/commandline/login/) .

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

När du har loggat in cachelagras autentiseringsuppgifterna av Docker.

### <a name="use-with-certificate"></a>Använd med certifikat

Om du har lagt till ett certifikat till tjänstens huvud namn kan du logga in på Azure CLI med certifikatbaserad autentisering och sedan använda kommandot [AZ ACR login][az-acr-login] för att få åtkomst till ett register. Att använda ett certifikat som en hemlighet i stället för ett lösen ord ger ytterligare säkerhet när du använder CLI. 

Du kan skapa ett självsignerat certifikat när du [skapar ett huvud namn för tjänsten](/cli/azure/create-an-azure-service-principal-azure-cli). Du kan också lägga till ett eller flera certifikat till ett befintligt huvud namn för tjänsten. Om du till exempel använder ett av skripten i den här artikeln för att skapa eller uppdatera ett huvud namn för tjänsten med rättigheter att hämta eller skicka avbildningar från ett register, lägger du till ett certifikat med hjälp av kommandot [AZ AD SP Credential rereset][az-ad-sp-credential-reset] .

Om du vill använda tjänstens huvud namn med certifikat för att [Logga in på Azure CLI](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal)måste certifikatet vara i PEM-format och innehålla den privata nyckeln. Om certifikatet inte är i det format som krävs använder du ett verktyg som `openssl` till exempel för att konvertera det. När du kör [AZ-inloggningen][az-login] för att logga in på CLI med hjälp av tjänstens huvud namn, ska du också ange program-ID för tjänstens huvud namn och Active Directory klient-ID. I följande exempel visas dessa värden som miljövariabler:

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

Kör sedan [AZ ACR-inloggning][az-acr-login] för att autentisera med registret:

```azurecli
az acr login --name myregistry
```

CLI använder den token som skapades när du körde `az login` för att autentisera din session med registret.

## <a name="next-steps"></a>Nästa steg

* I [översikten över autentiseringen](container-registry-authentication.md) finns andra scenarier för att autentisera med ett Azure Container Registry.

* Ett exempel på hur du använder ett Azure Key Vault för att lagra och hämta autentiseringsuppgifter för tjänstens huvud namn för ett behållar register finns i självstudien för att [skapa och distribuera en behållar avbildning med ACR uppgifter](container-registry-tutorial-quick-task.md).

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-login]: /cli/azure/reference-index#az-login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
