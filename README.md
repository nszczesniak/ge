# great-expectations-template

Standardized directory structure and resources for development of  Great Expectations projects

## Requirements

A docker runtime with support for docker-compose.

## How To Use

All command line examples assume a *nix environment.

### Initial Repo Setup

Create a new git repo using this repo as a template.

Clone your new git repo and update/remove the LICENSE as appropriate to your use case.

### Booting The Development Environment

```bash
# cd ~your new repo~/runtime
# docker-compose up
```

At this point you should have a running docker container which has mounted ~your new repo~/project and ~your new repo~/data into the container at /home/project and /home/data respectively. Your container is bound to the docker-compose runtime in the foreground of your cli.

Note: docker-compose was chosen as a way to codify the boot process to ensure a consistent and trouble free startup. It is not strictly necessary for the development environment to function as intended. For example the same result is possible using only the docker command syntax. See runtime/docker-compose.yml to understand the required configuration for booting using the docker command syntax.

### Workflow

If you followed the docker-compose example above you have a docker-compose process running in the foreground of your cli. Docker-compose has downloaded, created, and booted a container called ge-dev.

Killing the docker-compose process will kill the container, however the files generated in /home/project and /home/data will not be destroyed as these are your created on the host file system within ~your new repo~/project and ~your new repo~/data, respectively. These files are your project that will comprise your test suite.

