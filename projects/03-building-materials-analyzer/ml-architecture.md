# Architektura Modeli ML i Pipeline Analityczny

## 1. Architektura Modeli Uczenia Maszynowego

Pipeline analityczny w projekcie Building Materials Analyzer łączy podejście szeregów czasowych z modelami regresyjnymi w celu prognozowania cen jednostkowych materiałów budowlanych:

1. **Model Trendu Długoterminowego (Macro Trend):**
   - Śledzenie średniookresowej dynamiki cen surowców podstawowych (klinkier, ropa naftowa pod XPS, wskaźniki inflacji przemysłowej).
2. **Dekompozycja Sezonowa (Seasonal Decomposition):**
   - Modelowanie cykli rocznych w budownictwie w Polsce:
     - *Q1 (zima):* najniższy popyt, promocje magazynowe producentów.
     - *Q2 (wiosna):* dynamiczny wzrost cen fundamentów i izolacji.
     - *Q3 (lato):* szczyt zapotrzebowania na materiały konstrukcyjne i pokrycia dachowe.
     - *Q4 (jesień):* wykończeniówka i ocieplenia przed zimą.
3. **Modele Regresyjne:**
   - Implementacja zespołów drzew decyzyjnych (`RandomForestRegressor`, `GradientBoostingRegressor`) z walidacją krzyżową TimeSeriesSplit.

---

## 2. Architektura Integracji Scraperów

Dla każdego portalu e-commerce (Leroy Merlin, Castorama) zastosowano standaryzowany interfejs:
```text
BaseScraper
├── fetch_category(category_url) -> RawHTML / JSON
├── parse_products(raw_payload) -> List[ProductDTO]
├── clean_prices(products) -> CleanedPriceDataFrame
└── save_snapshot(database / parquet)
```
- Normalizacja jednostek miary (cena za $m^2$, za sztukę, za paletę lub worek 25 kg).
- Mapowanie unikalnych kodów EAN / SKU w celu porównywania identycznych produktów pomiędzy sieciami handlowymi.
