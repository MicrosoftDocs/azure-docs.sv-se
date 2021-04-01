---
title: Installations Azure Site Recovery för virtuella Azure VMware-lösningar
description: Lär dig hur du konfigurerar haveri beredskap till Azure för virtuella Azure VMware-lösningar med Azure Site Recovery.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 3ac1f5bd3d44b7f98284cead60b34689f3d7be30
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395503"
---
# <a name="setup-azure-site-recovery-for-azure-vmware-solution-vms"></a>Installations Azure Site Recovery för virtuella Azure VMware-lösningar

I den här artikeln beskrivs hur du aktiverar replikering för virtuella datorer i Azure VMware-lösningen för haveri beredskap till Azure med hjälp av tjänsten [Azure Site Recovery](site-recovery-overview.md) .

Det här är den tredje självstudien i en serie som visar hur du konfigurerar haveri beredskap till Azure för virtuella Azure VMware-lösningar. I den föregående själv studie kursen har vi för [berett Azure VMwares lösnings miljö](avs-tutorial-prepare-avs.md) för haveri beredskap till Azure.


I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Konfigurera inställningarna för käll replikering och en Site Recovery konfigurations server i Azure VMware-lösningen privat moln
> * Konfigurera inställningarna för replikering.
> * Skapar en replikeringsprincip.
> * Aktivera replikering för en virtuell VMware-dator.

> [!NOTE]
> Självstudier visar den enklaste distributions vägen för ett scenario. De använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i artikeln i avsnittet så här i Site Recovery innehålls förteckningen.

## <a name="before-you-start"></a>Innan du börjar

Slutför de föregående självstudierna:

