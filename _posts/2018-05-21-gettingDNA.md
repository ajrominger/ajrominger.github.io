---
title: "Getting sequence data with R"
author: "A. J. Rominger"
date: "21 May 2018"
tags: diversification evo-ecology data-science
---


I've always been enamored with the genus *Castilleja*. I thought it would be fun to see how much sequence data there is for the group in the hopes of one day doing a cool phylogenetic/biogeographic study.

The folks at [rOpenSci](https://ropensci.org/) have given us a lot of help with the package *rentrez*. Below I work through getting actual sequence data for all *Castilleja* in GenBank.

``` r
library(XML)
library(rentrez)

# retrieve Castilleja sequence IDs
castilleja <- entrez_search(db = 'nuccore', term = 'Castilleja[ORGN]', 
                            retmax = 1000)

# get one sequence
x <- entrez_fetch(db = 'nuccore', id = castilleja$ids[1], 
                  rettype = 'native', retmode = 'xml', 
                  parsed = TRUE)

# parse XML to list
x <- xmlToList(x)
```

Figuring out the XML
--------------------

With that one sequence in hand we need to figure out what from the unruly XML we want. I'd like information about the sequence ID, taxon sequenced, what was sequenced (locus or primers), where the specimen comes from, museum curation information, publication information, and of course the sequence itself.

Getting those things is just a matter of figuring out where they live, which I have no better solution for other than poking around in the XML. Which is how I got the following. First note there are redundancies in the XML nodes, so if we convert the list `x` into a vector, each entry will have a unique name because all the nodes are concatenated.

``` r
# turn list into vector
x <- unlist(x)

# all nodes share this in common:
basePath <- 'Bioseq-set_seq-set.Seq-entry.Seq-entry_seq.Bioseq'
```

### Sequence ID

GenBank:

``` r
x[paste(basePath, 
        'Bioseq_id', 'Seq-id', 'Seq-id_genbank', 'Textseq-id', 'Textseq-id_accession', 
        sep = '.')]
```

    ## Bioseq-set_seq-set.Seq-entry.Seq-entry_seq.Bioseq.Bioseq_id.Seq-id.Seq-id_genbank.Textseq-id.Textseq-id_accession 
    ##                                                                                                        "MG220179"

Broader NCBI:

``` r
x[paste(basePath, 
        'Bioseq_id', 'Seq-id', 'Seq-id_gi',
        sep = '.')]
```

    ## Bioseq-set_seq-set.Seq-entry.Seq-entry_seq.Bioseq.Bioseq_id.Seq-id.Seq-id_gi 
    ##                                                                 "1331395866"

### Taxon

``` r
x[paste(basePath,
        'Bioseq_descr', 'Seq-descr', 'Seqdesc', 'Seqdesc_source', 'BioSource',
        'BioSource_org', 'Org-ref', 'Org-ref_taxname', 
        sep = '.')]
```

    ## Bioseq-set_seq-set.Seq-entry.Seq-entry_seq.Bioseq.Bioseq_descr.Seq-descr.Seqdesc.Seqdesc_source.BioSource.BioSource_org.Org-ref.Org-ref_taxname 
    ##                                                                                                                           "Castilleja rupicola"

### Locus information

This can be tricky, some is in the description

``` r
x[paste(basePath,
        'Bioseq_descr', 'Seq-descr', 'Seqdesc', 'Seqdesc_title', 
        sep = '.')]
```

    ##                                                                                                    Bioseq-set_seq-set.Seq-entry.Seq-entry_seq.Bioseq.Bioseq_descr.Seq-descr.Seqdesc.Seqdesc_title 
    ## "Castilleja rupicola voucher CCDB-24910-B07 5.8S ribosomal RNA gene, partial sequence; internal transcribed spacer 2, complete sequence; and large subunit ribosomal RNA gene, partial sequence."

More can be found in other attributes

``` r
x[paste(basePath,
        'Bioseq_descr', 'Seq-descr', 'Seqdesc', 'Seqdesc_molinfo', 
        'MolInfo', 'MolInfo_biomol', '', 'attrs.value', 
        sep = '.')]
```

    ## Bioseq-set_seq-set.Seq-entry.Seq-entry_seq.Bioseq.Bioseq_descr.Seq-descr.Seqdesc.Seqdesc_molinfo.MolInfo.MolInfo_biomol..attrs.value 
    ##                                                                                                                            "genomic"

``` r
x[paste(basePath,
        'Bioseq_annot', 'Seq-annot', 'Seq-annot_data', 'Seq-annot_data_ftable', 
        'Seq-feat', 'Seq-feat_comment', 
        sep = '.')]
```

    ## Bioseq-set_seq-set.Seq-entry.Seq-entry_seq.Bioseq.Bioseq_annot.Seq-annot.Seq-annot_data.Seq-annot_data_ftable.Seq-feat.Seq-feat_comment 
    ##                                           "contains 5.8S ribosomal RNA, internal transcribed spacer 2, and large subunit ribosomal RNA"

We can get the length of the sequence

``` r
as.integer(x[paste(basePath,
                   'Bioseq_annot', 'Seq-annot', 'Seq-annot_data', 
                   'Seq-annot_data_ftable', 'Seq-feat', 
                   'Seq-feat_location', 'Seq-loc', 'Seq-loc_int', 
                   'Seq-interval', 'Seq-interval_to', 
        sep = '.')]) -
    as.integer(x[paste(basePath,
                       'Bioseq_annot', 'Seq-annot', 'Seq-annot_data', 
                       'Seq-annot_data_ftable', 'Seq-feat', 
                       'Seq-feat_location', 'Seq-loc', 'Seq-loc_int', 
                       'Seq-interval', 'Seq-interval_from', 
                       sep = '.')])
```

    ## [1] 351

Perhaps most definitive we can get the primers

``` r
x[paste(basePath,
        'Bioseq_descr', 'Seq-descr', 'Seqdesc', 'Seqdesc_source', 
        'BioSource', 'BioSource_pcr-primers', 'PCRReactionSet', 
        'PCRReaction', 'PCRReaction_forward', 'PCRPrimerSet', 'PCRPrimer', 
        'PCRPrimer_seq', 'PCRPrimerSeq', 
        sep = '.')]
```

    ## Bioseq-set_seq-set.Seq-entry.Seq-entry_seq.Bioseq.Bioseq_descr.Seq-descr.Seqdesc.Seqdesc_source.BioSource.BioSource_pcr-primers.PCRReactionSet.PCRReaction.PCRReaction_forward.PCRPrimerSet.PCRPrimer.PCRPrimer_seq.PCRPrimerSeq 
    ##                                                                                                                                                                                                           "atgcgatacttggtgtgaat"

``` r
x[paste(basePath,
        'Bioseq_descr', 'Seq-descr', 'Seqdesc', 'Seqdesc_source', 
        'BioSource', 'BioSource_pcr-primers', 'PCRReactionSet', 
        'PCRReaction', 'PCRReaction_reverse', 'PCRPrimerSet', 'PCRPrimer', 
        'PCRPrimer_seq', 'PCRPrimerSeq', 
        sep = '.')]
```

    ## Bioseq-set_seq-set.Seq-entry.Seq-entry_seq.Bioseq.Bioseq_descr.Seq-descr.Seqdesc.Seqdesc_source.BioSource.BioSource_pcr-primers.PCRReactionSet.PCRReaction.PCRReaction_reverse.PCRPrimerSet.PCRPrimer.PCRPrimer_seq.PCRPrimerSeq 
    ##                                                                                                                                                                                                           "tcctccgcttattgatatgc"

### Specimen location

Latitude and longitude if provided

``` r
x[grep('lat-lon', x) + 1]
```

    ## Bioseq-set_seq-set.Seq-entry.Seq-entry_seq.Bioseq.Bioseq_descr.Seq-descr.Seqdesc.Seqdesc_source.BioSource.BioSource_subtype.SubSource.SubSource_name 
    ##                                                                                                                                 "49.158 N 121.594 W"

Country and other geography from the specimen label

``` r
x[grep('country', x) + 1]
```

    ##                                                                                     Bioseq-set_seq-set.Seq-entry.Seq-entry_seq.Bioseq.Bioseq_descr.Seq-descr.Seqdesc.Seqdesc_source.BioSource.BioSource_subtype.SubSource.SubSource_name 
    ## "Canada: British Columbia, Cascade Mountains, Skagit Range, Foley Peak, south slope, ca. 13 km northwest of north end of Chilliwack Lake, Cascade Mtns, Skagit Range, Foley Peak, south slope, ca. 13 km NW of N end of Chilliwack Lake"

Collection date info

``` r
x[grep('collection-date', x) + 1]
```

    ## Bioseq-set_seq-set.Seq-entry.Seq-entry_seq.Bioseq.Bioseq_descr.Seq-descr.Seqdesc.Seqdesc_source.BioSource.BioSource_subtype.SubSource.SubSource_name 
    ##                                                                                                                                        "21-Sep-1999"

### Museum/curration information

Unique ID

``` r
x[paste(basePath,
        'Bioseq_descr', 'Seq-descr', 'Seqdesc', 'Seqdesc_source', 
        'BioSource', 'BioSource_org', 'Org-ref', 'Org-ref_orgname', 
        'OrgName', 'OrgName_mod', 'OrgMod', 'OrgMod_subname', 
        sep = '.')]
```

    ## Bioseq-set_seq-set.Seq-entry.Seq-entry_seq.Bioseq.Bioseq_descr.Seq-descr.Seqdesc.Seqdesc_source.BioSource.BioSource_org.Org-ref.Org-ref_orgname.OrgName.OrgName_mod.OrgMod.OrgMod_subname 
    ##                                                                                                                                                                          "CCDB-24910-B07"

Other database (e.g. BOLD)

``` r
x[paste(basePath,
        'Bioseq_descr', 'Seq-descr', 'Seqdesc', 'Seqdesc_source', 
        'BioSource', 'BioSource_org', 'Org-ref', 'Org-ref_db', 
        'Dbtag', 'Dbtag_db', 
        sep = '.')]
```

    ## Bioseq-set_seq-set.Seq-entry.Seq-entry_seq.Bioseq.Bioseq_descr.Seq-descr.Seqdesc.Seqdesc_source.BioSource.BioSource_org.Org-ref.Org-ref_db.Dbtag.Dbtag_db 
    ##                                                                                                                                                    "BOLD"

``` r
x[paste(basePath,
        'Bioseq_descr', 'Seq-descr', 'Seqdesc', 'Seqdesc_source', 
        'BioSource', 'BioSource_org', 'Org-ref', 'Org-ref_db', 
        'Dbtag', 'Dbtag_tag', 'Object-id', 'Object-id_str', 
        sep = '.')]
```

    ## Bioseq-set_seq-set.Seq-entry.Seq-entry_seq.Bioseq.Bioseq_descr.Seq-descr.Seqdesc.Seqdesc_source.BioSource.BioSource_org.Org-ref.Org-ref_db.Dbtag.Dbtag_tag.Object-id.Object-id_str 
    ##                                                                                                                                                                 "PCUBC113-14.ITS2"

### Publication

PubMed

``` r
x[paste(basePath,
        'Bioseq_descr', 'Seq-descr', 'Seqdesc', 'Seqdesc_pub', 
        'Pubdesc', 'Pubdesc_pub', 'Pub-equiv', 'Pub', 'Pub_article', 
        'Cit-art', 'Cit-art_ids', 'ArticleIdSet', 'ArticleId', 
        'ArticleId_pubmed', 'PubMedId', 
        sep = '.')]
```

    ## Bioseq-set_seq-set.Seq-entry.Seq-entry_seq.Bioseq.Bioseq_descr.Seq-descr.Seqdesc.Seqdesc_pub.Pubdesc.Pubdesc_pub.Pub-equiv.Pub.Pub_article.Cit-art.Cit-art_ids.ArticleIdSet.ArticleId.ArticleId_pubmed.PubMedId 
    ##                                                                                                                                                                                                      "29299394"

DOI

``` r
x[paste(basePath,
        'Bioseq_descr', 'Seq-descr', 'Seqdesc', 'Seqdesc_pub', 
        'Pubdesc', 'Pubdesc_pub', 'Pub-equiv', 'Pub', 'Pub_article', 
        'Cit-art', 'Cit-art_ids', 'ArticleIdSet', 'ArticleId', 
        'ArticleId_doi', 'DOI', 
        sep = '.')]
```

    ## Bioseq-set_seq-set.Seq-entry.Seq-entry_seq.Bioseq.Bioseq_descr.Seq-descr.Seqdesc.Seqdesc_pub.Pubdesc.Pubdesc_pub.Pub-equiv.Pub.Pub_article.Cit-art.Cit-art_ids.ArticleIdSet.ArticleId.ArticleId_doi.DOI 
    ##                                                                                                                                                                                  "10.3732/apps.1700079"

### Sequence itself

``` r
x[paste(basePath,
        'Bioseq_inst', 'Seq-inst', 'Seq-inst_seq-data', 'Seq-data', 
        'Seq-data_ncbi2na', 'NCBI2na', 
        sep = '.')]
```

    ##                                                         Bioseq-set_seq-set.Seq-entry.Seq-entry_seq.Bioseq.Bioseq_inst.Seq-inst.Seq-inst_seq-data.Seq-data.Seq-data_ncbi2na.NCBI2na 
    ## "E48356E05362DFE0642F995825D1A58A919797A9B464D9B6D55356D67590A9BDABAEA9A13E9556E67936669697039895BA61D1B4610BAEBE0D741DDB9ED3A63D8B644DA91BAC8541A54378FB97D8598552B4A6A345678BF0"

The DNA sequence is stored in `ncbi2na` format, which is `ASN.1` encoded binary. Borrowing from Kinser (2010) we can decode this as follows:

``` r
# function to parse dna from ASN.1
dnaFromASN1 <- function(x) {
    x <- strsplit(x, '')[[1]]
    x[x == '0'] <- 'AA'
    x[x == '1'] <- 'AC'
    x[x == '2'] <- 'AG'
    x[x == '3'] <- 'AT'
    x[x == '4'] <- 'CA'
    x[x == '5'] <- 'CC'
    x[x == '6'] <- 'CG'
    x[x == '7'] <- 'CT'
    x[x == '8'] <- 'GA'
    x[x == '9'] <- 'GC'
    x[x == 'A'] <- 'GG'
    x[x == 'B'] <- 'GT'
    x[x == 'C'] <- 'TA'
    x[x == 'D'] <- 'TC'
    x[x == 'E'] <- 'TG'
    x[x == 'F'] <- 'TT'
    
    return(paste(x, collapse = ''))
}
```

And test it:

``` r
# get ASN.1 formatted sequence
asn1 <- dnaFromASN1(x[paste(basePath,
                            'Bioseq_inst', 'Seq-inst', 'Seq-inst_seq-data', 
                            'Seq-data', 'Seq-data_ncbi2na', 'NCBI2na', 
                            sep = '.')])

# get FASTA sequence
fasta <- entrez_fetch(db = 'nuccore', id = castilleja$ids[1], rettype = 'fasta')
fasta <- paste(strsplit(fasta, '\n')[[1]][-1], collapse = '')

# compare the two
asn1 == fasta
```

    ## [1] TRUE

Putting it all together
-----------------------

Let's make a function that can take a vector of NCBI IDs and return a list with sequence data, and metadata about the sequence. To start the sequence data could just be a vector of class `character` and for each entry in that sequence vector, we could have a row in an accompanying `data.frame` with the metadata for that sequence.

First note, when we call `rentrez::entrez_fetch` for multiple IDs, and turn the output into a list, we get a list whose first element contains an entry for each record:

``` r
# get multiple sequences
x <- entrez_fetch(db = 'nuccore', id = castilleja$ids[1:3], 
                  rettype = 'native', retmode = 'xml', 
                  parsed = TRUE)
x <- xmlToList(x)
length(x[[1]])
```

    ## [1] 3

Each record starts with

``` r
names(x[[1]])
```

    ## [1] "Seq-entry" "Seq-entry" "Seq-entry"

So our function to get all records should first use `rentrez::entrez_fetch` on all the IDs, then loop over each `Seq-entry` and extract the relevant information. We can first write the function to get the relevant information. Let's assume that each `Seq-entry` element has been turned into a vector as before

``` r
cleanEntrez <- function(x) {
    # starting after `Seq-entry` we now need a shorter base path
    basePath <- 'Seq-entry_seq.Bioseq'
    
    # return everything we want
    c(
        genbank = as.character(x[paste(basePath, 
                                       'Bioseq_id', 'Seq-id', 'Seq-id_genbank', 
                                       'Textseq-id', 'Textseq-id_accession', 
                                       sep = '.')]),
        ncbi = as.character(x[paste(basePath, 
                                    'Bioseq_id', 'Seq-id', 'Seq-id_gi',
                                    sep = '.')]),
        taxon = as.character(x[paste(basePath,
                                     'Bioseq_descr', 'Seq-descr', 'Seqdesc', 
                                     'Seqdesc_source', 'BioSource', 'BioSource_org', 
                                     'Org-ref', 'Org-ref_taxname', 
                                     sep = '.')]),
        seqdesc_title = as.character(x[paste(basePath,
                                             'Bioseq_descr', 'Seq-descr', 'Seqdesc', 
                                             'Seqdesc_title', 
                                             sep = '.')]),
        biomol = as.character(x[paste(basePath,
                                      'Bioseq_descr', 'Seq-descr', 'Seqdesc', 
                                      'Seqdesc_molinfo', 'MolInfo', 'MolInfo_biomol', '', 
                                      'attrs.value', 
                                      sep = '.')]),
        seqfeat_comment = as.character(x[paste(basePath,
                                               'Bioseq_annot', 'Seq-annot', 
                                               'Seq-annot_data', 'Seq-annot_data_ftable', 
                                               'Seq-feat', 'Seq-feat_comment', 
                                               sep = '.')]),
        seq_length = as.integer(x[paste(basePath,
                                        'Bioseq_annot', 'Seq-annot', 'Seq-annot_data', 
                                        'Seq-annot_data_ftable', 'Seq-feat', 
                                        'Seq-feat_location', 'Seq-loc', 'Seq-loc_int', 
                                        'Seq-interval', 'Seq-interval_to', 
                                        sep = '.')]) -
            as.integer(x[paste(basePath,
                               'Bioseq_annot', 'Seq-annot', 'Seq-annot_data', 
                               'Seq-annot_data_ftable', 'Seq-feat', 
                               'Seq-feat_location', 'Seq-loc', 'Seq-loc_int', 
                               'Seq-interval', 'Seq-interval_from', 
                               sep = '.')]),
        primer_forward = as.character(x[paste(basePath,
                                              'Bioseq_descr', 'Seq-descr', 'Seqdesc', 
                                              'Seqdesc_source', 'BioSource', 
                                              'BioSource_pcr-primers', 'PCRReactionSet', 
                                              'PCRReaction', 'PCRReaction_forward', 
                                              'PCRPrimerSet', 'PCRPrimer', 'PCRPrimer_seq', 
                                              'PCRPrimerSeq', 
                                              sep = '.')]),
        primer_reverse = as.character(x[paste(basePath,
                                              'Bioseq_descr', 'Seq-descr', 'Seqdesc', 
                                              'Seqdesc_source', 'BioSource', 
                                              'BioSource_pcr-primers', 'PCRReactionSet', 
                                              'PCRReaction', 'PCRReaction_reverse', 
                                              'PCRPrimerSet', 'PCRPrimer', 'PCRPrimer_seq', 
                                              'PCRPrimerSeq', 
                                              sep = '.')]),
        lat_lon = as.character(x[grep('lat-lon', x) + 1]),
        geo_description = as.character(x[grep('country', x) + 1]),
        coll_date = as.character(x[grep('collection-date', x) + 1]),
        museum_UID = as.character(x[paste(basePath,
                                          'Bioseq_descr', 'Seq-descr', 'Seqdesc', 
                                          'Seqdesc_source', 'BioSource', 'BioSource_org', 
                                          'Org-ref', 'Org-ref_orgname', 'OrgName', 
                                          'OrgName_mod', 'OrgMod', 'OrgMod_subname', 
                                          sep = '.')]),
        other_db = as.character(x[paste(basePath,
                                        'Bioseq_descr', 'Seq-descr', 'Seqdesc', 
                                        'Seqdesc_source', 'BioSource', 'BioSource_org', 
                                        'Org-ref', 'Org-ref_db', 'Dbtag', 'Dbtag_db', 
                                        sep = '.')]),
        other_db_id = as.character(x[paste(basePath,
                                           'Bioseq_descr', 'Seq-descr', 'Seqdesc', 
                                           'Seqdesc_source', 'BioSource', 'BioSource_org', 
                                           'Org-ref', 'Org-ref_db', 'Dbtag', 'Dbtag_tag', 
                                           'Object-id', 'Object-id_str', 
                                           sep = '.')]),
        pubmed = as.character(x[paste(basePath,
                                      'Bioseq_descr', 'Seq-descr', 'Seqdesc', 'Seqdesc_pub', 
                                      'Pubdesc', 'Pubdesc_pub', 'Pub-equiv', 'Pub', 
                                      'Pub_article', 'Cit-art', 'Cit-art_ids', 
                                      'ArticleIdSet', 'ArticleId', 'ArticleId_pubmed', 
                                      'PubMedId', 
                                      sep = '.')]),
        doi = as.character(x[paste(basePath,
                                   'Bioseq_descr', 'Seq-descr', 'Seqdesc', 'Seqdesc_pub', 
                                   'Pubdesc', 'Pubdesc_pub', 'Pub-equiv', 'Pub', 
                                   'Pub_article', 'Cit-art', 'Cit-art_ids', 'ArticleIdSet', 
                                   'ArticleId', 'ArticleId_doi', 'DOI', 
                                   sep = '.')]),
        sequence = dnaFromASN1(x[paste(basePath,
                                       'Bioseq_inst', 'Seq-inst', 'Seq-inst_seq-data', 
                                       'Seq-data', 'Seq-data_ncbi2na', 'NCBI2na', 
                                       sep = '.')])
    )
}

# test it
cleanEntrez(unlist(x[[1]][[1]]))
```

    ##                                                                                                                                                                                                                                                                                                                                                            genbank 
    ##                                                                                                                                                                                                                                                                                                                                                         "MG220179" 
    ##                                                                                                                                                                                                                                                                                                                                                               ncbi 
    ##                                                                                                                                                                                                                                                                                                                                                       "1331395866" 
    ##                                                                                                                                                                                                                                                                                                                                                              taxon 
    ##                                                                                                                                                                                                                                                                                                                                              "Castilleja rupicola" 
    ##                                                                                                                                                                                                                                                                                                                                                      seqdesc_title 
    ##                                                                                                                                                                  "Castilleja rupicola voucher CCDB-24910-B07 5.8S ribosomal RNA gene, partial sequence; internal transcribed spacer 2, complete sequence; and large subunit ribosomal RNA gene, partial sequence." 
    ##                                                                                                                                                                                                                                                                                                                                                             biomol 
    ##                                                                                                                                                                                                                                                                                                                                                          "genomic" 
    ##                                                                                                                                                                                                                                                                                                                                                    seqfeat_comment 
    ##                                                                                                                                                                                                                                                                      "contains 5.8S ribosomal RNA, internal transcribed spacer 2, and large subunit ribosomal RNA" 
    ##                                                                                                                                                                                                                                                                                                                                                         seq_length 
    ##                                                                                                                                                                                                                                                                                                                                                              "351" 
    ##                                                                                                                                                                                                                                                                                                                                                     primer_forward 
    ##                                                                                                                                                                                                                                                                                                                                             "atgcgatacttggtgtgaat" 
    ##                                                                                                                                                                                                                                                                                                                                                     primer_reverse 
    ##                                                                                                                                                                                                                                                                                                                                             "tcctccgcttattgatatgc" 
    ##                                                                                                                                                                                                                                                                                                                                                            lat_lon 
    ##                                                                                                                                                                                                                                                                                                                                               "49.158 N 121.594 W" 
    ##                                                                                                                                                                                                                                                                                                                                                    geo_description 
    ##                                                                                                                           "Canada: British Columbia, Cascade Mountains, Skagit Range, Foley Peak, south slope, ca. 13 km northwest of north end of Chilliwack Lake, Cascade Mtns, Skagit Range, Foley Peak, south slope, ca. 13 km NW of N end of Chilliwack Lake" 
    ##                                                                                                                                                                                                                                                                                                                                                          coll_date 
    ##                                                                                                                                                                                                                                                                                                                                                      "21-Sep-1999" 
    ##                                                                                                                                                                                                                                                                                                                                                         museum_UID 
    ##                                                                                                                                                                                                                                                                                                                                                   "CCDB-24910-B07" 
    ##                                                                                                                                                                                                                                                                                                                                                           other_db 
    ##                                                                                                                                                                                                                                                                                                                                                             "BOLD" 
    ##                                                                                                                                                                                                                                                                                                                                                        other_db_id 
    ##                                                                                                                                                                                                                                                                                                                                                 "PCUBC113-14.ITS2" 
    ##                                                                                                                                                                                                                                                                                                                                                             pubmed 
    ##                                                                                                                                                                                                                                                                                                                                                         "29299394" 
    ##                                                                                                                                                                                                                                                                                                                                                                doi 
    ##                                                                                                                                                                                                                                                                                                                                             "10.3732/apps.1700079" 
    ##                                                                                                                                                                                                                                                                                                                                                           sequence 
    ## "TGCAGAATCCCGTGAACCATCGAGTCTTTGAACGCAAGTTGCGCCCGAAGCCTCACGGCCGAGGGCACGCCTGCCTGGGCGTCACGCATCGCGTCGTCCCCCATCCCGTCCGCTCCGCAAGGGCGTTCGGGTGGTGGGGCGGACATTGGCCCCCCGTGCGCTGCATCGCGCGGCCGGCCTAAATGCGAGCCCGTGGCGACTCACGTCACGACAAGTGGTGGTTGAATCCTCAACTCTCGTGCTGTCATGGCGATTCGAGTCGCACATCGGGCACGTGGTAGACCCAACGGCCCAATCTGATTGTGCCTTCGACCGCGACCCCAGGTCAGGCGGGATCACCCGCTGAGTTTAA"

Now we can bundle this into a function to extract data for multiple records

``` r
# function to loop over records, extracting data from each
getGenBankSeq <- function(ids) {
    allRec <- entrez_fetch(db = 'nuccore', id = ids, 
                           rettype = 'native', retmode = 'xml', 
                           parsed = TRUE)
    allRec <- xmlToList(allRec)[[1]]
    
    o <- lapply(allRec, function(x) {
        cleanEntrez(unlist(x))
    })
    
    temp <- array(unlist(o), dim = c(length(o[[1]]), length(ids)))
    seqVec <- temp[nrow(temp), ]
    seqDF <- as.data.frame(t(temp[-nrow(temp), ]))
    names(seqDF) <- names(o[[1]])[-nrow(temp)]
    
    return(list(seq = seqVec, data = seqDF))
}


# test it
getGenBankSeq(castilleja$ids[1:3])
```

    ## $seq
    ## [1] "TGCAGAATCCCGTGAACCATCGAGTCTTTGAACGCAAGTTGCGCCCGAAGCCTCACGGCCGAGGGCACGCCTGCCTGGGCGTCACGCATCGCGTCGTCCCCCATCCCGTCCGCTCCGCAAGGGCGTTCGGGTGGTGGGGCGGACATTGGCCCCCCGTGCGCTGCATCGCGCGGCCGGCCTAAATGCGAGCCCGTGGCGACTCACGTCACGACAAGTGGTGGTTGAATCCTCAACTCTCGTGCTGTCATGGCGATTCGAGTCGCACATCGGGCACGTGGTAGACCCAACGGCCCAATCTGATTGTGCCTTCGACCGCGACCCCAGGTCAGGCGGGATCACCCGCTGAGTTTAA"                                                                                                
    ## [2] "TGCAGAATCCCGTGAACCATCGAGTCTTTGAACGCAAGTTGCGCCCGAAGCCATTAGGTTGAGGGCACGTCTGCCTGGGCGTCACATATCGTTGCCCTATGCCCAACGCCTAGTGATAGGCACTGTGCAGGGCGAATGTTGGCTTCCCGTGAGCGTTGTTGTCTCATGGTTGGCTGAAAACCGAGTCCTTGGTAGGGTGTTTGCCATGATAGATGGTGGTTGTGTGACCCACGAGACCGATCATGTGCGTCTCTACCAGATTTGGCCTCCAGTGACCCACGTGCGTCTTTGAGCGCTCATGACGAGACCTCAGGTCAGGCGGGGCTACCCGCTGAATTTAAGCATATCAATAAGCGGAGGAAAAGAAACTAACAAGGATTCCCTTAGTAACGGCGAGCGAACCGGGAAGAGCCCACCATGAGAATCGGTCGCCCCTGGCGTTCGAAAA"
    ## [3] "GCAAGTTGCGCCCGAAGCCTCACGGCCGAGGGCACGCCTGCCTGGGCGTCACGCATCGCGTCGTCCCCCATCCCGTCCGCTCCGCAAGGGCGTTCGGGTGGTGGGGCGGACATTGGCCCCCCGTGCGCTGCGTCGCGCGGCCGGCCTAAATGCGAGCCCGTGGCGACTCACGTCACGACAAGTGGTGGTTGAATCCTCAACTCTCGTGCTGTTGTGGCGATTCGAGTCGCACATCGGGCACGTGGTAGACCCAACGGCCCAATCTGATTGTGCCTTCGACCGCGACCCCAGGTCAGGCGGGATCACCCGCTGAGTTTAAA"                                                                                                                                
    ## 
    ## $data
    ##    genbank       ncbi                 taxon
    ## 1 MG220179 1331395866   Castilleja rupicola
    ## 2 MG220112 1331395798    Castilleja caudata
    ## 3 MG220107 1331395793 Castilleja parviflora
    ##                                                                                                                                                                                       seqdesc_title
    ## 1   Castilleja rupicola voucher CCDB-24910-B07 5.8S ribosomal RNA gene, partial sequence; internal transcribed spacer 2, complete sequence; and large subunit ribosomal RNA gene, partial sequence.
    ## 2     Castilleja caudata voucher CCDB-18342-F4 5.8S ribosomal RNA gene, partial sequence; internal transcribed spacer 2, complete sequence; and large subunit ribosomal RNA gene, partial sequence.
    ## 3 Castilleja parviflora voucher CCDB-20338-A06 5.8S ribosomal RNA gene, partial sequence; internal transcribed spacer 2, complete sequence; and large subunit ribosomal RNA gene, partial sequence.
    ##    biomol
    ## 1 genomic
    ## 2 genomic
    ## 3 genomic
    ##                                                                               seqfeat_comment
    ## 1 contains 5.8S ribosomal RNA, internal transcribed spacer 2, and large subunit ribosomal RNA
    ## 2 contains 5.8S ribosomal RNA, internal transcribed spacer 2, and large subunit ribosomal RNA
    ## 3 contains 5.8S ribosomal RNA, internal transcribed spacer 2, and large subunit ribosomal RNA
    ##   seq_length          primer_forward        primer_reverse
    ## 1        351    atgcgatacttggtgtgaat  tcctccgcttattgatatgc
    ## 2        444    atgcgatacttggtgtgaat gacgcttctccagactacaat
    ## 3        318 attcccggaccacgcctggctga  tcctccgcttattgatatgc
    ##              lat_lon
    ## 1 49.158 N 121.594 W
    ## 2 65.324 N 134.533 W
    ## 3  47.25 N 120.283 W
    ##                                                                                                                                                                                                                          geo_description
    ## 1 Canada: British Columbia, Cascade Mountains, Skagit Range, Foley Peak, south slope, ca. 13 km northwest of north end of Chilliwack Lake, Cascade Mtns, Skagit Range, Foley Peak, south slope, ca. 13 km NW of N end of Chilliwack Lake
    ## 2                                                                                                                                                                        Canada: Yukon Territory, Bonnet Plume River, near Margaret Lake
    ## 3                                                                                                                                   USA: Washington, Sec. 4, T.2 ON., R. 21E., on Ellensburg-Wenatchee Rd., ca. 5 mi N of Colochum Pass.
    ##     coll_date     museum_UID other_db       other_db_id   pubmed
    ## 1 21-Sep-1999 CCDB-24910-B07     BOLD  PCUBC113-14.ITS2 29299394
    ## 2 09-Jul-2005  CCDB-18342-F4     BOLD BBYUK1832-12.ITS2 29299394
    ## 3 12-May-1968 CCDB-20338-A06     BOLD MKTRT1773-14.ITS2 29299394
    ##                    doi
    ## 1 10.3732/apps.1700079
    ## 2 10.3732/apps.1700079
    ## 3 10.3732/apps.1700079

References
----------

Kinser, Jason. 2010. *Python for Bioinformatics*. Jones & Bartlett Publishers.
