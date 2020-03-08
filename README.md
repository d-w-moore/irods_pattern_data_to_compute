# irods_pattern_data_to_compute

Initial set-up for **Data To Compute** demonstration
---

Tested on 
  - Ubuntu 16.04 
    (Assuming an `ubuntu` user having password-less `sudo`
     on a single-node, vanilla install of iRODS-4.2.7 )
    
Install necessary prerequisites:
```
sudo apt-get update
sudo apt-get -y install imagemagick
sudo apt-get -y install python-pip
sudo pip install python-irodsclient
```

Clone this repo:
```
git clone https://github.com/irods/irods_pattern_data_to_compute
```

Build the Data To Compute package
```
cd
git clone https://github.com/irods/irods_training
sudo apt-get -y install irods-externals-cmake3.5.2-0 irods-dev
export PATH=/opt/irods-externals/cmake3.5.2-0/bin/:$PATH
```

Build SLURM and its prerequisite `munged`
```
cd ~/irods_training/advanced/hpc_data_to_compute/
./ubuntu_16/install_munge_and_slurm.sh
```
