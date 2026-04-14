### 1. Folder structure
Create sub-directories under main project folder:
1. `data/` contains all raw data files
2. `notebooks/` contains all Jupyter Notebooks 
3. `outputs/` is where all outputs (.csv, .md, .png) are saved

### 2. Reproducibility rules
1. End every day with **Restart Kernel & Run All**
2. Notebook structure: **Imports &rarr; Paths &rarr; Load Data &rarr; Steps &rarr; Exports &rarr; Summary**

### 3 File/path troubleshooting
In `notebooks/D0_setup.ipynb`, verify:
1. your CSV exists at `../data/online_retail.csv`
2. you can read it with `pd.read_csv(...)`
3. you can save a test plot into `outputs/`