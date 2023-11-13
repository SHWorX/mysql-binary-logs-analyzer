[TOC]
# MySQL Binary Logs Analyzer

## Description
This tool helps to analyze the MySQL/MariaDB binary log files. The generated report will give an overview of
how many UPDATE, INSERT, DELETE, DROP and TRUNCATE operations appear in the binary logs. This report can help
during the investigation of slow replication, performance impacts on MySQL/MariaDB server etc.

### Technical description
In the first phase the tool look up all binary log files which matches the given parameters. After that it 
will call the command `mysqlbinlog --base64-output=decode-rows -v` and passes the list of matching files 
as arguments, the output of this will then be saved in the file `mysql-raw.log` under the defined output 
path.

In the second phase, the tool will then analyze the content of the file `mysql-raw.log` and generate a 
report, in which the UPDATE, INSERT, DELETE, DROP and TRUNCATE operations are grouped, and contain the 
amount of how many times each operation appears in the binary logs.

After the report is generated, the tool will automatically delete the file `mysql-raw.log`, since it have no use 
anymore.

Additional to the report file, the tool will also generate a text file, which contains the list of binary 
logs, which have been taken into account for the report.

This repository contains 2 *.txt files:<br>
`2023-11-13_1699854803_binary-logs-list.txt`<br>
`2023-11-13_1699854803_binary-logs-report.txt`

Which are examples of the report and file list.

## Installation
**Requirements**

- Python 3

To install this tool, just copy the file into ```/usr/bin/``` and set execution permissions (chmod 755).
## Usage
The usage of this tool is simple, all you have to do is executing the tool on shell.
If you want (or if necessary), you can specify some working parameters.

### Examples ###

#### No arguments ####
```bash
user@machine:~$ analyze-mysqlbinlogs
```

#### Specify binary logs file name (without extension!) ####
```bash
user@machine:~$ analyze-mysqlbinlogs -n my-binary-logs
```

#### Specify binary logs file path ####
```bash
user@machine:~$ analyze-mysqlbinlogs -p /var/lib/mysql
```

#### Specify output path ####
```bash
user@machine:~$ analyze-mysqlbinlogs -o /path/where/output/files/shall/be/saved/
```

#### Specify start and end date ####
```bash
user@machine:~$ analyze-mysqlbinlogs -f 2023-11-08 -t 2023-11-09
```

#### Specify start datetime and end date ####
```bash
user@machine:~$ analyze-mysqlbinlogs -f 2023-11-08 08:20:00 -t 2023-11-09
```

#### Specify start and end datetime via unix timestamp ####
```bash
user@machine:~$ analyze-mysqlbinlogs -f 1699402800 -t 1699545599
```

### Full list of available options/arguments
```bash
user@machine:~$ analyze-mysqlbinlogs -h
USAGE: analyze-mysql-binlogs --dt-from=<datetime> --dt-end=<datetime> --binary-logs-file-name=<file-name-without-extension> --binary-logs-file-path=<path> --output-path=<output-path>
-h						Shows this help
-f	--dt-from=<datetime>			The start date(time) the binary logs are created to analyze (allowed formats: YYYY-MM-DD, YYYY-MM-DD HH:MM:SS, Unix timestamp)
						If given as YYYY-MM-DD, then the start datetime will be: YYYY-MM-DD 00:00:00.
						If omitted, the start datetime will be the current date at 00:00:00.
-t	--dt-to=<datetime>			The end date(time) the binary logs are created to analyze (allowed formats: YYYY-MM-DD, YYYY-MM-DD HH:MM:SS, Unix timestamp)
						If given as YYYY-MM-DD, then the end datetime will be: YYYY-MM-DD 23:59:59
						If omitted, the end datetime will be the current date at 23:59:59.
-n	--binary-logs-file-name=<name>		The name the MySQL binary log files without the file extension (default: main-web-server-bin
-p	--binary-logs-file-path=<path>		The path where the MySQL binary logs are (default: /var/lib/mysql/)
-o	--output-path=<output-path>		The path where the output files are saved (default: <current-directory>
```