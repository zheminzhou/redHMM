# redHMM (recombination & diversifying selection based on Hidden Markov Model)
RecHMM + DivHMM, identification of recombination and diversifying selection in bacterial genomes


# INSTALLATION:

redHMM was developed and tested in Python >=3.8. It depends on several Python libraries: 
~~~~~~~~~~
numba
numpy
pandas
~~~~~~~~~~

All libraries can be installed using pip: 

~~~~~~~~~~
pip install numba numpy pandas
~~~~~~~~~~


The whole environment can also be installed in conda:


~~~~~~~~~~
conda create --name redhmm python==3.11
conda activate redhmm
conda install -c conda-forge numba numpy pandas
~~~~~~~~~~

The installation process normally finishes in <10 minutes. 

NOTE: redHMM uses the output of phylo module in EToKi (https://github.com/zheminzhou/EToKi) as the input. 


# Quick Start (with examples)
## predict recombination regions
~~~~~~~~~~~
$ cd /path/to/redHMM/
$ ./RecHMM -d examples/demo.mutations.gz -p examples/demo
~~~~~~~~~~~

This can take over half an hour (There are ~600 genomes). Use -n <number_processes> to (slightly) accelerate the calculation. 


## predict diversifying selections
~~~~~~~~~~~
$ cd /path/to/redHMM/
$ ./DivHMM -d examples/demo.mutations.gz -p examples/demo -r examples/demo.importation.region
~~~~~~~~~~~
This process finishes in 10 mins.  

Alternatively, DivHMM can be run without RecHMM, and the recombinant SNPs will be included in the analysis. 
~~~~~~~~~~~
$ cd /path/to/redHMM/
$ ./DivHMM -d examples/demo.mutations.gz -p examples/demo
~~~~~~~~~~~



# USAGE:
## RecHMM - detect recombination regions

~~~~~~~~~~~~~~
$ ./RecHMM --help
usage: RecHMM [-h] --data DATA [--model MODEL] [--task TASK] [--init INIT] [--prefix PREFIX] [--cool_down COOL_DOWN] [--n_proc N_PROC] [--bootstrap BOOTSTRAP] [--report] [--marginal MARGINAL] [--tree TREE]
              [--clean] [--local_r LOCAL_R] [--local_nu LOCAL_NU] [--local_delta LOCAL_DELTA]

Parameters for RecHMM.

optional arguments:
  -h, --help            show this help message and exit
  --data DATA, -d DATA  A list of mutations generated by EToKi phylo
  --model MODEL, -m MODEL
                        Read a saved best model.
  --task TASK, -t TASK  task to run.
                        0: One rec category from external sources.
                        1: Three rec categories considering internal, external and mixed sources [default].
  --init INIT, -i INIT  Initiate models with guesses of recombinant proportions.
                        Default: 0.05,0.5,0.95
  --prefix PREFIX, -p PREFIX
                        Prefix for all the outputs
  --cool_down COOL_DOWN, -c COOL_DOWN
                        Delete the worst model every N iteration. Default:5
  --n_proc N_PROC, -n N_PROC
                        Number of processes. Default: 5.
  --bootstrap BOOTSTRAP, -b BOOTSTRAP
                        Number of Randomizations for confidence intervals.
                        Default: 1000.
  --report, -r          Only report the model and do not calculate external sketches.
  --marginal MARGINAL, -M MARGINAL
                        Find recombinant regions using marginal likelihood rather than [DEFAULT] maximum likelihood method.
                        [DEFAULT] 0 to use Viterbi algorithm to find most likely path.
                         Otherwise (0, 1) use forward-backward algorithm, and report regions with >= M posterior likelihoods as recombinant sketches.
  --tree TREE, -T TREE  [INPUT, OPTIONAL] A labelled tree. Only used to generate corresponding mutational tree.
  --clean, -v           Do not show intermediate results during the iterations.
  --local_r LOCAL_R, -lr LOCAL_R
                        Specify a comma-delimited list of branches that share a different R/theta (Frequency of rec) ratio than the global consensus. Can be specified multiple times.
                        Use "*" to assign different value for each branch.
  --local_nu LOCAL_NU, -ln LOCAL_NU
                        Specify a comma-delimited list of branches that share a different Nu (SNP density in rec) than the global consensus. Can be specified multiple times.
                        Use "*" to assign different value for each branch.
  --local_delta LOCAL_DELTA, -ld LOCAL_DELTA
                        Specify a comma-delimited list of branches that share a different Delta (Length of rec sketches) than the global consensus. Can be specified multiple times.
                        Use "*" to assign different value for each branch.
~~~~~~~~~~~~~~~~~

## DivHMM - detect regions suffering diversifying selection

~~~~~~~~~~~~~~~~~
$ ./DivHMM --help
usage: DivHMM [-h] --data DATA [--rechmm RECHMM] [--model MODEL] [--task TASK] [--init INIT] [--prefix PREFIX] [--cool_down COOL_DOWN] [--report] [--marginal MARGINAL] [--clean]

Parameters for DivHMM.

optional arguments:
  -h, --help            show this help message and exit
  --data DATA, -d DATA  A list of mutations generated by EToKi phylo
  --rechmm RECHMM, -r RECHMM
                        Imported regions generated by RecHMM [recommend to use]
  --model MODEL, -m MODEL
                        Read a saved best model.
  --task TASK, -t TASK  task to run.
                        0: One mut category.
                        1: Three mut categories including mixed sources [default].
  --init INIT, -i INIT  Initiate models with guesses of proportions of divergent regions.
                        Default: 0.01,0.05,0.1
  --prefix PREFIX, -p PREFIX
                        Prefix for all the outputs.
  --cool_down COOL_DOWN, -c COOL_DOWN
                        Delete the worst model every N iteration. Default:5
  --report, -R          Only report the model and do not calculate external sketches.
  --marginal MARGINAL, -M MARGINAL
                        Find recombinant regions using marginal likelihood rather than [DEFAULT] maximum likelihood method.
                        [DEFAULT] 0 to use Viterbi algorithm to find most likely path.
                         Otherwise (0, 1) use forward-backward algorithm, and report regions with >= M posterior likelihoods as recombinant sketches.
  --clean, -v           Do not show intermediate results during the iterations.
~~~~~~~~~~~~~~~~~



# Outputs:
## RecHMM generates:

### a collection of parameters in the best fitted model
~~~~~~~~~~~~~
<prefix>.best.model.json
~~~~~~~~~~~~~

### a summary report of parameters in the best fitted model
~~~~~~~~~~~~~
<prefix>.best.model.report
~~~~~~~~~~~~~

### imported regions <prefix>.diversified.region
~~~~~~~~~~~~~
$ head examples/demo.recombination.region
#Branch name    mutationRate    recombinationRate       MutationCoverage
#       Importation     seqName start   end     type    score
Branch  EA9197AA        M=2.23560e-04   R=4.55917e-06   B=4516957.000
Branch  N_1157  M=1.77026e-04   R=3.70544e-06   B=4516957.000
Branch  N_228   M=1.70222e-04   R=4.00942e-06   B=4516957.000
Branch  N_1158  M=9.89594e-05   R=2.37799e-06   B=4516957.000
Branch  N_910   M=2.23170e-07   R=4.56467e-07   B=4474559.000
        Importation     N_910   AE017220.1      3563867 3573072 External        1.000
        Importation     N_910   AE017220.1      4082453 4115901 External        1.000
Branch  VA2905AA        M=5.26460e-05   R=1.43670e-06   B=4516957.000
~~~~~~~~~~~~~

Parameters for each Branch are shown in lines start with "Branch". 
* M - mutation rate
* R - recombination rate
* B - size of regions without recombination

Regions involved in recombinations are described in lines with "Importation", following by the branches, coordinates, categories of recombinations, and probability (only useful when using --marginal). 


## DivHMM generates:

### a collection of parameters in the best fitted model
~~~~~~~~~~~~~
<prefix>.div.model.json
~~~~~~~~~~~~~


### a summary report of parameters in the best fitted model
~~~~~~~~~~~~~
<prefix>.div.model.report
~~~~~~~~~~~~~

### diversifying regions <prefix>.diversified.region
~~~~~~~~~~~~~
$ head examples/demo.diversified.region
#Branch name    mutationRate    diversifiedRate MutationCoverage
#       DiversifiedRegion       seqName start   end     type    score
DiversifiedRegion       0       M=3.83551e-03   D=3.29318e-04   B=4512480.000
        DiversifiedRegion       0       AE017220.1      60991   61105   Mixed(D+H)      1.000
        DiversifiedRegion       0       AE017220.1      69499   69593   Diversified     1.000
        DiversifiedRegion       0       AE017220.1      95116   95139   Mixed(D+H)      1.000
        DiversifiedRegion       0       AE017220.1      290765  290784  Mixed(D+H)      1.000
        DiversifiedRegion       0       AE017220.1      345597  345697  Diversified     1.000
        DiversifiedRegion       0       AE017220.1      474894  475966  Homoplastic     1.000
        DiversifiedRegion       0       AE017220.1      679714  680243  Homoplastic     1.000
~~~~~~~~~~~~~

Parameters are shown in the first line.  
* M - mutation rate
* R - frequencies of getting into diversifying regions
* B - size of regions without diversifying selections

Regions involved in diversifying selection are described in lines with "DiversifiedRegion", following by the branches, coordinates, categories of regions, and probability (only useful when using --marginal). 




# Citation and Reproduction Instructions

### Reproduction Instructions
All data required for reproduction of the analysis were distributed in this repository under
https://github.com/zheminzhou/redHMM/examples/demo.mutations.gz



