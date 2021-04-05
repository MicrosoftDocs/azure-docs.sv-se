---
title: Azure VMware-lösning av CloudSimple – konfigurera DNS för CloudSimple privat moln
description: Beskriver hur du konfigurerar DNS-namnmatchning för åtkomst till vCenter Server i ett privat CloudSimple-moln från lokala arbets stationer
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 63822050512421895b0cfed08fb141f77da20b03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899260"
---
# <a name="configure-dns-for-name-resolution-for-private-cloud-vcenter-access-from-on-premises-workstations"></a>Konfigurera DNS för namn matchning för privat moln vCenter-åtkomst från lokala arbets stationer

För att få åtkomst till vCenter-servern i ett CloudSimple privat moln från lokala arbets stationer måste du konfigurera DNS-namnupplösning så att vCenter-servern kan åtgärdas via värdnamn och IP-adress.

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-private-cloud"></a>Hämta IP-adressen för DNS-servern för ditt privata moln

1. Logga in på [CloudSimple-portalen](access-cloudsimple-portal.md).

2. Navigera till **resurser**  >  **privata moln** och välj det privata moln som du vill ansluta till.

3. På sidan **Sammanfattning** i det privata molnet under **grundläggande information** kopierar du IP-adressen för den privata molnets DNS-server.

    ![DNS-servrar för privata moln](media/private-cloud-dns-server.png)


Använd något av dessa alternativ för DNS-konfigurationen.

* [Skapa en zon på DNS-servern för *. cloudsimple.io](#create-a-zone-on-a-microsoft-windows-dns-server)
* [Skapa en villkorlig vidarebefordrare på din lokala DNS-server för att lösa *. cloudsimple.io](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>Skapa en zon på DNS-servern för *. cloudsimple.io

Du kan konfigurera en zon som en stub-zon och peka på DNS-servrarna i det privata molnet för namn matchning. Det här avsnittet innehåller information om hur du använder en DNS-server (BIND) eller en Microsoft Windows DNS-server.

### <a name="create-a-zone-on-a-bind-dns-server"></a>Skapa en zon på en BIND DNS-Server

Vilken fil och vilka parametrar som ska konfigureras kan variera beroende på din enskilda DNS-konfiguration.

Om du till exempel vill använda standard konfigurationen för BIND-servern redigerar du/etc/named.conf-filen på DNS-servern och lägger till följande zon information.

> [!NOTE]
>Den här artikeln innehåller referenser till termen Slav, en term som Microsoft inte längre använder. När termen tas bort från program varan tar vi bort det från den här artikeln.

```
zone "az.cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>Skapa en zon på en Microsoft Windows DNS-Server

1. Högerklicka på DNS-servern och välj **ny zon**. 
  
    ![Skärm bild som markerar meny alternativet ny zon.](media/DNS01.png)
2. Välj **stub-zon** och klicka på **Nästa**.

    ![Skärm bild som visar alternativet stub-zon.](media/DNS02.png)
3. Välj lämpligt alternativ beroende på din miljö och klicka på **Nästa**.

    ![Skärm bild som visar alternativen för zondad data replikering.](media/DNS03.png)
4. Välj **zon för vanlig sökning** och klicka på **Nästa**.

    ![Skärm bild som markerar alternativet för zon för vanlig sökning.](media/DNS01.png)
5. Ange zon namnet och klicka på **Nästa**.

    ![Skärm bild som visar var du anger namnet på zonen.](media/DNS05.png)
6. Ange IP-adresserna för DNS-servrarna för ditt privata moln som du har fått från CloudSimple-portalen.

    ![Ny zon](media/DNS06.png)
7. Klicka på **Nästa** om du vill slutföra installationen av guiden.

## <a name="create-a-conditional-forwarder"></a>Skapa en villkorlig vidarebefordrare

En villkorlig vidarebefordrare vidarebefordrar alla förfrågningar om DNS-namnmatchning till den angivna servern. Med den här installationen vidarebefordras alla förfrågningar till *. cloudsimple.io till DNS-servrarna som finns i det privata molnet. I följande exempel visas hur du konfigurerar vidarebefordrare på olika typer av DNS-servrar.

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>Skapa en villkorlig vidarebefordrare på en BIND DNS-Server

Vilken fil och vilka parametrar som ska konfigureras kan variera beroende på din enskilda DNS-konfiguration.

Om du till exempel vill använda standard konfigurationen för BIND-servern redigerar du/etc/named.conf-filen på din DNS-server och lägger till följande information om villkorlig vidarebefordran.

```
zone "az.cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>Skapa en villkorlig vidarebefordrare på en Microsoft Windows DNS-Server

1. Öppna DNS-hanteraren på DNS-servern.
2. Högerklicka på **villkorliga vidarebefordrare** och välj alternativet för att lägga till en ny villkorlig vidarebefordrare.

    ![Villkorlig vidarebefordrare 1 Windows DNS](media/DNS08.png)
3. Ange DNS-domänen och IP-adressen för DNS-servrarna i det privata molnet och klicka på **OK**.
