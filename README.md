# Data Version Control DVC
- DVC helps to track large data files eg: datasets, pickl, sql that cannot be uploaded in the git.

### Setup DVC
- Your project source should be linked with git. 
- Install dvc `pip install dvc`
- Run `dvc init`
    - This command will create a .dvc folder wich will contain .dvc/.gitignore and .dvc/config
- After initializing the dvc commit .dvc folder to git repo.
    - `git commit -m "Initialize DVC"`
- Setup a remote repository to save all your data files through dvc.
    - This can be local or cloud storage.
    - For local 
        ```
        mkdir /storage/mystorage
        dvc remote add -d myremote /storage/mystorage
        ```
    - For Cloud
        ```
        dvc remote add -d storage "storage-url"
        ```
     - eg: for s3 `s3://storage/mystorage`
     - eg: for google drive `gdrive://<YourDriveID>`
- Push (Upload the data into storage.)
    - `dvc push`
    - This command will upload the local data into dvc storage (whichever it maybe configured local-storage or cloud-storage.)
    - This command will created a .dvc file eg: data.csv.dvc which will contain meta data and path to track the data files that are being uploaded to dvc storage.
    - For testing: delete the local data and perform `dvc pull` this command will download the tracked data from the dvc storage and save it in local repo.

### Modify, Update Data
- You can make any changes to the data in local repo as required.
- For testing just add or delete few rows of data from the data files.
- Now add the modified data into dvc with `dvc add`.
- Use `dvc push` to upload modified data into dvc repository.
    - This command will modify the data.dvc file and update its meta data and data path to dvc storage.
- Finally use `git commit -m "Modified Data" data/data.csv.dvc` `git tag -a "v1.0"`
    - This will save modified data.dvc in our git repo.
    - It is useful to apply tag in git to make roll back easier

### Roll Back To Older Data Version
- To roll back to older data version or switch data version use git checkout `git checkout v1.1 data/data.csv.dvc` and `dvc checkout`
    - This will use .dvc file and track the path to the required data version.


### Share Data With DVC
- Find File or Dir `dvc list Your-Git-Repo-Url file-or-folder`
- `dvc get` Dose not works across different project.
- Use `dvc import Your-Git-Repo-Url file-name -o destination/file.csv`
    ```
    dvc import https://github.com/iterative/dataset-registry \
    get-started/data.xml -o data/data.xml
    ```
    - This will download data file and .dvc file for meta
- `dvc update data/data.csv.dvc` Checks the newest data version and downloads

### Get Data Using Python API
```
import dvc.api
import pandas as pd
with dvc.api.open("wine/datasets/actual.csv", repo="https://github.com/mizall90/newDVC") as fd:
    df = pd.read_csv(fd)
    print(df)
```