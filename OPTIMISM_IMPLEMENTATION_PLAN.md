# Optimism Chain Support - Implementierungsplan

**Status:** Geplant  
**Datum:** 21. Dezember 2025  
**Issue:** https://github.com/coinbase/x402/issues/835

## Zusammenfassung

Dieser Plan beschreibt die Schritte zur Integration von Optimism (OP Mainnet) und Optimism Sepolia Testnet in das x402-Protokoll. Beide Chains sind EVM-kompatible Layer-2-Lösungen mit nativer USDC-Unterstützung und können mit dem bestehenden `exact`-Schema für EVM-Chains implementiert werden.

## Technische Details

### OP Mainnet (Chain ID: 10)
- **Network Identifier:** `optimism`
- **Chain ID:** `10`
- **USDC Contract:** `0x0b2C639c533813f4Aa9D7837CAf62653d097Ff85`
- **USDC Version:** `2`
- **RPC:** Öffentliche Endpunkte verfügbar
- **Explorer:** https://optimistic.etherscan.io/

### OP Sepolia Testnet (Chain ID: 11155420)
- **Network Identifier:** `optimism-sepolia`
- **Chain ID:** `11155420`
- **USDC Contract:** `0x5fd84259d66Cd46123540766Be93DFE6D43130D7`
- **USDC Version:** `2`
- **RPC:** Öffentliche Endpunkte verfügbar
- **Explorer:** https://sepolia-optimism.etherscan.io/

## Verifizierung

✅ USDC-Verträge auf beiden Chains deployed und verifiziert  
✅ EIP-3009 `transferWithAuthorization`-Funktion verfügbar  
✅ Kompatibel mit bestehendem x402 `exact`-Schema  
✅ Keine neuen kryptografischen Mechanismen erforderlich  
✅ **Keine neuen Interfaces erforderlich** - Nutzt bestehende EVM-Mechanismen

## Contributing Guidelines Compliance

Dieser Plan folgt den [x402 Contributing Guidelines](../CONTRIBUTING.md):

### Workflow-Compliance
- ✅ **Issue existiert:** [#835](https://github.com/coinbase/x402/issues/835)
- ✅ **Fork & Branch-Strategie:** Folgt Standard-Workflow
- ✅ **Tests erforderlich:** Umfassende Test-Strategie enthalten
- ✅ **Dokumentation:** Alle relevanten Dokumente werden aktualisiert
- ✅ **Commit Signing:** Muss bei Implementation aktiviert sein

### New Chains Guidelines
Gemäß [CONTRIBUTING.md#new-chains](../CONTRIBUTING.md#new-chains):

**Keine neuen Scheme-Mechanismen erforderlich:**
- Optimism ist **vollständig EVM-kompatibel**
- Verwendet **bestehende `exact` EVM-Implementation**
- Nutzt vorhandene `SchemeNetworkClient`, `SchemeNetworkServer`, `SchemeNetworkFacilitator` Interfaces (TypeScript)
- Nutzt vorhandene `ClientScheme`, `ServerScheme`, `FacilitatorScheme` Interfaces (Go)
- Nutzt vorhandene Python Client/Server-Patterns

**Nur Konfigurationserweiterung notwendig:**
- Neue Chain-ID-Mappings
- USDC-Vertragsadressen für Optimism
- Network-Identifikatoren

**Kein Re-Audit erforderlich:**
- Da keine Abweichung vom Reference-EVM-Mechanism
- Nutzt identische EIP-3009-Implementation wie andere EVM-Chains

## Implementierungsumfang

### 1. TypeScript SDK (`typescript/packages/shared` oder `@x402/shared`)

#### Dateien zu ändern:
- **Network-Mappings:** 
  - Dateien mit `EvmNetworkToChainId`-Konstanten finden und erweitern
  - Neue Einträge für `optimism` (Chain ID 10) und `optimism-sepolia` (Chain ID 11155420) hinzufügen

- **USDC-Konfigurationen:**
  - Dateien mit USDC-Token-Konfigurationen finden
  - USDC-Vertragsadressen für beide Chains hinzufügen
  - USDC Version 2 für beide Chains konfigurieren

- **Network-Validierung:**
  - Validierungslogik für neue Networks erweitern
  - Sicherstellen, dass Optimism-Networks in allen relevanten Type Guards/Validatoren enthalten sind

#### Aufgaben:
1. Suche nach bestehenden Network-Konfigurationsdateien (z.B. `constants.ts`, `networks.ts`, `evm.ts`)
2. Füge Optimism und Optimism Sepolia zu Chain-ID-Mappings hinzu
3. Füge USDC-Konfigurationen für beide Networks hinzu
4. Aktualisiere TypeScript-Types/Interfaces für die neuen Networks
5. Aktualisiere Dokumentation/JSDoc-Kommentare

### 2. Go SDK (`go/`)

#### Dateien zu ändern:
- **constants.go:**
  - Neue Network-Konstanten für Optimism und Optimism Sepolia hinzufügen
  - Chain-ID-Konstanten definieren

- **types.go oder network-spezifische Dateien:**
  - USDC-Konfigurationen für beide Chains hinzufügen
  - Network-Identifier-Mappings erweitern

- **Validierung:**
  - Network-Validierungsfunktionen aktualisieren

#### Aufgaben:
1. Definiere Konstanten für Optimism-Networks (Chain IDs, Network Names)
2. Füge USDC-Vertragsadressen und Konfigurationen hinzu
3. Erweitere Switch-Cases und Map-Strukturen für Network-Handling
4. Aktualisiere Dokumentation in Kommentaren
5. Stelle sicher, dass alle Network-bezogenen Helper-Funktionen die neuen Chains unterstützen

### 3. Python SDK (`python/x402/`)

#### Dateien zu ändern:
- **Network-Konfigurationsdateien:**
  - Optimism-Mappings hinzufügen
  - Chain-ID-Zuordnungen erweitern

- **USDC-Konfiguration:**
  - USDC-Vertragsadressen für beide Networks hinzufügen
  - Token-Konfigurationen erweitern

#### Aufgaben:
1. Identifiziere Python-Module mit Network-Definitionen
2. Füge Optimism und Optimism Sepolia zu Network-Enums/Dicts hinzu
3. Konfiguriere USDC-Verträge für beide Chains
4. Erweitere Validierungslogik
5. Aktualisiere Type Hints und Docstrings

### 4. Java SDK (`java/`)

Falls vorhanden, ähnliche Schritte wie bei Python:
1. Network-Konstanten hinzufügen
2. USDC-Konfigurationen erweitern
3. Validierungslogik aktualisieren

### 5. Tests

#### Test-Kategorien:

**a) Verifizierungstests:**
- USDC-Vertragserreichbarkeit für beide Chains testen
- `transferWithAuthorization`-Funktion verfügbar und aufrufbar verifizieren
- EIP-3009-Kompatibilität bestätigen

**b) Settlement-Tests:**
- Erfolgreiche Zahlungsabwicklung auf Optimism Mainnet testen
- Erfolgreiche Zahlungsabwicklung auf Optimism Sepolia testen
- Error-Handling bei ungültigen Transaktionen testen

