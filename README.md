# oci-objectstore

Perform various object store operations on the Oracle Cloud. This step uses the OCI Command Line Interface: https://github.com/oracle/oci-cli

**Note: It is recommended that you use environment variables in wercker, so you don't include any private keys in your code.**

# Options

## Options Common to all commands

* `region` (required) The OCI region that will be used for authorization.
* `tenancy-ocid` (required) The tenancy OCID that will be used for authorization. (typically looks something like ocid1.tenancy.oc1..xxxxxxx)
* `user-ocid` (required) The user OCID that will be used for authorization. (typically looks something like ocid1.user.oc1..xxxxxxx)
* `fingerprint` (required) The key fingerprint of the user's public key on OCI - used for authorization.
* `api-key` (required) The private key in PEM format to be used for authorization.
* `namespace` (required) The namespace to use in OCI object store (i.e. your tenancy name).
* `bucket-name` (required) The name of the OCI object store bucket to use.
* `command` (required) The OCI CLI command you wish to perform. Supported commands are `bulk-upload`, `bulk-download`, `get` and `put`. See `oci os object --help` for more on these commands.
* `options` (optional) Arbitrary options provided to the OCI CLI command that you specify in `command`. See `oci os object <command name> --help` for more on the option available for each specific command (e.g.) `oci os object bulk-upload --help`

## Options dependent on the command used

* `local-dir` (required for `bulk-upload` and `bulk-download`) The local directory to use for bulk upload from or bulk download to.

* `prefix` (optional, used by `bulk-upload` and `bulk-download`)
    * For `bulk-upload` this is the prefix to used for all object names. Defaults to the basename of the local-dir if not specified. (e.g.) if bulk-upload is invoked for local-dir /tmp/mydir then the default prefix (if not specified) for all uploaded objects will be 'mydir/'. See the `--object-prefix` option under `oci os object bulk-upload --help`
    * For `bulk-download` this is the prefix to filter downloaded objects by. See the `--prefix` option under `oci os object bulk-download --help`

* `local-file` (required only for `put`, optional for `get` command, corresponds to `--file` option of OCI CLI `get` and `put`)
    * For the `put` command, this is the path to the local file to be uploaded to OCI object store. 
    * For the `get` command, this is the local file to download an object to. If unspecified, the local file name will be the name of the object. If the name of the object contains '/' characters, then only the portion after the last '/' character will be used for the file name.

* `object-name` (required for `get`,  optional for `put` command, corresponds to the `--name` option of OCI CLI `get` and `put`)
    * For the `put` command, if specified, this is used as the object name of the uploaded local-file on OCI object store. If unspecified, the default is the basename of the local-file
    * For the `get` command, object-name is required, and is the name of the object to download.

* overwrite (optional, defaults to false) If specified, translates to the `--overwrite` option of OCI CLI bulk-upload and bulk-download commands, or the `--force` option of the OCI CLI `put` command. Ignored for the `get` command.

# Examples

### bulk-upload
```
build:
    steps:
      - oci-objectstore:
        command: bulk-upload
        region: us-ashburn-1
        tenancy-ocid: ocid1.tenancy.oc1..xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
        user-ocid: ocid1.user.oc1..xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
        fingerprint: nn:nn:nn:nn:nn:nn:nn:nn
        api-key: "$(cat your_api_key.pem)"
        bucket-name: mybucket
        namespace: mytenancy
        local-dir: /tmp/mydir
        overwrite: true
```

### put
```
build:
    steps:
      - oci-objectstore:
        command: put
        region: us-ashburn-1
        tenancy-ocid: ocid1.tenancy.oc1..xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
        user-ocid: ocid1.user.oc1..xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
        fingerprint: nn:nn:nn:nn:nn:nn:nn:nn
        api-key: "$(cat your_api_key.pem)"
        bucket-name: mybucket
        namespace: mytenancy
        local-file: mydir/myswagger.json
        overwrite: true
        options: --content-type application/swagger+json
```

# Permissions

It's a good idea to create a user which just has enough permissions to be able to sync to the OCI object store buckets that it needs to. 

# Changelog
## 0.1.0
- Initial release
