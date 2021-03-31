---
title: Utvärdera lokala servrar med hjälp av en importerad CSV-fil med Azure Migrate Server bedömning
description: Beskriver hur du identifierar lokala servrar för migrering till Azure med hjälp av en importerad CSV-fil i Azure Migrate Server bedömning
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.openlocfilehash: dfa7ee941e2c373b02fe5fb2f2a648a60a677670
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96753118"
---
# <a name="tutorial-assess-servers-using-an-imported-csv-file"></a>Självstudie: utvärdera servrar med en importerad CSV-fil

Som en del av migreringen till Azure identifierar du din lokala inventering och dina arbets belastningar. 

I den här självstudien lär du dig att utvärdera lokala datorer med Azure Migrate: Server utvärderings verktyget med hjälp av en importerad CSV-fil (kommaavgränsade värden). 

Om du använder en CSV-fil behöver du inte konfigurera Azure Migrate-enheten för att identifiera och utvärdera servrar. Du kan kontrol lera de data som du delar i filen och mycket data är valfria. Den här metoden är användbar om:

- Du vill skapa en snabb första utvärdering innan du distribuerar enheten.
- Det inte går att distribuera Azure Migrate-enheten i din organisation.
- Du inte kan dela autentiseringsuppgifter som ger åtkomst till lokala servrar.
- Säkerhetsbegränsningar förhindrar att du samlar in och skickar data som samlas in av enheten till Azure.

> [!NOTE]
> Du kan inte migrera servrar som importer ATS med en CSV-fil.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Konfigurera ett Azure-konto
> * Konfigurera ett Azure Migrate-projekt
> * Förbered en CSV-fil
> * Importera filen
> * Utvärdera servrar

> [!NOTE]
> Självstudier visar den snabbaste sökvägen för att testa ett scenario och använda standard alternativ där det är möjligt. 

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