**c) Integration-Tests:**
- End-to-End-Tests mit `exact`-Schema
- Client-Server-Kommunikation mit Optimism-Networks testen
- Facilitator-Integration mit Optimism verifizieren

#### Test-Dateien zu erstellen/erweitern:

**TypeScript:**
- `e2e/tests/networks/optimism.test.ts` (neu)
- `e2e/tests/networks/optimism-sepolia.test.ts` (neu)
- Bestehende Multi-Chain-Tests erweitern

**Go:**
- `go/test/optimism_test.go` (neu)
- Bestehende Network-Tests erweitern (`client_test.go`, `facilitator_test.go`, etc.)

**Python:**
- Entsprechende Test-Dateien im Python-Verzeichnis

### 6. Dokumentation

#### Zu aktualisierende Dokumente:

1. **Core Concepts:**
   - `docs/core-concepts/network-and-token-support.md`: Optimism zu unterstützten Networks hinzufügen

2. **Quickstart Guides:**
   - `docs/getting-started/quickstart-for-buyers.md`: Optimism-Beispiele hinzufügen
   - `docs/getting-started/quickstart-for-sellers.md`: Optimism-Konfigurationsbeispiele

3. **README-Dateien:**
   - `/README.md`: Optimism zur Liste unterstützter Networks hinzufügen
   - `typescript/README.md`: TypeScript-spezifische Optimism-Hinweise
   - `go/README.md`: Go-spezifische Optimism-Hinweise
   - `python/README.md`: Python-spezifische Optimism-Hinweise

4. **Examples:**
   - Beispielcode für Optimism-Integration hinzufügen:
     - `examples/typescript/`: Neues Optimism-Beispiel
     - `examples/go/`: Neues Optimism-Beispiel
     - `examples/python/`: Neues Optimism-Beispiel

## Implementierungsreihenfolge

### Phase 1: Grundlegende Integration (Kritischer Pfad)
1. TypeScript SDK - Network-Mappings und USDC-Konfiguration
2. Go SDK - Network-Konstanten und USDC-Konfiguration
3. Python SDK - Network-Mappings und USDC-Konfiguration
4. Java SDK - Falls vorhanden

### Phase 2: Validierung
5. Basis-Verifizierungstests für beide Chains schreiben
6. Manuelle Tests auf Testnets durchführen
7. USDC-Vertragszugriff verifizieren

### Phase 3: Integration & Tests
8. Settlement-Tests implementieren
9. End-to-End-Tests mit `exact`-Schema
10. CI/CD-Pipeline für Optimism-Tests erweitern

### Phase 4: Dokumentation & Beispiele
11. Dokumentation aktualisieren
12. Code-Beispiele erstellen
13. Migration-Guide erstellen (falls notwendig)

### Phase 5: Review & Release
14. Code-Review durchführen
15. Alle Tests durchführen
16. Release-Notes vorbereiten
17. Release durchführen

## Risiken und Überlegungen

