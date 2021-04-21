---
title: Konfigurera offentlig registeråtkomst
description: Konfigurera IP-regler för att ge åtkomst till ett Azure-containerregister från valda offentliga IP-adresser eller adressintervall.
ms.topic: article
ms.date: 03/08/2021
ms.openlocfilehash: 00912f0e66c84feff40e6439d59ccdfa82a4ab6a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785844"
---
# <a name="configure-public-ip-network-rules"></a>Konfigurera regler för offentligt IP-nätverk

Ett Azure-containerregister accepterar som standard anslutningar via Internet från värdar i alla nätverk. Den här artikeln visar hur du konfigurerar containerregistret så att det endast tillåter åtkomst från specifika offentliga IP-adresser eller adressintervall. Motsvarande steg som använder Azure CLI och Azure Portal tillhandahålls.

IP-nätverksregler konfigureras på slutpunkten för det offentliga registret. IP-nätverksregler gäller inte för privata slutpunkter som konfigurerats med [Private Link](container-registry-private-link.md)

Konfiguration av IP-åtkomstregler är tillgängligt på **tjänstnivån** premiumcontainerregister. Information om registertjänstnivåer och begränsningar finns i [Azure Container Registry nivåer](container-registry-skus.md).

Varje register stöder högst 100 regler för nätverksåtkomst.

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="access-from-selected-public-network---cli"></a>Åtkomst från valt offentligt nätverk – CLI

### <a name="change-default-network-access-to-registry"></a>Ändra standardnätverksåtkomst till registret

Om du vill begränsa åtkomsten till ett valt offentligt nätverk ändrar du först standardåtgärden till neka åtkomst. Ersätt namnet på registret i följande [az acr update-kommando:][az-acr-update]

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Lägga till nätverksregel i registret

Använd kommandot [az acr network-rule add][az-acr-network-rule-add] för att lägga till en nätverksregel i registret som tillåter åtkomst från en offentlig IP-adress eller ett intervall. Du kan till exempel ersätta containerregistrets namn och den offentliga IP-adressen för en virtuell dator i ett virtuellt nätverk.

```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --ip-address <public-IP-address>
```

> [!NOTE]
> När du har lagt till en regel tar det några minuter för regeln att gälla.

## <a name="access-from-selected-public-network---portal"></a>Åtkomst från valt offentligt nätverk – portalen

1. Navigera till containerregistret i portalen.
1. Under **Inställningar** väljer du **Nätverk.**
1. På fliken **Offentlig åtkomst** väljer du för att tillåta offentlig åtkomst från **Valda nätverk**.
1. Under **Brandvägg** anger du en offentlig IP-adress, till exempel den offentliga IP-adressen för en virtuell dator i ett virtuellt nätverk. Eller ange ett adressintervall i CIDR-notation som innehåller den virtuella datorns IP-adress.
1. Välj **Spara**.

![Konfigurera brandväggsregel för containerregister][acr-access-selected-networks]

> [!NOTE]
> När du har lagt till en regel tar det några minuter för regeln att gälla.

> [!TIP]
> Du kan också aktivera registeråtkomst från en lokal klientdator eller IP-adressintervall. För att tillåta den här åtkomsten behöver du datorns offentliga IPv4-adress. Du hittar den här adressen genom att söka efter "what is my IP address" (vad är min IP-adress) i en webbläsare. Den aktuella klientens IPv4-adress visas också automatiskt när du konfigurerar brandväggsinställningar **på** sidan Nätverk i portalen.

## <a name="disable-public-network-access"></a>Inaktivera åtkomst till offentligt nätverk

Du kan också inaktivera den offentliga slutpunkten i registret. Om du inaktiverar den offentliga slutpunkten åsidosätts alla brandväggskonfigurationer. Du kanske till exempel vill inaktivera offentlig åtkomst till ett register som skyddas i ett virtuellt nätverk med [hjälp av Private Link](container-registry-private-link.md).

