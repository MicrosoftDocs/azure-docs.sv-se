---
title: Azure Lab Services-administratörs guide | Microsoft Docs
description: Den här guiden hjälper administratörer som skapar och hanterar labb konton med hjälp av Azure Lab Services.
ms.topic: article
ms.date: 10/20/2020
ms.openlocfilehash: 3ad3ee38a6c08a6af85822d76012cc6dfc34ff4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462478"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services-administratörs guide
IT-administratörer som hanterar ett universitets moln resurser är vanligt vis ansvariga för att ställa in labb kontot för sin skola. När du har konfigurerat ett labb konto skapar administratörer eller lärare de labb som finns i kontot. Den här artikeln innehåller en översikt över de Azure-resurser som är inblandade och vägledningen för att skapa dem.

![Diagram över en övergripande vy över Azure-resurser i ett labb konto.](./media/administrator-guide/high-level-view.png)

- Labb finns i en Azure-prenumeration som ägs av Azure Lab Services.
- Labb konton, ett delat avbildnings galleri och avbildnings versioner finns i din prenumeration.
- Du kan ha ditt labb konto och det delade avbildnings galleriet i samma resurs grupp. I det här diagrammet finns de i olika resurs grupper.

Mer information om arkitekturen finns i grunderna i [labb arkitektur](./classroom-labs-fundamentals.md).

## <a name="subscription"></a>Prenumeration
Ditt universitet kan ha en eller flera Azure-prenumerationer. Du använder prenumerationer för att hantera fakturering och säkerhet för alla Azure-resurser och-tjänster som används i den, inklusive Lab-konton.

Relationen mellan ett labb konto och dess prenumeration är viktig eftersom:

- Faktureringen rapporteras via prenumerationen som innehåller labb kontot.
- Du kan bevilja användare i prenumerationens Azure Active Directory-klient (Azure AD) åtkomst till Azure Lab Services. Du kan lägga till en användare som labb konto ägare eller deltagare eller som labb skapare eller labb ägare.

Labb och virtuella datorer (VM) hanteras och hanteras i en prenumeration som ägs av Azure Lab Services.

## <a name="resource-group"></a>Resursgrupp
En prenumeration innehåller en eller flera resurs grupper. Resurs grupper används för att skapa logiska grupperingar av Azure-resurser som används tillsammans inom samma lösning.  

När du skapar ett labb konto måste du konfigurera resurs gruppen som innehåller labb kontot. 

