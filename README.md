cp -rp /scratch/work/public/overlay-fs-ext3/overlay-50G-10M.ext3.gz .

//srun --c16 -t1:00:00 --mem=64GB --pty /bin/bash

srun --time=4:00:00 --cpus-per-task=6 --mem=64GB --gres=gpu:1 --pty /bin/bash

gunzip overlay-50G-10M.ext3.gz

//singularity exec --nv --overlay overlay-50G-10M.ext3:rw /scratch/work/public/singularity/cuda12.6.3-cudnn9.5.1-ubuntu22.04.5.sif /bin/bash

try this instead:



singularity exec --nv --overlay overlay-50G-10M.ext3:rw /scratch/work/public/singularity/cuda10.1-cudnn7-devel-ubuntu18.04.sif /bin/bash



//bash /share/apps/utils/singularity-conda/setup-conda.bash

create this script setup-conda.bash instead:

```
#!/bin/bash

wget  --no-check-certificate https://repo.anaconda.com/miniconda/Miniconda3-py311_25.1.1-2-Linux-x86_64.sh

bash Miniconda3-py311_25.1.1-2-Linux-x86_64.sh -b -p /ext3/miniconda3

cat<<EOF | tee /ext3/env.sh
#!/bin/bash

source /ext3/miniconda3/etc/profile.d/conda.sh
export PATH=/ext3/miniconda3/bin:\${PATH}
export PYTHONPATH=/ext3/miniconda3/bin:\${PATH}

EOF

source /ext3/env.sh

conda update -n base conda --yes

conda install pip --yes

conda clean --all --yes

conda install ipykernel --yes

```

bash setup-conda.bash


conda create -p /ext3/py3.11 python=3.11 -y

conda activate py3.11 // or whatever the printout says to activate

source /ext3/env.sh

export PATH=/ext3/py3.11/bin:$PATH

pip install --upgrade pip

pip install tensorflow==2.15

git clone https://github.com/kr-colab/ReLERNN.git

cd ReLERNN

pip install .

//https://repo.anaconda.com/miniconda/Miniconda3-py311_25.1.1-2-Linux-x86_64.sh


add this to your ~/.bash_profile

echo 'export PATH=/ext3/py3.11/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
