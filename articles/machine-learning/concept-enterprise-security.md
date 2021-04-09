---
title: Företagssäkerhet och styrning
titleSuffix: Azure Machine Learning
description: 'Använd Azure Machine Learning på ett säkert sätt: autentisering, auktorisering, nätverks säkerhet, data kryptering och övervakning.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/20/2020
ms.openlocfilehash: a079504872eaf3840416a99e784c4d33a6828b0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "94992037"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Företags säkerhet och styrning för Azure Machine Learning

I den här artikeln får du lära dig mer om säkerhets-och styrnings funktioner som är tillgängliga för Azure Machine Learning. Dessa funktioner är användbara för administratörer, DevOps och MLOps som vill skapa en säker konfiguration som är kompatibel med företagets principer. Med Azure Machine Learning och Azure-plattformen kan du:

* Begränsa åtkomsten till resurser och åtgärder med användar konton eller grupper
* Begränsa inkommande och utgående nätverkskommunikation
* Kryptera data under överföring och vila
* Sök efter sårbarheter
* Tillämpa och granska konfigurations principer

## <a name="restrict-access-to-resources-and-operations"></a>Begränsa åtkomsten till resurser och åtgärder

[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) är identitets tjänst leverantören för Azure Machine Learning. Du kan skapa och hantera säkerhets objekt (användare, grupp, tjänstens huvud namn och hanterad identitet) som används för att _autentisera_ till Azure-resurser. Multi-Factor Authentication stöds om Azure AD har kon figurer ATS för att använda den.

Här är autentiseringsprocessen för Azure Machine Learning med Multi-Factor Authentication i Azure AD:

1. Klienten loggar in på Azure AD och hämtar en Azure Resource Manager-token.
1. Klienten presenterar token för att Azure Resource Manager och till alla Azure Machine Learning.
1. Azure Machine Learning tillhandahåller en Machine Learning-tjänstetoken till användar beräknings målet (till exempel Azure Machine Learning Compute Cluster). Denna token används av användar beräknings målet för att anropa till Machine Learning tjänsten när körningen har slutförts. Omfattningen är begränsad till arbets ytan.

