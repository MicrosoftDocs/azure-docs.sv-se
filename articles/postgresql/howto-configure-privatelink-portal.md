---
title: Privat länk – Azure Portal-Azure Database for PostgreSQL-enskild server
description: Lär dig hur du konfigurerar en privat länk för Azure Database for PostgreSQL-enskild server från Azure Portal
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 01/09/2020
ms.openlocfilehash: 75defcf88128e1b100e403d74f76577525f0bdeb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105642121"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-using-portal"></a>Skapa och hantera privat länk för Azure Database for PostgreSQL-en server med hjälp av portalen

En privat slut punkt är det grundläggande Bygg blocket för privat länk i Azure. Den gör det möjligt för Azure-resurser, t. ex. Virtual Machines (VM), att kommunicera privat med privata länk resurser.  I den här artikeln får du lära dig hur du använder Azure Portal för att skapa en virtuell dator i en Azure-Virtual Network och en Azure Database for PostgreSQL enskild server med en privat Azure-slutpunkt.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

> [!NOTE]
> Funktionen privat länk är bara tillgänglig för Azure Database for PostgreSQL servrar i Generell användning eller Minnesoptimerade pris nivåer. Se till att databas servern är på någon av dessa pris nivåer.

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-an-azure-vm"></a>Skapa en virtuell Azure-dator

I det här avsnittet ska du skapa ett virtuellt nätverk och under nätet som är värd för den virtuella datorn som används för att komma åt din privata länk resurs (en PostgreSQL-server i Azure).

### <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket
I det här avsnittet ska du skapa en Virtual Network och under nätet som är värd för den virtuella datorn som används för åtkomst till din privata länk resurs.

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs**  >  **nätverk**  >  **virtuellt nätverk**.
2. I **Skapa virtuellt nätverk** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *MyVirtualNetwork*. |
    | Adressutrymme | Ange *10.1.0.0/16*. |
    | Prenumeration | Välj din prenumeration.|
    | Resursgrupp | Välj **Skapa ny**, ange *myResourceGroup* och välj sedan **OK**. |
    | Location | Välj **Europa, västra**.|
    | Undernät – Namn | Ange *undernät*. |
    | Undernät – adressintervall | Ange *10.1.0.0/24*. |
    |||
3. Lämna resten som standard och välj **skapa**.

### <a name="create-virtual-machine"></a>Skapa virtuell dator

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa en resurs**  >  **beräknings**  >  **virtuell dator**.

