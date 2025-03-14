# Ansible Rolle: RHEL 9 NTP-Konfiguration für Hetzner Cloud

Eine Ansible-Rolle, um Chrony (NTP)-Server auf Red Hat Enterprise Linux 9 und abgeleiteten Distributionen wie AlmaLinux oder Rocky Linux speziell für Hetzner Cloud Server zu konfigurieren.

🇩🇪 **Hinweis:** Diese Rolle wurde mit ❤️ in Deutschland erstellt.

## Funktionen

- Einfache Integration in Foreman/Katello (Importvorlage liegt bei)
- Dynamische Konfiguration mehrerer NTP-Server
- Verwendung DHCP-basierter NTP-Server möglich
- Optionales Hardware-Timestamping und RTC-Synchronisierung
- Erweiterte Logging-Funktionen

## Anforderungen

- Ansible Version 2.14 oder neuer
- Unterstützte Betriebssysteme: RHEL 9, AlmaLinux 9, Rocky Linux 9

## Rollen-Variablen

| Parameter                                 | Erforderlich | Benutzereingabe | Standardwert                          | Beschreibung                                                  |
|-------------------------------------------|--------------|-----------------|---------------------------------------|---------------------------------------------------------------|
| `n_hcloud_ntp_servers`                    | Ja           | Ja             | ntp1.hetzner.de, ntp2.hetzner.com     | Liste der NTP-Server (kommagetrennt, Leerzeichen werden automatisch entfernt, z.B. `server1.example.com,server2.example.com`). |
| `n_hcloud_ntp_allowed_subnets_enabled`    | Nein         | Ja             | `false`                               | Aktiviert subnetzbasierte Zugriffskontrolle (Allow-Regeln).   |
| `n_hcloud_ntp_allowed_subnets`            | Nein         | Ja             | `10.0.0.0/8`                          | Liste erlaubter Client-Subnetze, kommasepariert.              |
| `n_hcloud_ntp_use_dhcp_enabled`           | Nein         | Ja             | `true`                                | Nutzt vom DHCP bereitgestellte NTP-Server.                    |
| `n_hcloud_ntp_dhcp_sourcedir`             | Nein         | Ja             | `/run/chrony-dhcp`                    | Quellverzeichnis für DHCP-basierte NTP-Informationen.         |
| `n_hcloud_ntp_driftfile_enabled`          | Nein         | Ja             | `true`                                | Aktiviert Drift-Datei zur Zeitabweichungsprotokollierung.     |
| `n_hcloud_ntp_driftfile`                  | Nein         | Ja             | `/var/lib/chrony/drift`               | Pfad zur Driftdatei (ermöglicht präzise Zeitmessung).         |
| `n_hcloud_ntp_makestep_enabled`           | Nein         | Ja             | `true`                                | Erlaubt größere Zeitsprünge beim Start.                       |
| `n_hcloud_ntp_makestep_threshold`         | Nein         | Ja             | `1.0`                                 | Zeit-Schwellwert für große Korrekturen in Sekunden.           |
| `n_hcloud_ntp_makestep_limit`             | Nein         | Ja             | `3`                                   | Anzahl erlaubter Zeitkorrekturen nach Systemstart.            |
| `n_hcloud_ntp_enable_rtcsync`             | Nein         | Ja             | `true`                                | Aktiviert Echtzeit-Uhr-Synchronisation (RTC).                 |
| `n_hcloud_ntp_hwtimestamp_enabled`        | Nein         | Ja             | `false`                               | Hardware-Zeitstempel auf Netzwerkschnittstellen aktivieren.   |
| `n_hcloud_ntp_hwtimestamp_interfaces`     | Nein         | Ja             | `*`                                   | Netzwerk-Interfaces für Hardware-Zeitstempel.                 |
| `n_hcloud_ntp_minsources_enabled`         | Nein         | Ja             | `false`                               | Mindestanzahl an NTP-Quellen für Synchronisierung erzwingen.  |
| `n_hcloud_ntp_minsources`                 | Nein         | Ja             | `2`                                   | Anzahl mindestens benötigter NTP-Quellen.                     |
| `n_hcloud_ntp_local_stratum_enabled`      | Nein         | Ja             | `false`                               | Stellt lokalen Zeitdienst bereit, falls unsynchronisiert.     |
| `n_hcloud_ntp_local_stratum`              | Nein         | Ja             | `10`                                  | Lokaler Stratum-Wert (Zeitdienst-Qualitätsstufe).             |
| `n_hcloud_ntp_authselectmode_enabled`     | Nein         | Ja             | `false`                               | Authentifizierung für NTP-Quellen aktivieren.                 |
| `n_hcloud_ntp_authselectmode`             | Nein         | Ja             | `require`                             | Authentifizierungsmodus (`require`, `prefer`).                |
| `n_hcloud_ntp_keyfile`                    | Nein         | Ja             | `/etc/chrony.keys`                    | Pfad zur NTP-Schlüsseldatei für Authentifizierung.            |
| `n_hcloud_ntp_ntsdumpdir`                 | Nein         | Ja             | `/var/lib/chrony`                     | Speicherort für NTS-Schlüssel und Cookies.                    |
| `n_hcloud_ntp_leapsecmode_enabled`        | Nein         | Ja             | `false`                               | Fügt Schaltsekunden langsam ein (`slew`) statt sofort (`step`). |
| `n_hcloud_ntp_leapsecmode`                | Nein         | Ja             | `slew`                                | Methode zur Behandlung von Schaltsekunden (`slew`/`step`).    |
| `n_hcloud_ntp_leapsectz`                  | Nein         | Ja             | `right/UTC`                           | Zeitzone für Schaltsekunden-Datenbank (TAI-UTC).              |
| `n_hcloud_ntp_logging_enabled`            | Nein         | Ja             | `false`                               | Aktiviert erweitertes Logging.                                |
| `n_hcloud_ntp_logging_dir`                | Nein         | Ja             | `/var/log/chrony`                     | Logdateien-Verzeichnis.                                       |
| `n_hcloud_ntp_logging_selections`         | Nein         | Ja             | `measurements,statistics,tracking`    | Welche Log-Informationen gespeichert werden sollen.           |

