---
title: Förbereda DPM-servern för säkerhets kopiering av arbets belastningar
description: I den här artikeln lär du dig att förbereda för säkerhets kopiering av System Center Data Protection Manager (DPM) till Azure med hjälp av tjänsten Azure Backup.
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: a61112f04d3faef83a80395a7d316c63130e7758
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263273"
---
# <a name="prepare-to-back-up-workloads-to-azure-with-system-center-dpm"></a>Förbereda säkerhets kopiering av arbets belastningar till Azure med System Center DPM

Den här artikeln förklarar hur du förbereder för säkerhets kopiering av System Center Data Protection Manager (DPM) till Azure med hjälp av tjänsten Azure Backup.

Artikeln innehåller:

- En översikt över hur du distribuerar DPM med Azure Backup.
- Krav och begränsningar för att använda Azure Backup med DPM.
- Steg för att förbereda Azure, inklusive konfiguration av ett Recovery Services säkerhets kopierings valv och eventuellt ändra typen av Azure Storage för valvet.
- Steg för att förbereda DPM-servern, inklusive att hämta autentiseringsuppgifter för valvet, installera Azure Backup Agent och registrera DPM-servern i valvet.
- Fel söknings tips för vanliga fel.

## <a name="why-back-up-dpm-to-azure"></a>Varför säkerhetskopiera DPM till Azure?

[System Center DPM](/system-center/dpm/dpm-overview) säkerhetskopierar fil-och program data. DPM interagerar med Azure Backup enligt följande:

- **DPM som körs på en fysisk server eller lokal virtuell dator** – du kan säkerhetskopiera data till ett säkerhets kopierings valv i Azure, förutom disk-och band säkerhets kopiering.
- **DPM som körs på en virtuell Azure-dator** – från System Center 2012 R2 med uppdatering 3 eller senare kan du distribuera DPM på en virtuell Azure-dator. Du kan säkerhetskopiera data till Azure-diskar som är anslutna till den virtuella datorn eller använda Azure Backup för att säkerhetskopiera data till ett säkerhets kopierings valv.

Företags fördelarna med att säkerhetskopiera DPM-servrar till Azure är:

- För lokala DPM-datorer tillhandahåller Azure Backup ett alternativ till långsiktig distribution till band.
- För att DPM ska köras på en virtuell Azure-dator kan du med Azure Backup avlasta lagrings utrymme från Azure-disken. Genom att lagra äldre data i ett säkerhets kopierings valv kan du skala upp din verksamhet genom att lagra nya data på disk.

## <a name="prerequisites-and-limitations"></a>Krav och begränsningar

**Inställning** | **Krav**
--- | ---
DPM på en virtuell Azure-dator | System Center 2012 R2 med DPM 2012 R2 Samlad uppdatering 3 eller senare.
DPM på en fysisk server | System Center 2012 SP1 eller senare; System Center 2012 R2.
DPM på en virtuell Hyper-V-dator | System Center 2012 SP1 eller senare; System Center 2012 R2.
DPM på en virtuell VMware-dator | System Center 2012 R2 med Samlad uppdatering 5 eller senare.
Komponenter | DPM-servern måste ha Windows PowerShell och .NET Framework 4,5 installerat.
Appar som stöds | [Läs mer om](/system-center/dpm/dpm-protection-matrix) vad som kan säkerhetskopieras av DPM.
Filtyper som stöds | De här fil typerna kan säkerhets kopie ras med Azure Backup:<br> <li>Krypterad (endast fullständiga säkerhets kopior)<li> Komprimerade (stegvisa säkerhets kopieringar stöds) <li> Sparse (stegvisa säkerhets kopieringar stöds)<li> Komprimerad och sparse (behandlas som sparse)
Filtyper som inte stöds | <li>Servrar på SKIFT läges känsliga fil system<li> hårda länkar (hoppades över)<li> referens punkter (överhoppad)<li> krypterad och komprimerad (överhoppad)<li> krypterad och sparse (överhoppad)<li> Komprimerad ström<li> parsa data ström
Lokal lagring | Alla datorer som du vill säkerhetskopiera måste ha lokalt ledigt lagrings utrymme som är minst 5% av storleken på de data som säkerhets kopie ras. Till exempel krävs minst 5 GB ledigt utrymme på arbets platsen för att säkerhetskopiera 100 GB data.
Valv lagring | Det finns ingen gräns för mängden data som du kan säkerhetskopiera till ett Azure Backup-valv, men storleken på en data källa (till exempel en virtuell dator eller databas) får inte överstiga 54 400 GB.
Azure ExpressRoute | Du kan säkerhetskopiera dina data via Azure ExpressRoute med offentlig peering (tillgänglig för gamla kretsar) och Microsoft-peering. Säkerhets kopiering över privat peering stöds inte.<br/><br/> **Med offentlig peering**: säkerställa åtkomst till följande domäner/adresser:<br/><br/>- `http://www.msftncsi.com/ncsi.txt` <br/><br/>- `microsoft.com` <br/><br/>-`.WindowsAzure.com`<br/><br/>-`.microsoftonline.com`<br/><br/>-`.windows.net`<br/><br/> **Med Microsoft-peering**väljer du följande tjänster/regioner och relevanta community-värden:<br/><br/>– Azure Active Directory (12076:5060)<br/><br/>– Microsoft Azure region (enligt platsen för ditt Recovery Services-valv)<br/><br/>-Azure Storage (enligt platsen för ditt Recovery Services-valv)<br/><br/>Mer information finns i [krav för ExpressRoute-routning](../expressroute/expressroute-routing.md).<br/><br/>**Obs**: offentlig peering är inaktuell för nya kretsar.
Azure Backup-agent | Om DPM körs i System Center 2012 SP1 installerar du Rollup 2 eller senare för DPM SP1. Detta krävs för agent installation.<br/><br/> Den här artikeln beskriver hur du distribuerar den senaste versionen av Azure Backup Agent, även kallat MARS-agenten (Microsoft Azure Recovery Service). Om du har en tidigare distribuerad version uppdaterar du till den senaste versionen för att se till att säkerhets kopieringen fungerar som förväntat.

