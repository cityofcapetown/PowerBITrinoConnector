# Power BI Trino
A Microsoft Power BI Custom Connector for importing Trino data into Power BI to interactively transform, visualize and analyze data. 

## Trino client REST API
The connector communicates directly with the [Trino client REST API](https://trino.io/docs/current/develop/client-protocol.html) to retrieve data and provides some pararmeters to configure. Client timeout errors (ABANDONED_QUERY) can be fixed by changing the value of query.client.timeout in the coordinators config.properties file (the default is 5 minutes).

## Authentication
 The supported [authentication kinds](https://learn.microsoft.com/en-us/power-query/handling-authentication#authentication-kinds) are Anonymous, Basic (UsernamePassword) and OAuth.

## Need further support?
In case you encounter any issues while installing or loading data, just open an issue or feel free to reach out to one of the contributors of this repository. 

## Usage: Power BI Desktop

See [the installation guide]().

**NB** these instructions are specific to the City of Cape Town, and assumes you're connected to the City network.

## Further development

This is actually a relatively simple design - we're just templating [this file](./Trino.pq.tmpl) with the appropriate OAuth secrets at build time.

Hence, if you would like to modify, simply make the relevant changes to the template and trigger a Jenkins build, or apply the steps locally.

## Releases

**October 2021**
- 2021-10-15: First Release

**April 2023**
- 2023-04-26: OAuth added

**February 2024**
- 2024-02-07: Native SQL Query parameter added
- 2024-02-25: City of Cape Town Fork
