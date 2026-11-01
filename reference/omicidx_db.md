# Tab-completable lazy tables for an OmicIDX release

Returns every table in the release as a lazy `dplyr` table, nested by
schema, so that `db$sradb$<TAB>` completes table names. Each table is
built the first time it is accessed, and no data are read until a query
is run (for example with
[`dplyr::collect()`](https://dplyr.tidyverse.org/reference/compute.html)).

## Usage

``` r
omicidx_db(con = NULL, version = "latest")
```

## Arguments

- con:

  A connection from
  [`omicidx_connect()`](https://seandavi.github.io/omicidxr/reference/omicidx_connect.md),
  or a version string to open a new one. Defaults to `version`.

- version:

  character(1). Release to connect to when `con` is `NULL`; see
  [`omicidx_connect()`](https://seandavi.github.io/omicidxr/reference/omicidx_connect.md).

## Value

An `omicidx_db` object: a named list with one element per schema, each
an environment of lazy tables. The connection is available as
`attr(db, "con")`, and its release, with row counts, as
`omicidx_release(db)`. All omicidxr helpers accept an `omicidx_db` in
place of a connection.

## Examples

``` r
db <- omicidx_db()
db
#> <omicidx_db> OmicIDX release latest (published 2026-10-31)
#> 
#> geometadb
#>   geo_supplemental_files    7,130,018
#>   gpl                          29,166
#>   gse                         299,765
#>   gse_gpl                     345,234
#>   gse_gsm                  10,615,354
#>   gsm                       8,818,838
#> 
#> main
#>   bioprojects                      1,133,204
#>   biosamples                      60,645,792
#>   db_creation_metadata
#>   geo_platforms                       29,166
#>   geo_samples                      8,818,838
#>   geo_series                         299,765
#>   geo_series_with_rnaseq_counts       27,714
#>   sra_accessions                 163,477,940
#>   sra_experiments                 42,771,188
#>   sra_runs                        45,687,189
#>   sra_samples                     44,784,635
#>   sra_studies                        777,168
#> 
#> sradb
#>   experiment       42,771,188
#>   human_runs        8,568,803
#>   mouse_runs        4,426,634
#>   rnaseq_runs       9,098,952
#>   run              45,832,573
#>   run_with_study   45,832,573
#>   sample           44,784,635
#>   sra              45,832,573
#>   study               777,168
#>   wgs_runs          9,645,478
#> 
#> Tables: db$<schema>$<table>. Connection: attr(db, "con").
db$sradb$study |>
    dplyr::count(study_type, sort = TRUE) |>
    head() |>
    dplyr::collect()
#> # A tibble: 6 × 2
#>   study_type                   n
#>   <chr>                    <dbl>
#> 1 Other                   444868
#> 2 Transcriptome Analysis  130005
#> 3 Whole Genome Sequencing 128447
#> 4 Metagenomics             71449
#> 5 Population Genomics        836
#> 6 Epigenetics                707
DBI::dbDisconnect(attr(db, "con"))
```
