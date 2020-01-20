# How to use CDSW

## Accessing to CDSW

Simply, you may access to CDSW via the link on Cloudera Manager.

```
$ ./get_cluster_ip.sh ${CLUSTER_CONF}
[Cloudera Manager]
Public IP: 54.65.50.23    Private IP: 10.0.0.142
CM URL: http://10.0.0.142:7180

[Nodes]
    master    Public IP:   13.112.189.230    Private IP:       10.0.0.124
    worker    Public IP:     54.248.19.28    Private IP:       10.0.0.243
    worker    Public IP:    54.238.98.138    Private IP:       10.0.0.157
    worker    Public IP:    13.230.218.46    Private IP:       10.0.0.108
      cdsw    Public IP:    13.230.82.112    Private IP:        10.0.0.44

[CDSW]
CDSW URL: http://cdsw.10.0.0.44.xip.io

[SSH Tunnel for Dynamic Port Forwarding]
ssh -i your-aws-sshkey.pem -D 8157 -q centos@54.65.50.23

$ ssh -i your-aws-sshkey.pem -D 8157 -q centos@54.65.50.23
```

After above, you can access to http://cdsw.10.0.0.44.xip.io from your web browser via SSH SOCKS Proxy (See https://www.cloudera.com/documentation/director/latest/topics/director_security_socks.html).

## Logging into CDSW

### Secure Cluster
1. Access to CDSW from browser.
2. Click "**Sign Up for a New Account**" and create a new account. This username doesn't relate to existing OS users or Kerberos principals. Therefore you can create any users.
    - Full Name: (anything)
    - Username: (anything)
    - Email:  (anything)
    - Password: (anything)
    
3. After logging in, authenticate against your cluster’s Kerberos KDC by going to the top-right dropdown menu and clicking **Settings** -> **Hadoop Authentication**. You can use the prepared principals `user<N>@HADOOP`. Please read the following "User and Authentication" section of README. In case you use `user1`:
    
    - Principal: user1@HADOOP
    - Password: user1
    
### Unsecure Cluster
1. Access to CDSW from browser.
2. Click "**Sign Up for a New Account**" and create a new account. This username *DOES* relate to existing OS users. In case you use `user1`:
    
    - Full Name: (anything)
    - Username: user1
    - Email: (anything)
    - Password: (anything)
3. It's okay to remain blank in **Settings** -> **Hadoop Authentication**.
 



## Setting up for GPU

### Custom Engine Image

You need to have a custom CUDA-capable Engine Image. You can refer to my [Notes on building the image](./cdsw-engine.md) as well as Cloudera Documentation below.

