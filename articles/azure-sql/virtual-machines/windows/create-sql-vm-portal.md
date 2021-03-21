---
title: Etablera en virtuell Windows-dator med Azure Portal
description: Den här guiden omfattar alternativ som är tillgängliga för att använda Azure Portal för att etablera SQL Server på en virtuell Windows-dator.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 11/07/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: f0b7e04becd42180df3393d7036294c1629cdc21
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499500"
---
# <a name="how-to-use-the-azure-portal-to-provision-a-windows-virtual-machine-with-sql-server"></a>Använda Azure Portal för att etablera en virtuell Windows-dator med SQL Server

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Den här guiden omfattar alternativ som är tillgängliga för att använda Azure Portal för att etablera SQL Server på en virtuell Windows-dator (VM). Den här artikeln beskriver fler konfigurations alternativ än den [SQL Server VM snabb](sql-vm-create-portal-quickstart.md)starten, som fokuserar mer djupt på en enskild konfiguration. 

Använd den här guiden för att skapa en egen SQL Server VM. Eller Använd den som referens för de tillgängliga alternativen i Azure Portal.

> [!TIP]
> Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](frequently-asked-questions-faq.md).

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sql-server-virtual-machine-gallery-images"></a><a id="select"></a> SQL Server Galleri avbildningar för virtuella datorer

När du skapar en SQL Server virtuell dator kan du välja en av flera förkonfigurerade avbildningar från galleriet för virtuella datorer. Följande steg visar hur du väljer en av SQL Server 2017-avbildningarna.

1. Välj **Azure SQL** i den vänstra menyn i Azure Portal. Om **Azure SQL** inte finns i listan väljer du **alla tjänster** och skriver sedan *Azure SQL* i sökrutan. 

   Du kan också välja stjärnan bredvid **Azure SQL** för att spara den som en favorit och lägga till den som ett objekt i navigeringen till vänster. 

1. Välj **+ Lägg** till för att öppna **alternativ sidan Välj SQL-distribution** . Du kan visa mer information genom att välja **Visa information**. 
1. Skriv *2017* i rutan SQL Server avbildnings sökning på panelen **SQL Virtual Machines** och välj sedan **gratis SQL Server licens: SQL Server 2017 developer på Windows Server 2016** i list rutan. 

   ![Välj avbildning av virtuell SQL-dator](./media/create-sql-vm-portal/select-sql-vm-image-portal.png)

   > [!TIP]
   > Developer Edition används i den här artikeln eftersom det är en komplett, kostnads fri version av SQL Server för utvecklings testning. Du betalar endast för kostnaden för den VM som körs. Du kan dock välja vilken som helst av de avbildningar som ska användas i den här genom gången. En beskrivning av tillgängliga avbildningar finns i [Översikt över SQL Server virtuella Windows-datorer](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo).

   > [!TIP]
   > Licens kostnaderna för SQL Server ingår i priset per sekund för den virtuella dator som du skapar och varierar beroende på utgåva och kärnor. SQL Server Developers versionen är dock kostnads fri för utveckling och testning, inte produktion. SQL Express är dessutom kostnads fritt för lätta arbets belastningar (mindre än 1 GB minne, mindre än 10 GB lagrings utrymme). Du kan också hämta din egen licens (BYOL) och bara betala för den virtuella datorn. Dessa avbildningsnamn föregås av {BYOL}. 
   >
   > Mer information om alternativen finns i [Pricing guidance for SQL Server Azure VMs](pricing-guidance.md) (Prisvägledning för virtuella SQL Server Azure-datorer).


1. Välj **Skapa**.


## <a name="1-configure-basic-settings"></a>1. konfigurera grundläggande inställningar

Ange följande information på fliken **grundläggande** :

