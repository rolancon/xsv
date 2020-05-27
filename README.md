# XSV

XSV is an intermediary text format for interchange between relation databases and spreadsheets. It is based on the [TSV](https://en.wikipedia.org/wiki/Tab-separated_values) (tab-separated values) format, with extra features like subsectioning (derived from [MIME multipart messages](https://en.wikipedia.org/wiki/MIME#Multipart_messages)), filesystem modeling, and supports [JSON](https://en.wikipedia.org/wiki/JSON)-compatible cell values.

The XSV format encodes data in a file in a tabular structure (called a table). An XSV file has the `.xsv` extension. The contents of an XSV file must be UTF-8 encoded (a superset of US-ASCII). It consists of an optional header, plus zero ore more rows of data.

The header, which gives a name to zero or more columns, is separated from the body containing the rows of data with carriage-return character (`\r`). Column values are separated by tabs (`\t`). The naming convention for a column is that it can contain only alphanumeric characters (digits `0-9`, lowercase letters `a-z` and uppercase letters `A-Z`) and underscores (`_`), should not consist of just underscores and should not start with a digit, e.g. `--unique_column_name`. A single underscore (`_`) as name is allowed though, and represents an anonymous or default column. Names should not be empty. Each column value should be unique.

Each row of data is separated from the next one with a newline character (`\n`), which should not be preceded by a carriage-return (`\r`), and contains one or more JSON scalar values separated by tabs; a separate row value is called a cell. A cell can be empty, which is the same as the empty string in JSON (`""`). If the header is present each cell belongs the column based on its index in the row, so the first cells of each row belong to the first column.

XSV supports the following values: the empty value (`null`), boolean values (`true` or `false`), number values (like `1`,`2.1`, `-2`, `2e3` and `3e-2`) and string values, but without the double quotes and as a consequence without escaped double quotes (which is unneccesary in XSV): so `String without double quotes and unescaped " (double quote).` instead of `"String with double quotes and escaped \" (double quote)."`. A string value can contain a subset of the [JSON-escapes](https://www.json.org): Unicode codepoints, tab, carriage-return, newline and backslash. The composite array and object values are not supported as XSV cells, but are modelled as rows and headers respectively.

XSV also supports encoding more than one table in the same file. The boundary consists of two hyphens (`--`), called the marker, on its own line before each included table, followed by a unique name (for each boundary), which follows the above naming convention, e.g. `--unique_table_name`. The boundary ends in a carriage-return followed by a newline (`\r\n`). The name represent the table name in a relational database, or the sheet name in a spreadsheet.  It is allowed to include a boundary when there is no or only one table present in the file: this represents an empty table in a relational database, or an empty sheet in a spreadsheet. When the file contains at least one boundary the end of the file should contain an empty boundary on its own line (so just `--`).

Another feature which XSV supports is filesystem modelling: file names are considered representing the name of a relational database (or schema) in a relational database, or the name of a spreadsheet (file). Therefore XSV file names should also follow the above naming convention, with the only modifications that they contain hyphens (`-`) instead of underscores (`_`), and a single hyphen is not allowed. Multiple files are then representative of multiple databases or spreadsheets.

XSV has strict rules for dealing with extra whitespace (spaces, tabs, newlines and carriage-returns). No whitespace should precede the first boundary or header or row in a file. Spaces and tabs are allowed (and ignored) in a boundary between the marker (`--`) and the boundary name, and also between the boundary name and the newline (`\n`) which separates it from the rest of the file. Spaces and tabs are allowed (and ignored) in a header before and after a column name. Spaces are allowed and not ignored in a row at the start end end of a cell: they becomes part of the content of the cell. Tabs in cells should be escaped (`\t`). It is possible to have a cell which contains only one space (` `). There should not be more than one line separator (`\r\n`,`\r` or `\n`) separating boundaries, headers and rows from one another. At the end of the file extra line separators (newlines or carriage-returns) are allowed (and ignored). Also note that since the body of table (the rows) may be empty, it is possible that a header (ending in `\r`) may be immediately followed by an empty row (ending in `\n`), resulting in `\r\n` (the boundary ending). However, since it does not start with the boundary marker (`--`) no confusion is possible.

To facilitate the interchange between databases and spreadsheets take the following into consideration:
- Both databases and spreadsheets support all the value types of XSV: booleans, number and strings. Therefore booleans and numbers should be imported as their respective types, and not as strings.
- Strings in XSV are not quoted. This is dealt with properly in spreadsheets, but for databases the string should be surrounded by two single quotes (`'`) first, and embedded single quotes should be dealt with by doubling them up (`''`).
- To specifically cast booleans and numbers to strings we can follow the spreadsheet convention to precede them with a single quote (`'`), like `'true` and `'1`, which would be just a regular string in XSV then, and could be imported directly into a sheet. To import them as strings in a database the single quote should be dealt with expliticly by adding another single quote at the end of the string (which then constitutes a valid string).
- Database row values can contain both explicit null values and empty strings, spreadsheet cells can can only be empty. Therefore the XSV `null` value should be dealt with explicitly when importing into a sheet: it should be converted to an empty string, which then becomes an empty value.
