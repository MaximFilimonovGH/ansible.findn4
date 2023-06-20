# ansible.findn4

This playbook setups the findNeighbours4 application (https://github.com/davidhwyllie/findNeighbour4).

The playbook also installs the following dependencies for running the Nextflow pipelines:
* [Catwalk](https://github.com/dvolk/catwalk)
* [Mongodb](https://www.mongodb.com/) 6.0.4

It is designed to run on a clean Ubuntu 20.04 server (i.e. a server on which mongodb, catwalk etc. have not yet been installed).

This repo also contains a GitHub Actions workflow which tests the playbook.

## Requirements

findNeighbours4 requires Python 3.8 or 3.9. Ubuntu 20.04 has Python 3.8 as default.

## Install instructions

Install ansible

```
sudo apt update
sudo apt install ansible
```

Clone the repository

```
git clone repository
```

Run the playbook

```
cd ansible.findn4
ansible-playbook playbook.yml
```

## Demo server

A set of data for demonstration of server function is installed alongside the main software.
More information on the data alongside more data sets can be found [here](https://github.com/davidhwyllie/findNeighbour4/blob/master/doc/demos_real.md).

To run the server in background use the following command from `findNeighbours4` directory:

```
pipenv run nohup python findNeighbours4_server.py demos/AC587/config/config_demo.json &
```

To populate the database with test data run a python script that reads fasta files and adds them to mongodb:

```
pipenv run python demo/demo_ac587.py
```

Now all the data is in mongodb and can be accessed from mongodb shell by using the following command: `mongosh`

Additionally, the data can be probed using a set of REST API calls that can be found [here](https://github.com/davidhwyllie/findNeighbour4/blob/master/doc/rest-routes.md), i.e. by using the following commands:

```
curl localhost:5032/api/v2/2d71e116-1b1d-46e6-86d7-6c82ac3dced8/neighbours_within/10

curl localhost:5032/api/v2/2d71e116-1b1d-46e6-86d7-6c82ac3dced8/annotation

curl localhost:5032/api/v2/2d71e116-1b1d-46e6-86d7-6c82ac3dced8/015fd748-2e21-44e7-9937-fd9363e04bc1/exact_distance
```

## Updating software versions
The versions for the installed software are found in `ansible.findn4/roles/findn4/defaults/main.yml`. 
These can be updated. When updates are made to the playbook, a new GitHub Actions run is triggered.

To check for the latest versions of Mongodb look [here](https://www.mongodb.com/docs/manual/reference/versioning/).

## GitHub Actions Run
Everytime a commit is made to master branch, this triggers a run of the GitHub Actions workflow found in 
`ansible.findn4/.github/workflows/main.yml`.

This workflow tests the ansible playbook by running it on a GitHub Actions hosted server. The OS of the server is set in `ansible.findn4/.github/workflows/main.yml` in the following line:

```
runs-on: ubuntu-20.04
```

This can be changed to another Ubuntu environment e.g. `ubuntu-18.04` (note that if you do this, you'll have to update Python to 3.8 manually and adjust Ubuntu version in `ansible.findn4/roles/findn4/defaults/main.yml` to match the OS and that this playbook can only run on Ubuntu servers). 

More information on GitHub environments can be found [here](https://github.com/actions/virtual-environments).

If the GitHub Actions workflow run is successful, you'll see a green tick next to the commit, failed runs show a red cross.
You can also check the badge at the top of the README for the build status.
To investigate a failed run, look under the Actions tab at the top of the page for more information, 
then click on the failed run and then click build.
The workflow can also be manually run through the Actions tab.