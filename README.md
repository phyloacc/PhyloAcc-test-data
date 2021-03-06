# PhyloAcc test data

This repository contains a small test dataset to run with PhyloAcc after [installing](https://phyloacc.github.io/).

The test dataset is based on the ratite dataset from [Hu et al. 2019](https://doi.org/10.1093/molbev/msz049) and is composed of 500 simulated genomic elements each 200bp long with varying rates.

To use this test repo, first clone it by clicking the green Code button above and copying your preferred link. Then, in your shell clone the repo:

```bash
git clone [copied URL from green Code button]
```

<details><summary>Click here to see a breakdown the above command</summary>
<p>

| Command line parameter              | Description |
|-------------------------------------|-------------|
| git                                 | This calls the main git program from the command line  |
| clone                               | The sub-program of git used to copy repositories from the web to your local machine |
| [copied URL from green Code button] | The URL of the repository you wish to copy |

</p>
</details>
</br>

Then enter that directory:

```bash
cd PhyloAcc-test-data
```

<details><summary>Click here to see a breakdown the above command</summary>
<p>

| Command line parameter  | Description |
|-------------------------|-------------|
| cd                      | The Linux change directory command  |
| PhyloAcc-test-data      | The path to the directory you want to enter |

</p>
</details>
</br>

## Test commands

There are two ways you can run PhyloAcc, either through the interface with `phyloacc.py [options]` or directly with `PhyloAcc-ST [config file]`. We strongly recommond using the interface to take care of batching with snakemake which will greatly increase the efficiency of running on large datasets.

### To test batching with the interface

First, run the interface to generate batches and snakemake files:

```bash
phyloacc.py -a simu_500_200_diffr_2-1.fa -b simu_500_200_diffr_2-1.bed -i id-subset.txt -m ratite.mod -o phyloacc-test -t "strCam;rhePen;rheAme;casCas;droNov;aptRow;aptHaa;aptOwe;anoDid" -g "allMis;allSin;croPor;gavGan;chrPic;cheMyd;anoCar" -n 4 -batch 5 -j 2 -part "[a valid partition on your cluster]"
```

<details><summary>Click here to see a breakdown the above command</summary>
<p>
</br>

| Command line parameter                       | Description |
|----------------------------|-------------|
| phyloacc.py | This tells the shell to run the phyloacc Python interface -- use `phyloacc.py -h` for a full list of options |
| -a simu_500_200_diffr_2-1.fa | This option specifies the path to a concatenated alignment file in FASTA format |
| -b simu_500_200_diffr_2-1.bed | This option specifies the path to a bed file that contains partitions for each element in the alignment file |
| -i id-subset.txt | This option specifies the path to a text file containing one element ID per line to subset the dataset -- PhyloAcc will only be run on these elements |
| -m ratite.mod | This option specifies the path to the MOD file from PHAST, which contains pre-estimated neutral substitution rates and a species tree |
| -o phyloacc-test | This option specifies the path to the desired output directory, which will be created if it does not exist |
| -t "strCam;rhePen;rheAme;casCas;droNov;aptRow;aptHaa;aptOwe;anoDid" | A semi-colon separated list of target species |
| -g "allMis;allSin;croPor;gavGan;chrPic;cheMyd;anoCar" | A semi-colon separated list of outgroup species |
| -n 4 | This is the number of processes that `phyloacc.py` will use to generate the batches |
| -batch 5 | This is the number of loci per batch |
| -j 2 | This is the number of batches to submit to your cluster simultaneously |
| -part "[a valid partition on your cluster]" | This should be a partition on your cluster to which you wish to submit jobs |

</p>
</details>
</br>

Next, run the resulting snakemake command that is printed to the screen. This will be different for every user, but should look something like this:

```bash
snakemake -p -s [/your/path/to]/PhyloAcc-test-data/phyloacc-test/phyloacc-job-files/snakemake/run_phyloacc.smk --configfile [/your/path/to]/PhyloAcc-test-data/phyloacc-test/phyloacc-job-files/snakemake/phyloacc-config.yaml --profile [/your/path/to]/PhyloAcc-test-data/phyloacc-test/phyloacc-job-files/snakemake/profiles/slurm_profile --dryrun
```

<details><summary>Click here to see a breakdown the above command</summary>
<p>

| Command line parameter  | Description |
|-------------------------|-------------|
| snakemake               | A program that handles workflows and cluster job submission; this should be installed automatically when you install PhyloAcc from bioconda  |
| -p                      | This option tells snakemake to print the shell commands that are executed |
| -s [/your/path/to]/PhyloAcc-test-data/phyloacc-test/phyloacc-job-files/snakemake/run_phyloacc.smk | This option specifies the path to the snakemake file; this is written automatically by `phyloacc.py` |
| --configfile [/your/path/to]/PhyloAcc-test-data/phyloacc-test/phyloacc-job-files/snakemake/phyloacc-config.yaml | The path to the config file for a given workflow; this is written automatically by `phyloacc.py` |
| --profile [/your/path/to]/PhyloAcc-test-data/phyloacc-test/phyloacc-job-files/snakemake/profiles/slurm_profile | The path to a directory containing a cluster profile so snakemake knows how to submit jobs to your cluster; this is written automatically by `phyloacc.py` |
| --dryrun                | The --dryrun option tells snakemake to report the commands it will execute without actually executing them |

</p>
</details>
</br>

Once there are no errors and you are satisfied this will run the batches you want, remove the `--dryrun` option to run the batches with Snakemake. Since this is a very small dataset, it should only take a couple of minutes, but for large datasets you would want to run this in the background or submit the snakemake command as its own job.

Finally, after the batches have completed, you will want to gather the outputs with the post-processing script:

```bash
phyloacc_post.py -i phyloacc-test
```

<details><summary>Click here to see a breakdown the above command</summary>
<p>

| Command line parameter  | Description |
|-------------------------|-------------|
| phyloacc_post.py        | The post-processing script after all batches have been run |
| -i phyloacc-test       | The path to the directory that contains the PhyloAcc job files, which is the output directory (`-o`) specified when running `phyloacc.py` |

</p>
</details>
</br>

### To test PhyloAcc directly

If you wish to test the PhyloAcc-ST binary directly, run the following command:

```bash
PhyloAcc-ST phyloacc-test-config.txt
```

<details><summary>Click here to see a breakdown the above command</summary>
<p>

| Command line parameter    | Description |
|---------------------------|-------------|
| PhyloAcc-ST               | The path to the PhyloAcc binary/executable |
| phyloacc-test-config.txt  | The path to the PhyloAcc config file |

</p>
</details>
</br>

This will take a little longer since it doesn't do batching, but should still finish within a few minutes.

## Files in this repository

| File                       | Description |
|----------------------------|-------------|
| id-subset.txt              | A subset of the 500 simulated elements to run, one element ID per line     |
| phyloacc-test-config.txt   | A PhyloAcc config file as input to the PhyloAcc-ST binary  |
| ratite.mod                 | The neutral rate and tree file from PHAST for the ratite data |
| README.md                  | This file!  |
| simu_500_200_diffr_2-1.bed | A bed file that contains the partitions of the 500 simulated elements in the alignment file |
| simu_500_200_diffr_2-1.fa  | Concatenated alignments for all 500 simulated elements in FASTA format |

