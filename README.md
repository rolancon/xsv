# XSV

XSV is an intermediary text format for interchange between relation databases and spreadsheets. It is based on the [TSV](https://en.wikipedia.org/wiki/Tab-separated_values) (tab-separated values) format, with extra features like subsectioning (derived from [MIME multipart messages](https://en.wikipedia.org/wiki/MIME#Multipart_messages)), filesystem modeling, and supports [JSON](https://en.wikipedia.org/wiki/JSON)-compatible cell values.

The XSV format encodes data in a file in a tabular structure (called a table). An XSV file has the `.xsv` extension. The contents of an XSV file must be UTF-8 encoded (a superset of US-ASCII). It consists of an optional header, plus zero ore more rows of data.

The header, which gives a name to zero or more columns, is separated from the body containing the rows of data with carriage-return plus newline characters (`\r\n`). Column values are separated by tabs (`\t`). The naming convention for a column is that it can contain only alphanumeric characters (digits `0-9`, lowercase letters `a-z` and uppercase letters `A-Z`) and underscores (`_`), and should not start with a digit, e.g. `--unique_column_name`. A single underscore (`_`) as name is allowed and represents an anonymous column. Each column value should be unique.

Each row of data is separated from the next one with a newline character (`\n`), which should not be preceded by a carriage-return (`\r`), and contains one or more JSON scalar values separated by tabs; a separate row value is called a cell. A cell can be empty, which is the same as the empty string in JSON (`""`). If the header is present each cell belongs the column based on its index in the row, so the first cells of each row belong to the first column.

XSV supports the following values: the empty value (`null`), boolean values (`true` or `false`), number values (like `1`,`2.1`, `-2`, `2e3` and `3e-2`) and string values, but without the double quotes and without escaped double quotes: so `String without double quotes and unescaped " (double quote).` instead of `"String with double quotes and escaped \" (double quote)."`. With the exception of the escaped double quote (which is unneccesary in XSV) a string value can contain any of the other [JSON-escapes](https://www.json.org): Unicode codepoints, tab, carriage-return, newline, formfeed, backspace, forward-slash and backslash. The composite array and object values are not supported as XSV cells, but are modelled as rows and headers respectively.

XSV also supports encoding more than one table in the same file. The boundary consists of two hyphens (`--`) on its own line before each included table, followed by a unique name (for each boundary), which follows the above naming convention, e.g. `--unique_table_name`. The boundary ends in a newline (`\n`), optionally preceded by an (ignored) carriage-return (`\r`). The name represent the table name in a relational database, or the sheet name in a spreadsheet.  It is allowed to include a boundary when there is no or only one table present in the file: this represents an empty table in a relational database, or an empty sheet in a spreadsheet. When the file contains at least one boundary the end of the file should contain an empty boundary on its own line (so just `--`).

Another feature which XSV supports is filesystem modelling: file names are considered representing the name of a relational database (or schema) in a relational database, or the name of a spreadsheet (file). Therefore XSV file names should also follow the above naming convention, with the only modification that they contain hyphens (`-`) instead of underscores (`_`). Multiple files are then representative of multiple databases or spreadsheets.

XSV has strict rules for dealing with extra whitespace (spaces, tabs, newlines and carriage-returns). No whitespace should precede the first boundary or header or row in a file. Spaces and tabs are allowed (and ignored) in a boundary between the marker (`--`) and the boundary name, and also between the boundary name and the newline (`\n`) which separates it from the rest of the file. Spaces and tabs are allowed (and ignored) in a header before and after a column name. Spaces are allowed and not ignored in a row at the start end end of a cell: they becomes part of the content of the cell. Tabs in cells should be escaped (`\t`). It is possible to have a cell which contains of only one space (` `). There should not be more than one line separator (`\n` or `\r\n`) separating boundaries, headers and rows from one another. At the end of the file extra whitespace (spaces, tabs, newlines or carriage-returns) is allowed (and ignored).
