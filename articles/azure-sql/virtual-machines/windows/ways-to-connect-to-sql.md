---
title: Ansluta till en SQL Server virtuell dator (Resource Manager) | Microsoft Docs
description: Lär dig hur du ansluter till din SQL Server virtuella dator på Azure. I det här avsnittet används den klassiska distributions modellen. Scenarierna varierar beroende på nätverks konfigurationen och klientens plats.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 28b68178b98e53b7a7d4192ad20c05a667344969
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356743"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Ansluta till en SQL Server virtuell dator på Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

## <a name="overview"></a>Översikt

I det här avsnittet beskrivs hur du ansluter till din SQL på en virtuell Azure-dator (VM). Det täcker några [allmänna anslutnings scenarier](#connection-scenarios) och innehåller sedan [steg i portalen för att ändra anslutnings inställningar](#change). Om du behöver felsöka eller konfigurera anslutningar utanför portalen kan du läsa den [manuella konfigurationen](#manual) i slutet av det här avsnittet. 

Om du hellre vill ha en fullständig genom gång av både etablering och anslutning, se [etablera en SQL Server virtuell dator på Azure](create-sql-vm-portal.md).

## <a name="connection-scenarios"></a>Anslutnings scenarier

Hur en klient ansluter till en SQL Server VM varierar beroende på klientens plats och nätverks konfigurationen.

Om du etablerar en SQL Server VM i Azure Portal har du möjlighet att ange typen av **SQL-anslutning**.

![Alternativ för offentlig SQL-anslutning under etableringen](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity.png)

Alternativen för anslutning är:

| Alternativ | Beskrivning |
|---|---|
| **Offentlig** | Anslut till SQL Server via Internet. |
| **Privat** | Anslut till SQL Server i samma virtuella nätverk. |
| **Inställningar** | Anslut till SQL Server lokalt på samma virtuella dator. | 

I följande avsnitt beskrivs **offentliga** och **privata** alternativ i detalj.

## <a name="connect-to-sql-server-over-the-internet"></a>Ansluta till SQL Server via Internet

Om du vill ansluta till din SQL Server databas motor från Internet väljer du **offentlig** för **SQL-anslutnings** typen i portalen under etableringen. Portalen utför automatiskt följande steg:

* Aktiverar TCP/IP-protokollet för SQL Server.
* Konfigurerar en brand Väggs regel för att öppna SQL Server TCP-port (standard 1433).
* Aktiverar SQL Server autentisering som krävs för offentlig åtkomst.
* Konfigurerar nätverks säkerhets gruppen på den virtuella datorn till all TCP-trafik på den SQL Server porten.

> [!IMPORTANT]
> Avbildningarna av virtuella datorer för SQL Server Developer-och Express-versioner aktiverar inte TCP/IP-protokollet automatiskt. För Developer-och Express-versioner måste du använda Konfigurationshanteraren för SQL Server för att [manuellt Aktivera TCP/IP-protokollet](#manualtcp) när du har skapat den virtuella datorn.

Alla klienter med Internet åtkomst kan ansluta till SQL Server-instansen genom att ange antingen den offentliga IP-adressen för den virtuella datorn eller en DNS-etikett som tilldelats den IP-adressen. Om SQL Server porten är 1433 behöver du inte ange den i anslutnings strängen. Följande anslutnings sträng ansluter till en virtuell SQL-dator med en DNS-etikett för att `sqlvmlabel.eastus.cloudapp.azure.com` använda SQL-autentisering (du kan också använda den offentliga IP-adressen).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Även om den här strängen möjliggör anslutning för klienter över Internet innebär detta inte att vem som helst kan ansluta till din SQL Server-instans. Externa klienter måste använda rätt användar namn och lösen ord. För ytterligare säkerhet kan du dock undvika den välkända porten 1433. Om du till exempel skulle konfigurera SQL Server att lyssna på port 1500 och upprätta rätt regler för brand vägg och nätverks säkerhets grupper, kan du ansluta genom att lägga till port numret till Server namnet. I följande exempel ändras föregående genom att lägga till ett anpassat port nummer, **1500**, till Server namnet:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> När du frågar SQL Server på en virtuell dator via Internet, omfattas alla utgående data från Azure-datacentret av normal [prissättning på utgående data överföringar](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Ansluta till SQL Server i ett virtuellt nätverk

När du väljer **privat** för **SQL-anslutnings** typen i portalen, konfigurerar Azure de flesta inställningar som är identiska med **offentliga**. Den enda skillnaden är att det inte finns någon regel för nätverks säkerhets grupper som tillåter yttre trafik på SQL Server porten (standard 1433).

> [!IMPORTANT]
> Avbildningarna av virtuella datorer för SQL Server Developer-och Express-versioner aktiverar inte TCP/IP-protokollet automatiskt. För Developer-och Express-versioner måste du använda Konfigurationshanteraren för SQL Server för att [manuellt Aktivera TCP/IP-protokollet](#manualtcp) när du har skapat den virtuella datorn.

Privat anslutning används ofta tillsammans med ett [virtuellt nätverk](../../../virtual-network/virtual-networks-overview.md), vilket möjliggör flera scenarier. Du kan ansluta virtuella datorer i samma virtuella nätverk, även om de virtuella datorerna finns i olika resurs grupper. Med en [plats-till-plats-VPN](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)kan du skapa en hybrid arkitektur som ansluter virtuella datorer med lokala nätverk och datorer.

Med virtuella nätverk kan du också ansluta dina virtuella Azure-datorer till en domän. Detta är det enda sättet att använda Windows-autentisering för att SQL Server. De andra anslutnings scenarierna kräver SQL-autentisering med användar namn och lösen ord.

Förutsatt att du har konfigurerat DNS i det virtuella nätverket kan du ansluta till din SQL Server-instans genom att ange namnet på den SQL Server VM datorn i anslutnings strängen. I följande exempel förutsätts att Windows-autentisering har kon figurer ATS och att användaren har beviljats åtkomst till SQL Server-instansen.

```
Server=mysqlvm;Integrated Security=true
```

## <a name="change-sql-connectivity-settings"></a><a id="change"></a> Ändra inställningar för SQL-anslutning

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Du kan ändra anslutnings inställningarna för den SQL Server virtuella datorn i Azure Portal.

1. I Azure Portal väljer du **virtuella SQL-datorer**.

2. Välj din SQL Server VM.

3. Under **Inställningar** väljer du **säkerhet**.

4. Ändra **SQL-anslutnings nivån** till inställningen som krävs. Du kan också använda det här avsnittet för att ändra SQL Server port eller inställningarna för SQL-autentisering.

   ![Ändra SQL-anslutning](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity-change.png)

5. Vänta några minuter på att uppdateringen ska slutföras.

   ![Uppdaterings meddelande för virtuell SQL-dator](./media/ways-to-connect-to-sql/sql-vm-updating-notification.png)

## <a name="enable-tcpip-for-developer-and-express-editions"></a><a id="manualtcp"></a> Aktivera TCP/IP för utvecklare och Express-utgåvor

När du ändrar SQL Server anslutnings inställningar aktiverar Azure inte automatiskt TCP/IP-protokollet för SQL Server Developer och Express-versioner. I anvisningarna nedan förklaras hur du aktiverar TCP/IP manuellt så att du kan fjärransluta via IP-adress.

Anslut först till SQL Server virtuella datorn med fjärr skrivbord.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Sedan aktiverar du TCP/IP-protokollet med **Konfigurationshanteraren för SQL Server**.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Anslut med SSMS

Följande steg visar hur du skapar en valfri DNS-etikett för din virtuella Azure-dator och sedan ansluter med SQL Server Management Studio (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="manual-configuration-and-troubleshooting"></a><a id="manual"></a> Manuell konfiguration och fel sökning

Även om portalen tillhandahåller alternativ för att automatiskt konfigurera anslutning, är det bra att veta hur du konfigurerar anslutningen manuellt. Att förstå kraven kan också under lätta fel sökning.

I följande tabell visas kraven för att ansluta till SQL Server på den virtuella Azure-datorn.

| Krav | Beskrivning |
|---|---|
| [Aktivera SQL Server autentiseringsläge](/sql/database-engine/configure-windows/change-server-authentication-mode#use-ssms) | SQL Server autentisering krävs för att fjärrans luta till den virtuella datorn om du inte har konfigurerat Active Directory på ett virtuellt nätverk. |
| [Skapa en SQL-inloggning](/sql/relational-databases/security/authentication-access/create-a-login) | Om du använder SQL-autentisering behöver du en SQL-inloggning med ett användar namn och lösen ord som också har behörighet till mål databasen. |
| [Aktivera TCP/IP-protokoll](#manualtcp) | SQL Server måste tillåta anslutningar via TCP. |
| [Aktivera brand Väggs regel för den SQL Server porten](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | Brand väggen på den virtuella datorn måste tillåta inkommande trafik på SQL Server porten (standard 1433). |
| [Skapa en regel för nätverks säkerhets grupp för TCP 1433](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | Du måste tillåta att den virtuella datorn tar emot trafik på SQL Server porten (standard 1433) om du vill ansluta via Internet. Lokala och virtuella-nätverks anslutningar kräver inte detta. Detta är det enda steg som krävs i Azure Portal. |

> [!TIP]
> Stegen i föregående tabell utförs åt dig när du konfigurerar anslutningen i portalen. Använd bara de här stegen för att bekräfta konfigurationen eller konfigurera anslutning manuellt för SQL Server.

## <a name="next-steps"></a>Nästa steg

Om du vill se etablerings instruktioner tillsammans med dessa anslutnings steg, se hur du [konfigurerar en SQL Server virtuell dator på Azure](create-sql-vm-portal.md).

Andra avsnitt som handlar om att köra SQL Server på virtuella Azure-datorer finns [SQL Server på Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md).