- Du kan lägga till upp till 20 000-servrar i en enda CSV-fil och i ett Azure Migrate-projekt. 
- Namnen på de operativ system som anges i CSV-filen måste innehålla och matcha [namn som stöds](#supported-operating-system-names).


## <a name="prepare-an-azure-user-account"></a>Förbereda ett Azure-användarkonto

Om du vill skapa ett Azure Migrate-projekt behöver du ett konto med:
- Deltagar-eller ägar behörigheter för en Azure-prenumeration.
- Behörighet att registrera Azure Active Directory appar.

Om du nyligen skapade ett kostnadsfritt Azure-konto är du ägare av prenumerationen. Om du inte är prenumerations ägare kan du arbeta med ägaren för att tilldela behörigheterna på följande sätt:

1. I Azure Portal söker du efter "prenumerationer" och under **tjänster** väljer du **prenumerationer**.

    ![Sök i rutan för att söka efter Azure-prenumerationen](./media/tutorial-discover-import/search-subscription.png)

2. På sidan **prenumerationer** väljer du den prenumeration där du vill skapa ett Azure Migrate-projekt. 
3. I prenumerationen väljer du **åtkomst kontroll (IAM)**  >  **kontrol lera åtkomst**.
4. I **kontrol lera åtkomst** söker du efter det relevanta användar kontot.
5. I **Lägg till en roll tilldelning** klickar du på **Lägg till**.

    ![Sök efter ett användar konto för att kontrol lera åtkomst och tilldela en roll](./media/tutorial-discover-import/azure-account-access.png)

6. I **Lägg till roll tilldelning** väljer du rollen deltagare eller ägare och väljer kontot (azmigrateuser i vårt exempel). Klicka sedan på **Spara**.

    ![Öppnar sidan Lägg till roll tilldelning för att tilldela kontot en roll](./media/tutorial-discover-import/assign-role.png)

7. I portalen söker du efter användare och under **tjänster** väljer **du användare**.
8. I **användar inställningar** kontrollerar du att Azure AD-användare kan registrera program (anges till **Ja** som standard).

    ![Verifiera i användar inställningar som användare kan registrera Active Directory appar](./media/tutorial-discover-import/register-apps.png)



## <a name="set-up-a-project"></a>Konfigurera ett projekt

Skapa ett nytt Azure Migrate projekt om du inte har något.

1. I Azure-portalen > **Alla tjänster** söker du efter **Azure Migrate**.
2. Under **Tjänster** väljer du **Azure Migrate**.
3. I **Översikt** väljer du **skapa projekt**.
5. I **skapa projekt** väljer du din Azure-prenumeration och resurs grupp. Skapa en resurs grupp om du inte har någon.
6. I **projekt information** anger du projekt namnet och geografin som du vill skapa projektet i. Granska stödda geografiska områden för [offentliga](migrate-support-matrix.md#supported-geographies-public-cloud) och [offentliga moln](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Rutor för projekt namn och region](./media/tutorial-discover-import/new-project.png)

7. Välj **Skapa**.
8. Vänta några minuter tills Azure Migrate-projektet har distribuerats.

Verktyget **Azure Migrate: Server bedömning** läggs till som standard i det nya projektet.

![Sida som visar verktyget för Server bedömning som har lagts till som standard](./media/tutorial-discover-import/added-tool.png)

## <a name="prepare-the-csv"></a>Förbereda CSV-filen

Ladda ned CSV-mallen och lägg till serverinformation i den.

### <a name="download-the-template"></a>Ladda ned mallen

1. I **Migreringsmål** > **Servrar** > **Azure Migrate: Serverutvärdering** väljer du **Identifiera**.
2. I **Identifiera datorer** väljer du **Importera med CSV**.
3. Välj **Ladda ned** för att ladda ned CSV-mallen. Du kan också [ladda ned den direkt](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![Ladda ned CSV-mall](./media/tutorial-discover-import/download-template.png)

### <a name="add-server-information"></a>Lägg till serverinformation

Samla in serverdata och lägg till den i CSV-filen.

- Samla in data genom att exportera den från verktyg du använder till lokal serverhantering, till exempel VMware vSphere eller din databas för konfigurationshantering (CMDB).
- Om du vill granska dataexemplen laddar du ned vår [exempelfil](https://go.microsoft.com/fwlink/?linkid=2108405).

Följande tabell sammanfattar de filfält som ska fyllas i:

**Fältnamn** | **Obligatorisk** | **Information**
--- | --- | ---
**Servernamn** | Yes | Vi rekommenderar att det fullständiga domännamnet (FQDN) anges.
**IP-adress** | No | Serveradress.
**Kärnor** | Yes | Antal processorkärnor som har allokerats till servern.
**Minne** | Yes | Totalt RAM-minne, i MB, som har allokerats till servern.
**Operativsystemets namn** | Yes | Serverns operativsystem. <br/> Operativsystemnamn som matchar eller innehåller namnen i [den här](#supported-operating-system-names) listan identifieras av utvärderingen.
**Operativsystemversion** | No | Serverns operativsystemversion.
**Operativsystemarkitektur** | No | Serverns operativsystemarkitektur <br/> Giltiga värden är: x64, x86, amd64, 32-bitars eller 64-bitars
**Antal diskar** | No | Behövs inte om separat diskinformation anges.
**Disk 1, storlek**  | No | Maximal diskstorlek i GB.<br/>Du kan lägga till information för fler diskar genom att [lägga till kolumner](#add-multiple-disks) i mallen. Du kan lägga till upp till åtta diskar.
**Disk 1, läsåtgärder** | No | Diskens läsåtgärder per sekund.
**Disk 1, skrivåtgärder** | No | Diskens skrivåtgärder per sekund.
**Disk 1, läsningsdataflöde** | No | Data som läses från disken per sekund, i MB per sekund.
**Disk 1, skrivningsdataflöde** | No | Data som skrivs till disk per sekund, i MB per sekund.
**Processoranvändning i procent** | No | Procentandelen CPU som används.
**Minnesanvändning i procent** | No | Procentandel RAM som används.
**Totalt antal diskar, läsåtgärder** | No | Disk läsnings åtgärder per sekund.
**Totalt antal diskar, skrivåtgärder** | No | Disk-Skriv åtgärder per sekund.
**Totalt antal diskar, läsningsdataflöde** | No | Data läses från disken, i MB per sekund.
**Totalt antal diskar, skrivningsdataflöde** | No | Data som skrivs till disk, i MB per sekund.
**Inkommande nätverksdataflöde** | No | Data som tas emot av servern, i MB per sekund.
**Utgående nätverksdataflöde** | No | Data som skickas av servern, i MB per sekund.
**Typ av inbyggd programvara** | No | Serverns inbyggda programvara. Värdena kan vara ”BIOS” eller ”UEFI”.
**MAC-adress**| No | Serverns MAC-adress.


### <a name="add-operating-systems"></a>Lägga till operativsystem

Utvärderingen identifierar specifika operativsystemnamn. Det namn som du anger måste exakt matcha en av strängarna i [listan med namn som stöds](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Lägga till flera diskar

Mallen innehåller standardfält för den första disken. Du kan lägga till liknande kolumner för upp till åtta diskar.

Om du till exempel vill ange alla fält för en andra disk lägger du till följande kolumner:

- Disk 2, storlek
- Disk 2, läsåtgärder
- Disk 2, skrivåtgärder
- Disk 2, läsningsdataflöde
- Disk 2, skrivningsdataflöde


## <a name="import-the-server-information"></a>Importera serverinformationen

När du har lagt till information i CSV-mallen importerar du CSV-filen till Server utvärderingen.

1. I Azure Migrate, i **Identifiera datorer**, går du till den färdiga mallen.
2. Välj **Importera**.
3. Importstatusen visas.
    - Om det visas några varningar i statusen, kan du antingen åtgärda dem eller fortsätta utan att hantera dem.
    - Förbättra utvärderingsprecisionen genom att förbättra serverinformationen enligt förslagen i varningarna.
    - Om du vill visa och åtgärda varningar väljer du **Ladda ned varningsinformation .CSV**. Åtgärden hämtar CSV-filen med varningarna. Granska varningarna och åtgärda problemen vid behov.
    - Om fel visas i statusen så att importens status är **Misslyckades**, måste du åtgärda felen innan du kan fortsätta med importen:
        1. Ladda ned CSV-filen som nu innehåller felinformation.
        1. Granska och åtgärda felen vid behov. 
        1. Ladda upp den ändrade filen igen.
4. När importstatusen är **Slutförd** har serverinformationen importerats. Uppdatera om import processen inte verkar vara fullständig.

## <a name="update-server-information"></a>Uppdatera serverinformation

Du kan uppdatera informationen för en server genom att importera data för servern på nytt med samma **Servernamn**. Det går inte att ändra fältet **Servernamn**. Det finns för närvarande inget stöd för att ta bort servrar.

## <a name="verify-servers-in-the-portal"></a>Verifiera servrarna i portalen

Så här kontrollerar du att servrarna visas i Azure-portalen efter identifieringen:

1. Öppna instrumentpanelen för Azure Migrate.
2. På sidan **Azure Migrate – Servrar** > **Azure Migrate: Serverutvärdering** väljer du den ikon som visar antalet **Identifierade servrar**.
3. Välj fliken **Importbaserat**.



## <a name="supported-operating-system-names"></a>Namn på operativsystem som stöds

Operativ system namn som anges i CSV-filen måste innehålla och matcha. Om de inte gör det kan du inte utvärdera dem. 

**A – H** | **I-R** | **S-T** | **U-Z**
--- | --- | --- | ---
Apple Mac OS X 10<br/>Asianux 3<br/>Asianux 4<br/>Asianux 5<br/>CentOS<br/>CentOS 4/5<br/>CoreOS Linux<br/>Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8<br/>FreeBSD | IBM OS/2<br/>MS-DOS<br/>Novell NetWare 5<br/>Novell NetWare 6<br/>Oracle Linux<br/>Oracle Linux 4/5<br/>Oracle Solaris 10<br/>Oracle Solaris 11<br/>Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora | SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UnixWare 7<br/> Serenity Systems eComStation 1<br/>Serenity system eComStation <br/>Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9<br/><br/>SUSE Linux Enterprise 10<br/>SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE | Ubuntu Linux<br/>VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6<br/>Windows 10<br/>Windows 2000<br/>Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003<br/>Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Windows Server Threshold<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional

## <a name="next-steps"></a>Nästa steg

I den här kursen får du:

> [!div class="checklist"]
> * Skapat ett Azure Migrate-projekt 
> * Identifierade servrar med hjälp av en importerad CSV-fil. Kör nu en utvärdering för [migrering av virtuella VMware-datorer till virtuella Azure-datorer](./tutorial-assess-vmware-azure-vm.md).