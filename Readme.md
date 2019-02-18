# fpa Filter Pairwise Alignment

[![Build Status](https://travis-ci.org/natir/fpa.svg?branch=master)](https://travis-ci.org/natir/fpa)

Filter output of all-against-all read mapping, you filter or select:

- internal match
- containment
- dovetails
- self matcing
- read name match against regex

All this filter can be revert.

For internal match, containment, dovetails definition go read [algorithm 5 in minimap article](https://academic.oup.com/bioinformatics/article/32/14/2103/1742895/Minimap-and-miniasm-fast-mapping-and-de-novo)

## Usage

```
fpa <option> <input> <output> <subcommand>
```

By default input and output are stdin and stdout so you can use like this:

```
minimap2 long_read.fasta long_read.fasta | fpa -d | gzip - > long_read_dovetail.paf.gz
minimap2 long_read.fasta long_read.fasta | fpa -l 500 -L 2000 > match_between_500_2000.paf
minimap2 long_read.fasta long_read.fasta | fpa -s -m read_1 > no_self_match_no_read_1.paf
minimap2 long_read.fasta long_read.fasta | fpa -s -r rename.csv > no_self_match_renamed_read_1.paf
minimap2 long_read.fasta long_read.fasta | fpa -s -r rename.csv -o gfa1 > no_self_match_renamed_read_1.gfa
minimap2 long_read.fasta long_read.fasta | fpa -l 500 index -f match_upper_500_query_index.paf.idx -t query  > match_upper_500.paf
minimap2 long_read.fasta long_read.fasta | fpa -l 500 - match_upper_500.paf index -f match_upper_500_query_index.paf.idx -t query
```

### Rename option

The renaming option replaces the name of the read with another one.

If the path passed in parameter exists, the file will be read as a two-column csv, the first column is the original name and the second corresponds to the new name:
```
original name1, new name1
original name2, new name2
```

If the name of the read does not exist in the file it will not be replaced.

If the path passed as parameter does not exist, the names will automatically be replaced by a number a file like above example will be created.

### Index option

fpa can build an index of offset of the records in the file where a reads appears. 

The index file looks like this:
```
read_id1, start_of_range_1:end_of_range_1; start_of_range_2:end_of_range_2;…
read_id2, start_of_range_1:end_of_range_1; start_of_range_2:end_of_range_2;…
```

fpa can index read only when it's query (first read in record) or target (second read in record) or both of them.

### Output mode

You can get an output in gfa format with the -o option (--output-mode), two modes are available:
- basic: the output and in the format that the input
- gfa1: the output is in gfa1 format

In gfa1 mode options -C and -I indicate that containments and internalmatches are included in the gfa (default option enabled), -c and -i indicate that containments and internalmatches should not be included.

## Rationale

Long Read mapping tools provides all match they found in read dataset, for many usage some of match aren't usfull, this programme provide some filter to remove it. 
This soft can be replace by a simple awk, bash, python, ~perl~, {your favorite language}.

## Requirements

- [Rust](https://www.rust-lang.org/) >= 1.32
- libgz
- libbzip2
- liblzma

## Instalation

### With cargo

If you have a rust environment setup you can run :

```
cargo install fpa_lr
```

### With conda

fpa is avaible in [bioconda channel](https://bioconda.github.io/)

if bioconda channel is setup you can run :

```
conda install fpa
```

### From source

```
git clone https://github.com/natir/fpa.git
cd fpa
git checkout v0.3

cargo build
cargo test
cargo install
```

