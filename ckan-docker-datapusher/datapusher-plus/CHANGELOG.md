# Changelog
All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [0.1.0] - 2022-09-09

### Added
* available smarter data type mapping to Postgres data types. By looking at the min/max values of a column,
  we can infer the best postgres data type - integer, bigint or numeric, instead of using the numeric Postgres type
  for all integers.
  This is done by changing TYPE_MAPPING of `Integer` from `numeric` to `smartint`.
* Add resource preview metadata fields:
    * `preview` - if the resource is a preview, and not the entire file, containing only the first PREVIEW_ROWS of the file (boolean)
    * `preview_rows` - the number of rows of the preview
    * `total_record_count` - the actual number of rows of the file

### Changed
* change mapping of inferred Date fields to the Postgres `date` data type, instead of using Postgres `timestamp` data type for
  both Date (YYYY-MM-DD) and Datetime (YYYY-MM-DD HH:MM:SS TZ) columns.
* warn when duplicates are found, instead of info
* decreased default preview to 1,000 rows
* better error handling when calling qsv binary
* update instructions to use the latest qsv binary - qsv 0.67.0

### Fixed
* trimmed header and column values when processing spreadsheets. As spreadsheets are more often than not, manually curated,
  there are often invisible whitespaces that "look" right that may cause invalid CSVs - e.g. column names with leading/trailing whitespaces
  that cause Postgres errors when columns are created using the Excel column name.

## [0.0.23] - 2022-05-09
 ### Changed
* use `psycopg2-binary` instead of `psycopg2` to ease installation and eliminate need to have postgres dev files
* made logging messages auto-dedup aware if dupes are detected, by adding "unique" qualifier to record count
* pointed to the latest qsv version (0.46.1) with the excel off by 1 fix
* added note about nightly builds of qsv for maximum performance
* added note about additional DP+ supported Excel and TSV subformats
* use JOB_CONFIG consistently for setting DP+ settings
* made qsvdp the default QSV_BIN
* added note about how to install python 3.7 and above in DP+ virtual environment

### Removed
* removed Hitchiker's guide quote from setup.py epilog
* removed `six` as DP+ requires at least python 3.7
* removed `pytest` step in Development installation until the tests are adapted to DP+

### Fixed
* fixed development installation procedure, so no assumptions are made
* fixed production deployment procedure and made it more detailed
* fixed off by 1 error in `excel` export message in qsv

## [0.0.21] - 2022-05-04
### Added
* additional analysis & preparation steps enabled by qsv
  * blazing fast, guaranteed data type inferences with comprehensive descriptive statistics
  * configurable Excel/ODS exports (supports XLS, XLSX, ODS, XLSM, and XLSB formats)
  * automatic deduplication (https://github.com/dathere/datapusher-plus/pull/25)
  * [RFC 4180](https://datatracker.ietf.org/doc/html/rfc4180) validation and UTF-8 transcoding of CSV files
  * smart date inferencing (https://github.com/dathere/datapusher-plus/pull/28)
  * automatic preview subset creation
  * DP+ optimized qsv binary (qsvdp) - which is 6x smaller than regular qsv, 3x smaller than qsvlite, and with 
    the self-update engine removed.
* added an init_db command for initializing jobs_store db by [@categulario](https://github.com/categulario) (https://github.com/dathere/datapusher-plus/pull/3)
* automatic resource alias (aka Postgres view) creation (https://github.com/dathere/datapusher-plus/pull/26)
* added JOB_CONFIG environment variable by [@categulario](https://github.com/categulario) (https://github.com/dathere/datapusher-plus/pull/6)
* added package install by [@categulario](https://github.com/categulario) (https://github.com/dathere/datapusher-plus/pull/16)
* Postgres COPY to datastore
* additional data types inferred and changed postgres type mapping
  (String -> text; Float -> numeric; Integer -> numeric; Date -> timestamp;
  DateTime -> timestamp; NULL -> text)
* added requirement to create a Postgres application role/user with SUPERUSER privs
  to do native Postgres operations through psycopg2
* added more verbose logging with elapsed time per phase (Analysis & Preparation Phase,
  Copy Phase, Total elapsed time)
* added Containerfile reference by [@categulario](https://github.com/categulario) (https://github.com/dathere/datapusher-plus/pull/22)
* added `datasize` dependency for human-readable file sizes in messages
* added a Changelog using the Keep a Changelog template.
* published `datapusher-plus` on pypi
* added a GitHub action to publish a pypi package on release
* added a Roadmap Tracking EPIC, that will always be updated as DP+ evolves https://github.com/dathere/datapusher-plus/issue/5

### Changed
* DP+ requires at least Python 3.7, as it needs `CAPTURE_OUTPUT` option in `subprocess.run`.
  It should still continue to work with CKAN<=2.8 as it runs in its own virtualenv.
* replaced messytables with qsv
* removed old "chunked" datastore inserts
* removed requirements.txt, requirements/dependencies are now in setup.py
* expanded documentation - rationale for DP+; addl config vars; modified installation procedure; scnshots; etc.
* improvements to install instructions by [@categulario](https://github.com/categulario) (https://github.com/dathere/datapusher-plus/pull/20)
* made DP+ a detached Datapusher fork to maintain own issue tracker, discussions, releases, etc.
