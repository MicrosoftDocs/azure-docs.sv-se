---
title: OpenShift container Platform 3,11 i Azure-förutsättningar
description: Krav för att distribuera OpenShift container Platform 3,11 i Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/23/2019
ms.author: haroldw
ms.openlocfilehash: 645402d6c6b1fe744938ef5597098f46bc80c78e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101672370"
---
# <a name="common-prerequisites-for-deploying-openshift-container-platform-311-in-azure"></a>Vanliga krav för distribution av OpenShift container Platform 3,11 i Azure

I den här artikeln beskrivs vanliga krav för att distribuera OpenShift container Platform eller OKD i Azure.

Vid installationen av OpenShift används Ansible spel böcker. Ansible använder Secure Shell (SSH) för att ansluta till alla kluster värdar för att slutföra installations stegen.

När Ansible gör SSH-anslutningen till fjärranslutna värdar kan den inte ange ett lösen ord. Av den anledningen kan den privata nyckeln inte ha ett lösen ord (lösen fras) som är kopplat till den eller distributionen Miss lyckas.

Eftersom de virtuella datorerna (VM) distribuerar via Azure Resource Manager mallar, används samma offentliga nyckel för åtkomst till alla virtuella datorer. Motsvarande privata nyckel måste finnas på den virtuella datorn som kör alla spel böcker också. För att utföra den här åtgärden på ett säkert sätt används ett Azure Key Vault för att skicka den privata nyckeln till den virtuella datorn.

Om det finns ett behov av beständiga lagrings enheter för behållare krävs permanenta volymer. OpenShift stöder Azure virtuella hård diskar (VHD) för permanenta volymer, men Azure måste först konfigureras som moln leverantör.

I den här modellen, OpenShift:

- Skapar ett VHD-objekt i ett Azure Storage-konto eller en hanterad disk.
- Monterar den virtuella hård disken till en virtuell dator och formaterar volymen.
- Monterar volymen till pod.

För att den här konfigurationen ska fungera behöver OpenShift behörighet att utföra dessa uppgifter i Azure. Ett huvud namn för tjänsten används för det här ändamålet. Tjänstens huvud namn är ett säkerhets konto i Azure Active Directory som beviljas behörigheter till resurser.

Tjänstens huvud namn måste ha åtkomst till de lagrings konton och virtuella datorer som utgör klustret. Om alla kluster resurser för OpenShift distribuerar till en enda resurs grupp, kan tjänstens huvud namn beviljas behörigheter till den resurs gruppen.

I den här guiden beskrivs hur du skapar artefakter som är associerade med förutsättningarna.

> [!div class="checklist"]
> * Skapa ett nyckel valv för att hantera SSH-nycklar för OpenShift-klustret.
> * Skapa ett tjänst huvud namn för användning av Azure Cloud Provider.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure 
Logga in på din Azure-prenumeration med kommandot [AZ login](/cli/azure/reference-index) och följ anvisningarna på skärmen, eller klicka på **prova** att använda Cloud Shell.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Du bör använda en dedikerad resurs grupp som värd för nyckel valvet. Den här gruppen är separat från resurs gruppen som kluster resurserna för OpenShift distribuerar till.

I följande exempel skapas en resurs grupp med namnet *keyvaultrg* på platsen för *öster* :

```azurecli
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv
Skapa ett nyckel valv för att lagra SSH-nycklarna för klustret med kommandot [AZ-valv](/cli/azure/keyvault) för att skapa. Nyckel valvets namn måste vara globalt unikt och måste vara aktiverat för mall distribution eller så Miss söker distributionen "KeyVaultParameterReferenceSecretRetrieveFailed"-fel.

I följande exempel skapas ett nyckel valv med namnet Key *Vault* i *keyvaultrg* -resurs gruppen:

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Skapa en SSH-nyckel 
En SSH-nyckel krävs för att skydda åtkomsten till OpenShift-klustret. Skapa ett SSH-nyckelpar med `ssh-keygen` kommandot (på Linux eller MacOS):
 
```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> Ditt SSH-nyckelpar får inte ha ett lösen ord/en lösen fras.

