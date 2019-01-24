---
title: Progettare per le operazioni
titleSuffix: Azure Application Architecture Guide
description: Progettare un'applicazione per offrire al team operativo gli strumenti necessari.
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: 75eaa7f8e322c66a83d2b43d180780a2fdcd745b
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54480485"
---
# <a name="design-for-operations"></a>Progettare per le operazioni

## <a name="design-an-application-so-that-the-operations-team-has-the-tools-they-need"></a>Progettare un'applicazione per offrire al team operativo degli strumenti necessari

Il cloud ha significativamente modificato il ruolo dei team operativi, che non sono più responsabili della gestione dell'hardware e dell'infrastruttura che ospita l'applicazione.  Premesso ciò, le operazioni costituiscono ancora una parte essenziale per la corretta esecuzione di un'applicazione cloud. Alcune delle principali funzioni dei team operativi includono:

- Distribuzione
- Monitoraggio
- Riassegnazione
- Risposta agli eventi imprevisti
- Controllo della sicurezza

Per le applicazioni cloud è particolarmente importante poter contare su funzionalità affidabili di registrazione e analisi. Coinvolgere il team operativo nelle attività di progettazione e pianificazione, per garantire che l'applicazione fornisca loro i dati e le informazioni dettagliate di cui hanno bisogno per svolgere il loro lavoro.  <!-- to do: Link to DevOps checklist -->

## <a name="recommendations"></a>Consigli

**Rendere ogni elemento osservabile**. Dopo la distribuzione e l'esecuzione di una soluzione, i log e le analisi costituiscono la principale fonte di informazioni sul sistema. L'*analisi* registra un percorso nel sistema ed è utile per individuare colli di bottiglia, problemi di prestazioni e punti di guasto. La *registrazione* acquisisce singoli eventi, quali modifiche dello stato dell'applicazione, errori ed eccezioni. Eseguire registrazioni nell'ambiente di produzione, in caso contrario si perderanno i dati dettagliati proprio quando sono più necessari.

**Instrumentare per il monitoraggio**. Il monitoraggio offre informazioni dettagliate sull'esecuzione di un'applicazione, in termini di disponibilità, prestazioni e integrità del sistema. Il monitoraggio consente, ad esempio, di sapere se si sta rispettando il contratto di servizio. Il monitoraggio viene eseguito durante il normale utilizzo del sistema. Deve essere il più possibile in tempo reale, in modo che il personale addetto alle operazioni possa intervenire tempestivamente in caso di problemi. In teoria, il monitoraggio può contribuire a risolvere i problemi prima che generino un errore critico. Per altre informazioni, vedere [Monitoraggio e diagnostica][monitoring].

**Strumento per l'analisi della causa radice**. L'analisi della causa radice è il processo di individuazione della causa principale degli errori. Viene eseguita dopo che si è già verificato un errore.

**Usare l'analisi distribuita**. Usare un sistema di analisi distribuita progettato per la concorrenza, la modalità asincrona e la scalabilità cloud. Le analisi devono includere un ID di correlazione che passi attraverso i limiti di servizio. Una singola operazione potrebbe comportare chiamate a più servizi dell'applicazione. Se un'operazione non riesce, l'ID di correlazione consente di individuare la causa dell'errore.

**Standardizzare log e metriche**. Il team operativo dovrà aggregare i log dei vari servizi nella soluzione. Se ogni servizio usa un proprio formato di registrazione, diventa difficile o impossibile usarli per ottenere informazioni utili. Definire uno schema comune che includa campi quali ID di correlazione, nome dell'evento, indirizzo IP del mittente e così via. I singoli servizi possono derivare schemi personalizzati che ereditano lo schema di base e contengono campi aggiuntivi.

**Automatizzare le attività di gestione**, inclusi provisioning, distribuzione e monitoraggio. L'automazione di un'attività consente di renderla ripetibile e meno soggetta a errori umani.

**Gestire la configurazione come codice**. Archiviare i file di configurazione in un sistema di controllo della versione, in modo da tenere traccia e controllare la versione delle modifiche ed eseguirne il rollback, se necessario.

<!-- links -->

[monitoring]: ../../best-practices/monitoring.md