### Technische Risiken:
- **RPC-Endpunkt-Stabilität:** Öffentliche Optimism-RPC-Endpunkte könnten Rate-Limits haben
  - *Mitigation:* Mehrere RPC-Provider dokumentieren, Retry-Logik implementieren

- **Gas-Kosten:** Optimism hat andere Gas-Mechanismen als Ethereum
  - *Mitigation:* Gas-Estimierung testen, Dokumentation mit Gas-Hinweisen

- **Network-Upgrades:** Optimism könnte Breaking Changes einführen
  - *Mitigation:* Versionierung beachten, Monitoring für Contract-Changes

### Kompatibilitätsrisiken:
- **USDC-Version:** Sicherstellen, dass Version 2 mit allen x402-Features kompatibel ist
  - *Mitigation:* Umfassende Tests der `transferWithAuthorization`-Funktion

- **EIP-3009-Implementierung:** Könnte subtile Unterschiede zu anderen Chains haben
  - *Mitigation:* Spezifische Tests für Optimism-USDC-Verträge

## Abhängigkeiten

### Externe Abhängigkeiten:
- Optimism RPC-Provider
- USDC-Verträge auf Optimism (bereits deployed)
- Etherscan/Block-Explorer-API für Vertragsverifizierung

### Interne Abhängigkeiten:
- Bestehendes `exact`-Schema muss EVM-Chains unterstützen (✅ bereits vorhanden)
- TypeScript/Go/Python-SDKs müssen Multi-Chain-fähig sein (✅ bereits vorhanden)

## Erfolgsmetriken

- ✅ Beide Networks (Mainnet und Sepolia) vollständig integriert
- ✅ Alle automatisierten Tests erfolgreich
- ✅ Dokumentation vollständig aktualisiert
- ✅ Mindestens ein funktionierendes End-to-End-Beispiel pro SDK
- ✅ Keine Breaking Changes für bestehende Nutzer
- ✅ CI/CD-Pipeline läuft grün mit Optimism-Tests

## Contributing Workflow

Gemäß den [Contributing Guidelines](../CONTRIBUTING.md#contributing-workflow):

### 1. ✅ Issue erstellt
- Issue [#835](https://github.com/coinbase/x402/issues/835) bereits vorhanden

### 2. Fork & Clone
```bash
# Fork auf GitHub erstellen
# Dann lokal clonen:
git clone https://github.com/YOUR_USERNAME/x402.git
cd x402
```

### 3. Branch erstellen
```bash
git checkout -b feature/optimism-chain-support
```

### 4. Commit Signing konfigurieren
```bash
git config --global commit.gpgsign true
```

### 5. Änderungen durchführen
- Folge dem language-specific Development Guide für jedes SDK
- Schreibe Tests für neue Funktionalität
- Aktualisiere Dokumentation
- Committe regelmäßig mit signierten Commits

### 6. Tests ausführen
```bash
# TypeScript
cd typescript && pnpm test

# Python
cd python/x402 && uv run pytest

# Go
cd go && make test
```

### 7. PR einreichen
- PR-Template vollständig ausfüllen
- Issue #835 verlinken
- Sicherstellen, dass CI grün ist

## Nächste Schritte

1. **Codebase-Analyse:**
   - ✅ Relevante Dateien identifiziert (siehe Implementierungsumfang oben)
   - Bestehende Patterns für EVM-Chain-Integration verstehen
   - Ähnliche Commits für Base/Polygon als Referenz nutzen

2. **Prototyping:**
   - Kleine Proof-of-Concept-Implementation in TypeScript SDK
   - Manuelle Tests auf Optimism Sepolia durchführen

3. **Implementation:**
   - Nach Freigabe: Schrittweise Implementation gemäß Phasenplan
   - Separate PRs pro SDK-Sprache oder ein monolithischer PR (Team-Entscheidung)
   - Tests parallel zur Implementation schreiben
   - Alle Commits signieren

4. **Review & Merge:**
   - Code-Reviews von x402 Foundation Team einholen
   - Feedback einarbeiten
   - Merge nach erfolgreichen Tests und CI

## Referenzen

- **Issue:** https://github.com/coinbase/x402/issues/835
- **Optimism Docs:** https://docs.optimism.io/
- **Circle USDC Docs:** https://docs.circle.com/stablecoins/usdc-on-main-networks#optimism
- **USDC Contract (OP Mainnet):** https://optimistic.etherscan.io/address/0x0b2C639c533813f4Aa9D7837CAf62653d097Ff85
- **USDC Contract (OP Sepolia):** https://sepolia-optimism.etherscan.io/address/0x5fd84259d66Cd46123540766Be93DFE6D43130D7
- **EIP-3009:** https://eips.ethereum.org/EIPS/eip-3009

## Notizen

- Implementierung folgt dem gleichen Pattern wie bestehende EVM-Chains (Base, Polygon)
- Keine neuen kryptografischen Mechanismen erforderlich
- Optimism ist produktionsreif und hat hohes Transaktionsvolumen
- Native USDC-Unterstützung (nicht bridged) macht Integration einfacher
- Community-Mitglied (@fretchen) bereit zur Mithilfe bei der Implementation
