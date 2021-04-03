---
title: Konfigurera en Windows Virtual Desktop Disaster Recovery plan – Azure
description: Konfigurera en verksamhets kontinuitet och katastrof återställnings plan för distributionen av virtuella Windows-datorer.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 968f82a143872fd282222539ab71a70db488a20d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91935770"
---
# <a name="set-up-a-business-continuity-and-disaster-recovery-plan"></a>Konfigurera en verksamhets kontinuitet och katastrof återställnings plan

För att skydda din organisations data kan du behöva införa en strategi för affärs kontinuitet och haveri beredskap (BCDR). En BCDR strategi håller dina appar och arbets belastningar igång under planerade och oplanerade drift-eller Azure-avbrott.

Windows Virtual Desktop erbjuder BCDR för Windows Virtual Desktop-tjänsten för att bevara kundmetadata under avbrott. När ett avbrott uppstår i en region växlar tjänstens infrastruktur komponenter över till den sekundära platsen och fortsätter att fungera som vanligt. Du kan fortfarande komma åt tjänsterelaterade metadata och användare kan fortfarande ansluta till tillgängliga värdar. Slut användar anslutningarna kommer att vara online så länge klient miljön eller värdarna är tillgängliga.

För att se till att användarna fortfarande kan ansluta under ett regions avbrott måste du replikera deras virtuella datorer (VM) på en annan plats. Under avbrott växlar den primära platsen över till de replikerade virtuella datorerna på den sekundära platsen. Användare kan fortsätta att komma åt appar från den sekundära platsen utan avbrott. Utöver VM-replikering måste du hålla användar identiteter tillgängliga på den sekundära platsen. Om du använder profil behållare måste du också replikera dem. Se slutligen till att dina affärsappar som förlitar sig på data på den primära platsen kan redundansväxla med resten av data.

Om du vill att användarna ska vara anslutna under ett avbrott måste du göra följande i den här ordningen:

- Replikera de virtuella datorerna på en sekundär plats.
- Om du använder profil behållare ställer du in datareplikering på den sekundära platsen.
- Se till att användar identiteter som du har konfigurerat på den primära platsen är tillgängliga på den sekundära platsen.
- Se till att alla branschspecifika program som förlitar sig på data på den primära platsen har redundansväxlats till den sekundära platsen.

## <a name="vm-replication"></a>VM-replikering

Först måste du replikera dina virtuella datorer till den sekundära platsen. Dina alternativ för att göra detta beror på hur dina virtuella datorer konfigureras:

- Du kan konfigurera alla virtuella datorer för både poolbaserade och personliga värdbaserade pooler med Azure Site Recovery. Med den här metoden behöver du bara konfigurera en adresspool och dess relaterade app-grupper och arbets ytor.
- Du kan skapa en ny adresspool i området redundans samtidigt som du har inaktiverat alla resurser på din plats för redundans. För den här metoden måste du konfigurera nya app-grupper och arbets ytor i området för redundans. Du kan sedan använda en Azure Site Recovery-plan för att aktivera värdar för pooler.
- Du kan skapa en adresspool som är ifylld av virtuella datorer som skapats i både den primära regionen och redundansväxlingen samtidigt som de virtuella datorerna i redundansväxlingen är inaktiverade. I så fall behöver du bara konfigurera en adresspool och dess relaterade app-grupper och arbets ytor. Du kan använda en Azure Site Recovery plan för att sätta på pooler med den här metoden.

