# postgresql-zerodown-upgrades
==============================
 
# Overview


This repository has been evolved from [our-postgresql-setup](https://github.com/gocardless/our-postgresql-setup). Thanks @CrisSinjo / GoCardless.

It helps you quickly spin up a 3-node cluster of PostgreSQL, managed by Pacemaker, and proxied by PgBouncer/PgPool

# What's in the cluster?

When you start the cluster, you get 3 nodes, each running:

  - PostgreSQL
  - Pacemaker
  - PgBouncer
  - PgPool
  - PgAgent


Codes tested in Centos 7 (PostgreSQL 10) and Ubuntu 16.04 (PostgreSQL 9.4) .

The cluster is configured with a single primary, one synchronous replica, and one asynchronous replica.

# Dependencies
1. [Virtualbox](https://www.virtualbox.org/wiki/Downloads)
2. [Vagrant](http://www.vagrantup.com/downloads.html)
3. ` git clone https://github.com/gocardless/our-postgresql-setup.git`
4. [Recommended] [tmux](https://tmux.github.io)

# Getting started

## With tmux (recommended)
1.  `./tmux-session.sh start`

## By hand
1.  On 3 separate windows:
2.  `vagrant up pg01 && vagrant ssh pg01`
3.  `vagrant up pg02 && vagrant ssh pg02`
4.  `vagrant up pg03 && vagrant ssh pg03`

# Viewing cluster status

You can run `crm_mon -Afr` on any node to see the current state of the cluster and all resources in it. Press `^c` to quit.

# Connecting to PostgreSQL

Once the cluster is up, you have two options:

  - Connect directly to Postgres on the PostgresqlVIP at 172.28.33.10
  - Connect via PgBouncer at 172.28.33.9

*Note*: The migrator.py script will only give you zero-downtime migrations if you connect via PgBouncer.

# Running a zero-downtime migration

1. Ensure clients are connected to the PgBouncerVIP.
2. Run `/vagrant/migrator.py` on the node that has the PgBouncerVIP (you can find out where the PgBouncerVIP is by viewing the cluster status).
3. Follow the prompts.
    1. It is safe to ignore the `Make sure you have the following command ready...` prompt. This is aimed at cases where you'd want to quickly re-enable traffic, and doesn't matter when running locally.
4. Assuming everything went well, the primary will migrate to the synchronous replica, and the clients won't have received any connection resets.


# Further reading
* [Pacemaker CRM shell Quick Reference](https://github.com/ClusterLabs/pacemaker/blob/master/doc/pcs-crmsh-quick-ref.md)

# References
* [PostgreSQL](https://www.postgresql.org)
* [tmux](https://tmux.github.io)
* [Vagrant](http://vagrantup.com)
* [VirtualBox](http://www.virtualbox.org)