## Verwendung

Verwendung dieser Rolle in einem Ansible Playbook-Beispiel:

```yaml
---
- hosts: all
  roles:
    - role: ansible_rhel_9_ntp_hcloud
      n_hcloud_ntp_servers: "{{ general_ntp_servers | split(',') }}"
      n_hcloud_ntp_allowed_subnets_enabled: "{{ general_allowed_subnets_enabled }}"
      n_hcloud_ntp_allowed_subnets: "{{ general_allowed_subnets | split(',') | map('trim') | list }}"
      n_hcloud_ntp_use_dhcp_enabled: "{{ general_ntp_use_dhcp_enabled }}"
      n_hcloud_ntp_dhcp_sourcedir: "{{ general_ntp_dhcp_sourcedir }}"
      n_hcloud_ntp_driftfile_enabled: "{{ general_ntp_driftfile_enabled }}"
      n_hcloud_ntp_driftfile: "{{ general_ntp_driftfile }}"
      n_hcloud_ntp_makestep_enabled: "{{ general_ntp_makestep_enabled }}"
      n_hcloud_ntp_makestep_threshold: "{{ general_ntp_makestep_threshold }}"
      n_hcloud_ntp_makestep_limit: "{{ general_ntp_makestep_limit }}"
      n_hcloud_ntp_enable_rtcsync: "{{ general_enable_sync }}"
      n_hcloud_ntp_hwtimestamp_enabled: "{{ general_ntp_hwtimestamp_enabled }}"
      n_hcloud_ntp_hwtimestamp_interfaces: "{{ general_ntp_hwtimestamp_interfaces }}"
      n_hcloud_ntp_minsources_enabled: "{{ general_ntp_minsources_enabled }}"
      n_hcloud_ntp_minsources: "{{ general_ntp_minsources }}"
      n_hcloud_ntp_local_stratum_enabled: "{{ general_ntp_local_stratum_enabled }}"
      n_hcloud_ntp_local_stratum: "{{ general_ntp_local_stratum }}"
      n_hcloud_ntp_authselectmode_enabled: "{{ general_ntp_authselectmode_enabled }}"
      n_hcloud_ntp_authselectmode: "{{ general_ntp_authselectmode }}"
      n_hcloud_ntp_enable_rtcsync: "{{ general_enable_sync }}"
      n_hcloud_ntp_hwtimestamp_enabled: "{{ general_ntp_hwtimestamp_enabled }}"
      n_hcloud_ntp_hwtimestamp_interfaces: "{{ general_ntp_hwtimestamp_interfaces }}"
      n_hcloud_ntp_keyfile: "{{ general_ntp_keyfile }}"
      n_hcloud_ntp_ntsdumpdir: "{{ general_ntp_ntsdumpdir }}"
      n_hcloud_ntp_leapsecmode_enabled: "{{ general_ntp_leapsecmode_enabled }}"
      n_hcloud_ntp_leapsecmode: "{{ general_ntp_leapsecmode }}"
      n_hcloud_ntp_leapsectz: "{{ general_ntp_leapsectz }}"
      n_hcloud_ntp_logging_enabled: "{{ general_ntp_logging_enabled }}"
      n_hcloud_ntp_logging_dir: "{{ general_ntp_logging_dir }}"
      n_hcloud_ntp_logging_selections: "{{ general_ntp_logging_selections }}"
```


## Foreman/Katello Integration

Die Datei zur Integration mit Foreman/Katello liegt hier:

```
files/ansible_rhel_9_ntp_hcloud.erb
```

Diese kannst du direkt im Foreman Backend importieren:

```
Hosts → Vorlagen → Job-Vorlagen
```

## Lizenz

Dieses Projekt steht unter der **MIT Lizenz** – weitere Details in der [LICENSE](LICENSE) Datei.

## Autor

Pierre by NAWKO

