---
layout: post
title: Useful PQL Queries
date: 2022-07-01
summary: A community-collection of PQL queries
---

## What?

PQL is the [Puppet Query Language](https://puppet.com/docs/puppetdb/latest/api/query/examples-pql.html). You can use it to query the PuppetDB for information.

## Why?

The syntax isn't alway very intuitive. That's why this page exists. It serves as a central place where the community can find and submit PQL queries!

## How?

All queries here are documented in the [string-based query style](https://puppet.com/docs/puppetdb/7/api/query/v4/pql.html), not the [AST](https://puppet.com/docs/puppetdb/7/api/query/v4/ast.html) style. You need the [Puppet Client tools](https://puppet.com/docs/puppetdb/7/pdb_client_tools.html) (for Open Source or PE) to get the `puppet query` face. To install the client tools on Open Source, you can do:

```
puppet resource package puppetdb_cli ensure=present provider=puppet_gem
```

And Afterwards run the query with `/opt/puppetlabs/puppet/bin/puppet-query $query`. For PE installations you can use `puppet query`.

On the PuppetServer side, installing the `puppetdb-termini` package allow to use the `puppetdb_query()` function in a manifest to generate portions of catalog based on result of queries, e.g. find all Virtual-Hosts on all machines and add the relevant configuration to a monitoring system.

## Contribute?!

You can submit your own Queries by editing [voxpupuli.github.io/_docs/pql_queries.md](https://github.com/voxpupuli/voxpupuli.github.io/blob/master/_docs/pql_queries.md) on GitHub or by pressing the edit button in the upper right corner.

## Query Collection

### List all certificate names from all known nodes

```
puppet query 'nodes[certname] {}'
```

Result:

```
[
  {
    "certname": "puppetserver.bastelfreak.org"
  },
  {
    "certname": "puppetdb.bastelfreak.org"
  }
]
```

### Get all nodes that have a specific class in their catalog

```
puppet query 'nodes[certname] {resources {type = "class" and title = "CapitalizedClassname"}}'
```

### Get all nodes that have a specific class in their catalog and start with bla-

```
puppet query 'nodes[certname] {certname ~ "^bla-" and resources {type = "class" and title = "CapitalizedClassname"}}'
```

### Get all nodes with changes and a specific resource

```
puppet query 'nodes[certname] {latest_report_status = "changed" and certname in inventory[certname]{resources { type = "Service" and title = "my_service"}}}'
```

### Get all nodes where one specific resource changed

```
puppet query 'events[certname] {resource_type = "Service" and resource_title = "apache2" and latest_report? = true and corrective_change = true}'
```
