---
title: Få åtkomst till nätverks begränsad register med hjälp av betrodd Azure-tjänst
description: Aktivera en betrodd Azure-tjänstinstans för säker åtkomst till en nätverks begränsad behållar register för att hämta eller skicka avbildningar
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 2e6b6ee3736f98f53ebb0aa43d707d42ba4cc058
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716490"
---
# <a name="allow-trusted-services-to-securely-access-a-network-restricted-container-registry-preview"></a>Tillåt betrodda tjänster att säkert komma åt en nätverks begränsad container Registry (för hands version)

Azure Container Registry kan tillåta Select-betrodda Azure-tjänster att komma åt ett register som är konfigurerat med nätverks åtkomst regler. När betrodda tjänster är tillåtna kan en betrodd tjänst instans på ett säkert sätt kringgå registrets nätverks regler och utföra åtgärder som pull-eller push-avbildningar. Tjänst instansens hanterade identitet används för åtkomst och måste tilldelas en Azure-roll och autentisera med registret.

Använd Azure Cloud Shell eller en lokal installation av Azure CLI för att köra kommando exemplen i den här artikeln. Om du vill använda det lokalt, krävs version 2,18 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Att tillåta register åtkomst av betrodda Azure-tjänster är en **förhands gransknings** funktion.

## <a name="limitations"></a>Begränsningar

