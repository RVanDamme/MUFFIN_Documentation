## Troubleshooting
* If metawrap installation fail while using conda. A known fix is to install metawrap in a conda environment and put the path in "modules/metawrap_refine_bin.nf"
  To do so run the following command:
  
```sh

#create an env and install metawrap
conda create -y -p /path/to/install/metawrap-env python=2.7
conda activate /path/to/install/metawrap-env
conda config --add channels defaults
conda config --add channels conda-forge
conda config --add channels bioconda
conda config --add channels ursky
conda install -y -c ursky metawrap-mg
conda deactivate

#edit MAFIN/modules/metawrap_refine_bin.nf to use the env of metawrap
#you need to change the line 3 and 25 to the path of your env (/path/to/install/metawrap-env)
```


* If you run the pipeline with google life sciences and get error code 14
  It means the process was killed by google, you just need to run the pipeline again don't forget to add "-resume"

* If either Metawrap or checkm have the following error
  You need to increase the RAM in the command for local_engine and conda or in the "configs/containers.config"
```
IOError: [Errno 2] No such file or directory: 'binsA.checkm/storage/tree/concatenated.tre' 
``` 

* For other issue please open a ticket on ![github](https://github.com/RVanDamme/MUFFIN/issues)
