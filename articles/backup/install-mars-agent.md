---
title: Installera mars Microsoft Azure agenten (Recovery Services)
description: Lär dig hur du installerar Microsoft Azure Recovery Services-agenten (MARS) för att operativsystemet ska kunna äldsta till en windows-dator.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 3ea48aaa6aad4a51463c4c028ead22f31163f810
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519229"
---
# <a name="install-the-azure-backup-mars-agent"></a>Installera mars Azure Backup agenten

Den här artikeln förklarar hur du installerar Microsoft Azure Recovery Services-agenten (MARS). MARS kallas även Azure Backup agent.

## <a name="about-the-mars-agent"></a>Om MARS-agenten

Azure Backup använder MARS-agenten för att backa upp filer, mappar och systemtillstånd från lokala datorer och virtuella Azure-datorer. Dessa säkerhetskopior lagras i ett Recovery Services-valv i Azure. Du kan köra agenten:

* Direkt på lokala Windows-datorer. De här datorerna kan valva direkt till ett Recovery Services-valv i Azure.
* På virtuella Azure-datorer som kör Windows sida vid sida med säkerhetskopieringstillägget för virtuella Azure-datorer. Agenten backar upp specifika filer och mappar på den virtuella datorn.
* På en Microsoft Azure Backup Server-instans (MABS) eller en System Center Data Protection Manager (DPM)-server. I det här scenariot ar datorer och arbetsbelastningar tillbaka till MABS eller Data Protection Manager. Mabs eller Data Protection Manager sedan MARS-agenten för att valva upp till Azure.

Vilka data som är tillgängliga för säkerhetskopiering beror på var agenten är installerad.

