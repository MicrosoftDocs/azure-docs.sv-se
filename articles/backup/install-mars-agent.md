---
title: Installera Microsoft Azure Recovery Services (MARS)-agenten
description: Lär dig hur du installerar Microsoft Azure Recovery Services-agenten (MARS) för att säkerhetskopiera Windows-datorer.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: acf38fdf338fcdd0cd7902d4295f0f03310543a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98986878"
---
# <a name="install-the-azure-backup-mars-agent"></a>Installera Azure Backup MARS-agenten

Den här artikeln beskriver hur du installerar Microsoft Azure Recovery Services (MARS)-agenten. MARS kallas även Azure Backup agenten.

## <a name="about-the-mars-agent"></a>Om MARS-agenten

Azure Backup använder MARS-agenten för att säkerhetskopiera filer, mappar och system tillstånd från lokala datorer och virtuella Azure-datorer. Dessa säkerhets kopior lagras i ett Recovery Services valv i Azure. Du kan köra agenten:

* Direkt på lokala Windows-datorer. De här datorerna kan säkerhetskopiera direkt till ett Recovery Services valv i Azure.
* På virtuella Azure-datorer som kör Windows sida vid sida med tillägget för Azure VM-säkerhetskopiering. Agenten säkerhetskopierar vissa filer och mappar på den virtuella datorn.
* På en Microsoft Azure Backup Server-instans (MABS) eller en System Center Data Protection Manager-Server (DPM). I det här scenariot kan datorer och arbets belastningar säkerhetskopiera till MABS eller Data Protection Manager. Sedan använder MABS eller Data Protection Manager MARS-agenten för att säkerhetskopiera till ett valv i Azure.

Vilka data som är tillgängliga för säkerhets kopiering beror på var agenten är installerad.

