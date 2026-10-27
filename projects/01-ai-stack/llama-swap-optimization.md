# Inżynieria Optymalizacji VRAM w `llama-swap`

## 1. Wyzwanie Inżynieryjne
Karta graficzna **NVIDIA GeForce RTX 4060** oferuje nowoczesną architekturę Ada Lovelace z rdzeniami Tensor 4. generacji, lecz posiada twarde ograniczenie pojemnościowe: **8192 MB VRAM**.

Uruchamianie modeli 9B i 12B w precyzjach `Q6_K` oraz `Q8_0` wraz z dużym oknem kontekstowym (16k - 65k tokenów) przekracza fizyczną pojemność pamięci wideo. Standardowe podejście "załadowania całego modelu" kończy się natychmiastowym błędem `CUDA Out of Memory`.

---

## 2. Zastosowane Rozwiązania Architektoniczne

### A. Kwantyzacja Bufora Kontekstu (KV Cache)
Standardowy bufor KV w `f16` przy oknie 32 768 tokenów konsumuje ponad **4 GB VRAM**. Zastosowanie kwantyzacji:
```bash
--cache-type-k q8_0 --cache-type-v q8_0
```
zredukowało narzut pamięciowy bufora o **50%**, umożliwiając oddelegowanie dodatkowych warstw sieci neuronowej bez zauważalnego spadku jakości (perpleksji) odpowiedzi.

### B. Akceleracja Flash Attention
Flaga `--flash-attn on` eliminuje potrzebę alokowania pełnej macierzy uwagi kwadratowej $O(N^2)$ w pamięci VRAM, zastępując ją obliczeniami blokowymi w pamięci podręcznej SRAM procesora graficznego. Pozwala to na stabilne przetwarzanie kontekstu do 65 536 tokenów.

### C. Matematyczny Model Podziału Warstw (Hybrid Offloading)

Wzór na szacunkowe zużycie pamięci VRAM:
$$VRAM_{total} = (W_{layer} \times N_{gpu}) + KV_{cache} + VRAM_{cuda\_runtime}$$

Gdzie:
- $W_{layer}$: średnia waga pojedynczej warstwy transformera w danej kwantyzacji.
- $N_{gpu}$: liczba warstw oddelegowanych do GPU (`--n-gpu-layers`).
- $KV_{cache}$: wielkość bufora kontekstu przy wybranym `--ctx-size`.
- $VRAM_{cuda\_runtime}$: narzut systemowy sterownika NVIDIA (~450–600 MB).

---

## 3. Matryca Konfiguracyjna Modeli w `llama-swap`

| Model | Architektura | Warstwy (GPU/All) | Rozmiar GGUF | Kontekst | Alokacja VRAM | Przepustowość |
|---|---|---|---|---|---|---|
| **Gemma-4-E4B** | 42 warstwy | **99 / 42 (100% GPU)** | 4.2 GB (Q6_K_P) | 65 536 | ~5.8 GB | **~75+ tok/s** |
| **Qwen3.8-9B-heretic** | 32 warstwy | **29 / 32** | 7.36 GB (Q6_K) | 32 768 | ~7.6 GB | **~28-35 tok/s** |
| **gemma-4-12B-it** | 48 warstw | **41 / 48** | 7.1 GB (Q4_K_M) | 16 384 | ~7.5 GB | **~24-30 tok/s** |
| **gemma-4-12B-coder** | 48 warstw | **26 / 48** | 12.7 GB (Q8_0) | 16 384 | ~7.7 GB | **~15-18 tok/s** |

---

## 4. Mechanizm Dynamicznego Swappowania (TTL)

Plik konfiguracyjny definiuje `ttl: 300` (5 minut) dla każdego modelu. Silnik `llama-swap`:
1. Utrzymuje w pamięci VRAM aktualnie odpytywany model.
2. W przypadku żądania do innego modelu, automatycznie zwalnia zasoby VRAM poprzedniego modelu, nie wymagając restartu kontenera.
3. Chroni procesor graficzny przed kolizją pamięciową pomiędzy generowaniem obrazów w ComfyUI a inferencją tekstową.
