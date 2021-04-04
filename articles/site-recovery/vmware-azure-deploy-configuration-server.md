---
title: Distribuera konfigurations servern i Azure Site Recovery
description: I den här artikeln beskrivs hur du distribuerar en konfigurations Server för katastrof återställning i VMware med Azure Site Recovery
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/15/2019
ms.author: ramamill
ms.openlocfilehash: a8ceb3df68ebe42f83c70ed62327bf59c0dfc225
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92359823"
---
# <a name="deploy-a-configuration-server"></a>Distribuera en konfigurationsserver

Du distribuerar en lokal konfigurations server när du använder [Azure Site Recovery](site-recovery-overview.md) för haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure. Konfigurations servern samordnar kommunikationen mellan lokala VMware och Azure. Den hanterar också datareplikering. Den här artikeln vägleder dig genom de steg som krävs för att distribuera konfigurations servern när du replikerar virtuella VMware-datorer till Azure. Om du behöver konfigurera en konfigurations Server för fysisk server-replikering, se [Konfigurera konfigurations servern för haveri beredskap för fysiska servrar till Azure](physical-azure-set-up-source.md).

> [!TIP]
> Om du vill veta mer om rollen för en konfigurations server som en del av Azure Site Recovery arkitektur, se [VMware till Azure Disaster Recovery Architecture](vmware-azure-architecture.md).

## <a name="deploy-a-configuration-server-through-an-ova-template"></a>Distribuera en konfigurations server via en ägg mall

Konfigurations servern måste konfigureras som en virtuell VMware-dator med hög tillgänglighet och vissa minimi krav för maskin vara och storlek. För bekväm och enkel distribution tillhandahåller Site Recovery en hämtnings bar mall för öppen Virtualization-program (ägg) för att konfigurera konfigurations servern som uppfyller alla krav som anges här.

## <a name="prerequisites"></a>Förutsättningar

De minsta maskin varu kraven för en konfigurations Server sammanfattas i följande avsnitt.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Krav för Azure Active Directory behörighet

Du måste ha en användare med någon av följande behörigheter i Azure Active Directory (Azure AD) för att registrera konfigurations servern med Azure Site Recovery Services.

