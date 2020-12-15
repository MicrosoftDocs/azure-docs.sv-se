---
title: Felsöka distribution och identifiering av Azure Migrate-enheter
description: Få hjälp med installation av installationer och dator identifiering.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 90abe6bf680f6a186b970631dcd0a42d6d36bf94
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511594"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Felsöka Azure Migrate-installationen och identifieringen

Den här artikeln hjälper dig att felsöka problem när du distribuerar [Azure Migrate](migrate-services-overview.md) -installationen och hur du använder-enheten för att identifiera lokala datorer.


## <a name="whats-supported"></a>Vad stöds?

[Granska](migrate-appliance.md) support kraven för installationen.


## <a name="invalid-ovf-manifest-entry"></a>"Ogiltig OVF-manifest post"

Om du får felet "den tillhandahållna manifest filen är ogiltig: ogiltig OVF-manifest post", gör du följande:

1. Kontrol lera att filen med ägg Azure Migrates apparaten laddas ned korrekt genom att kontrol lera dess hash-värde. [Läs mer](./tutorial-discover-vmware.md). Om hash-värdet inte matchar laddar du ned den och försöker distribuera igen.
2. Om distributionen fortfarande Miss lyckas och du använder VMware vSphere-klienten för att distribuera OVF-filen kan du prova att distribuera den via vSphere-webbklienten. Om distributionen fortfarande Miss lyckas kan du prova att använda en annan webbläsare.
3. Om du använder vSphere-webbklienten och försöker distribuera den på vCenter Server 6,5 eller 6,7 försöker du att distribuera de ägg som finns direkt på ESXi-värden:
   - Anslut till ESXi-värden direkt (i stället för vCenter Server) med webb klienten (https://<*host IP Address*>/UI).
   - I **hem**  >  **inventering** väljer du mall för **fil**  >  **distribution OVF**. Bläddra till de ägg och slutför distributionen.
4. Kontakta Azure Migrate support om distributionen fortfarande Miss lyckas.

## <a name="cant-connect-to-the-internet"></a>Det går inte att ansluta till Internet

Detta kan inträffa om installations datorn finns bakom en proxyserver.

- Ange autentiseringsuppgifterna om proxyn behöver dem.
- Om du använder en URL-baserad brand Väggs-proxy för att kontrol lera utgående anslutningar lägger du till [dessa URL: er](migrate-appliance.md#url-access) i en lista över tillåtna.
- Om du använder en avlyssnings-proxy för att ansluta till Internet importerar du det till den virtuella datorns dator med [de här stegen](./migrate-appliance.md).


## <a name="clicking-on-login-button-opens-a-new-tab-with-no-device-code"></a>När du klickar på knappen inloggning "öppnas en ny flik utan enhets kod

Om du efter att ha klickat på inloggning "på enhetens konfigurations hanterare, öppnas en ny flik utan kod, gå tillbaka till fliken installation av Configuration Manager och du hittar enhets koden i fetstil under knappen" inloggning "", som visas på skärm bilden nedan. Kopiera koden och klistra in den på fliken inloggning för att autentisera med Azure.

![Enhets kod](./media/troubleshoot-appliance-discovery/code.png)

## <a name="cant-sign-into-azure-from-the-appliance-web-app"></a>Det går inte att logga in på Azure från produktens webbapp

Felet "Tyvärr! vi har problem med att logga in dig" visas om du använder ett felaktigt Azure-konto för att logga in på Azure. Felet beror på ett par olika orsaker:

- Om du loggar in på enhetens webb program för det offentliga molnet kan du använda användar kontots autentiseringsuppgifter för den offentliga moln portalen.
- Om du loggar in på enhetens webb program för det offentliga molnet med autentiseringsuppgifter för användar kontot för den privata moln portalen.

Se till att du använder rätt autentiseringsuppgifter.

##  <a name="datetime-synchronization-error"></a>Fel vid synkronisering av datum/tid

Ett fel vid synkronisering av datum och tid (802) anger att Server klockan kanske inte är synkroniserad med den aktuella tiden med mer än fem minuter. Ändra klockan på den insamlade datorn så att den matchar den aktuella tiden:

1. Öppna en administratörs kommando tolk på den virtuella datorn.
2. Kör **w32tm/TZ** för att kontrol lera tids zonen.
3. Kör **w32tm/resync** för att synkronisera tiden.


## <a name="unabletoconnecttoserver"></a>"UnableToConnectToServer"

Om du får det här anslutnings felet kanske du inte kan ansluta till vCenter Server *servername*. com: 9443. Fel informationen indikerar att det inte finns någon slut punkt `https://\*servername*.com:9443/sdk` som kan ta emot meddelandet.

- Kontrol lera om du kör den senaste versionen av installationen. Om du inte gör det uppgraderar du installationen till den [senaste versionen](./migrate-appliance.md).
- Om problemet fortfarande uppstår i den senaste versionen kanske installationen inte kan lösa det angivna vCenter Server namnet, eller så kan den angivna porten vara fel. Om porten inte anges försöker insamlaren som standard att ansluta till port nummer 443.

    1. Pinga *servername*. com från enheten.
    2. Om steg 1 Miss lyckas försöker du ansluta till vCenter-servern med hjälp av IP-adressen.
    3. Identifiera rätt port nummer för att ansluta till vCenter Server.
    4. Kontrol lera att vCenter Server är igång.


## <a name="error-6005260039-appliance-might-not-be-registered"></a>Fel 60052/60039: installationen kanske inte kan registreras

- Fel 60052, "installationen kanske inte har registrerats till Azure Migrate projektet" inträffar om det Azure-konto som används för att registrera installationen har otillräcklig behörighet.
    - Se till att Azure-användarkontot som används för att registrera installationen har minst deltagar behörighet för prenumerationen.
    - [Lär dig mer](./migrate-appliance.md#appliance---vmware) om de Azure-roller och behörigheter som krävs.
- Fel 60039, "installationen kanske inte kan registreras Azure Migrate projektet" kan inträffa om registreringen Miss lyckas eftersom det Azure Migrate-projekt som används för att registrera installationen inte kan hittas.
    - I Azure Portal och kontrol lera om projektet finns i resurs gruppen.
    - Om projektet inte finns skapar du ett nytt Azure Migrate-projekt i resurs gruppen och registrerar enheten igen. [Lär dig hur du](./create-manage-projects.md#create-a-project-for-the-first-time) skapar ett nytt projekt.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Fel 60030/60031: Key Vault hanterings åtgärd misslyckades

Om du får felet 60030 eller 60031 kan du göra följande för att utföra en Azure Key Vault hanterings åtgärd:
- Kontrol lera att Azure-användarkontot som används för att registrera installationen har minst deltagar behörighet för prenumerationen.
- Kontrol lera att kontot har åtkomst till nyckel valvet som anges i fel meddelandet och försök sedan igen.
- Kontakta Microsofts supportavdelning om problemet kvarstår.
- [Lär dig mer](./migrate-appliance.md#appliance---vmware) om de Azure-roller och behörigheter som krävs.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Fel 60028: det gick inte att initiera identifiering

Fel 60028: "Det gick inte att initiera identifieringen på grund av ett fel. Åtgärden kunde inte utföras för den angivna listan över värdar eller kluster "anger att identifiering inte kunde startas på värdarna som anges i felet på grund av ett problem med att komma åt eller hämta information om virtuella datorer. Resten av värdarna har lagts till.

- Lägg till värdarna i listan i fel igen med alternativet **Lägg till värd** .
- Om det finns ett verifierings fel kan du läsa igenom reparations vägledningen för att åtgärda felen och sedan försöka **Spara och starta identifierings** alternativet igen.

## <a name="error-60025-azure-ad-operation-failed"></a>Fel 60025: Azure AD-åtgärd misslyckades 
Fel 60025: "en Azure AD-åtgärd misslyckades. Felet inträffade när Azure AD-programmet skapades eller uppdaterades när Azure-användarkontot som används för att initiera identifieringen skiljer sig från det konto som används för att registrera installationen. Gör något av följande:

- Se till att det användar konto som initierar identifieringen är samma som det som används för att registrera enheten.
- Ange åtkomst behörighet för Azure Active Directory program till det användar konto som identifierings åtgärden misslyckades för.
- Ta bort resurs gruppen som skapades tidigare för det Azure Migrate projektet. Skapa en annan resurs grupp för att starta igen.
- [Läs mer](./migrate-appliance.md#appliance---vmware) om Azure Active Directory program behörigheter.


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>Fel 50004: det går inte att ansluta till värd eller kluster

Fel 50004: "det går inte att ansluta till en värd eller ett kluster eftersom Server namnet inte kan matchas. WinRM-felkod: 0x803381B9 "kan inträffa om Azure DNS tjänsten för installationen inte kan matcha klustret eller värd namnet som du har angett.

- Om du ser det här felet i klustret, FQDN.
- Du kan också se det här felet för värdar i ett kluster. Detta anger att enheten kan ansluta till klustret, men klustret returnerar värdnamn som inte är FQDN. Lös problemet genom att uppdatera hosts-filen på-enheten genom att lägga till en mappning av IP-adress och värdnamn:
    1. Öppna anteckningar som administratör.
    2. Öppna filen C:\Windows\System32\Drivers\etc\hosts.
    3. Lägg till IP-adressen och värd namnet i en rad. Upprepa detta för varje värd eller kluster där du ser det här felet.
    4. Spara och stäng värdfilen.
    5. Kontrol lera om enheten kan ansluta till värdarna med appen för hantering av appar. Efter 30 minuter bör du se den senaste informationen för de här värdarna i Azure Portal.


## <a name="error-60001-unable-to-connect-to-server"></a>Fel 60001: det går inte att ansluta till servern 

- Se till att det finns en anslutning från-enheten till servern
- Om det är en Linux-server, se till att lösenordsbaserad autentisering är aktiverat med följande steg:
    1. Logga in på Linux-datorn och öppna SSH-konfigurationsfilen med kommandot ' vi/etc/ssh/sshd_config '
    2. Ange alternativet "PasswordAuthentication" till Ja. Spara filen.
    3. Starta om SSH-tjänsten genom att köra tjänsten sshd restart
- Om det är en Windows-Server kontrollerar du att port 5985 är öppen för att tillåta fjärr-WMI-anrop.
- Om du identifierar en GCP Linux-server och använder en rot användare använder du följande kommandon för att ändra standardinställningen för rot inloggning
    1. Logga in på Linux-datorn och öppna SSH-konfigurationsfilen med kommandot ' vi/etc/ssh/sshd_config '
    2. Ange alternativet "PermitRootLogin" till Ja.
    3. Starta om SSH-tjänsten genom att köra tjänsten sshd restart

## <a name="error-no-suitable-authentication-method-found"></a>Fel: ingen lämplig autentiseringsmetod hittades

Se till att lösenordsbaserad autentisering är aktiverat på Linux-servern med hjälp av följande steg:
    1. Logga in på Linux-datorn och öppna SSH-konfigurationsfilen med kommandot ' vi/etc/ssh/sshd_config '
    2. Ange alternativet "PasswordAuthentication" till Ja. Spara filen.
    3. Starta om SSH-tjänsten genom att köra tjänsten sshd restart


## <a name="discovered-vms-not-in-portal"></a>Identifierade virtuella datorer som inte är i portalen

Om identifierings status är "identifiering pågår", men ännu inte ser de virtuella datorerna i portalen, väntar du några minuter:
- Det tar ungefär 15 minuter för en virtuell VMware-dator.
- Det tar ungefär två minuter för varje tillagd värd för identifiering av virtuella Hyper-V-datorer.

Om du väntar och statusen inte ändras väljer du **Uppdatera** på fliken **servrar** . Detta ska visa antalet identifierade servrar i Azure Migrate: Server utvärdering och Azure Migrate: Server-migrering.

Om detta inte fungerar och du identifierar VMware-servrar:

- Kontrol lera att det vCenter-konto som du har angett har behörigheten korrekt konfigurerad, med åtkomst till minst en virtuell dator.
- Azure Migrate kan inte identifiera virtuella VMware-datorer om vCenter-kontot har åtkomst beviljad på nivån vCenter VM Folder. [Läs mer](set-discovery-scope.md) om omfattnings identifiering.

## <a name="vm-data-not-in-portal"></a>VM-data som inte ingår i portalen

Om identifierade virtuella datorer inte visas i portalen eller om VM-data är inaktuella, väntar du några minuter. Det tar upp till 30 minuter för ändringar i identifierade VM-konfigurationsdata som visas i portalen. Det kan ta några timmar innan ändringar i program data visas. Om det inte finns några data efter den här tiden kan du prova att uppdatera enligt följande

1. I **servrar**  >  **Azure Migrate Server utvärdering** väljer du **Översikt**.
2. Under **Hantera** väljer du **agenthälsa**.
3. Välj **Uppdatera agent**.
4. Vänta tills uppdaterings åtgärden har slutförts. Nu bör du se uppdaterad information.

## <a name="deleted-vms-appear-in-portal"></a>Borttagna virtuella datorer visas i portalen

Om du tar bort virtuella datorer och de fortfarande visas i portalen väntar du i 30 minuter. Om de fortfarande visas uppdaterar du enligt beskrivningen ovan.

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>Jag ser inte prestanda data för vissa nätverkskort på mina fysiska servrar

Detta kan inträffa om Hyper-V-virtualisering är aktiverat på den fysiska servern. På grund av en produkt lucka samlas nätverks data flödet på de virtuella nätverkskort som identifierats.

## <a name="error-the-file-uploaded-is-not-in-the-expected-format"></a>Fel: Den överförda filen har inte det förväntade formatet
Vissa verktyg har regionala inställningar som gör att CSV-filen skapas med semikolon som avgränsare. Ändra inställningarna så att kommatecken används som avgränsare.

## <a name="i-imported-a-csv-but-i-see-discovery-is-in-progress"></a>Jag importerade en CSV men meddelandet ”Identifiering pågår” visas
Den här statusen visas om din CSV-överföring misslyckades på grund av ett verifierings fel. Prova att importera CSV-filen igen. Du kan ladda ned felrapporten från föregående uppladdning och följa reparationsrekommendationerna i filen för att åtgärda felen. Felrapporten kan laddas ned från avsnittet ”Importera information” på sidan ”Identifiera datorer”.

## <a name="do-not-see-application-details-even-after-updating-guest-credentials"></a>Se inte programinformation trots att gäst uppgifter har uppdaterats
Program identifieringen körs en gång var 24: e timme. Om du vill se informationen omedelbart uppdaterar du enligt följande. Det kan ta några minuter beroende på Nej. identifierade virtuella datorer.

1. I **servrar**  >  **Azure Migrate Server utvärdering** väljer du **Översikt**.
2. Under **Hantera** väljer du **agenthälsa**.
3. Välj **Uppdatera agent**.
4. Vänta tills uppdaterings åtgärden har slutförts. Nu bör du se uppdaterad information.

## <a name="unable-to-export-application-inventory"></a>Det gick inte att exportera program inventeringen
Se till att användaren laddar ned inventeringen från portalen har deltagar behörighet för prenumerationen.

## <a name="no-suitable-authentication-method-found-to-complete-authentication-publickey"></a>Det gick inte att hitta någon lämplig autentiseringsmetod för att slutföra autentisering (PublicKey)
Nyckelbaserad autentisering fungerar inte, Använd lösenordsautentisering.

## <a name="common-app-discovery-errors"></a>Vanliga fel vid identifiering av appar

Azure Migrate stöder identifiering av program, roller och funktioner med hjälp av Azure Migrate: Server utvärdering. Identifiering av appar stöds för närvarande endast för VMware. [Läs mer](how-to-discover-applications.md) om kraven och stegen för att konfigurera identifiering av appar.

Vanliga fel för identifiering av appar sammanfattas i tabellen. 

**Fel** | **Orsak** | **Åtgärd**
--- | --- | ---
9000: det går inte att identifiera VMware-verktygets status.     |   VMWare-verktyg kanske inte har installerats eller är skadade.    |   Se till att VMware-verktygen är installerade och körs på den virtuella datorn.
9001: VMware-verktyg är inte installerat.     |   VMWare-verktyg kanske inte har installerats eller är skadade.    |   Se till att VMware-verktygen är installerade och körs på den virtuella datorn.
9002: VMware-verktyg körs inte.   |   VMWare-verktyg kanske inte har installerats eller är skadade.    |   Se till att VMware-verktygen är installerade och körs på den virtuella datorn.
9003: operativ system typen stöds inte för identifiering av gäst datorer.    |   Operativ system som körs på servern är varken Windows eller Linux.    |   Operativ system typer som stöds är endast Windows och Linux. Om servern verkligen är Windows eller Linux kontrollerar du vilken typ av operativ system som anges i vCenter Server.
9004: den virtuella datorn körs inte.     |   Den virtuella datorn är avstängd.  |   Se till att den virtuella datorn är påslagen.
9005: operativ system typen stöds inte för identifiering av gäst datorer.    |   Typ av operativ system stöds inte för identifiering av gäst datorer.     |   Operativ system typer som stöds är endast Windows och Linux.
9006: URL: en för att ladda ned metadatafilen från gästen är tom.     |   Detta kan inträffa om identifierings agenten inte fungerar som förväntat.    |   Problemet bör automatiskt lösa in24 timmar. Kontakta Microsoft Support om problemet kvarstår.
9007: det gick inte att hitta processen som kör identifierings aktiviteten i den virtuella gäst datorn.   |   Detta kan inträffa om identifierings agenten inte fungerar korrekt.   |   Problemet bör lösas automatiskt om 24 timmar. Kontakta Microsoft Support om problemet kvarstår.
9008: det går inte att hämta den virtuella gäst datorns process status.   |   Problemet kan uppstå på grund av ett internt fel.   |   Problemet bör lösas automatiskt om 24 timmar. Kontakta Microsoft Support om problemet kvarstår.
9009: Windows UAC har förhindrat körning av identifierings uppgift på servern.  |   Windows User Account Control (UAC)-inställningar på servern är restriktiva och förhindrar identifiering av installerade program.  |   I User Account Control inställningar på servern konfigurerar du att UAC-inställningen ska vara på en av de lägre två nivåerna.
9010: VM är avstängd.     |   Den virtuella datorn är avstängd.  |   Se till att den virtuella datorn är påslagen.
9011: det gick inte att hitta den identifierade metadatafilen i fil systemet för den virtuella gäst datorn.    |   Problemet kan uppstå på grund av ett internt fel.   |   Problemet bör lösas automatiskt om 24 timmar. Kontakta Microsoft Support om problemet kvarstår.
9012: den identifierade metadatafilen är tom.     |   Problemet kan uppstå på grund av ett internt fel.   |   Problemet bör lösas automatiskt om 24 timmar. Kontakta Microsoft Support om problemet kvarstår.
9013: en ny tillfällig profil skapas för varje inloggning.    |   En ny tillfällig profil skapas för varje inloggning till den virtuella VMware-datorn.    |   Kontakta Microsoft Support för att få en lösning.
9014: det går inte att hämta metadata från fil systemet för den virtuella gäst datorn.     |   Ingen anslutning till ESXi-värd    |   Se till att enheten kan ansluta till port 443 på den ESXi-värd som kör den virtuella datorn
9015: gäst åtgärds rollen är inte aktive rad på vCenter-användarkontot   |   Gäst drifts rollen är inte aktive rad på vCenter-användarkontot.   |   Se till att rollen gäst åtgärder är aktive rad på vCenter-användarkontot.
9016: det går inte att identifiera eftersom gäst åtgärds agenten är inaktuell.   |   VMware-verktygen har inte installerats korrekt eller är inte aktuella.    |   Se till att VMware-verktygen är korrekt installerade och aktuella.
9017: det gick inte att hitta filen med identifierade metadata på den virtuella datorn.  |   Problemet kan uppstå på grund av ett internt fel.   |   Kontakta Microsoft Support för att få en lösning.
9018: PowerShell är inte installerat på de virtuella gäst datorerna.  |   PowerShell är inte tillgängligt på den virtuella gäst datorn.    |   Installera PowerShell på den virtuella gäst datorn.
9019: det gick inte att identifiera på grund av problem med den virtuella gäst datorn.     |   Det gick inte att utföra VMware-gästen på den virtuella datorn.    |   Se till att autentiseringsuppgifterna för den virtuella datorn är giltiga och att användar namnet som angavs i autentiseringsuppgifterna för gäst-VM är i UPN-format.
9020: behörigheten att skapa fil nekas.    |   Rollen som är associerad med användaren eller grup principen begränsar användaren från att skapa filen i mappen    |   Kontrol lera att den angivna gäst användaren har behörigheten Skapa för filen i mappen. Se **meddelanden** i Server utvärderingen för mappens namn.
9021: det gick inte att skapa filen i systemets Temp-sökväg.     |   VMware-verktyget rapporterar systemets Temp-sökväg i stället för användarens tillfälliga sökväg.    |   Uppgradera VMware-verktygets version över 10287 (NGC/VI-klient format).
9022: åtkomst till WMI-objekt nekas.    |   Rollen som är associerad med användaren eller grup principen begränsar användaren från att komma åt WMI-objektet.  |   Kontakta Microsoft Support.
9023: det går inte att köra PowerShell eftersom variabeln i systemroots miljön är tom.    |   Värdet för systemroot-miljövariabeln är tomt för den virtuella gäst datorn.     |   Kontakta Microsoft Support för att få en lösning.
9024: det går inte att identifiera eftersom miljövariabelt värde för TEMPORÄRt miljö är tomt.    |   Värdet för miljövariabeln TEMP är tomt för den virtuella gäst datorn.   |   Kontakta Microsoft Support.
9025: PowerShell är skadat i de virtuella gäst datorerna.  |   PowerShell är skadat på den virtuella gäst datorn.    |   Installera om PowerShell i den virtuella gäst datorn och kontrol lera att PowerShell kan köras på den virtuella gäst datorn.
9026: det går inte att köra gäst åtgärder på den virtuella datorn.  |   VM-tillstånd tillåter inte att gäst åtgärder körs på den virtuella datorn.   |   Kontakta Microsoft Support för att få en lösning.
9027: gäst åtgärds agenten körs inte på den virtuella datorn.   |   Det gick inte att kontakta gäst drifts agenten som körs i den virtuella datorn.    |   Kontakta Microsoft Support för att få en lösning.
9028: det går inte att skapa filen eftersom det inte finns tillräckligt med disk utrymme i den virtuella datorn.     |   Det finns inte tillräckligt med utrymme på disken.   |   Se till att det finns tillräckligt med utrymme i den virtuella datorns disk utrymme.
9029: ingen åtkomst till PowerShell på den virtuella gäst datorns autentiseringsuppgifter.   |   Åtkomst till PowerShell är inte tillgänglig för användaren.     |   Se till att användaren som har lagts till på-enheten har åtkomst till PowerShell på den virtuella gäst datorn.
9030: det gick inte att samla in identifierade metadata eftersom ESXi-värden är frånkopplad.     |   ESXi-värden är i frånkopplat tillstånd.   |   Se till att den ESXi-värd som kör den virtuella datorn är ansluten.
9031: det gick inte att samla in identifierade metadata eftersom ESXi-värden inte svarar.   |   Fjärrvärden är i ett ogiltigt tillstånd.    |   Se till att den ESXi-värd som kör den virtuella datorn körs och är ansluten.
9032: det gick inte att identifiera på grund av ett internt fel.   |   Problemet kan uppstå på grund av ett internt fel.   |   Kontakta Microsoft Support för att få en lösning.
9033: det går inte att identifiera eftersom VM-användarnamnet innehåller ogiltiga tecken.     |   Ogiltiga tecken upptäcktes i användar namnet.   |   Ange autentiseringsuppgifter för den virtuella datorn igen och se till att det inte finns några ogiltiga tecken.
9034: det angivna användar namnet är inte i UPN-format.    |   Användar namnet är inte i UPN-format.  |   Se till att användar namnet är i UPN-format (User Principal Name).
9035: det går inte att identifiera eftersom PowerShell-språkläget inte har angetts till fullständigt språk.  |   Språk läge för PowerShell i den virtuella gäst datorn har inte ställts in på fullt språk.   |   Se till att PowerShell-språkläget är inställt på "fullständigt språk".
9037: data insamling pausas tillfälligt eftersom den virtuella datorns svars tid är för hög.    |   Den identifierade virtuella datorn tar för lång tid att svara     |   Ingen åtgärd krävs. Ett nytt försök kommer att göras inom 24 timmar för program identifiering och 3 timmar för beroende analys (utan agent).
10000: operativ system typen stöds inte.   |   Operativ system som körs på servern är varken Windows eller Linux.    |   Operativ system typer som stöds är endast Windows och Linux.
10001: det gick inte att hitta skript för Server identifiering på enheten.    |   Identifiering fungerar inte som förväntat.   |   Kontakta Microsoft Support för att få en lösning.
10002: identifierings uppgiften har inte slutförts i tid.     |   Identifierings agenten fungerar inte som förväntat.     |   Problemet bör lösas automatiskt om 24 timmar. Kontakta Microsoft Support om problemet kvarstår.
10003: processen som kör identifierings aktiviteten avslutades med ett fel.    |   Processen som kör identifierings aktiviteten avslutades med ett fel.  |   Problemet bör lösas automatiskt om 24 timmar. Om problemet kvarstår kontaktar du Microsoft Support.
10004: autentiseringsuppgifter har inte angetts för typen av gäst operativ system.  |   Autentiseringsuppgifter för åtkomst till datorer av den här operativ system typen har inte tillhandahållits i Azure Migrate-installationen.    |   Lägg till autentiseringsuppgifter för datorer på enheten
10005: de angivna autentiseringsuppgifterna är inte giltiga.   |   Autentiseringsuppgifterna för att få åtkomst till servern är felaktiga.  |   Uppdatera de autentiseringsuppgifter som anges i installationen och se till att servern kan nås med autentiseringsuppgifterna.
10006: gäst operativ system typen stöds inte av arkivet för autentiseringsuppgifter.  |   Operativ system som körs på servern är varken Windows eller Linux.    |   Operativ system typer som stöds är endast Windows och Linux.
10007: det gick inte att bearbeta identifierade metadata.    |   Ett fel uppstod vid försök att deserialisera JSON.    |   Kontakta Microsoft Support för att få en lösning.
10008: det gick inte att skapa en fil på servern.    |  Problemet kan uppstå på grund av ett internt fel.    |   Kontakta Microsoft Support för att få en lösning.
10009: det gick inte att skriva identifierade metadata till en fil på servern.  |   Problemet kan uppstå på grund av ett internt fel.   |   Kontakta Microsoft Support för att få en lösning.




## <a name="next-steps"></a>Nästa steg
Konfigurera en installation för [VMware](how-to-set-up-appliance-vmware.md), [Hyper-V](how-to-set-up-appliance-hyper-v.md)eller [fysiska servrar](how-to-set-up-appliance-physical.md).