Your container runtime has an installation of the [great-expectations codebase](https://greatexpectations.io/) and all related runtime dependencies. You can work with this environment by externally calling commands to be executed within the container, or you can enter the container and run commands from within.

Either way, the expected workflow will be a 1-time bootstrapping process to create your project skeleton. These files will be created in the project directory.

The data directory exists to add file-based data to your project for use in developing your test suite. great_expectations also supports a SQL-compatible database as a data source. If you wish to add example data for development purposes running in a SQL-compatible database it is possible to edit the runtime/docker-compose.yaml file to boot and network a databse container with example data in it for this purpose. The details about how to do this are outside of the scope of this README at this time.

After bootstrapping your project you will interactively edit your auto-generated test suite using a Jupyter notebook. The notebook is auto-generated by great_expectations on-demand at the time you need it. Everything to do this, including the Jupyter notebook runtime is available from with the ge-dev container.

### Initializing Your Project

After your ge-dev container is booted and continuously running your next step will be to initialize your project environment.

During the initialization of your project you will be asked to identify a datasource for great_expectations to auto-generate a test suite against. This auto-generated test suite is the starting point of your project. You can skip this step in initialization and manually add a datasource later, however you won't get much work done until you have your auto-generated test suite so now is the best time to identify your development data set and add it to your project.

Assuming you are using file-based data, add at least 1 example file to the data directory of your project on your host machine.

If you are running docker-compose in the foreground of your cli, as shown in the examples above, you will open a new cli now and...

```bash
# docker exec -it ge-dev bash
# cd /home/project
# great_expectations init
```

This command can also easily be run from outside the container in typical docker exec fashion :

```bash
# docker exec -it ge-dev bash -c 'cd /home/project && great_expectations init'
```

Either way, at this point you should follow the prompts given to you by the great_expectations initialization process. Unless you know you want/need something different it is suggested that you choose the options as shown below.

Note that this example uses a dataset available from <https://github.com/superconductive/ge_example_project/tree/master/data/npidata> but you've added your own files to your data directory and these will be available within the container at /home/data/~your file name~. You should also note the step which asks you to name your test suite. A project can contain multiple test suites so choose a name that makes sense to your project here.

```bash
OK to proceed? [Y/n]: Y

What data would you like Great Expectations to connect to?
    1. Files on a filesystem (for processing with Pandas or Spark)
    2. Relational database (SQL)
: 1

What are you processing your files with?
    1. Pandas
    2. PySpark
: 1

Enter the path (relative or absolute) of a data file
: /home/data/npidata_pfile_20190902-20190908.csv

Name the new expectation suite [npidata_pfile_20190902-20190908.warning]: example-test-suite

Great Expectations will choose a couple of columns and generate expectations about them
to demonstrate some examples of assertions you can make about your data.

Press Enter to continue
:

Generating example Expectation Suite...
Building Data Docs...
The following Data Docs sites were built:
- local_site:
   file:///home/project/great_expectations/uncommitted/data_docs/local_site/index.html
A new Expectation suite 'example-test-suite' was added to your project

Great Expectations is now set up.
```

In the final output of your project initialization some Data Docs were built and placed within the "uncommitted" directory of your project. As the name implies this directory should not be committed to version control and great_expectations has added the requisite .gitignore file to ensure this does not happen. To browse this content you can browse to the location of the index.html file on the filesystem of the host machine and open the index.html file in a browser.

#### TIP

If you're doing a fresh clone of a repo that has already been setup using the first time setup process above, you'll still need to run these steps to recreate the "uncommitted" directory and related files necessary for great_expectations to work properly. Your output won't match what has been described above, but don't worry, this is not a problem.

### Editing Your Test Suite

The ge-dev container does not come with a graphical user interface or a browser capable of providing a good user experience with Jupyter notebooks. However using a Jupyter notebook as your IDE for test suite development is the recommendation from the team at great_expectations.

Some helper scripts have been added to make it easier to build and run a Jupyter notebook from within the ge-dev container while using your host's web browser to access the notebook.

From inside the container run the command :

```bash
# notebook build ~your test suite name~
```

This command can also easily be run from outside the container in typical docker exec fashion :

```bash
# docker exec -it ge-dev notebook build ~your test suite name~
```

In either case the result will be an auto-generated Jupyter notebook from the latest great_expectations test-suite definition, however this step does not start the Jupyter notebook runtime.

To start the Jupyter notebook runtime, from inside the container run the command :

```bash
# notebook edit
```

This command can also easily be run from outside the container in typical docker exec fashion :

```bash
# docker exec -it ge-dev notebook edit
```

Your result should look something like this :

```bash
developer@ac6c692cac62:/$ notebook edit
[I 10:20:04.355 NotebookApp] Writing notebook server cookie secret to /home/developer/.local/share/jupyter/runtime/notebook_cookie_secret
[I 10:20:04.621 NotebookApp] Serving notebooks from local directory: /home/project/great_expectations/uncommitted
[I 10:20:04.622 NotebookApp] The Jupyter Notebook is running at:
[I 10:20:04.623 NotebookApp] http://ac6c692cac62:8888/?token=90510c04087c287d8a7e923705206b77eb73b54d388ed1cd
[I 10:20:04.624 NotebookApp]  or http://127.0.0.1:8888/?token=90510c04087c287d8a7e923705206b77eb73b54d388ed1cd
[I 10:20:04.624 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 10:20:04.629 NotebookApp]

    To access the notebook, open this file in a browser:
        file:///home/developer/.local/share/jupyter/runtime/nbserver-86-open.html
    Or copy and paste one of these URLs:
        http://ac6c692cac62:8888/?token=90510c04087c287d8a7e923705206b77eb73b54d388ed1cd
     or http://127.0.0.1:8888/?token=90510c04087c287d8a7e923705206b77eb73b54d388ed1cd
```

Note that the URLs provided may be incorrect for your host setup. The notebook helper script binds port 8888 to 0.0.0.0 of the docker network. How you have configured your docker runtime will determine which host/IP to use to access this network via the host browser. For example, running docker on Windows via the Docker Quickstart Terminal typically results in the container runtime binding to the host IP 192.168.99.100 so in this case the correct way to access the notebook from the host machine is to point a browser at <http://192.168.99.100:8888/?token=30213d790abc1c8a54bd0aa17200dbe1b45f61acdcb6535d.>

It is also important to note that the token at the end of the URL is produced by Jupyter each time it boots the runtime, and this is used to control access to your notebook. It will change on every notebook runtime boot.

### Next Steps

At this point you have everything you need to begin editing your test suite and to further explore the capabilities of great_expectations but there are a couple of things to note.

#### TIP 1

You're using the great_expectations software and the only thing this codebase has done is to wrap great_expectations into a templated git repo with a bunded runtime to ensure a consistent and reliable developer experience across all projects started from this template.

#### TIP 2

great_expectations has really good documentation and tutorials - <https://docs.greatexpectations.io/> - that will teach you everything you need to know about how use their software. It should be noted that all commands they reference in their documentation can be run exactly as they are shown in their documentation by logging into the container runtime you booted at the beginning of this README

```bash
# docker exec -it ge-dev bash
# cd /home/project
# ~run any great_expectations command~
```

WITH A VERY IMPORTANT EXCEPTION - If you expect to run the Jupyter notebook inside of the container, but interact with it via a browser running from the host machine, the notebook cannot be booted from within the container as shown in the great_expectations documentation. Use the process described in the "Editing Your Test Suite" section of this README to make that possible or take a look at what the runtime/helper_commands/notebook script of this repo is doing and boot the notebook yourself using a similar approach to bind the notebook to IP 0.0.0.0 and port 8888.

#### TIP 3

Your auto-generated data_docs are an important part of your project and should be kept up to date by auto-generating them before merging to master. The convention expected by this template is to place the most recent and correct data_docs into the data_docs folder at the root of this template when they are ready to be updated in version control.

By default great_expectations generates them into a directory which is not tracked in version control at project/great_expectations/uncommitted/data_docs

You can re-generate your data_docs at any time. More info can be found here - <https://docs.greatexpectations.io/en/latest/command_line.html#great-expectations-docs>


### Running In Production

The convention for running this codebase in production is a single entry point in the root of the "project" directory named main.py. From this script you should build your batch_kwargs and call your expectation suites.

This file should be runnable in dev, stage, and production with all differences between those environments abstracted into environment variables. great_expectations support this natively by giving environment variables priority over config file definitions. <https://docs.greatexpectations.io/en/latest/reference/data_context_reference.html#config-variables-file> This template supports this mode through the inclusion of a server.env file which is read at container boot time and used to inject the environment variables defined within into the container runtime.