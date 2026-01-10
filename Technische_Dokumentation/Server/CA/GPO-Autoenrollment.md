# GPO – Automatische Zertifikatsregistrierung (Auto-Enrollment)

Dieses Dokument beschreibt die Konfiguration einer Group Policy (GPO),
mit der Domänen-Computer automatisch Zertifikate von der Microsoft
Enterprise Certificate Authority beziehen.

Ziel ist es, Clients nach dem Domain-Join zur automatischen
Zertifikatsregistrierung zu befähigen bzw. zu verpflichten.

---

## Ziel der Konfiguration

- Domänen-Computer sollen automatisch ein Computerzertifikat erhalten
- Zertifikate sollen automatisch erneuert und aktualisiert werden
- Manuelle Zertifikatsanforderungen sollen vermieden werden

---

## Voraussetzungen

- Microsoft Enterprise CA ist installiert und funktionsfähig
- Geeignetes Zertifikat-Template (z. B. „Computer“) ist vorhanden
- Clients sind Mitglied der Active-Directory-Domäne
- Administrative Berechtigungen für GPO-Verwaltung

---

## Schritt 1: Erstellen einer neuen Group Policy

- **Group Policy Management** öffnen
- Neue GPO erstellen
- Name der GPO, z. B.:
  - `GPO-Certificate-AutoEnrollment`
- GPO mit der OU verknüpfen, in der sich die Client-Computer befinden

---

## Schritt 2: Konfiguration der Auto-Enrollment-Richtlinie

Die GPO wird im folgenden Pfad konfiguriert:

`Computer Configuration  
→ Policies  
→ Windows Settings  
→ Security Settings  
→ Public Key Policies`

- Richtlinie **Certificate Services Client – Auto-Enrollment** öffnen
- Status auf **Enabled** setzen
- Folgende Optionen aktivieren:
  - **Renew expired certificates, update pending certificates and remove revoked certificates**
  - **Update certificates that use certificate templates**

Diese Einstellungen sorgen dafür, dass Zertifikate automatisch
angefordert, erneuert und aktualisiert werden.

---

## Schritt 3: Konfiguration des Zertifikat-Templates

Damit Auto-Enrollment funktioniert, muss ein geeignetes Template
für Computer verfügbar sein.

### Vorgehen

- **Certificate Templates** Konsole öffnen
- Template **Computer** verwenden oder duplizieren
- Berechtigungen prüfen:
  - `Domain Computers`
    - **Enroll**
    - **Autoenroll**
- Template speichern

---

## Schritt 4: Veröffentlichen des Templates auf der CA

- **Certification Authority** Konsole öffnen
- Rechtsklick auf **Certificate Templates**
- **New → Certificate Template to Issue**
- Das konfigurierte Template auswählen und veröffentlichen

---

## Schritt 5: Aktualisierung der Clients

Auf einem Domänen-Client:

- Gruppenrichtlinien aktualisieren:

gpupdate /force

- Client neu starten (empfohlen)

---

## Schritt 6: Überprüfung der Zertifikatsregistrierung

Auf dem Client:

- `certlm.msc` öffnen
- Pfad: Personal → Certificates

## Ergebnis

Nach erfolgreicher Konfiguration erhalten Domänen-Clients automatisch
ein gültiges Computerzertifikat von der Enterprise Certificate Authority.
Die Zertifikate können nun für weitere sicherheitsrelevante Zwecke
verwendet werden (z. B. 802.1X, VPN, TLS).




