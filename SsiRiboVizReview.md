# RiboViz Review

Mike Jackson, The Software Sustainability Institute

23 August 2018

## Introduction

[RiboViz](https://riboviz.org) is an open source computational pipeline to extract biological insight from high-throughput data measuring protein synthesis. RiboViz is under development by the Institute for Cell Biology, University of Edinburgh, the Department of Genetics, Rutgers School of Arts and Sciences and the Department of Biology, University of Pennsylvania.

This report describes a review of RiboViz and its supporting resources from the perspective of users and developers

The resources consulted were RiboViz's:

* [Web site](https://riboviz.org/), which presents interactive visualisations.
* [Git](https://github.com/shahpr/RiboViz/) repository on [GitHub](https://github.com), containing RiboViz's source code. 
* [wiki](https://github.com/shahpr/RiboViz/wiki), complementing the repository, which includes background information on RiboViz and user documentation.
* [issue tracker](https://github.com/shahpr/RiboViz/issues), complementing the repository.

The review was undertaken between 09/08/18 and 23/08/18. The version of RiboViz used was commit [f77beadc1a4ee71cb720bd90160533ed6db00d7e](https://github.com/shahpr/RiboViz/commit/f77beadc1a4ee71cb720bd90160533ed6db00d7e).

Please contact the author at m.jackson@software.ac.uk if you have any queries.

### Platform

The platform used was an [Ubuntu](https://www.ubuntu.com/) 18.04 LTS (bionic) virtual machine (VM) configured with 1 processor; 1GB RAM; 20GB hard-disk.

This VM was run using [VMWare Player 7.1.4](https://my.vmware.com/web/vmware/free#desktop_end_user_computing/vmware_player/7_0) 64-bit (AKA VMWare Workstation 14 Player 14.0.0) on a Dell Latitude E7440 (64-bit Intel Core i5-4310U 2GHz 2 core CPU, 8GB RAM, 185GB hard disk) running Windows 10 Enterprise.

---

## Running RiboViz

The current version of RiboViz (f77beadc1a4ee71cb720bd90160533ed6db00d7e) was run under the following configurations:

* Python 2.7, `vignette_config.yaml` with `nprocesses: 1`, before `2to3` changes were applied (see [Update Python code to be both Python 2 and Python 3 compliant](#update-python-code-to-be-both-python-2-and-python-3-compliant) below).
* Python 2.7, `vignette_config.yaml` with `nprocesses: 1`, after `2to3` changes were applied.
* Python 3.6, `vignette_config.yaml` with `nprocesses: 1`.
* Python 3.6, `vignette_config.yaml` with `nprocesses: 4`.

In the current version of RiboViz, the Python code is compliant with Python 2 only. However, `vignette/vignette_config.yaml` is configured with `nprocesses: 4` which causes `cutadapt` to raise the following error:

```
ERROR: Running in parallel is not supported on Python 2
```

This is also noted in the RiboViz issue [Multi-core adapter trimming #30](https://github.com/shahpr/RiboViz/issues/30). This configuration is inconsistent with the source code. Setting `nprocesses: 1` by default, making the Python code both Python 2 and Python 3 compliant, and documenting the `nprocesses` flag for Python 3 users, would resolve this.

After implementing these changes, `prepRiboViz.sh` raised another error:

```bash
Rscript --vanilla scripts/generate_stats_figs.R --Ncores=1
  --MinReadLen=10 --MaxReadLen=50 --Buffer=250 --PrimaryID=Name
  --dataset=vignette --hdFile=vignette/output/WT3AT.h5
  --out_prefix=vignette/output/WT3AT
  --orf_fasta=vignette/input/yeast_YAL_CDS_w_250utrs.fa --rpf=TRUE
  --dir_out=vignette/output --dir_scripts=scripts 

...

Error in isSingleString(filepath) : object 'orf_gff_file' not found
Calls: readGFFAsGRanges ... readGFF -> .make_filexp_from_filepath ->
  isSingleString 
Execution halted
finished processing sample fq_files_WT3AT
```

This was fixed by adding the following command-line flag to the `Rscript` call:

```bash
... --orf_gff_file=vignette/input/yeast_YAL_CDS_w_250utrs.gff3
```

The configuration Python 3.6, `vignette_config.yaml` with `nprocesses: 4` also raised the following error:

```
Error in apply(output, 1, sum) : dim(X) must have a positive length

Error in H5Dopen(fid, paste0("/", gene, "/", dataset, "/reads/data")) : 
  HDF5. Dataset. Object not found.
```

A RiboViz developer suggested trying the previous version, commit [1ce0ebd94d91854c25c87e4a99f4edf51ab40b44](https://github.com/shahpr/RiboViz/commit/1ce0ebd94d91854c25c87e4a99f4edf51ab40b44). This version was tried and all configurations succeeded except for Python 3.6, `vignette_config.yaml` with `nprocesses: 4` which continued to give rise to:

```
Error in apply(output, 1, sum) : dim(X) must have a positive length

Error in H5Dopen(fid, paste0("/", gene, "/", dataset, "/reads/data")) : 
  HDF5. Dataset. Object not found.
```

---

## Resources

### Create a project email address

The email addresses on the web site's "Help" page are to individual developers. Requests for users to contact specific team members via their personal email addresses should be avoided, just in case they are on leave, or they leave. Any emails from users should go to a project email account (e.g. support@riboviz.org), and more than one team member should have access to this. This ensures that emails are replied to in a timely manner. A user can be deterred from using software further, or a potential user might look elsewhere, if they get no reply to an email. In the worst case this can damage the sender's opinion of both the software and the project and can lead to bad word-of-mouth to others.

### Create a "RiboViz" organisation on GitHub

At present the RiboViz repository is within a personal GitHub account. Create a "RiboViz" organisation on GitHub and move the RiboViz repository into this. This allows you to host additional repositories related to RiboViz e.g. if you develop complementary tools. More importantly, this can help to give the impression that RiboViz is software that is developed by a project or community rather than a solo developer. This can help contribute to building a user and developer community in the longer-term because the software is in a more neutral space than a personal, or institutional-specific, repository.

### Migrate user documentation out of the wiki

RiboViz's user documentation is within the wiki. Though there is a small set of documentation just now, one issue with wikis is they can grow randomly and quickly become challenging to navigate. There is also the risk of the documentation diverging from the current implementation of the software. It can also be hard to identify to what version of the software each page applies to, unless this is explicitly stated. 

While GitHub wikis are represented behind the scenes as a Git repository, consider explicitly migrating the documentation into a Git repository. Either it could be placed in the same repository as the source code, or in a complementary repository. In the latter case, Git tags (e.g. "RiboViz-1.2.3") used across the repositories to keep the source code and documentation in sync, when releases are done.

[GitHub Pages](https://pages.github.com/) can auto-generate a web site from raw MarkDown held in a Git repository on GitHub. It also supports HTML and JavaScript and can perform post-processing for auto-generation of menu bars, indices etc. The Software Sustainability Institute's [sample-site](https://github.com/softwaresaved/sample-site) Git repository contains a template for a [sample open source research software project web site](http://softwaresaved.github.io/sample-site/), written in MarkDown, with a menu bar and index, and which is automatically processed by GitHub pages into a web site that is hosted by GitHub.

### Use your issue tracker

The RiboViz Git repository has an associated issue tracker, currently with 0 open, and 4 closed, issues. Ensure that all planned features, known bugs etc. are recorded in the issue tracker.

---

## Measuring impact

### Provide a recommended citation

It is becoming popular, amongst research software developers, to request citation of software when it is used by researchers who produce publications based on research to which the software contributed. For example, the R software have a recommended citation:

> R Core Team (2017) R: A Language and Environment for Statistical Computing, R Foundation for Statistical Computing, Vienna, Austria,2017. https://www.R-project.org

The Distance sampling project at the University of St. Andrews has a [citation](http://distancesampling.org/citation.html) which is based on a complementary paper, rather than their software:

> Thomas, L., S.T. Buckland, E.A. Rexstad, J. L. Laake, S. Strindberg, S. L. Hedley, J. R.B. Bishop, T. A. Marques, and K. P. Burnham. 2010. Distance software: design and analysis of distance sampling surveys for estimating population size. Journal of Applied Ecology 47: 5-14. DOI: 10.1111/j.1365-2664.2009.01737.x

Distance complement their recommended citation with a comment that:

> We suggest in your write-ups, you be explicit about the version and release number of Distance you used, since results may vary between versions. For example, you might write in a methods section: "Analyses were carried out using Distance 6.0 release 2 (Thomas et al. 2010)" where the citation lists the above paper.

See the following reference for advice on software citation:

Smith AM, Katz DS, Niemeyer KE, FORCE11 Software Citation Working Group. (2016) Software Citation Principles. PeerJ Computer Science 2:e86. doi:[10.7717/peerj-cs.86](http://doi.org/10.7717/peerj-cs.86) 

A suitable citation would be the RiboViz paper:

riboviz: analysis and visualization of ribosome profiling datasets, Carja et al., BMC Bioinformatics 2017. doi:[10.1186/s12859-017-1873-8](https://doi.org/10.1186/s12859-017-1873-8).

### Provide a Citation.cff file

To complement your recommended citation, add a `Citation.cff` file in [Citation File Format](https://citation-file-format.github.io/) to your Git repository.

A basic example is in [Citation.cff](./Citation.cff), accompanying this report.

See also [Appendix: Validating a `Citation.cff` file](#appendix-validating-a-citationcff-file).

---

## Web site

### Add keys for graphs

On the "Home" page, provide a key for the colours on the "Sequence-based features and normalized reads" graph. Similarly, for graphs on the "TE" page.

On the "Gene of interest" page, each graph shows the colours of the selected databases in a key, but not the names. The names of the selected databases are only visible at the top of the page, or if the user moves the pointer over a data point in a graph. Display the names of the selected databases in the keys for each graph.

### Group related radiobuttons

On the "Home" page, beside the "tRNA abundances and codon-specific reads" graph, put the tRNA, tAI, log(RNAseq), log(Microarray) radiobuttons on the same line, or group them somehow, to make it clear that they form a single set.

### Add links

On the "Home" page and the "Datasets" page, make "Gene of interest" a link to https://riboviz.org/GeneBased.html.

On the "Home" page, make "R/Shiny" link to https://www.r-project.org/ and https://shiny.rstudio.com/.

### Be consistent in presenting links

On the Home page, in the "Rationale and details" section, "riboviz" and "GitHub" are both bold, but only "GitHub" is a link. On the "Help" page, developer names and "GitHub" are both links, but this is not clear to the user until they move the mouse over the text. Similarly, for the publications on the "Methods" page.

Be consistent, both within your web site, and with other web sites and format links (e.g. using blue and/or underlining them) so they stand out from the surrounding text.

### Don't use "here" or "this" as link text

The "Methods" page states:

> A Github wiki of the methods can be found here.

Words like "here" and "this" are not recommended for link text as they are vague. See, for example, [Writing Hyperlinks: Salient, Descriptive, Start with Keyword](https://www.nngroup.com/articles/writing-links).

Reword the link to something like "The analysis methods are on the _GitHub wiki_".

### Support more intuitive deletion of selected databases

On the "Gene of interest" page, the "Databases" list is non-intuitive. A user selects databases using a drop-down list but they delete selected databases by pressing the delete key (after guessing that this is how deletion is done). Provide some text to explain how to delete selected databases. Alternatively, make the associated colour-coded list of selected databases a list of checkboxes with a complementary button to allow users to delete any checked databases in the list of selected databases.

### State format and content of data downloads

On the "Gene of interest" page, by the "Download all data" button, state that the data is provided as a ZIP file of comma-separated values (CSV) files.

On the "Datasets" page:

* By the "Download data" buttons, state that the data is provided as tab-separated values (TSV) file.
* By the "Download all processed data" button, state that the data is provided as a .TAR.GZ file of TSV files.

---

## Documentation

User documentation is on the wiki and in the file `vignette/vignette_readme.txt` in Git.

### State versions of dependencies

"2. Prerequisites" states that a Python installation is required. However, it is not stated whether RiboViz is only Python 2 compliant, only Python 3 compliant, or compliant with both. There were a number of changes made between Python 2 and 3 which can affect backwards compatibility, the most notable being that commands of form `print "hi"` now need to be written as `print("hi")` and the `L` suffix for long integers is no longer supported, as Python 3 treats all integers as long integers. See [Print is a function](https://docs.python.org/3.0/whatsnew/3.0.html#print-is-a-function) and [Integers](https://docs.python.org/3.0/whatsnew/3.0.html#integers) in [What's New in Python 3.0](https://docs.python.org/3.0/whatsnew/3.0.html#integers).

After starting with Python 3, I found that the Python code in `scripts`/` is only Python 2 compliant. Attempts to run under Python 3 give rise to errors e.g.

```
python scripts/trim_5p_mismatch.py -mm 2 -in vignette/tmp/WT3AT_orf_map.sam -out vignette/tmp/WT3AT_orf_map_clean.sam
  File "scripts/trim_5p_mismatch.py", line 15
    parser.add_argument("-mm","--mismatches",dest="mismatches",nargs='?',default=1L,type=int,help="number of mismatches to allow")
   ^
SyntaxError: invalid syntax
```

See also [Update Python code to be both Python 2 and Python 3 compliant](#update-python-code-to-be-both-python-2-and-python-3-compliant) below.

### State minimum, or known-good, versions

Related to the above, it is useful to document minimum versions of dependencies, or, at least, versions for which RiboViz is known to work.

### State minimal machine specifications

Document minimal requirements for disk space and memory required e.g. to run any examples such as the "vignette".

### Document missing dependencies

Running the "vignette", as documented in `vignette/vignette_readme.txt` revealed missing dependencies e.g.

```
scripts/prepRiboviz.sh: line 131: samtools: command not found
scripts/prepRiboviz.sh: line 129: samtools: command not found
scripts/prepRiboviz.sh: line 133: samtools: command not found
scripts/prepRiboviz.sh: line 138: bedtools: command not found
scripts/prepRiboviz.sh: line 140: bedtools: command not found
```

Document these dependencies:

* [Samtools](http://www.htslib.org/)
* [bedtools](http://bedtools.readthedocs.io/en/latest/)

### Document how to set `samtools` memory-per-thread in `prepRiboviz.sh`

Running `prepRiboviz.sh` with the "vignette" under Python 3 failed to create `.bam` and `.bam.bai` files in `vignette/outputs/`. This was traced to the following command:

```bash
samtools sort -@ ${nprocesses} -O bam -o ${fn_out}.bam -
```

which gave an error:

```
samtools sort: couldn't allocate memory for bam_mem
```

My VM had 2GB of which 1GB was free. By explicitly setting the maximum memory per thread for `samtools` to `256M`, this error was avoided:

```bash
samtools sort -@ ${nprocesses} -m 256M -O bam -o ${fn_out}.bam -
```

(`256M` coupled with `nprocesses: 4` in `vignette_config.yml` sums to 1GB).

### Document how to deploy the web site and Shiny server

With respect to the web site and Shiny server I managed to get "something" running (see [Appendix: Running the web site and Shiny locally](#appendix-running-the-web-site-and-shiny-locally) below). The web site showed pages with interactive graphs and the behaviour of the visualisations was comparable to the live web site at http://riboviz.org. The significant exception was the "Gene of interest" page, GeneBased.html, the content of which would grey-out and display the following error in the terminal window running the Shiny server (this error also arose if visiting the Shiny server URL directly):

```
Warning: Error in h5checktypeOrOpenLoc: Error in h5checktypeOrOpenLoc(). Cannot open file. File './2016/Weinberg/unselected_total_RNA/GSM1969533.h5' does not exist.
```

`Data/` has a file 12016_Weinberg_unselected_total_RNA.tar.gz`, however this does not contain any `.h5` files.

It is unclear how the content of `Data/` relates to `Shiny/` or if there is missing content. Provide documentation, preferably complemented by scripts, to allow for the directory/file structure to be set up and for a local copy of the web site and Shiny server to be deployed.

### Provide an example of symlinking files

"2. Prerequisites" states, for "Input files":

> It is easiest if you put all these files in a single input directory. Alternatively, you could symlink them from a single directory.

Provide an example.

### Add copyright information

The repository has a `LICENSE` file but there is no information on who holds the copyright to RiboViz. A copyright statement helps you inform others as to who "owns" RiboViz and who has granted them permission, via the licence, to use and modify it.

### Add a list of software citations

Add a list of citations of all the software that RiboViz uses, using citations listed on the software's pages, where present. This should include the following.

pysam 0.15.0, https://github.com/pysam-developers/pysam

Li et al. (2009) "The Sequence Alignment/Map format and SAMtools", Bioinformatics. 2009 Aug 15;25(16):2078-9. doi:[10.1093/bioinformatics/btp352](https://doi.org/10.1093/bioinformatics/btp352)

Martin, M. (2011). Cutadapt removes adapter sequences from high-throughput sequencing reads. EMBnet.journal, 17(1), pp. 10-12. doi:[10.14806/ej.17.1.200](https://doi.org/10.14806/ej.17.1.200)

Kim D, Langmead B and Salzberg SL. HISAT: a fast spliced aligner with low memory requirements. Nature Methods 2015. doi:[10.1038/nmeth.3317](https://doi.org/10.1038/nmeth.3317). Epub 2015 Mar 9.

Langmead B, Trapnell C, Pop M, Salzberg SL. Ultrafast and memory-efficient alignment of short DNA sequences to the human genome. Genome Biol 10:R25. doi:[10.1186/gb-2009-10-3-r25](https://doi.org/10.1186/gb-2009-10-3-r25). Epub 2009 Mar 4.

R Core Team (2018) R: A Language and Environment for Statistical Computing, R Foundation for Statistical Computing, Vienna, Austria, 2018. https://www.R-project.org.

Morgan M, Pagès H, Obenchain V, Hayden N (2018). Rsamtools: Binary alignment (BAM), FASTA, variant call (BCF), and tabix file import. R package version 1.32.0, http://bioconductor.org/packages/release/bioc/html/Rsamtools.html.

Fischer B, Pau G, Smith M (2018). rhdf5: HDF5 interface to R. R package version 2.24.0.

Lawrence M, Gentleman R, Carey V (2009). "rtracklayer: an R package for interfacing with genome browsers." Bioinformatics, 25, 1841-1842. doi:[10.1093/bioinformatics/btp328](https://doi.org/10.1093/bioinformatics/btp328), http://bioinformatics.oxfordjournals.org/content/25/14/1841.abstract.

Kevin Ushey (2018). RcppRoll: Efficient Rolling / Windowed Operations. R package version 0.3.0. https://CRAN.R-project.org/package=RcppRoll

Trevor L Davis (2018). optparse: Command Line Option Parser. R package version 1.6.0. https://CRAN.R-project.org/package=optparse

Hadley Wickham and Lionel Henry (2018). tidyr: Easily Tidy Data with 'spread()' and 'gather()' Functions. R package version 0.8.1. https://CRAN.R-project.org/package=tidyr

H. Wickham. ggplot2: Elegant Graphics for Data Analysis. Springer-Verlag New York, 2016. http://ggplot2.org

Winston Chang, Joe Cheng, JJ Allaire, Yihui Xie and Jonathan McPherson (2018). shiny: Web Application Framework for R. R package version 1.1.0. https://CRAN.R-project.org/package=shiny

Plotly Technologies Inc. Collaborative data science. Montréal, QC, 2015. https://plot.ly.

### Add the full citation for D3

On the "Home" page, add the citation for "(Bostock et al., 2011)":

Bostock, Michael and Ogievetsky, Vadim and Heer, Jeffrey, 'D3 Data-Driven Documents', IEEE Transactions on Visualization and Computer Graphics, 17(12), pp2301-2309, December 2011, doi: [10.1109/TVCG.2011.185](http://dx.doi.org/10.1109/TVCG.2011.185).

### Add links

On the "Home" page, make:

* "R/Shiny" link to https://www.r-project.org/ and https://shiny.rstudio.com/.
* "D3" link to https://d3js.org/.

### Don't use "here" or "this" as link text

On the "2. Prerequisites" page, there are three "here" links:

> - rRNA sequences from here.
> - transcript sequences from here.
> - gff file from here

Replace these e.g.

> - Transcript sequences, yeast_CDS_w_250utrs.fa
> - rRNA sequences, rrna.fa
> - Coding sequences locations, yeast_CDS_w_250utrs.gff3

On the "4. Structure of HDF5 data", page there are two "this" links:

> To learn more about accessing and manipulating HDF5 files in R, read this and this.

Replace these e.g.

> To learn more about accessing and manipulating HDF5 files in R, see Bioconductor's HDF5 interface to R and Neon's Introduction to HDF5 Files in R.

### Fix broken links

On the "2. Prerequisites" page, "shiny" links to https://cran.r-project.org/web/packages/ggplot2/index.html). Correct this to https://cran.r-project.org/web/packages/shiny/index.html.

On the "4. Structure of HDF5 data", page the two "this" links are broken:

* https://bioconductor.org/packages/release/bioc/html/rhdf5
* http://neondataskills.org/HDF5/Intro-To-HDF5-In-R/

These (I think) should be fixed to:

* https://bioconductor.org/packages/release/bioc/html/rhdf5.html
* https://www.neonscience.org/hdf5-intro-r

### Reword note-style text

On the "4. Structure of HDF5 data": "Snippet from an example..." => "A snippet from an example..."

### Combine `vignette/vignette_readme.txt` and "3. Mapping mRNA and ribosome protected reads to transcriptome and collecting data into hdf5 file"

Add this to the wiki (and then to another destination as suggested in [Migrate user documentation out of the wiki](#migrate-user-documentation-out-of-the-wiki) above) and combine with "3. Mapping mRNA and ribosome protected reads to transcriptome and collecting data into hdf5 file" so that all the documentation is kept in one place, and in a consistent format.

---

## Source code

### Fix `prepRiboViz.sh` `'orf_gff_file' not found` bug

This bug, and its fix was described in [Running RiboViz](#running-riboviz) above.

### Update Python code to be both Python 2 and Python 3 compliant

The Python code is not Python 3 compliant. [2to3](https://docs.python.org/2/library/2to3.html) is a Python package that proposes changes to Python 2 code to make it Python 3-compliant too. For example, running:

```bash
2to3 scripts/check_fasta_gff.py
```

gives:

```
-    print( "Checking fasta file " + fastain + "\n with gff file " + gffin )
+    print(( "Checking fasta file " + fastain + "\n with gff file " + gffin ))
     
-            print( CDS_coord.seqid + " doesn't start with ATG" )
+            print(( CDS_coord.seqid + " doesn't start with ATG" ))

-            print( CDS_coord.seqid + " doesn't stop at end" )
+            print(( CDS_coord.seqid + " doesn't stop at end" ))

-            print( CDS_coord.seqid + " has internal STOP" )
+            print(( CDS_coord.seqid + " has internal STOP" ))
```

`2to3` can also apply its proposed changes changes in-place:

```bash
2to3 -w scripts/check_fasta_gff.py 
2to3 -w scripts/trim_5p_mismatch.py 
```

### Automatically calculate memory-per-thread for `samtools` in `prepRiboviz.sh` 

As described earlier in [Document how to set `samtools` memory-per-thread in `prepRiboviz.sh`](#document-how-to-set-samtools-memory-per-thread-in-prepribovizsh) a user may need to explcitly set the available memory per thread, based on the free memory available. Automating this would save the user from doing this manually. This could be easier still if `prepRiboviz.sh` was migrated to Python.

### Port shell and Perl scripts to Python

There are shell and Perl scripts in `scripts`:

* `parse_yaml.sh`
* `prepRiboviz.sh`
* `trim_5p_mismatch.pl`
* `trim_5p_mismatch_sam.pl`

Migrating these to Python would reduce the number of languages a developer would need to be familiar with.

### Improve modularity of Python and R scripts

The Python scripts in `scripts/` have all their functionally embedded in `__main__` blocks only, executed when the script is run from the command-line. Similarly, the R scripts in `scripts/` include functions, but, most of their functionality is not embedded in functions but is executed directly when the scripts are run from the command-line.

Refactor these scripts so that any functionality is wrapped into functions in separate Python/R scripts from those that handle command-line invocations. This would decouple the user interface from the underlying functionality. This, in turn, would allow the Python/R functionality to be used within other applications as libraries. It would also make it easier to test discrete parts of the functionality in isolation - it is easier to use Python unit tests and a test framework such as [pytest](https://pytest.org) to test a Python function than to test a Python script invoked from the command-line.

---

## Using Git and GitHub

### Use branches to help to ensure "master" always works

In [Running RiboViz](#running-riboviz) a number of errors in the current version were noted. The risk of such errors seeping into releases can be minimised by adopting an approach to development where the "master" branch is guaranteed, as far as possible, to hold only a stable, working version of RiboViz. Vincent Driessen has proposed [A successful Git branching model](https://nvie.com/posts/a-successful-git-branching-model/) ("Git flow") to help ensure this. The key points are:

* A "master" branch always holds code that is production (or release) ready.
* A "develop" branch holds the latest changes intended for the next release. These are merged into "master" when steady.
* Development of specific features are done in feature branches.
* Releases are tagged e.g. 0.1, 0.2 etc.

Under this model, if a developer wanted to create a new RiboViz feature, they could:

* Fork the RiboViz repository.
* Clone their fork into their development environment.
* Create a new feature branch of "develop".
* Do their development, regularly pushing their feature branch to their fork.
* Submit a pull request for their branch from their fork to "develop" in the RiboViz repository.

This model can be simplified by ignoring the need for a "develop" branch, but taking care that pull requests with changes to "master" are always reviewed before being merged, to ensure that "master" is always production/release ready.

For more on creating releases in GitHub, see [Creating Releases](https://help.github.com/articles/creating-releases/). This can complement the approach just suggested, whenever you know "master" is stable cut a release and recommend users use these tagged releases rather than the latest version in the Git repository.

For numbering of releases, [Semantic Versioning](https://semver.org/) is recommended.

### Beware your repository size

A download of a [ZIP](https://github.com/shahpr/RiboViz/archive/master.zip) of RiboViz from GitHub occupies ~278MB (according to the bash command `du -sm`), ~519M once unzipped (using the Bash command `du -sm`). A clone of the current repository (f77beadc1a4ee71cb720bd90160533ed6db00d7e) occupies ~885MB. This is approaching the 1GB limit that GitHub recommends repositories respect in their page on [What is my disk quota?](https://help.github.com/articles/what-is-my-disk-quota/). GitHub caution that:

> We recommend repositories be kept under 1GB each. This limit is easy to stay within if large files are kept out of the repository. If your repository exceeds 1GB, you might receive a polite email from GitHub Support requesting that you reduce the size of the repository to bring it back down.

The bulk of the space is consumed by the `Data/` directory, which occupies ~338MB.

GitHub recommend [Git Large File Storage](https://git-lfs.github.com/) (Git LFS). In [About storage and bandwidth usage](https://help.github.com/articles/about-storage-and-bandwidth-usage/) GitHub comment that:

> All personal and organization accounts using Git LFS receive 1 GB of free storage and 1 GB a month of free bandwidth. If the bandwidth and storage quotas are not enough, you can choose to purchase an additional quota for Git LFS.

For more information, see GitHub's [Managing large files](https://help.github.com/categories/managing-large-files/).

### Consider depositing your data

If your data has value as research outputs in their own right, consider depositing the data sets in an institutional or general purpose digita repository. For more information, see

* DCC (2014). '[Five steps to decide what data to keep: a checklist for appraising research data v.1](http://www.dcc.ac.uk/resources/how-guides/five-steps-decide-what-data-keep)'. Edinburgh: Digital Curation Centre. Available online: http://www.dcc.ac.uk/resources/how-guides
* Whyte, A. (2015). '[Where to keep research data: DCC checklist for evaluating data repositories](http://www.dcc.ac.uk/resources/how-guides-checklists/where-keep-research-data/where-keep-research-data)' v.1.1 Edinburgh: Digital Curation Centre. Available online: www.dcc.ac.uk/resources/how-guides

### Don't include absolute symbolic links in Git

There are symbolic links in the source code which are specific to the committer's environment e.g.:

```bash
ls -l Data | grep ^l
lrwxrwxrwx 1 ubuntu ubuntu     11 Aug 21 07:32 2009 -> /data/2009/
lrwxrwxrwx 1 ubuntu ubuntu     11 Aug 21 07:32 2010 -> /data/2010/
lrwxrwxrwx 1 ubuntu ubuntu     11 Aug 21 07:32 2011 -> /data/2011/
lrwxrwxrwx 1 ubuntu ubuntu     11 Aug 21 07:32 2012 -> /data/2012/
lrwxrwxrwx 1 ubuntu ubuntu     11 Aug 21 07:32 2013 -> /data/2013/
lrwxrwxrwx 1 ubuntu ubuntu     11 Aug 21 07:32 2014 -> /data/2014/
lrwxrwxrwx 1 ubuntu ubuntu     11 Aug 21 07:32 2015 -> /data/2015/
lrwxrwxrwx 1 ubuntu ubuntu     11 Aug 21 07:32 2016 -> /data/2016/
```
```bash
ls -l Shiny | grep ^l
```
```
lrwxrwxrwx 1 ubuntu ubuntu       18 Aug 21 07:32 2009 -> /data/riboseq/2009
lrwxrwxrwx 1 ubuntu ubuntu       18 Aug 21 07:32 2012 -> /data/riboseq/2012
lrwxrwxrwx 1 ubuntu ubuntu       18 Aug 21 07:32 2013 -> /data/riboseq/2013
lrwxrwxrwx 1 ubuntu ubuntu       18 Aug 21 07:32 2014 -> /data/riboseq/2014
lrwxrwxrwx 1 ubuntu ubuntu       18 Aug 21 07:32 2015 -> /data/riboseq/2015
lrwxrwxrwx 1 ubuntu ubuntu       18 Aug 21 07:32 2016 -> /data/riboseq/2016
lrwxrwxrwx 1 ubuntu ubuntu       35 Aug 21 07:32 RPKM -> /home/txing/shinyProject/data/RPKM/
lrwxrwxrwx 1 ubuntu ubuntu       19 Aug 21 07:32 Supp -> /data/riboseq/Supp/
```

These links are unusable to others who clone the repository.

---

## Examples of implemented recommendations

[ssi](https://github.com/softwaresaved/RiboViz/tree/ssi) is a branch of a [fork](https://github.com/softwaresaved/RiboViz/) of RiboViz in the [softwaresaved](https://github.com/softwaresaved) project. It was branched from the current version (f77beadc1a4ee71cb720bd90160533ed6db00d7e) of the RiboViz Git repository.

It has examples implementing the following recommendations:

* Resources
  - Migrate user documentation out of the wiki
* Documentation
  - State versions of dependencies
  - State minimum, or known-good, versions
  - Document missing dependencies
  - Document how to set `samtools` memory-per-thread in `prepRiboviz.sh`
  - Provide an example of symlinking files
  - Add links
  - Don't use "here" or "this" as link text
  - Fix broken links
  - Reword note-style text
  - Combine `vignette/vignette_readme.txt` and "3. Mapping mRNA and ribosome protected reads to transcriptome and collecting data into hdf5 file" (combined then migrated out of the wiki as above)
* Source code
  - Fix `prepRiboViz.sh` `'orf_gff_file' not found` bug
  - Update Python code to be both Python 2 and Python 3 compliant

The refactored documentation, and that migrated from the wiki, is in `docs/install.md`.

`vignette/vignette_config.yaml` has `nprocesses: 1` by default.

The tests of [Running RiboViz](#running-riboviz) were done using dependencies set up using this expanded documentation.

`install/` has scripts that I wrote to reinstall RiboViz dependencies after my virtual machine decided to stop working and needed to be rebuilt. These are documented in `install/quick-install.md`.

---

## Appendix: Running the web site and Shiny locally

Start Shiny on http://localhost:4000:

```bash
R -e "shiny::runApp('Shiny', 4000)"
```

Edit `Frontend/GeneBased.html`:

* Change:

```html
<iframe height="1000px" width="100%" src="../yeast/" style="border: none;"></iframe>
```

* to specify the location of your Shiny server:

```html
<iframe height="1000px" width="100%" src="http://localhost:4000" style="border: none;"></iframe>
```

Start a web server on http://localhost:4000:

* If using Python 2, run:

```bash
python -m SimpleHTTPServer 8000
```

* If using Python 3, run:

```bash
python -m http.server 8000
```

Visit http://localhost:8000

---

## Appendix: Validating a `Citation.cff` file

`Citation.cff` can be validated automatically as follows. This uses a Python package which requires Python 3.

1. Get the Citation File Format schema:

```bash
git clone https://github.com/citation-file-format/schema
```

2. Install [pykwalifire](https://github.com/sdruskat/pykwalifire), YAML/JSON schema validation library:

```bash
pip install pykwalifire
```

3. Validate `Citation.cff`:

```bash
pykwalifire -s schema/CFF-Core/schema.yaml -d Citation.cff  -y cff
```
