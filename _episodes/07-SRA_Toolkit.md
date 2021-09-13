# SRA Toolkit

There are a lot of data in different repositories in the cloud. A lot of works have been 
putting effort in sequencing genomic data to get the information needed to answer a miryad of 
questions.
One of the biggest repository-pages for genomic/metagenomic data is the National Certer for 
Biotechnology Information (NCBI). Regardless of the big data that NCBI has hoarded in the last 
decade, how to acess and download this data remains troublesome. Users around institutes have 
been facing the inconclusive path of going from a `Bioproject` page, to the `Biosample` 
information, then jumped to the `SRA experiments` inmense list and return finally to the 
`Biosample` page. In order to download the desired data, we need to locate the *Accession* 
number of the sample data, and type it in one of the `SRA Toolkit` tools. This is not trivial (It should be!), so let's go 
to explore `SRA Toolkit`

## Using fasterq-dump to download data

In ancient times (before the sra-tools version 2.9.1), `fastq-dump` was the tool used by 
deafult to access public dat in NCBI. But, with the 2.9.1 version, `fasterq-dump` became
available. It is faster than its predecesor and a little bit more intuitive that the former 
(`fastq-dump` was little intuitive). `fastq-dump` is still available, but the line of 
thought of the developers is to erase it sooner than latter.

### Creating a folder for the data
It is important to maintain onder in your projects. This extrapolates to the tools used inside
each one of them. We will create a folder inside or disk where we will save the downloaded data:

~~~
$ mkdir ~/sra-toolkit/data/
~~~
{: .bash}

~~~
$ cd ~/sra-toolkit/data/
~~~
{: .bash}

Inside this folder, we will download the data that we will use the rest of the lesson.

### Accessing to the data

Uploadig the data used in a research project is now a must for the majority of the magazines.
We will go to the [paper-page](https://www.tandfonline.com/doi/full/10.1080/21678421.2021.1904994?scroll=top&needAccess=true) to access to the data-page from the NCBI.
In this page, we will go to the aknowledgements section, were the 
**Data availability statement** is found.

<a href="{{ page.root }}/fig/02-07-01.png">
  <img src="{{ page.root }}/fig/02-07-01.png" alt="Data availability 
  section in the paper-page" />
</a>

###### Figure 1. Data availability section in the paper-page

Here, we will found the `Bioproject` number. This is a identification-code, linked to a page
where the information concerning a project and its data is hoarded. For this paper, the number
is: `PRJNA566436`. 
With this information, we can open a NCBI page in our favorite internet brower, and paste this
code in the search section. Then, a new pag with the search results will be displayed. Here, 
we will choose the `human gut metagenome` option:

<a href="{{ page.root }}/fig/02-07-02.png">
  <img src="{{ page.root }}/fig/02-07-02.png" alt="Search results after using the Bioproject
  code: PRJNA566436" />
</a>


###### Figure 2. Search results after using the Bioproject code: PRJNA566436

In the new displayed page, we can see different pieces of information. We are interested in the
**Project Data** section, which shows in a table, the sequence data. NCBI saves each set of
files (usually libraries) in a `Sequence Read Archive` (SRA). This will be the identifiaction
code that we will search if we want to download the data. We will click on the number of 
**SRA experiments**, which is **18**:

<a href="{{ page.root }}/fig/02-07-03.png">
  <img src="{{ page.root }}/fig/02-07-03.png" alt="SRA Experiments section for accessing
  the libraries information" />
</a>

###### Figure 3. SRA Experiments section for accessing the libraries information

This will lead us to a page where the desired information is still inconspicuous. We will click
on the **Send results to Run selector** option, at the top of the page:

<a href="{{ page.root }}/fig/02-07-04.png">
  <img src="{{ page.root }}/fig/02-07-04.png" alt="Option to display a more informative 
  resume of the data" />
</a>

###### Figure 4. Option to display a more informative resume of the data

The resulting page (SRA Run Selector), will show the information the is located in the 
`Bioproject` and `Biosample` pages. Also, in the bottom we will find a table with useful 
information provided by the authors. In order to obtain the *Accession* info, we will
download a **Accession List** by clicking in this section of the **Select** square:

<a href="{{ page.root }}/fig/02-07-05.png">
  <img src="{{ page.root }}/fig/02-07-05.png" alt="Downloading the Accession List file" />
</a>

###### Figure 5. Downloading the Accession List file



~~~

~~~
{: .bash}




~~~

~~~
{: .bash}
