---
title: Få åtkomst till nätverksbegränsat register med betrodd Azure-tjänst
description: Aktivera en betrodd Azure-tjänstinstans för säker åtkomst till ett nätverksbegränsat containerregister för att hämta eller skicka avbildningar
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 4b0d7feb223bcfcec4e8b2c786b211f4e3c3c3eb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785878"
---
# <a name="allow-trusted-services-to-securely-access-a-network-restricted-container-registry-preview"></a>Tillåt betrodda tjänster att få säker åtkomst till ett nätverksbegränsat containerregister (förhandsversion)

Azure Container Registry kan tillåta att utvalda betrodda Azure-tjänster får åtkomst till ett register som har konfigurerats med regler för nätverksåtkomst. När betrodda tjänster tillåts kan en betrodd tjänstinstans på ett säkert sätt kringgå registrets nätverksregler och utföra åtgärder som pull- eller push-avbildningar. Tjänstinstansens hanterade identitet används för åtkomst och måste tilldelas en Azure-roll och autentiseras med registret.

Använd den Azure Cloud Shell eller en lokal installation av Azure CLI för att köra kommandoexempel i den här artikeln. Om du vill använda den lokalt krävs version 2.18 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Att tillåta registeråtkomst från betrodda Azure-tjänster är en **förhandsgranskningsfunktion.**

## <a name="limitations"></a>Begränsningar

