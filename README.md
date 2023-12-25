# OpenAlex Works Crawler
*Written as part of RA work for Manuela Collis. Script last updated February 2023.*

## Purpose
Retrieves the full bibliographies of academics from the [OpenAlex](https://openalex.org/) database when provided with a list of OpenAlex identifiers. For each author, the OpenAlex database provides information on each publication's DOI, citation count, co-authors, and more. Identifiers were gathered from author names using a similar script that queries OpenAlex's authors database at a previous stage of the project (not released for confidentiality reasons).

## Methods
OpenAlex imposes a rate limit of 10 API requests per second. Above this limit, the server returns a 429 error. This script uses a [token-bucket](https://en.wikipedia.org/wiki/Token_bucket) approach to stay within the rate limit while maximizing the number of calls per second. The script submits 10 requests in parallel, loops over pages of results as needed, . The actual rate may sometimes be significantly lower than 10 requests per second due to computer processing speed limitations. This usually occurs when pages are result-dense; for example, the script will not be able to make more API calls if it is already processing 10 responses that each have 200 works with dozens of co-authors. 

The API will sometimes responds with a 503 error when a request is too large. This would happen if you were to filter for all of [Joseph Stiglitz's](https://api.openalex.org/works?filter=authorships.author.id%3AA5074218217) works (1944 in total), for example. This script deals with this by saving the problematic queries for the end. It then submits those queries with a lower results-per-page parameter, which resolves the 503 error.

## Replication
The code is written for Python 3.11. The external packages required for the script are pandas (for dataframe loading and returning results), requests (to submit requests to the OpenAlex API), and Jupyter (the script is written in notebook format). A full list of conda-readable requirements can be found in the `openalex_env.yml` file.

A sample input file `sample_names.csv` is available for testing. Each row corresponds to an academic, and includes their name (`diss_name`), a unique external identifier assigned by the researcher (`diss_uid`), and a link to the academic's works portal on OpenAlex (`works_api_url`). 
