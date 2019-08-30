---
title: 'Quickstart: Create a Windows'
titleSuffix: Azure Data Science Virtual Machine 
description: Configure and create a Data Science Virtual Machine on Azure for analytics and machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm

author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.topic: quickstart
ms.date: 02/22/2019

---

# Quickstart: Set up a Windows Data Science Virtual Machine on Azure

The Microsoft Windows Data Science Virtual Machine (DSVM) is a Windows Server 2016 virtual machine (VM) image on Azure. It comes preinstalled and configured with tools for data analytics and machine learning.

## Included data science tools

The following tools are included in a DSVM:

* Python SDK for the [Azure Machine Learning service](../service/index.yml).
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index) Developer edition.
* Anaconda Python distribution.
* Jupyter Notebook with R, Python, and PySpark kernels.
* Microsoft Visual Studio Community.
* Microsoft Power BI Desktop.
* Microsoft SQL Server 2017 Developer edition.
* A standalone Apache Spark instance for local development and testing.
* [JuliaPro](https://juliacomputing.com/products/juliapro.html).
* Machine learning and data analytics tools:
  * Deep learning frameworks: [TensorFlow](https://www.tensorflow.org/), [Chainer](https://chainer.org/), MXNet, and Keras AI frameworks are included on the VM.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): A fast machine learning system that supports techniques like online hashing, allreduce, reductions, learning2search, and active and interactive learning.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): A tool that provides fast and accurate boosted tree implementation.
  * [Rattle](https://togaware.com/rattle/). The R analytical tool that gets you started with data analytics and machine learning in R. It includes GUI-based data exploration and modeling with automatic R code generation.
  * [Weka](https://www.cs.waikato.ac.nz/ml/weka/): Visual data mining and machine learning software in Java.
  * [Apache Drill](https://drill.apache.org/): A schema-free SQL query engine for Apache Hadoop, NoSQL, and cloud storage. It supports ODBC and JDBC interfaces for querying NoSQL and files from standard BI tools like Power BI, Microsoft Excel, and Tableau.
* Libraries in R and Python for use in Azure Machine Learning and other Azure services.
* Git, including Git Bash, to work with source code repositories like GitHub and Azure DevOps. Git provides several popular Linux command-line tools that are accessible both in Git Bash and in a command prompt. Examples are awk, sed, perl, grep, find, wget, and curl.
* Development tools and editors (RStudio, PyCharm).

### About data science

Data science involves iterating on a sequence of tasks:

1. Find, load, and preprocess data.
1. Build and test models.
1. Deploy the models for consumption in intelligent applications.

Data scientists use several tools for these tasks. It can be time consuming to find the appropriate versions of software and then download and install them. The DSVM saves time by providing a ready-to-use image that can be provisioned on Azure, with several popular tools preinstalled and configured.

The DSVM jump-starts your analytics project. You can work on tasks in various languages, including R, Python, SQL, and C#. Visual Studio provides an easy-to-use integrated development environment (IDE) to develop and test your code. The Azure SDK is included in the VM, so you can build your applications by using services on the Microsoft cloud platform.

There are no software charges for this DSVM image. You pay only the Azure usage fees. They depend on the size of the virtual machine that you provision. For more information, see the [Data Science Virtual Machine page](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows).

### Other DSVM versions

* An [Ubuntu](dsvm-ubuntu-intro.md) image. It has tools similar to the DSVM, plus a few deep learning frameworks.
* A [Linux CentOS](linux-dsvm-intro.md) image.
* The [Windows Server 2012 edition](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) of the Data Science Virtual Machine. A few tools are available only on the Windows Server 2016 edition. Otherwise, this article also applies to the Windows Server 2012 edition.

## Prerequisite

To create a Microsoft Data Science Virtual Machine, you must have an Azure subscription. See [Get Azure free trial](https://azure.com/free).

## Create your DSVM

To create a DSVM instance:

1. Go to the virtual machine listing on the [Azure portal](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016). You might be prompted to sign in to your Azure account if you're not already signed in.
1. Select the **Create** button at the bottom.

   ![Virtual machine listing on the Azure portal, with Create button](./media/provision-vm/configure-data-science-virtual-machine.png)

1. Enter the following information to configure each of the steps shown in the right pane of the screenshot:

   1. **Basics**:
      * **Name**: Enter the name of the DSVM.
      * **VM Disk Type**: Select either **SSD** or **HDD**. For an NC_v1 GPU instance like Nvidia Tesla K80 based, choose **HDD** as the disk type.
      * **User Name**: Enter the admin account ID.
      * **Password**: Enter the admin account password.
      * **Subscription**: If you have more than one subscription, select the one on which the machine will be created and billed.
      * **Resource Group**: Create a new group or use an existing one.
      * **Location**: Select the datacenter that's most appropriate. For fastest network access, it's the datacenter that has most of your data or is closest to your physical location.
   1. **Size**: Select the server type that meets your functional requirements and cost constraints. For more choices of VM sizes, select **View All**.
   1. **Settings**:  
      * **Use Managed Disks**. Choose **Managed** if you want Azure to manage the disks for the VM. If not, you need to specify a new or existing storage account.  
      * **Other parameters**. You can use the default values. If you want to use non-default values, hover over the informational link for help on the specific fields.
   1. **Summary**: Verify that all the information you entered is correct. Select **Create**.

> [!NOTE]
> * The VM doesn't incur any additional charges beyond the compute cost for the server size that you chose in the **Size** step.
> * Provisioning takes 10 to 20 minutes. You can view the status of your VM on the Azure portal.

## Access the DSVM

After the VM is created and provisioned, you can access it through a remote desktop connection. Use the admin account credentials that you configured in the **Basics** step of creating a virtual machine. 

You're ready to start using the tools that are installed and configured on the VM. Many of the tools can be accessed through **Start** menu tiles and desktop icons.

You can also attach a DSVM to Azure Notebooks to run Jupyter notebooks on the VM and bypass the limitations of the free service tier. For more information, see [Manage and configure Notebooks projects](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

<a name="tools"></a>

## Tools installed on the DVSM

In the following sections, learn more about the tools that come installed on the Data Science Virtual Machine.

### Microsoft Machine Learning Server Developer edition

You can use Microsoft Enterprise Library for your analytics because Machine Learning Server Developer edition is installed on the VM. Previously known as Microsoft R Server, Machine Learning Server is a broadly deployable analytics platform. It's scalable and commercially supported.

Machine Learning Server supports various big data statistics, predictive modeling, and machine learning tasks. It supports the full range of analytics: exploration, analysis, visualization, and modeling. By using and extending open-source R and Python, Machine Learning Server is compatible with R and Python scripts and functions. It's also compatible with CRAN, pip, and Conda packages to analyze data at the enterprise scale.

Machine Learning Server addresses the in-memory limitations of open-source R by adding parallel and chunked processing of data. This means you can run analytics on much bigger data than what fits in main memory. 

Visual Studio Community is included on the VM. It has the R tools for Visual Studio and Python Tools for Visual Studio (PTVS) extensions that provide a full IDE for working with R or Python. We also provide other IDEs like [RStudio](https://www.rstudio.com) and [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) on the VM.

### Python

For development by using Python, Anaconda Python distributions 2.7 and 3.6 are installed. These distributions have the base Python along with about 300 of the most popular math, engineering, and data analytics packages. You can use PTVS, which is installed in Visual Studio Community 2017. Or you can use one of the IDEs bundled with Anaconda, like IDLE or Spyder. Search for and open one of these packages (Windows logo key+S).

> [!NOTE]
> To point the Python Tools for Visual Studio at Anaconda Python 2.7, you need to create custom environments for each version. To set these environment paths in Visual Studio 2017 Community, go to **Tools** > **Python Tools** > **Python Environments**. Then select **+ Custom**.

Anaconda Python 3.6 is installed under C:\Anaconda. Anaconda Python 2.7 is installed under C:\Anaconda\envs\python2. For detailed steps, see the [PTVS documentation](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters).

### The Jupyter Notebook

Anaconda Distribution also comes with the Jupyter Notebook, an environment to share code and analysis. The Jupyter Notebook server is preconfigured with Python 2.7, Python 3.x, PySpark, Julia, and R kernels. To start the Jupyter server and open the browser to access the notebook server, use the **Jupyter Notebook** desktop icon.

We package several sample notebooks in Python and R. After you access Jupyter, the notebooks show how to work with the following technologies:

* Machine Learning Server
* SQL Server Machine Learning Services, in-database analytics
* Python
* Microsoft Cognitive Toolkit
* TensorFlow
* Other Azure technologies

You can find the link to the samples on the notebook home page after you authenticate to the Jupyter Notebook by using the password that you created earlier.

### Visual Studio Community 2017

The DSVM includes Visual Studio Community. This is a free version of the popular IDE from Microsoft that you can use for evaluation purposes and small teams. See the [Microsoft Software License Terms](https://www.visualstudio.com/support/legal/mt171547).

Open Visual Studio by using the desktop icon or the **Start** menu. Search for programs (Windows logo key+S), followed by **Visual Studio**. From there, you can create projects in languages like C#, Python, R, and Node.js. Installed plug-ins make it convenient to work with the following Azure services:

* Azure Data Catalog
* Azure HDInsight for Hadoop and Spark
* Azure Data Lake

A plug-in called Azure Machine Learning for Visual Studio Code also integrates with Azure Machine Learning and helps you rapidly build AI applications.

> [!NOTE]
> You might get a message that your evaluation period is expired. Enter your Microsoft account credentials. Or create a new free account to get access to Visual Studio Community.

### SQL Server 2017 Developer edition

The DSVM comes with a developer version of SQL Server 2017 with Machine Learning Services. This SQL Server edition comes in either R or Python and can run in-database analytics. 

Machine Learning Services provides a platform for developing and deploying intelligent applications. You can use these languages and many packages from the community to create models and generate predictions for your SQL Server data. You can keep analytics close to the data because Machine Learning Services, in-database, integrates both the R and Python languages within SQL Server. This integration eliminates the cost and security risks associated with data movement.

> [!NOTE]
> The SQL Server Developer edition is only for development and test purposes. You need a license to run it in production.

You can access SQL Server by opening Microsoft SQL Server Management Studio. Your VM name is populated as **Server Name**. Use Windows authentication when you sign in as the admin on Windows. When you're in SQL Server Management Studio, you can create other users, create databases, import data, and run SQL queries.

To enable in-database analytics by using SQL Server Machine Learning Services, run the following command as a one-time action in SQL Server Management Studio after you sign in as the server administrator:

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

Replace `%COMPUTERNAME%` with your VM name.

### Azure

Several Azure tools are installed on the VM:

* A desktop shortcut goes to Azure SDK documentation.
* Use AzCopy to copy data in and out of your Azure storage account. To see usage, enter **Azcopy** at a command prompt.
* Use Azure Storage Explorer to browse through the objects that you store in your Azure storage account. It also copies data to and from Azure Storage. To access this tool, enter **Storage Explorer** in the **Search** field. Or find it on the Windows **Start** menu.
* AdlCopy copies data to Azure Data Lake. To see usage, enter **adlcopy** in a command prompt.
* The dtui tool copies data to and from Azure Cosmos DB, a NoSQL database in the cloud. Enter **dtui** in a command prompt.
* The integration runtime copies data between on-premises data sources and the cloud. It's used within tools like Azure Data Factory.
* Use Azure PowerShell to administer your Azure resources in the PowerShell scripting language. It's also installed on your VM.

### Power BI

The DSVM comes with Power BI Desktop installed to help you build dashboards and visualizations. Use this tool to pull data from different sources, to author your dashboards and reports, and to publish them to the cloud. For more information, see the [Power BI site](https://powerbi.microsoft.com). You can find Power BI Desktop on the **Start** menu.

> [!NOTE]
> You need a Microsoft Office 365 account to access Power BI.

### Azure Machine Learning SDK for Python

Data scientists and AI developers use the Azure Machine Learning SDK for Python to build and run machine learning workflows with the [Azure Machine Learning service](../service/overview-what-is-azure-ml.md). You can interact with the service in Jupyter Notebooks or another Python IDE by using open-source frameworks such as TensorFlow and scikit-learn.

The Python SDK is preinstalled on the Microsoft Data Science Virtual Machine. To start using the Python SDK, see [Use Python to get started with Azure Machine Learning](../service/quickstart-create-workspace-with-python.md).

## More Microsoft development tools

You can use the [Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) to find and download other Microsoft development tools. There's also a shortcut to the tool on the Microsoft Data Science Virtual Machine desktop.  

## Important directories on the VM

| Item | Directory |
| --- | --- |
| Jupyter Notebook server configurations | C:\ProgramData\jupyter |
| Jupyter Notebook samples home directory | C:\dsvm\notebooks and c:\users\\<username\>\notebooks |
| Other samples | C:\dsvm\samples |
| Anaconda, default: Python 3.6 | C:\Anaconda |
| Anaconda Python 2.7 environment | C:\Anaconda\envs\python2 |
| Microsoft Machine Learning Server (standalone) for Python | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Default R instance, Machine Learning Server (standalone) | C:\Program Files\Microsoft\ML Server\R_SERVER |
| SQL Server Machine Learning Services in-database instance directory | C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Miscellaneous tools | C:\dsvm\tools |

> [!NOTE]
> On the Windows Server 2012 edition of the DSVM and the Windows Server 2016 edition before March 2018, the default Anaconda environment is Python 2.7. The secondary environment is Python 3.5, located at C:\Anaconda\envs\py35.

## Next steps

* Explore the tools on the DSVM by opening the **Start** menu.
* Learn about the Azure Machine Learning service by reading [What is Azure Machine Learning service?](../service/overview-what-is-azure-ml.md) and trying out [quickstarts and tutorials](../service/index.yml).
* In File Explorer, browse to C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts for samples that use the RevoScaleR library in R that supports data analytics at enterprise scale. 
* Read the article [Ten things you can do on the Data Science Virtual Machine](https://aka.ms/dsvmtenthings).
* Learn how to build end-to-end analytical solutions systematically by using the [Team Data Science Process](../team-data-science-process/index.yml).
* Visit the [Azure AI Gallery](https://gallery.cortanaintelligence.com) for machine learning and data analytics samples that use Azure Machine Learning and related data services on Azure. We've also provided an icon for this gallery on the **Start** menu and on the desktop of the virtual machine.
