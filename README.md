## Overview

This repo demonstrate the bug reported for CellProfiler as [#4476](https://github.com/CellProfiler/CellProfiler/issues/4476).

Two pipelines are provided, along with example image file `data/multichannel.nd2` and a file list text file `image_list_nd2.txt`.  The table below summarizes the run result for both pipelines using different modes.


|                                         | `withExtractionFromHeader.cppipe`                            | `withoutExtractionFromHeader.cppipe` |
| --------------------------------------- | ------------------------------------------------------------ | ------------------------------------ |
| Run `.cpproj` using GUI                 | Successful withour error.                                    | Successful without error.            |
| Run `.cppipe` via command line with GUI | Error. But can be completed if manually trigger metadata extraction. | Successful without error.            |
| Run in headless mode                    | Exited with error.                                           | Successful without error.            |

Note the two pipelines are almost identical except that
  - there is an extra extraction method used in `withExtractionFromHeader.cppipe`;
  - the `NamesAndTypes` modules are slightly different because metadata available are not the same. 

Note also that since absolute paths are required, the `image_list_nd2.txt` needs to be modified to match the new location.




## More detail for how CP was run in headless mode

### Error when *extraction from header* is used (i.e., `withExtractionFromHeader.cppipe`)
```bash
cellprofiler -c -r \
    -p withExtractionFromHeader.cppipe \
    --file-list image_list_nd2.txt \
    -o output
```

```
MySQL could not be loaded.
Traceback (most recent call last):
  File "/usr/local/anaconda3/envs/cp-test/lib/python3.8/site-packages/cellprofiler/modules/exporttodatabase.py", line 154, in <module>
    import MySQLdb
  File "/usr/local/anaconda3/envs/cp-test/lib/python3.8/site-packages/MySQLdb/__init__.py", line 18, in <module>
    from . import _mysql
ImportError: dlopen(/usr/local/anaconda3/envs/cp-test/lib/python3.8/site-packages/MySQLdb/_mysql.cpython-38-darwin.so, 2): Library not loaded: @rpath/libssl.1.1.dylib
  Referenced from: /usr/local/anaconda3/envs/cp-test/lib/python3.8/site-packages/MySQLdb/_mysql.cpython-38-darwin.so
  Reason: image not found
CP-JAVA 14:27:04.882 [Thread-1] WARN  o.c.imageset.ChannelFilter - Empty image set list: no images passed the filtering criteria.
```





### Successful run when *extraction from header* is NOT used (i.e., `withoutExtractionFromHeader.cppipe`)

```bash
cellprofiler -c -r \
    -p withoutExtractionFromHeader.cppipe \
    --file-list image_list_nd2.txt \
    -o output
```

```
MySQL could not be loaded.
Traceback (most recent call last):
  File "/usr/local/anaconda3/envs/cp-test/lib/python3.8/site-packages/cellprofiler/modules/exporttodatabase.py", line 154, in <module>
    import MySQLdb
  File "/usr/local/anaconda3/envs/cp-test/lib/python3.8/site-packages/MySQLdb/__init__.py", line 18, in <module>
    from . import _mysql
ImportError: dlopen(/usr/local/anaconda3/envs/cp-test/lib/python3.8/site-packages/MySQLdb/_mysql.cpython-38-darwin.so, 2): Library not loaded: @rpath/libssl.1.1.dylib
  Referenced from: /usr/local/anaconda3/envs/cp-test/lib/python3.8/site-packages/MySQLdb/_mysql.cpython-38-darwin.so
  Reason: image not found
Times reported are CPU and Wall-clock times for each module
Wed Nov  3 14:24:40 2021: Image # 1, module Images # 1: CPU_time = 0.00 secs, Wall_time = 0.00 secs
Wed Nov  3 14:24:40 2021: Image # 1, module Metadata # 2: CPU_time = 0.00 secs, Wall_time = 0.00 secs
Wed Nov  3 14:24:40 2021: Image # 1, module NamesAndTypes # 3: CPU_time = 0.86 secs, Wall_time = 0.41 secs
Wed Nov  3 14:24:40 2021: Image # 1, module Groups # 4: CPU_time = 0.00 secs, Wall_time = 0.00 secs
Wed Nov  3 14:24:40 2021: Image # 1, module ExportToSpreadsheet # 5: CPU_time = 0.00 secs, Wall_time = 0.00 secs
```