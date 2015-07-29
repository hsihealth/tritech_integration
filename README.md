#Syncordia/Medlert Tri-Tech API

### Overview

Syncordia will be creating a suite of integrated efficiency and performance optimized web applications to improve our billing processes. At the same time we wish to leverage our existing investment in Tri-Tech Billing. 

For this to be possible, we must be able to enhance our existing read-only interfaces with Tri-Tech Resolve Billing to include Write capabilities as well so that our custom applications can fully interact with claims hosted in Tri-Tech Resolve Billing without the need for human intervention.

Although Tri-Tech Resolve Billing exposes an import interface that includes the majority of the data points we required, this interface is strictly a one-time, manually initiated data import and does not provide us with the ability to continuously interact with claims over the course of time or in a way that eliminates the need for a person to manage the interactions between our system as Tri-Tech.

Therefore we are hopeful that, given Medlerts extensive experience directly interacting with the Tri-Tech database, a partnership can help us achieve our goals.

### Needs

As I mentioned Tri-Tech exposes an import mechanism that exposes most of the data points we need. Therefore it is a great place to start as far as a requirements specification - we would like the ability to read and write the fields specified in their import documentation but on a more flexible basis, bypassing the actual import mechanism.

Additionally we will need to ability to manipulate some of the lookup tables / data dictionaries so that we can manage some of these entities in our own software, for example adding Payors or Physicians.

This repository contains a few files that we can use to begin our conversation.

First there is the official Tri-tech documentation for their import that lists the available tables and fields, the majority of which we would like to include in any API ([amazon_ascii_layout_v1.xls](https://github.com/hsihealth/tritech_integration/blob/master/amazon_ascii_layout_v1.xls))

Second, there is my list of tables (taken partly from the prior file) that lists the transactional and reference tables I think we would need to complete the API. ([needed_read_write_tables.md](https://github.com/hsihealth/tritech_integration/blob/master/needed_read_write_tables.md))

Finally, for reference, there is the set of SQL scripts we currently use in our existing read only integration with Tri-Tech - this should give you a detailed picture of what data we are using and how we are using it. ([existing_read_only_queries.md](https://github.com/hsihealth/tritech_integration/blob/master/existing_read_only_queries.md))

