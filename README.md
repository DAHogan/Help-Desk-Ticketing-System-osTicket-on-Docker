# Help Desk Ticketing System — osTicket on Docker

A self-hosted help desk ticketing system built with osTicket, running in Docker containers on Ubuntu Server. This project simulates a real IT support workflow — logging, triaging, resolving, and closing tickets — and connects directly to the `SudoFixIt.local` Active Directory lab by resolving realistic tickets tied to domain users.

---

## Environment

| Component | Details |
|---|---|
| Host machine | macOS Apple Silicon (UTM/QEMU ARM) |
| Docker host | Ubuntu Server 25.10 (ARM64) |
| Containers | osTicket (web) + MariaDB (database) |
| Orchestration | Docker Compose |
| Ticketing app | osTicket v1.17.5 |
| Server IP | 192.168.64.52 |
| Related lab | SudoFixIt.local Active Directory environment |

---

## What I built

- Deployed a dedicated Ubuntu Server 25.10 VM as a Docker host
- Installed Docker Engine and Docker Compose from the official Docker repository
- Wrote a multi-container Docker Compose configuration for osTicket and MariaDB
- Configured cross-container networking and persistent storage volumes
- Resolved a CPU architecture mismatch (amd64 image on ARM64 host) using QEMU binfmt emulation
- Diagnosed and fixed container-to-container database authentication issues
- Completed the osTicket web installer and configured the help desk identity
- Created departments (IT, HR) mirroring the Active Directory OU structure
- Created and resolved realistic support tickets tied to domain users
- Documented full ticket lifecycle: submission, triage, assignment, response, resolution, closure

---

## Project structure

```
Help-Desk-osTicket/
├── README.md
├── config/
│   └── docker-compose.yml
└── screenshots/
    ├── 01-support-portal-live.png
    ├── 02-staff-control-panel.png
    ├── 03-departments-configured.png
    ├── 04-ticket-printer-resolved.png
    └── 05-ticket-lockout-resolved.png
```

---

## Skills demonstrated

| Skill | Tool / Technology |
|---|---|
| Linux server administration | Ubuntu Server 25.10 |
| Containerization | Docker Engine, Docker Compose |
| Multi-container orchestration | docker-compose.yml |
| Cross-architecture emulation | QEMU binfmt (amd64 on ARM64) |
| Container networking & volumes | Docker networks, named volumes |
| Database troubleshooting | MariaDB, MySQL authentication |
| Web application deployment | osTicket, Nginx, PHP-FPM |
| Help desk ticket lifecycle | osTicket staff control panel |
| Department & workflow configuration | osTicket admin panel |
| Linux command line & text editing | bash, nano, ufw, curl |

---

## Ticket lifecycle examples

### Ticket #953073 — Account lockout (High priority)
A domain user (`bwilliams`) reported being locked out after repeated failed logins. Resolution referenced the actual Active Directory administration steps from the SudoFixIt.local lab:

- Verified the account lockout status in Active Directory Users and Computers (ADUC)
- Confirmed the account triggered the domain lockout policy (5 failed attempts)
- Unlocked the account via ADUC and confirmed the user could log in
- Advised the user on avoiding future lockouts

This ticket connects three skill areas — Active Directory account management, security lockout policy, and help desk ticketing — into a single documented support case.

### Ticket #230850 — Network printer issue (Normal priority)
A domain user (`jsmith`) could not connect to a network printer. Resolved with standard troubleshooting: verified printer availability, reconnected the network printer, and verified drivers.

---

## Key troubleshooting notes

- **Docker Desktop will not run on Windows Server inside UTM** on Apple Silicon — Hyper-V requires nested virtualization, which UTM/QEMU does not expose to Windows guests. A dedicated Linux VM is the correct approach and matches real-world osTicket deployments.
- **Use the ARM64 Ubuntu ISO** (`live-server-arm64`) on Apple Silicon, not amd64.
- **osTicket images are amd64-only.** Running them on an ARM64 host requires `platform: linux/amd64` in the compose file **plus** QEMU binfmt handlers installed (`qemu-user-static`, `binfmt-support`, and `tonistiigi/binfmt --install all`). Without the binfmt handlers, the container crashes with `exec format error`.
- **Database credentials must match exactly** between the MySQL service and the osTicket service in the compose file. A mismatch produces `Access denied for user` and a container restart loop. Avoid special characters like `!` in passwords to prevent shell/YAML interpretation issues.
- **Wipe volumes with `docker compose down -v`** when changing database credentials — MySQL only applies `MYSQL_USER`/`MYSQL_PASSWORD` on first initialization of an empty data volume.
- **osTicket rejects `.local` email addresses** — its email format validator does not recognize `.local` as a valid TLD. Use a public-style TLD (e.g. `sudofixit.com`) for test accounts. This also reflects real environments, where the internal AD domain and public email domain typically differ.

---

## Part of a larger lab series

This project extends the SudoFixIt.local Windows IT Home Lab into help desk operations:

| Project | Focus |
|---|---|
| Phase 1 | Active Directory & user management |
| Phase 2 | Networking — DNS, DHCP, Group Policy |
| Phase 3 | Security hardening & monitoring |
| Help Desk | osTicket ticketing system (this project) |

---

*Built as part of a self-directed IT/cybersecurity home lab to develop practical, job-ready skills.*
