# *Percona Operator for MySQL based on Percona XtraDB Cluster* 1.13.0

* **Date**

   July 11, 2023

* **Installation**

   [Installing Percona Operator for MySQL based on Percona XtraDB Cluster](../System-Requirements.md#installation-guidelines)

## Release Highlights

* It is now [possible to control](../operator.md#backup-allowparallel) whether backup jobs are executed  in parallel or sequentially, which can be useful to avoid the cluster overload; also, CPU and memory resource limits can now be configured for the backup restore job
* A substantial improvement of the [backup documentation](../backups.md) was done in this release, making it much easier to read, and the [backup restore options](../operator.md#perconaxtradbclusterrestore-custom-resource-options) have been added to the Сustom Resource reference
* We are deeply committed to delivering software that truly sets the bar for quality and stability. With our latest release, we put an all-hands-on-deck approach towards fine-tuning the Operator with minor improvements, along with addressing key bugs reported by our vibrant community. We are extremely grateful to each and every person who submitted feedback and collaborated to help us get to the bottom of these pesky issues.

## New Features and improvements

* {{ k8spxcjira(1088) }}: It is now possible to configure CPU and memory resources for the backup restore job in the PerconaXtraDBClusterRestore Custom Resource options
* {{ k8spxcjira(1166) }}: Starting from now, Docker image tags for Percona XtraBackup include full XtraBackup version instead of the major number used before
* {{ k8spxcjira(1189) }}: Improve security and meet compliance requirements by building the Operator based on Red Hat Universal Base Image (UBI) 9 instead of UBI 8
* {{ k8spxcjira(1192) }}: Backup and restore documentation was substantially improved to make it easier to work with, and [backup restore options](../operator.md#perconaxtradbclusterrestore-custom-resource-options) have been added to the Сustom Resource reference
* {{ k8spxcjira(1210) }}: A [headless service](https://kubernetes.io/docs/concepts/services-networking/service/#headless-services) can now be configured for [ProxySQL](../proxysql-conf.md/headless-service) and [HAProxy](../haproxy-conf.md/headless-service) to make them usable on a tenant network (thanks to Vishal Anarase for contribution)
* {{ k8spxcjira(1225) }}: The Operator (system) users are now created with the `PASSWORD EXPIRE NEVER` policy to avoid breaking the cluster due to the password expiration set by the `default_password_lifetime` system variable
* {{ k8spxcjira(362) }}: Code clean-up and refactoring for checking if ProxySQL and HAProxy enabled in the Custom Resource (thanks to Vladislav Safronov for contributing)
* {{ k8spxcjira(1224) }}: New `backup.allowParallel` Custom Resource option allows to disable running backup jobs in parallel, which can be useful to avoid connection issues caused by the cluster overload
* {{ k8spxcjira(1183) }}: The Operator now uses the [caching_sha2_password](https://dev.mysql.com/doc/refman/8.0/en/caching-sha2-pluggable-authentication.html) authentication plugin for MySQL 8.0 instead of the older [mysql_native_password](https://dev.mysql.com/doc/refman/8.0/en/native-pluggable-authentication.html) one

## Bugs Fixed

* {{ k8spxcjira(1179) }} and {{ k8spxcjira(1183) }}: Fix a bug due to which the Operator didn't use TLS encryption for system users
* {{ k8spxcjira(1188) }}: The database Helm chart has improved defaults, including the use of random passwords generated by the Operator, and disabling `delete-pxc-pvc` and `delete-proxysql-pvc` finalizers to avoid possible data loss during migration
* {{ k8spxcjira(1220) }}: Fix a bug due to which DNS resolution problem could force HAProxy to remove all Percona XtraDB Cluster instances, including healthy ones
* {{ k8spxcjira(1164) }}: Fix a bug which caused the Operator to recreate Secrets in case of the ProxySQL to  HAProxy switch with active `delete-proxysql-pvc` finalizer
* {{ k8spxcjira(1255) }}: The log rotation was broken for the audit log, causing it to be written to the old file after the rotation
* {{ k8spxcjira(687) }}: Fix a bug which caused the backup restoration not starting in the environment which previously had a cluster with a failed restore
* {{ k8spxcjira(835) }} and {{ k8spxcjira(1029) }}: Fix a bug which prevented using ProxySQL on the replica cluster in cross-site replication
* {{ k8spxcjira(989) }}: Fix a bug which caused on-demand (manual) backup to fail in IPv6-enabled (dual-stack) environments because of the backup script unable to figure out the proper Pod IPv4 address (thanks to Song Yang for contribution)
* {{ k8spxcjira(1106) }}: Fix a bug which caused point-in-time recovery failure in case of a corrupted binlog file in `/var/lib/mysql`
* {{ k8spxcjira(1122) }}: Fix a bug which made disabling verification of the storage server TLS certificate with `verifyTLS` PerconaXtraDBClusterRestore Custom Resource option not working
* {{ k8spxcjira(1135) }}: Fix a bug where a cluster could incorrectly get a READY status while it had a service with an external IP still in pending state
* {{ k8spxcjira(1149) }}: Fix `delete-pxc-pvc` finalizer unable to delete TLS Secret used for external communications in case if this Secret had non-customized default name
* {{ k8spxcjira(1161) }}: Fix a bug due to which PMM couldn't continue monitoring HAProxy Pods after the [PMM Server API key change](../monitoring#operator-monitoring-client-token)
* {{ k8spxcjira(1163) }}: Fix a bug that made it impossible to delete the cluster in init state in case of enabled finalizers
* {{ k8spxcjira(1199) }}: Fix a bug due to which the Operator couldn't restore backups from Azure blob storage if `spec.backupSource.azure.container` was not specified 
* {{ k8spxcjira(1205) }}: Fix a bug which made the Operator to ignore the `verifyTLS` option for backups deletion caused by the `delete-s3-backup` finalizer (thanks to Christ-Jan Prinse for reporting)
* {{ k8spxcjira(1229) }} and {{ k8spxcjira(1197) }}: Fix a bug due to which the Operator was unable to delete backups from Azure blob storage
* {{ k8spxcjira(1236) }}: Fix the pxc container entrypoint script printing passwords into the standard output
* {{ k8spxcjira(1242) }}: Fix a bug due to which the unquoted password value was passed to the pmm-admin commands, making PMM Client unable to add MySQL service
* {{ k8spxcjira(1243) }}: Fix a bug which prevented deleting PMM agent from the PMM Server inventory on Pod termination
* {{ k8spxcjira(1126) }}: Fix a bug that `pxc-db` Helm chart had PVC-based backup storage enabled by default, which could be inconvenient for the users storing backups in cloud
* {{ k8spxcjira(1265) }}: Fix a bug due to which get pxc-backup command could show backup as failed after the first unsuccessful attempt while backup job was continuing attempts

## Known issues and limitations

* {{ k8spxcjira(1183) }}: Switching between HAProxy and ProxySQL load balancer can’t be done on existing clusters because ProxySQL does not yet support [caching_sha2_password](https://dev.mysql.com/doc/refman/8.0/en/caching-sha2-pluggable-authentication.html) authentication plugin; this makes it necessary to choose load balancer at the cluster creation time

## Supported Platforms

The Operator was developed and tested with Percona XtraDB Cluster versions 8.0.32-24.2 and 5.7.42-31.65. Other options may also work but have not been tested. Other software components include:

* Percona XtraBackup versions 2.4.28 and 8.0.32-26
* HAProxy 2.6.12
* ProxySQL 2.5.1-1.1
* LogCollector based on fluent-bit 2.1.5
* PMM Client 2.38

The following platforms were tested and are officially supported by the Operator
1.13.0:

* [Google Kubernetes Engine (GKE)](https://cloud.google.com/kubernetes-engine) 1.24 - 1.27
* [Amazon Elastic Container Service for Kubernetes (EKS)](https://aws.amazon.com) 1.23 - 1.27
* [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/en-us/services/kubernetes-service/) 1.24 - 1.26
* [OpenShift](https://www.redhat.com/en/technologies/cloud-computing/openshift) 4.10 - 4.13
* [Minikube](https://minikube.sigs.k8s.io/docs/) 1.30 (based on Kubernetes 1.27)

This list only includes the platforms that the Percona Operators are specifically tested on as part of the release process. Other Kubernetes flavors and versions depend on the backward compatibility offered by Kubernetes itself.
