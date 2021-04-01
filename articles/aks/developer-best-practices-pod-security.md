---
title: Bästa praxis för utvecklare – Pod Security i Azure Kubernetes Services (AKS)
description: Lär dig metod tips för utvecklare för att skydda poddar i Azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: zarhoads
ms.openlocfilehash: 1c7143b6d3479cf3083cfc730301c68dcf4eb705
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92900820"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Metod tips för Pod-säkerhet i Azure Kubernetes service (AKS)

När du utvecklar och kör program i Azure Kubernetes service (AKS) är säkerheten för din poddar en viktig faktor. Dina program bör vara utformade för principen om minst antal privilegier som krävs. Att skydda privata data är framför allt för kunderna. Du vill inte ha autentiseringsuppgifter, t. ex. databas anslutnings strängar, nycklar eller hemligheter och certifikat som exponeras för den utanför världen, där en angripare kan utnyttja dessa hemligheter i skadliga syfte. Lägg inte till dem i din kod eller bädda in dem i behållar avbildningarna. Den här metoden skulle skapa en risk för exponering och begränsa möjligheten att rotera dessa autentiseringsuppgifter eftersom behållar avbildningarna måste återskapas.

I den här artikeln fokuserar vi på hur du skyddar poddar i AKS. Lär dig att:

> [!div class="checklist"]
> * Använd Pod säkerhets kontext för att begränsa åtkomsten till processer och tjänster eller behörighets eskalering
> * Autentisera med andra Azure-resurser med Pod-hanterade identiteter
> * Begär och hämta autentiseringsuppgifter från ett digitalt valv som Azure Key Vault

Du kan också läsa metod tips för [kluster säkerhet][best-practices-cluster-security] och för [hantering av behållar avbildningar][best-practices-container-image-management].

## <a name="secure-pod-access-to-resources"></a>Skydda Pod åtkomst till resurser

**Rekommendationer om bästa praxis** – att köra som en annan användare eller grupp och begränsa åtkomsten till de underliggande nodernas processer och tjänster, definiera Pod säkerhets kontext inställningar. Tilldela det lägsta antalet behörigheter som krävs.

För att dina program ska kunna köras korrekt ska poddar köras som en definierad användare eller grupp och inte som *rot*. Med `securityContext` POD eller container kan du definiera inställningar som *RunAsUser* eller *fsGroup* för att anta lämpliga behörigheter. Tilldela bara de nödvändiga användar-eller grupp behörigheterna och Använd inte säkerhets kontexten som ett sätt att anta ytterligare behörigheter. *RunAsUser*, behörighets eskalering och andra inställningar för Linux-funktioner är bara tillgängliga på Linux-noder och poddar.

När du kör som en icke-rot användare kan behållare inte bindas till de privilegierade portarna under 1024. I det här scenariot kan Kubernetes-tjänster användas för att dölja det faktum att en app körs på en viss port.

En POD säkerhets kontext kan också definiera ytterligare funktioner eller behörigheter för åtkomst till processer och tjänster. Följande definitioner av vanliga säkerhets kontexter kan anges:

* **allowPrivilegeEscalation** definierar om Pod kan anta *rot* privilegier. Utforma dina program så att den här inställningen alltid är inställd på *falskt*.
* **Linux-funktioner** låter Pod komma åt underliggande noder. Ta hand om att tilldela dessa funktioner. Tilldela det lägsta antalet behörigheter som krävs. Mer information finns i [Linux-funktioner][linux-capabilities].
* **SELinux-etiketter** är en Linux-modul för kernel-säkerhet som gör att du kan definiera åtkomst principer för tjänster, processer och fil åtkomst program. Ange det lägsta antalet behörigheter som krävs. Mer information finns i [SELinux-alternativ i Kubernetes][selinux-labels]

I följande exempel Pod YAML manifest anges säkerhets kontext inställningar för att definiera:

* Pod körs som användar-ID *1000* och del av grupp-ID *2000*
* Det går inte att eskalera privilegier att använda `root`
* Ger Linux-funktioner åtkomst till nätverks gränssnitt och värdens real tids klocka (maskin vara)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    fsGroup: 2000
  containers:
    - name: security-context-demo
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      securityContext:
        runAsUser: 1000
        allowPrivilegeEscalation: false
        capabilities:
          add: ["NET_ADMIN", "SYS_TIME"]