Du måste också ha en resurs grupp när du skapar ett [delat avbildnings Galleri](#shared-image-gallery). Du kan placera ditt labb konto och ditt delade avbildnings galleri i samma resurs grupp eller i två separata resurs grupper. Det kan vara bra att ta den här andra metoden om du planerar att dela avbildnings galleriet över olika lösningar.

När du skapar ett labb konto kan du automatiskt skapa och koppla ett delat avbildnings galleri på samma gång.  Det här alternativet resulterar i labb kontot och det delade avbildnings galleriet skapas i separata resurs grupper. Du ser det här beteendet när du följer stegen som beskrivs i den [Konfigurera delade avbildnings galleriet](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation) när du skapar själv studie kursen för labb konto. Bilden i början av den här artikeln använder den här konfigurationen. 

Vi rekommenderar att du investerar tiden fram och planerar resurs gruppernas struktur, eftersom det *inte* går att ändra ett labb konto eller en resurs grupp för delade avbildnings Galleri när den har skapats. Om du behöver ändra resurs gruppen för de här resurserna måste du ta bort och återskapa ditt labb konto eller det delade avbildnings galleriet.

## <a name="lab-account"></a>Labb konto

Ett labb konto fungerar som en behållare för en eller flera labb. När du kommer igång med Azure Lab Services är det vanligt att ha ett enda labb konto. När din labb användning skalas, kan du välja att skapa fler labb konton senare.

I följande lista beskrivs scenarier där mer än ett labb konto kan vara fördelaktigt:

- **Hantera olika princip krav i labbet**

    När du konfigurerar ett labb konto ställer du in principer som gäller för *alla* labb under labb kontot, till exempel:
    - Det virtuella Azure-nätverket med delade resurser som labbet kan komma åt. Du kan till exempel ha en uppsättning labb som behöver åtkomst till en delad data uppsättning i ett virtuellt nätverk.
    - Avbildningar av virtuella datorer som labbet kan använda för att skapa virtuella datorer. Du kan till exempel ha en uppsättning labb som behöver åtkomst till [data science VM för Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) Azure Marketplace-avbildningen.

    Om var och en av dina labb har unika princip krav kan det vara bra att skapa separata labb konton för att hantera varje labb separat.

- **Tilldela en separat budget till varje labb konto**
  
    I stället för att rapportera alla labb kostnader via ett enda labb konto kan du behöva en tydligare fördelad budget. Du kan till exempel skapa separata labb konton för ditt universitets matematik avdelning, dator vetenskaps avdelning och så vidare, för att distribuera budgeten mellan olika avdelningar.  Du kan sedan Visa kostnaden för varje enskilt labb konto genom att använda [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md).

- **Isolera pilot labb från aktiva eller produktions labb**
  
    Du kan ha fall där du vill pilot policy ändringar för ett labb konto utan att potentiellt påverka dina aktiva eller produktions labb. I den här typen av scenario kan du skapa ett separat labb konto för pilotering i syfte att isolera ändringar. 

## <a name="lab"></a>Labb

Ett labb innehåller virtuella datorer som är tilldelade till en enda student.  I allmänhet kan du förväntar dig att:

- Ha ett labb för varje klass.
- Skapa en ny uppsättning labb för varje termin, kvartal eller annat akademiskt system som du använder. För klasser som behöver använda samma avbildning bör du använda ett [delat avbildnings Galleri](#shared-image-gallery). På så sätt kan du återanvända bilder i labb och akademiska perioder.

Tänk på följande när du bestämmer hur du ska strukturera labben:

- **Alla virtuella datorer i ett labb distribueras med samma avbildning som publiceras**

    Det innebär att om du har en klass som kräver att olika labb avbildningar publiceras samtidigt, måste du skapa ett separat labb för varje bild.
  
- **Användnings kvoten anges på labb nivån och gäller för alla användare i labbet**

    Om du vill ange olika kvoter för användare måste du skapa separata labb. Det går dock att lägga till fler timmar till vissa användare efter att du har angett kvoten.
  
- **Schemat för start eller avstängning anges på labb nivån och gäller för alla virtuella datorer i labbet**

    Precis som med kvot inställningen måste du skapa ett separat labb för varje schema om du behöver ange olika scheman för användarna.

Som standard har varje labb ett eget virtuellt nätverk.  Om du har aktiverat virtuell nätverks-peering har varje labb sitt eget undernät som peer-kopplas med det angivna virtuella nätverket.

## <a name="shared-image-gallery"></a>Galleri för delad avbildning

Ett delat avbildnings galleri är kopplat till ett labb konto och fungerar som en central lagrings plats för att lagra avbildningar. En bild sparas i galleriet när en lärare väljer att exportera den från en labbs mall för virtuella datorer. Varje gången en lärare gör ändringar i mallen för den virtuella datorn och exporterar den, sparas nya versioner av avbildningen och de tidigare versionerna bevaras.

Lärare kan publicera en avbildnings version från galleriet för delad avbildning när de skapar ett nytt labb. Även om galleriet lagrar flera versioner av en avbildning, kan lärare bara välja den senaste versionen under labb skapandet.

Tjänsten Shared Image Gallery är en valfri resurs som du kanske inte behöver omedelbart om du startar med bara några få labb. Delade avbildnings gallerier ger dock många fördelar som är användbara när du skalar upp till ytterligare labb:

- **Du kan spara och hantera versioner av en mall för VM-avbildningar**

    Det är praktiskt att skapa en anpassad avbildning eller göra ändringar (program vara, konfiguration och så vidare) till en avbildning från det offentliga Azure Marketplace-galleriet.  Det är till exempel vanligt för lärare att kräva att olika program varor eller verktyg installeras. I stället för att kräva att studenter installerar dessa krav manuellt på egen hand, kan olika versioner av mallen VM-avbildning exporteras till ett delat avbildnings Galleri. Du kan sedan använda dessa avbildnings versioner när du skapar nya labb.

- **Du kan dela och återanvänd VM-avbildningar för mallar i labbet**

    Du kan spara och återanvända en bild så att du inte behöver konfigurera den från grunden varje gången du skapar ett nytt labb. Om till exempel flera klasser behöver använda samma avbildning, kan du skapa den en gång och exportera den till det delade avbildnings galleriet så att den kan delas mellan labb.

- **Image-tillgänglighet är säkerställd genom automatisk replikering**

    När du sparar en bild från ett labb till det delade bild galleriet replikeras den automatiskt till andra [regioner inom samma geografi](https://azure.microsoft.com/global-infrastructure/regions/). Om det uppstår ett avbrott för en region påverkas inte publiceringen av bilden till ditt labb, eftersom en avbildnings replik från en annan region kan användas.  Det kan också vara bättre att publicera virtuella datorer från flera repliker.

Om du vill gruppera delade avbildningar logiskt, kan du göra något av följande:

- Skapa flera Galleri för delade avbildningar. Varje labb konto kan bara ansluta till ett delat avbildnings Galleri, så det här alternativet kräver också att du skapar flera labb konton.
- Använd ett enda delat avbildnings galleri som delas av flera labb konton. I det här fallet kan varje labb konto bara aktivera avbildningar som är tillämpliga för labben i det kontot.

## <a name="naming"></a>Namngivning

När du kommer igång med Azure Lab Services rekommenderar vi att du skapar namn konventioner för resurs grupper, labb konton, labb och delade avbildnings Galleri. Även om namngivnings konventionerna som du fastställer är unika för organisationens behov, innehåller följande tabell allmänna rikt linjer:

| Resurstyp | Roll | Föreslaget mönster | Exempel |
| ------------- | ---- | ----------------- | -------- | 
| Resursgrupp | Innehåller ett eller flera labb konton och ett eller flera gallerier för delade avbildningar | \<organization short name\>-\<environment\>– RG<ul><li>**Organisationens kort namn** identifierar namnet på den organisation som resurs gruppen stöder.</li><li>**Miljön** identifierar miljön för resursen, till exempel *pilot* eller *produktion*.</li><li>**RG** står för resurs *gruppen* resurs typ.</li></ul> | contosouniversitylabs – rg<br/>contosouniversitylabs – pilot – rg<br/>contosouniversitylabs-Prod-rg |
| Labb konto | Innehåller en eller flera labb | \<organization short name\>-\<environment\>-La<ul><li>**Organisationens kort namn** identifierar namnet på den organisation som resurs gruppen stöder.</li><li>**Miljön** identifierar miljön för resursen, till exempel *pilot* eller *produktion*.</li><li>**La** står för resurs typ *labb konto*.</li></ul> | contosouniversitylabs-La<br/>mathdeptlabs-La<br/>sciencedeptlabs-pilot-La<br/>sciencedeptlabs-Prod-La |
| Labb | Innehåller en eller flera virtuella datorer |\<class name\>-\<timeframe\>-\<educator identifier\><ul><li>**Klass namn** identifierar namnet på klassen som labbet stöder.</li><li>**Tidsram** identifierar den tidsram i vilken klassen erbjuds.</li>**Lärare-identifieraren** identifierar den lärare som äger labbet.</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-spring2019-johndoe |
| Galleri för delad avbildning | Innehåller en eller flera versioner av virtuella dator avbildningar | \<organization short name\>formatmall | contosouniversitylabsgallery |

Mer information om hur du namnger andra Azure-resurser finns i [namngivnings konventioner för Azure-resurser](/azure/architecture/best-practices/naming-conventions).

## <a name="regionslocations"></a>Regions\locations

När du konfigurerar Azure Lab Services resurser måste du ange en region eller plats för data centret som ska vara värd för resurserna. I nästa avsnitt beskrivs hur en region eller plats kan påverka varje resurs som är involverad i att ställa in ett labb.

### <a name="resource-group"></a>Resursgrupp

Regionen anger data centret där information om en resurs grupp lagras. Azure-resurser som ingår i resurs gruppen kan finnas i en annan region än den överordnade.

### <a name="lab-account"></a>Labb konto

Ett labb kontos plats anger den region som en resurs finns i.  

### <a name="lab"></a>Labb

Den plats som ett labb finns i varierar beroende på följande faktorer:

  - **Labb kontot är peer-kopplat med ett virtuellt nätverk**
  
    Du kan [peer-koppla ett labb konto till ett virtuellt nätverk](./how-to-connect-peer-virtual-network.md) när de befinner sig i samma region.  När ett labb konto är peer-kopplat med ett virtuellt nätverk skapas labb automatiskt i samma region som både labb kontot och det virtuella nätverket.

    > [!NOTE]
    > När ett labb konto är peer-kopplat med ett virtuellt nätverk är inställningen **Tillåt att labb skapare för att välja labb plats** inaktive rad. Mer information finns i [Tillåt labb skapare att välja plats för labbet](./allow-lab-creator-pick-lab-location.md).
    
  - **Inget virtuellt nätverk är peer-kopplat *och* labb skapare får inte välja labb platsen**
  
    När *inget* virtuellt nätverk är peer-kopplat med labb kontot och [labb skapare *inte tillåts* att välja labb platsen](./allow-lab-creator-pick-lab-location.md), skapas labb automatiskt i en region som har tillgänglig VM-kapacitet.  Mer specifikt Azure Lab Services letar efter tillgänglighet i [regioner som ligger inom samma geografi som labb kontot](https://azure.microsoft.com/global-infrastructure/regions).

  - **Inget virtuellt nätverk är peer-kopplat *och* labb skapare kan välja labb plats**
       
    När *inget* virtuellt nätverk är peer-kopplat och [labb skapare *har tillåtelse* att välja labb platsen](./allow-lab-creator-pick-lab-location.md), är de platser som kan väljas av Labbets skapare beroende av tillgänglig kapacitet.

> [!NOTE]
> För att säkerställa att en region har tillräcklig VM-kapacitet är det viktigt att du först begär kapacitet genom labb kontot när du skapar labbet.

En allmän regel är att ange en resurs region till en som är närmast dess användare. För labb, innebär det att skapa labbet som är närmast dina studenter. För onlinekurser vars studenter finns över hela världen kan du använda ditt bästa omdöme för att skapa ett labb som är centralt befinner dig. Eller så kan du dela upp en klass i flera labb enligt dina studenters regioner.

## <a name="vm-sizing"></a>Storlek på virtuell dator

När administratörer eller labb skapare skapar ett labb kan de välja mellan olika VM-storlekar, beroende på deras klass rums behov. Kom ihåg att beräknings storleken är tillgänglig beroende på den region som ditt labb konto finns i.

| Storlek | Specifikationer | Serie | Föreslagen användning |
| ---- | ----- | ------ | ------------- |
| Liten| <ul><li>2 &nbsp; kärnor</li><li>3,5 GB RAM-minne</li> | [Standard_A2_v2](../virtual-machines/av2-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Lämpar sig bäst för kommando rad, öppna webbläsare, webb servrar med låg trafik, små till medel stora databaser. |
| Medel | <ul><li>4 &nbsp; kärnor</li><li>7 &nbsp; GB &nbsp; ram</li> | [Standard_A4_v2](../virtual-machines/av2-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Passar bäst för Relations databaser, minnes intern cachelagring och analys. |
| Medium (kapslad virtualisering) | <ul><li>4 &nbsp; kärnor</li><li>16 &nbsp; GB &nbsp; ram</li></ul> | [Standard_D4s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#dsv3-series) | Passar bäst för Relations databaser, minnes intern cachelagring och analys.
| Stor | <ul><li>8 &nbsp; kärnor</li><li>16 &nbsp; GB &nbsp; ram</li></ul>  | [Standard_A8_v2](../virtual-machines/av2-series.md) | Lämpar sig bäst för program som behöver snabbare processorer, bättre prestanda för lokala diskar, stora databaser, stora minnes-cacheminnen.  Den här storleken stöder även kapslad virtualisering. |
| Stor (kapslad virtualisering) | <ul><li>8 &nbsp; kärnor</li><li>32 &nbsp; GB &nbsp; ram</li></ul>  | [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#dsv3-series) | Lämpar sig bäst för program som behöver snabbare processorer, bättre prestanda för lokala diskar, stora databaser, stora minnes-cacheminnen. |
| Liten GPU (visualisering) | <ul><li>6 &nbsp; kärnor</li><li>56 &nbsp; GB &nbsp; ram</li>  | [Standard_NV6](../virtual-machines/nv-series.md) | Passar bäst för fjärrvisualisering, strömning, spel och kodning med hjälp av ramverk som OpenGL och DirectX. |
| Liten GPU (Compute) | <ul><li>6 &nbsp; kärnor</li><li>56 &nbsp; GB &nbsp; ram</li></ul>  | [Standard_NC6](../virtual-machines/nc-series.md) |Passar bäst för dator intensiva program som AI och djup inlärning. |
| Medelhög GPU (visualisering) | <ul><li>12 &nbsp; kärnor</li><li>112 &nbsp; GB &nbsp; ram</li></ul>  | [Standard_NV12](../virtual-machines/nv-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Passar bäst för fjärrvisualisering, strömning, spel och kodning med hjälp av ramverk som OpenGL och DirectX. |

## <a name="manage-identity"></a>Hantera identitet

Genom att använda [rollbaserad åtkomst kontroll i Azure (RBAC)](../role-based-access-control/overview.md) för åtkomst till labb konton och labb kan du tilldela följande roller:

- Labb konto **ägare**

    En administratör som skapar ett labb konto tilldelas automatiskt rollen som labb konto ägare. Ägar rollen kan:
     - Ändra labb konto inställningarna.
     - Ge andra administratörer åtkomst till labb kontot som ägare eller deltagare.
     - Ge lärare till gång till labb som skapare, ägare eller deltagare.
     - Skapa och hantera alla labb i labb kontot.

- Labb konto **deltagare**

    En administratör som har tilldelats deltagar rollen kan:
    - Ändra labb konto inställningarna.
    - Skapa och hantera alla labb i labb kontot.

    Deltagaren kan dock *inte* ge andra användare åtkomst till antingen labb konton eller labb.

- **Labb skapare**

    För att skapa labb i ett labb konto måste en lärare vara medlem i rollen labb skapare.  En lärare som skapar ett labb läggs automatiskt till som en labb ägare. Mer information finns i [lägga till en användare till labb skaparen-rollen](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role). 

- Labb **ägare** eller **deltagare**
  
    En lärare i en labb ägare eller deltagar roll kan visa och ändra inställningarna för labbet. Personen måste också vara medlem i rollen labb konto läsare.

    En viktig skillnad mellan labb ägare och deltagar roller är att endast en ägare kan ge andra användare åtkomst till att hantera ett labb. En deltagare *kan inte* ge andra användare åtkomst till att hantera ett labb.

- **Galleri för delad avbildning**

    När du ansluter ett delat avbildnings galleri till ett labb konto beviljas labb konto ägare och deltagare och labb skapare, labb ägare och labb deltagare automatiskt åtkomst till att visa och spara bilder i galleriet.

När du tilldelar roller, hjälper det dig att följa dessa tips:

   - Normalt bör endast administratörer vara medlemmar i ett labb konto ägare eller deltagar roll. Labb kontot kan ha fler än en ägare eller deltagare.
   - För att ge lärare möjlighet att skapa nya labb och hantera labb som de skapar, behöver du bara tilldela dem rollen som labb skapare.
   - För att ge lärare möjlighet att hantera vissa labb, men *inte* möjligheten att skapa nya labb, tilldelar du antingen rollen ägare eller deltagare för varje labb som de ska hantera. Du kanske till exempel vill ge en lärare och en undervisnings assistent möjlighet att samar beta med ett labb. Mer information finns i [Lägg till ägare i ett labb](./how-to-add-user-lab-owner.md).

## <a name="pricing"></a>Priser

### <a name="azure-lab-services"></a>Azure Lab Services

Mer information om priser finns i [Azure Lab Services priser](https://azure.microsoft.com/pricing/details/lab-services/).


### <a name="shared-image-gallery"></a>Delat bildgalleri

Du måste också beakta priserna för tjänsten Shared Image Gallery om du planerar att använda delade avbildnings gallerier för att lagra och hantera avbildnings versioner. 

Det kostar inget att skapa ett delat avbildnings galleri och koppla det till ditt labb konto. Inga kostnader uppkommer förrän du sparar en avbildnings version i galleriet. Priset för att använda ett delat avbildnings galleri är vanligt vis ganska försumbart, men det är viktigt att förstå hur det beräknas, eftersom det inte ingår i prissättningen för Azure Lab Services.  

#### <a name="storage-charges"></a>Lagrings kostnader

Om du vill lagra avbildnings versioner använder ett delat avbildnings Galleri standard diskar som hanteras av hård disk enheter (HDD). Storleken på den HDD-hanterade disk som används beror på storleken på avbildnings versionen som lagras. Mer information om priser finns i [priser för Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).

#### <a name="replication-and-network-egress-charges"></a>Utgående kostnader för replikering och nätverk

När du sparar en avbildnings version med hjälp av en virtuell labb mal len virtuell dator lagrar Azure Lab Services först den i en käll region och replikerar sedan automatiskt käll avbildnings versionen till en eller flera mål regioner. 

Det är viktigt att Observera att Azure Lab Services automatiskt replikerar käll avbildnings versionen till alla [mål regioner inom det geografiska område](https://azure.microsoft.com/global-infrastructure/regions/) där labbet finns. Om ditt labb t. ex. finns i geografien USA replikeras en avbildnings version till var och en av de åtta regioner som finns i USA.

Nätverks utgående belastning sker när en avbildnings version replikeras från käll regionen till ytterligare mål regioner. Det debiterade beloppet baseras på avbildnings versionens storlek när avbildningens data först överförs utgående från käll regionen.  Pris information finns i [pris information för bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/).

Utgående kostnader kan åsidosättas för [utbildnings lösningar](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) kunder. Kontakta din konto ansvarige om du vill veta mer. 

Mer information finns i "vilka data överförings program finns för akademiska kunder och hur kvalificerar jag?" i avsnittet Vanliga frågor och svar på sidan [program för utbildnings institutioner](https://azure.microsoft.com/pricing/details/bandwidth/) .

#### <a name="pricing-example"></a>Prissättnings exempel

Nu ska vi titta på ett exempel på kostnaden för att spara en mall för en VM-avbildning i ett delat avbildnings Galleri. Antag följande scenarier:

- Du har en anpassad VM-avbildning.
- Du håller på att spara två versioner av avbildningen.
- Ditt labb är i USA, som har totalt åtta regioner.
- Varje avbildnings version är 32 GB i storlek. Därför är hård disk-Managed disk pris $1,54 per månad.

Den totala kostnaden per månad beräknas som:

* *Antal avbildningar antal &times; versioner &times; antal repliker som &times; hanteras disk pris = total kostnad per månad*

I det här exemplet är kostnaden:

* 1 anpassad avbildning (32 GB) &times; 2 versioner &times; 8 amerikanska regioner &times; $1,54 = $24,64 per månad

> [!NOTE]
> Föregående beräkning är endast till exempel syfte. Den omfattar lagrings kostnader som är kopplade till användning av delade avbildnings gallerier och omfattar *inte* utgående kostnader. För faktiska priser för lagring, se [Managed disks prissättning](https://azure.microsoft.com/en-us/pricing/details/managed-disks/).

#### <a name="cost-management"></a>Kostnadshantering

Det är viktigt för labb konto administratörer att hantera kostnader genom att rutinmässigt ta bort onödiga bild versioner från galleriet. 

Ta inte bort replikeringen till vissa regioner som ett sätt att minska kostnaderna, även om det här alternativet finns i det delade avbildnings galleriet. Ändringar i replikeringen kan ha negativa effekter på möjligheten att Azure Lab Services publicera virtuella datorer från avbildningar som sparats i ett delat avbildnings Galleri.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du konfigurerar och hanterar labb finns i:

- [Installations guide för labb konto](account-setup-guide.md)  
- [Installations guide för labb](setup-guide.md)  
- [Kostnadshantering för labb](cost-management-guide.md)  
- [Använda Azure Lab Services i Teams](lab-services-within-teams-overview.md)
