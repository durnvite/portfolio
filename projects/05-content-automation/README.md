# Projekt 05: Content Automation — Asynchroniczny Pipeline Wideo i Emocjonalne TTS

> **Główny stos:** Python 3.11+, Celery, Redis, Edge-TTS, Silero TTS, FFmpeg, MoviePy

---

## 1. Cel Projektu

W pełni zautomatyzowany, asynchroniczny system generowania materiałów wideo zoptymalizowany pod platformy krótkich form (YouTube Shorts, TikTok, Reels). Pipeline orkiestruje ekstrakcję wątków z serwisów społecznościowych (Reddit), oczyszczanie i formatowanie tekstu, syntezę mowy z modulacją emocjonalną oraz dynamiczny montaż wideo.

```mermaid
flowchart TD
    subgraph S1 ["1. Ekstrakcja & Oczyszczanie Tekstu"]
        REDDIT["📱 <b>Źródło: Reddit API</b><br/><small>Ekstrakcja viralowych wątków</small>"]
        CLEANER["🧹 <b>TextCleaner</b><br/><small>Rozwinięcia skrótów, fonetyka PL</small>"]
        REDDIT ==> CLEANER
    end

    subgraph S2 ["2. Emocjonalna Synteza Audio"]
        TTS["🎙️ <b>Emotional TTS Engine</b><br/><small>Edge-TTS & Silero neuronowe</small>"]
        AUDIO["⏱️ <b>Normalizacja & Znaczniki</b><br/><small>Word-level timestamps dla napisów</small>"]
        TTS ==> AUDIO
    end

    subgraph S3 ["3. Asynchroniczny Render (GPU NVENC)"]
        CELERY["⚡ <b>Celery Worker + Redis</b><br/><small>Kolejkowanie zadań renderowania</small>"]
        ASSEMBLE["🎬 <b>assemble_video_task</b><br/><small>MoviePy + FFmpeg NVENC (RTX 4060)</small>"]
        FINAL["📦 <b>Gotowe Wideo MP4</b><br/><small>Dynamiczne napisy i podkład</small>"]
        CELERY ==> ASSEMBLE ==> FINAL
    end

    CLEANER ==>|Formatowany tekst| TTS
    AUDIO ==>|Wypoziomowane audio + sync| CELERY

    style S1 fill:#f8f9fa,stroke:#d0d7de,stroke-width:1.5px,stroke-dasharray: 4 4
    style S2 fill:#f8f9fa,stroke:#d0d7de,stroke-width:1.5px,stroke-dasharray: 4 4
    style S3 fill:#f0f7ff,stroke:#0969da,stroke-width:1.5px

    style REDDIT fill:#ffffff,stroke:#ea580c,stroke-width:1.5px
    style CLEANER fill:#ffffff,stroke:#6c757d,stroke-width:1.5px
    style TTS fill:#ffffff,stroke:#8250df,stroke-width:1.5px
    style AUDIO fill:#ffffff,stroke:#1a7f37,stroke-width:1.5px
    style CELERY fill:#ffffff,stroke:#0969da,stroke-width:1.5px
    style ASSEMBLE fill:#ffffff,stroke:#0969da,stroke-width:2px
    style FINAL fill:#ffffff,stroke:#1a7f37,stroke-width:2px
```

---

## 2. Kluczowe Elementy Architektury

1. **Orkiestracja Zadania przez Celery:**
   - Rozbicie renderowania na niezależne podzadania (`generate_audio_task`, `transcribe_task`, `assemble_video_task`).
   - Monitorowanie stanu zadań i odporność na awarie pojedynczych workerów.
2. **TextCleaner & Emotional TTS:**
   - Autorski moduł `TextCleaner` przekształcający slang internetowy, skróty i emotikony na fonetyczne odpowiedniki czytane naturalnie w języku polskim.
   - Integracja z `Edge-TTS` oraz modelem neuronowym `Silero TTS` z dynamicznym doborem tonacji i tempa głosu.
3. **Wydajność Montażu Wideo (`assemble_video_task`):**
   - Optymalizacja potoku FFmpeg z wykorzystaniem akceleracji sprzętowej NVENC na karcie NVIDIA RTX 4060.
   - Generowanie dynamicznych napisów słowo-po-słowie (Word-level subtitle sync) na podstawie precyzyjnych znaczników czasowych audio.
4. **Agent Decision Logging Persistence Layer:**
   - Śledzenie decyzji podejmowanych przez modele językowe na każdym etapie montażu w celu powtarzalności i audytu jakości.
