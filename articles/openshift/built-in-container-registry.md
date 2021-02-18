---
title: Konfigurera inbyggt containerregister för Azure Red Hat OpenShift 4
description: Konfigurera inbyggt containerregister för Azure Red Hat OpenShift 4
author: jiangma
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: dda050b8d824f0ff0ac1c84d2f008387de55aedf
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636392"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>Konfigurera inbyggt containerregister för Azure Red Hat OpenShift 4

Azure Red Hat OpenShift tillhandahåller ett integrerat behållar avbildnings register med namnet [OpenShift container Registry (OCR)](https://docs.openshift.com/container-platform/4.6/registry/architecture-component-imageregistry.html) som lägger till möjligheten att automatiskt etablera nya avbildnings databaser på begäran. Detta ger användare en inbyggd plats för deras program versioner för att skicka de resulterande avbildningarna.

I den här artikeln ska du konfigurera det inbyggda registret för behållar avbildningar för ett Azure Red Hat OpenShift-kluster (ARO) 4. Du lär dig följande:

> [!div class="checklist"]
> * Konfigurera Azure AD
> * Konfigurera OpenID Connect
> * Åtkomst till det inbyggda registret för behållar avbildningar

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt ARO-kluster. Om du behöver ett ARO-kluster går du till självstudien för ARO och [skapar ett Azure Red Hat OpenShift 4-kluster](./tutorial-create-cluster.md). Se till att skapa klustret med `--pull-secret` argumentet till `az aro create` .  Detta är nödvändigt för att konfigurera Azure Active Directory autentisering och det inbyggda behållar registret.

När du har klustret kan du ansluta till klustret genom att följa stegen i [ansluta till ett Azure Red Hat OpenShift 4-kluster](./tutorial-connect-cluster.md).
   * Se till att följa stegen i "installera OpenShift CLI" eftersom vi använder `oc` kommandot senare i den här artikeln.
   * Anteckna kluster konsolens URL, som ser ut så här `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` . Värdena för `<random>` och `<region>` kommer att användas senare i den här artikeln.
   * Anteckna `kubeadmin` autentiseringsuppgifterna. De kommer också att användas senare i den här artikeln.

### <a name="configure-azure-active-directory-authentication"></a>Konfigurera Azure Active Directory-autentisering 

Azure Active Directory (Azure AD) implementerar OpenID Connect (OIDC). Med OIDC kan du använda Azure AD för att logga in på ARO-klustret. Följ stegen i [konfigurera Azure Active Directory-autentisering](configure-azure-ad-cli.md) för att konfigurera klustret.

## <a name="access-the-built-in-container-image-registry"></a>Åtkomst till det inbyggda registret för behållar avbildningar

Nu när du har konfigurerat autentiseringsmetoderna för ARO-klustret, ska vi aktivera åtkomst till det inbyggda registret.

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>Definiera Azure AD-användaren som administratör

1. Logga in på webb konsolen OpenShift från webbläsaren med autentiseringsuppgifterna för en Azure AD-användare. Vi använder OpenID-autentiseringen OpenShift mot Azure Active Directory att använda OpenID för att definiera administratören.

   1. Använd en InPrivate-, Incognito-eller motsvarande webb läsar fönster funktion för att logga in i-konsolen. Fönstret ser annorlunda ut efter det att OIDC har Aktiver ATS.
   
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="OpenID Connect-aktiverat inloggnings fönster.":::
   1. Välj **OpenID**

   > [!NOTE]
   > Anteckna användar namnet och lösen ordet som du använder för att logga in här. Det här användar namnet och lösen ordet fungerar som administratör för andra åtgärder i den här och andra artiklar.
2. Logga in med OpenShift CLI med hjälp av följande steg.  Den här processen kallas för diskussion `oc login` .
   1. I den högra delen av webb konsolen expanderar du snabb menyn för den inloggade användaren och väljer sedan **Kopiera inloggnings kommando**.
   2. Logga in på ett nytt flik-fönster med samma användare om det behövs.
   3. Välj **visnings-token**.
   4. Kopiera värdet i listan nedan **Logga in med denna token** till Urklipp och kör det i ett gränssnitt, som visas här.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

3. Kör `oc whoami` i-konsolen och anteckna utdata som **\<aad-user>** .  Vi använder det här värdet senare i artikeln.
4. Logga ut från OpenShift-webbkonsolen. Välj knappen högst upp till höger i webbläsarfönstret som är märkt som **\<aad-user>** och välj **Logga ut**.


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>Ge Azure AD-användaren nödvändiga roller för register interaktion

1. Logga in på webb konsolen OpenShift från webbläsaren med hjälp av `kubeadmin` autentiseringsuppgifterna.
1. Logga in på OpenShift CLI med token för `kubeadmin` genom att följa stegen `oc login` ovan, men gör det när du har loggat in på webb konsolen med `kubeadmin` .
1. Kör följande kommandon för att aktivera åtkomst till det inbyggda registret för **AAD-användaren**.

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   
   # Output should look similar to the following.
   # Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge

   # Output should look similar to the following.
   # config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>

   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

#### <a name="obtain-the-container-registry-url"></a>Hämta container Registry-URL: en

Använd `oc get route` kommandot som visas bredvid Hämta behållarens register-URL.

```bash
# Note: the value of "Container Registry URL" in the output is the fully qualified registry name.
HOST=$(oc get route default-route --template='{{ .spec.host }}')
echo "Container Registry URL: $HOST"
```

   > [!NOTE]
   > Observera konsolens utdata från **container Registry-URL**. Den kommer att användas som fullständigt register namn för den här guiden och efterföljande.

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat det inbyggda registret för behållar avbildningar kan du komma igång genom att distribuera ett program i OpenShift. För Java-program kan du ta [en titt på distribuera ett Java-program med Open frihet/WebSphere frihet på ett Azure Red Hat OpenShift 4-kluster](howto-deploy-java-liberty-app.md).