Vi rekommenderar att du använder [Azure Site Recovery](../site-recovery/site-recovery-overview.md) för att hantera replikering av virtuella datorer på andra Azure-platser, enligt beskrivningen i [Azure-till-Azure Disaster Recovery-arkitekturen](../site-recovery/azure-to-azure-architecture.md). Vi rekommenderar särskilt att du använder Azure Site Recovery för personliga värdar, eftersom Azure Site Recovery stöder både [serverbaserade och klientbaserade SKU: er](../site-recovery/azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

Om du använder Azure Site Recovery behöver du inte registrera de virtuella datorerna manuellt. Windows Virtual Desktop-agenten i den sekundära virtuella datorn använder automatiskt den senaste säkerhetstoken för att ansluta till tjänst instansen närmast den. Den virtuella datorn (Session Host) på den sekundära platsen kommer automatiskt att bli en del av den här poolen. Slutanvändaren måste återansluta under processen, men förutom att det inte finns några andra manuella åtgärder.

Om det finns befintliga användar anslutningar under avbrottet måste du avsluta användar anslutningarna i den aktuella regionen innan administratören kan starta redundans till den sekundära regionen.

Kör denna cmdlet för att koppla från användare i Windows Virtual Desktop (klassisk):

```powershell
Invoke-RdsUserSessionLogoff
```

Kör denna cmdlet för att koppla från användare i den Azure-integrerade versionen av Windows Virtual Desktop:

```powershell
Remove-AzWvdUserSession
```

När du har loggat ut alla användare i den primära regionen kan du redundansväxla de virtuella datorerna i den primära regionen och låta användarna ansluta till de virtuella datorerna i den sekundära regionen. Mer information om hur den här processen fungerar finns i [Replikera virtuella Azure-datorer till en annan Azure-region](../site-recovery/azure-to-azure-how-to-enable-replication.md).

## <a name="virtual-network"></a>Virtuellt nätverk

Ta sedan hänsyn till nätverks anslutningen under avbrottet. Du måste kontrol lera att du har konfigurerat ett virtuellt nätverk (VNET) i den sekundära regionen. Om dina användare behöver åtkomst till lokala resurser måste du konfigurera det virtuella nätverket för att få åtkomst till dem. Du kan upprätta lokala anslutningar med en VPN-, ExpressRoute-eller virtuell WAN-anslutning.

Vi rekommenderar att du använder Azure Site Recovery för att konfigurera VNET i redundansväxlingen eftersom det bevarar inställningarna för det primära nätverket och inte behöver peering.

## <a name="user-identities"></a>Användar identiteter

Se sedan till att domänkontrollanten är tillgänglig på den sekundära platsen. 

Det finns tre sätt att hålla domänkontrollanten tillgänglig:

   - Ha Active Directory-domän kontrollant på den sekundära platsen
   - Använd en lokal Active Directory-domän kontroll enhet
   - Replikera Active Directory-domän styrenheten med [Azure Site Recovery](../site-recovery/site-recovery-active-directory.md)

## <a name="user-and-app-data"></a>Användar-och AppData

Om du använder profil behållare är nästa steg att konfigurera datareplikering på den sekundära platsen. Du har fem alternativ för att lagra FSLogix-profiler:

   - Lagringsdirigering (S2D)
   - Nätverks enheter (VM med extra enheter)
   - Azure Files
   - Azure NetApp Files
   - Molnbaserad cache för replikering

Mer information finns [i lagrings alternativ för FSLogix profil behållare i Windows Virtual Desktop](store-fslogix-profile.md).

Om du konfigurerar haveri beredskap för profiler är följande alternativ:

   - Konfigurera intern Azure-replikering (till exempel Azure Files replikering av standard lagrings konto, Azure NetApp Files replikering eller Azure Files synkronisering för fil servrar).
    
     >[!NOTE]
     >NetApp-replikeringen sker automatiskt när du först har konfigurerat den. Med Azure Site Recovery planer kan du lägga till för skript och efter skript för att redundansväxla icke-VM-resurser replikera Azure Storage resurser.

   - Konfigurera FSLogix Cloud cache för både app-och användar data.
   - Konfigurera katastrof återställning för AppData enbart för att säkerställa åtkomst till affärs kritiska data hela tiden. Med den här metoden kan du hämta användar data när du har avbrottet.

Låt oss ta en titt på hur du konfigurerar FSLogix för att konfigurera haveri beredskap för varje alternativ.

### <a name="fslogix-configuration"></a>FSLogix-konfiguration

FSLogix-agenten har stöd för flera profil platser om du konfigurerar register posterna för FSLogix.

Så här konfigurerar du register poster:

1. Öppna **Registereditorn**.
2. Gå till **dator**  >  **HKEY_LOCAL_MACHINE**  >  **program**  >  **FSLogix**  >  **profiler**.
   
     > [!div class="mx-imgBorder"]
     > ![En skärm bild av profil fönstret i Registereditorn. VHDLocation har valts.](media/regedit-profiles.png)

3. Högerklicka på **VHDLocations** och välj **Redigera multi-String**.

     > [!div class="mx-imgBorder"]
     > ![En skärm bild av fönstret Redigera multi-sträng. I värde data visas platser för Centrual USA och östra USA.](media/multi-string-edit.png)

4. I fältet **Värde data** anger du de platser som du vill använda.
5. När du är klar väljer du **Ok**.

Om den första platsen inte är tillgänglig växlar FSLogix-agenten automatiskt över till den andra, och så vidare.

Vi rekommenderar att du konfigurerar FSLogix-agenten med en sökväg till den sekundära platsen i huvud regionen. När den primära platsen stängs av replikeras FLogix-agenten som en del av den virtuella datorn Azure Site Recovery replikeringen. När de replikerade virtuella datorerna är klara försöker agenten automatiskt att försöka med en sökväg till den sekundära regionen.

Anta till exempel att de virtuella datorerna i den centrala sessionen är i regionen Central USA, men din profil behållare är i den centrala regionen för prestanda skäl.

I det här fallet konfigurerar du FSLogix-agenten med en sökväg till lagringen i Central USA. Du kan konfigurera de virtuella datorerna i sessionen som ska replikeras i västra USA. När sökvägen till Central USA Miss lyckas försöker agenten att skapa en ny sökväg för lagring i västra USA i stället.

### <a name="s2d"></a>S2D

Eftersom S2D hanterar replikering mellan regioner internt, behöver du inte konfigurera den sekundära sökvägen manuellt.

### <a name="network-drives-vm-with-extra-drives"></a>Nätverks enheter (VM med extra enheter)

Om du replikerar de virtuella datorerna för virtuella datorer med hjälp av Azure Site Recovery som virtuella datorer i sessionen, behåller återställningen samma sökväg, vilket innebär att du inte behöver konfigurera om FSlogix.

### <a name="azure-files"></a>Azure Files

Azure Files stöder asynkron replikering över regioner som du kan ange när du skapar lagrings kontot. Om den asynkrona typen av Azure Files redan täcker dina katastrof återställnings mål behöver du inte göra ytterligare konfiguration.

Om du behöver synkron replikering för att minimera data förlust rekommenderar vi att du använder FSLogix Cloud cache i stället.

>[!NOTE]
>Det här avsnittet beskriver inte mekanismen för autentisering av redundans för Azure Files.

### <a name="azure-netapp-files"></a>Azure NetApp Files

Läs mer om Azure NetApp Files i [skapa replikerings-peering för Azure NetApp Files](../azure-netapp-files/cross-region-replication-create-peering.md).

## <a name="app-dependencies"></a>Appsamband

Se slutligen till att alla affärsappar som förlitar sig på data som finns i den primära regionen kan redundansväxla till den sekundära platsen. Se också till att konfigurera de inställningar som apparna behöver för att fungera på den nya platsen. Om till exempel en av apparna är beroende av SQL-Dataservern, se till att replikera SQL på den sekundära platsen. Du bör konfigurera appen så att den använder den sekundära platsen som en del av redundansväxlingen eller som standard konfiguration. Du kan modellera program beroenden på Azure Site Recovery planer. Mer information finns i [om återställnings planer](../site-recovery/recovery-plan-overview.md).

## <a name="disaster-recovery-testing"></a>Test av haveri beredskap

När du är klar med att konfigurera haveri beredskap vill du testa planen och se till att den fungerar.

Här följer några förslag på hur du kan testa planen:

- Om de virtuella test datorerna har Internet åtkomst tar de över alla befintliga sessionsnycklar för nya anslutningar, men alla befintliga anslutningar till den ursprungliga sessionen är aktiva. Se till att administratören som kör testet loggar ut alla aktiva användare innan du testar planen. 
- Du bör endast utföra fullständiga haveri beredskap under en underhålls period för att inte störa dina användare. Du kan också använda en modempool i validerings miljön för testet. 
- Se till att testet täcker alla affärs kritiska appar.
- Vi rekommenderar att du bara växlar upp till 100 virtuella datorer i taget. Om du har fler virtuella datorer än så rekommenderar vi att du växlar över dem i grupper om 10 minuter.

## <a name="next-steps"></a>Nästa steg

Om du har frågor om hur du skyddar dina data, förutom att planera för avbrott, kan du kolla i vår [säkerhets guide](security-guide.md).