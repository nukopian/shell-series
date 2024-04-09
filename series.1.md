% series(1) 1.0.1 | extract a series from a source table
% Iain Campbell

## NAME
series - extract a series from a source table

## SYNOPSIS
	series [options] [--]

    Options:
		-f, --fields column-spec
		-d, --delimiter input-separator
		-s, --separator output-separator
		-r, --rows style
		    --csv
		    --tuple
		    --list
		    --set
		-H, --no-header
		-S, --no-separator
		-Q, --no-quotes
		-h, --help

    style:
		a string used to define the opening and closing characters that wrap
		each row of the series and the final flattened result:

			style := [opening][quote]
			opening := '(' | '[' | '{' | ''
			quote := '"' | "'" | ''

## DESCRIPTION
Use the `series` filter to create a series from a larger source table. Input is taken from `stdin`, while output is written to `stdout`.

The `series` filter may also be used in conjunction with the `flatten` filter to reshape data into a single-line sequence. 

### Options

#### `-f`, `--fields` _column-spec_

Specify fields to extract from each row of the table. Ranges are specified using a lower bound, followed by a dash and an optional upper bound (_e.g._ 1- or 3-5). Multiple field numbers and ranges may be separated by commas (_e.g._ 1,2,5-7).

The default is to extract all columns (effectively, 1-).

#### `-d`, `--delimiter` _input-separator_

Under the hood, it is awk that parses the source table.</p><p>If specified, the _awk-field-separator_ is passed to awk using its -F option; otherwise, default awk behaviour applies.

#### `-s`, `--separator` _output-separator_ | `SPACE` | `NONE`

Specify the string used to separate each field of each row of the output series.

#### `-r`, `--rows` _style_ | `NONE`

Specify how to decorate each row of the table. The _style_ value should consist of an optional bracket style (`(`, `[`, or `{`) followed by an optional quoting style (`'` or `"`).

Whereas quoting style applies to each individual fields of each row of the output series, bracket style is applied to each entire row.

#### `-t`, `--table`

A more compact form of `-s " "` for a table-style.

Provided no output separator is specified, columns of the output series will be aligned to table.

#### `--csv`

A more compact alternative to `-s "," -r '"'` for a CSV list.

#### `--tuple`

A more compact alternative to `-s ',' -r "('"` for a Python tuple-style or SQL value-set.

#### `--list`

A more compact alternative to `-s ',' -r "['"` for a Python list-style.

#### `--set`

A more compact alternative to `-s ',' -r "{'"` for a Python set-style.

#### `-H`, `--no-header`

Do not process the first row of the source table, or include it in the output series.

#### `-S`, `--no-separator`

The shorthand options specify a separator. You may use this option afterwards to cancel that effect.

#### `-Q`, `--no-quotes`

The shorthand options specify a quoting style. You may use this option afterwards to cancel that effect.

#### `-h`, `--help`

Display usage notes.

## Examples

Given a file (`sample`) containing the following rows:

	hostname                       uuid                                  ipv4          free
	phx3plohvmn0030.prod.phx3.gdg  3556aed5-f4e7-4ffe-890c-2627a034f8cf  10.43.6.130   0.00
	phx3plohvmn0115.prod.phx3.gdg  c5c0258a-a2f9-4981-aba3-20064bfb21e1  10.43.9.214   0.03
	phx3plohvmn0376.prod.phx3.gdg  906345f5-3ea7-41c6-bb27-9db3a629bf26  10.43.11.157  0.03
	phx3plohvmn0084.prod.phx3.gdg  18993486-79c7-4465-bb27-ac1d9bd45d51  10.43.9.231   0.05
	phx3plohvmn0382.prod.phx3.gdg  9497032c-7a24-462f-b58b-ae6f08b71460  10.43.11.193  0.06
	phx3plohvmn0040.prod.phx3.gdg  6db9abd7-ec07-48af-9a02-9404f406d9e2  10.43.10.6    0.06
	phx3plohvmn0053.prod.phx3.gdg  951ba7d5-2c40-42d9-9da8-4309b18d8da3  10.43.8.87    0.07
	phx3plohvmn0045.prod.phx3.gdg  9972bd00-2c72-47a3-9672-58e5031a1298  10.43.10.28   0.15
	phx3plohvmn0404.prod.phx3.gdg  3d6df97e-f689-498b-9385-85e75c2fec69  10.43.9.2     0.18
	phx3plohvmn0370.prod.phx3.gdg  81193f20-c01b-4f25-af3d-00c9af0f36eb  10.43.11.161  0.25

