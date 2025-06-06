# Falcon Sensor Bosh release

* Clone this repository and execute:

  `bosh init-release`


* The config directory is created with the following contents:
  ```
  ├ config
  │   ├ blobs.yml
  │   └ final.yml
  ├ jobs
  ├ packages
  └ src
  ```

* Copy Falcon sensor agent (Crowdstrike agent) to `src` directory, example:
  ```
  ├ src
  │   ├  falcon-installer
  ```

* Register blobs or a new version using the following command:
  ```
  bosh add-blob src/falcon-installer falcon-installer
  ```
This populates the `blobs.yml` with blob filename, filename size, and SHA

* Update `packages > falcon-linux-sensor > spec`, to include the blob
  ```
  files:
    - falcon-installer
  ```

* Update the file under `config > final.yml` to include the `blobstore_path` variable:

  ```
  blobstore:
    provider: local
    options:
      blobstore_path: /Users/bob/blobs

  name: falcon-sensor
  ```

* Create the Release
  ```
  bosh create-release --final --force --tarball=falcon-sensor.tgz
  ```

* Upload the bosh Release
  ```
  bosh upload-release falcon-sensor.tgz
  ```

* Generate runtime config
  ```
  bosh int runtime-config/falcon.yml --var=version=1 --var=client_id=xxxxxxxxxx --var=client_secret=xxxxxxxxxx --var=cid=xxxxxxxxxx --var=apd=true > generated_runtime_config.yml
  ```

* Update the runtime config
  ```
  bosh update-runtime-config --name=falcon-sensor generated_runtime_config.yml
  ```

* Trigger `Apply Changes` from Ops Manager