[![Autentisering i Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

Varje arbets yta har en associerad systemtilldelad [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) som har samma namn som arbets ytan. Den här hanterade identiteten används för att på ett säkert sätt få åtkomst till resurser som används av arbets ytan. Den har följande Azure RBAC-behörigheter för anslutna resurser:

| Resurs | Behörigheter |
| ----- | ----- |
| Arbetsyta | Deltagare |
| Lagringskonto | Storage Blob Data-deltagare |
| Nyckelvalv | Åtkomst till alla nycklar, hemligheter, certifikat |
| Azure Container Registry | Deltagare |
| Resurs grupp som innehåller arbets ytan | Deltagare |
| Resurs grupp som innehåller nyckel valvet (om det skiljer sig från det som innehåller arbets ytan) | Deltagare |

Vi rekommenderar inte att administratörer återkallar åtkomsten av den hanterade identiteten till de resurser som anges i tabellen ovan. Du kan återställa åtkomsten med hjälp av [åtgärden omsynkronisera nycklar](how-to-change-storage-access-key.md).

Azure Machine Learning skapar också ett ytterligare program (namnet börjar med `aml-` eller `Microsoft-AzureML-Support-App-` ) med åtkomst på deltagar nivå i din prenumeration för varje region för arbets ytan. Om du till exempel har en arbets yta i USA, östra och en i Nord Europa i samma prenumeration, ser du två av dessa program. Med dessa program kan Azure Machine Learning hjälpa dig att hantera beräknings resurser.

Du kan också konfigurera dina egna hanterade identiteter för användning med Azure Virtual Machines-och Azure Machine Learning Compute-kluster. Med en virtuell dator kan den hanterade identiteten användas för att komma åt din arbets yta från SDK, i stället för den enskilda användarens Azure AD-konto. Med ett beräknings kluster används den hanterade identiteten för att få åtkomst till resurser som skyddade data lager som användaren som kör utbildnings jobbet kanske inte har åtkomst till. Mer information finns i [autentisering för Azure Machine Learning-arbetsyta](how-to-setup-authentication.md).

> [!TIP]
> Det finns vissa undantag för användningen av Azure AD och Azure RBAC i Azure Machine Learning:
> * Du kan också aktivera __SSH__ -åtkomst för att beräkna resurser som Azure Machine Learning beräknings instans och beräknings kluster. SSH-åtkomst baseras på offentliga/privata nyckel par, inte Azure AD. SSH-åtkomst regleras inte av Azure RBAC.
> * Du kan autentisera till modeller som distribueras som webb tjänster (slut punkts slut punkter) med hjälp av __nyckel__ -eller __tokenbaserad__ autentisering. Nycklar är statiska strängar, medan tokens hämtas med hjälp av ett Azure AD-säkerhetsobjekt. Mer information finns i [Konfigurera autentisering för modeller som distribueras som en webb tjänst](how-to-authenticate-web-service.md).

Mer information finns i följande artiklar:
* [Autentisering för Azure Machine Learning arbets yta](how-to-setup-authentication.md)
* [Hantera åtkomst till Azure Machine Learning](how-to-assign-roles.md)
* [Ansluta till lagrings tjänster](how-to-access-data.md)
* [Använd Azure Key Vault för hemligheter när du tränar](how-to-use-secrets-in-runs.md)
* [Använd Azure AD-hanterad identitet med Azure Machine Learning](how-to-use-managed-identities.md)
* [Använd Azure AD-hanterad identitet med din webb tjänst](how-to-use-azure-ad-identity.md)

## <a name="network-security-and-isolation"></a>Nätverks säkerhet och isolering

För att begränsa nätverks åtkomsten till Azure Machine Learning resurser kan du använda [Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md). Med virtuella nätverk kan du skapa nätverks miljöer som är delvis eller helt isolerade från det offentliga Internet. Detta minskar angrepps ytan för din lösning, samt risken för data exfiltrering.

Du kan använda en VPN-gateway (virtuellt privat nätverk) för att ansluta enskilda klienter eller ditt eget nätverk till VNet

Azure Machine Learning arbets ytan kan använda [Azures privata länk](../private-link/private-link-overview.md) för att skapa en privat slut punkt bakom VNet. Detta ger en uppsättning privata IP-adresser som kan användas för att komma åt arbets ytan inifrån det virtuella nätverket. Några av de tjänster som Azure Machine Learning förlitar sig på kan också använda Azures privata länk, men vissa är beroende av nätverks säkerhets grupper eller användardefinierad routning.

Mer information finns i följande dokument:

* [Översikt över virtuella nätverks isolering och sekretess](how-to-network-security-overview.md)
* [Skydda arbetsyteresurser](how-to-secure-workspace-vnet.md)
* [Skydda träningsmiljöer](how-to-secure-training-vnet.md)
* [Säker miljö för störningar](how-to-secure-inferencing-vnet.md)
* [Använda Studio i ett säkert virtuellt nätverk](how-to-enable-studio-virtual-network.md)
* [Använda anpassad DNS](how-to-custom-dns.md)
* [Konfigurera brand vägg](how-to-access-azureml-behind-firewall.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>Datakryptering

Azure Machine Learning använder en mängd olika beräknings resurser och data lager på Azure-plattformen. Mer information om hur var och en av dessa stöder data kryptering i vila och under överföring finns i [data kryptering med Azure Machine Learning](concept-data-encryption.md).

När du distribuerar modeller som webb tjänster kan du Aktivera TLS (Transport Layer Security) för att kryptera data under överföring. Mer information finns i [Konfigurera en säker webb tjänst](how-to-secure-web-service.md).

## <a name="vulnerability-scanning"></a>Sårbarhetsgenomsökning

[Azure Security Center](../security-center/security-center-introduction.md) erbjuder enhetlig säkerhetshantering och avancerat skydd mot hot i olika hybridmolnarbetsbelastningar. För Azure Machine Learning bör du Aktivera genomsökning av [Azure Container Registry](../container-registry/container-registry-intro.md) resurs-och Azure Kubernetes-tjänsteresurser. Mer information finns i [Azure Container Registry avbildnings genomsökning av Security Center](../security-center/defender-for-container-registries-introduction.md) och [integrering med Azure Kubernetes Services med Security Center](../security-center/defender-for-kubernetes-introduction.md).

## <a name="audit-and-manage-compliance"></a>Granska och hantera efterlevnad

[Azure policy](../governance/policy/index.yml) är ett styrnings verktyg som gör att du kan se till att Azure-resurserna är kompatibla med dina principer. Du kan ange principer för att tillåta eller framtvinga vissa konfigurationer, till exempel om din Azure Machine Learning arbets yta använder en privat slut punkt. Mer information om Azure Policy finns i Azure Policy- [dokumentationen](../governance/policy/overview.md). Mer information om principerna som är speciella för Azure Machine Learning finns i [Granska och hantera efterlevnad med Azure policy](how-to-integrate-azure-policy.md).

## <a name="next-steps"></a>Nästa steg

* [Skydda Azure Machine Learning webb tjänster med TLS](how-to-secure-web-service.md)
* [Använda en Machine Learning modell som distribueras som en webb tjänst](how-to-consume-web-service.md)
* [Använda Azure Machine Learning med Azure-brandvägg](how-to-access-azureml-behind-firewall.md)
* [Använda Azure Machine Learning med Azure Virtual Network](how-to-network-security-overview.md)
* [Data kryptering i vila och under överföring](concept-data-encryption.md)
* [Bygg en API för rekommendationer i real tid i Azure](/azure/architecture/reference-architectures/ai/real-time-recommendation)
