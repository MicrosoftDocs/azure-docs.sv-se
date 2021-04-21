---
title: Hantera brandväggsregler – Azure CLI – Azure Database for PostgreSQL – flexibel server
description: Skapa och hantera brandväggsregler för Azure Database for PostgreSQL – flexibel server med hjälp av Azure CLI-kommandoraden.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: beed3dac1a2ca5bc6d2a87ba2a9044333e798fa9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778525"
---
# <a name="create-and-manage-azure-database-for-postgresql---flexible-server-firewall-rules-using-the-azure-cli"></a>Skapa och hantera Azure Database for PostgreSQL – brandväggsregler för flexibel server med hjälp av Azure CLI

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibel server är en förhandsversion

Azure Database for PostgreSQL – Flexibel server stöder två typer av ömsesidigt uteslutande nätverksanslutningsmetoder för att ansluta till din flexibla server. De två alternativen är:

* Offentlig åtkomst (tillåtna IP-adresser)
* Privat åtkomst (VNet-integrering)

I den här artikeln fokuserar vi på att skapa PostgreSQL-server med offentlig åtkomst **(tillåtna IP-adresser)** med hjälp av Azure CLI och ger en översikt över Azure CLI-kommandon som du kan använda för att skapa, uppdatera, ta bort, lista och visa brandväggsregler när servern har skapats. Med *offentlig åtkomst (tillåtna IP-adresser)* är anslutningarna till PostgreSQL-servern begränsade till endast tillåtna IP-adresser. Klientens IP-adresser måste tillåtas i brandväggsregler. Mer information finns i Offentlig åtkomst [(tillåtna IP-adresser).](./concepts-networking.md#public-access-allowed-ip-addresses) Brandväggsreglerna kan definieras när servern skapas (rekommenderas) men kan även läggas till senare.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Det [Azure Cloud Shell](../../cloud-shell/overview.md) är ett kostnadsfritt interaktivt gränssnitt som du kan använda för att köra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också öppna Cloud Shell en separat webbläsarflik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash) . Välj **Kopiera** för att kopiera kodblocken, klistra in det i Cloud Shell och välj Retur **för** att köra den.

Om du föredrar att installera och använda CLI lokalt kräver den här snabbstarten Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Förutsättningar

Du måste logga in på ditt konto med kommandot [az login.](/cli/azure/reference-index#az_login) Observera **ID-egenskapen,** som refererar till **prenumerations-ID för** ditt Azure-konto.

```azurecli-interactive
az login
```

Välj den specifika prenumerationen under ditt konto med [kommandot az account](/cli/azure/account#az_account_set) set. Anteckna **ID-värdet från az** login-utdata som ska användas som värde för **prenumerationsargumentet** i kommandot .  Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Om du vill hämta hela prenumerationen använder [du az account list](/cli/azure/account#az_account_list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-firewall-rule-during-flexible-server-create-using-azure-cli"></a>Skapa brandväggsregel när flexibel server skapas med Hjälp av Azure CLI

Du kan använda kommandot för att skapa den flexibla servern med offentlig åtkomst `az postgres flexible-server --public access` *(tillåtna IP-adresser)* och konfigurera brandväggsreglerna när du skapar en flexibel server. Du kan använda **växeln --public-access** för att ange tillåtna IP-adresser som ska kunna ansluta till servern. Du kan ange en eller flera IP-adresser som ska ingå i listan över tillåtna IP-adresser. IP-adressintervallet måste vara streckavgränsat och innehåller inga blanksteg. Det finns olika alternativ för att skapa en flexibel server med HJÄLP av CLI, som du ser i exemplet nedan.

Läs referensdokumentationen för Azure CLI <!--FIXME --> för en fullständig lista över konfigurerbara CLI-parametrar. I kommandona nedan kan du till exempel ange resursgruppen.

- Skapa en flexibel server med offentlig åtkomst och lägg till klientens IP-adress för att få åtkomst till servern
    ```azurecli-interactive
    az postgres flexible-server create --public-access <my_client_ip>
    ```
- Skapa en flexibel server med offentlig åtkomst och lägg till IP-adressintervallet för att få åtkomst till den här servern

    ```azurecli-interactive
    az postgres flexible-server create --public-access <start_ip_address-end_ip_address>
    ```
- Skapa en flexibel server med offentlig åtkomst och tillåt att program från Azures IP-adresser ansluter till din flexibla server
    ```azurecli-interactive
    az postgres flexible-server create --public-access 0.0.0.0
    ```
    > [!IMPORTANT]
    > Det här alternativet konfigurerar brandväggen för att tillåta offentlig åtkomst från Azure-tjänster och -resurser i Azure till den här servern, inklusive anslutningar från prenumerationer från andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
    >
- - Skapa en flexibel server med offentlig åtkomst och tillåt alla IP-adresser
    ```azurecli-interactive
    az postgres flexible-server create --public-access all
    ```
    >[!Note]
    > Kommandot ovan skapar en brandväggsregel med ip-startadress=0.0.0.0, slut-IP-adress=255.255.255.255 och inga IP-adresser blockeras. Alla värdar på Internet kan komma åt den här servern. Vi rekommenderar starkt att du endast använder den här regeln tillfälligt och endast på testservrar som inte innehåller känsliga data.
- Skapa en flexibel server med offentlig åtkomst och utan IP-adress
    ```azurecli-interactive
    az postgres flexible-server create --public-access none
    ```
    >[!Note]
    > Vi rekommenderar inte att du skapar en server utan brandväggsregler. Om du inte lägger till några brandväggsregler kommer ingen klient att kunna ansluta till servern.
## <a name="create-and-manage-firewall-rule-after-server-create"></a>Skapa och hantera brandväggsregel när servern har skapat
Kommandot **az postgres flexible-server firewall-rule** används från Azure CLI för att skapa, ta bort, lista, visa och uppdatera brandväggsregler.

Kommandon:
- **skapa:** Skapa en flexibel serverbrandväggsregel.
- **list**: Lista över brandväggsregler för flexibel server.
- **update**: Uppdatera en flexibel serverbrandväggsregel.
- **visa**: Visa information om en brandväggsregel för flexibel server.
- **delete**: Ta bort en brandväggsregel för flexibel server.

Läs referensdokumentationen för Azure CLI <!--FIXME --> för en fullständig lista över konfigurerbara CLI-parametrar. I kommandona nedan kan du till exempel ange resursgruppen.

### <a name="create-a-firewall-rule"></a>Skapa en brandväggsregel
Använd kommandot `az postgres flexible-server firewall-rule create` för att skapa en ny brandväggsregel på servern.
Om du vill tillåta åtkomst till ett ip-adressintervall anger du IP-adressen som Start-IP-adress och Slut-IP-adress, som i det här exemplet.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Om du vill tillåta åtkomst för en enskild IP-adress anger du bara en enskild IP-adress, som i det här exemplet.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 1.1.1.1
```

Om du vill tillåta att program från Azure IP-adresser ansluter till din flexibla server anger du IP-adressen 0.0.0.0 som Start-IP, som i det här exemplet.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen för att tillåta offentlig åtkomst från Azure-tjänster och -resurser i Azure till den här servern, inklusive anslutningar från prenumerationer från andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 
När kommandot har skapats visas information om brandväggsregeln som du har skapat i JSON-format (som standard). Om det uppstår ett fel visar utdata felmeddelandetexten i stället.

### <a name="list-firewall-rules"></a>Lista brandväggsregler 
Använd kommandot `az postgres flexible-server firewall-rule list` för att visa en lista över de befintliga serverbrandväggsreglerna på servern. Observera att servernamnattributet anges i **växeln --name.** 
```azurecli-interactive
az postgres flexible-server firewall-rule list --name mydemoserver
```
Utdata visar reglerna, om de finns, i JSON-format (som standard). Du kan använda --output table**-växeln för att mata ut resultatet i ett mer läsbart tabellformat.
```azurecli-interactive
az postgres flexible-server firewall-rule list --name mydemoserver --output table
```

### <a name="update-a-firewall-rule"></a>Uppdatera en brandväggsregel
Använd kommandot `az postgres flexible-server firewall-rule update` för att uppdatera en befintlig brandväggsregel på servern. Ange namnet på den befintliga brandväggsregeln som indata, samt start-IP-adress och slut-IP-adressattribut som ska uppdateras.
```azurecli-interactive
az postgres flexible-server firewall-rule update --name mydemoserver --rule-name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
När kommandot har lyckats visas information om brandväggsregeln som du har uppdaterat i JSON-format (som standard). Om det uppstår ett fel visar utdata felmeddelandetexten i stället.

> [!NOTE]
> Om brandväggsregeln inte finns skapas regeln av uppdateringskommandot.
### <a name="show-firewall-rule-details"></a>Visa information om brandväggsregler
Använd kommandot `az postgres flexible-server firewall-rule show` för att visa information om den befintliga brandväggsregeln från servern. Ange namnet på den befintliga brandväggsregeln som indata.
```azurecli-interactive
az postgres flexible-server firewall-rule show --name mydemoserver --rule-name FirewallRule1
```
När kommandot har lyckats visas information om brandväggsregeln som du har angett i JSON-format (som standard). Om det uppstår ett fel visar utdata felmeddelandetexten i stället.

### <a name="delete-a-firewall-rule"></a>Ta bort en brandväggsregel
Använd kommandot `az postgres flexible-server firewall-rule delete` för att ta bort en befintlig brandväggsregel från servern. Ange namnet på den befintliga brandväggsregeln.
```azurecli-interactive
az postgres flexible-server firewall-rule delete --name mydemoserver --rule-name FirewallRule1
```
När det har lyckats finns det inga utdata. Vid fel visas felmeddelandetext.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [nätverk i Azure Database for PostgreSQL – flexibel server](./concepts-networking.md)
- Mer information om [Azure Database for PostgreSQL – brandväggsregler för flexibel server](./concepts-networking.md#public-access-allowed-ip-addresses)
- [Skapa och hantera Azure Database for PostgreSQL – brandväggsregler för flexibel server med hjälp av Azure Portal](./how-to-manage-firewall-portal.md).
