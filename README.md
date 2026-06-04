# U-Net Semantic Segmentation of Aerial Imagery

Projekt dotyczy segmentacji semantycznej obrazów lotniczych/satelitarnych z użyciem architektury **U-Net** zaimplementowanej w **PyTorch**.

Celem projektu jest przygotowanie danych obrazowych, zakodowanie masek segmentacyjnych, pocięcie obrazów na mniejsze fragmenty oraz wytrenowanie modelu, który klasyfikuje każdy piksel obrazu do jednej z klas terenowych.

Projekt składa się z dwóch głównych etapów:

1. **Preprocessing danych** — przygotowanie obrazów i masek do treningu.
2. **Trening modelu U-Net** — uczenie modelu segmentacji semantycznej.

---

## 1. Przygotowanie środowiska

Najpierw należy utworzyć lokalne środowisko wirtualne `.venv` i zainstalować wymagane biblioteki z pliku `requirements.txt`.

### Windows PowerShell

W folderze projektu uruchom:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
python -m ipykernel install --user --name semantic-segmentation-unet --display-name "Python (semantic-segmentation-unet)"
```

Jeżeli PowerShell blokuje aktywację środowiska lub uruchamianie skryptów, można jednorazowo użyć:

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
```

Następnie ponownie aktywować środowisko:

```powershell
.\.venv\Scripts\Activate.ps1
```

### Linux / macOS

