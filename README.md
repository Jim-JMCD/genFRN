## genfrn - _Generic File Re-namer_
  
__*genFRN*__ a script ( Bash & Powershell) that renames files, in a directory, that have generic meaningless names by adding a prefix that is created from parent direcory names. 

Once the files have been renamed they can be moved, backed up etc without relying upon the directory structure to give them meaning. If you move files that only have generic names away from their directory structure you can lose all the contextual information.  The script creates an executable log that will undo the renaming processing. The number of parent directory names you want to include in the prefix of the new name is selectable. For safety you can abort when the preview has completed. 

Powershell undo functionality differs from the Bash version. Powershell is unable to undo renmaning if you bail out during processing.   

Best used in directories that only contain many files with genric names like:

* image-00204.jpg
* log.202505261809
* scan_056.pdf 

and given context and meaning by their parent directories.
_______________________________
### Usage
Users can select how many levels of the parent directories that can be added to the name. Users can undo changes to naming if required.
~~~
genFRN <parent level> [input directory]
~~~
* parent level   = only numbers 1 , 2, 3 and 4 permitted, manditory
* input directory = optional input direcotory.
* The default directory is the directory from where the script is run ($PWD)

### Notes
__*All files in the directory are renamed*__. Directories and hidden files are ignored.  Any file name starting with "." is ignored.

The original file name is retained with with the parent directory names added. Example of setting the parent level: 

If source is __../image-067.jpg__  then
~~~
Parent Level           Resulting File Name
  1                      Tue_image-067.jpg
  2                Week4_Tue_image-067.jpg
  3            May_Week4_Tue_image-067.jpg
  4       2025_May_Week4_Tue_image-067.jpg
~~~
The script is not recursive, it only processes all the files in the assigned directory.  

If source paths that are too short the script will use what it can. If source is __/tmp/Tue/__ and the request is for 3 parent levels the prefix added will be __tmp_Tue\___

Directories are not permitted as input: root "/" , current directory as "." and parent of current as ".."
Relative pathes as input will be processed.


### HOW TO UNDO the renaming.

__*Any changes made to the directory contents after the renaming has completed could interfer with the recovery process*__.

__*Bailing out during processing :*__ 
* BASH       : The excutable log file can be used to undo processing up the point of bail out.
* POWERSHELL : Recovery from a bail out is manual.  Powershell does not have the functiolaity of the bash *trap* command. 

__*Recovery*__
In the directory that is processed an executable log file is created. To recover run the command:
~~~
  ./genFRN_log_<YYYYMMDD-hhmmss>.sh
~~~
Where \<YYYYMMDD-hhmmss\> is the date-time of log file creation. When the undo is complete remove the log file.

Example: Renaming all the file in subdirectory __logs__ , all files to have four parent directory names added.
~~~
$./genFRN 4 ./logs                                 # It starts with a preview of changes
file_9         -> transfer_wk1_Thu_logs_file_9
file_47        -> transfer_wk1_Thu_logs_file_47
file_2         -> transfer_wk1_Thu_logs_file_2
file_44        -> transfer_wk1_Thu_logs_file_44
file_49        -> transfer_wk1_Thu_logs_file_49
                                                   # Edited, list too long
file_26        -> transfer_wk1_Thu_logs_file_26
file_20        -> transfer_wk1_Thu_logs_file_20
file_50        -> transfer_wk1_Thu_logs_file_50
Proceed with renaming? [y/n] y                     # Preview finished 
Are you sure?          [y/n] y
file_9         -> transfer_wk1_Thu_logs_file_9
file_47        -> transfer_wk1_Thu_logs_file_47
file_2         -> transfer_wk1_Thu_logs_file_2
file_44        -> transfer_wk1_Thu_logs_file_44
file_49        -> transfer_wk1_Thu_logs_file_49
 ...edited...
file_26        -> transfer_wk1_Thu_logs_file_26
file_20        -> transfer_wk1_Thu_logs_file_20
file_50        -> transfer_wk1_Thu_logs_file_50

Renaming complete.

To UNDO run: ./logs/genFRN_log_20251101-105116.sh
If you ABORTED while renaming you can still use ./logs/genFRN_log_20251101-105116.sh to undo any changes
$
$cd logs
$ls
transfer_wk1_Thu_logs_file_1   transfer_wk1_Thu_logs_file_33
transfer_wk1_Thu_logs_file_10  transfer_wk1_Thu_logs_file_34
transfer_wk1_Thu_logs_file_11  transfer_wk1_Thu_logs_file_35
transfer_wk1_Thu_logs_file_12  transfer_wk1_Thu_logs_file_36
transfer_wk1_Thu_logs_file_13  transfer_wk1_Thu_logs_file_37
                                                               # Edited, list too long.   
transfer_wk1_Thu_logs_file_3   transfer_wk1_Thu_logs_file_8
transfer_wk1_Thu_logs_file_30  transfer_wk1_Thu_logs_file_9
transfer_wk1_Thu_logs_file_31  genFRN_log_20251101-105116.sh
transfer_wk1_Thu_logs_file_32
$
~~~
### UNDO
~~~
$ ./genFRN_log_20251101-105116.sh                              # Run the log file 
$ ls
file_1   file_16  file_22  file_29  file_35  file_41  file_48  file_9
file_10  file_17  file_23  file_3   file_36  file_42  file_49  genFRN_log_20251101-105116.sh
file_11  file_18  file_24  file_30  file_37  file_43  file_5
file_12  file_19  file_25  file_31  file_38  file_44  file_50
file_13  file_2   file_26  file_32  file_39  file_45  file_6
file_14  file_20  file_27  file_33  file_4   file_46  file_7
file_15  file_21  file_28  file_34  file_40  file_47  file_8
$
$ rm genFRN_log_20251101-105116.sh
~~~
Deep Seek V3 was used to convert the Bash script to Powershell. DeepSeek succeeded with no errors at the first attepmt. Microsoft Co-Pilot, ChatGPT and Mistral Le Chat all failed to convert to the script to Powershell, all attempts to rectify all the problems also failed.   
