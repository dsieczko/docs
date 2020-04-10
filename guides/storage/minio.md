<!-- <meta>
{
    "title":"Object Storage with Minio",
    "description":"Deploy, Setup, & Configure Minio Object Storage Server",
    "tag":["S3", "Object Storage", "Minio"],
    "seo-title": "Object Storage with Minio - Packet Technical Guides",
    "seo-description": "Deploy, Setup, & Configure Minio Object Storage Server",
    "og-title": "Object Storage with Minios",
    "og-description": Deploy, Setup, & Configure Minio Object Storage Servert"
}
</meta> -->

MinIO is a high performance distributed object storage server, designed for large-scale private cloud infrastructure. MinIO is designed in a cloud-native manner to scale sustainably in multi-tenant environments. 


#### Installing & Configuring Minio 

You can install the Minio server by compiling the source code or via a binary file. For this guide, we will make use of the binary and we will be deploying on a server running Ubuntu 18.04.

First, if you haven't updated the package database recently, update it now:

```
apt get update
```

Next, download the Minio server binary:

```
curl -O https://dl.minio.io/server/minio/release/linux-amd64/minio
```

You will need to change permissions for the file you just downloaded to make it executable:

```
chmod +x minio
```

Next, the binary will need to be relocated to the `/usr/local/bin` directory where the startup script expects to find it. We'll be downloading the startup script below.

It is not suggested to run the Minio server as root. Instead, we'll create a user called `minio-user`.

This is done by running: 

```
useradd -r minio-user -s /sbin/nologin
```

Dependent upon your particular setup, the creation of the storage directory may vary.

The directory in which you wish Minio storage to run will need to have its permissions adjusted to allow `minio-user` access.

```
chown minio-user:minio-user /path/to/storage
```

Next up, we will need to create a Minios configuration file. Typically, the most common location for this file is in `/etc/`. Make sure this location has the same permissions as the storage directory for `minio-user`.

An example of the Minio configuration: 

```
MINIO_VOLUMES="/usr/local/share/minio/"
MINIO_OPTS="-C /etc/minio --address your-server-ip:9000"
```

* MINIO_VOLUMES: is the directory (path) where your files will reside. 

* MINIO_OPTS: is the Minio server's IP address and port. 


#### Minio Startup Script

First up, download the descrpitor file: 

```
curl -O https://raw.githubusercontent.com/minio/minio-service/master/linux-systemd/minio.service
````

Double check the contents of this file. Once you are satisified with the content, the file will then need to be moved to `/etc/systemd/system`.

To ensure systemd knows of the new startup script, you'll need to reload the systemd units:

```
systemctl daemon-reload
```

Also, to ensure that the Minio Server automatically starts at reboot, add it to systemctl:

```
systemctl enable minio
```

This completes the install, and setup process for Minio. You should now be able to start the service!

```
systemctl start minio
```

Double check that is in fact running:

```
systemctl status minio
```

You should see an output similar to: 

```
 minio.service - MinIO
   Loaded: loaded (/etc/systemd/system/minio.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2020-04-09 15:31:45 UTC; 1 day 3h ago
     Docs: https://docs.min.io
 Main PID: 2757 (minio)
    Tasks: 20 (limit: 4915)
   CGroup: /system.slice/minio.service
           └─2757 /usr/local/bin/minio server -C /etc/minio --address s3.example.com:443 /usr/local/minio/data
```



More information about Minio is available at the [project’s documentation website](https://docs.minio.io/) & information about how to secure your Minio Server can be found at [Let's Encrypt](https://docs.min.io/docs/generate-let-s-encypt-certificate-using-concert-for-minio.html). 
