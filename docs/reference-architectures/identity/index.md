---
title: Scegliere una soluzione per l'integrazione di Active Directory locale con Azure.
description: Confronta le architetture di riferimento per l'integrazione di Active Directory locale con Azure.
ms.date: 07/02/2018
ms.openlocfilehash: 7e89998c59bccf4d37cebca5ddd4ea7ecba58cd5
ms.sourcegitcommit: 776b8c1efc662d42273a33de3b82ec69e3cd80c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38987530"
---
# <a name="choose-a-solution-for-integrating-on-premises-active-directory-with-azure"></a>Scegliere una soluzione per l'integrazione di Active Directory locale con Azure

Questo articolo confronta le opzioni per l'integrazione dell'ambiente di Active Directory locale con una rete di Azure. Per ogni opzione è disponibile un'architettura di riferimento più dettagliata.

Molte organizzazioni usano Active Directory Domain Services per autenticare le identità associate a utenti, computer, applicazioni o ad altre risorse incluse in un limite di sicurezza. I servizi di gestione delle identità e delle directory vengono in genere ospitati in locale, ma se l'applicazione è ospitata in parte in locale e in parte in Azure, potrebbe esserci latenza nell'invio delle richieste di autenticazione da Azure a locale. L'implementazione dei servizi di gestione delle identità e delle directory in Azure può ridurre la latenza.

Azure offre due soluzioni per l'implementazione dei servizi di gestione delle identità e delle directory in Azure: 

* Usare [Azure AD][azure-active-directory] per creare un dominio di Active Directory nel cloud e connetterlo al dominio di Active Directory locale. [Azure AD Connect][azure-ad-connect] integra le directory locali con Azure AD.

* Estendere l'infrastruttura locale esistente di Active Directory ad Azure, distribuendo una macchina virtuale in Azure che esegue Active Directory Domain Services come controller di dominio. Questa architettura è più comune quando la rete locale e la rete virtuale di Azure sono connesse tramite una connessione VPN o ExpressRoute. Sono possibili diverse varianti di questa architettura: 

    - Creare un dominio in Azure e aggiungerlo alla foresta di Active Directory locale.
    - Creare una foresta separata in Azure considerata attendibile dai domini nella foresta locale.
    - Replicare una distribuzione di Active Directory Federation Services in Azure. 

Le sezioni successive descrivono tutte queste opzioni in modo più dettagliato.

## <a name="integrate-your-on-premises-domains-with-azure-ad"></a>Integrare i domini locali con Azure AD

Usare Azure Active Directory per creare un dominio in Azure e collegarlo a un dominio locale di Active Directory. 

La directory di Azure AD non è un'estensione di una directory locale. Si tratta piuttosto di una copia che contiene gli stessi oggetti e le stesse identità. Le modifiche apportate a questi elementi locali vengono copiate in Azure AD, tuttavia le modifiche apportate in Azure AD non vengono replicate nel dominio locale.

È anche possibile usare Azure AD senza usare una directory locale. In questo caso, Azure AD funge da fonte principale di tutte le informazioni di identità, invece di contenere i dati replicati da una directory locale.

**Vantaggi**

* Non è necessario gestire un'infrastruttura di Active Directory nel cloud. Azure AD è completamente gestito da Microsoft.
* Azure AD offre le stesse informazioni di identità disponibili in locale.
* L'autenticazione può essere eseguita in Azure, riducendo la necessità di applicazioni esterne e utenti per contattare il dominio locale.

**Problematiche**

* I servizi di gestione delle identità sono limitati a utenti e gruppi. Non è possibile eseguire l'autenticazione del servizio e degli account del computer.
* È necessario configurare la connettività con il dominio locale per mantenere la sincronizzazione della directory di Azure AD. 
* Per abilitare l'autenticazione tramite Azure AD è possibile che sia necessario riscrivere le applicazioni.

**Architettura di riferimento**

- [Integrare i domini Active Directory locali con Azure Active Directory][aad]