**Cloudera Documentaion:**
 [Using NVIDIA GPUs for Cloudera Data Science Workbench Projects](https://docs.cloudera.com/documentation/data-science-workbench/1-6-x/topics/cdsw_gpu.html#custom_cuda_engine)

You may want to access the latest information:
[Using NVIDIA GPUs for Cloudera Data Science Workbench Projects](https://docs.cloudera.com/documentation/data-science-workbench/latest/topics/cdsw_gpu.html#custom_cuda_engine)


### CDSW Settings

#### Engine Images

**Admin** -> **Engines** -> **Engine Images**

I built a sample CUDA-capable engine image. You may use [my image](https://hub.docker.com/r/yoshiyukikono/cdsw-cuda/) (`yoshiyukikono/cdsw-cuda:8`).


**Note:** When you start a new session for the first time, it will take pretty longer time than usual beause the above engine image is downloaded at that time.

##### Jupyter notebook
If you want to use Jupyter notebook with custome engine images, you need to add an Editor entry.

1. Under Engine Images, click the Edit button for the customized engine image that you want to configure for Jupyter Notebook.
1. Click New Editor.
1. Complete the fields:

Name: Enter `Jupyter Notebook`.
Command: Enter the command to start Jupyter Notebook. This command is:
```
/usr/local/bin/jupyter-notebook --no-browser --ip=127.0.0.1 --port=${CDSW_APP_PORT} --NotebookApp.token= --NotebookApp.allow_remote_access=True --log-level=ERROR
```

#### Maximum GPUs
**Admin** -> **Engines** -> **Engine Profiles** -> **Maximum GPUs per Session/Job**

You must increase the number from 0 to 1 or higher.


For more details, please read the Cloudera documentation mentioned above.

## Testing GPU setup

To confirm if GPU is used properly on your environment, please refer to [Notes on building the image](./cdsw-engine.md).



## Checking CDSW Status

On the CDSW node, you can check the status of CDSW using `cdsw status`.
The following example is the output of `cdsw status` after installation finished (details are different depending on the version).

```
$ sudo cdsw status
cdsw status
Sending detailed logs to [/tmp/cdsw_status_HAmpRL.log] ...
CDSW Version: [1.3.0:9bb84f6]
OK: Application running as root check
OK: Sysctl params check
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
|        NAME       |   STATUS   |           CREATED-AT          |   VERSION   |   EXTERNAL-IP   |          OS-IMAGE         |         KERNEL-VERSION         |   GPU   |   STATEFUL   |
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
|   ip-10-0-0-167   |    True    |   2018-01-28 14:40:09+00:00   |   v1.6.11   |       None      |   CentOS Linux 7 (Core)   |   3.10.0-514.16.1.el7.x86_64   |    0    |     True     |
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
1/1 nodes are ready.
-------------------------------------------------------------------------------------------------------------------------------------------------------------------
|                    NAME                   |   READY   |    STATUS   |   RESTARTS   |           CREATED-AT          |     POD-IP     |    HOST-IP     |   ROLE   |
-------------------------------------------------------------------------------------------------------------------------------------------------------------------
|             etcd-ip-10-0-0-167            |    1/1    |   Running   |      0       |   2018-01-28 14:41:34+00:00   |   10.0.0.167   |   10.0.0.167   |   None   |
|        kube-apiserver-ip-10-0-0-167       |    1/1    |   Running   |      0       |   2018-01-28 14:40:08+00:00   |   10.0.0.167   |   10.0.0.167   |   None   |
|   kube-controller-manager-ip-10-0-0-167   |    1/1    |   Running   |      0       |   2018-01-28 14:40:07+00:00   |   10.0.0.167   |   10.0.0.167   |   None   |
|         kube-dns-3911048160-krs31         |    3/3    |   Running   |      0       |   2018-01-28 14:40:24+00:00   |   100.66.0.3   |   10.0.0.167   |   None   |
|              kube-proxy-lzq95             |    1/1    |   Running   |      0       |   2018-01-28 14:40:24+00:00   |   10.0.0.167   |   10.0.0.167   |   None   |
|        kube-scheduler-ip-10-0-0-167       |    1/1    |   Running   |      0       |   2018-01-28 14:40:07+00:00   |   10.0.0.167   |   10.0.0.167   |   None   |
|      node-problem-detector-v0.1-h8mbg     |    1/1    |   Running   |      0       |   2018-01-28 14:41:48+00:00   |   10.0.0.167   |   10.0.0.167   |   None   |
|              weave-net-91xmq              |    2/2    |   Running   |      0       |   2018-01-28 14:40:24+00:00   |   10.0.0.167   |   10.0.0.167   |   None   |
-------------------------------------------------------------------------------------------------------------------------------------------------------------------
All required pods are ready in cluster kube-system.
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
|                  NAME                  |   READY   |     STATUS    |   RESTARTS   |           CREATED-AT          |      POD-IP     |    HOST-IP     |           ROLE           |
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
|         cron-1906902965-qxnjd          |    1/1    |    Running    |      0       |   2018-01-28 14:41:47+00:00   |    100.66.0.4   |   10.0.0.167   |           cron           |
|          db-1165222207-99m93           |    1/1    |    Running    |      0       |   2018-01-28 14:41:47+00:00   |    100.66.0.5   |   10.0.0.167   |            db            |
|        db-migrate-9bb84f6-6lhbr        |    0/1    |   Succeeded   |      0       |   2018-01-28 14:41:47+00:00   |    100.66.0.7   |   10.0.0.167   |        db-migrate        |
|           engine-deps-ctmvq            |    1/1    |    Running    |      0       |   2018-01-28 14:41:47+00:00   |    100.66.0.6   |   10.0.0.167   |       engine-deps        |
|   ingress-controller-684706958-zl9l3   |    1/1    |    Running    |      0       |   2018-01-28 14:41:47+00:00   |    10.0.0.167   |   10.0.0.167   |    ingress-controller    |
|        livelog-2502658797-ltqg5        |    1/1    |    Running    |      0       |   2018-01-28 14:41:47+00:00   |    100.66.0.8   |   10.0.0.167   |         livelog          |
|      reconciler-2738760185-lqlmz       |    1/1    |    Running    |      0       |   2018-01-28 14:41:47+00:00   |    100.66.0.9   |   10.0.0.167   |        reconciler        |
|       spark-port-forwarder-mx286       |    1/1    |    Running    |      0       |   2018-01-28 14:41:48+00:00   |    10.0.0.167   |   10.0.0.167   |   spark-port-forwarder   |
|          web-3320989329-698m4          |    1/1    |    Running    |      0       |   2018-01-28 14:41:48+00:00   |   100.66.0.11   |   10.0.0.167   |           web            |
|          web-3320989329-cqtpz          |    1/1    |    Running    |      0       |   2018-01-28 14:41:48+00:00   |    100.66.0.7   |   10.0.0.167   |           web            |
|          web-3320989329-gwmfm          |    1/1    |    Running    |      0       |   2018-01-28 14:41:48+00:00   |   100.66.0.10   |   10.0.0.167   |           web            |
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
All required pods are ready in cluster default.
All required Application services are configured.
All required config maps are ready.
All required secrets are available.
Persistent volumes are ready.
Persistent volume claims are ready.
Ingresses are ready.
Checking web at url: http://cdsw.10.0.0.167.xip.io
OK: HTTP port check
Cloudera Data Science Workbench is ready!
```
