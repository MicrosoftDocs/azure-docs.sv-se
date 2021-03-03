---
title: OpenShift container Platform 3,11 i Azure post-Deployment-uppgifter
description: Ytterligare aktiviteter för efter ett OpenShift container Platform 3,11-kluster har distribuerats.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.custom: devx-track-ansible, devx-track-azurecli
ms.openlocfilehash: c3f9aaa15a697202aa76c563ed62bf37443d69ec
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669395"
---
# <a name="post-deployment-tasks"></a>Uppgifter efter distribution

När du har distribuerat ett OpenShift-kluster kan du konfigurera ytterligare objekt. Den här artikeln beskriver:

- Så här konfigurerar du enkel inloggning med hjälp av Azure Active Directory (Azure AD)
- Konfigurera Azure Monitor loggar för att övervaka OpenShift
- Konfigurera mått och loggning
- Så här installerar du Open Service Broker för Azure (OSBA)

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Konfigurera enkel inloggning med hjälp av Azure Active Directory

Om du vill använda Azure Active Directory för autentisering måste du först skapa en Azure AD App-registrering. Den här processen omfattar två steg: att skapa appens registrering och konfigurera behörigheter.

### <a name="create-an-app-registration"></a>Skapa en appregistrering

De här stegen använder Azure CLI för att skapa appens registrering och GUI (portal) för att ange behörigheterna. För att kunna skapa registrerings appen behöver du följande fem delar av informationen:

- Visnings namn: registrerings namn för app (till exempel OCPAzureAD)
- Start sida: OpenShift-konsolens URL (till exempel `https://masterdns343khhde.westus.cloudapp.azure.com/console` )
- ID-URI: OpenShift-konsolens URL (t. ex. `https://masterdns343khhde.westus.cloudapp.azure.com/console` )
- Svars-URL: offentlig huvud webb adress och registrerings namn för appen (till exempel `https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD` )
- Lösen ord: säkert lösen ord (Använd ett starkt lösen ord)

I följande exempel skapas en app-registrering med hjälp av föregående information:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com/console --password {Strong Password}
```

Om kommandot lyckas får du en JSON-utdata som liknar:

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD"
  ]
}
```

Anteckna den appId-egenskap som returnerades från kommandot för ett senare steg.

I Azure-portalen:

1. Välj **Azure Active Directory**  >  **app-registrering**.
2. Sök efter din registrering av appen (till exempel OCPAzureAD).
3. Klicka på appens registrering i resultatet.
4. Under **Inställningar** väljer du **nödvändiga behörigheter**.
5. Under **nödvändiga behörigheter** väljer du **Lägg till**.

   ![Registrera appar](media/openshift-post-deployment/app-registration.png)

6. Klicka på steg 1: Välj API och klicka sedan på **Windows Azure Active Directory (Microsoft. Azure. ActiveDirectory)**. Klicka på **Välj** längst ned.

   ![Välj API för app-registrering](media/openshift-post-deployment/app-registration-select-api.png)

7. I steg 2: Välj behörigheter väljer du **Logga in och Läs användar profil** under **delegerade behörigheter** och klickar sedan på **Välj**.

   ![Åtkomst till app-registrering](media/openshift-post-deployment/app-registration-access.png)

8. Välj **Klar**.

### <a name="configure-openshift-for-azure-ad-authentication"></a>Konfigurera OpenShift för Azure AD-autentisering

Om du vill konfigurera OpenShift för att använda Azure AD som autentiseringsprovider måste/etc/Origin/Master/Master-config.yaml-filen redige ras på alla huvudnoder.

Hitta klient-ID: t med hjälp av följande CLI-kommando:

```azurecli
az account show
```

Leta upp följande rader i yaml-filen:

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com/console/
  grantConfig:
    method: auto
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: htpasswd_auth
    provider:
      apiVersion: v1
      file: /etc/origin/master/htpasswd
      kind: HTPasswdPasswordIdentityProvider
```

Infoga följande rader direkt efter föregående rader:

```yaml
  - name: <App Registration Name>
    challenge: false
    login: true
    mappingMethod: claim
    provider:
      apiVersion: v1
      kind: OpenIDIdentityProvider
      clientID: <appId>
      clientSecret: <Strong Password>
      claims:
        id:
        - sub
        preferredUsername:
        - unique_name
        name:
        - name
        email:
        - email
      urls:
        authorize: https://login.microsoftonline.com/<tenant Id>/oauth2/authorize
        token: https://login.microsoftonline.com/<tenant Id>/oauth2/token
```

Se till att texten justeras korrekt under Identityprovider. Hitta klient-ID: t med hjälp av följande CLI-kommando: ```az account show```

Starta om OpenShift Master-tjänsterna på alla huvudnoder:

```bash
sudo /usr/local/bin/master-restart api
sudo /usr/local/bin/master-restart controllers
```

I OpenShift-konsolen visas nu två alternativ för autentisering: htpasswd_auth och [registrering av appar].

## <a name="monitor-openshift-with-azure-monitor-logs"></a>Övervaka OpenShift med Azure Monitor loggar

Det finns tre sätt att lägga till Log Analytics agent i OpenShift.
- Installera Log Analytics agent för Linux direkt på varje OpenShift-nod
- Aktivera Azure Monitor VM-tillägg på varje OpenShift-nod
- Installera Log Analytics agenten som OpenShift daemon-uppsättning

Läs fullständiga [instruktioner](../../azure-monitor/containers/containers.md#configure-a-log-analytics-agent-for-red-hat-openshift) om du vill ha mer information.

## <a name="configure-metrics-and-logging"></a>Konfigurera mått och loggning

Baserat på grenen kan Azure Resource Manager mallar för OpenShift container Platform och OKD tillhandahålla indataparametrar för att aktivera mått och loggning som en del av installationen.

Marketplace-erbjudandet för OpenShift container Platform är också ett alternativ för att aktivera mått och loggning under kluster installationen.

Om mått/loggning inte har Aktiver ATS under installationen av klustret, kan de enkelt aktive ras efter faktumet.

### <a name="azure-cloud-provider-in-use"></a>Azure Cloud Provider används

SSH till skydds-noden eller den första huvudnoden (baserat på mall och gren som används) med hjälp av de autentiseringsuppgifter som angavs under distributionen. Utfärda följande kommando:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>Azure Cloud Provider används inte

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>Installera Open Service Broker for Azure (OSBA)

Öppna Service Broker för Azure eller OSBA, så att du kan etablera Azure-Cloud Services direkt från OpenShift. OSBA i en öppen Service Broker API-implementering för Azure. Den öppna Service Broker-API: n är en specifikation som definierar ett gemensamt språk för moln leverantörer som kan användas av molnbaserade program för att hantera moln tjänster utan att låsa.

Om du vill installera OSBA i OpenShift följer du instruktionerna som finns här: https://github.com/Azure/open-service-broker-azure#openshift-project-template . 
> [!NOTE]
> Slutför bara stegen i avsnittet OpenShift-projektmall och inte hela avsnittet Installera.

## <a name="next-steps"></a>Nästa steg

- [Kom igång med OpenShift container Platform](https://docs.openshift.com)
