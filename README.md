# Semantic Segmentation U-Net Project

Projekt służy do przygotowania danych oraz trenowania modelu U-Net do segmentacji semantycznej obrazów lotniczych/satelitarnych.

W repozytorium znajdują się dwa główne notebooki:

- `data_preprocessing.ipynb` — przygotowanie danych, kodowanie masek klas oraz cięcie obrazów i masek na patche.
- `Train_model.ipynb` — podział danych na train/validation/test, definicja datasetu PyTorch, model U-Net, trening oraz wizualizacja predykcji.

## Struktura danych

Notebook preprocessingowy zakłada strukturę danych podobną do:

```text
Semantic segmentation dataset/
├── Tile 1/
│   ├── images/
│   │   ├── image_part_001.jpg
│   │   └── ...
│   └── masks/
│       ├── image_part_001.png
│       └── ...
├── Tile 2/
│   ├── images/
│   └── masks/
└── ...
```

Po uruchomieniu preprocessingu powstaje folder:

```text
prepared_data256/
├── images/
│   ├── image_part_001_0.png
│   ├── image_part_001_1.png
│   └── ...
└── masks/
    ├── image_part_001_0.png
    ├── image_part_001_1.png
    └── ...
```

## Ważne: spójny `patch_size`

W notebooku `Train_model.ipynb` dane są ładowane z folderu:

```python
prepared_data256
```

Dlatego w `data_preprocessing.ipynb` najlepiej ustawić:

```python
patch_size = 256
```

Jeżeli ustawisz np. `patch_size = 1024`, preprocessing utworzy folder `prepared_data1024`, a trening nie znajdzie danych, dopóki nie zmienisz ścieżki w notebooku treningowym.

## Klasy segmentacji

Maski są kodowane na 6 klas:

| ID | Klasa |
|---:|---|
| 0 | Building |
| 1 | Land |
| 2 | Road |
| 3 | Vegetation |
| 4 | Water |
| 5 | Unlabeled |

Mapowanie kolorów znajduje się w funkcji `mask_encoding()` w notebooku preprocessingowym.

## Instalacja środowiska

### Windows PowerShell

W folderze projektu uruchom:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
python -m ipykernel install --user --name semantic-segmentation-unet --display-name "Python (semantic-segmentation-unet)"
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

Potem otwórz Jupytera:

```bash
jupyter notebook
```

W notebooku wybierz kernel:

```text
Python (semantic-segmentation-unet)
```

## Sugerowana kolejność pracy

1. Wrzuć dataset do folderu `Semantic segmentation dataset/`.
2. Otwórz `data_preprocessing.ipynb`.
3. Ustaw `patch_size = 256`.
4. Uruchom preprocessing.
5. Sprawdź, czy powstał folder `prepared_data256/images` oraz `prepared_data256/masks`.
6. Otwórz `Train_model.ipynb`.
7. Uruchom komórki treningowe od góry do dołu.

## Typowy workflow Git

Po zmianach w projekcie:

```bash
git status
git add .
git commit -m "Opis zmian"
git push
```

## Co wrzucać do repozytorium

Wrzucamy:

```text
data_preprocessing.ipynb
Train_model.ipynb
requirements.txt
README.md
setup_venv_windows.ps1
setup_venv_linux_mac.sh
.gitignore
```

Nie wrzucamy:

```text
.venv/
prepared_data256/
prepared_data1024/
Semantic segmentation dataset/
__pycache__/
.ipynb_checkpoints/
*.pth
*.pt
```

Duże dane i wytrenowane modele najlepiej trzymać poza repozytorium albo użyć Git LFS.

## Uwaga o notebookach

Przed commitem warto zrobić w każdym notebooku:

```text
Kernel -> Restart Kernel and Run All Cells
```

Dzięki temu łatwo sprawdzić, czy projekt da się uruchomić od zera.

W obecnej wersji notebooka preprocessingowego warto sprawdzić lub usunąć niedokończone fragmenty typu:

```python
img_path =
images =
```

Takie komórki mogą przerwać wykonanie opcji `Run All`.

## Wizualizacja architektury U-Net

Notebook treningowy używa opcjonalnie `torchview` do wygenerowania grafu architektury modelu.

Jeżeli zapis grafu do PNG nie działa, może brakować systemowego programu Graphviz.

### Windows

Najprościej zainstalować Graphviz z oficjalnego instalatora i dodać go do `PATH`.

### Linux

```bash
sudo apt-get install graphviz
```

### macOS

```bash
brew install graphviz
```