* Du måste använda en systemtilldelad hanterad identitet som är aktive rad i en [betrodd tjänst](#trusted-services) för att få åtkomst till ett nätverks begränsat behållar register. Användare som tilldelats hanterade identiteter stöds inte för närvarande.
* Att tillåta betrodda tjänster gäller inte för ett behållar register som kon figurer ATS med en [tjänst slut punkt](container-registry-vnet.md). Funktionen påverkar bara register som är begränsade till en [privat slut punkt](container-registry-private-link.md) eller som har [regler för offentliga IP-åtkomst](container-registry-access-selected-networks.md) . 

## <a name="about-trusted-services"></a>Om betrodda tjänster

Azure Container Registry har en skiktad säkerhets modell som stöder flera nätverkskonfigurationer som begränsar åtkomsten till ett register, inklusive:

* [Privat slut punkt med Azure privat länk](container-registry-private-link.md). När ett registers privata slut punkt är tillgängligt, är det bara tillgängligt för resurser i det virtuella nätverket med hjälp av privata IP-adresser.  
* [Brand Väggs regler för registret](container-registry-access-selected-networks.md), som endast tillåter åtkomst till registrets offentliga slut punkt från vissa offentliga IP-adresser eller adress intervall. Du kan också konfigurera brand väggen så att den blockerar all åtkomst till den offentliga slut punkten när du använder privata slut punkter.

När de distribueras i ett virtuellt nätverk eller konfigureras med brand Väggs regler, nekar ett register åtkomst som standard till användare eller tjänster utanför dessa källor. 

Flera Azure-tjänster med flera innehavare körs från nätverk som inte kan ingå i dessa nätverks inställningar för registret, vilket förhindrar att de hämtar eller skickar avbildningar till registret. Genom att utse vissa tjänst instanser som "betrodda" kan en register ägare tillåta utvalda Azure-resurser att på ett säkert sätt kringgå registrets nätverks inställningar för att hämta eller push-avbildningar. 

### <a name="trusted-services"></a>Betrodda tjänster

Instanser av följande tjänster kan komma åt en nätverks begränsad behållar register om register inställningen **Tillåt betrodda tjänster** är aktive rad (standard). Fler tjänster kommer att läggas till med tiden.

|Betrodd tjänst  |Användnings scenarier som stöds  |
|---------|---------|
|ACR-uppgifter     | [Få åtkomst till ett annat register från en ACR-uppgift](container-registry-tasks-cross-registry-authentication.md)       |
|Machine Learning | [Distribuera](../machine-learning/how-to-deploy-custom-docker-image.md) eller [träna](../machine-learning/how-to-train-with-custom-image.md) en modell i en Machine Learning arbets yta med en anpassad Docker-behållar avbildning |
|Azure Container Registry | [Importera avbildningar från ett annat Azure Container Registry](container-registry-import-images.md#import-from-an-azure-container-registry-in-the-same-ad-tenant) | 

> [!NOTE]
> Som att aktivera inställningen Tillåt betrodda tjänster tillåter inte instanser av andra hanterade Azure-tjänster, inklusive App Service, Azure Container Instances och Azure Security Center för att få åtkomst till en nätverks begränsad behållar register.

## <a name="allow-trusted-services---cli"></a>Tillåt betrodda tjänster – CLI

Inställningen Tillåt betrodda tjänster är som standard aktive rad i ett nytt Azure Container Registry. Inaktivera eller aktivera inställningen genom att köra kommandot [AZ ACR Update](/cli/azure/acr#az-acr-update) .

För att inaktivera:

```azurecli
az acr update --name myregistry --allow-trusted-services false
```

Aktivera inställningen i ett befintligt register eller i ett register där det redan har inaktiverats:

```azurecli
az acr update --name myregistry --allow-trusted-services true
```

## <a name="allow-trusted-services---portal"></a>Tillåt betrodda tjänster – Portal

Inställningen Tillåt betrodda tjänster är som standard aktive rad i ett nytt Azure Container Registry. 

Så här inaktiverar eller aktiverar du inställningen på portalen igen:

1. I portalen navigerar du till behållar registret.
1. Under **Inställningar** väljer du **nätverk**. 
1. I **Tillåt offentligt nätverks åtkomst** väljer du **valda nätverk** eller **inaktiverade**.
1. Gör något av följande:
    * Om du vill inaktivera åtkomst av betrodda tjänster går du till **brand Väggs undantag** och avmarkerar **Tillåt att betrodda Microsoft-tjänster kommer åt det här behållar** 
    * Om du vill tillåta betrodda tjänster går du till **brand Väggs undantag** och kontrollerar **Tillåt att betrodda Microsoft-tjänster har åtkomst till detta behållar register**
1. Välj **Spara**.

## <a name="trusted-services-workflow"></a>Arbets flöde för betrodda tjänster

Här är ett typiskt arbets flöde som gör det möjligt för en instans av en betrodd tjänst att komma åt en nätverks begränsad behållar register.

1. Aktivera en systemtilldelad [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) i en instans av någon av de [betrodda tjänsterna](#trusted-services) för Azure Container Registry.
1. Tilldela identiteten en [Azure-roll](container-registry-roles.md) till registret. Du kan till exempel tilldela ACRPull-rollen för att hämta behållar avbildningar.
1. Konfigurera inställningen för att tillåta åtkomst av betrodda tjänster i registret som är begränsat till nätverket.
1. Använd identitetens autentiseringsuppgifter för att autentisera med det begränsade registret i nätverket. 
1. Hämta bilder från registret eller utför andra åtgärder som tillåts av rollen.

### <a name="example-acr-tasks"></a>Exempel: ACR-aktiviteter

I följande exempel visas hur du använder ACR-uppgifter som en betrodd tjänst. Se [autentisering mellan register i en ACR-aktivitet med hjälp av en Azure-hanterad identitet](container-registry-tasks-cross-registry-authentication.md) för aktivitets information.

1. Skapa eller uppdatera ett Azure Container Registry och [skicka en exempel bas avbildning](container-registry-tasks-cross-registry-authentication.md#prepare-base-registry) till registret. Det här registret är *bas registret* för scenariot.
1. I ett andra Azure Container Registry [definierar](container-registry-tasks-cross-registry-authentication.md#define-task-steps-in-yaml-file) och [skapar](container-registry-tasks-cross-registry-authentication.md#option-2-create-task-with-system-assigned-identity) du en ACR-uppgift för att hämta en avbildning från bas registret. Aktivera en systemtilldelad hanterad identitet när aktiviteten skapas.
1. Tilldela aktivitets identiteten [en Azure-roll för att få åtkomst till bas registret](container-registry-tasks-authentication-managed-identity.md#3-grant-the-identity-permissions-to-access-other-azure-resources). Du kan till exempel tilldela AcrPull-rollen som har behörighet att hämta avbildningar.
1. [Lägg till autentiseringsuppgifter för hanterad identitet](container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task) i uppgiften.
1. Om du vill bekräfta att aktiviteten kringgår nätverks begränsningar [inaktiverar du offentlig åtkomst](container-registry-access-selected-networks.md#disable-public-network-access) i bas registret.
1. Kör uppgiften. Om grundläggande register och aktivitet har kon figurer ATS korrekt körs aktiviteten, eftersom bas registret tillåter åtkomst.

Så här testar du inaktive ring av åtkomst av betrodda tjänster:

1. I bas registret inaktiverar du inställningen för att tillåta åtkomst av betrodda tjänster.
1. Kör uppgiften igen. I det här fallet Miss lyckas aktiviteten eftersom det inte längre går att utföra åtgärden på grund registret.

## <a name="next-steps"></a>Nästa steg

* Information om hur du begränsar åtkomsten till ett register med en privat slut punkt i ett virtuellt nätverk finns i [Konfigurera Azures privata länk för ett Azure Container Registry](container-registry-private-link.md).
* Information om hur du konfigurerar brand Väggs regler för registret finns i [Konfigurera regler för offentliga IP-nätverk](container-registry-access-selected-networks.md).
