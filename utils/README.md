_imported from http://pizzachili.di.unipi.it/experiments.html_

## Experimental Setup

We recommend using one text of each type provided in the corpus, to cover a representative set of relevant applications, unless you are interested in a specific application. We give one text of each type, but also prefixes of 50MB, 100MB and 200MB of it. This way the user can choose the size that best fits its domain of application of the compressed index. We point out that the English text has also a prefix of 1GB, in case you have trouble to handle the full >2GB text.

In case you are interested in monitoring the performance for increasing text sizes, you will prefer to test increasing prefixes of a text collection. In the utilities there is a program to cut text prefixes of any length.

The performance of a compressed index may be evaluated either at construction time or at query time.

At **construction time** we are interested in:

* <font color="#000077">_Time_</font> to build the index, considering user+system time.

* <font color="#000077">_Internal memory working space_</font>: amount of RAM necessary to build the index (peak usage along the construction process). Note that we do not consider the temporary disk usage, as this is difficult to measure. This means that a construction algorithm that uses secondary memory can improve this measure, in exchange for higher construction time (I/Os).

* <font color="#000077">_Permanent space on disk_</font>: amount of disk space used to store the index. Note that the index can be stored in a way that is so compact that it cannot be just loaded and used, but needs some uncompress operation. This is permissible, and will be paid in index load time (see next). In an extreme case, one could just compress the text and declare it as the index on disk, and build the index from scratch at load time. This will look good in terms of permanent space on disk, but will incur in a very high load time. Note that this is not cheating in terms of index size, because the real index size is measured as the internal memory usage to solve queries

At **query time** we are interested in:

* <font color="#000077">_Index load time_</font>: user+system time to read the index from disk so that it becomes ready for querying. See the previous item _permanent space on disk_.

* <font color="#000077">_Internal memory usage to solve queries_</font>: amount of RAM necessary to answer a substring query (peak usage along the query process). This is a measure of the actual space needed by the index to operate. To ensure that an index uses only the internal memory, you can run the query process without any write permission on disk. The indexes usually have parameters that trade query time for index space (disk or internal working space), so this has to be measured as a function of these parameters.

* <font color="#000077">_Query time to solve queries_</font>: user+system time to carry out queries of different type. The same comments on space usage above (parameter dependence and expected behavior depending on queries) apply for time.

The last two measures, namely internal memory usage and query time, are relevant for the following queries:

* <font color="#000077">_Counting_</font>: Usually one is interested in the performance as a function of the pattern length, so the experiment should report the results in this way, or just in terms of time per pattern character if the function turns out to be linear. For each length, a sufficient number of searches must be averaged so as to take care of the variance due to pattern characteristics and to computer time.

* <font color="#000077">_Locating_</font>: As the process has to allocate an array of occurrences to return, this array is included in its memory usage (although, as we report peak memory usage, it is not necessarily clear how to discount it). Time results can be given normalized as time per occurrence.

* <font color="#000077">_Displaying_</font>: As the process has to allocate an array of snippets to return, this array is included in its memory usage. As for time, it can be normalized as time per character displayed.

* <font color="#000077">_Extracting_</font>: As the process has to allocate a text string to return, the space for this string is included in its memory usage. As for time, it can be normalized as time per character extracted.

