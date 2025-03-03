# Interactive Parallel Python on Kestrel with Jupyter

For a general introduction to using Jupyter notebooks on Kestrel, please refer to the [official documentation](https://nrel.github.io/HPC/Documentation/Development/Jupyter/). For quick low compute intensive jobs, please visit the [Kestrel JupyterHub](https://kestrel-jhub.hpc.nrel.gov/). This page conveys specific information on how to leverage parallel computing on NREL HPC systems with python through Jupyter notebooks. Accompanying notebooks to test if the environment and the allocation has been configured correctly are in the 'exampleNotebooks' folder.

## Setting up your account

Login to Kestrel
```
$ ssh -X <username>@kestrel-gpu.hpc.nrel.gov
```

Navigate to you project directory
```
$ cd /projects/<projectname>/<username>/
```

Load nvidia hpc programming environment
```
$ module load PrgEnv-nvhpc/8.5.0
```

Check available conda modules and load one
```
$ module avail conda
$ module load anaconda3/2024.06.1
```

Create a new environment named ‘myEnv’ in the current directory
```
$ conda create --prefix ./myEnv
```

Activate your new environment
```
$ conda activate /kfs2/projects/projectname/<username>/myEnv
# (or) From the same directory
$ conda activate ./myEnv
```

Create a jupyter kernel named ‘myEnvJupyter’ from the myEnv environment
```
$ python -m ipykernel install --user --name=myEnvJupyter
```

(Optional) To access your `scratch` directory from your project directory, execute the following in your project directory
```
$ ln -s /scratch/<username>/ scratch
```
The above command will create a symbolic link to the `scratch` folder, which can be navigated to from Jupyter hub to access files in your scratch directory.

## Install packages

CuPy : [“An open-source array library for GPU-accelerated computing with Python”](https://cupy.dev/)
```
$ nvcc –version
$ conda install -c conda-forge cupy
$ conda install -c conda-forge cupy cudnn cutensor nccl
```

numba-cuda : [“CUDA GPU programming by directly compiling a restricted subset of Python code into CUDA kernels”](https://nvidia.github.io/numba-cuda/user/index.html)
```
$ conda install -c conda-forge numba-cuda
$ conda install -c conda-forge cuda-nvcc cuda-nvrtc "cuda-version>=12.0"
```

mpi4py : [“Python bindings for the Message Passing Interface (MPI) standard to exploit multiple processors”](https://mpi4py.readthedocs.io/en/stable/) 
```
$ conda install -c conda-forge mpi4py openmpi
$ conda install cuda-cudart cuda-version=12
$ conda install nccl
```

ipyparallel : [“Interactive Parallel Computing with IPython”](https://ipyparallel.readthedocs.io/en/latest/)
```
$ conda install ipyparallel
```

Dask : [“A flexible open-source Python library for parallel and distributed computing”](https://www.dask.org/)
```
$ conda install dask
$ conda install dask-jobqueue
$ conda install graphviz
$ conda install ipycytoscape
$ conda install matplotlib
```

## Launching jobs

A general guide to allocating resources for running jobs on the Kestrel can be found in the [official documentation](https://nrel.github.io/HPC/Documentation/Systems/Kestrel/Running/). Below are example proceedures suitable for running jobs involving specific python modules, depending on their parallelisation capability. 

The text in red box shows an example of the output parameter `<nodename>` and the text in yellow box shows an example of the output parameter `<alphabet soup>`, relevant to the following tutorial.

<img src="metadata/nodeName.png" alt="<nodename>" width="300"/>

<img src="metadata/alphabetSoup.png" alt="<alphabet soup>" width="600"/>

<!-- ![<alphabet soup>](metadata/alphabetSoup.png "<alphabet soup>") -->

### GPU compatible modules: E.g. CuPy, numba-cuda etc.

1. Kestrel: Launch an interactive job
    ```
    $ salloc -A <projectname> -t 00:15:00 --partition=debug --gres=gpu:2
    $ module load anaconda3/2024.06.1
    $ conda activate ./myEnv
    $ jupyter-lab --no-browser --ip=$(hostname -s)
    ```


2. Local terminal: Establish a SSH tunnel
    ```
    $ ssh -N -L 8888:<nodename>:8888 <username>@kestrel-gpu.hpc.nrel.gov
    ```

3. Web browser
    ```
    http://127.0.0.1:8888/?token=<alphabet soup>
    ```

    File > New > Notebook > myEnvJupyter


### Multi thread capable modules: E.g. Dask

1. Kestrel: Launch a multi thread interactive job
    ```
    $ salloc -A <projectname> -t 00:15:00 --nodes=1 --ntasks-per-node=104 --partition=debug
    $ module load anaconda3/2024.06.1
    $ conda activate ./myEnv
    $ jupyter-lab --no-browser --ip=$(hostname -s)
    ```

2. Local terminal: Establish a SSH tunnel
    ```
    $ ssh -N -L 8888:<nodename>:8888 <username>@kestrel.hpc.nrel.gov
    ```

3. Web browser
    ```
    http://127.0.0.1:8888/?token=<alphabet soup>
    ```

    File > New > Notebook > myEnvJupyter


### Multi node capable job. E.g. mpi4py through ipyparallel 

1. Kestrel: Launch a multi node interactive job
    ```
    $ salloc -A <projectname> -t 00:15:00 --nodes=2 --ntasks-per-node=1 --partition=short
    $ module load anaconda3/2024.06.1
    $ conda activate ./myEnv
    $ jupyter-lab --no-browser --ip=$(hostname -s)
    ```

2. Local terminal: Establish a SSH tunnel
    ```
    $ ssh -N -L 8888:<nodename>:8888 <username>@kestrel.hpc.nrel.gov
    ```

3. Web browser
    ```
    http://127.0.0.1:8888/?token=<alphabet soup>
    ```

    File > New > Notebook > myEnvJupyter


### GPU + multi node jobs. E.g. CuPy + mpi4py through ipyparallel

1. Kestrel: Launch an interactive job
    ```
    $ salloc -A projectname -t 00:15:00 --nodes=2 --ntasks-per-node=1 --gres=gpu:2 
    $ module load anaconda3/2024.06.1
    $ conda activate ./myEnv
    $ jupyter-lab --no-browser --ip=$(hostname -s)
    ```

2. Local terminal: Establish a SSH tunnel
    ```
    $ ssh -N -L 8888:<nodename>:8888 <username>@kestrel-gpu.hpc.nrel.gov
    ```

3. Web browser
    ```
    http://127.0.0.1:8888/?token=<alphabet soup>
    ```

    File > New > Notebook > myEnvJupyter

###

