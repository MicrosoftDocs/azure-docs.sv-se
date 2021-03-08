---
title: Använd Azure Lab Services för Hackathon
description: I den här artikeln beskrivs hur du använder Azure Lab Services för att skapa labb som du kan använda för att köra Hackathon kickar.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d4648def48557d70547ef7d41d6c3e516671849e
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452738"
---
# <a name="use-azure-lab-services-for-your-next-hackathon"></a>Använd Azure Lab Services för nästa Hackathon
Azure Lab Services är utformad för att vara lätt att använda så att du snabbt kan skapa ett nytt labb av virtuella datorer (VM) för din Hackathon.  Använd följande check lista för att se till att din Hackathon går så smidigt som möjligt. Den här check listan bör utföras av IT-avdelningen eller fakulteten som ansvarar för att skapa och hantera ditt Hackathon-labb. 

Om du vill använda labb tjänster för din Hackathon, se till att både labb kontot och labbet skapas minst några dagar innan du börjar med din Hackathon. Följ också anvisningarna nedan:

## <a name="guidance"></a>Vägledning

- **Skapa labbet i en region eller plats som är närmast deltagarna**. 

    Du kan minska svars tiden genom att skapa ditt labb i en region som är närmast dina Hackathon-deltagare.  Om deltagarna är placerade över hela världen, måste du använda det bästa omdömet för att skapa ett labb som finns centralt.  Eller dela upp Hackathon för att använda flera labb baserat på de platser där deltagarna finns.