### Example 1

Extract the first column only:

```shell
series -f1 < sample
```
```
hostname
phx3plohvmn0030.prod.phx3.gdg
phx3plohvmn0115.prod.phx3.gdg
phx3plohvmn0376.prod.phx3.gdg
phx3plohvmn0084.prod.phx3.gdg
phx3plohvmn0382.prod.phx3.gdg
phx3plohvmn0040.prod.phx3.gdg
phx3plohvmn0053.prod.phx3.gdg
phx3plohvmn0045.prod.phx3.gdg
phx3plohvmn0404.prod.phx3.gdg
phx3plohvmn0370.prod.phx3.gdg
```

### Example 2

Omitting the header row, extract the first column only:

```shell
series -f1 -H < sample
```
```
phx3plohvmn0030.prod.phx3.gdg
phx3plohvmn0115.prod.phx3.gdg
phx3plohvmn0376.prod.phx3.gdg
phx3plohvmn0084.prod.phx3.gdg
phx3plohvmn0382.prod.phx3.gdg
phx3plohvmn0040.prod.phx3.gdg
phx3plohvmn0053.prod.phx3.gdg
phx3plohvmn0045.prod.phx3.gdg
phx3plohvmn0404.prod.phx3.gdg
phx3plohvmn0370.prod.phx3.gdg
```

### Example 3

Omitting the header row, extract the first and third through fourth columns and quote each value. Make each row a tuple:

```shell
series -f1,3-4 -H --tuple < sample
```
```
('phx3plohvmn0030.prod.phx3.gdg','10.43.6.130','0.00')
('phx3plohvmn0115.prod.phx3.gdg','10.43.9.214','0.03')
('phx3plohvmn0376.prod.phx3.gdg','10.43.11.157','0.03')
('phx3plohvmn0084.prod.phx3.gdg','10.43.9.231','0.05')
('phx3plohvmn0382.prod.phx3.gdg','10.43.11.193','0.06')
('phx3plohvmn0040.prod.phx3.gdg','10.43.10.6','0.06')
('phx3plohvmn0053.prod.phx3.gdg','10.43.8.87','0.07')
('phx3plohvmn0045.prod.phx3.gdg','10.43.10.28','0.15')
('phx3plohvmn0404.prod.phx3.gdg','10.43.9.2','0.18')
('phx3plohvmn0370.prod.phx3.gdg','10.43.11.161','0.25')
```

### Example 4

Omitting the header row, extract the second column, but combine with the `flatten` filter to create an SQL value list (or tuple):

```shell
series -f2 -H < sample | flatten --tuple
```
```
('3556aed5-f4e7-4ffe-890c-2627a034f8cf','c5c0258a-a2f9-4981-aba3-20064bfb21e1','906345f5-3ea7-41c6-bb27-9db3a629bf26','18993486-79c7-4465-bb27-ac1d9bd45d51','9497032c-7a24-462f-b58b-ae6f08b71460','6db9abd7-ec07-48af-9a02-9404f406d9e2','951ba7d5-2c40-42d9-9da8-4309b18d8da3','9972bd00-2c72-47a3-9672-58e5031a1298','3d6df97e-f689-498b-9385-85e75c2fec69','81193f20-c01b-4f25-af3d-00c9af0f36eb')
```
