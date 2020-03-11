# irods_pattern_data_to_compute

Initial set-up for **Data To Compute** demonstration
---

Tested on 
  - Ubuntu 16.04 
    (Assuming an `ubuntu` user having password-less `sudo`
     on a single-node, vanilla install of iRODS-4.2.7 )

  - Follow "Administration - Getting Started" from UGM 2019 to fullfill prerequisites:

      - set up iRODS 4.2.7 via package install
      - skip the audit plugin setup if desired

---

Install necessary prerequisites:
```
sudo apt-get update
sudo apt-get -y install imagemagick
sudo apt-get -y install python-pip
sudo pip install python-irodsclient
```
---
Clone this repo:
```
git clone https://github.com/irods/irods_pattern_data_to_compute
```
---
Build the Data To Compute package

```
sudo apt-get -y install irods-externals-cmake3.5.2-0 irods-dev
export PATH=/opt/irods-externals/cmake3.5.2-0/bin/:$PATH
cd
mkdir build_data_to_compute
cd build_data_to_compute
cmake ../irods_pattern_data_to_compute/
make package
sudo dpkg -i ./irods-hpc-data-to-compute-example_4.2.7~xenial_amd64.deb
```
---

Build SLURM and its prerequisite `munged`

```
~/irods_pattern_data_to_compute/ubuntu_16/install_munge_and_slurm.sh
```

---

Make sure that iRODS server is running
```
sudo su - irods -c '~/irodsctl restart'
```

Create storage resources for the demo:
  - As `ubuntu` user:
    ```
    sudo mkdir /iRods
    sudo chmod irods:irods /iRods
    ```
  - As `irods` (service account) user:
    ```
    iadmin mkresc img_resc unixfilesystem `hostname`:/iRods/img_resc
    iadmin mkresc lts_resc unixfilesystem `hostname`:/iRods/lts_resc
    ```
