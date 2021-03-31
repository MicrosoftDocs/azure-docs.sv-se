---
title: Använda LetsEncrypt.org-certifikat med Application Gateway
description: Den här artikeln innehåller information om hur du hämtar ett certifikat från LetsEncrypt.org och använder det på din Application Gateway för AKS-kluster.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: df8722e8160538daa1535711092790dbb2405097
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84807036"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>Använda certifikat med LetsEncrypt.org i Application Gateway för AKS-kluster

I det här avsnittet konfigureras AKS för att utnyttja [LetsEncrypt.org](https://letsencrypt.org/) och automatiskt hämta ett TLS/SSL-certifikat för din domän. Certifikatet kommer att installeras på Application Gateway, vilket utför SSL/TLS-avslutning för ditt AKS-kluster. Installations programmet som beskrivs här använder [certifikat hanterarens](https://github.com/jetstack/cert-manager) Kubernetes-tillägg, som automatiserar skapandet och hanteringen av certifikat.

Följ stegen nedan om du vill installera [cert Manager](https://docs.cert-manager.io) på ditt befintliga AKS-kluster.

1. Helm-diagram

    Kör följande skript för att installera `cert-manager` Helm-diagrammet. Detta kommer att:

    - skapa ett nytt `cert-manager` namn område på din AKS
    - skapa följande CRDs: certifikat, Challenge, ClusterIssuer, Issuer, order
    - Installera cert-Manager-diagram (från [docs.cert-Manager.IO)](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps)

    ```bash
    #!/bin/bash

    # Install the CustomResourceDefinition resources separately
    kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

    # Create the namespace for cert-manager
    kubectl create namespace cert-manager

    # Label the cert-manager namespace to disable resource validation
    kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

    # Add the Jetstack Helm repository
    helm repo add jetstack https://charts.jetstack.io

    # Update your local Helm chart repository cache
    helm repo update

    # Install the cert-manager Helm chart
    helm install \
      --name cert-manager \
      --namespace cert-manager \
      --version v0.8.0 \
      jetstack/cert-manager
    ```

2. ClusterIssuer-resurs

    Skapa en `ClusterIssuer` resurs. Det krävs för `cert-manager` att representera den `Lets Encrypt` certifikat utfärdare där de signerade certifikaten kommer att hämtas.

    Genom att använda den icke-namnområdede `ClusterIssuer` resursen utfärdar certifikat hanteraren certifikat som kan användas från flera namn områden. `Let’s Encrypt` i används ABC-protokollet för att kontrol lera att du styr ett angivet domän namn och utfärdar ett certifikat. Mer information om hur du konfigurerar `ClusterIssuer` egenskaper finns [här](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html). `ClusterIssuer` instruerar `cert-manager` att utfärda certifikat med hjälp av den `Lets Encrypt` mellanlagrings miljö som används för testning (rot certifikatet finns inte i webbläsare/klient förtroende lager).

    Standard typen av utmaning i YAML nedan är `http01` . Andra utmaningar dokumenteras på [letsencrypt.org-utmanings typer](https://letsencrypt.org/docs/challenge-types/)

    > [!IMPORTANT] 
    > Uppdatera `<YOUR.EMAIL@ADDRESS>` i yaml nedan

    ```bash
    #!/bin/bash
    kubectl apply -f - <<EOF
    apiVersion: certmanager.k8s.io/v1alpha1
    kind: ClusterIssuer
    metadata:
    name: letsencrypt-staging
    spec:
    acme:
        # You must replace this email address with your own.
        # Let's Encrypt will use this to contact you about expiring
        # certificates, and issues related to your account.
        email: <YOUR.EMAIL@ADDRESS>
        # ACME server URL for Let’s Encrypt’s staging environment.
        # The staging environment will not issue trusted certificates but is
        # used to ensure that the verification process is working properly
        # before moving to production
        server: https://acme-staging-v02.api.letsencrypt.org/directory
        privateKeySecretRef:
        # Secret resource used to store the account's private key.
        name: example-issuer-account-key
        # Enable the HTTP-01 challenge provider
        # you prove ownership of a domain by ensuring that a particular
        # file is present at the domain
        http01: {}
    EOF
    ```

3. Distribuera App

    Skapa en ingress-resurs för att exponera `guestbook` programmet med hjälp av Application Gateway med hjälp av kryptera certifikat.

    Se till att du Application Gateway har en offentlig IP-konfiguration för klient delen med ett DNS-namn (antingen med standard `azure.com` domänen eller etablera en `Azure DNS Zone` tjänst och tilldela en egen anpassad domän).
    Observera anteckningen `certmanager.k8s.io/cluster-issuer: letsencrypt-staging` , som instruerar cert Manager att bearbeta den märkta ingress-resursen.

    > [!IMPORTANT] 
    > Uppdatera `<PLACEHOLDERS.COM>` i yaml nedan med din egen domän (eller Application Gateway en, till exempel "KH-AKS-ingress.westeurope.cloudapp.Azure.com")

    ```bash
    kubectl apply -f - <<EOF
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
    name: guestbook-letsencrypt-staging
    annotations:
        kubernetes.io/ingress.class: azure/application-gateway
        certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    spec:
    tls:
    - hosts:
        - <PLACEHOLDERS.COM>
        secretName: guestbook-secret-name
    rules:
    - host: <PLACEHOLDERS.COM>
        http:
        paths:
        - backend:
            serviceName: frontend
            servicePort: 80
    EOF
    ```

    Efter några sekunder kan du komma åt `guestbook` tjänsten via Application Gateway HTTPS-URL: en med hjälp av det automatiskt utfärdade **mellanlagrings** `Lets Encrypt` certifikatet.
    Webbläsaren kan varna dig om en ogiltig certifikat utfärdare. Det tillfälliga certifikatet utfärdas av `CN=Fake LE Intermediate X1` . Detta är en indikation på att systemet fungerade som förväntat och att du är redo för ditt produktions certifikat.

4. Produktions certifikat

    När ditt mellanlagrings certifikat har kon figurer ATS kan du växla till en produktions server för produktion på företaget:
    1. Ersätt mellanlagrings anteckningen på din ingångs resurs med: `certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. Ta bort den befintliga mellanlagringen `ClusterIssuer` som du skapade i föregående steg och skapa en ny genom att ersätta företagets server från ClusterIssuer-yaml ovan med `https://acme-v02.api.letsencrypt.org/directory`

5. Certifikatets giltighets tid och förnyelse

    Innan `Lets Encrypt` certifikatet upphör att gälla `cert-manager` uppdateras certifikatet automatiskt i Kubernetes Secret Store. Vid det här tillfället kommer Application Gateway ingångs kontroll enhet att tillämpa den uppdaterade hemlighet som refereras i de ingående resurserna som används för att konfigurera Application Gateway.
