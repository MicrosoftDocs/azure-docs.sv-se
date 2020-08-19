---
title: Installera MongoDB på en virtuell Windows-dator i Azure
description: Lär dig hur du installerar MongoDB på en virtuell Azure-dator som kör Windows Server 2012 R2 som skapats med Resource Manager-distributions modellen.
documentationcenter: ''
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: how-to
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: d02fa8fa23b587db06f3d2d1e08f0a8565471123
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510427"
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Installera och konfigurera MongoDB på en virtuell Windows-dator i Azure
[MongoDB](https://www.mongodb.org) är en populär NoSQL-databas med öppen källkod och hög prestanda. Den här artikeln vägleder dig genom installationen och konfigurationen av MongoDB på en virtuell Windows Server 2016-dator (VM) i Azure. Du kan också [Installera MongoDB på en virtuell Linux-dator i Azure](../linux/install-mongodb.md).

## <a name="prerequisites"></a>Krav
Innan du installerar och konfigurerar MongoDB måste du skapa en virtuell dator och helst lägga till en datadisk till den. Se följande artiklar för att skapa en virtuell dator och lägga till en data disk:

* Skapa en virtuell Windows Server-dator med hjälp [av Azure Portal](quick-create-portal.md) eller [Azure PowerShell](quick-create-powershell.md).
* Koppla en datadisk till en virtuell Windows Server-dator med hjälp [av Azure Portal](attach-managed-disk-portal.md) eller [Azure PowerShell](attach-disk-ps.md).

Om du vill börja installera och konfigurera MongoDB [loggar du in på den virtuella Windows Server-datorn](connect-logon.md) med hjälp av fjärr skrivbord.

## <a name="install-mongodb"></a>Installera MongoDB
> [!IMPORTANT]
> MongoDB säkerhetsfunktioner, t. ex. autentisering och IP-adress bindning, är inte aktiverade som standard. Säkerhetsfunktioner bör aktive ras innan du distribuerar MongoDB till en produktions miljö. Mer information finns i [MongoDB Security and Authentication](https://www.mongodb.org/display/DOCS/Security+and+Authentication).


1. När du har anslutit till den virtuella datorn med hjälp av fjärr skrivbord öppnar du Internet Explorer från aktivitets fältet.
2. Välj **Använd rekommenderade säkerhets-, sekretess-och kompatibilitetsinställningar** när Internet Explorer öppnas första gången och klicka på **OK**.
3. Förbättrad säkerhets konfiguration i Internet Explorer är aktiverat som standard. Lägg till MongoDB-webbplatsen i listan över tillåtna platser:

   * Välj **verktygs** ikonen i det övre högra hörnet.
   * I **Internet alternativ**väljer du fliken **säkerhet** och väljer sedan ikonen **Betrodda platser** .
   * Klicka på knappen **platser** . Lägg till *https:// \* . MongoDB.com* i listan över betrodda platser och stäng sedan dialog rutan.

     ![Konfigurera säkerhets inställningar för Internet Explorer](./media/install-mongodb/configure-internet-explorer-security.png)
4. Bläddra till sidan [MongoDB-downloads](https://www.mongodb.com/downloads) ( https://www.mongodb.com/downloads) .
5. Om det behövs väljer du **Community Server** -versionen och väljer sedan den senaste aktuella stabila versionen för*Windows Server 2008 R2 64-bitars och senare*. Klicka på **Ladda ned (MSI)** för att hämta installations programmet.

    ![Ladda ned MongoDB Installer](./media/install-mongodb/download-mongodb.png)

    Kör installations programmet när hämtningen är klar.
6. Läs och godkänn licens avtalet. När du uppmanas väljer du **Slutför** installationen.
7. Om du vill kan du även välja att installera kompass, ett grafiskt gränssnitt för MongoDB.
8. På den sista skärmen klickar du på **Installera**.

## <a name="configure-the-vm-and-mongodb"></a>Konfigurera VM och MongoDB
1. Path-variablerna uppdateras inte av installations programmet för MongoDB. Utan MongoDB `bin` -platsen i din PATH-variabel måste du ange den fullständiga sökvägen varje gången du använder en MongoDB körbar fil. Så här lägger du till platsen i din PATH-variabel:

   * Högerklicka på **Start** -menyn och välj **system**.
   * Klicka på **Avancerade systeminställningar**och klicka sedan på **miljövariabler**.
   * Under **Systemvariabler**väljer du **sökväg**och klickar sedan på **Redigera**.

     ![Konfigurera PATH-variabler](./media/install-mongodb/configure-path-variables.png)

     Lägg till sökvägen till MongoDB- `bin` mappen. MongoDB installeras vanligt vis i *C:\Program Files\MongoDB*. Verifiera installations Sök vägen på den virtuella datorn. I följande exempel läggs standard installations platsen för MongoDB till i `PATH` variabeln:

     ```
     ;C:\Program Files\MongoDB\Server\3.6\bin
     ```

     > [!NOTE]
     > Se till att lägga till det inledande semikolonet ( `;` ) för att indikera att du lägger till en plats i `PATH` variabeln.

2. Skapa MongoDB-data och logg kataloger på din datadisk. Från **Start** -menyn väljer du **kommando tolken**. I följande exempel skapas katalogerna på enhet F:

    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. Starta en MongoDB-instans med följande kommando, och justera sökvägen till dina data och logg kataloger i enlighet med detta:

    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```

    Det kan ta flera minuter för MongoDB att allokera Journal-filerna och börja lyssna efter anslutningar. Alla logg meddelanden dirigeras till *F:\MongoLogs\mongolog.log* -filen när `mongod.exe` servern startar och allokerar Journal-filer.

   > [!NOTE]
   > Kommando tolken är fokuserad på den här uppgiften medan MongoDB-instansen körs. Lämna kommando tolks fönstret öppet om du vill fortsätta att köra MongoDB. Du kan också installera MongoDB as service enligt beskrivningen i nästa steg.

4. För en mer robust MongoDB-upplevelse installerar du `mongod.exe` som en tjänst. Att skapa en tjänst innebär att du inte behöver lämna en kommando tolk som körs varje gång du vill använda MongoDB. Skapa tjänsten på följande sätt och justera sökvägen till dina data och logg kataloger i enlighet med detta:

    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
    ```

    Föregående kommando skapar en tjänst med namnet MongoDB, med en beskrivning av "Mongo DB". Följande parametrar anges också:

   * `--dbpath`Alternativet anger platsen för data katalogen.
   * `--logpath`Alternativet måste användas för att ange en loggfil, eftersom den aktiva tjänsten inte har något kommando fönster för att visa utdata.
   * `--logappend`Alternativet anger att en omstart av tjänsten leder till att utdata läggs till i den befintliga logg filen.

   Starta MongoDB-tjänsten genom att köra följande kommando:

    ```
    net start MongoDB
    ```

    Mer information om hur du skapar MongoDB-tjänsten finns i [Konfigurera en Windows-tjänst för MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>Testa MongoDB-instansen
När MongoDB körs som en enskild instans eller installeras som en tjänst kan du nu börja skapa och använda dina databaser. Starta MongoDB administrativ Shell genom att öppna ett annat kommando tolks fönster från **Start** -menyn och ange följande kommando:

```
mongo
```

Du kan lista databaserna med `db` kommandot. Infoga data på följande sätt:

```
db.foo.insert( { a : 1 } )
```

Sök efter data på följande sätt:

```
db.foo.find()
```

Utdata ser ut ungefär så här:

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

Avsluta `mongo` konsolen på följande sätt:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Konfigurera regler för brand vägg och nätverks säkerhets grupp
Nu när MongoDB är installerat och körs öppnar du en port i Windows-brandväggen så att du kan fjärrans luta till MongoDB. Om du vill skapa en ny regel för inkommande trafik för att tillåta TCP-port 27017, öppna en administrativ PowerShell-kommandotolk och ange följande kommando:

```powershell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

Du kan också skapa regeln med hjälp av grafiskt hanterings verktyg för **Windows-brandväggen med avancerad säkerhet** . Skapa en ny regel för inkommande trafik för att tillåta TCP-port 27017.

Om det behövs kan du skapa en regel för nätverks säkerhets gruppen för att tillåta åtkomst till MongoDB från utanför det befintliga Azure Virtual Network-undernätet. Du kan skapa regler för nätverks säkerhets grupper med hjälp av [Azure Portal](nsg-quickstart-portal.md) eller [Azure PowerShell](nsg-quickstart-powershell.md). Som med regler för Windows-brandväggen tillåter du TCP-port 27017 till det virtuella nätverks gränssnittet för den virtuella MongoDB-datorn.

> [!NOTE]
> TCP-port 27017 är den standard port som används av MongoDB. Du kan ändra den här porten med hjälp av `--port` parametern när du startar `mongod.exe` manuellt eller från en tjänst. Om du ändrar porten måste du uppdatera Windows-brandväggen och reglerna för nätverks säkerhets grupper i föregående steg.


## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig hur du installerar och konfigurerar MongoDB på din virtuella Windows-dator. Du kan nu komma åt MongoDB på din virtuella Windows-dator genom att följa de avancerade avsnitten i [MongoDB-dokumentationen](https://docs.mongodb.com/manual/).

