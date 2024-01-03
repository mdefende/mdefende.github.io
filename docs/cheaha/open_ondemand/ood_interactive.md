# Interactive Apps

The Interactive Apps dropdown from the toolbar lists applications which can be launched and interacted with directly from the browser. These applications run in [Slurm Jobs](../slurm/introduction.md#batch-job-workflow) on [Cheaha](../index.md). There is an [HPC Desktop](#hpc-desktop) that will allow you access to an operating system desktop, terminal, filesystem, and Cheaha software, all running on Cheaha. There are also several common and popular [Standalone Programs](#standalone-programs) including [Matlab](#matlab), [Jupyter Notebooks](#jupyter-notebook), and [RStudio Server](#rstudio-server). All interactive apps have similar job submission forms, please see [HPC Desktop](#hpc-desktop) for an overview.

There is also a [My Interactive Sessions Page](#my-interactive-sessions) listing the available apps and your current interactive sessions. If you get logged out, disconnected, or lose track of an interactive application (because of a closed tab or computer shutdown) you can reconnect to running applications on this page.

## HPC Desktop

Clicking on the HPC Desktop Interactive App, the following form will appear:

![!Setup for HPC Desktop session.](./images/ood_interactive_hpc_vnc.png)

The form allows you to customize the resources required for your interactive app job. For the HPC Desktop app, you may select the number of hours, [partition](../hardware.md#summary), number of GPUs, number of CPUs, and memory per CPU in gigabytes (GB). Note that the "number of GPUs" field is ignored if a partition is selected that has no GPUs. See our [Hardware Summary](../hardware.md#summary) for more information.

For information about other interactive apps, see the [Standalone Programs section](#standalone-programs).

<!-- markdownlint-disable MD046 -->
!!! tip

    You can decrease wait time in the queue by choosing resources carefully. The closer your request is to actual usage, the more optimal your wait time will be. Please see our section on [Job Efficiency](../job_efficiency.md) for more information.
<!-- markdownlint-enable MD046 -->

Once you've selected the compute resources you need, "Launch" the job. This will bring you to the [My Interactive Sessions page](#my-interactive-sessions).

### Visual Studio Code Remote Tunnel

It is possible to remotely access Cheaha using an [HPC Desktop Job](#hpc-desktop) as a host for Visual Studio Code (VSCode), with the "Remote - Tunnels" extension. You can read more about the extension and process at <https://code.visualstudio.com/docs/remote/tunnels>.

To use this method you will need either a GitHub account or Microsoft account. Microsoft accounts can be obtained using your SSO credentials through Microsoft.

<!-- markdownlint-disable MD046 -->
!!! danger

    Do _NOT_ use the remote tunnel extension if you intend to view or work with [Restricted/PHI Data](https://www.uab.edu/it/home/policies/data-classification/classification-overview) while using VSCode.

    When using a tunnel, all information visible within VSCode is end-to-end encrypted and sent from Cheaha to your local machine through a third-party service (the tunnel). Use of any third party services and encryption for Restricted/PHI Data requires a risk assessment first, on a case-by-case basis.
<!-- markdownlint-enable MD046 -->

<!-- markdownlint-disable MD046 -->
!!! important

    Do not use "Remote - SSH" to access Cheaha, as all processes run on the login node. VSCode Server, and associated processes, running on the login node may be shut down at any time to free login node resources. Instead, please use "Remote - Tunnels" as described below.
<!-- markdownlint-enable MD046 -->

**One-time setup:**

Install the [VSCode CLI](https://code.visualstudio.com/docs/editor/command-line) into your `/home/$USER` directory on Cheaha. Instructions for obtaining the CLI executable are available at <https://code.visualstudio.com/docs/remote/tunnels#_using-the-code-cli>.

To avoid typing `./code` for commands, try placing the untarred output into `~/bin/` and adding that directory to `$PATH` in your `~/.bashrc` before starting a job. Then you will only need to type `code` for commands.

**Each session setup:**

1. Start an [HPC Desktop Job](#hpc-desktop) on Cheaha with the required resources. Note that all VSCode processing, including debuggers, unit testing, Jupyter Notebook server, file access, etc., all happen in this same job context. Adjust resources in relation to your development needs.
2. Within the HPC Desktop Job, open a terminal and run the command `code tunnel` (assumes you've added `code` to `$PATH`).
3. Select whether you would like to login with a Microsoft or GitHub account. You should then see a URL and code.

    ![!Terminal showing VSCode Tunnel CLI with Microsoft Account selected and URL and authentication code.](./images/vscode_tunnel_ms_account.png)

    ![!Terminal showing VSCode Tunnel CLI with GitHub Account selected and URL and authentication code.](./images/vscode_tunnel_gh_account.png)

4. In your local browser, navigate to the URL and enter the code. If you see a `https://vscode.dev/tunnel/...` URL, ignore it. It leads to an online-only instance of VSCode running on a Microsoft Cloud service, not to your local machine.

    ![!Browser crop image showing Microsoft Account device activation panel.](./images/vscode_activate_ms_account.png)

    ![!Browser crop image showing GitHub Account device activation panel.](./images/vscode_activate_gh_account.png)

5. Open VSCode on your local machine and click the `><` button in the lower-left corner of the main VSCode window to popen the Command Palette. Select "Connect to Tunnel..." to find your tunnel.

    ![!VSCode "Connect To..." Button](./images/vscode_tunnel_connect_button.png)

    ![!VSCode Command Palette showing "Connect to Tunnel..." highlighted by mouse pointer.](./images/vscode_tunnel_palette_connect.png)

6. Select the same login method as in step (3). You may be asked to login locally.

    ![!VSCode Command Palette showing tunnel account selection.](./images/vscode_tunnel_palette_login.png)

7. Select your tunnel from the list.

    ![!VSCode Command Palette showing tunnel selection with a tunnel highlighted.](./images/vscode_tunnel_palette_tunnel_selection.png)

After the previous step, you should be connected to your tunnel. Now your local VSCode window is acting as a front-end for processing and file access occuring on Cheaha.

## Standalone Programs

Some software can be run standalone, and does not require an [HPC Desktop](#hpc-desktop). Setup for most of these follow the same rules as creation of an HPC Desktop job in terms of requesting resources. You will also need to select the version of software to use for the job.

<!-- markdownlint-disable MD046 -->
!!! note

    Versions shown in the OOD form may not line up with versions available in modules. If you need a version not available in OOD, please feel free to [send in a ticket](../../index.md#contact-us).
<!-- markdownlint-enable MD046 -->

### Jupyter Notebook

Jupyter Notebooks are available for use graphically in your browser via OOD. As with other standalone programs, you'll need to select the resources required using the job creation form. The form is shown below.

![!Jupyter Notebook job request form.](./images/ood_jupyter_notebook_form.png)

Jupyter Notebooks are commonly used with Anaconda environments. If you are unfamiliar with Anaconda environments please see the [Working with Anaconda Environments section](#working-with-anaconda-environments) below before continuing here.

To modify the Operating System (OS) environment that Anaconda and Jupyter will run in, please use the Environment Setup field to load modules. For GPU applications you'll need to load a `CUDA/*` module. If working with deep learning workflows, you will also possibly need to load the `cuDNN/*-CUDA-*` module corresponding to your choice of `CUDA/*` module version. These are required for popular ML/DL/AI libraries like TensorFlow, Keras, and PyTorch. Use `module spider cuda` and `module spider cudnn` to view the list of appropriate modules. An example of what to put in the Environment Setup field, when using Tensorflow in a Jupyter notebook, is shown below.

```shell
# ENVIRONMENT SETUP
module load CUDA/12.2.0
module load cuDNN/8.9.2.26-CUDA-12.2.0
```

For information on which versions of CUDA to load for Tensorflow and PyTorch, please see [Tensorflow Compatibility](../slurm/gpu.md#tensorflow-compatibility) and [PyTorch Compatibility](../slurm/gpu.md#pytorch-compatibility).

<!-- markdownlint-disable MD046 -->
!!! note

    If you get a Failed to Connect message when opening the job, close the tab and wait a couple of minutes. Jupyter is still initializing and takes some time after the job first begins running.
<!-- markdownlint-enable MD046 -->

<!-- markdownlint-disable MD046 -->
!!! important

    If you are not able to see your environment, you may need to install the `ipykernel` package. It is required for Jupyter to recognize your environment. See [Packages for Jupyter](../../workflow_solutions/using_anaconda.md#packages-for-jupyter) for more information.
<!-- markdownlint-enable MD046 -->

<!-- markdownlint-disable MD046 -->
!!! important

    Do not load `module load Anaconda3` in the `Environment Setup` field, as it is loaded automatically. Loading any versions of `Anaconda3` would affect the Python executable, which is used by default. These results in hard-to-diagnose errors in the OOD Jupyter notebook.
<!-- markdownlint-enable MD046 -->

<!-- markdownlint-disable MD046 -->
!!! warning

    Having `conda/mamba activate` and `source activate` statements in the `Environment Setup` field can cause unexpected and silent job failure. Avoid using `conda activate` in the `Environment Setup` field.
<!-- markdownlint-enable MD046 -->

### Working with Anaconda Environments

For information on working with Anaconda environments please see our [Using Anaconda page](../../workflow_solutions/using_anaconda.md). The please review our [Cheaha-specific Anaconda page](../software/software.md#anaconda-on-cheaha) for important tips and how to avoid common pitfalls.

#### Extra Jupyter Arguments

The `Extra Jupyter Arguments` field allows you to pass additional arguments to the Jupyter Server as it is being started. It can be helpful to point the server to the folder containing your notebook. To do this, assuming your notebooks are stored in `/data/user/$USER`, also known as `$USER_DATA`, put `--notebook-dir=$USER_DATA` in this field. You will be able to navigate to the notebook if it is in a subdirectory of `notebook-dir`, but you won't be able to navigate to any other directories. An example is shown below.

![!Jupyter Notebook job request form Extra jupyter arguments field.](./images/ood_jupyter_notebook_extra_args_box.png)

#### Submitting the Jupyter Notebook Job

Submitting the job will bring you to the `My Interactive Jobs` window while the Jupyter job is initialized. Click `Connect to Jupyter` to open the Jupyter Home Page.

<!-- markdownlint-disable MD046 -->
!!! note

    If you get a Failed to Connect message when opening the job, close the tab and wait a couple of minutes. Jupyter is still initializing and takes some time after the job first begins running.
<!-- markdownlint-enable MD046 -->

#### The Jupyter Server Home Page

The Jupyter Server Home Page will look like the following

![!Home page for jupyter notebooks.](./images/ood_jupyter_notebook_home.png)

From here, you can navigate to and select an existing notebook, or you can create a new one using one of your existing virtual environments or the base environment. Once inside a Jupyter notebook, you can use the `Kernel --> Change kernel` menu to select your preferred Anaconda environment.

<!-- markdownlint-disable MD046 -->
!!! important

    See [Anaconda Environments](../../workflow_solutions/using_anaconda.md#packages-for-jupyter) for information on Jupyter related packages.
<!-- markdownlint-enable MD046 -->

<!-- markdownlint-disable MD046 -->
!!! warning

    Files deleted within using the Jupyter server interface are not truly deleted. Instead they are moved to `$HOME/.local/share/Trash`. This may cause inflation of personal storage usage on Cheaha.
<!-- markdownlint-enable MD046 -->

#### Python Libraries and Virtual Environments

To run Jupyter with specific libraries and packages outside of the base install, you will need to create a virtual environment first. You can do this either in an HPC Desktop job or in the `Conda` tab of the Jupyter homepage.

The `Conda` has the following layout:

![!Creating and managing environments in Jupyter.](./images/ood_jupyter_notebook_create_conda_env.png)

1. Current environments (red): a listing of the current existing environments in your `$HOME/.conda/envs` folder.
2. Available packages (green): a list of all packages available to install from conda sources.
3. Installed packages (blue): a list of the packages installed in the currently selected environment.

To create a new environment, click the `+` button at the top of the `Current environments` pane and enter the name of the environment. After it has been created, you can select packages to install by searching for the package name at the top right of the `Available packages` pane. After selecting the package, click the `->` button, and the package and all its dependencies will be installed.

<!-- markdownlint-disable MD046 -->
!!! note

    If a package is not available using the `conda` command directly, it will not be listed as an available package. Use a terminal window to install the package as necessary.
<!-- markdownlint-enable MD046 -->

<!-- markdownlint-disable MD046 -->
!!! note

    In order to use an environment with Jupyter, the `ipykernel` library is necessary. Creating an environment in the Conda tab will autoinstall this library. If using the terminal, use `conda install ipykernel` to install it.
<!-- markdownlint-enable MD046 -->

After successfully creating your environment, navigate to the Files tab. You can create a new notebook using the `New` dropdown menu in the top right. Select your virtual environment of choice, and a notebook will be created and opened.

#### Help GPU is not Available with TensorFlow or PyTorch

If you are using Jupyter with TensorFlow or PyTorch and no GPU is found, please see our Slurm GPU page sections on [TensorFlow Compatibility](../slurm/gpu.md#tensorflow-compatibility) and [PyTorch Compatibility](../slurm/gpu.md#pytorch-compatibility). For MATLAB, please see [MATLAB Compatibility](../slurm/gpu.md#matlab).

### RStudio Server

RStudio is available for use graphically in your browser via OOD. As with other standalone programs, you'll need to select the resources required using the job creation form. You'll also need to select both the version of RStudio you wish to use, and the version of R you wish to use. To adjust the environment, please use the Environment Setup field to load modules besides R and RStudio as seen below. All other modules and paths should be loaded here as it is difficult to load and consistently use modules once RStudio starts.

![!RStudio Server job request form Environment Setup field.](./images/ood_rstudio_server_env_setup_box.png)

<!-- markdownlint-disable MD046 -->
!!! important

    Unless an older version of R is absolutely necessary, it is highly suggested to always use the newest version of R and RStudio for both updated functionality within those software as well as updated compilers for package installation. Using the newest version of R solves most known package installation errors.
<!-- markdownlint-enable MD046 -->

#### RStudio and Python

If you have a workflow that uses both R and Python, it is strongly recommended to use the [reticulate](https://rstudio.github.io/reticulate/) package along with Anaconda environments. Reticulate allows researchers to load Python packages into a native R session as objects. For instance, if someone prefer some functionality of the `pandas` package but has other code already written in R, they can import `pandas` to R and use both simultaneously.

This also allows researchers to download precompiled command line binaries into an Anaconda environment and easliy use them in their R scripts.

For setup, use the following steps:

1. In a terminal on a compute node, either in an HPC Desktop job or by clicking the blue Host button on any job card:

    1. Load the `Anaconda3` module
    2. Create an Anaconda environment. More information about how to create Anaconda environments can be found [in our documentation](../../workflow_solutions/using_anaconda.md).
    3. Activate your environment and install your requuired python packages using either `pip install` or `conda install` depending on the package source.

    <!-- markdownlint-disable MD046 -->
    !!! note

        The preceding steps should only need to be run once. If other Python packages need to be installed in the same environment, repeat steps 1 and 3. You will not need to recreate your environment.
    <!-- markdownlint-enable MD046 -->

2. In RStudio:

    1. Add the command `module load Anaconda3` to the Environment Setup window when requesting the RStudio job.
    2. If not already installed, install the `reticulate` package using either `install.packages` or the [renv](#rstudio-projects-and-renv) package.
    3. Use `reticulate::use_condaenv('env_name')` to load your conda environment.
    4. From here, you will be able to interact with all of the python packages and non-python precompiled binaries in your Anaconda environment using R and RStudio. Please read more about how to do that in [reticulate's documentation](https://rstudio.github.io/reticulate/#importing-python-modules).

For cases where your R code only needs access to precompiled binaries or libraries and does not need to import any Python libraries, you can instead create your Anaconda environment and add the following lines into the Environment Setup window:

``` bash
module load Anaconda3
conda activate <env_name>
```

This will add those binaries and libraries to your environment `$PATH` which RStudio will inherit.

<!-- markdownlint-disable MD046 -->
!!! important

    If you're wanting to directly use any Python package in R, **DO NOT** include the `conda activate` command in the Environment Setup. Use `reticulate` instead as described above.
<!-- markdownlint-enable MD046 -->

#### RStudio Projects and renv

The most recent versions of RStudio installed on Cheaha support R Projects as well as package management through the `renv` package. Please read more about improving analysis reproducibility using both of these tools in our [workflow solutions](../../workflow_solutions/r_environments.md)

#### Using Pandoc and `knitr` within RStudio

If you want to use RMarkdown to create reports in RStudio, R modules using version 4.2.0 and later include `knitr` compatibility. Please use the latest versions of both R and Rstudio for fully integrated `knitr` functionality.

#### Starting With a Clean Session to Avoid Errors

By default, RStudio loads the most recently opened project at startup and restores the `.RData` file into the workspace. If you only work on a single project, this may be helpful. If you frequently change projects then these default settings can create difficult-to-diagnose errors, or you may inadvertently alter a project by adding incorrect packages, for example.

To reduce the risk of these kinds of errors, uncheck the highlighted fields below in the RStudio Options menu under the "General" selection.

- Restore most recently opened project at startup
- Restore .RData into workspace at startup

![!image showing fields to uncheck highlighted with red markers](images/ood_rstudio_server_clean_session.png)

### Matlab

Matlab is available for use graphically in your browser via OOD. As with other standalone programs, you'll need to select the resources required using the job creation form. The form is shown below.

![!Matlab job request form.](./images/ood_matlab_form.png)

<!-- markdownlint-disable MD046 -->
!!! warning

Matlab tends to consume substantial memory at startup. You may experience difficulty with job errors below 20 GB of total memory.
<!-- markdownlint-enable MD046 -->

#### Using Anaconda Python from within Matlab

Matlab has the ability to interoperate with Python from within Matlab. The official documentation for this featuer may be found at <https://www.mathworks.com/help/matlab/call-python-libraries.html>.

This section is dedicated to using this feature with Anaconda on Cheaha. To use Python contained in an Anaconda Environment within Matlab, please use the following steps.

1. Create an [HPC Interactive Desktop Job](#interactive-apps) using [Open OnDemand](ood_main.md).
2. Open a terminal in that job. The following steps should all be run in this terminal unless otherwise specified.
3. [Load the Anaconda Module](../software/software.md#anaconda-on-cheaha).
4. [Create an Environment](../../workflow_solutions/using_anaconda.md#using-anaconda) in Anaconda with the packages needed.
5. [Activate the Environment](../../workflow_solutions/using_anaconda.md#activate-an-environment),
6. Load the Matlab [Module](../software/modules.md).
7. Start Matlab by entering the command `matlab`.
8. Verify success by entering `pyenv` at the Matlab prompt (not the terminal window). Multiple lines of text will be returned at the prompt. Among them you should see a line like the following, with your environment name in place of `<env_name>`.

    ```text
    Executable: /home/$USER/.conda/envs/<env_name>/bin/python
    ```

You may optionally verify that Python works correctly by entering `py.list(["hello", "world"])`. A python list object should appear in the workspace.

#### Using a GPU with MATLAB

Please see the [MATLAB Section on our GPU Page](../slurm/gpu.md#matlab).

## My Interactive Sessions

The My Interactive Sessions page looks like:

![!List of interactive sessions shown as job cards.](./images/ood_interactive_sessions.png)

For each job running via Open OnDemand, there will be a card listed on this page. Each card has basic information about the number of cores, nodes, and time remaining for the job. Also shown are the Job ID in the top-left part of the job card, and a Session ID link near the center of the card. The Job ID and Session ID are important for diagnosing issues you may encounter on Cheaha while using Open OnDemand. The Session ID link points to a folder in your home directory with information we may ask for.

Click the `Launch Desktop in new tab` button to open your interactive VNC session. You may have to wait for the card to change from blue to green to launch the job.

<!-- markdownlint-disable MD046 -->
!!! bug

    If your job fails to launch, please see our [FAQ](../../help/faq.md) for possible solutions, or [contact us](../../index.md#contact-us).
<!-- markdownlint-enable MD046 -->

<!-- markdownlint-disable MD046 -->
!!! note

    For HPC Desktop, you do not need to request resources after you open the Desktop. You are already on a compute node. Any tasks you run will use the resources you requested when initializing the job.
<!-- markdownlint-enable MD046 -->

<!-- markdownlint-disable MD046 -->
!!! note

    You can request another interactive session in a terminal in HPC Desktop. Only the terminal you requested the other interactive session in will have access to the new resources. Everything else in the HPC Desktop will run with the resources you requested when creating the initial job.
<!-- markdownlint-enable MD046 -->

These interactive jobs can be stopped early by clicking `Delete` on the right side of the job card.