1. Användaren måste ha en programutvecklare-roll för att kunna skapa ett program.
    - För att verifiera loggar du in på Azure Portal.</br>
    - Gå till **Azure Active Directory**  >  **roller och administratörer**.</br>
    - Verifiera att rollen programutvecklare är tilldelad till användaren. Om inte, använder du en användare med den här behörigheten eller kontaktar en [administratör för att aktivera behörigheten](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md#assign-roles).
    
2. Om rollen programutvecklare inte kan tilldelas kontrollerar du att flaggan **användare kan registrera program** är inställd på **Sant** för att användaren ska kunna skapa en identitet. Så här aktiverar du följande behörigheter:
    - Logga in på Azure-portalen.
    - Gå till **Azure Active Directory**  >  **användar inställningar**.
    - Under **Appregistreringar** **kan användare registrera program**, välja **Ja**.

      ![Azure-AD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> Active Directory Federation Services (AD FS) *stöds inte*. Använd ett konto som hanteras via [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md).

## <a name="download-the-template"></a>Ladda ned mallen

1. I valvet går du till **förbereda infrastruktur**  >  **källa**.
2. I **Förbered källa** väljer du **+Konfigurationsserver**.
3. I **Lägg till server** kontrollerar du att **Konfigurationsserver för VMware** visas i **Servertyp**.
4. Hämta embryo-mallen för konfigurations servern.

   > [!TIP]
   >Du kan också hämta den senaste versionen av konfigurations Server mal len direkt från [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

> [!NOTE]
> Den licens som tillhandahölls med en ägg mall är en utvärderings licens som är giltig i 180 dagar. Efter den här perioden måste du skaffa en licens.

## <a name="import-the-template-in-vmware"></a>Importera mallen i VMware

1. Logga in på VMware vCenter-servern eller vSphere ESXi-värden med hjälp av VMware vSphere-klienten.
2. I menyn **Arkiv** väljer du **distribuera OVF-mallen** för att starta guiden **distribuera OVF-mall** .

     ![Distribuera OVF-mall](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. På **Välj källa** anger du platsen för den nedladdade OVF:en.
4. På **Detaljer för recensionen** väljer du **Nästa**.
5. I **Välj namn och mapp** och **Välj konfiguration** accepterar du standardinställningarna.
6. I **Välj lagring** väljer du för bästa prestanda **Thick Provision Eager Zeroed** i **Välj virtuellt diskformat**. Användning av alternativet tunn allokering kan påverka konfigurations serverns prestanda.
7. Acceptera standardinställningarna på resten av sidorna i guiden.
8. I **Klart att slutföras**:

    * Om du vill konfigurera den virtuella datorn med standardinställningarna väljer du **ström när distributionen** är  >  **klar**.
    * Om du vill lägga till ytterligare ett nätverks gränssnitt avmarkerar du **Energis par efter distributionen** och väljer sedan **Slutför**. Konfigurationsservermallen distribueras som standard med ett enda nätverkskort. Du kan lägga till ytterligare nätverkskort efter distributionen.

> [!IMPORTANT]
> Ändra inte resursfiler, till exempel minne, kärnor och processor begränsning, eller ändra eller ta bort installerade tjänster eller filer på konfigurations servern efter distributionen. Dessa typer av ändringar påverkar registreringen av konfigurations servern med Azure-tjänster och konfigurations serverns prestanda.

## <a name="add-an-additional-adapter"></a>Lägg till ytterligare en adapter

> [!NOTE]
> Två nätverkskort krävs om du planerar att behålla IP-adresserna för käll datorerna på redundansväxling och vill växla tillbaka till lokala platser senare. Ett nätverkskort är anslutet till käll datorer och det andra NÄTVERKSKORTet används för Azure-anslutning.

Om du vill lägga till ett extra nätverkskort i konfigurations servern lägger du till det innan du registrerar servern i valvet. Det går inte att lägga till fler adaptrar efter registreringen.

1. I vSphere-klientlagret högerklickar du på den virtuella datorn och väljer **Redigera inställningar**.
2. I **Maskinvara** väljer du **Lägg till** > **Ethernet-kort**. Välj sedan **Nästa**.
3. Välj en adaptertyp och ett nätverk.
4. Om du vill ansluta det virtuella NÄTVERKSKORTet när den virtuella datorn är aktive rad väljer du **Anslut vid start**. Välj sedan **Nästa**  >  **Slutför**  >  **OK**.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>Registrera konfigurations servern med Azure Site Recovery Services

1. I VMware vSphere klient konsolen aktiverar du den virtuella datorn.
2. Den virtuella datorn startas med en Windows Server 2016-installation. Godkänn licensavtalet och ange ett administratörslösenord.
3. När installationen är klar loggar du in på den virtuella datorn som administratör.
4. Första gången du loggar in, inom några sekunder startas konfigurations verktyget för Azure Site Recovery.
5. Ange det namn som ska användas för att registrera konfigurationsservern med Site Recovery. Välj sedan **Nästa**.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har upprättats väljer du **Logga in** för att logga in i din Azure-prenumeration.</br>
    a. Autentiseringsuppgifterna måste ha åtkomst till det valv där du vill registrera konfigurationsservern.</br>
    b. Se till att det valda användar kontot har behörighet att skapa ett program i Azure. Om du vill aktivera de behörigheter som krävs följer du rikt linjerna i avsnittet [Azure Active Directory behörighets krav](#azure-active-directory-permission-requirements).
7. Verktyget utför vissa konfigurationsåtgärder och startar sedan om datorn.
8. Logga in på datorn igen. Guiden Konfigurera Server hantering startar automatiskt om några sekunder.

### <a name="configure-settings"></a>Konfigurera inställningar

1. I guiden konfigurations Server hantering väljer du **Konfigurera anslutning**. I list rutorna väljer du först det nätverkskort som den inbyggda processervern använder för identifiering och push-installation av mobilitets tjänsten på käll datorer. Välj sedan det nätverkskort som konfigurations servern använder för anslutning till Azure. Välj **Spara**. Du kan inte ändra den här inställningen när den har konfigurerats. Ändra inte IP-adressen för en konfigurations Server. Se till att den IP-adress som tilldelats konfigurations servern är en statisk IP-adress och inte en DHCP-IP.
2. Logga in på Microsoft Azure med de autentiseringsuppgifter som användes i steg 6 i [Registrera konfigurations servern med Azure Site Recovery tjänster](#register-the-configuration-server-with-azure-site-recovery-services)på **Välj Recovery Services valv**.
3. När du har loggat in väljer du din Azure-prenumeration och relevant resurs grupp och valv.

    > [!NOTE]
    > Efter registreringen finns det ingen möjlighet att ändra Recovery Services-valvet.
    > Att ändra ett Recovery Services-valv kräver disassociation av konfigurations servern från det aktuella valvet, och replikeringen av alla skyddade virtuella datorer under konfigurations servern stoppas. Läs mer i [Hantera konfigurations servern för haveri beredskap för virtuella VMware-datorer](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault).

4. **Installera program vara från tredje part**:

    |Scenario   |Steg att följa  |
    |---------|---------|
    |Kan jag hämta och installera MySQL manuellt?     |  Ja. Hämta MySQL-programmet, placera det i mappen **C:\Temp\ASRSetup** och installera manuellt. När du har accepterat villkoren och valt **Hämta och installera** är portalen *redan installerad*. Du kan fortsätta till nästa steg.       |
    |Kan jag undvika att hämta MySQL online?     |   Ja. Placera ditt MySQL installations program i mappen **C:\Temp\ASRSetup**. Godkänn villkoren, Välj **Ladda ned och installera** och portalen använder installations programmet som du har lagt till för att installera programmet. När installationen är klar fortsätter du till nästa steg.    |
    |Jag vill hämta och installera MySQL via Azure Site Recovery.    |  Godkänn licens avtalet och välj **Ladda ned och installera**. När installationen är klar fortsätter du till nästa steg.       |

5. Innan du fortsätter måste du kontrol lera **konfigurationen av enheten**.
6. På **konfigurera vCenter Server/vSphere ESXi-Server** anger du FQDN eller IP-adressen för vCenter-servern eller vSphere-värden, där de virtuella datorer som du vill replikera finns. Ange porten som servern lyssnar på. Ange ett eget namn som ska användas för VMware-servern i valvet.
7. Ange de autentiseringsuppgifter som ska användas av konfigurationsservern för att ansluta till VMware-servern. Site Recovery använder dessa autentiseringsuppgifter för att automatiskt identifiera virtuella VMware-datorer som är tillgängliga för replikering. Välj **Lägg till**  >  **Fortsätt**. De autentiseringsuppgifter som anges här sparas lokalt.
8. På **Konfigurera autentiseringsuppgifter för virtuell dator** anger du användar namn och lösen ord för virtuella datorer för att automatiskt installera mobilitets tjänsten under replikeringen. För **Windows** -datorer måste kontot ha lokal administratörs behörighet på de datorer som du vill replikera. För **Linux** anger du information om rot kontot.
9. Välj **Slutför konfigurationen** för att slutföra registreringen.
10. När registreringen är klar öppnar du Azure Portal och kontrollerar att konfigurations servern och VMware-servern visas på **Recovery Services valv**  >  **Hantera**  >  **Site Recovery infrastruktur**  >  **konfigurations servrar**.

## <a name="upgrade-the-configuration-server"></a>Uppgradera konfigurations servern

Information om hur du uppgraderar konfigurations servern till den senaste versionen finns i [Hantera konfigurations servern för haveri beredskap för VMware-datorer](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Instruktioner för hur du uppgraderar alla Site Recovery-komponenter finns i [tjänste uppdateringar i Site Recovery](service-updates-how-to.md).

## <a name="manage-the-configuration-server"></a>Hantera konfigurationsservern

För att undvika avbrott i pågående replikering kontrollerar du att konfigurations serverns IP-adress inte ändras efter att konfigurations servern har registrerats i ett valv. Mer information om vanliga konfigurations Server hanterings uppgifter finns i [Hantera konfigurations servern för haveri beredskap för VMware-datorer](vmware-azure-manage-configuration-server.md).

## <a name="troubleshoot-deployment-issues"></a>Felsöka distributionsproblem

Se vår [fel söknings artikel](vmware-azure-troubleshoot-configuration-server.md) för att lösa problem med distributionen & anslutningen.

## <a name="faqs"></a>Vanliga frågor och svar

* Hur länge är licensen på en konfigurations server som distribueras via OVF giltig? Vad händer om jag inte återaktiverar licensen?

    Den licens som tillhandahölls med en ägg mall är en utvärderings licens som är giltig i 180 dagar. Innan du går ut måste du aktivera licensen. Annars kan det leda till frekvent avstängning av konfigurations servern och orsaka hinder för replikerings aktiviteter. Mer information finns i [Hantera konfigurations servern för haveri beredskap för virtuella VMware-datorer](vmware-azure-manage-configuration-server.md#update-windows-license).

* Kan jag använda den virtuella datorn där konfigurations servern har installerats för olika användnings sätt?

    Nej. Använd den virtuella datorn för konfigurations serverns enda syfte. Se till att du följer alla specifikationer som anges i [krav](#prerequisites) för effektiv hantering av haveri beredskap.
* Kan jag byta valvet som redan är registrerat på konfigurations servern med ett nyligen skapat valv?

    Nej. När ett valv har registrerats på konfigurations servern kan det inte ändras.
* Kan jag använda samma konfigurations Server för att skydda både fysiska och virtuella datorer?

    Ja. Samma konfigurations Server kan användas för att replikera fysiska och virtuella datorer. Den fysiska datorn kan dock inte återställas till en virtuell VMware-dator.
* Vad är syftet med en konfigurations Server och var används den?

    Läs mer om konfigurations servern och dess funktioner i [VMware till Azure Replication Architecture](vmware-azure-architecture.md).
* Var hittar jag den senaste versionen av konfigurations servern?

    Anvisningar för hur du uppgraderar konfigurations servern via portalen finns i [Uppgradera konfigurations servern](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Instruktioner för hur du uppgraderar alla Site Recovery-komponenter finns i [tjänste uppdateringar i Site Recovery](./service-updates-how-to.md).
* Var kan jag hämta lösen frasen för konfigurations servern?

    Information om hur du hämtar lösen frasen finns i [Hantera konfigurations servern för haveri beredskap för virtuella VMware-datorer](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase).
* Kan jag ändra lösen frasen?

    Nej. Ändra inte lösen frasen för konfigurations servern. En ändring i lösen frasen avbryter replikeringen av skyddade datorer och leder till ett kritiskt hälso tillstånd.
* Var kan jag hämta valv registrerings nycklar?

    I **Recovery Services valv** väljer du **Hantera**  >  **Site Recovery infrastruktur**  >  **konfigurations servrar**. I **servrar** väljer du **Ladda ned registrerings nyckel** för att ladda ned valv filen med autentiseringsuppgifter.
* Kan jag klona en befintlig konfigurations Server och använda den för att dirigera om replikeringen?

    Nej. Användning av en Server komponent för klonad konfiguration stöds inte. Att klona en skalbar processerver är också ett scenario som inte stöds. Kloning av Site Recovery-komponenter påverkar pågående replikeringar.

* Kan jag ändra IP-adressen för en konfigurations Server?

    Nej. Ändra inte IP-adressen för en konfigurations Server. Se till att alla IP-adresser som har tilldelats konfigurations servern är statiska IP-adresser och inte DHCP IP-adresser.
* Kan jag konfigurera en konfigurations server på Azure?

    Konfigurera en konfigurations server i en lokal miljö med direkt detaljerad information med v-Center och för att minimera data överförings fördröjningar. Du kan göra schemalagda säkerhets kopieringar av konfigurations servern i [fel syfte](vmware-azure-manage-configuration-server.md#failback-requirements).

* Kan jag ändra cache-drivrutin på en konfigurations Server eller en skalbar processerver?

    Nej, det går inte att ändra cache-drivrutinen när installationen är klar.

Mer information om konfigurations servrar finns i [vanliga frågor och svar om konfigurations servern](vmware-azure-common-questions.md#configuration-server).

## <a name="next-steps"></a>Nästa steg

Konfigurera katastrof återställning av [virtuella VMware-datorer](vmware-azure-tutorial.md) till Azure.