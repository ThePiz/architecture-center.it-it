---
title: Distribuzione delle applicazioni di Azure per la resilienza e disponibilità
description: Una panoramica delle tecniche per creare distribuzioni affidabili in Azure
author: MikeWasson
ms.date: 04/10/2019
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.openlocfilehash: d8df3fe1c3353c60462959a625ba13ae47b96cf8
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59646643"
---
# <a name="deploying-azure-applications-for-resiliency-and-availability"></a>Distribuzione delle applicazioni di Azure per la resilienza e disponibilità

Durante il provisioning e aggiornare le risorse di Azure, il codice dell'applicazione e le impostazioni di configurazione, un processo ripetibile e prevedibile consente di evitare errori e tempo di inattività. Si consiglia di processi automatizzati per la distribuzione che è possibile eseguire su richiesta ed eseguire di nuovo se qualcosa non funziona. Dopo che i processi di distribuzione sono in esecuzione senza problemi, la documentazione di processo possibile mantenerli in questo modo.

## <a name="automate-processes"></a>Automatizzare i processi

Per attivare le risorse su richiesta, distribuire rapidamente le soluzioni, ridurre al minimo gli errori umani e produrre risultati coerenti e ripetibili, assicurarsi di automatizzare le distribuzioni e aggiornamenti.

### <a name="automate-as-many-processes-as-possible"></a>Automatizzare i processi tanti possibili

I processi di distribuzione più affidabili sono automatizzati e *idempotente* &mdash; , ovvero ripetibili per produrre gli stessi risultati.