```

Arbeta med din kluster operatör för att fastställa vilka säkerhets kontext inställningar du behöver. Försök att utforma dina program för att minimera ytterligare behörigheter och åtkomst till Pod kräver. Det finns ytterligare säkerhetsfunktioner för att begränsa åtkomsten med hjälp av AppArmor och seccomp (säker data behandling) som kan implementeras av kluster operatörer. Mer information finns i [skydda container åtkomst till resurser][apparmor-seccomp].

## <a name="limit-credential-exposure"></a>Begränsa exponering för autentiseringsuppgifter

**Vägledning för bästa praxis** – definiera inte autentiseringsuppgifter i program koden. Använd hanterade identiteter för Azure-resurser för att låta din POD begära åtkomst till andra resurser. Ett digitalt valv, till exempel Azure Key Vault, bör också användas för att lagra och hämta digitala nycklar och autentiseringsuppgifter. Pod-hanterade identiteter är endast avsedd för användning med Linux-poddar och behållar avbildningar.

Undvik att använda fasta eller delade autentiseringsuppgifter om du vill begränsa risken för att autentiseringsuppgifter exponeras i program koden. Autentiseringsuppgifter eller nycklar får inte inkluderas direkt i din kod. Om autentiseringsuppgifterna exponeras måste programmet uppdateras och omdistribueras. En bättre metod är att ge poddar sin egen identitet och sitt sätt att autentisera sig själva eller automatiskt hämta autentiseringsuppgifter från ett digitalt valv.

### <a name="use-azure-container-compute-upstream-projects"></a>Använd Azure Container Compute-projekt

> [!IMPORTANT]
> Associerade AKS-projekt med öppen källkod stöds inte av teknisk support för Azure. De tillhandahålls för användare att själv installera i kluster och samla in feedback från vår community.

Med följande [associerade AKS-projekt med öppen källkod][aks-associated-projects] kan du automatiskt autentisera poddar eller begära autentiseringsuppgifter och nycklar från ett digitalt valv. Dessa projekt underhålls av Azure Container Compute Stream-teamet och ingår i en [bredare lista med projekt som är tillgängliga för användning](https://github.com/Azure/container-compute-upstream/blob/master/README.md#support).

 * [Azure Active Directory Pod-identitet][aad-pod-identity]
 * [Azure Key Vault Provider för hemligheter Store CSI-drivrutin](https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage)

#### <a name="use-pod-managed-identities"></a>Använda Pod-hanterade identiteter

En hanterad identitet för Azure-resurser gör att en POD autentiseras mot Azure-tjänster som stöder den, till exempel Storage eller SQL. Pod tilldelas en Azure-identitet som gör att de kan autentiseras för att Azure Active Directory och ta emot en digital token. Den här digitala token kan visas för andra Azure-tjänster som kontrollerar om Pod har behörighet att komma åt tjänsten och utföra de åtgärder som krävs. Den här metoden innebär att inga hemligheter krävs för databas anslutnings strängar, till exempel. Det förenklade arbets flödet för Pod-hanterad identitet visas i följande diagram:

:::image type="content" source="media/developer-best-practices-pod-security/basic-pod-identity.svg" alt-text="Förenklat arbets flöde för Pod-hanterad identitet i Azure":::

Med en hanterad identitet behöver program koden inte innehålla autentiseringsuppgifter för att få åtkomst till en tjänst, t. ex. Azure Storage. Som varje Pod autentiseras med sin egen identitet, så att du kan granska och granska åtkomst. Om ditt program ansluter till andra Azure-tjänster kan du använda hanterade identiteter för att begränsa åter användning av autentiseringsuppgifter och risk för exponering.

Mer information om Pod-identiteter finns i [Konfigurera ett AKS-kluster för att använda Pod hanterade identiteter och med dina program][aad-pod-identity]

#### <a name="use-azure-key-vault-with-secrets-store-csi-driver"></a>Använda Azure Key Vault med hemligheter Store CSI-drivrutin

Med Pod Identity Project kan du autentisera mot stöd för Azure-tjänster. För dina egna tjänster eller program utan hanterade identiteter för Azure-resurser kan du fortfarande autentisera med hjälp av autentiseringsuppgifter eller nycklar. Ett digitalt valv kan användas för att lagra dessa hemliga innehåll.

När program behöver en autentiseringsuppgift kommunicerar de med det digitala valvet, hämtar det senaste hemliga innehållet och ansluter sedan till den tjänst som krävs. Azure Key Vault kan vara det här digitala valvet. Det förenklade arbets flödet för att hämta autentiseringsuppgifter från Azure Key Vault med Pod-hanterade identiteter visas i följande diagram:

:::image type="content" source="media/developer-best-practices-pod-security/basic-key-vault.svg" alt-text="Förenklat arbets flöde för att hämta autentiseringsuppgifter från Key Vault med en POD-hanterad identitet":::

Med Key Vault kan du lagra och regelbundet rotera hemligheter som autentiseringsuppgifter, lagrings konto nycklar eller certifikat. Du kan integrera Azure Key Vault med ett AKS-kluster med hjälp av [Azure Key Vault providern för hemligheter från CSI-drivrutinen](https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage). Med hemligheter för att lagra CSI-drivrutinen kan AKS-klustret Hämta hemligt innehåll från Key Vault och på ett säkert sätt tillhandahålla dem till den begär ande pod. Samar beta med din kluster operatör för att distribuera hemligheter för att lagra CSI-drivrutinen på AKS Worker-noder. Du kan använda en POD-hanterad identitet för att begära åtkomst till Key Vault och hämta det hemliga innehåll som behövs via filen CSI-driv rutinen för hemligheter.

Azure Key Vault med hemligheter för att lagra CSI-drivrutinen kan användas för Linux-noder och poddar som kräver en Kubernetes-version på 1,16 eller senare. För Windows-noder och poddar krävs en Kubernetes-version på 1,18 eller högre.

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserar på hur du skyddar din poddar. Information om hur du implementerar vissa av dessa områden finns i följande artiklar:

* [Använda hanterade identiteter för Azure-resurser med AKS][aad-pod-identity]
* [Integrera Azure Key Vault med AKS][aks-keyvault-csi-driver]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo
[aks-keyvault-csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#selinuxoptions-v1-core
[aks-associated-projects]: https://awesomeopensource.com/projects/aks?categoryPage=11

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources
