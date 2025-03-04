# Creating {{ PG }} clusters

{{ PG }} clusters are one or more database hosts that replication can be configured between. Replication is enabled by default in any cluster consisting of more than one host: the master host accepts write requests, then duplicates changes synchronously in the primary replica and asynchronously in all the others.

{% note info %}

If database storage is 95% full, the cluster switches to read-only mode. Plan and increase the required storage size in advance.

{% endnote %}


The number of hosts that can be created with a {{ PG }} cluster depends on the storage option selected:

* When using network drives, you can request any number of hosts (from one to the current [quota](../concepts/limits.md) limit).

* When using SSDs, you can create at least three replicas along with the cluster (a minimum of three replicas is required to ensure fault tolerance). If the [available folder resources](../concepts/limits.md) are still sufficient after creating a cluster, you can add extra replicas.

By default, {{ mpg-short-name }} limits the maximum number of connections to each {{ PG }} cluster host. This maximum is calculated as follows: `200 × <number of vCPUs per host>`. For example, for a [s1.micro class](../concepts/instance-types.md) cluster, the `max_connections` default parameter value is 400 and can't be increased.

{% include [note-pg-user-connections.md](../../_includes/mdb/note-pg-user-connections.md) %}

## How to create a {{ PG }} cluster {#create-cluster}

{% list tabs %}

