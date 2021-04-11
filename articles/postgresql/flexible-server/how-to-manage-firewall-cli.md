---
title: Hantera brand Väggs regler – Azure CLI – Azure Database for PostgreSQL-flexibel Server
description: Skapa och hantera brand Väggs regler för Azure Database for PostgreSQL flexibel server med hjälp av kommando raden i Azure CLI.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3aafad8bcc42a9099dbd37b4e8349e4c093a0425
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226285"
---
# <a name="create-and-manage-azure-database-for-postgresql---flexible-server-firewall-rules-using-the-azure-cli"></a>Skapa och hantera Azure Database for PostgreSQL flexibla Server brand Väggs regler med hjälp av Azure CLI

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

Azure Database for PostgreSQL-flexibel Server stöder två typer av ömsesidigt exklusiva nätverks anslutnings metoder för att ansluta till din flexibla Server. De två alternativen är:

* Offentlig åtkomst (tillåtna IP-adresser)
* Privat åtkomst (VNet-integrering)

I den här artikeln fokuserar vi på att skapa en PostgreSQL-server med **offentlig åtkomst (tillåtna IP-adresser)** med hjälp av Azure CLI och ger en översikt över Azure CLI-kommandon som du kan använda för att skapa, uppdatera, ta bort, Visa och Visa brand Väggs regler efter att servern har skapats. Med *offentlig åtkomst (tillåtna IP-adresser)* begränsas anslutningarna till postgresql-servern till endast tillåtna IP-adresser. Klientens IP-adresser måste vara tillåtna i brand Väggs regler. Mer information om det finns i [offentlig åtkomst (tillåtna IP-adresser)](./concepts-networking.md#public-access-allowed-ip-addresses). Brand Väggs reglerna kan definieras när servern skapas (rekommenderas) men kan även läggas till senare.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) är ett kostnads fritt interaktivt gränssnitt som du kan använda för att köra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också öppna Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash) . Välj **Kopiera** för att kopiera kod blocken, klistra in den i Cloud Shell och välj **RETUR** för att köra den.

Om du föredrar att installera och använda CLI lokalt kräver den här snabb starten Azure CLI version 2,0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Förutsättningar

Du måste logga in på ditt konto med kommandot [AZ login](/cli/azure/reference-index#az-login) . Observera egenskapen **ID** som refererar till **prenumerations-ID** för ditt Azure-konto.

```azurecli-interactive
az login
```

Välj den aktuella prenumerationen under ditt konto med kommandot [AZ Account set](/cli/azure/account#az-account-set) . Anteckna **ID-** värdet från **AZ inloggnings** -utdata som ska användas som värde för argumentet **prenumeration** i kommandot. Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Använd [AZ Account List](/cli/azure/account#az-account-list)för att hämta alla prenumerationer.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-firewall-rule-during-flexible-server-create-using-azure-cli"></a>Skapa brand Väggs regel under flexibel Server skapa med Azure CLI

Du kan använda `az postgres flexible-server --public access` kommandot för att skapa en flexibel server med *offentlig åtkomst (tillåtna IP-adresser)* och konfigurera brand Väggs reglerna när du skapar en flexibel Server. Du kan använda växeln **--Public-Access** för att tillhandahålla tillåtna IP-adresser som kan ansluta till servern. Du kan ange en eller flera IP-adresser som ska ingå i listan över tillåtna IP-adresser. IP-adressintervall måste vara streck åtskilda och innehåller inga blank steg. Det finns olika alternativ för att skapa en flexibel server med CLI som visas i exemplet nedan.

Läs mer i referens dokumentationen för Azure CLI <!--FIXME --> en fullständig lista över konfigurerbara CLI-parametrar. I nedanstående kommandon kan du till exempel välja resurs gruppen.

- Skapa en flexibel server med offentlig åtkomst och Lägg till klientens IP-adress för att få åtkomst till servern
    ```azurecli-interactive
    az postgres flexible-server create --public-access <my_client_ip>
    ```
- Skapa en flexibel server med offentlig åtkomst och Lägg till IP-adressintervallet som ska ha åtkomst till den här servern

    ```azurecli-interactive
    az postgres flexible-server create --public-access <start_ip_address-end_ip_address>
    ```
- Skapa en flexibel server med offentlig åtkomst och Tillåt att program från Azure IP-adresser ansluter till din flexibla Server
    ```azurecli-interactive
    az postgres flexible-server create --public-access 0.0.0.0
    ```
    > [!IMPORTANT]
    > Det här alternativet konfigurerar brand väggen så att den tillåter offentlig åtkomst från Azure-tjänster och-resurser i Azure till den här servern, inklusive anslutningar från andra kunders prenumerationer. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
    >
- - Skapa en flexibel server med offentlig åtkomst och Tillåt alla IP-adresser
    ```azurecli-interactive
    az postgres flexible-server create --public-access all
    ```
    >[!Note]
    > Kommandot ovan skapar en brand Väggs regel med Start-IP-adress = 0.0.0.0, slut-IP-adress = 255.255.255.255 och inga IP-adresser kommer att blockeras. Alla värdar på Internet kan komma åt den här servern. Vi rekommenderar starkt att du bara använder den här regeln tillfälligt och bara på test servrar som inte innehåller känsliga data.
- Skapa en flexibel server med offentlig åtkomst och utan IP-adress
    ```azurecli-interactive
    az postgres flexible-server create --public-access none
    ```
    >[!Note]
    > Vi rekommenderar inte att du skapar en server utan några brand Väggs regler. Om du inte lägger till några brand Väggs regler kommer ingen klient att kunna ansluta till servern.
## <a name="create-and-manage-firewall-rule-after-server-create"></a>Skapa och hantera brand Väggs regler efter att servern har skapats
Kommandot **AZ postgress flexibla-Server Firewall-Rule** används från Azure CLI för att skapa, ta bort, Visa och uppdatera brand Väggs regler.

Kommandon:
- **skapa**: skapa en flexibel Server brand Väggs regel.
- **lista**: visar en lista över de flexibla reglerna för Server brand väggen.
- **Uppdatera**: uppdatera en regel för en flexibel Server brand vägg.
- **Visa**: Visa information om en flexibel Server brand Väggs regel.
- **ta bort**: ta bort en flexibel Server brand Väggs regel.

Läs mer i referens dokumentationen för Azure CLI <!--FIXME --> en fullständig lista över konfigurerbara CLI-parametrar. I nedanstående kommandon kan du till exempel välja resurs gruppen.

### <a name="create-a-firewall-rule"></a>Skapa en brandväggsregel
Använd `az postgres flexible-server firewall-rule create` kommandot för att skapa en ny brand Väggs regel på servern.
Om du vill tillåta åtkomst till ett intervall med IP-adresser anger du IP-adressen som Start-IP-adress och slut-IP-adress, som i det här exemplet.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Om du vill tillåta åtkomst för en enskild IP-adress kan du bara ange en enskild IP-adress, som i det här exemplet.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 1.1.1.1
```

Om du vill tillåta att program från Azure IP-adresser ansluter till din flexibla Server, anger du IP-adressen 0.0.0.0 som Start-IP, som i det här exemplet.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Det här alternativet konfigurerar brand väggen så att den tillåter offentlig åtkomst från Azure-tjänster och-resurser i Azure till den här servern, inklusive anslutningar från andra kunders prenumerationer. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 
När det är klart visar varje utdata-kommando information om brand Väggs regeln som du har skapat i JSON-format (som standard). Om det uppstår ett fel visar utdata i stället fel meddelande text i stället.

### <a name="list-firewall-rules"></a>Visa lista över brand Väggs regler 
Använd `az postgres flexible-server firewall-rule list` kommandot för att visa en lista över befintliga Server brand Väggs regler på servern. Observera att attributet Server namn anges i växeln **--Name** . 
```azurecli-interactive
az postgres flexible-server firewall-rule list --name mydemoserver
```
I utdata visas reglerna, om de finns, i JSON-format (som standard). Du kan använda parametern--output * * för att generera resultaten i ett mer läsbart tabell format.
```azurecli-interactive
az postgres flexible-server firewall-rule list --name mydemoserver --output table
```

### <a name="update-a-firewall-rule"></a>Uppdatera en brand Väggs regel
Använd `az postgres flexible-server firewall-rule update` kommandot för att uppdatera en befintlig brand Väggs regel på servern. Ange namnet på den befintliga brand Väggs regeln som indata, samt attributen Start-IP-adress och slut-IP-adress som ska uppdateras.
```azurecli-interactive
az postgres flexible-server firewall-rule update --name mydemoserver --rule-name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
När kommandot har slutförts visas information om den brand Väggs regel som du har uppdaterat i JSON-format (som standard). Om det uppstår ett fel visar utdata i stället fel meddelande text i stället.

> [!NOTE]
> Om brand Väggs regeln inte finns skapas regeln av kommandot Update.
### <a name="show-firewall-rule-details"></a>Visa information om brand Väggs regler
Använd `az postgres flexible-server firewall-rule show` kommandot för att visa den befintliga brand Väggs regel informationen från servern. Ange namnet på den befintliga brand Väggs regeln som inmatade.
```azurecli-interactive
az postgres flexible-server firewall-rule show --name mydemoserver --rule-name FirewallRule1
```
När kommandot har slutförts visas information om den brand Väggs regel som du har angett i JSON-format (som standard). Om det uppstår ett fel visar utdata i stället fel meddelande text i stället.

### <a name="delete-a-firewall-rule"></a>Ta bort en brandväggsregel
Använd `az postgres flexible-server firewall-rule delete` kommandot för att ta bort en befintlig brand Väggs regel från servern. Ange namnet på den befintliga brand Väggs regeln.
```azurecli-interactive
az postgres flexible-server firewall-rule delete --name mydemoserver --rule-name FirewallRule1
```
När det är klart finns det inga utdata. Vid fel visas fel meddelande text.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [nätverk i Azure Database for PostgreSQL-flexibel Server](./concepts-networking.md)
- Lär dig mer om regler för att [Azure Database for PostgreSQL flexibla Server brand vägg](./concepts-networking.md#public-access-allowed-ip-addresses)
- [Skapa och hantera Azure Database for PostgreSQL-flexibla Server brand Väggs regler med hjälp av Azure Portal](./how-to-manage-firewall-portal.md).
