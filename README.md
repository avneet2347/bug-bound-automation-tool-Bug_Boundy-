# Bug Bounty Automation

This is a Basic tool for Information Gathering with real time alerts using Telegram Bot 🚀

## Install with Docker

1. Pull the image from Docker Hub

```bash
docker pull ezhil56x/recon
```

2. Run the image

```bash
docker run --name recon -d -p 5000:5000 -e TELEGRAM_BOT_TOKEN="" -e TELEGRAM_CHAT_ID="" ezhil56x/recon
```

3. Go to [http://localhost:5000/recon](http://localhost:5000/recon) and add Organization Name and Domains

4. SSH into the container

```bash
docker exec -it recon /bin/bash
```

5. Run the `recon.sh`

```bash
cd recon
./recon.sh
```



















# Bug Bounty Automation Tool

A Web-Based Tool for Automated Information Gathering with Real-Time Telegram Alerts
Overview
The Bug Bounty Automation Tool is a Flask-based web application designed for bug bounty hunters and security researchers. It automates reconnaissance tasks like domain pinging, WHOIS lookups, IP geolocation, certificate searches (via crt.sh), open port scanning (Nmap), and subdomain enumeration (Subfinder + Httpx). Results are streamed in real-time via WebSockets, and alerts can be sent to Telegram for instant notifications.
Built on Kali Linux tools inside a Docker container, this tool simplifies OSINT (Open Source Intelligence) gathering for domains and organizations. Add organizations and domains via the UI, run recon scripts, and get structured outputs – perfect for CTF challenges, pentesting, or bug bounty programs.
Key Goals:

Streamline recon workflows with a simple web interface.
Real-time updates and Telegram integration for alerts.
Dockerized for easy deployment and portability.
Extensible with Go-based tools for fast subdomain discovery.

Features
Core Recon Tools

Ping Domain: Test connectivity with 4 pings and stream output.
WHOIS Lookup: Fetch domain registration details using python-whois.
IP Lookup: Geolocation and info via ip-api.com (JSON response).
Certificate Search: Query crt.sh for subdomains via SSL certificates.
Open Ports Scan: Quick Nmap scan (-F flag) for top 100 ports.
Full Recon: Add organizations/domains to a persistent store; run recon.sh for subdomain enumeration (Subfinder + Httpx).

Integration & UI

Real-Time Updates: WebSocket-powered live streaming of command outputs.
Telegram Alerts: Send notifications on task completion (configure bot token/chat ID via env vars).
Persistent Storage: Organizations and domains saved in recon/orgs.txt and per-org domains.txt.
Responsive Web UI: Simple forms for inputs; results displayed in dynamic sections.

General Features

Dockerized Deployment: Pre-built image with Kali tools, Go binaries (Subfinder, Httpx), and Python deps.
Cross-Platform: Runs anywhere Docker is installed (Windows, macOS, Linux).
Error Handling: Subprocess management with buffering for smooth streaming.
Extensible: Easy to add more tools (e.g., Nuclei, Amass) via Dockerfile.

Limitations (Current)

Telegram alerts not fully implemented in UI (env vars only; extend for per-task).
Recon script (recon.sh) runs manually inside container.
No authentication (add Flask-Login for production).

Tech Stack

Backend: Python 3 + Flask (web framework) + Flask-SocketIO (real-time comms)
Recon Tools:

WHOIS: python-whois
Scanning: Nmap, curl (for APIs)
Subdomain Enum: Subfinder (Go), Httpx (Go)


Containerization: Docker (Kali Linux base) + Go 1.22.1
Other Libraries: subprocess (CLI tools), os (file handling)
Dependencies: Handled via pip (Flask, Flask-SocketIO, python-whois, python-telegram-bot) and apt/Go installs in Dockerfile
No External DB: File-based storage for orgs/domains

Installation
Option 1: Docker (Recommended – Quick & Isolated)

Pull the Image:
textdocker pull ezhil56x/recon  # Or build your own: see Dockerfile

Run the Container:
textdocker run --name recon-tool -d -p 5000:5000 \
  -e TELEGRAM_BOT_TOKEN="your_bot_token_here" \
  -e TELEGRAM_CHAT_ID="your_chat_id_here" \
  ezhil56x/recon

Replace env vars with your Telegram Bot credentials (create bot via @BotFather).


Access the UI:

Open http://localhost:5000 in your browser.
Navigate to tools (e.g., /ping, /recon).


Run Full Recon:

SSH into container: docker exec -it recon-tool /bin/bash
Add org/domains via UI first (saves to files).
Run: cd recon && ./recon.sh (uses Subfinder/Httpx on domains.txt).


Stop/Remove:
textdocker stop recon-tool && docker rm recon-tool


Option 2: Local Development (Without Docker)

Clone the Repository:
textgit clone https://github.com/yourusername/bug-bounty-automation.git
cd bug-bounty-automation

Install Dependencies (on Kali/Debian-based):
textsudo apt update && sudo apt install -y wget curl iputils-ping python3 pip nmap
wget https://go.dev/dl/go1.22.1.linux-amd64.tar.gz && sudo tar -C /usr/local -xzf go1.22.1.linux-amd64.tar.gz && rm go1.22.1.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin
go install github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest
go install github.com/projectdiscovery/httpx/cmd/httpx@latest
export PATH=$PATH:/root/go/bin  # Adjust for your user
pip install Flask flask-socketio python-whois python-telegram-bot

Run the App:
textpython3 app.py

Access at http://localhost:5000.


Telegram Setup: Set TELEGRAM_BOT_TOKEN and TELEGRAM_CHAT_ID as env vars.

Usage
Quick Start

Launch UI: Go to http://localhost:5000/recon.
Add Organization: Enter org name (e.g., "ExampleCorp") and domains (comma-separated, e.g., "example.com, sub.example.com").

Saves to recon/<org>/domains.txt and tracks in orgs.txt.


Run Tools:

Ping/WHOIS/IP/Open Ports: Enter domain/IP > Click Start > Watch live output.
Cert Search: Enter domain > Streams JSON from crt.sh.


Full Recon:

Inside container: ./recon.sh – Enumerates subdomains and checks HTTP status.


Alerts: On task end, bot sends summary to your Telegram chat.

Example Workflow

Add "Tesla" org with "tesla.com".
Run WHOIS: Gets registrar, expiry.
Run Open Ports: Lists 80, 443 open.
Run Recon Script: Discovers subs like api.tesla.com, checks live with Httpx.


No Sensitive Data: This tool doesn't store creds (except Telegram env vars) – safe for public repos.
Rate Limiting: APIs like crt.sh/ip-api may throttle; add delays for heavy use.
Docker Security: Run as non-root if possible; scan image with Trivy.
Telegram: Use a dedicated bot; never share token.
Legal Note: Only scan domains you own or have permission for (bug bounties).
Test Locally: Verify tools (e.g., nmap --version) before UI runs.

Potential Improvements

Integrate Nuclei for vuln scanning.
Add auth (Flask-Login) and user sessions.
Scheduled recon with Celery/APScheduler.
Export results to JSON/CSV.
More tools: Amass, Aquatone for screenshots.

Contributing
Contributions welcome! Fork, branch, commit, and PR:

Fork the repo.
Create branch: git checkout -b feature/cool-tool.
Commit: git commit -m "Add cool tool".
Push: git push origin feature/cool-tool.
Open Pull Request.

Issues? Open a GitHub Issue with details.
License
This project is licensed under the MIT License - see the LICENSE file for details.
Acknowledgments

Developed by Avneet Kaur, B.Tech (CSE), 6th Semester.
Thanks to ProjectDiscovery for Subfinder/Httpx! 🔍


Last Updated: July 15, 2025