> [!NOTE]
> Om registret har ställts in i ett virtuellt nätverk med en tjänstslutpunkt [inaktiverar](container-registry-vnet.md)inaktivering av åtkomst till registrets offentliga slutpunkt även åtkomst till registret i det virtuella nätverket.

### <a name="disable-public-access---cli"></a>Inaktivera offentlig åtkomst – CLI

Om du vill inaktivera offentlig åtkomst med hjälp av Azure CLI kör [du az acr update][az-acr-update] och anger till `--public-network-enabled` `false` . Argumentet `public-network-enabled` kräver Azure CLI 2.6.0 eller senare. 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled false
```

### <a name="disable-public-access---portal"></a>Inaktivera offentlig åtkomst – portalen

1. I portalen navigerar du till containerregistret och väljer **Inställningar > Nätverk**.
1. På fliken **Offentlig åtkomst i** Tillåt offentlig **nätverksåtkomst väljer du** **Inaktiverad.** Välj sedan **Spara**.

![Inaktivera offentlig åtkomst][acr-access-disabled]


## <a name="restore-public-network-access"></a>Återställa åtkomst till offentligt nätverk

Om du vill återaktivera den offentliga slutpunkten uppdaterar du nätverksinställningarna för att tillåta offentlig åtkomst. Om du aktiverar den offentliga slutpunkten åsidosätts alla brandväggskonfigurationer. 

### <a name="restore-public-access---cli"></a>Återställa offentlig åtkomst – CLI

Kör [az acr update][az-acr-update] och ange till `--public-network-enabled` `true` . 

> [!NOTE]
> Argumentet `public-network-enabled` kräver Azure CLI 2.6.0 eller senare. 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled true
```

### <a name="restore-public-access---portal"></a>Återställa offentlig åtkomst – portalen

1. I portalen navigerar du till containerregistret och väljer **Inställningar > Nätverk**.
1. På fliken **Offentlig åtkomst** i Tillåt **offentlig nätverksåtkomst väljer** du Alla **nätverk**. Välj sedan **Spara**.

![Offentlig åtkomst från alla nätverk][acr-access-all-networks]

## <a name="troubleshoot"></a>Felsöka

Om en regel för offentligt nätverk har angetts, eller om offentlig åtkomst till registret nekas, misslyckas försök att logga in i registret från ett otillåtet offentligt nätverk. Klientåtkomst bakom en HTTPS-proxy misslyckas också om en åtkomstregel för proxyn inte har angetts. Du ser ett felmeddelande som liknar `Error response from daemon: login attempt failed with status: 403 Forbidden` eller `Looks like you don't have access to registry` .

De här felen kan också inträffa om du använder en HTTPS-proxy som tillåts av en regel för nätverksåtkomst, men proxyn inte är korrekt konfigurerad i klientmiljön. Kontrollera att både Docker-klienten och Docker-daemonen har konfigurerats för proxybeteende. Mer information finns i [HTTP/HTTPS-proxy](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) i Docker-dokumentationen.


## <a name="next-steps"></a>Nästa steg

* Information om hur du begränsar åtkomsten till ett register med hjälp av en privat slutpunkt i ett virtuellt nätverk finns [i Konfigurera Azure Private Link för ett Azure-containerregister.](container-registry-private-link.md)
* Om du behöver konfigurera regler för registeråtkomst bakom en klientbrandvägg kan du se Konfigurera regler för åtkomst till [ett Azure-containerregister bakom en brandvägg.](container-registry-firewall-access-rules.md)

[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az_acr_network_rule_add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az_acr_network_rule_remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az_acr_network_rule_list
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-update]: /cli/azure/acr#az_acr_update
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com

[acr-access-selected-networks]: ./media/container-registry-access-selected-networks/acr-access-selected-networks.png
[acr-access-disabled]: ./media/container-registry-access-selected-networks/acr-access-disabled.png
[acr-access-all-networks]: ./media/container-registry-access-selected-networks/acr-access-all-networks.png
