---
title: 'Azure AD Connect: förstå deklarativ etablering | Microsoft Docs'
description: Förklarar konfigurations modellen för deklarativ etablering i Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cfbb870d-be7d-47b3-ba01-9e78121f0067
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59dc94e37dfa1ef8b0b079bf5d78d0504e0cb8c7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91313628"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Azure AD Connect synkronisering: förstå deklarativ etablering
I det här avsnittet beskrivs konfigurations modellen i Azure AD Connect. Modellen kallas deklarativ etablering och gör att du enkelt kan göra en konfigurations ändring. Många saker som beskrivs i det här avsnittet är avancerade och krävs inte för de flesta kund scenarier.

## <a name="overview"></a>Översikt
Deklarativ etablering bearbetar objekt som kommer från en källa ansluten katalog och avgör hur objektet och attributen ska omvandlas från en källa till ett mål. Ett objekt bearbetas i en synkroniseringsanslutning och pipelinen är samma för inkommande och utgående regler. En regel för inkommande trafik från ett anslutnings utrymme till metaversum och en utgående regel är från metaversum till ett anslutnings utrymme.

![Diagram som visar ett exempel på en Sync-pipeline.](./media/concept-azure-ad-connect-sync-declarative-provisioning/sync1.png)  

Pipelinen har flera olika moduler. Var och en är ansvarig för ett koncept i Object synchronization.

![Diagram som visar modulerna i pipelinen.](./media/concept-azure-ad-connect-sync-declarative-provisioning/pipeline.png)  