Innan du börjar behöver du ett Azure-konto med Azure Backup funktionen aktive rad. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Läs om [priser för Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-settings"></a>Ändra lagrings inställningar

Du kan välja mellan Geo-redundant lagring och lokalt redundant lagring.

- Valvet använder geo-redundant lagring som standard.
- Om valvet är din primära säkerhets kopia lämnar du alternativet inställt på Geo-redundant lagring. Om du vill ha ett billigare alternativ som inte är lika beständigt kan du använda följande procedur för att konfigurera lokalt redundant lagring.
- Lär dig mer om [Azure Storage](../storage/common/storage-redundancy.md)och alternativen [Geo-redundant](../storage/common/storage-redundancy.md) och [lokalt redundant](../storage/common/storage-redundancy.md) lagring.
- Ändra lagrings inställningarna före den första säkerhets kopieringen. Om du redan har säkerhetskopierat ett objekt kan du sluta säkerhetskopiera det i valvet innan du ändrar lagrings inställningarna.

Så här redigerar du inställningen för lagringsreplikering:

1. Öppna instrument panelen för valvet.

2. I **Hantera**väljer du **infrastruktur för säkerhets kopiering**.

3. I menyn **säkerhets kopierings konfiguration** väljer du ett lagrings alternativ för valvet.

    ![Lista över säkerhetskopieringsvalv](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

## <a name="download-vault-credentials"></a>Ladda ned autentiseringsuppgifter för valvet

Du använder autentiseringsuppgifter för valvet när du registrerar DPM-servern i valvet.

- Valvautentiseringsfilen är ett certifikat som genereras av portalen för varje säkerhetskopieringsvalv.
- Portalen överför sedan den offentliga nyckeln till Access Control Service (ACS).
- Under arbets flödet för dator registrering görs certifikatets privata nyckel tillgänglig för användaren, som autentiserar datorn.
- Baserat på autentiseringen skickar Azure Backup tjänsten data till det identifierade valvet.

### <a name="best-practices-for-vault-credentials"></a>Metod tips för valv-autentiseringsuppgifter

Hämta autentiseringsuppgifterna genom att ladda ned valvets autentiseringsuppgift via en säker kanal från Azure Portal:

- Autentiseringsuppgifterna för valvet används endast under registrerings arbets flödet.
- Det är ditt ansvar att se till att valv filen för autentiseringsuppgifter är säker och inte komprometterad.
  - Om kontrollen av autentiseringsuppgifterna förloras kan autentiseringsuppgifterna för valvet användas för att registrera andra datorer i valvet.
  - Säkerhetskopierade data krypteras dock med en lösen fras som tillhör kunden, så befintliga säkerhets kopierings data kan inte komprometteras.
- Se till att filen sparas på en plats som kan nås från DPM-servern. Om den är lagrad i en fil resurs/SMB, kontrol lera åtkomst behörigheterna.
- Autentiseringsuppgifterna för valvet upphör att gälla efter 48 timmar. Du kan hämta nya autentiseringsuppgifter för valvet så många gånger som det behövs. Men endast den senaste filen med valv behörighet kan användas under registrerings arbets flödet.
- Den Azure Backup tjänsten är inte medveten om certifikatets privata nyckel och den privata nyckeln är inte tillgänglig i portalen eller tjänsten.

Hämta valv filen med autentiseringsuppgifter till en lokal dator på följande sätt:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Öppna det valv där du vill registrera DPM-servern.
3. I **Inställningar**väljer du **Egenskaper**.

    ![Öppna menyn för valvet](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. I **Egenskaper**  >  **autentiseringsuppgifter för säkerhets kopiering**väljer du **Hämta**. Portalen genererar valvets loggfil med en kombination av valv namnet och det aktuella datumet och gör den tillgänglig för nedladdning.

    ![Hämta autentiseringsuppgifter](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. Välj **Spara** för att ladda ned valv autentiseringsuppgifterna till mappen eller **Spara som** och ange en plats. Det tar upp till en minut innan filen skapas.

## <a name="install-the-backup-agent"></a>Installera säkerhets kopierings agenten

Alla datorer som säkerhets kopie ras av Azure Backup måste ha säkerhets kopierings agenten Microsoft Azure (MARS) installerat på den. Installera agenten på DPM-servern på följande sätt:

1. Öppna valvet som du vill registrera DPM-servern på.
2. I **Inställningar**väljer du **Egenskaper**.

    ![Öppna valv inställningar](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. På sidan **Egenskaper** laddar du ned Azure Backup agenten.

    ![Ladda ned](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

4. Kör MARSAgentInstaller.exe när du har laddat ned. Installera agenten på DPM-datorn.
5. Välj en installationsmapp och cache-mapp för agenten. Det lediga utrymmet på cacheplatsen måste vara minst 5% av säkerhetskopierade data.
6. Om du använder en proxyserver för att ansluta till Internet anger du informationen om proxyservern på skärmen **konfiguration** av proxyserver. Om du använder en autentiserad proxy anger du informationen om användar namn och lösen ord på den här skärmen.
7. Azure Backup agenten installerar .NET Framework 4,5 och Windows PowerShell (om de inte är installerade) för att slutföra installationen.
8. När agenten har installerats **stänger** du fönstret.

    ![Stäng](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

## <a name="register-the-dpm-server-in-the-vault"></a>Registrera DPM-servern i valvet

1. I DPM-administratörskonsolen > **hantering**väljer du **online**. Välj **Registrera**. Guiden Registrera Server öppnas.
2. I **proxykonfiguration**anger du proxyinställningarna efter behov.

    ![Proxykonfiguration](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
3. I **säkerhets kopierings valvet**bläddrar du till och väljer den valv fil för autentiseringsuppgifter som du laddade ned.

    ![Autentiseringsuppgifter för valv](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

4. I **begränsnings inställningen**kan du välja att aktivera bandbredds begränsning för säkerhets kopieringar. Du kan ange hastighets gränserna för ange arbets tid och dagar.

    ![Begränsnings inställning](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

5. I **Inställningar för återställnings mapp**anger du en plats som kan användas under data återställningen.

    - Azure Backup använder den här platsen som ett temporärt område för återställda data.
    - När du har slutfört data återställningen kommer Azure Backup att rensa data i det här avsnittet.
    - Platsen måste ha tillräckligt med utrymme för att lagra objekt som du förväntar dig att återställa parallellt.

    ![Inställning av återställnings mapp](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

6. I **krypterings inställning**genererar eller anger du en lösen fras.

    - Lösen frasen används för att kryptera säkerhets kopiorna till molnet.
    - Ange minst 16 tecken.
    - Spara filen på en säker plats, det behövs för återställningen.

    ![Kryptering](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Du äger krypterings lösen frasen och Microsoft har ingen insyn i den.
    > Om lösen frasen tappas bort eller tappas bort. Microsoft kan inte hjälpa till att återskapa säkerhets kopierings data.

7. Välj **Registrera** för att registrera DPM-servern på valvet.

När servern har registrerats till valvet är du nu redo att börja säkerhetskopiera till Microsoft Azure. Du måste konfigurera skydds gruppen i DPM-konsolen för att säkerhetskopiera arbets belastningar till Azure. [Lär dig hur](/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-2019) du distribuerar skydds grupper.

## <a name="troubleshoot-vault-credentials"></a>Felsöka autentiseringsuppgifter för valvet

### <a name="expiration-error"></a>Förfallo fel

Valv filen med autentiseringsuppgifter är bara giltig i 48 timmar (när den har laddats ned från portalen). Om du stöter på något fel på den här skärmen (till exempel "autentiseringsuppgifter för valv som angetts har upphört att gälla") loggar du in på Azure Portal och laddar ned valvets autentiseringsuppgifter-fil igen.

### <a name="access-error"></a>Åtkomst fel

Se till att filen med valvets autentiseringsuppgifter är tillgänglig på en plats som kan nås av installations programmet. Om du får åtkomst till relaterade fel, kopierar du filen med autentiseringsuppgifter för valvet till en tillfällig plats på den här datorn och försöker igen.

### <a name="invalid-credentials-error"></a>Ogiltiga autentiseringsuppgifter-fel

Om det uppstår ett ogiltigt valv för autentiseringsuppgifter (till exempel "ogiltiga valv uppgifter angavs") är filen antingen skadad eller har inte de senaste autentiseringsuppgifterna som är associerade med återställnings tjänsten.

- Försök igen när du har laddat ned en ny valv referens fil från portalen.
- Det här felet visas vanligt vis när du väljer alternativet **Hämta autentiseringsuppgifter för valv** i Azure Portal, två gånger i snabb följd. I det här fallet är det bara det andra valvet som är giltigt.
