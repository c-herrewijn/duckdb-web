---
layout: docu
title: Tableau – A Data Visualization Tool
---

[Tableau](https://www.tableau.com/) is a popular commercial data visualization tool.
In addition to a large number of built in connectors,
it also provides generic database connectivity via ODBC and JDBC connectors.

Tableau has two main versions: Desktop and Online (Server).

* For Desktop, connecting to a DuckDB database is similar to working in an embedded environment like Python.
* For Online, since DuckDB is in-process, the data needs to be either on the server itself

or in a remote data bucket that is accessible from the server.

## Database Creation

When using a DuckDB database file
the data sets do not actually need to be imported into DuckDB tables;
it suffices to create views of the data.
For example, this will create a view of the `h2oai` Parquet test file in the current DuckDB code base:

```sql
CREATE VIEW h2oai AS (
    FROM read_parquet('/Users/username/duckdb/data/parquet-testing/h2oai/h2oai_group_small.parquet')
);
```

Note that you should use full path names to local files so that they can be found from inside Tableau.
Also note that you will need to use a version of the driver that is compatible (i.e., from the same release)
as the database format used by the DuckDB tool (e.g., Python module, command line) that was used to create the file.

## Installing the JDBC Driver

Tableau provides documentation on how to [install a JDBC driver](https://help.tableau.com/current/pro/desktop/en-gb/jdbc_tableau.htm)
for Tableau to use.

> Tableau (both Desktop and Server versions) need to be restarted any time you add or modify drivers.

### Driver Links

The link here is for a recent version of the JDBC driver that is compatible with Tableau.
If you wish to connect to a database file,
you will need to make sure the file was created with a file-compatible version of DuckDB.
Also, check that there is only one version of the driver installed as there are multiple filenames in use.

<!-- markdownlint-disable MD034 -->
Download the [JAR file](https://repo1.maven.org/maven2/org/duckdb/duckdb_jdbc/{{ site.current_duckdb_java_version }}/duckdb_jdbc-{{ site.current_duckdb_java_version }}.jar).
<!-- markdownlint-enable MD034 -->

* macOS: Copy it to `~/Library/Tableau/Drivers/`
* Windows: Copy it to `C:\Program Files\Tableau\Drivers`
* Linux: Copy it to `/opt/tableau/tableau_driver/jdbc`.

## Using the PostgreSQL Dialect

If you just want to do something simple, you can try connecting directly to the JDBC driver
and using Tableau-provided PostgreSQL dialect.

1. Create a DuckDB file containing your views and/or data.
2. Launch Tableau
3. Under Connect > To a Server > More… click on “Other Databases (JDBC)” This will bring up the connection dialogue box. For the URL, enter `jdbc:duckdb:/User/username/path/to/database.db`. For the Dialect, choose PostgreSQL. The rest of the fields can be ignored:

![Tableau PostgreSQL](/images/guides/tableau/tableau-osx-jdbc.png)

However, functionality will be missing such as `median` and `percentile` aggregate functions.
To make the data source connection more compatible with the PostgreSQL dialect,
please use the DuckDB taco connector as described below.

## Installing the Tableau DuckDB Connector

While it is possible to use the Tableau-provided PostgreSQL dialect to communicate with the DuckDB JDBC driver,
we strongly recommend using the [DuckDB "taco" connector](https://github.com/motherduckdb/duckdb-tableau-connector).
This connector has been fully tested against the Tableau dialect generator
and [is more compatible](https://github.com/motherduckdb/duckdb-tableau-connector/blob/main/tableau_connectors/duckdb_jdbc/dialect.tdd)
than the provided PostgreSQL dialect.

The documentation on how to install and use the connector is in its repository,
but essentially you will need the
[`duckdb_jdbc.taco`](https://github.com/motherduckdb/duckdb-tableau-connector/raw/main/packaged-connector/duckdb_jdbc-v1.0.0-signed.taco) file.
(Despite what the Tableau documentation says, the real security risk is in the JDBC driver code,
not the small amount of JavaScript in the Taco.)

### Server (Online)

On Linux, copy the Taco file to `/opt/tableau/connectors`.
On Windows, copy the Taco file to `C:\Program Files\Tableau\Connectors`.
Then issue these commands to disable signature validation:

```bash
tsm configuration set -k native_api.disable_verify_connector_plugin_signature -v true
```

```bash
tsm pending-changes apply
```

The last command will restart the server with the new settings.

### macOS

Copy the Taco file to the `/Users/[User]/Documents/My Tableau Repository/Connectors` folder.
Then launch Tableau Desktop from the Terminal with the command line argument to disable signature validation:

```bash
/Applications/Tableau\ Desktop\ ⟨year⟩.⟨quarter⟩.app/Contents/MacOS/Tableau -DDisableVerifyConnectorPluginSignature=true
```

You can also package this up with AppleScript by using the following script:

```tableau
do shell script "\"/Applications/Tableau Desktop 2023.2.app/Contents/MacOS/Tableau\" -DDisableVerifyConnectorPluginSignature=true"
quit
```

Create this file with the [Script Editor](https://support.apple.com/guide/script-editor/welcome/mac)
(located in `/Applications/Utilities`)
and [save it as a packaged application](https://support.apple.com/guide/script-editor/save-a-script-as-an-app-scpedt1072/mac):

![tableau-applescript](/images/guides/tableau/applescript.png)

You can then double-click it to launch Tableau.
You will need to change the application name in the script when you get upgrades.

### Windows Desktop

Copy the Taco file to the `C:\Users\[Windows User]\Documents\My Tableau Repository\Connectors` directory.
Then launch Tableau Desktop from a shell with the `-DDisableVerifyConnectorPluginSignature=true` argument
to disable signature validation.

## Output

Once loaded, you can run queries against your data!
Here is the result of the first H2O.ai benchmark query from the Parquet test file:

![tableau-parquet](/images/guides/tableau/h2oai-group-by-1.png)
