---
title: "Redirection"
teaching: 30
exercises: 15
questions:
- "How can I search within files?"
- "How can I combine existing commands to do new things?"
objectives:
- "Employ the `grep` command to search for information within files."
- "Print the results of a command to a file."
- "Construct command pipelines with two or more stages."
keypoints:
- "`grep` is a powerful search tool with many options for customization."
- "`>`, `>>`, and `|` are different ways of redirecting output."
- "`command > file` redirects a command's output to a file."
- "`command >> file` redirects a command's output to a file without overwriting the existing contents of the file."
- "`command_1 | command_2` redirects the output of the first command as input to the second command."
- "for loops are used for iteration"
- "`basename` gets rid of repetitive parts of names"
---

## Searching files

We discussed in a previous episode how to search within a file using `less`. We can also
search within files without even opening them, using `grep`. `grep` is a command-line
utility for searching plain-text files for lines matching a specific set of 
characters (sometimes called a string) or a particular pattern 
(which can be specified using something called regular expressions). We're not going to work with 
regular expressions in this lesson, and are instead going to specify the strings 
we are searching for.
Let's give it a try!

> ## Nucleotide abbreviations
> 
> The four nucleotides that appear in DNA are abbreviated `A`, `C`, `T` and `G`. 
> Unknown nucleotides are represented with the letter `N`. An `N` appearing
> in a sequencing file represents a position where the sequencing machine was not able to 
> confidently determine the nucleotide in that position. You can think of an `N` as being aNy 
> nucleotide at that position in the DNA sequence. 
> 
{: .callout}

We'll search for strings inside of our fastq files. Let's first make sure we are in the correct 
directory.

~~~
$ cd ~/dc_workshop/data/untrimmed_fastq
$ ls  
~~~
{: .bash}
~~~
JC1A_R1.fastq   JC1A_R2.fastq     JP4D_R1.fastq     JP4D_R2.fastq  TruSeq3-PE.fa
~~~
{: .output}
Suppose we want to see how many reads in our file have really bad segments containing 10 consecutive unknown nucleotides (Ns).

