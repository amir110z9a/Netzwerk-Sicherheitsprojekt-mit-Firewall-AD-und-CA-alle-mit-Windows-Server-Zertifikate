
Dieses Repository dokumentiert die Konzeption und Umsetzung eines beispielhaften, abgesicherten Unternehmensnetzwerks in einer Laborumgebung.  
Im Projekt werden eine segmentierte Netzwerkstruktur mit zentraler Firewall, Active Directory, File Server, Certificate Authority (CA) sowie Monitoring mit PRTG und ein Backup-Konzept aufgebaut und dokumentiert.

---

## 1. Systembeschreibung

**Secure IT Lab Infrastructure:** Segmentiertes Unternehmensnetzwerk mit zentraler Firewall, AD, File Server, CA und Monitoring  

## 1. Ziele des Projekts

Ziel dieses Projekts ist die Absicherung einer Unternehmensnetzwerkumgebung durch den Einsatz einer zentralen Firewall, Netzwerksegmentierung, zentraler Authentifizierung sowie Überwachungs- und Zertifikatsinfrastruktur.

Dabei sollen insbesondere folgende Sicherheitsziele erreicht werden:

- Aufbau einer klar segmentierten Netzwerkstruktur (Client-Netz / Admin-Netz / Internet)
- Reduzierung von Angriffsflächen durch gezielte Firewall-Regeln und Zugriffsbeschränkungen
- Sicherstellung einer zentralen und kontrollierten Benutzer- und Geräteauthentifizierung über Microsoft Active Directory
- Schutz sensibler Unternehmensdaten durch einen zentralen File Server mit geregelten Zugriffsrechten
- Vertrauenswürdige Identitäts- und Geräteverwaltung mittels Certificate Authority (CA)

### 1.1 Architekturübersicht

Die Umgebung besteht aus:

- **Firewall** mit drei Interfaces:
  - `WAN / Internet`
  - `LAN-Clients` (Client-Netz)
  - `Admin-LAN` (Administrationsnetz)

- **Client-Netzwerk**: Arbeitsstationen / Benutzer-Clients  
- **Admin-Netzwerk**: Administrator-Workstations und Management-Zugänge  
- **File Server**: Zentrale Dateifreigaben für Benutzer  
- **Microsoft Active Directory (AD)**: Zentrale Benutzer- und Computerverwaltung, Authentifizierung  
- **Certificate Authority (CA)**: Ausstellung von Computer-Zertifikaten beim Domain-Join sowie geplante Zertifikats-basierte Client-Authentifizierung  
- **Monitoring-System (PRTG) und Backup**:
  - Überwachung von Netzwerkgeräten und Clients
  - Monitoring über **ICMP (Ping)** und **SNMP**
  - Voraussetzung: ICMP und SNMP müssen in der Firewall gezielt freigeschaltet sein
  - Backups werden regelmäßig und nach definierten Intervallen durchgeführt.

---

## 2. Schutzbedarfsanalyse

### 2.1 Schutzobjekte (Assets)

1. Firewall  
2. Client-Netzwerk  
3. Admin-Netzwerk  
4. File Server (inkl. Daten)  
5. Microsoft Active Directory  
6. Certificate Authority (CA)  
7. **Monitoring-System (PRTG)**

### 2.2 Bewertung nach Schutzzielen

| Asset                    | Vertraulichkeit | Integrität | Verfügbarkeit | Begründung |
|--------------------------|-----------------|-----------|---------------|-----------|
| Firewall                 | hoch            | hoch      | sehr hoch     | Ein Ausfall oder eine Kompromittierung hätte unmittelbare Auswirkungen auf die gesamte Netzwerkkommunikation |
| Client-Netzwerk          | niedrig/normal  | normal    | hoch          | Arbeitsfähigkeit abhängig |
| Admin-Netzwerk           | hoch            | hoch      | hoch          | Enthält administrative Zugänge |
| File Server              | sehr hoch       | sehr hoch | hoch          | Zentrale, teils geschäftskritische Daten |
| Active Directory         | sehr hoch       | sehr hoch | sehr hoch     |Eine Manipulation oder ein Ausfall würde sämtliche Benutzer- und Zugriffsstrukturen beeinträchtigen und könnte zu vollständigem Kontrollverlust über die Umgebung führen |
| Certificate Authority    | sehr hoch       | sehr hoch | hoch          |Eine Kompromittierung der CA würde dazu führen, dass Zertifikate missbraucht werden könnten und somit die gesamte Vertrauenskette gefährdet wäre | 
| **Monitoring-System**    | hoch            | hoch      | hoch          | Kritisch für Betriebstransparenz & Früherkennung von Ausfällen |


