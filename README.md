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
    
Add metadata annotations (for compute roles) onto storage resources
```
imeta add -R lts_resc COMPUTE_RESOURCE_ROLE LONG_TERM_STORAGE
imeta add -R img_resc COMPUTE_RESOURCE_ROLE IMAGE_PROCESSING
```

Stage input file for demo
```
icd
iput /tmp/stickers.jpg
```

```
irods@ub16dc:~$ ils -l
/tempZone/home/rods:
  rods              0 demoResc      2157087 2020-03-12.08:23 & stickers.jpg
irods@ub16dc:~$ irule -F spawn_remote_slurm_jobs.r
host=[ub16dc]
irods@ub16dc:~$ squeue 
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
                 7     debug convert.    irods PD       0:00      1 (None)
                 8     debug convert.    irods PD       0:00      1 (Priority)
                 9     debug convert.    irods PD       0:00      1 (Priority)
                 6     debug convert.    irods  R       0:02      1 ub16dc
irods@ub16dc:~$ squeue 
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
irods@ub16dc:~$ ils -lr
/tempZone/home/rods:
  rods              0 demoResc      2157087 2020-03-12.08:23 & stickers.jpg
  C- /tempZone/home/rods/.slurm  
/tempZone/home/rods/.slurm:
  C- /tempZone/home/rods/thumbnails  
/tempZone/home/rods/thumbnails:
  rods              1 lts_resc       229955 2020-03-12.08:27 & stickers_thumbnail_1024x1024.jpg
  rods              1 lts_resc         6456 2020-03-12.08:27 & stickers_thumbnail_128x128.jpg
  rods              1 lts_resc        19355 2020-03-12.08:27 & stickers_thumbnail_256x256.jpg
  rods              1 lts_resc        63029 2020-03-12.08:27 & stickers_thumbnail_512x512.jpg
irods@ub16dc:~$ 
```
```
