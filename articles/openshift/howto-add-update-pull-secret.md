---
title: Lägg till eller uppdatera din Red Hat pull-hemlighet i ett Azure Red Hat OpenShift 4-kluster
description: Lägg till eller uppdatera din Red Hat pull-hemlighet på befintliga 4. x ARO-kluster
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 05/21/2020
keywords: pull-hemlighet, Aro, OpenShift, Red Hat
ms.openlocfilehash: b1a8e45a2eab1b7b4c8a5936049f404d5bd3441c
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071094"
---
# <a name="add-or-update-your-red-hat-pull-secret-on-an-azure-red-hat-openshift-4-cluster"></a>Lägg till eller uppdatera din Red Hat pull-hemlighet i ett Azure Red Hat OpenShift 4-kluster

Den här guiden beskriver hur du lägger till eller uppdaterar din Red Hat pull-hemlighet för ett befintligt Azure Red Hat OpenShift (ARO) 4. x-kluster.

Om du skapar ett kluster för första gången kan du lägga till din pull-hemlighet när du skapar klustret. Mer information om hur du skapar ett ARO-kluster med Red Hat pull-hemlighet finns i [skapa ett Azure Red Hat OpenShift 4-kluster](tutorial-create-cluster.md#get-a-red-hat-pull-secret-optional).

## <a name="before-you-begin"></a>Innan du börjar

I den här hand boken förutsätter vi att du har ett befintligt Azure Red Hat OpenShift 4-kluster. Se till att du har administratörs åtkomst till klustret.

## <a name="prepare-your-pull-secret"></a>Förbered din pull-hemlighet
När du skapar ett ARO-kluster utan att lägga till en Red Hat pull-hemlighet, skapas en pull-hemlighet fortfarande på klustret automatiskt. Den här hämtnings hemligheten är dock inte fullständigt ifylld.

Det här avsnittet beskriver hur du uppdaterar en pull-hemlighet med ytterligare värden från Red Hat pull-hemlighet.

1. Hämta hemligheten som heter `pull-secret` i `openshift-config` namn området och spara den i en separat fil genom att köra följande kommando: 

    ```console
    oc get secrets pull-secret -n openshift-config -o template='{{index .data ".dockerconfigjson"}}' | base64 -d > pull-secret.json
    ```

    Dina utdata bör likna följande. (Observera att det faktiska hemliga värdet har tagits bort.)

    ```json
    {
        "auths": {
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

2. Gå till [Red Hat OpenShift Cluster Manager-portalen](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) och välj **Hämta pull-hemlighet**. Din Red Hat pull-hemlighet kommer att se ut så här. (Observera att de faktiska hemliga värdena har tagits bort.)

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            }
        }
    }
    ```

3. Redigera den pull-hemliga fil som du fick från klustret genom att lägga till i poster som finns i din Red Hat pull-hemlighet. 

    > [!IMPORTANT]
    > Om du inkluderar `cloud.openshift.com` posten från din Red Hat pull-hemlighet kommer klustret att börja skicka telemetridata till Red Hat. Inkludera bara det här avsnittet om du vill skicka telemetridata. Annars lämnar du följande avsnitt.    
    > ```json
    > {
    >         "cloud.openshift.com": {
    >             "auth": "<my-crc-secret>",
    >             "email": "klamenzo@redhat.com"
    >         }
    > ```

    > [!CAUTION]
    > Ta inte bort eller ändra du är `arosvc.azurecr.io` posten från din pull-hemlighet. Det här avsnittet behövs för att klustret ska fungera korrekt.

    ```json
    "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
    ```

    Den slutgiltiga filen bör se ut ungefär så här. (Observera att de faktiska hemliga värdena har tagits bort.)

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            },
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

4. Kontrol lera att filen är en giltig JSON-fil. Det finns många sätt att verifiera din JSON. I följande exempel används JQ:

    ```json
    cat pull-secret.json | jq
    ```

    > [!NOTE]
    > Om det finns ett fel i filen visas det som `parse error` .

