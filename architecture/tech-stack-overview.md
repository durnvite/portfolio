# Zestawienie Stosu Technologicznego (Tech Stack Overview)

## 1. Języki i Środowiska Uruchomieniowe
- **Python (3.10 - 3.14):** Główny język backendu, ML, orkiestracji i testów. Stosowanie type hints, Pydantic v2, Ruff linter, Black formatter.
- **PowerShell / Bash:** Skrypty rozruchowe, provisioning systemowy (Debian/Ubuntu), automatyzacja Docker.
- **Node.js (LTS / Alpine):** Serwery proxy, MCP Browser tools, Actual Budget server.

---

## 2. Konteneryzacja i DevOps
- **Docker & Docker Compose (v2):** Architektura wielostackowa, separacja sieci bridge (`ai-net`, sieci domyślne), ochrona uprawnień (`no-new-privileges:true`, `user: "1000:1000"`).
- **Portainer CE:** Zarządzanie kontenerami z poziomu GUI.
- **Uptime Kuma:** Monitorowanie dostępności endpointów i alerting.
- **Pytest:** Zaawansowany framework testowy E2E z podziałem na testy statyczne i dynamiczne z pełnym sprzątaniem środowiska (`down -v`).

---

## 3. Sztuczna Inteligencja i Uczenie Maszynowe
- **Silniki Inferencji:** `llama.cpp`, `llama-swap` (CUDA), `ComfyUI` z węzłem `ComfyUI-GGUF`.
- **Modele LLM / VLM:** Gemma 4 (E4B, 12B Q8_0/Q4_K_M), Qwen 3.8 / 3.5, Bielik v2.3, Qwen-Image 2.1.
- **Data Science:** Scikit-Learn, Pandas, NumPy, TimeSeriesSplit.
- **Bazy Wektorowe i RAG:** Qdrant, LangChain.

---

## 4. Bazy Danych i Magazyny Danych
- **Bazy Relacyjne i In-Memory:** SQLite (zoptymalizowany pod minimalny RAM i dysk SSD), PostgreSQL 16 (dla n8n), Redis 7 (in-memory broker bez zapisu na dysk).
- **Zarządzanie Pamięcią Wiedzy:** Obsidian Vault z metadanymi frontmatter YAML i formatem Markdown.