* Du måste använda en system tilldelad hanterad identitet som är aktiverad i en [betrodd tjänst för](#trusted-services) att få åtkomst till ett nätverksbegränsat containerregister. Användar tilldelade hanterade identiteter stöds inte för närvarande.
* Att tillåta betrodda tjänster gäller inte för ett containerregister som konfigurerats med en [tjänstslutpunkt.](container-registry-vnet.md) Funktionen påverkar endast register som är begränsade med en privat slutpunkt [eller som](container-registry-private-link.md) har regler för [offentlig IP-åtkomst](container-registry-access-selected-networks.md) tillämpad. 

## <a name="about-trusted-services"></a>Om betrodda tjänster

Azure Container Registry har en flerlagerssäkerhetsmodell med stöd för flera nätverkskonfigurationer som begränsar åtkomsten till ett register, inklusive:

* [Privat slutpunkt med Azure Private Link](container-registry-private-link.md). När det har konfigurerats är ett registers privata slutpunkt endast tillgänglig för resurser i det virtuella nätverket med hjälp av privata IP-adresser.  
* [Registerbrandväggsregler](container-registry-access-selected-networks.md), som endast tillåter åtkomst till registrets offentliga slutpunkt från specifika offentliga IP-adresser eller adressintervall. Du kan också konfigurera brandväggen så att den blockerar all åtkomst till den offentliga slutpunkten när du använder privata slutpunkter.

När det distribueras i ett virtuellt nätverk eller konfigureras med brandväggsregler nekar ett register som standard åtkomst till användare eller tjänster utanför dessa källor. 

Flera Azure-tjänster för flera innehavare fungerar från nätverk som inte kan ingå i dessa nätverksinställningar för registret, vilket hindrar dem från att hämta eller push-pusha avbildningar till registret. Genom att ange vissa tjänstinstanser som "betrodda" kan en registerägare tillåta att utvalda Azure-resurser på ett säkert sätt kringgår registrets nätverksinställningar för att hämta eller skicka avbildningar. 

### <a name="trusted-services"></a>Betrodda tjänster

Instanser av följande tjänster kan komma åt ett nätverksbegränsat containerregister om inställningen tillåt **betrodda** tjänster i registret är aktiverad (standardinställningen). Fler tjänster kommer att läggas till med tiden.

|Betrodd tjänst  |Användningsscenarier som stöds  |
|---------|---------|
|ACR-uppgifter     | [Få åtkomst till ett annat register än en ACR-uppgift](container-registry-tasks-cross-registry-authentication.md)       |
|Machine Learning | [Distribuera](../machine-learning/how-to-deploy-custom-docker-image.md) eller [träna en](../machine-learning/how-to-train-with-custom-image.md) modell i en Machine Learning med hjälp av en anpassad Docker-containeravbildning |
|Azure Container Registry | [Importera avbildningar från ett annat Azure-containerregister](container-registry-import-images.md#import-from-an-azure-container-registry-in-the-same-ad-tenant) | 

> [!NOTE]
> Att aktivera inställningen Tillåt betrodda tjänster tillåter inte instanser av andra hanterade Azure-tjänster, inklusive App Service, Azure Container Instances och Azure Security Center, att komma åt ett nätverksbegränsat containerregister.

## <a name="allow-trusted-services---cli"></a>Tillåt betrodda tjänster – CLI

Som standard är inställningen Tillåt betrodda tjänster aktiverad i ett nytt Azure-containerregister. Inaktivera eller aktivera inställningen genom att köra [kommandot az acr update.](/cli/azure/acr#az_acr_update)

Så här inaktiverar du:

```azurecli
az acr update --name myregistry --allow-trusted-services false
```

Så här aktiverar du inställningen i ett befintligt register eller ett register där det redan är inaktiverat:

```azurecli
az acr update --name myregistry --allow-trusted-services true
```

## <a name="allow-trusted-services---portal"></a>Tillåt betrodda tjänster – portalen

Som standard är inställningen Tillåt betrodda tjänster aktiverad i ett nytt Azure-containerregister. 

Så här inaktiverar eller återaktiverar du inställningen i portalen:

1. Navigera till containerregistret i portalen.
1. Under **Inställningar** väljer du **Nätverk.** 
1. I **Tillåt offentlig nätverksåtkomst** väljer du **Valda nätverk eller** **Inaktiverad**.
1. Gör något av följande:
    * Om du vill inaktivera åtkomst för betrodda tjänster **avmarkerar du** Tillåt betrodda tjänster Microsoft-tjänster åtkomst till det här containerregistret under **Brandväggsfel.** 
    * Om du vill tillåta betrodda tjänster under **Brandväggsfel** markerar **du Tillåt betrodda Microsoft-tjänster att komma åt det här containerregistret.**
1. Välj **Spara**.

## <a name="trusted-services-workflow"></a>Arbetsflöde för betrodda tjänster

Här är ett vanligt arbetsflöde för att göra det möjligt för en instans av en betrodd tjänst att komma åt ett nätverksbegränsat containerregister.

1. Aktivera en system tilldelad [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) i en instans av en av de [betrodda tjänsterna](#trusted-services) för Azure Container Registry.
1. Tilldela identiteten en [Azure-roll](container-registry-roles.md) till ditt register. Tilldela till exempel rollen ACRPull för att hämta containeravbildningar.
1. I det nätverksbegränsade registret konfigurerar du inställningen för att tillåta åtkomst av betrodda tjänster.
1. Använd identitetens autentiseringsuppgifter för att autentisera med det nätverksbegränsade registret. 
1. Hämta avbildningar från registret eller utför andra åtgärder som tillåts av rollen.

### <a name="example-acr-tasks"></a>Exempel: ACR-uppgifter

I följande exempel visas hur du använder ACR-uppgifter som en betrodd tjänst. Information om aktiviteter finns i Autentisering mellan register [i en ACR-uppgift](container-registry-tasks-cross-registry-authentication.md) med hjälp av en Azure-hanterad identitet.

1. Skapa eller uppdatera ett Azure-containerregister och [skicka en exempelbasavbildning](container-registry-tasks-cross-registry-authentication.md#prepare-base-registry) till registret. Det här registret är *basregistret* för scenariot.
1. I ett andra Azure-containerregister [definierar och](container-registry-tasks-cross-registry-authentication.md#define-task-steps-in-yaml-file) [skapar du en](container-registry-tasks-cross-registry-authentication.md#option-2-create-task-with-system-assigned-identity) ACR-uppgift för att hämta en avbildning från basregistret. Aktivera en system tilldelad hanterad identitet när du skapar uppgiften.
1. Tilldela uppgiftsidentiteten [en Azure-roll för åtkomst till basregistret](container-registry-tasks-authentication-managed-identity.md#3-grant-the-identity-permissions-to-access-other-azure-resources). Du kan till exempel tilldela rollen AcrPull, som har behörighet att hämta avbildningar.
1. [Lägg till autentiseringsuppgifter för hanterad](container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task) identitet i uppgiften.
1. Om du vill bekräfta att aktiviteten kringgår nätverksbegränsningar [inaktiverar du offentlig](container-registry-access-selected-networks.md#disable-public-network-access) åtkomst i basregistret.
1. Kör uppgiften. Om basregistret och uppgiften har konfigurerats korrekt körs uppgiften korrekt eftersom basregistret tillåter åtkomst.

Så här testar du inaktivering av åtkomst för betrodda tjänster:

1. I basregistret inaktiverar du inställningen för att tillåta åtkomst från betrodda tjänster.
1. Kör uppgiften igen. I det här fallet misslyckas aktivitetskörningen eftersom basregistret inte längre tillåter åtkomst för aktiviteten.

## <a name="next-steps"></a>Nästa steg

* Information om hur du begränsar åtkomsten till ett register med hjälp av en privat slutpunkt i ett virtuellt nätverk finns [i Konfigurera Azure Private Link för ett Azure-containerregister.](container-registry-private-link.md)
* Information om hur du konfigurerar brandväggsregler för registret finns [i Konfigurera regler för offentliga IP-nätverk.](container-registry-access-selected-networks.md)