> [!NOTE]
> I allmänhet säkerhetskopierar du en virtuell Azure-dator med hjälp av ett Azure Backup tillägg på den virtuella datorn. Den här metoden säkerhetskopierar hela den virtuella datorn. Om du vill säkerhetskopiera vissa filer och mappar på den virtuella datorn installerar du och använder MARS-agenten tillsammans med tillägget. Mer information finns i [arkitekturen för en inbyggd virtuell Azure VM-säkerhetskopiering](backup-architecture.md#architecture-built-in-azure-vm-backup).

![Steg i säkerhets kopierings processen](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Innan du börjar

* Lär dig hur [Azure Backup använder mars-agenten för att säkerhetskopiera Windows-datorer](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
* Lär dig mer om den [säkerhets kopierings arkitektur](backup-architecture.md#architecture-back-up-to-dpmmabs) som kör mars-agenten på en sekundär MABS eller Data Protection Manager Server.
* Granska [vad som stöds och vad du kan säkerhetskopiera](backup-support-matrix-mars-agent.md) av mars-agenten.
* Se till att du har ett Azure-konto om du behöver säkerhetskopiera en server eller klient till Azure. Om du inte har något konto kan du skapa ett [kostnads fritt](https://azure.microsoft.com/free/) på bara några minuter.
* Kontrol lera Internet åtkomst på de datorer som du vill säkerhetskopiera.
* Se till att användaren som utför installationen och konfigurationen av MARS-agenten har lokal administratörs behörighet på servern som ska skyddas.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Ändra Storage Replication

Som standard använder valven [Geo-redundant lagring (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage).

* Om valvet är din primära mekanism för säkerhets kopiering rekommenderar vi att du använder GRS.
* Du kan använda [Lokalt Redundant lagring (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) för att minska kostnaderna för Azure Storage.

Så här ändrar du typen av lagrings replikering:

1. I det nya valvet väljer du **Egenskaper** under avsnittet **Inställningar** .

1. På sidan **Egenskaper** under **säkerhets kopierings konfiguration** väljer du **Uppdatera**.

1. Välj typ av lagrings replikering och välj **Spara**.

    ![Uppdatera säkerhets kopierings konfiguration](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Du kan inte ändra typen av lagringsprovider när valvet har kon figurer ATS och innehåller säkerhets kopierings objekt. Om du vill göra det måste du återskapa valvet.
>

### <a name="verify-internet-access"></a>Verifiera Internet åtkomst

Om datorn har begränsad Internet åtkomst kontrollerar du att brand Väggs inställningarna på datorn eller proxyservern tillåter följande URL: er och IP-adresser:

* Webbadresser
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP-adresser
  * 20.190.128.0/18
  * 40.126.0.0/18

### <a name="use-azure-expressroute"></a>Använd Azure-ExpressRoute

Du kan säkerhetskopiera dina data via Azure ExpressRoute med hjälp av offentlig peering (tillgängligt för gamla kretsar) och Microsoft-peering. Säkerhets kopiering över privat peering stöds inte.

Om du vill använda offentlig peering bör du först se till att du har åtkomst till följande domäner och adresser:

* `http://www.msftncsi.com/ncsi.txt`
* `http://www.msftconnecttest.com/connecttest.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`
* IP-adresser
  * 20.190.128.0/18
  * 40.126.0.0/18

Om du vill använda Microsoft-peering väljer du följande tjänster, regioner och relevanta community-värden:

* Azure Active Directory (12076:5060)
* Azure-region, enligt platsen för ditt Recovery Services-valv
* Azure Storage, enligt platsen för ditt Recovery Services-valv

Mer information finns i [krav för ExpressRoute-routning](../expressroute/expressroute-routing.md).

> [!NOTE]
> Offentlig peering är föråldrad för nya kretsar.

Alla föregående URL: er och IP-adresser använder HTTPS-protokollet på port 443.

### <a name="private-endpoints"></a>Privata slutpunkter

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="download-the-mars-agent"></a>Hämta MARS-agenten

Hämta MARS-agenten så att du kan installera den på de datorer som du vill säkerhetskopiera.

Om du redan har installerat agenten på alla datorer kontrollerar du att du kör den senaste versionen av agenten. Hitta den senaste versionen i portalen eller gå direkt till [nedladdningen](https://aka.ms/azurebackup_agent).

1. I valvet, under **komma igång**, väljer du **säkerhets kopiering**.

    ![Öppna säkerhets kopierings målet](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. Under **var ska din arbets belastning köras?** väljer du **lokalt**. Välj det här alternativet även om du vill installera MARS-agenten på en virtuell Azure-dator.
1. Under **vad vill du säkerhetskopiera? väljer du** **filer och mappar**. Du kan också välja **system tillstånd**. Det finns många andra alternativ, men de här alternativen stöds bara om du kör en sekundär säkerhets kopierings Server. Välj **Förbered infrastruktur**.

    ![Konfigurera filer och mappar](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. Hämta MARS-agenten under **installera Recovery Services agent** för att **förbereda infrastrukturen**.

    ![Förbered infrastrukturen](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. I menyn Hämta väljer du **Spara**. Som standard sparas filen *MARSagentinstaller.exe* i mappen för nedladdningar.

1. Välj **redan hämta eller Använd den senaste Recovery Services agenten** och hämta sedan autentiseringsuppgifterna för valvet.

    ![Ladda ned autentiseringsuppgifter för valvet](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. Välj **Spara**. Filen laddas ned till mappen Hämtade filer. Du kan inte öppna filen med autentiseringsuppgifter för valvet.

## <a name="install-and-register-the-agent"></a>Installera och registrera agenten

1. Kör *MARSagentinstaller.exe* -filen på de datorer som du vill säkerhetskopiera.
1. I installations guiden för MARS agent väljer du **installations inställningar**. Där kan du välja var du vill installera agenten och välja en plats för cachen. Välj sedan **Nästa**.
   * Azure Backup använder cacheminnet för att lagra ögonblicks bilder av data innan de skickas till Azure.
   * Cacheplatsen måste ha ett ledigt utrymme som motsvarar minst 5 procent av storleken på de data som du säkerhetskopierar.

    ![Välj installations inställningar i installations guiden för MARS-agenten](./media/backup-configure-vault/mars1.png)

1. För **proxykonfiguration** anger du hur agenten som körs på Windows-datorn ska ansluta till Internet. Välj sedan **Nästa**.

   * Om du använder en anpassad proxy anger du eventuella nödvändiga proxyinställningar och autentiseringsuppgifter.
   * Kom ihåg att agenten behöver åtkomst till [vissa URL: er](#before-you-start).

    ![Konfigurera Internet åtkomst i MARS-guiden](./media/backup-configure-vault/mars2.png)

1. För **installation**, granska kraven och välj **Installera**.
1. När agenten har installerats väljer **du Fortsätt till registrering**.
1. I Guide identifiering för valv i **guiden Registrera Server**  >  bläddrar du till och väljer den inloggnings fil som du laddade ned. Välj sedan **Nästa**.

    ![Lägg till autentiseringsuppgifter för valvet med guiden Registrera Server](./media/backup-configure-vault/register1.png)

1. På sidan **krypterings inställning** anger du en lösen fras som ska användas för att kryptera och dekryptera säkerhets kopior för datorn. Mer information om tillåtna lösen Frass tecken [finns här](backup-azure-file-folder-backup-faq.md#what-characters-are-allowed-for-the-passphrase) .

    * Spara lösen frasen på en säker plats. Du behöver den för att återställa en säkerhets kopia.
    * Om du tappar bort lösen frasen kan Microsoft inte hjälpa dig att återställa säkerhets kopierings data.

1. Välj **Slutför**. Agenten är nu installerad och datorn är registrerad på valvet. Nu kan du konfigurera och schemalägga säkerhetskopieringen.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [säkerhetskopierar Windows-datorer med hjälp av Azure Backup mars-agenten](backup-windows-with-mars-agent.md)
