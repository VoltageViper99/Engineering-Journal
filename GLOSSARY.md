# Glossary

[Back to main README](README.md)

Plain-English explanations of the technical words that come up in this journal. If a word you tripped over is missing, it should be here, so tell me.

| Term | What it means |
| --- | --- |
| AI model (local) | A program that has learned patterns from a lot of text and can answer in sentences. "Local" means it runs on my own server instead of a company's cloud, so what I type never leaves the house. |
| API | A doorway one program uses to ask another program for something. A web page is for people; an API is the same idea for software. |
| Audit | A careful check of what a system is really doing, compared with what I think it is doing. Done before changing anything. |
| Backup | A second copy of data kept somewhere else, so losing one copy is not the end of the world. |
| Bluetooth speaker (paired) | A wireless speaker that has already been introduced to a device once, so they can connect again without setting up from scratch. |
| Caddy | The program I use as my reverse proxy (see below). |
| Claude | The AI assistant that wrote much of the code in these projects. I decide what is built, test it and run it. |
| Cloudflare | A company whose network sits in front of websites to make them faster and safer. I use it to host my business websites. |
| Cloudflare Pages | Cloudflare's service for hosting websites. When I save a change to the website's code, the live site updates on its own. |
| Compose file | A short text file that lists which containers to run and how to set them up, so a service can be rebuilt the same way every time. |
| Config file | A settings file. The program reads it when it starts to know how to behave. |
| Container | A sealed box that holds one program and everything it needs to run. Containers keep programs from interfering with each other and are easy to replace. |
| CSS | The language that controls how a web page looks: colours, fonts, sizes and layout. |
| Dashboard | A single screen that shows the state of many things at a glance. My main one is the [Control Centre](projects/control-centre.md). |
| Deploy | Putting a new version of something live, where people actually use it. |
| DNS | The internet's phone book. It turns a name people can remember into the address computers use. |
| Docker | The tool that runs containers on my server. |
| fail2ban | A guard program that watches for repeated failed logins and blocks the source for a while. |
| Firewall | A gatekeeper on a computer or network that decides which connections are allowed in or out. |
| Git | A tool that records every change to a set of files, who made it and why, so any change can be looked at or undone later. |
| GitHub | A website that stores Git projects online. This journal lives there. |
| Homelab | A small set of computers at home, run like a miniature company IT setup, for learning and for real family and business services. |
| HTTPS / TLS | The padlock in your browser. The connection is encrypted, so nobody in between can read it. |
| Kiosk | A computer that shows one app full screen, all the time, like a screen in a shop. Mine shows the dashboard and plays the music. |
| Ligature | Two letters a font joins into one shape, like "fi". Some fonts do it automatically. |
| Linux | A free operating system, an alternative to Windows or macOS. My server and my desktop run it. |
| Log | A diary a program writes as it runs: what it did, and what went wrong. The first place to look when something breaks. |
| Loopback | A connection a computer makes to itself. A service that only listens on loopback cannot be reached from any other machine. |
| Monitoring | Tools that keep checking that services are up and tell me when something is not. |
| Notification (ntfy) | The service that sends alerts to my phone, for example when an update fails. I run my own. |
| Ollama | The program that runs AI models on my own server. |
| Open source | Software whose code anyone can read, and usually reuse. Most of what I run is open source. |
| Port | A numbered door on a computer. Each service listens behind its own door, so closing doors that are not needed shrinks what an attacker can try. |
| Pull request | A proposed change that I read and approve before it goes into a project. Small pull requests are easier to check and to undo. |
| Python | A programming language known for being readable. I use it for scripts and learned it on my CD backup project. |
| Rate limiting | A rule like "no more than five tries in ten minutes", which stops a robot hammering a form or a login. |
| Repository (repo) | A project folder tracked by Git, with its full history of changes. |
| Reverse proxy | A receptionist for my services. Everything goes through one front door by name, and the proxy passes each visitor to the right service behind it. |
| Rollback | Going back to the previous version when a change goes wrong. |
| Script | A small program that does a job I would otherwise do by hand. |
| Secret | A password, key or token that grants access to something. Secrets never go into this journal. |
| Secret scanning | An automatic check that looks for passwords or keys before a change is saved, and refuses the change if it finds one. |
| Server | A computer that runs all the time and provides services to other devices, like file storage, music or photos. |
| Skin / theme | A change of look without a change of behaviour. My dashboard can be dressed as Windows 95 through to Windows 11. |
| SQLite | A small database that lives in a single file. Good for small apps. |
| SSH | A secure way to log in to another computer over the network and type commands on it. |
| Tailscale | A private network that links my own devices together securely, wherever they are, as if they were all at home. |
| Turnstile | Cloudflare's check that a website visitor is a person and not a robot, without the "click every traffic light" puzzles. |
| Uptime Kuma | The monitoring tool that keeps checking my services and alerts me if one stops answering. |
| VPN | A private, encrypted tunnel between devices over the internet. Tailscale is a kind of VPN. |
| Web UI | A settings or control page that opens in a web browser, instead of editing files by hand. |