* Under **projekt information** kontrollerar du att rätt prenumeration har valts. 
* I avsnittet **resurs grupp** väljer du antingen en befintlig resurs grupp i listan eller så skapar du en ny resurs grupp genom att välja **Skapa ny** . En resursgrupp är en samling relaterade resurser i Azure (virtuella datorer, lagringskonton, virtuella nätverk osv.). 

  ![Prenumeration](./media/create-sql-vm-portal/basics-project-details.png)

  > [!NOTE]
  > En ny resursgrupp är praktiskt om du bara testar eller lär dig om SQL Server-distributioner i Azure. När du är klar med testet tar du bort resursgruppen. När du gör det tas den virtuella datorn och alla resurser som associeras med resursgruppen bort automatiskt. Mer information om resurs grupper finns i [Azure Resource Manager översikt](../../../active-directory-b2c/overview.md).


* Under **instans information**:

    1. Ange ett unikt **namn för den virtuella datorn**.  
    1. Välj en plats för din **region**. 
    1. I den här hand boken lämnar du **tillgänglighets alternativ** inställda på _ingen infrastrukturs-redundans krävs_. Om du vill veta mer om tillgänglighets alternativ, se [tillgänglighet](../../../virtual-machines/availability.md). 
    1. I listan **avbildning** väljer du _gratis SQL Server licens: SQL Server 2017-utvecklare på Windows Server 2016_.  
    1. Välj att **ändra storleken** **på den** virtuella datorn och välj **a2 Basic** -erbjudandet. Se till att rensa dina resurser när du är klar med dem för att förhindra eventuella oväntade kostnader. Vad gäller produktionsarbetsbelastningar hittar du rekommendationer för datorstorlek och konfiguration i [Prestandametodtips för SQL Server på virtuella Azure-datorer](performance-guidelines-best-practices.md).

    ![Instansinformation](./media/create-sql-vm-portal/basics-instance-details.png)