- **Välj den beräknings storlek som passar bäst för användnings behov**.

    I allmänhet är ju större beräknings storlek, desto snabbare kommer den virtuella datorn att fungera. För att begränsa kostnaderna måste du dock välja lämplig beräknings storlek baserat på deltagarnas behov. Se [storleks information för virtuella datorer i administratörs guiden](administrator-guide.md#vm-sizing) för mer information om de tillgängliga beräknings storlekarna.
- **Konfigurera RDP\SSH för fjärr skrivbords anslutning till virtuella Linux-datorer**.

    Om din Hackathon använder virtuella Linux-datorer, se till att fjärr skrivbord är aktiverat så att deltagarna kan använda antingen RDP (Remote Desktop Protocol) eller SSH (Secure Shell) för att ansluta till sina virtuella datorer. Det här steget krävs bara för virtuella Linux-datorer och måste aktive ras när du skapar labbet. För RDP kan du också behöva installera och konfigurera RDP-servern och GUI-paketen på mallen VM innan du publicerar.  Mer information finns i [instruktions guiden om att aktivera fjärr skrivbord för Linux](how-to-enable-remote-desktop-linux.md).

- **Installera och stoppa Windows-uppdateringar**. 

    Om du använder en Windows-avbildning rekommenderar vi att du installerar de senaste Windows-uppdateringarna på Labbets [mall-VM](how-to-create-manage-template.md) innan du publicerar den för att skapa laborationer för virtuella datorer. Det är av säkerhets synpunkt och förhindrar att deltagare störs under Hackathon för att installera uppdateringar, vilket även kan leda till att de virtuella datorerna startas om. Du kan också överväga att inaktivera Windows-uppdateringar för att förhindra framtida avbrott. Se [instruktions guiden för att installera och konfigurera Windows-uppdateringar](how-to-prepare-windows-template.md#install-and-configure-updates).
- **Bestäm hur studenter ska säkerhetskopiera sitt arbete**. 

    Studenterna är varje tilldelad en virtuell dator under Hackathon livs längd. De kan spara sitt arbete direkt på datorn, men vi rekommenderar att eleverna säkerhetskopierar sitt arbete så att de har åtkomst till den när Hackathon är över. De bör till exempel spara till en extern plats, till exempel OneDrive, GitHub och så vidare. Om du vill använda OneDrive kan du välja att konfigurera det automatiskt för studenter på sina virtuella labb datorer. Se [instruktions guiden för att installera och konfigurera OneDrive](how-to-prepare-windows-template.md#install-and-configure-onedrive).
- **Ange VM-kapacitet i enlighet med antalet deltagare**. 

    Kontrol lera att din labbs virtuella dator kapacitet är inställt baserat på antalet deltagare som du förväntar dig på din Hackathon. När du publicerar mallen virtuell dator kan det ta flera timmar att skapa alla datorer i labbet. Därför rekommenderar vi att du gör det bra i förväg i början av Hackathon. Mer information finns i [instruktions guiden om att uppdatera labb kapaciteten](how-to-set-virtual-machine-passwords.md#update-the-lab-capacity).

- **Bestäm om du vill begränsa åtkomsten till labbet**. 

    När du lägger till användare i labbet finns det ett begränsa åtkomst alternativ som är aktiverat som standard. Den här funktionen kräver att du lägger till alla dina e-postmeddelanden i Hackathon-deltagarna i listan innan de kan registrera sig och komma åt labbet med hjälp av registrerings länken. Om du har en Hackathon där du inte vet vem deltagarna kommer före händelsen kan du välja att inaktivera alternativet begränsa åtkomst, vilket gör att vem som helst kan registrera sig i labbet med hjälp av registrerings länken. Mer information finns i [instruktions guiden för att lägga till användare](how-to-configure-student-usage.md).

- **Verifiera inställningar för schema, kvot och automatisk avstängning**. 

    Labb tjänster ger flera kostnads kontroller för att begränsa användningen av virtuella datorer. Men om dessa inställningar är felkonfigurerade kan de orsaka att dina labbs virtuella datorer stängs av på oväntad avstängning. Kontrol lera följande inställningar för att se till att dessa inställningar har kon figurer ATS korrekt för din Hackathon:

    **Schema**: ett [schema](how-to-create-schedules.md) gör att du automatiskt kan styra när dina labbs datorer ska startas och stängas av. Inget schema konfigureras som standard när du skapar ett nytt labb. Du bör dock se till att ditt labb schema ställs in enligt vad som passar din Hackathon.  Exempel: om din Hackathon startar på lördag kl. 8:00 och slutar på söndag till 5:00 PM – kan du skapa ett schema som automatiskt startar datorn vid 7:30 AM på lördag (cirka 30 minuter innan Hackathon börjar) och stänger ner det vid 5:00 EM på söndag. I stället kan du också välja att inte använda ett schema alls.

    **Kvot**: [kvoten](how-to-configure-student-usage.md#set-quotas-for-users) styr antalet timmar som deltagare får åtkomst till en virtuell dator utanför de schemalagda timmarna. Om kvoten uppnås när en deltagare använder den, stängs datorn automatiskt av och deltagaren kommer inte att kunna starta om den om inte kvoten höjs. Som standard är kvoten inställd på 10 timmar när du skapar ett labb. Återigen bör du vara noga med att ange kvoten så att den tillåter tillräckligt med tid för Hackathon, vilket är särskilt viktigt om du inte har skapat ett schema.

    Automatisk **avstängning**: när den här inställningen är aktive rad, gör inställningen för automatisk [avstängning](how-to-enable-shutdown-disconnect.md) att virtuella Windows-datorer stängs av automatiskt efter en viss tids period när studenten har KOPPLAts bort från RDP-sessionen. Som standard är denna inställning inaktiverad.

- **Konfigurera brand Väggs inställningar för att tillåta anslutningar till virtuella labb datorer**. 

    Kontrol lera att inställningarna för din skola eller organisations brand vägg tillåter att du ansluter till virtuella labb datorer med RDP\SSH. Mer information finns i [instruktions guiden om hur du konfigurerar nätverkets brand Väggs inställningar](how-to-configure-firewall-settings.md).

- **Installera RDP\SSH-klienten på deltagarnas surfplattor, Mac-datorer, datorer och så** vidare.

    Hackathon-deltagarna måste ha en RDP-och/eller SSH-klient installerad på sina surfplattor eller bärbara datorer som de ska använda för att ansluta till virtuella labb datorer. Du kan välja mellan olika RDP-eller SSH-klienter, till exempel:

    - Microsofts **anslutning till fjärrskrivbord** app för RDP-anslutningar. Anslutning till fjärrskrivbord-appen stöds på olika typer av plattformar, inklusive Chromebooks och [Mac](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162).
    - [SparaTillFil](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162) för att använda SSH för att ansluta till en virtuell Linux-dator.
- **Verifiera virtuella labb datorer**. 

    När du har publicerat virtuella labb datorer bör du kontrol lera att de är korrekt konfigurerade. Du behöver bara utföra den här verifieringen för en av deltagarens virtuella labb datorer:

    1. Anslut med RDP och/eller SSH.
    2. Öppna varje ytterligare program och verktyg som du har installerat för att anpassa den virtuella bas avbildningen för virtuella datorer.
    3. Gå igenom några grundläggande scenarier som är representativa för de aktiviteter som deltagarna gör för att säkerställa att prestanda för virtuella datorer är lämpliga baserat på den valda beräknings storleken.

## <a name="on-the-day-of-hackathon"></a>Dag i Hackathon
Det här avsnittet beskriver stegen för att slutföra dagen för din Hackathon.

1. **Starta virtuella labb datorer**.

    Din labb dator kan ta upp till 30 minuter att starta beroende på ditt operativ system. Därför är det viktigt att starta datorer innan Hackathon startar så att deltagarna inte behöver vänta. Om du använder ett schema bör du se till att de virtuella datorerna startas automatiskt minst 30 minuter tidigare.
2. **Bjud in studenter för att registrera sig och komma åt den virtuella labb datorn**. 

    Ge dina deltagare följande information så att deltagarna kan komma åt sina labbbaserade virtuella datorer. 

    - Labbets registrerings länk. 
    - Autentiseringsuppgifter som ska användas för att ansluta till datorn. Det här steget gäller bara om ditt labb använder en Windows-baserad avbildning och du har konfigurerat alla virtuella datorer för att använda samma lösen ord.
    - Instruktioner om hur deltagarna SSH-och/eller RDP till sina datorer.

        Mer information finns i [instruktions guiden om att skicka inbjudningar till användare](how-to-configure-student-usage.md#send-invitations-to-users) och [ansluta till virtuella Linux-datorer](how-to-use-remote-desktop-linux-student.md). 

## <a name="next-steps"></a>Nästa steg
Börja med att skapa ett labb konto i labbet genom att följa anvisningarna i artikeln: [Självstudier: Konfigurera ett labb konto med Azure Lab Services](tutorial-setup-lab-account.md).