## <a name="add-your-pull-secret-to-your-cluster"></a>Lägg till din pull-hemlighet till klustret

Kör följande kommando för att uppdatera din pull-hemlighet.

> [!NOTE]
> Genom att köra det här kommandot kommer klusternoderna att starta om en och en när de uppdateras. 

```console
oc set data secret/pull-secret -n openshift-config --from-file=.dockerconfigjson=./pull-secret.json
```

När hemligheten har angetts är du redo att aktivera Red Hat Certified-operatörer.

### <a name="modify-the-configuration-files"></a>Ändra konfigurationsfilerna

Ändra följande objekt för att aktivera Red Hat-operatörer.

Ändra först konfigurations filen exempel operatör. Sedan kan du köra följande kommando för att redigera konfigurations filen:

```
oc edit configs.samples.operator.openshift.io/cluster -o yaml
```

Ändra `spec.architectures.managementState` värdena och `status.architecture.managementState` från `Removed` till `Managed` . 

Följande YAML-kodfragment visar bara relevanta avsnitt i den redigerade YAML-filen:

```yaml
apiVersion: samples.operator.openshift.io/v1
kind: Config
metadata:
  
  ...
  
spec:
  architectures:
  - x86_64
  managementState: Managed
status:
  architectures:

  ...

  managementState: Managed
  version: 4.3.27
```

Kör sedan följande kommando för att redigera konfigurations filen för operatörs hubben:  

```console
oc edit operatorhub cluster -o yaml
```

Ändra `Spec.Sources.Disabled` värdena och `Status.Sources.Disabled` från `true` till `false` för alla källor som du vill aktivera.

Följande YAML-kodfragment visar bara relevanta avsnitt i den redigerade YAML-filen:

```yaml
Name:         cluster

...
                 dd3310b9-e520-4a85-98e5-8b4779ee0f61
Spec:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Disabled:  false
    Name:      redhat-operators
Status:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Status:    Success
    Disabled:  false
    Name:      community-operators
    Status:    Success
    Disabled:  false
    Name:      redhat-operators
    Status:    Success
Events:        <none>
```

Spara filen om du vill använda dina ändringar.

## <a name="validate-that-your-secret-is-working"></a>Verifiera att din hemlighet fungerar

När du har lagt till din pull-hemlighet och ändrat rätt konfigurationsfiler kan klustret ta flera minuter att uppdatera. Kontrol lera att klustret har uppdaterats genom att köra följande kommando för att Visa källor med certifierade operatörer och Red Hat-operatörer tillgängliga:

```console
$ oc get catalogsource -A
NAMESPACE               NAME                  DISPLAY               TYPE   PUBLISHER   AGE
openshift-marketplace   certified-operators   Certified Operators   grpc   Red Hat     10s
openshift-marketplace   community-operators   Community Operators   grpc   Red Hat     18h
openshift-marketplace   redhat-operators      Red Hat Operators     grpc   Red Hat     11s
```

Om du inte ser de certifierade operatörerna och Red Hat-operatörerna väntar du några minuter och försöker igen.

För att se till att din pull-hemlighet har uppdaterats och fungerar som den ska öppnar du OperatorHub och kontrollerar om det finns en Red Hat-verifierad operator. Kontrol lera till exempel om du vill se om lagrings operatorn OpenShift container är tillgänglig och se om du har behörighet att installera.

## <a name="next-steps"></a>Nästa steg
Läs mer om Red Hat pull-hemligheter i [använda image-pull-hemligheter](https://docs.openshift.com/container-platform/4.6/openshift_images/managing_images/using-image-pull-secrets.html).

Mer information om Red Hat OpenShift 4 finns i [Red Hat OpenShift container Platform-dokumentation](https://docs.openshift.com/container-platform/4.6/welcome/index.html).
