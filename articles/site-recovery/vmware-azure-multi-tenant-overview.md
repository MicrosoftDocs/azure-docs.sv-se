---
title: Katastrof återställning av VMware VM för flera innehavare med Azure Site Recovery
description: Innehåller en översikt över Azure Site Recovery stöd för haveri beredskap i VMWare till Azure i ett CSP-program (Multi-Tenant Environment).
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 64e40341ec56a2e1c561b2bcbb5e584830c14015
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93145590"
---
# <a name="overview-of-multi-tenant-support-for-vmware-disaster-recovery-to-azure-with-csp"></a>Översikt över stöd för flera innehavare för haveri beredskap i VMware till Azure med CSP

[Azure Site Recovery](site-recovery-overview.md) stöder miljöer med flera klienter för klient prenumerationer. Den har också stöd för flera innehavare för klient prenumerationer som skapas och hanteras via CSP-programmet (Microsoft Cloud Solution Provider).

Den här artikeln innehåller en översikt över hur du implementerar och hanterar VMware till Azure-replikering med flera innehavare.

## <a name="multi-tenant-environments"></a>Miljöer med flera klienter

Det finns tre större modeller med flera klienter:

* **HSP (Shared Hosting Services Provider)**: partnern äger den fysiska infrastrukturen och använder delade resurser (vCenter, data Center, fysisk lagring osv.) som värd för flera virtuella klient datorer i samma infrastruktur. Partnern kan tillhandahålla hantering av haveri beredskap som en hanterad tjänst, eller så kan klienten utföra en egen haveri beredskap som en självbetjänings lösning.

* **Dedikerad värd tjänst leverantör**: partnern äger den fysiska infrastrukturen, men använder dedikerade resurser (flera vCenter, fysiska data lager och så vidare) som värd för varje klients virtuella datorer på en separat infrastruktur. Partnern kan tillhandahålla hantering av katastrof återställning som en hanterad tjänst, eller så kan klienten äga den som en självbetjänings lösning.

* **(Managed Services Provider)**: kunden äger den fysiska infrastruktur som är värd för de virtuella datorerna och partnern tillhandahåller haveri beredskap och hantering.

## <a name="shared-hosting-services-provider-hsp"></a>HSP (Shared Hosting Services Provider)

De andra två scenarierna är del mängder av det delade värd scenariot och de använder samma principer. Skillnaderna beskrivs i slutet av den delade värd vägledningen.

Grundläggande krav i ett scenario med flera innehavare är att klienterna måste vara isolerade. En klient bör inte kunna observera vad en annan klient organisation har. I en partner hanterad miljö är detta krav inte lika viktigt som i en självbetjänings miljö, där det kan vara kritiskt. Den här artikeln förutsätter att klient isolering krävs.

Arkitekturen visas i följande diagram.

