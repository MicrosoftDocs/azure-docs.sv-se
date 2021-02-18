---
title: Kör privilegierade behållare i ett kluster med OpenShift i Azure Red Hat | Microsoft Docs
description: Kör privilegierade behållare för att övervaka säkerhet och efterlevnad.
author: makdaam
ms.author: b-lejaku
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 12/05/2019
keywords: Aro, OpenShift, aquasec, twistlock, Red Hat
ms.openlocfilehash: 77e75232b7f9d23f1d07931cc7dc231174e1312d
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633570"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>Köra privilegierade containers i ett Azure Red Hat OpenShift-kluster

> [!IMPORTANT]
> Azure Red Hat OpenShift 3,11 kommer att dras tillbaka 30 juni 2022. Stöd för att skapa nya Azure Red Hat OpenShift 3,11-kluster fortsätter till och med 30 november 2020. Efter pensionering kommer de återstående Azure Red Hat OpenShift 3,11-klustren att stängas av för att förhindra säkerhets problem.
> 
> Följ den här guiden för att [skapa ett Azure Red Hat OpenShift 4-kluster](tutorial-create-cluster.md).
> Om du har frågor kan du [kontakta oss](mailto:arofeedback@microsoft.com).

Du kan inte köra godtyckliga privilegierade behållare i Azure Red Hat OpenShift-kluster.
Två lösningar för säkerhetsövervakning och efterlevnad får köras på ARO-kluster.
I det här dokumentet beskrivs skillnaderna i dokumentationen för den allmänna OpenShift-distributionen av säkerhets produkt leverantörer.


Läs igenom dessa instruktioner innan du följer leverantörens instruktioner.
Avsnitts rubriker i produktspecifika steg hänvisas direkt till avsnitts rubriker i leverantörens dokumentation.

## <a name="before-you-begin"></a>Innan du börjar

Dokumentationen för de flesta säkerhets produkter förutsätter att du har administratörs behörighet för klustret.
Kund administratörer har inte alla behörigheter i Azure Red Hat OpenShift. Behörigheter som krävs för att ändra resurser i hela klustret är begränsade.

Se först till att användaren är inloggad i klustret som en kund administratör genom att köra `oc get scc` . Alla användare som är medlemmar i kund administratörs gruppen har behörighet att Visa säkerhets kontext begränsningar (SCCs) i klustret.

Se sedan till att den `oc` binära versionen är `3.11.154` .
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>Produktspecifika steg för turkos säkerhet
Bas instruktionerna som ska ändras finns i dokumentationen för den nya [säkerhets distributionen](https://docs.aquasec.com/docs/openshift-red-hat). Stegen här kommer att köras tillsammans med dokumentationen för turkos-distribution.

Det första steget är att kommentera de nödvändiga SCCs som kommer att uppdateras. De här anteckningarna förhindrar klustrets Sync-Pod från att återställa ändringar i dessa SSCs.

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>Steg 1: Förbered krav
Kom ihåg att logga in på klustret som en ARO-kundadministratör i stället för rollen kluster-admin.

Skapa projektet och tjänst kontot.
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

I stället för att tilldela rollen kluster läsare tilldelar du rollen kund administratör-kluster till turkos-kontot med följande kommando.
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

Fortsätt följa återstående instruktioner i steg 1.  I dessa instruktioner beskrivs hur du konfigurerar hemligheten för det turkosa registret.

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>Steg 2: Distribuera turkos-servern,-databasen och-gatewayen
Följ anvisningarna i den medföljande dokumentationen för att installera turkos-konsolen. yaml.

Ändra den angivna `aqua-console.yaml` .  Ta bort de två översta objekten med etiketten `kind: ClusterRole` och `kind: ClusterRoleBinding` .  De här resurserna skapas inte eftersom kundens administratör inte har behörighet för det här tillfället för att ändra `ClusterRole` och `ClusterRoleBinding` objekt.

Den andra ändringen kommer till den `kind: Route` del av `aqua-console.yaml` . Ersätt följande yaml för `kind: Route` objektet i `aqua-console.yaml` filen.
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    app: aqua-web
  name: aqua-web
  namespace: aqua-security
spec:
  port:
    targetPort: aqua-web
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: aqua-web
    weight: 100
  wildcardPolicy: None
```

Följ återstående instruktioner.

### <a name="step-3-login-to-the-aqua-server"></a>Steg 3: Logga in på den turkosa servern
Det här avsnittet ändras inte på något sätt.  Följ den medföljande dokumentationen.

Använd följande kommando för att hämta den turkos konsol adressen.
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>Steg 4: Distribuera turkosa genomdrivor
Ange följande fält vid distribution av Genomdrivare:

| Fält          | Värde         |
| -------------- | ------------- |
| Orchestrator   | OpenShift     |
| ServiceAccount | turkos – konto  |
| Project        | turkos – säkerhet |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>Produktspecifika steg för Prisma Cloud/twistlock

De grundläggande anvisningar som vi ska ändra finns i [dokumentationen för Prisma Cloud Deployment](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html)

Börja med att installera `twistcli` verktyget enligt beskrivningen i avsnitten "installera Prisma Cloud" och "Hämta moln program varan för Prisma".

Skapa ett nytt OpenShift-projekt
```
oc new-project twistlock
```

Hoppa över det valfria avsnittet "push The Prisma Cloud images to a Private Registry". Den fungerar inte i Azure Red Hat OpenShift. Använd online-registret i stället.

Du kan följa den officiella dokumentationen när du använder korrigeringarna som beskrivs nedan.
Börja med avsnittet "installera konsol".

### <a name="install-console"></a>Installera konsol

`oc create -f twistlock_console.yaml`I steg 2 får du ett fel när du skapar namn området.
Du kan ignorera det, namn området har skapats tidigare med `oc new-project` kommandot.

Används `azure-disk` för lagrings typ.

### <a name="create-an-external-route-to-console"></a>Skapa en extern väg till konsolen

Du kan antingen följa dokumentationen eller anvisningarna nedan om du föredrar kommandot oc.
Kopiera följande flödes definition till en fil med namnet twistlock_route. yaml på datorn
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    name: console
  name: twistlock-console
  namespace: twistlock
spec:
  port:
    targetPort: mgmt-http
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: twistlock-console
    weight: 100
  wildcardPolicy: None
```
kör sedan:
```
oc create -f twistlock_route.yaml
```

Du kan hämta den URL som tilldelats twistlock-konsolen med det här kommandot: `oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>Konfigurera konsol

Följ twistlock-dokumentationen.

### <a name="install-defender"></a>Installera Defender

`oc create -f defender.yaml`I steg 2 får du fel när du skapar kluster rollen och kluster roll bindningen.
Du kan ignorera dem.

Skydds bara distributioner kommer att distribueras på Compute-noder. Du behöver inte begränsa dem med en Node-selektor.
