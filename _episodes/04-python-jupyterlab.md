---
title: "Setting up a Python environment and running JupyterLab on Cirrus compute nodes"
teaching: 15
exercises: 10
questions:
- "How can I personalise my Python environment?"
- "How can I run JupyterLab on the Cirrus GPU nodes?"
objectives:
- "Learning how to install Python packages."
- "Learning how to access JupyterLab locally while ensuring it runs on Cirrus GPU nodes"
keypoints:
- "To be completed later"
---

## Which Python environment should I use?

PyTorch and TensorFlow are both available in the Cirrus modules. You can load PyTorch by running:

{: .language-bash}
```
auser@cirrus-login2:~> module load pytorch/1.13.1-gpu
```
{: .output}

and TensorFlow by running:

{: .language-bash}
```
auser@cirrus-login2:~> module load tensorflow/2.13.0-gpu
```
{: .output}

> ## You'll have to choose one!
> WARNING! You will not be able to have both the PyTorch and TensorFlow
> modules open at the same time! All examples below assume that the
> PyTorch module is being used -- the outputs may differ with the
> TensorFlow module.
{: .callout}

All the packages provided by these modules can be obtained by running `pip list`.

## How can I install my own Python packages?

If the package you require is not listed when running `pip list`, you
can install them using `pip`.

If you have not done this before, you will need to name your virtual environment 
and provide it with a designated folder. Here are instructions for creating an
environment called `myvenv` (please ensure that you change the project ID and 
the username for this to work):

{: .language-bash}
```
auser@cirrus-login2:~> python -m venv --system-site-packages /work/ic084/ic084/auser/myvenv
```
{: .output}

In this example, the environment `myvenv` is created within a folder
within `/work` to ensure that it is accessible from the compute
nodes. The `--system-site-packages` option ensures that this new environment 
is based on the currently loaded `python` module. You only need to do this 
step once -- your setup will stay the same for future login sessions.

Once you have a designated folder for your virtual environment, you must load 
this module to gain access to it. You can do this by running:

{: .language-bash}
```
auser@cirrus-login2:~> extend-venv-activate /work/ic084/ic084/auser/myvenv
auser@cirrus-login2:~> source /work/ic084/ic084/auser/myvenv/bin/activate
```
{: .output}

This will unload the base python module and load your specific
environment. Once you start customising your environment, you will
need to run these commands whenever starting a new session to ensure
that your environment is loaded.

You can now install Python packages by running:

{: .language-bash}
```
(myvenv) auser@cirrus-login2:~> python -m pip install <package name>
```
{: .output}

When you are finished installing packages, you can deactivate your
environment by using the `deactivate` command:

{: .language-bash}
```
(myvenv) auser@cirrus-login2:~> deactivate
auser@cirrus-login2:~>
```
{: .output}

## Using JupyterLab on Cirrus compute nodes

First, you will need to start an interactive session on one of the compute nodes:

{: .language-bash}
```
auser@cirrus-login2:~> srun --gres=gpu:1 --time=00:20:00 --partition=gpu --qos=reservation --reservation=ic084_1263984 --account=ic084 --pty /usr/bin/bash --login
auser@r1i0n14a: ->
```
{: .output}

You will know that you are connected because the node name will
change from `cirrus-loginX` to the node ID (which will look like `r1i0n14a`).
You will need the node ID later -- make a note of it.

Once the interactive session has started, start the JupyterLab
server by running the following (note: you will need to change `<port_number>` to a number of your choice between 5000-65535):

{: .language-bash}
```
auser@r1i0n14a: -> export JUPYTER_RUNTIME_DIR=/scratch/space1/ic084/$USER
auser@r1i0n14a: -> export HOME=/scratch/space1/ic084/$USER
auser@r1i0n14a: -> jupyter lab --ip=0.0.0.0 --no-browser --port=<port_number>
```
{: .output}

The first instruction ensures that the JupyterLab server begins in
the directory you are in. The second instruction resets the path to 
your `HOME` variable -- by default, Jupyter Lab will save new notebooks
in your home directory (which, on Cirrus, is `/home/ic084/ic084/$USER`) but, 
as this is inaccessible from teh compute nodes, we recommend that you reset 
this variable to a path accessible from those nodes (please note that this is 
a hacky workaround!). The third instruction starts the server and provides
it with a port to use for connections.

Once the server is setup, you will get a message saying:

{: .language-bash}
```
...
To access the server, open this file in a browser:
     file:///work/z04/z04/jsindt/jpserver-1839166-open.html
 Or copy and paste one of these URLs:
     http://r1i0n23:<port_number>/lab?token=0ce642adcd0d3b873dbd21a5fb10581c02c5dc5b48dd4ed7
     http://127.0.0.1:<port_number>/lab?token=0ce642adcd0d3b873dbd21a5fb10581c02c5dc5b48dd4ed7
...
```
{: .output}

You will need the URL starting `http:/127.0.0.1:<port_number>/lab/token=<string>` for a future step.

You may instead get a message saying
`channel_setup_fwd_listener_tcpip: cannot listen to port:<port_number>` -- this indicates that the port you have picked
is already being used -- please retry this step with a different
port number.

### Accessing the JupyterLab server from a Mac or Linux machine

On your laptop, open a new terminal window and run the following
command:

{: .language-bash}
```
ssh <username>@login.cirrus.ac.uk -L <port_number>:<node_id>:<port_number>
```
{: .output}

where `<username>` is your Cirrus username, `<port_number>` is the
port number that you used for the licence server, and `<node_id>` is
the ID of the node you are using. This will open a connection that
is "listening" for the JupyterLab server running on the login node.

Once the connection is established, you can access and run
JupyterLab by copying the `http:/127.0.0.1:<port_number>/lab/token=<string>` URl into a web browser.

### Accessing the JupyterLab server from MobaXTerm on a Windows machine

For this, you will need to configure an SSH tunnel on MobaXTerm:

  1. Click on the `Tunnelling` button above the MobaXterm terminal. Create a new tunnel by clicking on `New SSH tunnel` in the window that opens.

  2. In the new window that opens, make sure the `Local port forwarding` radio button is selected.

  3. In the `forwarded port` text box on the left under `My computer with MobaXterm`, enter the `<port_number>` that you chose when setting up the JupyterLab server.

  4. In the three text boxes on the bottom right under `SSH server` enter `login.cirrus.ac.uk`, your Cirrus username, and then `22`.

  5. At the top right, under `Remote server`, enter the Cirrus compute node ID that you noted earlier followed by the `<port_number>`.

  6. Click on the `Save` button.

  7. In the tunnelling window, you will now see a new row for the settings you just entered. If you like, you can give a name to the tunnel in the leftmost column to identify it. Click on the small key icon close to the right for the new connection to tell MobaXterm which SSH private key to use when connecting to Cirrus. You should tell it to use the same `.ppk` private key that you normally use.

  8. The tunnel should now be configured. Click on the small start button (like a play `>` icon) for the new tunnel to open it. You'll be asked to enter your Cirrus password or TOTP -- please do so.

Once the connection is established, you can access and run
JupyterLab by copying the `http:/127.0.0.1:<port_number>/lab/token=<string>` URl into a web browser.

{% include links.md %}