> [!NOTE]
> I allmänhet kan du backa upp en virtuell Azure-dator med hjälp Azure Backup ett tillägg på den virtuella datorn. Den här metoden bygger upp hela den virtuella datorn. Om du vill backa upp specifika filer och mappar på den virtuella datorn installerar och använder du MARS-agenten tillsammans med tillägget. Mer information finns i Arkitektur [för en inbyggd säkerhetskopiering av virtuella Azure-datorer.](backup-architecture.md#architecture-built-in-azure-vm-backup)

![Steg för säkerhetskopieringsprocess](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Innan du börjar

* Lär dig [Azure Backup använder MARS-agenten för att backa Windows-datorer.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)
* Lär dig mer [om säkerhetskopieringsarkitekturen](backup-architecture.md#architecture-back-up-to-dpmmabs) som kör MARS-agenten på en sekundär MABS Data Protection Manager server.
* Gå [igenom vad som stöds och vad du kan backa upp](backup-support-matrix-mars-agent.md) av MARS-agenten.
* Kontrollera att du har ett Azure-konto om du behöver hanteraren för en server eller klient till Azure. Om du inte har något konto kan du skapa ett [kostnadsfritt på](https://azure.microsoft.com/free/) bara några minuter.
* Kontrollera Internetåtkomsten på de datorer som du vill backa upp.
* Se till att användaren som utför installationen och konfigurationen av MARS-agenten har lokal administratörsbehörighet på servern som ska skyddas.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Ändra lagringsreplikering

Som standard använder valv [geo-redundant lagring (GRS).](../storage/common/storage-redundancy.md#geo-redundant-storage)

* Om valvet är din primära säkerhetskopieringsmekanism rekommenderar vi att du använder GRS.
* Du kan använda [lokalt redundant lagring (LRS) för att](../storage/common/storage-redundancy.md#locally-redundant-storage) minska kostnaderna för Azure-lagring.

Så här ändrar du lagringsreplikeringstypen:

1. I det nya valvet väljer **du** Egenskaper under **avsnittet** Inställningar.

1. På sidan **Egenskaper** går du till **Konfiguration av säkerhetskopiering** och väljer **Uppdatera**.

1. Välj typ av lagringsreplikering och välj **Spara.**

    ![Uppdatera konfiguration av säkerhetskopiering](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Du kan inte ändra lagringsreplikeringstypen när valvet har ställts in och innehåller säkerhetskopieringsobjekt. Om du vill göra detta måste du skapa valvet på nytt.
>

### <a name="verify-internet-access"></a>Verifiera Internetåtkomst

Om datorn har begränsad Internetåtkomst ska du kontrollera att brandväggsinställningarna på datorn eller proxyservern tillåter följande URL:er och IP-adresser:

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

### <a name="use-azure-expressroute"></a>Använda Azure ExpressRoute

Du kan backa upp dina data Azure ExpressRoute med hjälp av offentlig peering (tillgänglig för gamla kretsar) och Microsoft-peering. Säkerhetskopiering via privat peering stöds inte.

Om du vill använda offentlig peering måste du först se till att du har åtkomst till följande domäner och adresser:

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
* Azure Storage, enligt platsen för Recovery Services-valvet

Mer information finns i [ExpressRoute-routningskrav.](../expressroute/expressroute-routing.md)

> [!NOTE]
> Offentlig peering är inaktuell för nya kretsar.

Alla föregående URL:er och IP-adresser använder HTTPS-protokollet på port 443.

### <a name="private-endpoints"></a>Privata slutpunkter

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="download-the-mars-agent"></a>Ladda ned MARS-agenten

Ladda ned MARS-agenten så att du kan installera den på de datorer som du vill backa upp.

Om du redan har installerat agenten på någon av datorerna kontrollerar du att du kör den senaste versionen av agenten. Hitta den senaste versionen i portalen eller gå direkt till [nedladdningen](https://aka.ms/azurebackup_agent).

1. I valvet går du **till Komma igång** och väljer **Säkerhetskopiera.**

    ![Öppna säkerhetskopieringsmålet](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. Under **Var körs din arbetsbelastning?** väljer du **Lokal**. Välj det här alternativet även om du vill installera MARS-agenten på en virtuell Azure-dator.
1. Under **Vad vill du backa upp?** väljer du Filer och **mappar**. Du kan också välja **Systemtillstånd**. Många andra alternativ är tillgängliga, men de här alternativen stöds bara om du kör en sekundär säkerhetskopieringsserver. Välj **Förbered infrastruktur.**

    ![Konfigurera filer och mappar](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. För **Förbered infrastrukturen,** under **Installera Recovery Services-agenten,** laddar du ned MARS-agenten.

    ![Förbered infrastrukturen](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. I nedladdningsmenyn väljer du **Spara**. Som standard sparas filen *MARSagentinstaller.exe* i mappen för nedladdningar.

1. Välj **Ladda redan ned eller använd den senaste Recovery Services-agenten** och ladda sedan ned autentiseringsuppgifterna för valvet.

    ![Ladda ned autentiseringsuppgifter för valvet](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. Välj **Spara**. Filen laddas ned till mappen Hämtade filer. Du kan inte öppna filen med valvautentiseringsuppgifter.

## <a name="install-and-register-the-agent"></a>Installera och registrera agenten

1. Kör *MARSagentinstaller.exe* på de datorer som du vill backa upp.
1. I MARS-Installationsguiden väljer du **Installationsinställningar.** Där väljer du var du vill installera agenten och väljer en plats för cachen. Välj sedan **Nästa**.
   * Azure Backup använder cachen för att lagra dataögonblicksbilder innan de skickas till Azure.
   * Cacheplatsen ska ha ledigt utrymme som är lika med minst 5 procent av storleken på de data som du ska lagra.

    ![Välj installationsinställningar i MARS-Installationsguiden](./media/backup-configure-vault/mars1.png)

1. För **Proxykonfiguration** anger du hur agenten som körs på Windows-datorn ska ansluta till Internet. Välj sedan **Nästa**.

   * Om du använder en anpassad proxyserver anger du eventuella nödvändiga proxyinställningar och autentiseringsuppgifter.
   * Kom ihåg att agenten behöver åtkomst till [specifika URL:er.](#before-you-start)

    ![Konfigurera Internetåtkomst i MARS-guiden](./media/backup-configure-vault/mars2.png)

1. För **Installation** granskar du förutsättningarna och väljer **Installera**.
1. När agenten har installerats väljer du **Fortsätt till registrering.**
1. I **guiden Registrera server**  >  **Valvidentifiering** bläddrar du till och väljer filen med autentiseringsuppgifter som du laddade ned. Välj sedan **Nästa**.

    ![Lägga till valvautentiseringsuppgifter med hjälp av guiden Registrera server](./media/backup-configure-vault/register1.png)

1. På sidan **Krypteringsinställning** anger du en lösenfras som ska användas för att kryptera och dekryptera säkerhetskopior för datorn. [Mer information](backup-azure-file-folder-backup-faq.yml#what-characters-are-allowed-for-the-passphrase-) om tillåtna lösenfrastecken finns här.

    * Spara lösenfrasen på en säker plats. Du behöver den för att återställa en säkerhetskopia.
    * Om du förlorar eller glömmer lösenfrasen kan Microsoft inte hjälpa dig att återställa säkerhetskopierade data.

1. Välj **Slutför**. Agenten har nu installerats och datorn har registrerats i valvet. Nu kan du konfigurera och schemalägga säkerhetskopieringen.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [backar upp Windows-datorer med hjälp Azure Backup MARS-agenten](backup-windows-with-mars-agent.md)
