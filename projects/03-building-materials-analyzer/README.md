# Projekt 03: Building Materials Analyzer — Predykcja Cen Materiałów Budowlanych

> **Lokalizacja na dysku:** `G:\IT\Vibe coding\Predykcja cen materiałów budowlanych\building_materials_analyzer`  
> **Stos technologiczny:** Python 3.11+, Scikit-Learn, Pandas, NumPy, FastAPI, BeautifulSoup4, Pytest

---

## 1. Cel i Zakres Projektu

System automatyzujący pozyskiwanie, czyszczenie i analizę predykcyjną cen materiałów budowlanych na rynku polskim. Projekt powstał z myślą o optymalizacji kosztów inwestycji budowlanych poprzez identyfikację trendów sezonowych i prognozowanie najkorzystniejszych momentów na zakup kluczowych surowców (cement, styropian XPS, stal zbrojeniowa, wełna mineralna).

```mermaid
flowchart TD
    subgraph Input_Stage ["1. Warstwa Danych & ETL"]
        S1["📥 <b>Akwizycja (Scraping)</b><br/><small>Leroy Merlin • Castorama • OBI</small>"]
        S2["⚙️ <b>Przetwarzanie & Cechy</b><br/><small>Normalizacja • Indeksy cenowe • Sezonowość</small>"]
        S1 ==>|Czysty strumień danych| S2
    end

    subgraph Intelligence_Stage ["2. Warstwa Analityczna & Wyjście"]
        S3["🧠 <b>Silnik Predykcyjny ML</b><br/><small>Gradient Boosting • Random Forest • Trendy ARIMA</small>"]
        S4["📊 <b>Dystrybucja Wyników</b><br/><small>FastAPI REST API • Dashboard analityczny</small>"]
        S3 ==>|Prognozy i alerty zakupowe| S4
    end

    Input_Stage ==>|Znormalizowany zbiór cech| Intelligence_Stage

    style Input_Stage fill:#f8f9fa,stroke:#d0d7de,stroke-width:1.5px,stroke-dasharray: 4 4
    style Intelligence_Stage fill:#f0f7ff,stroke:#0969da,stroke-width:1.5px

    style S1 fill:#ffffff,stroke:#6c757d,stroke-width:1.5px
    style S2 fill:#ffffff,stroke:#6f42c1,stroke-width:1.5px
    style S3 fill:#ffffff,stroke:#0969da,stroke-width:2px
    style S4 fill:#ffffff,stroke:#1a7f37,stroke-width:1.5px
```

---

## 2. Kluczowe Komponenty Systemu

1. **Warstwa Akwizycji (Scraping):**
   - Dedykowane moduły scraperów dla wiodących marketów budowlanych (Leroy Merlin, Castorama).
   - Obsługa rotacji nagłówków `User-Agent`, parsowanie DOM i obsługa dynamicznych endpointów JSON.
2. **Kategorie Surowców:**
   - `Fundamenty`: bloczki betonowe, hydroizolacja.
   - `Izolacje / XPS`: styropian ekstrudowany, wełna mineralna.
   - `Konstrukcja`: stal zbrojeniowa, cement, drewno konstrukcyjne.
   - `Instalacje`: rury, armatura.
3. **Pipeline Danych i Feature Engineering:**
   - Analiza sezonowości (wpływ cyklu budowlanego wiosna-jesień na wahania cenowe).
   - Wyliczanie wskaźników kroczących (Moving Averages, Price Volatility Index).
4. **Jakość i Testy:**
   - Zestaw testów jednostkowych i integracyjnych (`test_all_models.py`, `test_dashboard.py`, `test_leroy_merlin_scraper.py`).

Szczegółowa architektura modeli uczenia maszynowego: [ml-architecture.md](ml-architecture.md).
