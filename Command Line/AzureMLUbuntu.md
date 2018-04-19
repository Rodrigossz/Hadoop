# Ubuntu

1.	Get all the required tools up and running:
1.1.	You will need an Azure ML Experimentation Service account.
1.2.	You will also need an Azure ML Model Management account. Typically, you are given an option to create one when creating the AML Experimentation Service account from the above.
1.3.	You must have the ability to connect to a Linux machine via SSH. You can use PuTTY or, alternatively, Bash shell from Windows Subsystem for Linux in Windows 10.
1.4.	Provision an Ubuntu DSVM from the Azure portal, size and location are mostly irrelevant unless you plan to work with really large datasets.

2.	SSH into the DSVM and run the following command to configure Docker:

```sudo
sudo usermod -a -G docker $USER 
``` 

3.	Disconnect from the DSVM and reconnect back to it via an SSH tunnel. This will let you use the JupyterHub running on the DSVM without having to open DSVM ports via Azure Portal. The command is:

``` ssh
ssh -L 8000:localhost:8000 username@DSVM_IP
```

You will need to substitute the IP for the DSVM you provisioned.

4.	Install the Azure ML Package for Forecasting:
4.1.	Download this script on the DSVM. The easiest way to accomplish it on DSVM is to do

```wget
wget https://azuremlftkrelease.blob.core.windows.net/dailyrelease/install_azuremlftk.sh
``` 

4.2.	Run the downloaded script via the command below. This can take about 20 minutes.

```bash
bash install_azuremlftk.sh
```

4.3.	Run the following command to activate the AMLFP environment:

```AMLFP
source activate azuremlftk_mar2018 
``` 

4.4.	The bash script from 4.2 will download the example notebook and place it in a subfolder "~/notebooks".  You can also download the walkthrough notebook manually and put it in a place where Jupyter can find it.
4.5.	Run az login and follow its prompts to connect your shell to Azure:
o	Notice that this command will require some action on your behalf. It will print an alphanumeric code to console, which you will need to copy.
o	Using the browser on your local machine, navigate to https://aka.ms/devicelogin and enter the code into the form.
o	Follow prompts until you see a message that you are logged in.
o	Once the command prompt returns control back to you, you can continue.
4.6.	On your local machine, open a browser and type https://localhost:8000 
4.7.	Follow the prompts until you are shown the JupyterHub login window. If you are shown the “Your connection is not private” screen, follow Option 1 in this guide.
4.8.	When shown the login screen, the same credentials that you used to connect to DSVM should work.
4.9.	Open the notebook titled “Dominicks_OJ_Sales_Forecasting.ipynb”.
4.10.	Go to the “Kernel” menu and change kernel to "azuremlftk_mar2018".
4.11.	Your first call to deploy() will probably fail because of a missing Kubernetes binary. Azure ML will download it for you and simply rerunning the same notebook cell should work. More details can be found here.
5.	Let us know if anything in the notebook does not work for you: AMLForecastingPackageUsers@service.microsoft.com
6.	Try out the Forecasting Package on your own forecasting problem. This programming reference will hopefully be helpful.

FAQ
1.	Strange errors in notebooks, deep in the FTK code. For example, “NoneType has no ‘transform’ method” occurs during featurization.
1.1.	This can occur if the functionality demonstrated in the notebook uses newer package than what is installed. Please update the package to newest version.
2.	My call to aml_deployment.deploy() timed out after 30 minutes! 
2.1.	Your Azure subscription may not be “registered” for the Azure Container Service or Azure Container Registry. Open the terminal, activate the azuremlftk_mar2018 environment, and run the following commands. Then restart the notebook:
•	az login [make sure to follow all prompts]
•	az provider register -n Microsoft.MachineLearningCompute
•	az provider register -n Microsoft.ContainerRegistry
•	az provider register -n Microsoft.ContainerService
•	[If you want to verify whether the registration was successful] 
az provider list --out table 
3.	I am having trouble activating the environment after cloning it!
3.1.	Make sure you are using the cmd.exe shell and not Powershell. There is a known bug in activating conda environments via Powershell. If you are adventurous, this may be helpful
4.	I am having trouble cloning the environment! Getting errors about "irreconcilable conflicts"...
4.1.	First of all, try ignoring the irreconcilable conflicts message and proceed with installation. Some users reported the process to work fine for them.
4.2.	If the above did not succeed, you may have to reinstall Azure ML Workbench. Usually this means there is some corruption in the "root" conda environment. Yes, it will take about 30 minutes, again.
4.3.	If you have installed packages into your "root" conda environment, their versions may be clashing with the versions needed by the AMLPF. Please let us know. Starting from a clean Install of the Workbench, and its python environment, normally fixes this.
5.	I am having trouble using the package in a Python or IPython shell. I am getting an ImportError: " DLL load failed: The specified module could not be found."
5.1.	This is likely caused by conda not cloning the root environment correctly. The full solution is detailed here at Stackoverflow, the solution with the green checkmark by Miloslav Raus worked for us. As a shortcut, if you're using Python 3.5 that comes with AML Workbench (as you should), you can take this missing dll and copy it to %AppData%\amlworkbench\Python\envs\azuremlftk_mar2018\ folder.
6.	I am running out of memory errors from Docker!
6.1.	You may have to adjust the settings in your Docker app. Sometimes it installs with suboptimal default values.
7.	I encounter "500 Server Error: Internal Server Error for url: http+docker..."
7.1.	This is usually an indication that there is some problem with the "pull image" functionality of the docker. One quick and easy way that could help to check whether the "pull image" functionality of the docker is working is by running this example command - docker pull ubuntu. If this command fails, then it means there is some problem with the image pulling functionality within the docker.
7.2.	There are various reasons that could lead to the problem of image pulling functionality, and here are several things that you could try to see whether it helps, and here again you can use the example command above to test whether the problem gets fixed:
•	Quit and restart your docker
•	Reinstall docker
•	Restart your laptop
•	Change DNS Server in network settings of docker settings from "Automatic" to "Fixed with 8.8.8.8"
8.	At deployment, I get "Failed to run command az account set –subscription 'XXX' … The subscription 'XXX' doesn’t exist in cloud 'AzureCloud'"!
8.1.	This occurs when the notebook is run without running az login first – the user is not authenticated to the cloud. Please run az login again, follow all prompts, and then re-run the command.
9.	TBD
9.1.	TBD