Mer information om SSH-nycklar i Windows finns i [så här skapar du SSH-nycklar i Windows](./ssh-from-windows.md). Se till att exportera den privata nyckeln i OpenSSH-format.

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Lagra den privata SSH-nyckeln i Azure Key Vault
I OpenShift-distributionen används SSH-nyckeln som du skapade för att skydda åtkomsten till OpenShift-huvudnoden. Om du vill att distributionen ska kunna hämta SSH-nyckeln på ett säkert sätt lagrar du nyckeln i Key Vault med hjälp av följande kommando:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten 
OpenShift kommunicerar med Azure med hjälp av ett användar namn och lösen ord eller ett huvud namn för tjänsten. Ett huvud namn för Azure-tjänsten är en säkerhets identitet som du kan använda med appar, tjänster och automatiserings verktyg som OpenShift. Du styr och definierar behörigheterna för vilka åtgärder som tjänstens huvud namn kan utföra i Azure. Det är bäst att begränsa behörigheten för tjänstens huvud namn till vissa resurs grupper i stället för hela prenumerationen.

Skapa ett huvud namn för tjänsten med [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp) och generera de autentiseringsuppgifter som OpenShift behöver.

I följande exempel skapas ett huvud namn för tjänsten som tilldelar den behörighet till en resurs grupp med namnet *openshiftrg*.

Skapa först resurs gruppen med namnet *openshiftrg*:

```azurecli
az group create -l eastus -n openshiftrg
```

Skapa tjänstens huvud namn:

```azurecli
az group show --name openshiftrg --query id
```

Spara utdata från kommandot och Använd i stället för $scope i nästa kommando

```azurecli
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --scopes $scope \
```

Anteckna den appId egenskapen och det lösen ord som returnerades från kommandot:

```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```

 > [!WARNING] 
 > Se till att skriva ned det säkra lösen ordet eftersom det inte går att hämta det här lösen ordet igen.

Mer information om tjänstens huvud namn finns i [skapa ett Azure-tjänstens huvud namn med Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>Krav som endast gäller för Resource Manager-mall

Hemligheter måste skapas för SSH privat nyckel (**sshPrivateKey**), Azure AD client Secret (**aadClientSecret**), OpenShift Admin Password (**OpenshiftPassword**) och Red Hat Subscription Manager Password eller aktiverings nyckel (**rhsmPasswordOrActivationKey**).  Om anpassade TLS/SSL-certifikat används måste dessutom sex ytterligare hemligheter skapas – **routingcafile**, **routingcertfile**, **routingkeyfile**, **mastercafile**, **mastercertfile** och **masterkeyfile**.  Dessa parametrar förklaras i detalj.

Mallen refererar till vissa hemliga namn, så du **måste** använda de fetstilta namnen som anges ovan (Skift läges känsligt).

### <a name="custom-certificates"></a>Anpassade certifikat

Som standard distribuerar mallen ett OpenShift-kluster med självsignerade certifikat för webb konsolen OpenShift och routningsdomänen. Om du vill använda anpassade TLS/SSL-certifikat anger du ' routingCertType ' till ' Custom ' och ' masterCertType ' till ' Custom '.  Du behöver certifikat utfärdare, certifikat och nyckel filer i. pem-format för certifikaten.  Det går att använda anpassade certifikat för en men inte för det andra.

Du måste lagra de här filerna i Key Vault hemligheter.  Använd samma Key Vault som det som används för den privata nyckeln.  I stället för att kräva ytterligare ytterligare indata för de hemliga namnen, hårdkodas mallen för att använda vissa hemliga namn för var och en av TLS/SSL-certifikatets filer.  Lagra certifikat data med hjälp av informationen i följande tabell.

| Hemligt namn      | Certifikatfil   |
|------------------|--------------------|
| mastercafile     | huvud-CA-fil     |
| mastercertfile   | huvud certifikat fil   |
| masterkeyfile    | huvud nyckel fil    |
| routingcafile    | Routning CA-fil    |
| routingcertfile  | cirkulerar certifikat fil  |
| routingkeyfile   | Nyckel fil för routning   |

Skapa hemligheterna med hjälp av Azure CLI. Nedan visas ett exempel.

```azurecli
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs följande avsnitt:
> [!div class="checklist"]
> * Skapa ett nyckel valv för att hantera SSH-nycklar för OpenShift-klustret.
> * Skapa ett tjänst huvud namn för användning av Azure Cloud Solution Provider.

Distribuera sedan ett OpenShift-kluster:

- [Distribuera OpenShift container Platform](./openshift-container-platform-3x.md)
- [Distribuera OpenShift container Platform Self-Managed Marketplace-erbjudande](./openshift-container-platform-3x-marketplace-self-managed.md)