![Delade HSP med en vCenter](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**Delad-värd med en vCenter-Server**

I diagrammet har varje kund en separat hanterings Server. Den här konfigurationen begränsar klient åtkomsten till klient datorer och aktiverar klient isolering. VMware VM-replikering använder konfigurations servern för att identifiera virtuella datorer och installera agenter. Samma principer gäller för miljöer med flera klienter, med tillägg av begränsning av identifiering av virtuella datorer med vCenter Access Control.

Kravet på data isolering innebär att all känslig infrastruktur information (t. ex. autentiseringsuppgifter för åtkomst) inte är hemlig till klienter. Därför rekommenderar vi att alla komponenter i hanterings servern förblir under den exklusiva kontrollen av partnern. Hanterings serverns komponenter är:

* Konfigurationsserver
* Processerver
* Huvudmålservern

En separat uppskalad processerver ingår också i partnerns kontroll.

## <a name="configuration-server-accounts"></a>Konfigurations Server konton

Varje konfigurations server i scenariot för flera innehavare använder två konton:

- **vCenter-åtkomst konto**: det här kontot används för att identifiera virtuella klient datorer. Den har tilldelats åtkomst behörigheter för vCenter. För att undvika åtkomst läckor rekommenderar vi att partners anger dessa autentiseringsuppgifter själva i konfigurations verktyget.

- **Åtkomst konto för virtuell dator**: det här kontot används för att installera mobilitets tjänst agenten på virtuella klient datorer, med en automatisk push. Det är vanligt vis ett domän konto som en klient kan ge till en partner, eller ett konto som partnern kan hantera direkt. Om en klient inte vill dela informationen med partnern direkt, kan de ange autentiseringsuppgifterna via begränsad åtkomst till konfigurations servern. Eller, med partnerns hjälp, kan de installera mobilitets tjänst agenten manuellt.

## <a name="vcenter-account-requirements"></a>krav för vCenter-konto

Konfigurera konfigurations servern med ett konto som har en särskild roll tilldelad till sig.

- Roll tilldelningen måste tillämpas på vCenter-kontot för varje vCenter-objekt och inte spridas till de underordnade objekten. Den här konfigurationen säkerställer klient isoleringen eftersom åtkomst spridningen kan leda till oavsiktlig åtkomst till andra objekt.

    ![Alternativet Sprid till underordnade objekt](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- Den alternativa metoden är att tilldela användar kontot och rollen till datacenter-objektet och sprida dem till underordnade objekt. Ge sedan kontot **Ingen åtkomst** roll för varje objekt (till exempel virtuella datorer som tillhör andra klienter) som inte ska vara tillgängliga för en viss klient. Den här konfigurationen är besvärlig. Det visar oavsiktlig åtkomst kontroll, eftersom varje nytt underordnat objekt automatiskt beviljas åtkomst som ärvs från den överordnade. Därför rekommenderar vi att du använder den första metoden.

### <a name="create-a-vcenter-account"></a>Skapa ett vCenter-konto

1. Skapa en ny roll genom att klona den fördefinierade *skrivskyddade* rollen och ge den ett bekvämt namn (till exempel Azure_Site_Recovery, som du ser i det här exemplet).
2. Tilldela följande behörigheter till den här rollen:

   * **Data lager**: allokera utrymme, bläddra i data lager, lågnivå fil åtgärder, ta bort fil, uppdatera filer för virtuella datorer
   * **Nätverk**: nätverks tilldelning
   * **Resurs**: tilldela en virtuell dator till en resurspool, migrera avstängd virtuell dator, migrera från virtuell dator
   * **Aktiviteter**: Skapa uppgift, uppdatera uppgift
   * **VM-konfiguration**: alla
   * **VM-interaktion** > svars fråga, enhets anslutning, konfigurera CD-medium, konfigurera diskett medium, Stäng av, slå på, VMware-verktyg installera
   * **VM-inventering** > skapa från en befintlig, skapa ny, registrera, avregistrera
   * **VM-etablering** > Tillåt nedladdning av virtuell dator, Tillåt uppladdning av filer för virtuella datorer
   * **Hantering av VM-ögonblicksbild** > ta bort ögonblicks bilder

       ![Dialog rutan Redigera roll](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. Tilldela åtkomst nivåer till vCenter-kontot (används i klient konfigurations servern) för olika objekt enligt följande:

>| Objekt | Roll | Kommentarer |
>| --- | --- | --- |
>| vCenter | Read-Only | Krävs endast för att tillåta vCenter-åtkomst för hantering av olika objekt. Du kan ta bort den här behörigheten om kontot aldrig kommer att tillhandahållas till en klient eller används för hanterings åtgärder på vCenter. |
>| Datacenter | Azure_Site_Recovery |  |
>| Värd-och värd kluster | Azure_Site_Recovery | Säkerställer att åtkomsten finns på objekt nivå, så att endast tillgängliga värdar har virtuella klient datorer före redundans och efter återställning efter fel. |
>| Data lager och data lager kluster | Azure_Site_Recovery | Samma som föregående. |
>| Nätverk | Azure_Site_Recovery |  |
>| Hanteringsserver | Azure_Site_Recovery | Inkluderar åtkomst till alla komponenter (CS, PS och MT) utanför CS-datorn. |
>| Virtuella klient datorer | Azure_Site_Recovery | Säkerställer att alla nya klient-VM: ar för en viss klient också får den här åtkomsten, eller att de inte kan identifieras via Azure Portal. |

Åtkomst till vCenter-kontot är nu slutfört. Det här steget uppfyller minimi kraven för behörigheter för att slutföra återställnings åtgärder. Du kan också använda de här åtkomst behörigheterna med dina befintliga principer. Ändra bara dina befintliga behörigheter så att de innehåller roll behörigheter från steg 2, tidigare.

### <a name="failover-only"></a>Endast redundans
För att begränsa haveri beredskaps åtgärder upp till endast växling vid fel (det vill säga utan failback-funktioner), använder du föregående procedur, med följande undantag:

- I stället för att tilldela *Azure_Site_Recovery* rollen till vCenter-kontot tilldelar du bara en *skrivskyddad* roll till det kontot. Den här behörighets uppsättningen tillåter VM-replikering och redundans och tillåter inte återställning efter fel.
- Allt annat i föregående process förblir i befintligt skick. För att säkerställa att klient isoleringen och begränsar VM-identifieringen, är varje behörighet fortfarande tilldelad på objekt nivå och inte sprids till underordnade objekt.

### <a name="deploy-resources-to-the-tenant-subscription"></a>Distribuera resurser till klient prenumerationen

1. Skapa en resurs grupp på Azure Portal och distribuera sedan ett Recovery Services-valv enligt den vanliga processen.
2. Ladda ned valvregistreringsnyckeln.
3. Registrera CS för klienten med hjälp av valv registrerings nyckeln.
4. Ange autentiseringsuppgifterna för de två åtkomst kontona, kontot för åtkomst till vCenter-servern och kontot för att komma åt den virtuella datorn.

    ![Hanterarens konfigurations Server konton](./media/vmware-azure-multi-tenant-overview/config-server-account-display.png)

### <a name="register-servers-in-the-vault"></a>Registrera servrar i valvet

1. I det Azure Portal i valvet som du skapade tidigare registrerar du vCenter-servern på konfigurations servern med det vCenter-konto som du skapade.
2. Slutför processen "prepare Infrastructure" för Site Recovery per den vanliga processen.
3. De virtuella datorerna är nu redo att replikeras. Kontrol lera att bara klientens virtuella datorer visas i **Replikera**  >  **Välj virtuella datorer**.

## <a name="dedicated-hosting-solution"></a>Dedikerad värd lösning

Som du ser i följande diagram är arkitektur skillnaden i en dedikerad värd lösning att varje klient organisations infrastruktur har kon figurer ATS för den klienten.

![Diagram som visar den arkitektoniska skillnaden i en dedikerad värd lösning är att varje klients infrastruktur har kon figurer ATS för den klienten.](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**Dedikerat värd scenario med flera vCenter**

## <a name="managed-service-solution"></a>Hanterad tjänst lösning

Som du ser i följande diagram är arkitektur skillnaden i en hanterad tjänst lösning att varje klients infrastruktur också är fysiskt åtskild från andra innehavares infrastruktur. Det här scenariot finns vanligt vis när klienten äger infrastrukturen och vill att en lösnings leverantör ska kunna hantera haveri beredskap.

![arkitektur-delad – HSP](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**Hanterat tjänst scenario med flera vCenter**

## <a name="next-steps"></a>Nästa steg
- [Lär dig mer](site-recovery-role-based-linked-access-control.md) om rollbaserad åtkomst kontroll i Site Recovery.
- Lär dig hur du [konfigurerar haveri beredskap för virtuella VMware-datorer till Azure](vmware-azure-tutorial.md).
- Lär dig mer om [flera innehavare med CSP för virtuella VMware-datorer](vmware-azure-multi-tenant-csp-disaster-recovery.md).