We are aware of the fact that performing all these measures is a repetitive process and could take much time. Since all indexes offer the same API we provide some _utility programs_ to automate the testing phase. Please download them before proceeding further in your experimentation (in the link you will find each file separately as well as **[utils.tar.gz](http://pizzachili.di.unipi.it/utils/utils.tar.gz)** with them all except the compressors). Below we comment some of their main features.

We provide a bunch of programs to manipulate the texts in the collections and to extract some statistics from them.

* <font color="#000077">**cut** `<filename> <X>`</font>  
    that produces `<filename>.<X>MB`, a prefix of `<filename>` of `<X>` megabytes. Files are [cut.c](utils/cut.c) (source) and [cut](utils/cut) (executable).

* <font color="#000077">**alph** `<filename>`</font>  
    gives basic alphabet statistics on `<filename>`. Files are [alph.c](utils/alph.c) (source) and [alph](utils/alph) (executable).

* <font color="#000077">**entrop** `<filename> <k>`</font>  
    computes the empirical entropy of order `<k>` of `<filename>`. Files are [entrop.c](utils/entrop.c) (source) and [entrop](utils/entrop) (executable). There are variants [entrop2](utils/entrop2.c) and [entrop3](utils/entrop3.c) with different time/memory tradeoffs.

* <font color="#000077">**gentext** `<filename> <n> <s>`</font>  
    generates in `<filename>` a random text of `<n>` megabytes, uniformly distributed over `<s>` characters, choosing ASCII values 1 to `<s>`. Files are [gentext.c](utils/gentext.c) (source) and [gentext](utils/gentext) (executable).

* <font color="#000077">**gzip**</font> sources and executables. Files are [gzip-1.3.3.tar.gz](utils/gzip-1.3.3.tar.gz) (sources), [gzip-1.3.3](utils/gzip-1.3.3) (compressor), and [gunzip](utils/gunzip) (decompressor).

*   <font color="#000077">**bzip2**</font> sources and executables. Files are [bzip2-1.0.3.tar.gz](utils/bzip2-1.0.3.tar.gz) (sources), [bzip2-1.0.3](utils/bzip2-1.0.3) (compressor), and [bunzip2](utils/bunzip2) (decompressor).

*   <font color="#000077">**ppmdi**</font> sources and executables (the version we used for our statistics). Files are [ppmdi.tar.gz](utils/ppmdi.tar.gz) (sources), [ppmdi](utils/ppmdi) (compressor), and [unppmdi](utils/unppmdi) (decompressor).

We provide a bunch of programs to generate random query patterns to be searched, and random snippets to be extracted. These will constitute the material for our experiments over the compressed indexes.

* <font color="#000077">**genpatterns** `<filename> <length> <number> <patterns file> [<forbidden>]`</font>  
    It generates in `<patterns file>` a `<number>` of substrings consisting of `<length>` characters chosen at random from `<filename>`. Strings with characters in `<forbidden>` are discarded (for nonprintable chracters `<forbidden>` uses \n, \t, etc. or \cN where N is a ASCII code written using 3 digits). The output file starts with a line of the form  
    `# number=<number> length=<length> file=<filename> forbidden=<forbidden>`  
    The `<number>` patterns then come one after the other without any separator. Files are [genpatterns.c](utils/genpatterns.c) (source) and [genpatterns](utils/genpatterns) (executable).

* <font color="#000077">**genintervals** `<filename> <length> <number> <intervals file>`</font>  
    It generates in `<intervals file>` a `<number>` of intervals having `<length>` chosen at random from `<filename>`. The output file starts with a line of the form  
    `# number=<number> length=<length> file=<filename>`  
    Then follow `<number>` lines having the form `<from>,<to>`. Files are [genintervals.c](utils/genintervals.c) (source) and [genintervals](utils/genintervals) (executable).

* <font color="#000077">**interface.h**</font>  
    It is the interface file providing the functions of the library. File is [interface.h](utils/interface.h).

* <font color="#000077">**build_index** `<source file> <index file> [<parameters>]`</font>  
    It builds the index for the text in file `<source file>`, storing it in `<index file>` (possibly with extensions on that file name). Any additional `<parameters>` will be passed to the construction function. At the end, the program sends to the standard error performance measures on time to build the index. File is [build_index.c](utils/build_index.c) (source).

* <font color="#000077">**run_queries** `<index file> <type> [<length>] [V]`</font>  
    It loads `<index>` and then executes over it the queries it receives from the standard input. The standard input comes in the format of the files written by **genpatterns** or **genintervals**. The query types are given by `<type>`. Value **C** means counting queries, **L** locating queries, **D** displaying queries, and **E** extracting queries. For the first three queries the standard input must follow the format of the file produced by **genpatterns**. For the last query (namely E), the format of the standard input must be the one of **genintervals**. Parameter `<length>` must be provided in case of displaying queries (D), and denotes the number of characters to display before and after each pattern occurrence. With the option V the program reports the results of a set of queries on the stantard output (in binary format). This output may be used with compare for checking the correctness of the query results. The program also reports on the standard error some time statistics regarding the query execution. File is [run_queries.c](utils/run_queries.c) (source).

* <font color="#000077">**compare** `<results file1> <results file2>`</font>

    `<results file1>` and `<results file2>` are binary files produced by run_queries with the option V. The program finds the differences between the two files. It is useful to check the correctness and completness of an index by comparing it against a trusted index (e.g. suffix array, which is made available). Files are [compare.c](utils/compare.c) (source) and [compare](utils/compare) (executable).

*   <font color="#000077">**memusage**</font> <font color="#000077">`[option] <program>`</font>

    We suggest to use the program by Ulrich Drepper for taking the peaks of internal memory usage by a given executable (i.e. `<program>`). This program reports on the standard output the peak of both stack and heap usage. It can also generate a graphical output, for which you need the gd-devel package. You can run memusage --help for more details. Files are [memusage](utils/memusage/memusage-2.2.2.tar.gz) (source) and [gd-devel](utils/memusage/gd-2.0.33.tar.gz).  

We note that **build\_index.c** and **run\_queries.c** are source programs that can be compiled to **build\_index.o** and **run\_queries.o**. To obtain the executables, they must be linked with each different index implementation. They make use of the index interface **interface.h** provided above. This interface is also useful to write other programs that use the index. A **makefile** is provided that compiles all the programs (except the compressors).

[Send Mail to Us](mailto:ferragina@di.unipi.it;gnavarro@dcc.uchile.cl) | © P. Ferragina and G. Navarro, Last update: September, 2005.