- Per automatizzare il provisioning delle risorse di Azure, è possibile usare [Terraform](/azure/virtual-machines/windows/infrastructure-automation#terraform), [Ansible](/azure/virtual-machines/windows/infrastructure-automation#ansible), [Chef](/azure/virtual-machines/windows/infrastructure-automation#chef), [Puppet](/azure/virtual-machines/windows/infrastructure-automation#puppet), [Azure PowerShell](/powershell/azure/overview), [interfaccia della riga di comando di Azure (CLI)](/cli/azure), o [modelli Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview#template-deployment).
- Per configurare le macchine virtuali, è possibile usare [cloud-init](/azure/virtual-machines/windows/infrastructure-automation#cloud-init) (per VM Linux) o [configurazione dello stato di automazione di Azure](/azure/automation/automation-dsc-overview) (DSC).
- Per automatizzare la distribuzione di applicazioni, è possibile usare [servizi di Azure DevOps](/azure/virtual-machines/windows/infrastructure-automation#azure-devops-services), [Jenkins](/azure/virtual-machines/windows/infrastructure-automation#jenkins), o altre soluzioni di integrazione continua/recapito Continuo.

Come procedura consigliata, creare un repository di script di automazione per categoria per l'accesso rapido, documentato con spiegazioni dei parametri ed esempi di uso di uno script. Sincronizzare questa documentazione con le distribuzioni di Azure e designare una persona principale che gestisca il repository.

Gli script di automazione possono anche attivare le risorse su richiesta per il ripristino di emergenza.

### <a name="use-code-to-provision-and-configure-infrastructure"></a>Usare il codice per eseguire il provisioning e configurare l'infrastruttura

Questa procedura, denominata *infrastruttura come codice,* potrebbe usare un approccio dichiarativo o un approccio imperativo (o una combinazione di entrambi).

- [Modelli di Resource Manager](/azure/azure-resource-manager/resource-group-overview#template-deployment) sono un esempio di un approccio dichiarativo.
- [PowerShell](/powershell/azure/overview) gli script sono un esempio di approccio imperativo.

### <a name="practice-immutable-infrastructure"></a>Infrastruttura non modificabile di procedure consigliate

In altre parole, non modificare l'infrastruttura dopo la distribuzione nell'ambiente di produzione. Dopo che sono state applicate modifiche ad hoc, si potrebbe non sapere esattamente cosa è cambiato, pertanto può essere difficile da risolvere i problemi del sistema.

### <a name="automate-and-test-deployment-and-maintenance-tasks"></a>Automatizzare e testare le attività di distribuzione e manutenzione

 Le applicazioni distribuite sono costituite da più parti che devono interagire tra loro. Distribuzione consigliabile sfruttare i meccanismi comprovati, ad esempio script, che è possibile aggiornare e convalidare la configurazione e automatizzare il processo di distribuzione. Testare tutti i processi completamente per garantire che gli errori non causino tempo di inattività aggiuntivo.

### <a name="implement-deployment-security-measures"></a>Implementare misure di sicurezza di distribuzione

Tutti gli strumenti di distribuzione devono includere le restrizioni di sicurezza per proteggere l'applicazione distribuita. Definire e applicare i criteri di distribuzione con attenzione e ridurre al minimo la necessità di intervento umano.

## <a name="deploy-to-multiple-regions-and-instances"></a>Distribuire in più aree e le istanze

Un'applicazione che dipende da una singola istanza di un servizio crea un singolo punto di guasto. Per migliorare la resilienza e scalabilità, eseguire il provisioning di più istanze.

- Per [Servizio app di Azure](/azure/app-service/app-service-value-prop-what-is/) selezionare un [piano di servizio app](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview/) che offra più istanze.
- Per la [servizi Cloud di Azure](/azure/cloud-services/cloud-services-choose-me), configurare ognuno dei ruoli usare [più istanze](/azure/cloud-services/cloud-services-choose-me/#scaling-and-management).
- Per la [macchine virtuali di Azure](/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), assicurarsi che l'architettura include più di una macchina virtuale e che ogni macchina virtuale sia inclusa un [set di disponibilità](/azure/virtual-machines/virtual-machines-windows-manage-availability/).

### <a name="consider-deploying-across-multiple-regions"></a>È consigliabile distribuire in più aree

È consigliabile distribuire tutti, ma le applicazioni meno critiche e i servizi delle applicazioni in più aree. Se l'applicazione viene distribuita in una singola area, nel raro caso che l'intera area diventi non disponibile, l'applicazione anche sarà disponibile.

Se si sceglie di distribuire in una singola area, prendere in considerazione la preparazione per la ridistribuzione in un'area secondaria in risposta a un errore imprevisto.

### <a name="redeploy-to-a-secondary-region"></a>Ridistribuzione in un'area secondaria

Se si eseguono applicazioni e database in un'area primaria singola senza replica, potrebbe essere la strategia di ripristino per la ridistribuzione in un'altra area. Questa soluzione è economica ma più appropriato per applicazioni non critiche che tollerano tempi di ripristino.

Se si sceglie questa strategia, automatizzare il processo di ridistribuzione quanto più possibile e includono gli scenari di ridistribuzione in test sulla risposta di emergenza.

Per automatizzare il processo di ridistribuzione, è consigliabile usare [Azure Site Recovery](/azure/site-recovery/).

## <a name="use-staging-and-production-environments"></a>Usare gli ambienti di staging e produzione

Con buon uso di ambienti di staging e produzione, è possibile eseguire il push degli aggiornamenti nell'ambiente di produzione in modo molto controllato e ridurre al minimo l'interruzione da problemi di distribuzione non prevedibili.

- [*Distribuzione di tipo blu-verde*](https://martinfowler.com/bliki/BlueGreenDeployment.html) prevede la distribuzione di un aggiornamento in un ambiente di produzione separato dall'applicazione live. Dopo la convalida della distribuzione, commutare il routing del traffico alla versione aggiornata. A tal fine consiste nell'usare la [slot di staging](/azure/app-service/web-sites-staged-publishing) disponibili nel servizio App di Azure per inserire temporaneamente una distribuzione prima di spostarla nell'ambiente di produzione.
- [*Versioni canary*](https://martinfowler.com/bliki/CanaryRelease.html) sono simili alle distribuzioni di tipo blu-verde. Anziché commutare tutto il traffico per l'applicazione aggiornata, indirizzare solo una piccola parte del traffico verso la nuova distribuzione. Se si è verificato un problema, ripristinare la distribuzione precedente. In caso contrario gradualmente indirizzare più traffico alla nuova versione. Se si usa servizio App di Azure, è possibile usare il test nella funzionalità di produzione per gestire una versione canary.

## <a name="create-a-rollback-plan-for-deployment-and-updates"></a>Creare un piano di ripristino per la distribuzione e aggiornamenti

Se una distribuzione non riesce, l'applicazione potrebbe diventare non disponibile. Per ridurre al minimo i tempi di inattività, progettare un processo di ripristino dello stato precedente per tornare a una versione valida ultimo. Prevedere una strategia per eseguire il rollback delle modifiche ai database e tutti gli altri servizi che dipende l'app.

Se si usa servizio App di Azure, è possibile configurare uno slot di sito valido ultimo e usarlo per eseguire il rollback di una distribuzione di app di API o web.

## <a name="log-and-audit-deployments"></a>Distribuzioni di log e controllo

Per acquisire come informazioni molto specifiche della versione possibili, implementare una strategia di registrazione affidabile. Se si usano tecniche di pre-distribuzione, verrà eseguito più di una versione dell'applicazione nell'ambiente di produzione. Se si verifica un problema, determinare quale versione rappresenta la causa.

## <a name="document-release-processes"></a>Processi di rilascio di documento

Senza la documentazione di processo di rilascio dettagliati, un operatore potrebbe distribuire un aggiornamento non valido o in modo non corretto può configurare le impostazioni per l'applicazione. Definire e documentare chiaramente il processo di rilascio e assicurarsi che sia disponibile a tutto il team operativo.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Monitorare l'integrità dell'applicazione per l'affidabilità](./monitoring.md)