2. I **Skapa en virtuell dator – grunder** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **PROJEKT INFORMATION** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade det i föregående avsnitt.  |
    | **INSTANS INFORMATION** |  |
    | Namn på virtuell dator | Ange *myVm*. |
    | Region | Välj **Europa, västra**. |
    | Alternativ för tillgänglighet | Lämna standard **ingen redundans för infrastruktur krävs**. |
    | Bild | Välj **Windows Server 2019 Data Center**. |
    | Storlek | Lämna standard **ds1 v2** som standard. |
    | **ADMINISTRATÖRSKONTO** |  |
    | Användarnamn | Ange ett användar namn som du väljer. |
    | Lösenord | Ange ett valfritt lösenord. Lösen ordet måste vara minst 12 tecken långt och uppfylla de [definierade komplexitets kraven](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Bekräfta lösenord | Ange lösenordet igen. |
    | **REGLER FÖR INKOMMANDE PORTAR** |  |
    | Offentliga inkommande portar | Lämna standardvärdet **none**. |
    | **SPARA PENGAR** |  |
    | Har du redan en Windows-licens? | Lämna standardvärdet **Nej**. |
    |||

1. Välj **Nästa: diskar**.

1. I **Skapa en virtuell dator – diskar** lämnar du standardinställningarna och väljer **Nästa: Nätverk**.

1. I **Skapa en virtuell dator – Nätverk** väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Virtuellt nätverk | Lämna standard **MyVirtualNetwork**.  |
    | Adressutrymme | Lämna standard **10.1.0.0/24**.|
    | Undernät | Lämna standard **under nätet (10.1.0.0/24)**.|
    | Offentlig IP-adress | Lämna standardvärdet **(New) myVm-IP**. |
    | Offentliga inkommande portar | Välj **Tillåt valda portar**. |
    | Välj inkommande portar | Välj **HTTP** och **RDP**.|
    |||


1. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** där Azure verifierar din konfiguration.

1. När du ser ett meddelande som anger att **valideringen har slutförts** klickar du på **Skapa**.

> [!NOTE]
> I vissa fall finns Azure Database for PostgreSQL och VNet-under nätet i olika prenumerationer. I dessa fall måste du se till att följande konfigurationer:
> - Se till att båda prenumerations resurs leverantören för **Microsoft. DBforPostgreSQL** är registrerad. Mer information hittar du i [Resource Manager-Registration][resource-manager-portal]

## <a name="create-an-azure-database-for-postgresql-single-server"></a>Skapa en Azure Database for PostgreSQL enskild server

I det här avsnittet ska du skapa en Azure Database for PostgreSQL-server i Azure. 

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa en resurs**  >  **databas**  >  **Azure Database for PostgreSQL**.

1. I **Azure Database for PostgreSQL distributions alternativ väljer du** **enskild server** och anger följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade det i föregående avsnitt.|
    | **Serverinformation** |  |
    |Servernamn  | Ange *Server*. Om det här namnet tas skapar du ett unikt namn.|
    | Användarnamn för administratör| Ange ett administratörs namn som du väljer. |
    | Lösenord | Ange ett valfritt lösenord. Lösen ordet måste vara minst 8 tecken långt och uppfylla de definierade kraven. |
    | Location | Välj en Azure-region där du vill att PostgreSQL-servern ska finnas. |
    |Version  | Välj den databas version av PostgreSQL-servern som krävs.|
    | Compute + Storage| Välj den pris nivå som krävs för servern baserat på arbets belastningen. |
    |||
 
7. Välj **OK**. 
8. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** där Azure verifierar din konfiguration. 
9. När du ser meddelandet valideringen har skickats väljer du **skapa**. 
10. När du ser meddelandet valideringen har skickats väljer du skapa. 

## <a name="create-a-private-endpoint"></a>Skapa en privat slutpunkt

I det här avsnittet ska du skapa en PostgreSQL-Server och lägga till en privat slut punkt till den. 

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa en resurs**  >  **nätverk**  >  **privat länk**.
2. I **privat länk Center – översikt**, på alternativet för att **skapa en privat anslutning till en tjänst**, väljer du **Start**.

    :::image type="content" source="media/concepts-data-access-and-security-private-link/privatelink-overview.png" alt-text="Översikt över privat länk":::

1. I **skapa en privat slut punkt – grunderna** anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade det i föregående avsnitt.|
    | **Instans information** |  |
    | Name | Ange *myPrivateEndpoint*. Om det här namnet tas skapar du ett unikt namn. |
    |Region|Välj **Europa, västra**.|
    |||
5. Välj **Nästa: resurs**.
6. I **skapa en privat slut punkt – resurs**, anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    |Anslutningsmetod  | Välj Anslut till en Azure-resurs i min katalog.|
    | Prenumeration| Välj din prenumeration. |
    | Resurstyp | Välj **Microsoft. DBforPostgreSQL/servers**. |
    | Resurs |Välj *Server*|
    |Målunderresurs |Välj *postgresqlServer*|
    |||
7. Välj **Nästa: konfiguration**.
8. I **skapa en privat slut punkt – konfiguration** anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    |**NÄTVERK**| |
    | Virtuellt nätverk| Välj *MyVirtualNetwork*. |
    | Undernät | Välj *undernät*. |
    |**PRIVAT DNS-INTEGRATION**||
    |Integrera med privat DNS-zon |Välj **Ja**. |
    |Privat DNS-zon |Välj *(ny) privatelink. postgres. Database. Azure. com* |
    |||

    > [!Note] 
    > Använd den fördefinierade privata DNS-zonen för din tjänst eller ange önskat DNS-zonnamn. Mer information finns i [konfiguration av DNS-zoner för Azure-tjänster](../private-link/private-endpoint-dns.md) .

1. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** där Azure verifierar din konfiguration. 
2. När du ser ett meddelande som anger att **valideringen har slutförts** klickar du på **Skapa**. 

    :::image type="content" source="media/concepts-data-access-and-security-private-link/show-postgres-private-link.png" alt-text="Privat länk har skapats":::

    > [!NOTE] 
    > Det fullständiga domännamnet i DNS-inställningen för kunden matchar inte den privata IP-adressen som har konfigurerats. Du måste konfigurera en DNS-zon för den konfigurerade FQDN: en som visas [här](../dns/dns-operations-recordsets-portal.md).

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Ansluta till en virtuell dator med hjälp av Fjärrskrivbord (RDP)


När du har skapat **myVm** ansluter du till den från Internet på följande sätt: 

1. I portalens sökfältet anger du *myVm*.

1. Välj knappen **Anslut**. När du har valt knappen **Anslut** öppnas **Anslut till den virtuella datorn**.

1. Välj **Hämta RDP-fil**. Azure skapar en Remote Desktop Protocol-fil (*. RDP*) och laddar ned den till datorn.

1. Öppna den *nedladdade RDP* -filen.

    1. Välj **Anslut** om du uppmanas att göra det.

    1. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn.

        > [!NOTE]
        > Du kan behöva välja **fler alternativ**  >  **Använd ett annat konto** för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn.

1. Välj **OK**.

1. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** om du får en certifikatvarning.

1. När virtuella datorns skrivbord visas kan du minimera det att gå tillbaka till din lokala dator.

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>Få åtkomst till PostgreSQL-servern privat från den virtuella datorn

1. Öppna PowerShell i fjärr skrivbordet för *myVM*.

2. Ange  `nslookup mydemopostgresserver.privatelink.postgres.database.azure.com` . 

    Du får ett meddelande som liknar detta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemopostgresserver.privatelink.postgres.database.azure.com
    Address:  10.1.3.4
    ```

3. Testa anslutningen till den privata länken för PostgreSQL-servern med valfri tillgänglig klient. I exemplet nedan har jag använt [Azure Data Studio](/sql/azure-data-studio/download) för att utföra åtgärden.

4. I **ny anslutning** anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | Servertyp| Välj **postgresql**.|
    | Servernamn| Välj *mydemopostgresserver.privatelink.postgres.Database.Azure.com* |
    | Användarnamn | Ange användar namn som username@servername anges när postgresql-servern skapas. |
    |Lösenord |Ange ett lösen ord som angavs när PostgreSQL-servern skapades. |
    |SSL|Välj **obligatoriskt**.|
    ||

5. Välj Anslut.

6. Bläddra bland databaser från menyn till vänster.

7. Du kan också Skapa eller fråga efter information från postgreSQL-servern.

8. Stäng fjärr skrivbords anslutningen till myVm.

## <a name="clean-up-resources"></a>Rensa resurser
När du är klar med den privata slut punkten, PostgreSQL-servern och den virtuella datorn tar du bort resurs gruppen och alla resurser den innehåller:

1. Skriv *myResourceGroup* i **sökrutan längst** upp i portalen och välj  *myResourceGroup*   från Sök resultaten.
2. Välj **Ta bort resursgrupp**.
3. Ange myResourceGroup för **Skriv resurs gruppens namn** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här instruktionen har du skapat en virtuell dator i ett virtuellt nätverk, en Azure Database for PostgreSQL-enskild server och en privat slut punkt för privat åtkomst. Du har anslutit till en virtuell dator från Internet och kommunicerat på ett säkert sätt till PostgreSQL-servern med hjälp av en privat länk. Mer information om privata slut punkter finns i [Vad är en privat Azure-slutpunkt](../private-link/private-endpoint-overview.md).

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md