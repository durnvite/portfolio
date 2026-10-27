# Projekt 04: Hermes Agentic VPS & Second Brain

> **Lokalizacje na dysku:** `G:\VPS` (Obsidian Second Brain Vault), `G:\Hermes` (Środowisko modeli i narzędzi)  
> **Środowisko wykonawcze:** Cloud Linux VPS + Lokalny PC (połączenie przez Tailscale / SSH)  
> **Główne moduły:** Hermes Supervisor, Damian.Core RAG, Hybrid Scraper, Freqtrade

---

## 1. Koncepcja i Architektura Ekosystemu

Projekt realizuje architekturę rozproszonej sieci agentów autonomicznych (Agentic AI) zintegrowanej z prywatną bazą wiedzy typu Second Brain (Obsidian Vault). Węzeł VPS pełni rolę 24/7 gatewaya wykonawczego, a stacja robocza dostarcza ciężką moc obliczeniową LLM:

```mermaid
flowchart TD
    subgraph Core ["Centralny Nadzór & Pamięć Długoterminowa"]
        HERMES["👑 <b>Hermes Supervisor Agent</b><br/><small>Orkiestracja sesji i routing modeli</small>"]
        VAULT[("🧠 <b>Obsidian Second Brain</b><br/><small>G:/VPS (Kanoniczne źródło prawdy - SoT)</small>")]
        HERMES <===> VAULT
    end

    subgraph Agents ["Agenty Specjalistyczne"]
        DC["🔍 <b>Damian.Core</b><br/><small>Qdrant RAG • Finish-Guard</small>"]
        HS["🕷️ <b>Hybrid Scraper</b><br/><small>Crawl4AI • Bypass Cloudflare</small>"]
        FT["📈 <b>Freqtrade Bot</b><br/><small>Sygnały algorytmiczne</small>"]
        HOME["🏡 <b>Dom w Kruszynie</b><br/><small>Monitoring i infrastruktura</small>"]
    end

    subgraph Runtimes ["Środowiska Wykonawcze"]
        LOCAL["🖥️ <b>Local AI Workstation</b><br/><small>RTX 4060 8GB • llama-swap</small>"]
        VPS["☁️ <b>Cloud VPS Ubuntu</b><br/><small>24/7 cron • systemd workers</small>"]
    end

    HERMES --> DC & HS & FT & HOME
    DC ==>|Ciężka inferencja LLM| LOCAL
    HS & FT & HOME ==>|Zadania 24/7| VPS

    style Core fill:#f8f9fa,stroke:#6f42c1,stroke-width:1.5px
    style Agents fill:#f8f9fa,stroke:#d0d7de,stroke-width:1.5px,stroke-dasharray: 4 4
    style Runtimes fill:#f0f7ff,stroke:#0969da,stroke-width:1.5px

    style HERMES fill:#ffffff,stroke:#6f42c1,stroke-width:2px
    style VAULT fill:#ffffff,stroke:#8250df,stroke-width:1.5px
    style DC fill:#ffffff,stroke:#0969da,stroke-width:1.5px
    style HS fill:#ffffff,stroke:#1a7f37,stroke-width:1.5px
    style FT fill:#ffffff,stroke:#bf8700,stroke-width:1.5px
    style HOME fill:#ffffff,stroke:#6c757d,stroke-width:1.5px
    style LOCAL fill:#ffffff,stroke:#0969da,stroke-width:2px
    style VPS fill:#ffffff,stroke:#24292f,stroke-width:1.5px
```

---

## 2. Kluczowe Podsystemy

### A. Hermes Supervisor
Główny agent nadzorujący sesje inżynieryjne, routing zapytań do modeli (Claude, Gemini, lokalne LLM) oraz automatyczne generowanie streszczeń sesji i rejestru zmian do bazy Obsidian.

### B. Damian.Core (RAG & Knowledge Base)
Moduł integrujący bazę wektorową Qdrant z wyszukiwaniem semantycznym (RAG). Wyposażony w mechanizm `finish-guard` gwarantujący ukończenie zadań i walidację wyników przed raportowaniem.

### C. Hybrid Scraper
Zaawansowany scraper agregujący zlecenia freelancingowe (Useme, Freelancehunt):
- Wykorzystanie `ai-cloudscraper` oraz `Crawl4AI` do omijania zabezpieczeń Cloudflare.
- Eksport danych do ustrukturyzowanych formatów CSV/JSON z automatyczną kategoryzacją budżetów i technologii.

### D. Obsidian Vault jako Kanoniczne Źródło Prawdy (SoT)
Hierarchiczna struktura notatek z metadanymi frontmatter, mapą projektów (`00 - Mapa projektów.md`) oraz logami audytów architektonicznych.

Szczegóły kontraktu agentowego: [multi-agent-contract.md](multi-agent-contract.md).