## <a name="ad-ds-in-azure-joined-to-an-on-premises-forest"></a>Active Directory Domain Services in Azure unito a una foresta locale

Distribuire i server di Active Directory Domain Services in Azure. Creare un dominio in Azure e aggiungerlo alla foresta di Active Directory locale. 

Prendere in considerazione questa opzione se si desidera usare le funzionalità di Active Directory Domain Services non implementate al momento da Azure AD. 

**Vantaggi**

* Offre accesso alle stesse informazioni di identità disponibili in locale.
* È possibile eseguire l'autenticazione di utenti, servizi e account del computer locale e in Azure.
* Non è necessario gestire una foresta di Active Directory separata. Il dominio in Azure può appartenere alla foresta locale.
* È possibile applicare i criteri del gruppo definiti dagli oggetti Criteri di gruppo locali nel dominio in Azure.

**Problematiche**

* È necessario distribuire e gestire i propri server e il dominio di Active Directory Domain Services nel cloud.
* Potrebbe verificarsi una certa latenza di sincronizzazione tra i server del dominio nel cloud e i server in esecuzione in locale.

**Architettura di riferimento**

- [Estendere Active Directory Domain Services in Azure][ad-ds]

## <a name="ad-ds-in-azure-with-a-separate-forest"></a>Active Directory Domain Services in Azure con una foresta separata

Distribuire i server di Active Directory Domain Services in Azure, ma creare un'altra [foresta][ad-forest-defn] di Active Directory separata dalla foresta locale. Questa foresta è considerata attendibile dai domini nella foresta locale.

Gli usi tipici di questa architettura includono mantenere la separazione della sicurezza per le identità e gli oggetti contenuti nel cloud ed eseguire la migrazione di singoli domini da un ambiente locale al cloud.

**Vantaggi**

* È possibile implementare le identità locali e separare solo le identità di Azure.
* Non è necessario eseguire la replica dalla foresta locale di Active Directory in Azure.

**Problematiche**

* L'autenticazione in Azure per le identità locali richiede hop di rete aggiuntivi per i server di Active Directory locali.
* È necessario distribuire i propri server e la foresta di Active Directory Domain Services nel cloud e stabilire le relazioni di trust appropriate tra le foreste.

**Architettura di riferimento**

- [Creare una foresta di risorse di Active Directory Domain Services in Azure][ad-ds-forest]

## <a name="extend-ad-fs-to-azure"></a>Estendere AD FS in Azure

Replicare una distribuzione di Active Directory Federation Services in Azure, per eseguire l'autenticazione federata e l'autorizzazione per i componenti in esecuzione in Azure. 

Usi tipici di questa architettura:

* Le organizzazioni partner possono autenticare e autorizzare gli utenti.
* Gli utenti possono eseguire l'autenticazione da Web browser in esecuzione all'esterno del firewall aziendale.
* Gli utenti possono connettersi da dispositivi esterni autorizzati, ad esempio dispositivi mobili. 

**Vantaggi**

* È possibile sfruttare le applicazioni in grado di riconoscere attestazioni.
* Offre la possibilità di considerare attendibili i partner esterni per l'autenticazione.
* Compatibilità con grandi set di protocolli di autenticazione.

**Problematiche**

* È necessario distribuire i server di Active Directory Domain Services, Active Directory Federation Services e del proxy applicazione Web di Active Directory Federation Services in Azure.
* Questa architettura può essere complessa da configurare.

**Architettura di riferimento**

- [Estendere Active Directory Federation Services in Azure][adfs]

<!-- links -->

[aad]: ./azure-ad.md
[ad-ds]: ./adds-extend-domain.md
[ad-ds-forest]: ./adds-forest.md
[ad-forest-defn]: /windows/desktop/AD/forests
[adfs]: ./adfs.md

[azure-active-directory]: /azure/active-directory-domain-services/active-directory-ds-overview
[azure-ad-connect]: /azure/active-directory/active-directory-aadconnect