- Management console

  1. In the management console, select the folder where you want to create a database cluster.

  1. Select **{{ mpg-name }}**.

  1. Click **Create cluster**.

  1. Enter a name for the cluster in the **Cluster name** field. The cluster name must be unique within the folder.

  1. Select the environment where you want to create the cluster (you can't change the environment once the cluster is created):
      - `PRODUCTION`: For stable versions of your apps.
      - `PRESTABLE`: For testing, including the {{ mpg-short-name }} service itself. The Prestable environment is first updated with new features, improvements, and bug fixes. However, not every update ensures backward compatibility.

  1. Select the DBMS version.
      {% note info %}

      When using version `10-1c` ({{ PG }} 10 for 1C), to comfortably host 50 users, we recommend selecting the `s2.medium` host class. For 30 users and less, the `s2.small` class is probably going to be enough.

      {% endnote %}

  1. Select the host class to define the technical specifications of the VMs where the database hosts will be deployed. All available options are listed in [{#T}](../concepts/instance-types.md). When you change the host class for the cluster, the characteristics of all existing hosts change, too.

  1. Under **Storage size**:


      - Select the type of storage, either a more flexible network type (**network-hdd** or **network-ssd**) or faster local SSD storage (**local-ssd**). The size of the local storage can only be changed in 100 GB increments.

      - Select the size to be used for data and backups. For more information about how backups take up storage space, see [{#T}](../concepts/backup.md).

  1. Under **Database**, specify the database attributes:

      - Database name. This name must be unique within the folder and contain only Latin letters, numbers, and underscores.

      - Username of the database owner. This name may only contain Latin letters, numbers, and underscores. By default, the new user is assigned 50 connections to each host in the cluster.

      - User password (from 8 to 128 characters).

      - Locale for sorting and character set locale. These settings define the rules for sorting strings (`LC_COLLATE`) and classifying characters (`LC_CTYPE`). In {{ mpg-name }}, locale settings apply at the level of an individual database.

           {% include [postgresql-locale](../../_includes/mdb/mpg-locale-settings.md) %}

  1. Under **Network settings**, select the cloud network to host the cluster in and security groups for cluster network traffic. You may need to additionally [set up the security groups](connect.md#configuring-security-groups) to connect to the cluster.

  1. Under **Hosts**, select parameters for the database hosts created with the cluster (keep in mind that if you use SSDs when creating a {{ PG }} cluster, you must set at least three hosts). If you open **Advanced settings**, you can choose specific subnets for each host. By default, each host is created in a separate subnet.

  1. If necessary, configure additional cluster settings:

     {% include [mpg-extra-settings](../../_includes/mdb/mpg-extra-settings-web-console.md) %}

  1. Click **Create cluster**.

- CLI

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  To create a cluster:

  
  1. Check whether the folder has any subnets for the cluster hosts:

     ```
     $ yc vpc subnet list
     ```

     If there are no subnets in the folder, [create the necessary subnets](../../vpc/operations/subnet-create.md) in {{ vpc-short-name }}.

  1. View a description of the CLI create cluster command:

      ```
      $ {{ yc-mdb-pg }} cluster create --help
      ```

  1. Specify the cluster parameters in the create command (only some of the supported parameters are given in the example):

  
      ```bash
      $ {{ yc-mdb-pg }} cluster create \
         --name <cluster name> \
         --environment <prestable or production> \
         --network-name <network name> \
         --host zone-id=<availability zone>,subnet-id=<subnet ID> \
         --resource-preset <host class> \
         --user name=<username>,password=<user password> \
         --database name=<database name>,owner=<database owner name> \
         --disk-size <storage size in GB> \
         --security-group-ids <list of security group IDs>
      ```
      
      The `subnet-id` should be specified if the selected availability zone contains two or more subnets.

      You can also specify some additional options in the `--host` parameter to manage replication in the cluster:
      - Replication source for the host in the `replication-source` option to [manually manage replication threads](../concepts/replication.md#replication-manual).
      - Host priority in the `priority` option to [influence the selection of a synchronous replica](../concepts/replication.md#selecting-the-master-and-a-synchronous-replica):
        - The host with the highest priority in the cluster becomes a synchronous replica.
        - If the cluster has multiple hosts with the highest priority, a synchronous replica is selected among them. 
        - The lowest and default priority is `0` and the highest is `100`.
      
      {% note warning %}
      
      When using the `--security-group-ids` option, you may need to [set up security groups](connect.md#configuring-security-groups) to connect to the cluster.
      
      {% endnote %}  

      You can also specify some additional options in the `--host` parameter to manage replication in the cluster:
      - Replication source for the host in the `replication-source` option to [manually manage replication threads](../concepts/replication.md#replication-manual).
      - Host priority in the `priority` option to [influence the selection of a synchronous replica](../concepts/replication.md#selecting-the-master-and-a-synchronous-replica):
        - The host with the highest priority value in the cluster becomes the synchronous replica.
        - If the cluster has multiple hosts with the highest priority, the synchronous replica is elected from among them. 
        - The lowest priority value is `0` (default) and the highest is `100`.
      
      {% note warning %}
      
      When using the `--security-group-ids` option, you may need to [set up the security groups](connect.md#configuring-security-groups) to connect to the cluster.
      
      {% endnote %}  

- Terraform

  {% include [terraform-definition](../../solutions/_solutions_includes/terraform-definition.md) %}

  If you don't have Terraform yet, [install it and configure the provider](../../solutions/infrastructure-management/terraform-quickstart.md#install-terraform).

  To create a cluster:

  1. In the configuration file, describe the parameters of resources that you want to create:

     {% include [terraform-create-cluster-step-1](../../_includes/mdb/terraform-create-cluster-step-1.md) %}

     Example configuration file structure:

     ```
     resource "yandex_mdb_postgresql_cluster" "<cluster name>" {
       name        = "<cluster name>"
       environment = "<PRESTABLE or PRODUCTION>"
       network_id  = "<network ID>"
     
       config {
         version = "<PostgreSQL version: 10, 10-1с, 11, 11-1c, 12, or 13>"
         resources {
           resource_preset_id = "<host class>"
           disk_type_id       = "<storage type>"
           disk_size          = "<storage size in GB>"
         }
       }
     
       database {
         name = "<database name>"
         owner = "<name of the database owner>"
       }
     
       user {
         name     = "<username>"
         password = "<user password>"
         permission {
           database_name = "<database name>"
         }
       }
     
       host {
         zone      = "<availability zone>"
         subnet_id = "<subnet ID>"
       }
     }
     
     resource "yandex_vpc_network" "<network name>" { name = "<network name>" }
     
     resource "yandex_vpc_subnet" "<subnet name>" {
       name           = "<subnet name>"
       zone           = "<availability zone>"
       network_id     = "<network ID>"
       v4_cidr_blocks = ["<range>"]
     }
     ```

     For more information about resources that you can create using Terraform, see the [provider's documentation](https://www.terraform.io/docs/providers/yandex/r/mdb_postgresql_cluster.html).

  1. Make sure that the configuration files are correct.

     {% include [terraform-create-cluster-step-2](../../_includes/mdb/terraform-create-cluster-step-2.md) %}

  1. Create a cluster.

     {% include [terraform-create-cluster-step-3](../../_includes/mdb/terraform-create-cluster-step-3.md) %}

{% endlist %}

## Examples {#examples}

### Creating a single-host cluster {#creating-a-single-host-cluster}

{% list tabs %}

- CLI

  To create a cluster with a single host, you should pass a single `--host` parameter.

  Let's say we need to create a {{ PG }} cluster with the following characteristics:

    - Named `mypg`.
  - In the `production` environment.
  - In the `default` network.
  - With one `{{ host-class }}` class host in the `b0rcctk2rvtr8efcch64` subnet in the `{{ zone-id }}` availability zone.
  - With 20 GB fast network storage (`{{ disk-type-example }}`).
  - With one user (`user1`) with the password `user1user1`.
  - With one `db1` database owned by the user `user1`.

  Run the command:

  
  ```
    {{ yc-mdb-pg }} cluster create \
    --name mypg \
    --environment production \
    --network-name default \
    --resource-preset {{ host-class }} \
    --host zone-id={{ zone-id }},subnet-id=b0rcctk2rvtr8efcch64 \
    --disk-type {{ disk-type-example }} \
    --disk-size 20 \
    --user name=user1,password=user1user1 \
    --database name=db1,owner=user1
  ```

- Terraform

  Let's say we need to create a {{ PG }} cluster and a network for it with the following characteristics:
  - Named `mypg`.
  - Version `13`.
  - In the `PRESTABLE` environment.
  - In the cloud with the ID `{{ tf-cloud-id }}`.
  - In a folder named `myfolder`.
  - Network: `mynet`.
  - With 1 `{{ host-class }}` class host in the new `mysubnet` subnet and `{{ zone-id }}` availability zone. The `mysubnet` subnet will have the range `10.5.0.0/24`.
  - With 20 GB fast network storage (`{{ disk-type-example }}`).
  - With one user (`user1`) with the password `user1user1`.
  - With one `db1` database owned by the user `user1`.

  The configuration file for the cluster looks like this:

  ```
  provider "yandex" {
    token = "<OAuth or static key of service account>"
    cloud_id  = "{{ tf-cloud-id }}"
    folder_id = "${data.yandex_resourcemanager_folder.myfolder.id}"
    zone      = "{{ zone-id }}"
  }
  
  resource "yandex_mdb_postgresql_cluster" "mypg" {
    name        = "mypg"
    environment = "PRESTABLE"
    network_id  = "${yandex_vpc_network.mynet.id}"
  
    config {
      version = 13
      resources {
        resource_preset_id = "{{ host-class }}"
        disk_type_id       = "{{ disk-type-example }}"
        disk_size          = 20
      }
    }
  
    database {
      name  = "db1"
      owner = "user1"
    }
  
    user {
      name     = "user1"
      password = "user1user1"
      permission {
        database_name = "db1"
      }
    }
  
    host {
      zone      = "{{ zone-id }}"
      subnet_id = "${yandex_vpc_subnet.mysubnet.id}"
    }
  }
  
  resource "yandex_vpc_network" "mynet" {  name = "mynet" }
  
  resource "yandex_vpc_subnet" "mysubnet" {
    name           = "mysubnet"
    zone           = "{{ zone-id }}"
    network_id     = "${yandex_vpc_network.mynet.id}"
    v4_cidr_blocks = ["10.5.0.0/24"]
  }
  ```

{% endlist %}