### 2.3 Gesamtschutzbedarf
> **Gesamt-Schutzbedarf: nach Maximalprinzip sehr hoch**

---

## 3. Risikoanalyse

### 3.1 Scope
- Firewall & Segmentierung  
- Client- und Admin-Netz  
- File Server  
- Active Directory  
- Certificate Authority  
- **Monitoring-System (PRTG)**

### 3.2 Bedrohungen (Threats)

1. Unautorisierter Zugriff auf interne Systeme  
2. Angriffe aus dem Internet (DoS, Exploits, Malware)  
3. Kompromittierung des AD  
4. Datenverlust / Manipulation am File Server  
5. Fehlkonfiguration Firewall / NAT  
6. Kompromittierung der CA  
7. **Fehlende Überwachung bei Monitoring-Ausfall → Probleme bleiben unentdeckt**

### 3.3 Risikobewertung

| Threat                                           | Eintrittswahrscheinlichkeit | Schadensausmaß | Risiko |
|--------------------------------------------------|----------------------------|----------------|--------|
| Internet-Angriff                                 | mittel                     | hoch           | hoch   |
| AD-Kompromittierung                              | niedrig                    | sehr hoch      | hoch   |
| File Server Datenverlust                         | mittel                     | sehr hoch      | hoch   |
| Firewall-/NAT-Fehlkonfiguration                  | mittel                     | hoch           | mittel |
| CA-Kompromittierung                              | niedrig                    | sehr hoch      | hoch   |
| **Ausfall des Monitoring-Systems (PRTG)**        | mittel                     | hoch           | mittel |

---

## 4. Sicherheitsmaßnahmen

### 4.1 Bereits umgesetzt

- Netzwerksegmentierung (Client / Admin)
- Granulare Firewall-Regeln
- NAT für Client-Internetverkehr
- Active Directory für zentrale Authentifizierung
- Trennung Admin-/User-Accounts 
- NTFS-Berechtigungen prüfen
- CA zur Zertifikatsverwaltung
- **Monitoring-System (PRTG)**
  - Überwachung per ICMP & SNMP
  - Früherkennung von Systemfehlern
  - Unterstützung bei Troubleshooting und Verfügbarkeitsüberwachung
  - Regelmäßige Backups

### 4.2 Zusätzliche Maßnahmen

#### Firewall & Netzwerk
- Regelmäßige Regel-Reviews

#### File Server
- Versioning / Shadow Copies

#### Active Directory
- MFA für Admins
- Härtung via GPO

#### CA
- Nur Admin-Netzzugriff
- Zertifikatskontrolle
- ggf. Offline Root CA

#### **Monitoring-System (PRTG)**
- SNMP nur auf notwendige Hosts beschränken
- ICMP/SNMP ausschließlich für Monitoring freigeben
- Zugriff nur aus Admin-/Monitoring-Netz
- Monitoring selbst überwachen (Health-Checks)
- Dokumentation der Monitoring-Policies

---

## 5. Restrisiko
- Zero-Day-Schwachstellen  
- Insider Threats  
- Katastrophale physische Ereignisse
- Phishing und Social Engineering  
- Ransomware 

---

## 6. Zusammenfassung
Dieses Dokument beschreibt Architektur, Schutzbedarf, Risikoanalyse und Sicherheitsmaßnahmen einer gesicherten Testumgebung.  



