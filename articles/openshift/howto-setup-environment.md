---
title: Konfigurera din Azure Red Hat-utvecklings miljö för OpenShift
description: Här är kraven för att arbeta med Microsoft Azure Red Hat OpenShift.
keywords: Red Hat OpenShift-installation konfigureras
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: azure-redhat-openshift
ms.custom: devx-track-azurecli
ms.openlocfilehash: c253c6bf81305b9b336525c20980cf9599463648
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102209874"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Konfigurera Azure Red Hat OpenShift-utvecklingsmiljön

> [!IMPORTANT]
> Azure Red Hat OpenShift 3,11 kommer att dras tillbaka 30 juni 2022. Stöd för att skapa nya Azure Red Hat OpenShift 3,11-kluster fortsätter till och med 30 november 2020. Efter pensionering kommer de återstående Azure Red Hat OpenShift 3,11-klustren att stängas av för att förhindra säkerhets problem.
> 
> Följ den här guiden för att [skapa ett Azure Red Hat OpenShift 4-kluster](tutorial-create-cluster.md).
> Om du har frågor kan du [kontakta oss](mailto:arofeedback@microsoft.com).

Om du vill skapa och köra Microsoft Azure Red Hat OpenShift-program måste du:

* Installera version 2.0.65 (eller högre) av Azure CLI (eller Använd Azure Cloud Shell).
* Registrera dig för `AROGA` funktionen och tillhör ande resurs leverantörer.
* Skapa en Azure Active Directory-klient (Azure AD).
* Skapa ett Azure AD-programobjekt.
* Skapa en Azure AD-användare.

I följande anvisningar får du stegvisa instruktioner om alla dessa krav.

## <a name="install-the-azure-cli"></a>Installera Azure CLI

Det krävs version 2.0.65 eller senare av Azure CLI i Azure Red Hat OpenShift. Om du redan har installerat Azure CLI kan du kontrol lera vilken version du har genom att köra:

```azurecli
az --version
```

Den första raden med utdata kommer att ha CLI-versionen, till exempel `azure-cli (2.0.65)` .

Här följer instruktioner för hur du [installerar Azure CLI](/cli/azure/install-azure-cli) om du behöver en ny installation eller en uppgradering.

Alternativt kan du använda [Azure Cloud Shell](../cloud-shell/overview.md). När du använder Azure Cloud Shell bör du välja **bash** -miljön om du planerar att följa tillsammans med själv studie serien [skapa och hantera en Azure Red Hat OpenShift-kluster](tutorial-create-cluster.md) .

## <a name="register-providers-and-features"></a>Registrera leverantörer och funktioner

`Microsoft.ContainerService AROGA`Funktionerna Feature,,, `Microsoft.Solutions` `Microsoft.Compute` `Microsoft.Storage` `Microsoft.KeyVault` och `Microsoft.Network` måste registreras i din prenumeration manuellt innan du distribuerar ditt första Azure Red Hat OpenShift-kluster.

Om du vill registrera dessa providers och funktioner manuellt kan du använda följande instruktioner från ett bash-gränssnitt om du har installerat CLI eller från Azure Cloud Shell-sessionen (bash) i Azure Portal:

1. Om du har flera Azure-prenumerationer anger du det relevanta prenumerations-ID:

    ```azurecli
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Registrera funktionen Microsoft. container service AROGA:

    ```azurecli
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Registrera Microsoft. Storage-providern:

    ```azurecli
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Registrera Microsoft. Compute-providern:

    ```azurecli
    az provider register -n Microsoft.Compute --wait
    ```

1. Registrera Microsoft. Solutions-providern:

    ```azurecli
    az provider register -n Microsoft.Solutions --wait
    ```

1. Registrera Microsoft. Network-providern:

    ```azurecli
    az provider register -n Microsoft.Network --wait
    ```

1. Registrera Microsoft. nyckel valv-providern:

    ```azurecli
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Uppdatera registreringen av Microsoft. container service-resurs leverantören:

    ```azurecli
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Skapa en Azure Active Directory-klient (Azure AD)

Tjänsten Azure Red Hat OpenShift kräver en associerad Azure Active Directory (Azure AD)-klient som representerar din organisation och dess relation till Microsoft. Med din Azure AD-klient kan du registrera, bygga och hantera appar, samt använda andra Azure-tjänster.

Om du inte har en Azure AD att använda som klient för ditt Azure Red Hat OpenShift-kluster, eller om du vill skapa en klient för testning, följer du anvisningarna i [skapa en Azure AD-klient för ditt Azure Red Hat OpenShift-kluster](howto-create-tenant.md) innan du fortsätter med den här guiden.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Skapa en Azure AD-användare, en säkerhets grupp och ett program objekt

För att kunna utföra uppgifter i klustret, till exempel att konfigurera lagring, måste du använda OpenShift i Azure Red Hat. Dessa behörigheter visas via ett [huvud namn för tjänsten](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object). Du vill också skapa en ny Active Directory användare för att testa appar som körs på ditt Azure Red Hat OpenShift-kluster.

Följ instruktionerna i [skapa ett Azure AD-App-objekt och-användare](howto-aad-app-configuration.md) för att skapa ett huvud namn för tjänsten, generera en URL för klient hemlighet och autentiserings-motanrop för din app och skapa en ny Azure AD-säkerhetsgrupp och-användare för att få åtkomst till klustret.

## <a name="next-steps"></a>Nästa steg

Nu är du redo att använda Azure Red Hat OpenShift!

Prova själv studie kursen:
> [!div class="nextstepaction"]
> [Skapa ett Azure Red Hat OpenShift-kluster](tutorial-create-cluster.md)

[azure-cli-install]: /cli/azure/install-azure-cli