---
title: 'Snabb start: skapa en Azure Database for MySQL flexibel Server – Azure Portal'
description: Den här artikeln vägleder dig genom att använda Azure Portal för att skapa en Azure Database for MySQL flexibel server på några minuter.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/22/2020
ms.openlocfilehash: 53878384f4eb056f0cb23ec9005043ac26c8fad2
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492629"
---
# <a name="quickstart-use-the-azure-portal-to-create-an-azure-database-for-mysql-flexible-server"></a>Snabb start: Använd Azure Portal för att skapa en Azure Database for MySQL flexibel Server

Azure Database for MySQL flexibel Server är en hanterad tjänst som du kan använda för att köra, hantera och skala hög tillgängliga MySQL-servrar i molnet. Den här snabb starten visar hur du skapar en flexibel server med hjälp av Azure Portal.

> [!IMPORTANT] 
> Azure Database for MySQL flexibel Server är för närvarande en offentlig för hands version.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal
Gå till [Azure-portalen](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Skapa en Azure Database for MySQL flexibel Server

Du skapar en flexibel server med en definierad uppsättning [beräknings-och lagrings resurser](./concepts-compute-storage.md). Du skapar servern i en [Azure-resursgrupp](../../azure-resource-manager/management/overview.md).

Utför de här stegen för att skapa en flexibel Server:

1. Sök efter och välj **Azure Database for MySQL-servrar** i portalen:
    
    > :::image type="content" source="./media/quickstart-create-server-portal/find-mysql-portal.png" alt-text="Skärm bild som visar en sökning efter Azure Database for MySQL-servrar.":::

2. Välj **Lägg till**. 

3. På sidan **välj Azure Database for MySQL distributions alternativ** väljer du **flexibel Server** som distributions alternativ:
     
    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="Skärm bild som visar alternativet för flexibel Server.":::    

4. På fliken **Grundläggande** anger du följande information: 

    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="Skärm bild som visar fliken grundläggande på sidan för den flexibla servern."::: 
                                    
    |**Inställning**|**Föreslaget värde**|**Beskrivning**|
    |---|---|---|
    Prenumeration|Ditt prenumerationsnamn|Den Azure-prenumeration som ska användas för servern. Om du har flera prenumerationer väljer du den prenumeration som du vill fakturera för resursen.|
    Resursgrupp|**myresourcegroup**| Ett nytt resursgruppnamn eller ett befintligt namn i prenumerationen.|
    Servernamn |**mydemoserver**|Ett unikt namn som identifierar din flexibla Server. Domän namnet `mysql.database.azure.com` läggs till i det Server namn du anger. Ditt servernamn får bara innehålla gemener, siffror och bindestreck. Det måste innehålla mellan 3 och 63 tecken.|
    Region|Den region som är närmast dina användare| Den plats som är närmast dina användare.|
    Arbets belastnings typ| Utveckling | För produktions belastningen kan du välja liten/medels Tor storlek eller stor storlek beroende på [max_connections](concepts-server-parameters.md#max_connections) krav|
    Tillgänglighetszon| Ingen preferens | Om ditt program i virtuella Azure-datorer, skalnings uppsättningar för virtuella datorer eller AKS-instansen tillhandahålls i en specifik tillgänglighets zon, kan du ange den flexibla servern i samma tillgänglighets zon som samordna program och databas för att förbättra prestanda genom att minska nätverks fördröjningen mellan zoner.|
    Hög tillgänglighet| Standardvärde | För produktions servrar rekommenderar vi att du aktiverar zon redundant hög tillgänglighet (HA) för verksamhets kontinuitet och skydd mot zon haverier|
    MySQL-version|**5.7**| En MySQL-huvud version.|
    Användarnamn för administratör |**mydemouser**| Ditt eget inloggnings konto som ska användas när du ansluter till servern. Administratörens användar namn får inte vara **azure_superuser**, **administratör**, **administratör**, **rot**, **gäst** eller **offentlig**.|
    Lösenord |Ditt lösenord| Ett nytt lösenord för serverns administratörskonto. Det måste innehålla mellan 8 och 128 tecken. Det måste också innehålla tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror (0 till 9) och icke-alfanumeriska tecken (!, $, #,% osv.).|
    Beräkning och lagring | **Burstable**, **Standard_B1ms**, **10 GIB**, **100 IOPS**, **7 dagar** | Konfigurationerna för beräkning, lagring, IOPS och säkerhets kopiering för den nya servern. Välj **Konfigurera Server**. **Burstable**, **Standard_B1ms**, **10 GIB**, **100 IOPS** och **7 dagar** är standardvärden för **beräknings nivå**, **beräknings storlek**, **lagrings storlek**, **IOPS** och **kvarhållningsperioden** för säkerhets kopiering. Du kan lämna dessa värden som de är eller justera dem. För snabbare data inläsningar under migreringen rekommenderar vi att du ökar IOPS till den maximala storlek som stöds av beräknings storlek och ändrar sedan tillbaka den till att spara kostnaden. Spara beräknings-och lagrings valet genom att välja **Spara** för att fortsätta med konfigurationen. Följande skärm bild visar beräknings-och lagrings alternativen.|
    
    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="Skärm bild som visar alternativ för beräkning och lagring.":::

5. Konfigurera nätverks alternativ.

    På fliken **nätverk** kan du välja hur servern kan kontaktas. Azure Database for MySQL flexibel Server erbjuder två sätt att ansluta till servern: 
   - Offentlig åtkomst (tillåtna IP-adresser)
   - Privat åtkomst (VNet-integrering) 
   
   När du använder offentlig åtkomst begränsas åtkomsten till servern till tillåtna IP-adresser som du lägger till i en brand Väggs regel. Den här metoden förhindrar att externa program och verktyg ansluter till servern och alla databaser på servern, om du inte skapar en regel för att öppna brand väggen för en speciell IP-adress eller ett intervall. När du använder privat åtkomst (VNet-integrering) är åtkomst till din server begränsad till ditt virtuella nätverk. I den här snabb starten får du lära dig hur du aktiverar offentlig åtkomst för att ansluta till servern. [Lär dig mer om anslutnings metoder i artikeln begrepp.](./concepts-networking.md)

    > [!NOTE]
    > Du kan inte ändra anslutnings metoden när du har skapat servern. Om du till exempel väljer **offentlig åtkomst (tillåtna IP-adresser)** när du skapar servern kan du inte ändra till **privat åtkomst (VNet-integrering)** när servern har skapats. Vi rekommenderar starkt att du skapar din server med privat åtkomst för att skydda åtkomsten till servern via VNet-integrering. [Läs mer om privat åtkomst i artikeln begrepp.](./concepts-networking.md)

    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="Skärm bild som visar fliken nätverk.":::  

6. Välj **Granska + skapa** för att granska din flexibla Server konfiguration.

7. Välj **Skapa** för att etablera servern. Etableringen kan ta några minuter.

8. Välj **meddelanden** i verktygsfältet (klock knappen) för att övervaka distributions processen. När distributionen är färdig kan du välja **Fäst på instrument panelen**, vilket skapar en panel för den flexibla servern på Azure Portal instrument panelen. Den här panelen är en genväg till serverns **översikts** sida. När du väljer **gå till resurs** öppnas sidan **Översikt** på servern.

Som standard skapas dessa databaser under din server: information_schema, MySQL, performance_schema och sys.

> [!NOTE]
> Du undviker anslutnings problem genom att kontrol lera om nätverket tillåter utgående trafik över Port 3306, som används av Azure Database for MySQL flexibel Server.  

## <a name="connect-to-the-server-by-using-mysqlexe"></a>Anslut till servern med hjälp av mysql.exe

Om du har skapat din flexibla server med hjälp av privat åtkomst (VNet-integrering) måste du ansluta till servern från en resurs i samma virtuella nätverk som din server. Du kan skapa en virtuell dator och lägga till den i det virtuella nätverket som skapats med din flexibla Server. Mer information om hur du konfigurerar [privat åtkomst-dokumentationen](how-to-manage-virtual-network-portal.md) .

Om du har skapat din flexibla server med hjälp av offentlig åtkomst (tillåtna IP-adresser) kan du lägga till din lokala IP-adress i listan över brand Väggs regler på servern. Se [skapa eller hantera brand Väggs regler dokumentation](how-to-manage-firewall-portal.md) för steg-för-steg-anvisningar.

Du kan använda antingen [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) eller [MySQL Workbench](./connect-workbench.md) för att ansluta till servern från den lokala miljön. Azure Database for MySQL flexibla servern stöder anslutning av klient program till MySQL-tjänsten med hjälp av Transport Layer Security (TLS), tidigare kallat Secure Sockets Layer (SSL). TLS är ett bransch standard protokoll som garanterar krypterade nätverks anslutningar mellan din databas server och klient program, så att du kan följa kraven för efterlevnad. Om du vill ansluta till din MySQL-flexibla Server måste du ladda ned det [offentliga SSL-certifikatet](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) för verifiering av certifikat utfärdare.

I följande exempel visas hur du ansluter till den flexibla servern med hjälp av kommando rads gränssnittet MySQL. Först installerar du kommando raden MySQL om den inte redan är installerad. Du kommer att hämta det DigiCertGlobalRootCA-certifikat som krävs för SSL-anslutningar. Använd inställningen--SSL-mode = krav på anslutnings sträng för att framtvinga verifiering av TLS/SSL-certifikat. Överför sökvägen till den lokala certifikat filen till parametern--SSL-ca. Ersätt värden med det faktiska Server namnet och lösen ordet.

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

Om du har etablerad den flexibla servern med hjälp av **offentlig åtkomst** kan du också använda [Azure Cloud Shell](https://shell.azure.com/bash) för att ansluta till din flexibla server med den förinstallerade mysql-klienten som visas nedan:

För att du ska kunna använda Azure Cloud Shell för att ansluta till din flexibla Server måste du tillåta nätverks åtkomst från Azure Cloud Shell till din flexibla Server. För att åstadkomma detta kan du gå till bladet **nätverk** på Azure Portal för din MySQL-flexibla Server och markera kryss rutan under **brand Väggs** avsnittet som säger "Tillåt offentlig åtkomst från valfri Azure-tjänst i Azure till den här servern", som visas på skärm bilden nedan och klicka på Spara för att spara inställningen.

 > :::image type="content" source="./media/quickstart-create-server-portal/allow-access-to-any-azure-service.png" alt-text="Skärm bild som visar hur du tillåter Azure Cloud Shell åtkomst till MySQL-flexibel Server för nätverks konfiguration för offentlig åtkomst.":::

> [!NOTE]
> Kontroll av alternativet **Tillåt offentlig åtkomst från alla Azure-tjänster i Azure till den här servern** bör endast användas för utveckling eller testning. Den konfigurerar brand väggen så att den tillåter anslutningar från IP-adresser som tilldelats till en Azure-tjänst eller till gång, inklusive anslutningar från andra kunders prenumerationer.

Klicka på **försök** att starta Azure Cloud Shell och Använd följande kommandon för att ansluta till din flexibla Server. Använd Server namnet, användar namnet och lösen ordet i kommandot. 

```azurecli-interactive
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!IMPORTANT]
> När du ansluter till en flexibel server med hjälp av Azure Cloud Shell måste du använda parametern--SSL = True och inte--SSL-mode = nödvändig.
> Den primära orsaken är Azure Cloud Shell levereras med förinstallerad mysql.exe-klient från MariaDB-distribution som kräver parametern--SSL medan mysql-klienten från Oracle-distributionen kräver parametern--SSL-mode.

Om du ser följande fel meddelande när du ansluter till den flexibla servern efter kommandot tidigare missade du inte att ange brand Väggs regeln med alternativet "Tillåt offentlig åtkomst från alla Azure-tjänster i Azure till den här servern" ovan, eller så har alternativet inte sparats. Försök att ställa in brand väggen igen och försök igen.

FEL 2002 (HY000): det går inte att ansluta till MySQL server på <servername> (115)

## <a name="clean-up-resources"></a>Rensa resurser
Nu har du skapat en Azure Database for MySQL flexibel server i en resurs grupp. Om du inte tror att du behöver dessa resurser i framtiden kan du ta bort dem genom att ta bort resurs gruppen, eller så kan du bara ta bort MySQL-servern. Slutför följande steg för att ta bort resurs gruppen:

1. I Azure Portal söker du efter och väljer **resurs grupper**.
1. I listan över resurs grupper väljer du namnet på din resurs grupp.
1. På **översikts** sidan för resurs gruppen väljer du **ta bort resurs grupp**.
1. I bekräftelse dialog rutan skriver du namnet på din resurs grupp och väljer sedan **ta bort**.

Om du vill ta bort servern kan du välja **ta bort** på sidan **Översikt** för servern, som du ser här:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="Skärm bild som visar hur du tar bort en server.":::

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Bygg en PHP-webbapp (Laravel) med MySQL](tutorial-php-database-app.md)
