# tolsra
This code is based off of q2sra (https://github.com/noahschulhof/q2sra/tree/main) and slightly modifies the pipeline to better suit the needs of the Knight Lab.


## Installation
```bash
$ pip install q2sra
```


## Prerequisites
- [`Python`](https://www.python.org/downloads/release/python-3116/) v3.11.6+
- [`QIIME 2`](https://qiime2.org/) v2023.7+
- [`SRA Toolkit`](https://hpc.nih.gov/apps/sratoolkit.html) v3.0.0+


### Installing `QIIME 2` with Conda

```bash
$ wget https://data.qiime2.org/distro/core/qiime2-2023.7-py38-linux-conda.yml
$ conda env create \
    -n qiime2-2023.7 \
    --file qiime2-2023.7-py38-linux-conda.yml
$ rm qiime2-2023.7-py38-linux-conda.yml
```


### Installing `SRA Toolkit`
Instructions can be found [here](https://github.com/ncbi/sra-tools/wiki/01.-Downloading-SRA-Toolkit).


## Initializing a `q2sra` Project
To create a project, initialize a `q2sra.Proj` object, supplying the intended project name as the sole parameter.

```python
>>> from q2sra import Proj
>>> proj = Proj('my_proj')
```

### `q2sra` Project Attributes
| Attribute   | Type         | Default         | Description            |
|-------------|--------------|-----------------|------------------------|
| `name`      | `str`        | `None`          | Project name           |
| `fields`    | `list[str]`  | `None`          | Metadata fields        |
| `nsamples`  | `int`        | `30`            | Maximum number of samples from each study |s
| `paired`    | `bool`       | `True`          | Whether to use forward **and** reverse reads (`True`) or exclusively forward reads (`False`)|

## Adding Metadata Fields
```python
q2sra.Proj.add_field(field: str, required: bool) -> None
```
### Arguments
- `field` - Name of field
- `required` - Whether the field is required [default = `False`]

### Example Run
```python
>>> proj.add_field('Phylum')
>>> proj.add_field('Country', required = True)
```


## Adding Studies to a Project
```python
q2sra.Proj.run(study_name: str, accession: str, include: list[str], exclude: list[str]) -> None
```

### Arguments
- `study_name` - Name of study
- `accession` - Study accession code in the [NCBI SRA database](https://www.ncbi.nlm.nih.gov/sra)
- `include` - List of substrings that **must** be included when filtering `.fastq` files [default = `[]`]
- `exclude` - List of substrings that **must** be excluded when filtering `.fastq` files [default = `[]`]

### Example Run (w/ user input)
```python
>>> proj.run('takagi_2022', 'PRJNA809527')
Phylum: Chordata
[Required] Country: Japan
```


## Saving a Project
```python
>>> proj.save()
```

### Output
`<proj name>.pkl` - a [`pickle`](https://docs.python.org/3/library/pickle.html) file storing the project's attributes.


## Loading a Pre-configured Project
Any existing `q2sra` project saved in `.pkl` format (see previous step) can later be loaded to perform additional actions (adding more studies, merging runs, etc.).

```python
q2sra.Proj.load(name: str) -> None
```

### Arguments
- `name` - Name of project to load

### Example Run
```python
>>> proj = Proj.load('my_proj')
```


## Aggregating Studies
After compiling a satisfactory number of studies, individual metadata files and `QIIME 2` feature tables/representative sequences can be merged for further analysis using the following method:

```python
>>> proj.merge()
```