* Källa, käll objekt
* [Scope](#scope), hittar alla regler för synkronisering som ligger inom omfånget
* [Anslut](#join), bestämmer relationen mellan kopplings utrymme och metaversum
* Transform, beräknar hur attribut ska transformeras och flödas
* [Prioritet](#precedence), löser motstridiga attribut bidrag
* Mål, målobjektet

## <a name="scope"></a>Omfång
Omfångs modulen utvärderar ett objekt och fastställer reglerna i omfånget och ska inkluderas i bearbetningen. Beroende på attributets attributvärden utvärderas de olika reglerna för synkronisering enligt omfånget. Till exempel har en inaktive rad användare utan Exchange-postlåda olika regler än en aktive rad användare med en post låda.  
![Diagram som visar scope-modulen för ett objekt.](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope1.png)  

Omfånget definieras som grupper och satser. Satserna finns inuti en grupp. Ett logiskt och används mellan alla satser i en grupp. Till exempel (avdelning = IT och land = Danmark). Ett logiskt eller används mellan grupper.

![Omfång](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope2.png)  
Omfånget i den här bilden ska läsas som (avdelning = IT och land = Danmark) eller (land = Sverige). Om antingen grupp 1 eller grupp 2 utvärderas till sant, är regeln i omfånget.

Scope-modulen stöder följande åtgärder.

| Åtgärd | Beskrivning |
| --- | --- |
| LIKA MED, NOTEQUAL |En sträng som utvärderar om värdet är lika med värdet i attributet. För multi-valued-attribut, se ISIN och ISNOTIN. |
| LESSTHAN, LESSTHAN_OR_EQUAL |En sträng jämför som utvärderar om värdet är mindre än värdet i attributet. |
| INNEHÅLLER, NOTCONTAINS |En sträng jämför som utvärderar om värdet kan hittas någonstans i värdet i attributet. |
| STARTSWITH, NOTSTARTSWITH |En sträng jämför som utvärderar om värdet är i början av värdet i attributet. |
| ENDSWITH, NOTENDSWITH |En sträng jämför som utvärderar om värdet är i slutet av värdet i attributet. |
| GREATERTHAN, GREATERTHAN_OR_EQUAL |En sträng jämför som utvärderar om värdet är större än värdet i attributet. |
| ISNULL, ISNOTNULL |Utvärderar om attributet saknas från objektet. Om attributet inte finns och därför är null är regeln i omfånget. |
| ISIN, ISNOTIN |Utvärderar om värdet finns i det definierade attributet. Den här åtgärden är den multi-värde variationen lika och NOTEQUAL. Attributet ska vara ett multi-värde-attribut och om värdet kan hittas i något av attributvärdena är regeln i omfånget. |
| ISBITSET, ISNOTBITSET |Utvärderar om en viss bit har angetts. Kan till exempel användas för att utvärdera bitarna i userAccountControl för att se om en användare är aktive rad eller inaktive rad. |
| ISMEMBEROF, ISNOTMEMBEROF |Värdet ska innehålla en DN till en grupp i anslutnings utrymmet. Om objektet är medlem i den angivna gruppen är regeln i omfånget. |

## <a name="join"></a>Slå ihop
Join-modulen i den synkroniserade pipelinen ansvarar för att hitta relationen mellan objektet i källan och ett objekt i målet. I en inkommande regel skulle den här relationen vara ett objekt i ett anslutnings utrymme som söker efter en relation till ett objekt i metaversum.  
![Koppling mellan CS och MV](./media/concept-azure-ad-connect-sync-declarative-provisioning/join1.png)  
Målet är att se om det redan finns ett objekt i metaversum, vilket skapats av en annan koppling, det ska vara associerat med. I en konto-resurs-skog bör användaren från konto skogen kopplas till användaren från resurs skogen.

Kopplingar används främst för regler för inkommande trafik för att ansluta till kopplings rummets objekt tillsammans till samma metaversum-objekt.

Kopplingarna definieras som en eller flera grupper. I en grupp har du satser. Ett logiskt och används mellan alla satser i en grupp. Ett logiskt eller används mellan grupper. Grupperna bearbetas i ordning från uppifrån och ned. När en grupp har hittat exakt en matchning med ett objekt i målet utvärderas inga andra kopplings regler. Om noll eller fler än ett objekt hittas fortsätter bearbetningen till nästa grupp av regler. Därför bör reglerna skapas i samma ordning som den allra allra första och mer fuzzy i slutet.  
![Kopplings definition](./media/concept-azure-ad-connect-sync-declarative-provisioning/join2.png)  
Kopplingarna i den här bilden bearbetas uppifrån och ned. Första sidan för synkronisering av synkronisering ser om det finns en matchning på Anställningsnr. Annars ser den andra regeln om konto namnet kan användas för att koppla ihop objekten. Om detta inte är en matchning, är den tredje och sista regeln en mer suddig matchning genom att använda namnet på användaren.

Om alla kopplings regler har utvärderats och det inte finns exakt en matchning, används **länk typen** på sidan **Beskrivning** . Om det här alternativet är inställt på **etablera**, skapas ett nytt objekt i målet.  
![Skärm bild som visar den nedrullningsbara menyn "länktyp" öppen.](./media/concept-azure-ad-connect-sync-declarative-provisioning/join3.png)  

Ett objekt får bara ha en regel för att synkronisera med kopplings regler i omfånget. Om det finns flera regler för synkronisering där koppling har definierats, uppstår ett fel. Prioritet används inte för att lösa kopplings konflikter. Ett objekt måste ha en kopplings regel i omfånget för att attributen ska flöda med samma riktning för inkommande/utgående trafik. Om du behöver flöda attribut både inkommande och utgående till samma objekt måste du ha både en inkommande och en regel för utgående synkronisering med Join.

Utgående koppling har ett särskilt beteende när den försöker etablera ett objekt till ett mål kopplings utrymme. Attributet DN används för att först försöka med en omvänd koppling. Om det redan finns ett objekt i mål kopplings utrymmet med samma DN, är objekten anslutna.

Join-modulen utvärderas bara en gång när en ny Synkroniseringsregel kommer att omfånget. När ett objekt har anslutits kopplas det inte från även om kopplings villkoren inte längre är uppfyllda. Om du vill koppla från ett objekt måste den Synkroniseringsregel som anslöt till objekten ingå i omfånget.

### <a name="metaverse-delete"></a>Ta bort metaversum
Ett metaversum-objekt finns kvar så länge det finns en Synkroniseringsregel i omfånget med **länk typen** inställd på **etablera** eller **StickyJoin**. En StickyJoin används när en koppling inte tillåts att etablera ett nytt objekt till metaversum, men när den har anslutits måste den tas bort i källan innan metaversum-objektet tas bort.

När ett metaversum-objekt tas bort markeras alla objekt som är kopplade till en regel för utgående synkronisering som är **markerade för att** ta bort.

## <a name="transformations"></a>Transformeringar
Transformeringarna används för att definiera hur attribut ska flöda från källan till målet. Flödena kan ha en av följande **flödes typer**: direkt, konstant eller uttryck. Ett direkt flöde, flödar ett attributvärde som-är utan ytterligare transformeringar. Ett konstant värde anger det angivna värdet. Ett uttryck använder det deklarativ etablerings uttryckets språk för att uttrycka hur omvandlingen ska vara. Information om uttrycks språk finns i avsnittet förstå definitions [språk för deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) .

![Etablera eller Anslut](./media/concept-azure-ad-connect-sync-declarative-provisioning/transformations1.png)  

Kryss rutan **Använd en gång** definierar att attributet bara ska anges när objektet skapas från början. Den här konfigurationen kan till exempel användas för att ange ett start lösen ord för ett nytt användar objekt.

### <a name="merging-attribute-values"></a>Sammanfogar attributvärden
I attributet flöden finns det en inställning för att avgöra om flervärdesattribut ska slås samman från flera olika anslutningar. Standardvärdet är **Update**, vilket anger att synkroniseringsregeln med högsta prioritet ska vinna.

![Skärm bild som visar avsnittet "Lägg till transformeringar" med den nedrullningsbara menyn "sammanslagnings typer" öppen.](./media/concept-azure-ad-connect-sync-declarative-provisioning/mergetype.png)  

Det finns också **sammanfognings** -och **MergeCaseInsensitive**. Med de här alternativen kan du sammanfoga värden från olika källor. Den kan till exempel användas för att sammanfoga medlems-eller proxyAddresses-attributet från flera olika skogar. När du använder det här alternativet måste alla synkroniserade regler i omfånget för ett objekt använda samma kopplings typ. Du kan inte definiera **uppdatering** från en koppling och **koppla** från en annan. Om du försöker igen visas ett fel meddelande.

Skillnaden mellan **sammanfognings** -och **MergeCaseInsensitive** är hur man bearbetar duplicerade attributvärden. Synkroniseringsmotorn ser till att dubblettvärden inte infogas i målattributet. Med **MergeCaseInsensitive** kommer duplicerade värden med bara en skillnad i fallet inte att vara närvarande. Du bör till exempel inte se både " SMTP:bob@contoso.com " och " smtp:bob@contoso.com " i målattributet. **Sammanslagning** tittar bara på exakta värden och flera värden där det bara finns en skillnad i vad som kan förekomma.

Alternativet **Ersätt** är detsamma som vid **uppdatering**, men används inte.

### <a name="control-the-attribute-flow-process"></a>Styr processen för attribut flöde
När flera regler för inkommande synkronisering har kon figurer ATS för att bidra till samma metaversum-attribut, används prioritet för att fastställa vinnare. Synkroniseringsregeln med högsta prioritet (lägst numeriskt värde) kommer att bidra till värdet. Samma sak inträffar för utgående regler. Synkroniseringsregeln med högst prioritet vinner och bidrar värdet till den anslutna katalogen.

I vissa fall, i stället för att bidra med ett värde, bör synkroniseringsregeln avgöra hur andra regler ska bete sig. Det finns vissa särskilda litteraler som används för det här fallet.

För regler för inkommande synkronisering kan litteralen **Null** användas för att ange att flödet inte har något värde att bidra. En annan regel med lägre prioritet kan bidra med ett värde. Om ingen regel tillför ett värde tas attributet metaversum bort. För en utgående regel, om **Null** är det sista värdet efter att alla formateringsregler har bearbetats, tas värdet bort i den anslutna katalogen.

Litteralen **AuthoritativeNull** liknar **Null** men med skillnaden att inga regler för lägre prioritet kan bidra till ett värde.

Ett Attribute-flöde kan också använda **IgnoreThisFlow**. Det liknar NULL i den mening att det inte finns något att bidra. Skillnaden är att det inte tar bort ett redan befintligt värde i målet. Det är som om attributet Flow aldrig har varit där.

Här är ett exempel:

I *ut till AD – användare Exchange hybrid* kan du hitta följande flöde:  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
Det här uttrycket ska läsas som: om användar post lådan finns i Azure AD, så flöda sedan attributet från Azure AD till AD. Om inte, kommer du inte att flöda något tillbaka till Active Directory. I det här fallet behåller den det befintliga värdet i AD.

### <a name="importedvalue"></a>ImportedValue
Funktionen ImportedValue skiljer sig från alla andra funktioner eftersom attributnamnet måste omges av citat tecken i stället för hakparenteser:  
`ImportedValue("proxyAddresses")`.

Normalt under synkroniseringen använder attributet det förväntade värdet, även om det inte har exporter ATS ännu eller om ett fel togs emot under exporten ("överst i torn"). En inkommande synkronisering förutsätter att ett attribut som ännu inte har nått en ansluten katalog når den. I vissa fall är det viktigt att bara synkronisera ett värde som har bekräftats av den anslutna katalogen ("hologram-och delta import torn").

Ett exempel på den här funktionen finns i den färdiga synkroniseringsregeln *i från AD – användare som är gemensam från Exchange*. I hybrid Exchange bör värdet som läggs till av Exchange Online bara synkroniseras när det har bekräftats att värdet har exporter ATS:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Prioritet
När flera regler för synkronisering försöker bidra till samma attributvärde till målet används prioritet svärdet för att fastställa vinnare. Regeln med högsta prioritet, lägsta numeriska värde, kommer att bidra till attributet i en konflikt.

![Sammanslagnings typer](./media/concept-azure-ad-connect-sync-declarative-provisioning/precedence1.png)  

Den här ordningen kan användas för att definiera mer exakta attribut flöden för en liten delmängd av objekt. De färdiga reglerna är till exempel att se till att attribut från ett aktiverat konto (**User AccountEnabled**) har prioritet från andra konton.

Prioriteten kan definieras mellan kopplingar. Det gör att kopplingar med bättre data kan bidra till värdena först.

### <a name="multiple-objects-from-the-same-connector-space"></a>Flera objekt från samma kopplings utrymme
Om du har flera objekt i samma kopplings utrymme som är anslutna till samma metaversum-objekt, måste prioriteten justeras. Om det finns flera objekt i omfånget för samma Synkroniseringsregel kan inte Synkroniseringsmotorn avgöra prioriteten. Det är tvetydigt vilket källobjektet ska bidra till värdet för metaversum. Den här konfigurationen rapporteras som tvetydig även om attributen i källan har samma värde.  
![Diagram som visar flera objekt som är kopplade till samma MV-objekt med ett transparent rött X-överlägg. ](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple1.png)  

I det här scenariot måste du ändra omfånget för reglerna för synkronisering, så att käll objekten har olika regler för synkronisering i omfånget. Det gör att du kan definiera en annan prioritet.  
![Flera objekt som är kopplade till samma MV-objekt](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Nästa steg
* Läs mer om uttrycks språket i [förstå deklarativ etablerings uttryck](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Se hur deklarativ etablering används direkt i [att förstå standard konfigurationen](concept-azure-ad-connect-sync-default-configuration.md).
* Se hur du gör en praktisk ändring med deklarativ etablering i [hur du ändrar standard konfigurationen](how-to-connect-sync-change-the-configuration.md).
* Fortsätt att läsa hur användare och kontakter fungerar tillsammans i [förstå användare och kontakter](concept-azure-ad-connect-sync-user-and-contacts.md).

**Översikts avsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)

**Referensämnen**

* [Azure AD Connect Sync: Functions reference](reference-connect-sync-functions-reference.md)
