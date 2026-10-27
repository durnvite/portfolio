# Matryca Węzłów Sprzętowych i Topologia Sieci

## 1. Szczegółowa Specyfikacja Węzłów

```text
+--------------------------------------------------------------------------------------------------+
|                                    TOPOLOGIA ŚRODOWISKA INŻYNIERSKIEGO                            |
+--------------------------------------------------------------------------------------------------+
| Węzeł                | Rola Architektoniczna       | CPU / GPU           | RAM    | Storage      |
+----------------------+-----------------------------+---------------------+--------+--------------+
| Stacja Robocza (PC)  | Ciężka inferencja AI, CUDA, | Intel Xeon E5-2697  | 32 GB  | M.2 NVMe SSD |
|                      | scrapowanie, montaż wideo   | RTX 4060 8GB VRAM   | DDR4   | + Dysk G:    |
+----------------------+-----------------------------+---------------------+--------+--------------+
| Serwer Giada F105    | Całodobowy homelab, DNS,    | Intel Celeron N3350 | 6 GB   | SSD 64 GB    |
|                      | magazyn dokumentów, budżet  | (2 rdzenie, 6W TDP) | DDR3L  | + HDD 320 GB |
+----------------------+-----------------------------+---------------------+--------+--------------+
| Cloud VPS            | Gateway agentowy, 24/7 web  | 2 vCPU              | 4 GB   | 80 GB NVMe   |
|                      | scrapers, sygnały Freqtrade | Linux KVM           |        |              |
+----------------------+-----------------------------+---------------------+--------+--------------+
```

---

## 2. Podział Zadań i Pomiary Zasobów

1. **Dlaczego podział jest asymetryczny?**
   - **Giada F105 (6W TDP):** Zoptymalizowana pod minimalne zużycie prądu w trybie ciągłym 24/7/365. Brak ruchomych części (chłodzenie pasywne), bezgłośna praca w mieszkaniu. Brak transkodowania wideo i brak lokalnego OCR.
   - **Stacja PC (Xeon + RTX 4060):** Uruchamiana na żądanie do zadań wymagających mocy: inferencja modeli LLM, trening modeli Scikit-Learn, renderowanie wideo FFmpeg NVENC oraz generowanie obrazów w ComfyUI.
   - **Cloud VPS:** Zapewnia stały adres publiczny IP, niezależność od przerw w dostawie internetu domowego i bezpieczne środowisko dla crawlerów webowych.
