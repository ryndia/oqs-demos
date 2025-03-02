# Scripts to generate OQS test server

This folder contains all scripts to [build a QSC-enabled nginx server running on ubuntu](build-ubuntu.sh) as well as generating all configuration files for running an interoperability test server: Running [python3 genconfig.py](genconfig.py) generates a local/self-signed root CA, all QSC certificates signed by this root CA for the currently supported list of QSC algorithms and the required nginx-server configuration file for a server running at the configured TESTFQDN server address.

*Note*: These scripts assume 
- coherent definition of test server FQDN as TESTFQDN in `genconfig.py` and `ext-csr.conf` files: By default "test.openquantumsafe.org" is set.
- presence of oqs-openssl common definitions file `common.py` (as stored at https://raw.githubusercontent.com/open-quantum-safe/oqs-provider/main/scripts/common.py).
- presence of Docker on the build machine to run the build process, the guest OS needs to be able to mount host directories for Docker (i.e. on Linux, SELinux permissions might be needed).
- presence on the target deploy server (i.e., at the machine designated at TESTFQDN) of a properly deployed [LetsEncrypt server certificate](https://letsencrypt.org/getting-started).

By default, the server is built to a specific set of versions of `liboqs`, `openssl`, `oqs-provider` and `nginx`. These versions are encoded in `build-ubuntu.sh` and may be changed/upgraded there.

### HOWTO

#### Build and deploy test server

On build machine run 

```
./build-ubuntu.sh
scp oqs-nginx-{LIBOQS_VERSION}.tgz yourid@yourserver:yourpath
```

At 'yourserver' run:
```
cd / && tar xzvf yourpath/oqs-nginx-{LIBOQS_VERSION}.tgz
cd /opt/nginx
/opt/nginx/sbin/nginx -c interop.conf
```

Note that, the oqs-nginx-{LIBOQS_VERSION}.tgz package contains all required configuration files and QSC certificates. **Unpacking the archive may overwrite an existing installation's configuration files. Use with care on a live server.**

#### Activation

Execute `/opt/nginx/sbin/nginx -c /opt/nginx/interop.conf` to start the test server.

*Note*: As the server many of ports, the server may need to be configured to permit this, e.g., using `ulimit -S -n 4096`.