> [!IMPORTANT]
> Den uppskattade månadskostnaden som visas på sidan **Välj en storlek** omfattar inte SQL Server-licenskostnaden. Den här uppskattningen är endast kostnaden för den virtuella datorn. För Express-och Developer-utgåvorna av SQL Server är denna uppskattning den totala beräknade kostnaden. För andra utgåvor kan du se [sidan med priser för Windows Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) and och välja din utgåva av SQL Server. Se även [pris vägledningen för SQL Server virtuella Azure-datorer](pricing-guidance.md) och [storlekar för virtuella datorer](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Under **administratörs konto** anger du ett användar namn och lösen ord. Lösen ordet måste vara minst 12 tecken långt och uppfylla de [definierade komplexitets kraven](../../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Administratörskonto](./media/create-sql-vm-portal/basics-administrator-account.png)

* Under **regler för inkommande port** väljer du **Tillåt valda portar** och väljer sedan **RDP (3389)** i list rutan. 

   ![Regler för inkommande portar](./media/create-sql-vm-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. Konfigurera valfria funktioner

### <a name="disks"></a>Diskar

Konfigurera disk alternativen på fliken **diskar** . 

* Under **typ av operativ system disk** väljer du den typ av disk som du vill använda för ditt operativ system i list rutan. Premium rekommenderas för produktions system men är inte tillgängligt för en grundläggande virtuell dator. Ändra storleken på den virtuella datorn om du vill använda en Premium SSD. 
* Under **Avancerat** väljer du **ja** under Använd **Managed disks**.

   > [!NOTE]
   > Microsoft rekommenderar Managed Disks för SQL Server. Managed Disks hanterar lagring i bakgrunden. När virtuella datorer med Managed Disks finns i samma tillgänglighetsuppsättning, distribuerar Azure dessutom lagringsresurser för att tillhandahålla rätt redundans. Mer information finns i [Översikt över Azure Managed disks](../../../virtual-machines/managed-disks-overview.md). Mer information om hanterade diskar i en tillgänglighetsuppsättning finns i [Använda hanterade diskar för virtuella datorer i tillgänglighetsuppsättning](../../../virtual-machines/availability.md).

![Disk inställningar för virtuell SQL-dator](./media/create-sql-vm-portal/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Nätverk

Konfigurera nätverks alternativen på fliken **nätverk** . 

* Skapa ett nytt **virtuellt nätverk** eller Använd ett befintligt virtuellt nätverk för SQL Server VM. Ange även ett **undernät** . 

* Under **nätverkskort nätverks säkerhets grupp** väljer du antingen en grundläggande säkerhets grupp eller en avancerad säkerhets grupp. Om du väljer alternativet grundläggande kan du välja inkommande portar för SQL Server VM som har samma värden som har kon figurer ATS på fliken **grundläggande** . Genom att välja alternativet Avancerat kan du välja en befintlig nätverks säkerhets grupp eller skapa en ny. 

* Du kan göra andra ändringar i nätverks inställningarna eller behålla standardvärdena.

![Nätverks inställningar för SQL VM](./media/create-sql-vm-portal/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Övervakning

Konfigurera övervakning och automatisk avstängning på fliken **övervakning** . 

* Azure aktiverar **startdiagnostik** som standard med samma lagrings konto som har angetts för den virtuella datorn. På den här fliken kan du ändra dessa inställningar och aktivera **diagnostik för operativ systemets gäst**. 
* Du kan också aktivera **systemtilldelad hanterad identitet** och **Automatisk avstängning** på den här fliken. 

![Hanterings inställningar för virtuella SQL-datorer](./media/create-sql-vm-portal/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. Konfigurera SQL Server inställningar

Konfigurera vissa inställningar och optimeringar för SQL Server på fliken **SQL Server inställningar** . Du kan konfigurera följande inställningar för SQL Server:

- [Anslutningsmöjligheter](#connectivity)
- [Autentisering](#authentication)
- [Azure Key Vault-integrering](#azure-key-vault-integration)
- [Storage-konfiguration](#storage-configuration)
- [Automatiserad uppdatering](#automated-patching)
- [Automatisk säkerhets kopiering](#automated-backup)
- [Machine Learning Services](#machine-learning-services)


### <a name="connectivity"></a>Anslutning

Under **SQL-anslutning** anger du vilken typ av åtkomst du vill ha till SQL Server-instansen på den här virtuella datorn. I den här genom gången väljer du **offentlig (Internet)** för att tillåta att anslutningar SQL Server från datorer eller tjänster på Internet. När det här alternativet är markerat konfigurerar Azure automatiskt brand väggen och nätverks säkerhets gruppen för att tillåta trafik på den valda porten.

> [!TIP]
> Som standard lyssnar SQL Server på en känd port, **1433**. För ökad säkerhet kan du ändra porten så att den lyssnar på en icke-standardport, till exempel 1401, i den föregående dialogrutan. Om du ändrar porten måste du ansluta med den porten från alla klient verktyg, till exempel SQL Server Management Studio (SSMS).

![Säkerhet för virtuella SQL-datorer](./media/create-sql-vm-portal/azure-sqlvm-security.png)

Om du vill ansluta till SQL Server via Internet måste du också aktivera SQL Server-autentisering, som beskrivs i nästa avsnitt.

Om du föredrar att inte aktivera anslutningar till databasmotorn via Internet väljer du något av följande alternativ:

* **Lokalt (endast inuti VM)** om du bara vill tillåta anslutningar till SQL Server inifrån den virtuella datorn.
* **Privat (inom Virtual Network)** om du vill tillåta anslutningar till SQL Server från datorer eller tjänster i samma virtuella nätverk.

I allmänhet kan du förbättra säkerheten genom att välja den mest restriktiva anslutningen som ditt scenario medger. Men alla alternativ kan skyddas via regler för nätverks säkerhets grupper (NSG) och SQL/Windows-autentisering. Du kan redigera NSG när den virtuella datorn har skapats. Mer information finns i [Säkerhetsöverväganden för SQL Server på Azure Virtual Machines](security-considerations-best-practices.md).

### <a name="authentication"></a>Autentisering

Om du behöver SQL Server autentisering väljer du **Aktivera** under **SQL-autentisering** på fliken **SQL Server inställningar** .

![SQL Server-autentisering](./media/create-sql-vm-portal/azure-sqlvm-authentication.png)

> [!NOTE]
> Om du planerar att komma åt SQL Server via Internet (alternativet offentlig anslutning) måste du aktivera SQL-autentisering här. Offentlig åtkomst till SQL Server kräver SQL-autentisering.

Om du aktiverar SQL Server-autentisering anger du ett **inloggningsnamn** och **lösenord**. Det här inloggnings namnet är konfigurerat som en SQL Server inloggning för autentisering och en medlem i den fasta Server rollen **sysadmin** . Mer information om autentiseringslägen finns i [Välja ett autentiseringsläge](/sql/relational-databases/security/choose-an-authentication-mode).

Om du inte vill aktivera SQL Server autentisering kan du använda det lokala administratörs kontot på den virtuella datorn för att ansluta till SQL Server-instansen.

### <a name="azure-key-vault-integration"></a>Azure Key Vault-integrering

Om du vill lagra säkerhets hemligheter i Azure för kryptering väljer du **SQL Server inställningar** och bläddrar ned till  **Azure Key Vault-integrering**. Välj **Aktivera** och fyll i den begärda informationen. 

![Azure Key Vault-integrering](./media/create-sql-vm-portal/azure-sqlvm-akv.png)

I följande tabell visas de parametrar som krävs för att konfigurera Azure Key Vault-integration (AKV).

| PARAMETER | BESKRIVNING | EXEMPEL |
| --- | --- | --- |
| **Key Vault-URL** |Platsen för nyckelvalvet. |`https://contosokeyvault.vault.azure.net/` |
| **Huvudnamn** |Azure Active Directory-tjänstens huvudnamn. Det här namnet kallas också för klient-ID:t. |`fde2b411-33d5-4e11-af04eb07b669ccf2` |
| **Huvudhemlighet** |Azure Active Directory-tjänstens huvudhemlighet. Den här hemligheten kallas även för klienthemligheten. |`9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=` |
| **Namn på autentiseringsuppgifter** |**Namn på autentiseringsuppgift**: AKV-integreringen skapar en autentiseringsuppgift i SQL Server och ger den virtuella datorn åtkomst till nyckel valvet. Välj ett namn för autentiseringsuppgifterna. |`mycred1` |

Mer information finns i [Konfigurera Azure Key Vault-integrering för SQL Server på Azure Virtual Machines](azure-key-vault-integration-configure.md).

### <a name="storage-configuration"></a>Storage-konfiguration

På fliken **SQL Server inställningar** under **lagrings konfiguration** väljer du **ändra konfiguration** för att öppna sidan prestanda optimerad lagrings konfiguration och anger lagrings kraven.

![Skärm bild som visar var du kan ändra lagrings konfigurationen.](./media/create-sql-vm-portal/sql-vm-storage-configuration-provisioning.png)

Under **Storage optimerat för** väljer du något av följande alternativ:

* **Allmänt** är standardinställningen och har stöd för de flesta arbetsbelastningar.
* **Transaktions bearbetningen** optimerar lagringen för traditionella databas OLTP-arbetsbelastningar.
* **Datalagerhantering** optimerar lagringen för analys- och rapporteringsarbetsbelastningar.

![Konfiguration av SQL VM-lagring](./media/create-sql-vm-portal/sql-vm-storage-configuration.png)

Du kan välja att lämna värdena som standard, eller så kan du manuellt ändra lagrings sto pol Ogin så att den passar dina IOPS-behov. Mer information finns i [lagrings konfiguration](storage-configuration.md). 

### <a name="sql-server-license"></a>SQL Server licens

Om du är Software Assurance-kund kan du använda [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) för att ta med din egen SQL Server licens och spara resurser. 

![SQL VM-licens](./media/create-sql-vm-portal/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Automatisk uppdatering

**Automatisk uppdatering** är aktiverat som standard. Med inställningen Automatisk uppdatering kan Azure korrigera SQL Server och operativsystemet automatiskt. Ange en dag i veckan, en tid och längden på en underhållsperiod. Azure utför uppdateringar under den här underhållsperioden. Schemat för underhålls perioden använder VM-språkvarianten. Om du inte vill att Azure ska korrigera SQL Server och operativ systemet automatiskt väljer du **inaktivera**.  

![Automatisk uppdatering av SQL VM](./media/create-sql-vm-portal/azure-sqlvm-automated-patching.png)

Mer information finns i [Automatisk uppdatering av SQL Server i Azure Virtual Machines](automated-patching.md).

### <a name="automated-backup"></a>Automatisk säkerhetskopiering

Aktivera automatiska säkerhetskopieringar för alla databaser under **Automatisk säkerhetskopiering**. Automatisk säkerhetskopiering är inaktiverat som standard.

När du aktiverar automatisk SQL-säkerhetskopiering kan du konfigurera följande inställningar:

* Kvarhållningsperiod (dagar) för säkerhetskopieringar
* Lagringskonto som ska användas för säkerhetskopieringar
* Krypteringsalternativ och lösenord för säkerhetskopieringar
* Säkerhetskopiera systemdatabaser
* Konfigurera schema för säkerhetskopiering

Välj **Aktivera** för att kryptera säkerhets kopian. Ange sedan **lösenordet**. Azure skapar ett certifikat för att kryptera säkerhetskopiorna och använder det angivna lösenordet för att skydda certifikatet. Som standard anges schemat automatiskt, men du kan skapa ett manuellt schema genom att välja **manuell**. 

![Automatisk säkerhets kopiering av SQL VM](./media/create-sql-vm-portal/automated-backup.png)

Mer information finns i [Automatisk säkerhetskopiering av SQL Server i Azure Virtual Machines](automated-backup-sql-2014.md).


### <a name="machine-learning-services"></a>Machine Learning Services

Du kan välja att aktivera [Machine Learning Services](/sql/advanced-analytics/). Med det här alternativet kan du använda Machine Learning med python och R i SQL Server 2017. Välj **Aktivera** i fönstret **SQL Server inställningar** .


## <a name="4-review--create"></a>4. granska + skapa

På fliken **Granska + skapa** :
1. Granska sammanfattningen.
1. Välj **skapa** för att skapa SQL Server, resurs grupp och resurser som angetts för den här virtuella datorn.

Du kan övervaka distributionen från Azure Portal. Knappen **Meddelanden** längst upp på skärmen visar grundläggande status för distributionen.

> [!NOTE]
> Ett exempel på en tid för Azure att distribuera en SQL Server VM: ett test SQL Server VM som tillhandahålls till regionen USA, östra med standardinställningar tar cirka 12 minuter att slutföra. Du kan uppleva kortare eller långsammare distributions tider baserat på din region och de valda inställningarna.

## <a name="open-the-vm-with-remote-desktop"></a><a id="remotedesktop"></a> Öppna den virtuella datorn med fjärr skrivbord

Använd följande steg för att ansluta till den SQL Server virtuella datorn med Remote Desktop Protocol (RDP):

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

När du ansluter till den virtuella SQL Server-datorn kan du starta SQL Server Management Studio och ansluta med Windows-autentisering med hjälp av dina autentiseringsuppgifter som lokal administratör. Om du har aktiverat SQL Server-autentisering kan du också ansluta med SQL-autentisering med hjälp av SQL-inloggningsnamnet och SQL-lösenordet som du konfigurerade under etableringen.

När du har anslutit till datorn kan du direkt ändra inställningarna för datorn och SQL Server efter behov. Du kan till exempel konfigurera brandväggsinställningarna eller ändra konfigurationsinställningarna för SQL Server.

## <a name="connect-to-sql-server-remotely"></a><a id="connect"></a> Anslut till SQL Server via fjärr anslutning

I den här genom gången valde du **offentlig** åtkomst för den virtuella datorn och **SQL Server autentisering**. Dessa inställningar konfigurerade automatiskt den virtuella datorn så att SQL Server-anslutningar tillåts från alla klienter över Internet (förutsatt att de har rätt SQL-inloggningsuppgifter).

> [!NOTE]
> Om du inte valde Offentlig under etableringen kan du ändra SQL-anslutningsinställningarna via portalen efter etableringen. Mer information hittar du i [Ändra SQL-anslutningsinställningarna](ways-to-connect-to-sql.md#change).

I följande avsnitt visas hur du ansluter via Internet till din SQL Server VM-instans.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > I det här exemplet används den gemensamma porten 1433. Det här värdet måste dock ändras om en annan port (till exempel 1401) angavs under distributionen av SQL Server VM. 


## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder SQL Server i Azure finns i [SQL Server på Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md) och [Vanliga frågor och svar](frequently-asked-questions-faq.md).