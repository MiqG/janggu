# `janggu` dataset modules

This fork only keep the parts of the original code related to efficient data reading. Hence, it is machine-learning framework agnostic.

## installation

```{shell}
# requires bedtools
mamba install bioconda::bedtools -y
pip install git+https://github.com/MiqG/janggu.git
```

## Usage example
Based on tutorial: https://nbviewer.org/github/BIMSBbioinfo/janggu/blob/master/src/examples/pytorch_convnet_example.ipynb

```{python}
import os
os.environ['JANGGU_OUTPUT'] = './janggu_tmp'

from pkg_resources import resource_filename
from janggu.data import Bioseq, Cover

# files
## regions of interest
ROI_TRAIN_FILE = resource_filename('janggu', 'resources/roi_train.bed')
ROI_TEST_FILE = resource_filename('janggu', 'resources/roi_test.bed')
## sequence
REFGENOME = resource_filename('janggu', 'resources/pseudo_genome.fa')
## measurements
PEAK_FILE = resource_filename('janggu', 'resources/scores.bed')

# make datasets
## parameters
order = 1 # possible higher order one-hot encoding, mono-, di-, tri-nucleotide encoding
binsize = 15000 # sequence windows

## init
DNA_TRAIN = Bioseq.create_from_refgenome(
    'dna',
    refgenome=REFGENOME,
    roi=ROI_TRAIN_FILE,
    binsize=binsize,
    order=order,
    cache=True
)
LABELS_TRAIN = Cover.create_from_bed(
    'peaks', 
    roi=ROI_TRAIN_FILE,
    bedfiles=PEAK_FILE,
    binsize=binsize,
    resolution=binsize,
    dtype='int',
    cache=True,
    storage='sparse'
)

# check
print('training set:', DNA_TRAIN.shape, LABELS_TRAIN.shape)
```
```{shell}
training set: (104, 15000, 1, 4) (104, 1, 1, 1)
```