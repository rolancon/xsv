# XSV

XSV is an intermediary text format for interchange between relation databases and spreadsheets. It is based on the [TSV](https://en.wikipedia.org/wiki/Tab-separated_values) (tab-separated values) format, with extra features like subsectioning (derived from [MIME multipart messages](https://en.wikipedia.org/wiki/MIME#Multipart_messages)), filesystem modeling, and supports [JSON](https://en.wikipedia.org/wiki/JSON)-compatible cell values.

The XSV format encodes data in a file in a tabular structure. An XSV file has the `.xsv` extension. The contents of an XSV file must be UTF-8 encoded (a superset of US-ASCII). It consists of an optional header, plus zero ore more rows of data.

The header, which gives a name to zero or more columns, is separated from the body containing the rows of data with carriage-return plus newline characters (`\r\n`). Header values are separated by tabs (`\t`).

Each row of data is separated from the next one with a newline character (`\n`) and contains of one or more JSON scalar values separated by tabs; a separate row value is called a cell.

XSV supports the following values: the empty value (`null`), boolean values (`true` or `false`), number values (like `1`,`2.1`, `-2`, `2e3`) and string values, but without the double quotes and without escaped double quotes: so `String without double quotes and escaped " (double quote).` instead of `"String with double quotes and escaped \" (double quote)."`. With the exception of the escaped double quote (which is unneccesary in XSV) a string value can contain any of the other [JSON-escapes](https://www.json.org): Unicode codepoints, tab, carriage-return, newline, formfeed, backspace, forward-slash and backslash. The composite values array and object are not supported as XSV cell values, but are modelled as rows and headers respectively.