W folderze projektu uruchom:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
pip install -r requirements.txt
python -m ipykernel install --user --name semantic-segmentation-unet --display-name "Python (semantic-segmentation-unet)"
```

### Uruchomienie notebooków

Po zainstalowaniu zależności uruchom Jupytera:

```bash
jupyter notebook
```

W notebooku wybierz kernel:

```text
Python (semantic-segmentation-unet)
```

Można też uruchamiać notebooki bezpośrednio w VS Code, wybierając ten sam kernel.

---

## 2. Struktura katalogów

Docelowa struktura projektu powinna wyglądać następująco:

```text
U-Net-SemanticSegmentationOfAerialmg/
├── data/
│   └── Semantic segmentation dataset/
│       ├── Tile 1/
│       │   ├── images/
│       │   │   ├── image_part_001.jpg
│       │   │   └── ...
│       │   └── masks/
│       │       ├── image_part_001.png
│       │       └── ...
│       ├── Tile 2/
│       │   ├── images/
│       │   └── masks/
│       └── ...
│
├── data_preprocessing.ipynb
├── Train_model.ipynb
├── requirements.txt
├── README.md
└── .gitignore
```

### Najważniejsze katalogi

* `data/Semantic segmentation dataset/` — folder z oryginalnym datasetem.
* `prepared_data256/` — folder z przygotowanymi patchami obrazów i masek.
* `data/data_preprocessing.ipynb` — notebook do przygotowania danych(powstaje dopiero po wygenrowaniu danych).
* `Train_model.ipynb` — notebook do trenowania modelu U-Net.
* `requirements.txt` — lista bibliotek potrzebnych do uruchomienia projektu.

---

## 3. Dataset

Projekt korzysta z datasetu **Semantic Segmentation of Aerial Imagery**, zawierającego obrazy lotnicze/satelitarne oraz odpowiadające im maski segmentacyjne.

Maski są kodowane na klasy semantyczne. W projekcie wykorzystywane jest 6 klas:

| ID | Klasa      |
| -: | ---------- |
|  0 | Building   |
|  1 | Land       |
|  2 | Road       |
|  3 | Vegetation |
|  4 | Water      |
|  5 | Unlabeled  |

Kodowanie kolorów masek do indeksów klas znajduje się w notebooku:

```text
data_preprocessing.ipynb
```

w funkcji:

```python
mask_encoding()
```

---

## 4. Kolejność uruchamiania projektu

Projekt należy uruchamiać w następującej kolejności:

```text
1. Utworzenie środowiska .venv
2. Instalacja zależności z requirements.txt
3. Uruchomienie data_preprocessing.ipynb
4. Wygenerowanie folderu prepared_data{patch_size}
5. Uruchomienie Train_model.ipynb
6. Trening i ewaluacja modelu U-Net
```

---

## 5. Preprocessing danych

Najpierw należy uruchomić notebook:

```text
data_preprocessing.ipynb
```

Notebook odpowiada za:

* wczytanie oryginalnych obrazów i masek,
* przekonwertowanie kolorów masek na numery klas,
* pocięcie dużych obrazów na mniejsze fragmenty,
* zapis przygotowanych danych do folderu `prepared_data{patch_size}`.

Najważniejszym parametrem jest:

```python
patch_size = 256
```

Parametr `patch_size` określa rozmiar fragmentów, na które zostaną pocięte obrazy i maski.

Przykładowo:

```python
patch_size = 256
```

utworzy folder:

```text
prepared_data256/
```

Natomiast:

```python
patch_size = 512
```

utworzy folder:

```text
prepared_data512/
```

---

## 6. Ważne: zgodność rozmiaru patchy z treningiem

Rozmiar patchy użyty w preprocessingu musi być zgodny z folderem używanym w notebooku treningowym.

Jeżeli w `data_preprocessing.ipynb` ustawiono:

```python
patch_size = 256
```

to w `Train_model.ipynb` dane powinny być ładowane z folderu:

```python
prepared_data256
```

Jeżeli zmienisz rozmiar patchy, np. na:

```python
patch_size = 512
```

to należy również zmienić ścieżkę w notebooku treningowym na:

```python
prepared_data512
```

W przeciwnym razie notebook treningowy nie znajdzie danych albo będzie trenował na danych przygotowanych dla innego rozmiaru patcha.

---

## 7. Trening modelu

Po przygotowaniu danych należy uruchomić notebook:

```text
Train_model.ipynb
```

Notebook treningowy wykonuje następujące kroki:

* wczytuje przygotowane obrazy i maski,
* dzieli dane na zbiory treningowy, walidacyjny i testowy,
* definiuje klasę datasetu PyTorch,
* tworzy model U-Net,
* definiuje funkcję straty i optymalizator,
* trenuje model,
* zapisuje historię treningu,
* wizualizuje predykcje modelu.

Model U-Net jest architekturą typu encoder-decoder z połączeniami skip connection. Encoder stopniowo zmniejsza rozdzielczość reprezentacji i wydobywa cechy wysokiego poziomu, a decoder odtwarza mapę segmentacji piksel po pikselu.

---

## 8. Typowy workflow dla nowej osoby w projekcie

Po sklonowaniu repozytorium:

```bash
git clone https://github.com/pawlej/U-Net-SemanticSegmentationOfAerialmg.git
cd U-Net-SemanticSegmentationOfAerialmg
```

Następnie należy utworzyć środowisko:

### Windows

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
python -m ipykernel install --user --name semantic-segmentation-unet --display-name "Python (semantic-segmentation-unet)"
jupyter notebook
```

### Linux / macOS

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
pip install -r requirements.txt
python -m ipykernel install --user --name semantic-segmentation-unet --display-name "Python (semantic-segmentation-unet)"
jupyter notebook
```

Potem należy uruchomić notebooki w kolejności:

```text
1. data_preprocessing.ipynb
2. Train_model.ipynb
```

---


## 9. Typowy workflow Git

Po wprowadzeniu zmian:

```bash
git status
git add .
git commit -m "Opis zmian"
git push
```

Przed commitem warto sprawdzić, czy Git nie dodaje przypadkowo środowiska `.venv` albo dużych plików modelu:

```bash
git status
```

Aktualizacja lokalnego repozytorium przez innych członków zespołu:

```bash
git pull
```

---

## 10. Uwagi dotyczące notebooków

Przed oddaniem projektu warto uruchomić notebooki od początku:

```text
Kernel -> Restart Kernel and Run All Cells
```

Najpierw:

```text
data_preprocessing.ipynb
```

potem:

```text
Train_model.ipynb
```

Dzięki temu można upewnić się, że cały pipeline działa od zera.
