### Command line options

- Options:
    - `-d`, `--diagram`: Print a diagram of the result
    - `-u`, `--url`: URL Parameter (required)
    - `-h`, `--help`: Print usage
    - `-g`, `--get`: Get a specific part of the URL (e.g., 'origin', 'host', etc. as mentioned in the examples above)
    - `-b`, `--benchmark`: Run benchmark for piped file functions
    - `-p`, `--path`: Process all the URLs in a given file
    - `-o`, `--output`: Output the results of the parsing to a file

### Usage/Examples: 

Well-formatted URL: 

```bash 
./buildbench/tools/adaparse "http://www.google.com"
```
Output: 

```
http://www.google.com
```

Ill-formatted URL: 

```bash 
./buildbench/tools/adaparse "h^tp:ws:/www.g00g.com"
```
Output: 

```
Invalid URL: h^tp:ws:/www.g00g.com
```


Diagram flag:

```bash
 $ ./buildbench/tools/adaparse -d http://www.google.com/bal\?a\=\=11\#fddfds
 ```

Output:

 ```
  http://www.google.com/bal?a==11#fddfds [38 bytes]
       | |             |   |     |
       | |             |   |     `------ hash_start
       | |             |   `------------ search_start 25
       | |             `---------------- pathname_start 21
       | |             `---------------- host_end 21
       | `------------------------------ host_start 7
       | `------------------------------ username_end 7
       `-------------------------------- protocol_end 5
```



### Piping Example

Ada can process URLs from piped input, making it easy to integrate with other command-line tools. Here's an example of how to pipe the output of another command into Ada. 

```bash
cat dragonball_url.txt | ./buildbench/tools/adaparse
```

Output:
```
http://www.goku.com
http://www.vegeta.com
http://www.gohan.com

```

It also supports the passing of arguments to each URL in said file: 

```bash
cat dragonball_url.txt  | ./buildbench/tools/adaparse -g host
```

Output:
```
www.goku.com
www.vegeta.com
www.gohan.com
```

The benchmark flag can be used to output the time it takes to process piped input:

```bash
cat wikipedia_100k.txt | ./buildbench/tools/adaparse -b
```

```bash
(---snip---)
file:///opt 
file:///Users 
file:///Users/lemire 
file:///Users/lemire/tmp 
file:///Users/lemire/tmp/linuxdump 
file:///Users/lemire/tmp/linuxdump/linuxfiles.txt 
file:///.dockerenv 
read 10124906 bytes in 3071328453 ns using 169312 lines
0.003296588481153891 GB/s
```

There is an option to output to a file on disk:

```bash 

cat wikipedia_100k.txt | ./buildbench/tools/adaparse -o wiki_output.txt
```

as well as read in from a file on disk without going through cat: 

```bash
./buildbench/tools/adaparse -p wikipedia_top_100_txt
```

You may also combine different flags together. E.g. Say one wishes to extract only the host from URLs stored in wikipedia.txt and output it to the test_write.txt file:

```bash
/build/tools/adaparse" -p wikipedia_top100.txt -o test_write.txt -g host -b
```

Console output:
```bash
read 5209265 bytes in 26737131 ns using 100000 lines, total_bytes is 5209265 used 160 loads
0.19483260937757307 GB/s(base)
```

Content of test_write.txt:
```bash
(---snip---)
en.wikipedia.org
en.wikipedia.org
en.wikipedia.org
en.wikipedia.org
en.wikipedia.org
en.wikipedia.org
en.wikipedia.org
en.wikipedia.org
en.wikipedia.org
en.wikipedia.org
(---snip---)
```