1. Kontrol lera att du har [konfigurerat Azure](avs-tutorial-prepare-azure.md) för haveri beredskap till Azure.
2. Följ [de här stegen](avs-tutorial-prepare-avs.md) för att förbereda distributionen av Azure VMware-lösningen för haveri beredskap till Azure.
3. I den här självstudien visar vi hur du replikerar en enskild virtuell dator. Om du distribuerar flera virtuella datorer bör du använda [verktyget för distributions planering](https://aka.ms/asr-deployment-planner). [Läs mer](site-recovery-deployment-planner.md) om det här verktyget.
4. I den här självstudien används ett antal alternativ som du kanske vill göra olika:
    - I självstudien används en ägg-mall för att skapa en virtuell VMware-dator i konfigurations servern. Om du inte kan göra det av någon anledning följer du [de här anvisningarna](physical-manage-configuration-server.md) för att konfigurera konfigurations servern manuellt.
    - I den här självstudien får Site Recovery automatiskt hämta och installera MySQL på konfigurations servern. Om du vill kan du konfigurera den manuellt i stället. [Läs mer](vmware-azure-deploy-configuration-server.md#configure-settings).




## <a name="select-a-protection-goal"></a>Välj ett skyddsmål

1. Välj valvets namn i **Recovery Services-valv**. I det här scenariot använder vi namnet **ContosoVMVault**.
2. I **Komma igång** väljer du Site Recovery. Välj sedan **Förbered infrastrukturen**.
3. I **skydds mål**  >  **där** finns datorerna väljer du **lokalt**.
4. I **Till vilken plats ska dina datorer replikeras?** väljer du **Till Azure**.
5. I **Är dina datorer virtualiserade?** väljer du **Ja, med VMware vSphere Hypervisor**. Välj sedan **OK**.



## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

I din käll miljö behöver du en enda, lokal dator med hög tillgänglighet som värd för dessa lokala Site Recovery-komponenter:

- **Konfigurations** Server: konfigurations servern samordnar kommunikationen mellan Azure VMware-lösningens privata moln och Azure och hanterar datareplikering.
- **Processerver**: processervern fungerar som en gateway för replikering. Den tar emot replikeringsdata; optimerar den med cachelagring, komprimering och kryptering och skickar den till ett cache Storage-konto i Azure. Processervern installerar också mobilitets tjänst agenten på de virtuella datorer som du vill replikera och utför automatisk identifiering av virtuella datorer i Azure VMware-lösningen.
- **Huvud mål server**: huvud mål servern hanterar replikeringsdata under återställning efter fel från Azure.


Alla dessa komponenter installeras tillsammans på en enda Azure VMware-lösning som kallas *konfigurations servern*. Som standard ställer vi in konfigurations servern som en virtuell VMware-dator med hög tillgänglighet för haveri beredskap för Azure VMware-lösningen. Om du vill göra detta laddar du ned en för beredd mall för öppen Virtualization-app (ägg) och importerar mallen till VMware för att skapa den virtuella datorn.

- Den senaste versionen av konfigurations servern är tillgänglig i portalen. Du kan också hämta det direkt från [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
- Om du av någon anledning inte kan använda en mall för att konfigurera en virtuell dator, följer du [dessa anvisningar](physical-manage-configuration-server.md) för att konfigurera konfigurations servern manuellt.
- Den licens som tillhandahölls med OVF-mallen är en utvärderings licens som är giltig i 180 dagar. Windows som körs på den virtuella datorn måste aktive ras med den licens som krävs.


### <a name="download-the-vm-template"></a>Ladda ned VM-mallen

1. I valvet går du till **förbereda infrastruktur**  >  **källa**.
2. I **Förbered källa** väljer du **+Konfigurationsserver**.
3. I **Lägg till server** kontrollerar du att **Konfigurationsserver för VMware** visas i **Servertyp**.
4. Hämta embryo-mallen för konfigurations servern.



## <a name="import-the-template-in-vmware"></a>Importera mallen i VMware


1. Logga in på VMware vCenter-servern eller vSphere ESXi-värden med VMware vSphere-klienten.
2. I menyn **Arkiv** väljer du **distribuera OVF-mallen** för att starta **guiden Distribuera OVF-mall**.

     ![Skärm bild av kommandot distribuera OVF-mall i VMWare vSphere-klienten.](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. På **Välj källa** anger du platsen för den nedladdade OVF:en.
4. På **Detaljer för recensionen** väljer du **Nästa**.
5. I **Välj namn och mapp** och **Välj konfiguration** accepterar du standardinställningarna.
6. I **Välj lagring** väljer du för bästa prestanda **Thick Provision Eager Zeroed** i **Välj virtuellt diskformat**.
7. Acceptera standardinställningarna på resten av sidorna i guiden.
8. På **Klar att slutföras** väljer du, om du vill konfigurera den virtuella datorn med standardinställningarna, **Slå på strömmen efter distributionen** > **Slutför**.

   > [!TIP]
   > Om du vill lägga till ett extra nätverkskort rensar du **energi förbrukningen när distributionen** är  >  **klar**. Mallen innehåller som standard en enda NIC. Du kan lägga till ytterligare nätverkskort efter distributionen.

## <a name="add-an-additional-adapter"></a>Lägg till ytterligare en adapter

Om du vill lägga till ett extra nätverkskort i konfigurations servern lägger du till det innan du registrerar servern i valvet. Det går inte att lägga till fler adaptrar efter registreringen.

1. I vSphere-klientlagret högerklickar du på den virtuella datorn och väljer **Redigera inställningar**.
2. I **Maskinvara** väljer du **Lägg till** > **Ethernet-kort**. Välj sedan **Nästa**.
3. Välj en adaptertyp och ett nätverk.
4. Om du vill ansluta det virtuella nätverkskortet när den virtuella datorn är påslagen väljer du **Connect at power on** (Anslut när strömmen är på). Välj **Nästa** > **Slutför**. Välj sedan **OK**.


## <a name="register-the-configuration-server"></a>Registrera konfigurationsservern

När konfigurations servern har kon figurer ATS kan du registrera den i valvet.

1. I VMware vSphere klient konsolen aktiverar du den virtuella datorn.
2. Den virtuella datorn startas med en Windows Server 2016-installation. Godkänn licensavtalet och ange ett administratörslösenord.
3. När installationen är klar loggar du in på den virtuella datorn som administratör.
4. Första gången du loggar in startas Azure Site Recovery-konfigurationsverktyget inom några sekunder.
5. Ange det namn som ska användas för att registrera konfigurationsservern med Site Recovery. Välj sedan **Nästa**.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har upprättats väljer du **Logga in** för att logga in i din Azure-prenumeration. Autentiseringsuppgifterna måste ha åtkomst till det valv där du vill registrera konfigurationsservern. Se till att de [roller](vmware-azure-deploy-configuration-server.md#azure-active-directory-permission-requirements) som krävs har tilldelats den här användaren.
7. Verktyget utför vissa konfigurationsåtgärder och startar sedan om datorn.
8. Logga in på datorn igen. Guiden Konfigurera serverhantering startar automatiskt om några sekunder.


### <a name="configure-settings-and-add-the-vmware-server"></a>Konfigurera inställningar och lägga till VMware-servern

Slutför konfigurationen och registrera konfigurations servern. Innan du fortsätter bör du se till att alla [krav](vmware-azure-deploy-configuration-server.md#prerequisites) uppfylls för att konfigurera konfigurations servern.


1. I guiden konfigurations Server hantering väljer du **Konfigurera anslutning**. I list rutorna väljer du först det nätverkskort som den inbyggda processervern använder för identifiering och push-installation av mobilitets tjänsten på käll datorer. Välj sedan det nätverkskort som konfigurations servern använder för anslutning med Azure. Välj sedan **Spara**. Du kan inte ändra den här inställningen när den har kon figurer ATS.
2. I **välj Recovery Services valv** väljer du din Azure-prenumeration och relevant resurs grupp och valv.
3. Godkänn licensavtalet i **Installera programvara från tredje part**. Välj **Ladda ned och installera** för att installera MySQL Server. Det här steget kan hoppas över om du har placerat MySQL i sökvägen. Läs [mer](vmware-azure-deploy-configuration-server.md#configure-settings)
4. I **Verifiera installationskonfiguration** verifieras förutsättningarna innan du fortsätter.
5. I **Konfigurera vCenter Server/vSphere ESXi-server** anger du FQDN eller IP-adress för vCenter-servern eller vSphere-värden där de virtuella datorer som du vill replikera är placerade. Ange porten som servern lyssnar på. Ange ett eget namn som ska användas för VMware-servern i valvet.
6. Ange de uppgifter för användarautentisering som ska användas av konfigurationsservern för att ansluta till VMware-servern. Kontrollera att användarnamn och lösenord är korrekta och att de ingår i gruppen Administratörer på den virtuella datorn som ska skyddas. Site Recovery använder dessa autentiseringsuppgifter för att automatiskt identifiera virtuella VMware-datorer som är tillgängliga för replikering. Välj **Lägg till** och välj sedan **Fortsätt**.
7. I **Konfigurera autentiseringsuppgifter för virtuell dator** anger du det användarnamn och lösenord som ska användas för att automatiskt installera mobilitetstjänsten på virtuella datorer, när replikering har aktiverats.
    - För Windows-datorer måste kontot ha lokal administratörsbehörighet på de datorer som du vill replikera.
    - För Linux anger du information för rotkontot.
8. Välj **Slutför konfigurationen** för att slutföra registreringen.
9. När registreringen är klar öppnar du Azure Portal och kontrollerar att konfigurations servern och VMware-servern visas på **Recovery Services valv**  >  **Hantera**  >  **Site Recovery infrastruktur**  >  **konfigurations servrar**.


När konfigurations servern har registrerats ansluter Site Recovery till VMware-servrar med hjälp av de angivna inställningarna och identifierar virtuella datorer.

> [!NOTE]
> Det kan ta 15 minuter eller mer innan kontonamnet visas i portalen. Om du vill uppdatera omedelbart väljer du **konfigurations servrar**  >  **_Server namn_*_ > _* uppdatera Server**.

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och kontrollera målresurserna.

1. Välj **Förbered infrastruktur**  >  **mål**. Ange den prenumeration som du vill använda. Vi använder en Resource Manager-modell.
2. Site Recovery kontrollerar att det finns ett eller flera virtuella nätverk. Du bör ha dessa när du konfigurerar Azure komponenterna i den [första självstudien](tutorial-prepare-azure.md) i den här serien med självstudier.

   ![Skärm bild av > mål alternativ för förberedelse av infrastruktur.](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

1. Öppna [Azure-portalen](https://portal.azure.com). Sök efter och välj **Recovery Services valv**.
2. Välj Recovery Services-valvet (**ContosoVMVault** i den här självstudien).
3. Om du vill skapa en replikeringsprincip väljer du **Site Recovery Infrastructure**  >  **Replication policys**  >  **+ replikeringsprincip**.
4. I **Skapa replikeringsprincip** anger du principnamnet. Vi använder **VMwareRepPolicy**.
5. I **Tröskelvärde för återställningspunktmål** använder du standardvärdet 60 minuter. Det här värdet anger hur ofta återställningspunkter skapas. En avisering genereras när den kontinuerliga replikeringen överskrider den här gränsen.
6. I **Återställningspunkt för kvarhållning** anger du hur länge varje återställningspunkt finns kvar. I den här självstudien använder vi 72 timmar. Replikerade virtuella datorer kan återställas till valfri punkt i ett kvarhållningsfönster.
7. I **Frekvens för programkonsekventa ögonblicksbilder** anger du hur ofta programkonsekventa ögonblicksbilder skapas. Vi använder standardinställningen 60 minuter. Välj **OK** för att skapa principen.

   ![Skärm bild av alternativen för att skapa replikeringsprincipen.](./media/vmware-azure-tutorial/replication-policy.png)

- Principen associeras automatiskt med konfigurationsservern.
- Som standard skapas automatiskt en matchande princip för återställning efter fel. Om replikeringsprincipen till exempel är **rep-policy** är failback-principen **rep-policy-failback**. Den här principen används inte förrän du initierar en återställning efter fel från Azure.

Obs! i scenariot för VMware-till-Azure tas krasch-konsekventa ögonblicks bilder med 5 lägsta intervall.

## <a name="enable-replication"></a>Aktivera replikering

Aktivera replikering för virtuella datorer enligt följande:

1. Välj **Replikera program**  >  **källa**.
2. I **Källa** väljer du **Lokalt** och väljer konfigurationsservern i **källplats**.
3. I **Datortyp** väljer du **Virtual Machines**.
4. I **vCenter/vSphere Hypervisor** väljer du vSphere-värden eller den vCenter-server som hanterar värden.
5. Välj processerver (installeras som standard på konfigurationsserverns virtuella dator). Välj sedan **OK**. Hälso status för varje processerver anges enligt rekommenderade gränser och andra parametrar. Välj en felfri processerver. Det går inte att välja en [kritisk](vmware-physical-azure-monitor-process-server.md#process-server-alerts) processerver. Du kan antingen [Felsöka och lösa](vmware-physical-azure-troubleshoot-process-server.md) felen **eller** konfigurera en [skalbar processerver](vmware-azure-set-up-process-server-scale.md).
6. I **Mål** väljer du den prenumeration och resursgrupp där du vill skapa de redundansväxlade virtuella datorerna. Vi använder Resource Manager-distributionsmodellen.
7. Välj det Azure-nätverk och undernät som virtuella Azure-datorer ska ansluta till efter en redundansväxling.
8. Välj **Konfigurera nu för valda datorer** om du vill använda nätverksinställningen på alla virtuella datorer som du aktiverat replikering för. Välj **Konfigurera senare** om du vill välja Azure-nätverket för varje dator.
9. I **Virtual Machines**  >  **Välj virtuella datorer** väljer du de datorer som du vill replikera. Du kan bara välja datorer som stöder replikering. Välj sedan **OK**. Om du inte kan visa/välja en viss virtuell dator kan du [läsa mer](./vmware-azure-troubleshoot-replication.md) om hur du löser problemet.
10. I **Egenskaper**  >  **Konfigurera egenskaper** väljer du det konto som ska användas av processervern för att automatiskt installera mobilitets tjänsten på datorn.
11. I **replikeringsinställningar**  >  **Konfigurera replikeringsinställningar** kontrollerar du att rätt replikeringsprincip är markerad.
12. Välj **Aktivera replikering**. Site Recovery installerar mobilitetstjänsten när replikering är aktiverad för en virtuell dator.
13. Du kan följa förloppet för jobbet **Aktivera skydd** i **Inställningar**  >  **jobb**  >  **Site Recovery jobb**. När jobbet **Slutför skydd** har körts och en generering av återställnings punkter har slutförts är datorn klar för redundans.
14. Det kan ta 15 minuter eller längre innan ändringarna träder i kraft och visas på portalen.
15. Om du vill övervaka de virtuella datorer som du lägger till kontrollerar du den senaste identifierade tiden för virtuella datorer i **konfigurations servrarna**  >  **senaste kontakt på**. Om du vill lägga till virtuella datorer utan att vänta på den schemalagda identifieringen markerar du konfigurationsservern (välj den inte) och väljer **Uppdatera**.

## <a name="next-steps"></a>Nästa steg
När du har aktiverat replikering kan du köra en detalj granskning för att se till att allt fungerar som förväntat.
> [!div class="nextstepaction"]
> [Köra ett programåterställningstest](avs-tutorial-dr-drill-azure.md)