> ## Determining quality
> 
> In this lesson, we're going to be manually searching for strings of `N`s within our sequence
> results to illustrate some principles of file searching. It can be really useful to do this
> type of searching to get a feel for the quality of your sequencing results, however, in your 
> research you will most likely use a bioinformatics tool that has a built-in program for
> filtering out low-quality reads. You'll learn how to use one such tool in 
> [a later lesson](https://carpentries-lab.github.io/metagenomics-analysis/02-assessing-read-quality/index.html).
> 
{: .callout}

Let's search for the string NNNNNNNNNN in the JC1A_R2.fastq file.
~~~
$ grep NNNNNNNNNN JC1A_R2.fastq
~~~
{: .bash}

This command returns a lot of output to the terminal. Every single line in the JC1A_R2.fastq 
file that contains at least 10 consecutive Ns is printed to the terminal, regardless of how long or short the file is. 
We may be interested not only in the actual sequence which contains this string, but 
in the name (or identifier) of that sequence. We discussed in a previous lesson 
that the identifier line immediately precedes the nucleotide sequence for each read
in a FASTQ file. We may also want to inspect the quality scores associated with
each of these reads. To get all of this information, we will return the line 
immediately before each match and the two lines immediately after each match.

We can use the `-B` argument for grep to return a specific number of lines before
each match. The `-A` argument returns a specific number of lines after each matching line. Here we want the line *before* and the two lines *after* each 
matching line, so we add `-B1 -A2` to our grep command.

~~~
$ grep -B1 -A2 NNNNNNNNNN JC1A_R2.fastq
~~~
{: .bash}

One of the sets of lines returned by this command is: 

~~~
@MISEQ-LAB244-W7:91:000000000-A5C7L:1:2111:5300:24013 2:N:0:TCGAAG
NNNNNNNNNNNCNANNANNNNNCGCCGGTGTTCTNCTGGGGNACGGANACCGAGTAGATCGGAACAGCGTCGTGGAGNGAAAGAGTGTAGATCCCGGTGGGCGGCGTATCATTAAAAAAAAAACCTGCTGGTCCTTGTCTC
+
AAA11BB3333BGG1GGEC1E?0E0B0BFDGFHD2FBH110A1BEE?A/BAFBDGH///>FEGGG><@/#//?#?/#//????########################################################################################################################################################################
~~~
{: .output}

> ## Exercise 1: Using grep
>
> 1. Search for the sequence `GATCGAGAGGGGATAGGCG` in the `JC1A_R2.fastq` file.
> Have your search return all matching lines and the name (or identifier) for each sequence
> that contains a match.
> 
> 2. Search for the sequence `AAGTT` in all FASTQ files.
> Have your search return all matching lines and the name (or identifier) for each sequence
> that contains a match.
> 
> > ## Solution  
> >'1.To search for the GATCGAGAGGGGATAGGCG sequence in the file JC1A_R2.fastq:  
> >~~~
> > $ grep -B1 GATCGAGAGGGGATAGGCG JC1A_R2.fastq
> >~~~
>> {: .bash}
> > The output shows all of the lines that contain the sequence GATCGAGAGGGGATAGGCG. As the flag -B1 is used, it also shows the previous line to each occurence. In a FastQ file the identifier of each sequence is one line avobe the sequence itself, therefore in this example you can see the names and the sequences that match your query.
> > 
> >'2.To search for a sequence in all of the FastQ files you could use the asterisk `*` wildcard before the file extension `.fastq` :
> >~~~
> > $ grep -B1 AAGTT *.fastq
> >~~~  
> > In this case, the lines with the sequence AAGTT are shown for all of the files that end with '.fastq' in the current directory. The output shows the name of the file followed by semicolon to differentiate what file each line comes from.
> > 
> {: .solution}
{: .challenge}

## Redirecting output

`grep` allowed us to identify sequences in our FASTQ files that match a particular pattern. 
All of these sequences were printed to our terminal screen, but in order to work with these 
sequences and perform other operations on them, we will need to capture that output in some
way. 

We can do this with something called "redirection". The idea is that
we are taking what would ordinarily be printed to the terminal screen and redirecting it to another location. 
In our case, we want to print this information to a file so that we can look at it later and 
use other commands to analyze this data.

The command for redirecting output to a file is `>`.

Let's try out this command and copy all the records (including all four lines of each record) 
in our FASTQ files that contain 
'NNNNNNNNNN' to another file called `bad_reads.txt`.

~~~
$ grep -B1 -A2 NNNNNNNNNN JC1A_R2.fastq > bad_reads.txt
~~~
{: .bash}

  

The prompt should sit there a little bit, and then it should look like nothing
happened. But type `ls`. You should see a new file called `bad_reads.txt`. 

We can check the number of lines in our new file using a command called `wc`. 
`wc` stands for **word count**. This command counts the number of words, lines, and characters
in a file. 

~~~
$ wc bad_reads.txt
~~~
{: .bash}

~~~
  402   489 50076 bad_reads.txt
~~~
{: .output}

This will tell us the number of lines, words and characters in the file. If we
want only the number of lines, we can use the `-l` flag for `lines`.

~~~
$ wc -l bad_reads.txt
~~~
{: .bash}

~~~
402 bad_reads.txt
~~~
{: .output}

Because we asked `grep` for all four lines of each FASTQ record, we need to divide the output by
four to get the number of sequences that match our search pattern.

> ## Exercise 2: Using `wc`
>
> How many sequences in `JC1A_R2.fastq` contain at least 3 consecutive Ns?
>
>> ## Solution
>>  
>>
>> ~~~
>> $ grep NNN JC1A_R2.fastq > bad_reads.txt
>> $ wc -l bad_reads.txt
>> ~~~
>> {: .bash}
>> 
>> ~~~
>596 bad_reads.txt
>> ~~~
>> {: .output}
>>
> {: .solution}
{: .challenge}

We might want to search multiple FASTQ files for sequences that match our search pattern.
However, we need to be careful, because each time we use the `>` command to redirect output
to a file, the new output will replace the output that was already present in the file. 
This is called "overwriting" and, just like you don't want to overwrite your video recording
of your kid's first birthday party, you also want to avoid overwriting your data files.

~~~
$ grep -B1 -A2 NNNNNNNNNN JC1A_R1.fastq > bad_reads.txt
$ wc -l bad_reads.txt
~~~
{: .bash}

~~~
24 bad_reads.txt
~~~
{: .output}


The old `bad_reads.txt` that counts bad quality reads from file `JC1A_R2.fastq` with 402 lines has been erased.
Instead a new `bad_reads.txt` that contain 24 lines from bad reads from `JC1A_R1.fastq` has been created.
We can avoid overwriting our files by using the command `>>`. `>>` is known as the "append redirect" and will 
append new output to the end of a file, rather than overwriting it.

~~~
$ grep -B1 -A2 NNNNNNNNNN JC1A_R2.fastq > bad_reads.txt
$ wc -l bad_reads.txt
~~~
{: .bash}

~~~
402 bad_reads.txt
~~~
{: .output}

~~~
$ grep -B1 -A2 NNNNNNNNNN JC1A_R1.fastq >> bad_reads.txt
$ wc -l bad_reads.txt
~~~
{: .bash}

~~~
426 bad_reads.txt
~~~
{: .output}

The output of our second call to `wc` shows that we have not overwritten our original data. 
The final number of 426 lines results from the adition of 402 reads from `JC1A_R2.fastq`
 file + 24 reads from  `JC1A_R1.fastq` file. We can also do this for more files with a single line of code by using a wildcard. 

~~~
$ rm bad_reads.txt
~~~
{: .bash}

~~~
$ grep -B1 -A2 NNNNNNNNNN *.fastq >> bad_reads.txt
$ wc -l bad_reads.txt
~~~
{: .bash}

~~~
427 bad_reads.txt
~~~
{: .output}

   

Since we might have multiple different criteria we want to search for, 
creating a new output file each time has the potential to clutter up our workspace. We also
so far haven't been interested in the actual contents of those files, only in the number of 
reads that we've found. We created the files to store the reads and then counted the lines in 
the file to see how many reads matched our criteria. There's a way to do this, however, that
doesn't require us to create these intermediate files - the pipe command (`|`).

This is probably not a key on
your keyboard you use very much, so let's all take a minute to find that key. 
What `|` does is take the output that is
scrolling by on the terminal and uses that output as input to another command. 
When our output was scrolling by, we might have wished we could slow it down and
look at it, like we can with `less`. Well it turns out that we can! We can redirect our output
from our `grep` call through the `less` command.

~~~
$ grep -B1 -A2 NNNNNNNNNN JC1A_R2.fastq | less
~~~
{: .bash}

We can now see the output from our `grep` call within the `less` interface. We can use the up and down arrows 
to scroll through the output and use `q` to exit `less`.

Redirecting output is often not intuitive, and can take some time to get used to. Once you're 
comfortable with redirection, however, you'll be able to combine any number of commands to
do all sorts of exciting things with your data!

None of the command line programs we've been learning
do anything all that impressive on their own, but when you start chaining
them together, you can do some really powerful things very
efficiently. 

## Writing for loops

Loops are key to productivity improvements through automation as they allow us to execute commands repeatedly. 
Similar to wildcards and tab completion, using loops also reduces the amount of typing (and typing mistakes). 
Loops are helpful when performing operations on groups of sequencing files, such as unzipping or trimming multiple
files. We will use loops for these purposes in subsequent analyses, but will cover the basics of them for now.

When the shell sees the keyword `for`, it knows to repeat a command (or group of commands) once for each item in a list. 
Each time the loop runs (called an iteration), an item in the list is assigned in sequence to the **variable**, and 
the commands inside the loop are executed, before moving on to  the next item in the list. Inside the loop, we call for 
the variable's value by putting `$` in front of it. The `$` tells the shell interpreter to treat the **variable**
as a variable name and substitute its value in its place, rather than treat it as text or an external command. In shell programming, this is usually called "expanding" the variable.

~~~
$ cd ../untrimmed_fastq/
~~~
{: .bash}

Let's write a for loop to show us the first two lines of the fastq files we downloaded earlier. You will notice shell prompt changes from `$` to `>` and back again as we were typing in our loop. The second prompt, `>`, is different to remind us that we haven’t finished typing a complete command yet. A semicolon, `;`, can be used to separate two commands written on a single line.

~~~
$ for filename in *.fastq
> do
> head -n 2 ${filename} >> seq_info.txt
> done
~~~
{: .bash}

To see the content of the little file we just made it is useful to use the `cat` command.
~~~
cat seq_info.txt
~~~
{: .bash}
~~~
@MISEQ-LAB244-W7:91:000000000-A5C7L:1:1101:13417:1998 1:N:0:TCGNAG
CTACGGCGCCATCGGCGNCCCCGGACGGTAGGAGACGGCGATGCTGGCCCTCGGCGCGGTCGCGTTCCTGAACCCCTGGCTGCTGGCGGCGCTCGCGGCGCTGCCGGTGCTCTGGGTGCTGCTGCGGGCGACGCCGCCGAGCCCGCGGCGGGTCGGATTCCCCGGCGTGCGCCCCCCGCTCCGGCTCGAGGACGCCGCACCGACGCCCCACCCCCCCCCCCGGTGGCTCCTCCTGCCGCCCTGCCTGATCC
@MISEQ-LAB244-W7:91:000000000-A5C7L:1:1101:13417:1998 2:N:0:TCGNAG
CGCGATCAGCAGCGGCCCGGAACCGGTCAGCCGCGCCNTGGGGTTCAGCACCGGCNNGGCGAAGGCCGCGATCGCGGCGGCGGCGATCAGGCAGCGCAGCAGCAGGAGCCACCAGGGCGTGCGGTCGGGCGTCCGTTCGGCGTCCTCGCGCCCCAGCAGCAGGCGCACGCCAGGGAATCCGACCCGCCGCCGGCTCGGCCGCGTCNCCCGCNCCCGCCCCCCGAGCACCCGNAGCCNCNCCACCGCCGCCC
@MISEQ-LAB244-W7:156:000000000-A80CV:1:1101:12622:2006 1:N:0:CTCAGA
CCCGTTCCTCGGGCGTGCAGTCGGGCTTGCGGTCTGCCATGTCGTGTTCGGCGTCGGTGGTGCCGATCAGGGTGAAATCCGTCTCGTAGGGGATCGCGAAGATGATCCGCCCGTCCGTGCCCTGAAAGAAATAGCACTTGTCAGATCGGAAGAGCACACGTCTGAACTCCAGTCACCTCAGAATCTCGTATGCCGTCTTCTGCTTGAAAAAAAAAAAAGCAAACCTCTCACTCCCTCTACTCTACTCCCTT
@MISEQ-LAB244-W7:156:000000000-A80CV:1:1101:12622:2006 2:N:0:CTCAGA
GACAAGTGCTATTTCTTTCAGGGCACGGACGGGCGGATCATCTTCGCGATCCCCTACGAGACGGATTTCACCCTGATCGGCACCACCGACGCCGAACACGACATGGCAGACCGCAAGCCCGACTGCACGCCCGAGGAACGGGAGATCGGAAGAGCGTCGTGTAGGAAAGAGTGTAGATCTCGGTGGTCGCCGTATCATTAAAAAAAAAAAGCGATCAACTCGACCGACCTGTCTTATTATATCTCACGTAA
~~~
{: .output}

The for loop begins with the formula `for <variable> in <group to iterate over>`. In this case, the word `filename` is designated 
as the variable to be used over each iteration. In our case `JC1A_R1.fastq` and `JC1A_R2.fastq` will be substituted for `filename` 
because they fit the pattern of ending with .fastq in directory we've specified. The next line of the for loop is `do`. The next line is 
the code that we want to execute. We are telling the loop to print the first two lines of each variable we iterate over and save the information
to a file. Finally, the word `done` ends the loop.

Note that we are using `>>` to append the text to our `seq_info.txt` file. If we used `>`, the `seq_info.txt` file would be rewritten
every time the loop iterates, so it would only have text from the last variable used. Instead, `>>` adds to the end of the file.

## Using Basename in for loops
Basename is a function in UNIX that is helpful for removing a uniform part of a name from a list of files. In this case, we will use basename to remove the `.fastq` extension from the files that we’ve been working with. 

~~~
$ basename JC1A_R2.fastq .fastq
~~~
{: .bash}

We see that this returns just the SRR accession, and no longer has the .fastq file extension on it.

~~~
JC1A_R2
~~~
{: .output}

If we try the same thing but use `.fasta` as the file extension instead, nothing happens. This is because basename only works when it exactly matches a string in the file.

~~~
$ basename JC1A_R2.fastq .fasta
~~~
{: .bash}

~~~
JC1A_R2.fastq
~~~
{: .output}

Basename is really powerful when used in a for loop. It allows to access just the file prefix, which you can use to name things. Let's try this.

Inside our for loop, we create a new name variable. We call the basename function inside the parenthesis, then give our variable name from the for loop, in this case `${filename}`, and finally state that `.fastq` should be removed from the file name. It’s important to note that we’re not changing the actual files, we’re creating a new variable called name. The line > echo $name will print to the terminal the variable name each time the for loop runs. Because we are iterating over two files, we expect to see two lines of output.

~~~
$ for filename in *.fastq
> do
> name=$(basename ${filename} .fastq)
> echo ${name}
> done
~~~
{: .bash}
~~~
JC1A_R1
JC1A_R2
JP4D_R1
JP4D_R2
~~~
{: .output}


> ## Exercise 3: Using `basename`
>
> Print the file prefix of all of the `.txt` files in our current directory.
>
>> ## Solution
>>  
>>
>> ~~~
>> $ for filename in *.txt
>> > do
>> > name=$(basename ${filename} .txt)
>> > echo ${name}
>> > done
>> ~~~
>> {: .bash}
>>
> {: .solution}
{: .challenge}

One way this is really useful is to move files. Let's rename all of our .txt files using `mv` so that they have the years on them, which will document when we created them. 

~~~
$ for filename in *.txt
> do
> name=$(basename ${filename} .txt)
> mv ${filename}  ${name}_2019.txt
> done
~~~
{: .bash}
