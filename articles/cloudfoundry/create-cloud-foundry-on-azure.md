---
title: Skapa ett Pivotal Cloud Foundry-kluster i Azure
description: Lär dig att konfigurera de parametrar som behövs för att etablera ett Pivotal Cloud Foundry-kluster (PCF) i Azure
services: Cloud Foundry
documentationcenter: CloudFoundry
author: ruyakubu
manager: brunoborges
editor: ruyakubu
ms.assetid: ''
ms.author: ruyakubu
ms.date: 09/13/2018
ms.devlang: ''
ms.service: azure
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: 3444d6c62b5e36b7e68cfaf6da1ec534e2ea4ec6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102551461"
---
# <a name="create-a-pivotal-cloud-foundry-cluster-on-azure"></a>Skapa ett Pivotal Cloud Foundry-kluster i Azure

I den här självstudien finns genvägar till att skapa och generera de parametrar som behövs för att etablera ett Pivotal Cloud Foundry-kluster i Azure. Du kan söka efter Pivotal Cloud Foundry-lösningen på Azure [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

![Söka efter Pivotal Cloud Foundry i Azure](media/deploy/pcf-marketplace.png)


## <a name="generate-an-ssh-public-key"></a>Generera en offentlig SSH-nyckel

Det finns flera sätt att generera en offentlig SSH-nyckel (Secure Shell) med hjälp av Windows, Mac eller Linux.

```Bash
ssh-keygen -t rsa -b 2048
```

Mer information finns i [Använda SSH-nycklar med Windows i Azure](../virtual-machines/linux/ssh-from-windows.md).

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

> [!NOTE]
>
> Du behöver behörighet till ägarkontot för att kunna skapa tjänstens huvudnamn. Du kan också skriva ett skript som automatiserar skapandet av tjänstens huvudnamn. Till exempel kan du använda Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp).

1. Logga in på ditt Azure-konto.

    ```azurecli
    az login
    ```

    ![Azure CLI-inloggning](media/deploy/az-login-output.png )
 
    Kopiera värdet ”id” som **prenumerations-ID** och kopiera värdet ”tenantId”, som vi ska använda senare.

2. Ange din standardprenumeration för den här konfigurationen.

    ```azurecli
    az account set -s {id}
    ```

3. Skapa ett Azure Active Directory-program för din PCF. Ange ett unikt alfanumeriskt lösenord. Lagra lösenordet som **clientSecret**, vilket vi ska använda senare.

    ```azurecli
    az ad app create --display-name "Svc Principal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}
    ```

    Kopiera sedan värdet ”appId” i utdatan som **clientID**, vilket vi ska använda senare.

    > [!NOTE]
    >
    > Välj din egen program start sida och ID-URI, till exempel http \: //www \. contoso.com.

4. Skapa ett huvudnamn för tjänsten med ditt nya app-ID.

    ```azurecli
    az ad sp create --id {appId}
    ```

5. Ange behörighetsrollen för tjänstens huvudnamn som Deltagare.

    ```azurecli
    az role assignment create --assignee "{enter-your-homepage}" --role "Contributor"
    ```

    Du kan också använda

    ```azurecli
    az role assignment create --assignee {service-principal-name} --role "Contributor"
    ```

    ![Rolltilldelning för tjänstens huvudnamn](media/deploy/svc-princ.png )

6. Kontrollera att du kan logga in på tjänstens huvudnamn med app-ID, lösenord och klient-ID.

    ```azurecli
    az login --service-principal -u {appId} -p {your-password}  --tenant {tenantId}
    ```

7. Skapa en .json-fil i nedanstående format. Använd de värden för **prenumerations-ID**, **tenantID**, **clientID** och **clientSecret** som du kopierade tidigare. Spara filen.

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>Hämta Pivotal Network-token

1. Registrera dig eller logga in på ditt [Pivotal Network](https://network.pivotal.io)-konto.
2. Välj ditt profilnamn i det övre högra hörnet på sidan. Välj **Redigera profil**.
3. Rulla längst ned på sidan och kopiera värdet **LEGACY API TOKEN**. Det här är ditt värde för **Pivotal Network-token** som du kommer att använda senare.

## <a name="provision-your-cloud-foundry-cluster-on-azure"></a>Etablera Cloud Foundry-klustret i Azure

Nu har du alla parametrar som behövs för att etablera ditt [Pivotal Cloud Foundry-kluster i Azure](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).
Ange parametrarna och skapa ditt PCF-kluster.

## <a name="verify-the-deployment-and-sign-in-to-the-pivotal-ops-manager"></a>Kontrollera distributionen och logga in på Pivotal Ops Manager

1. PCF-klustret visar en distributionsstatus.

    ![Azures distributionsstatus](media/deploy/deployment.png )

2. Välj länken **Distributioner** i navigeringen till vänster för att hämta autentiseringsuppgifter för din PCF Ops Manager. Välj **Distributionsnamn** på nästa sida.
3. I den vänstra navigeringen väljer du länken **Utdata** för att visa URL, användarnamn och lösenord för PCF Ops Manager. ”OPSMAN-FQDN”-värdet är URL:en.
 
    ![Cloud Foundry-distributionsutdata](media/deploy/deploy-outputs.png )
 
4. Starta URL:en i en webbläsare. Ange autentiseringsuppgifterna från föregående steg för att logga in.

    ![Inloggningssida för Pivotal](media/deploy/pivotal-login.png )
         
    > [!NOTE]
    >
    > Om åtgärden misslyckas i Internet Explorer och ett varningsmeddelande visas om att webbplatsen inte är säker, väljer du **Mer information** och går till webbsidan. I Firefox väljer du **Fortsätt** och lägger till certifieringen för att fortsätta.

5. PCF Ops Manager visar de distribuerade Azure-instanserna. Nu kan du skapa och hantera dina program här.
               
    ![Distribuerad Azure-instans i Pivotal](media/deploy/ops-mgr.png )
