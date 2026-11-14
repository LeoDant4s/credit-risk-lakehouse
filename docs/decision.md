## Bronze ingestion: Auto Loader vs COPY INTO

**Date:** 2026-11-14
**Status:** Accepted

### Context
Monthly SCR.data CSV files (semicolon-delimited, quoted fields, 24 string columns)
land in a Unity Catalog volume. The Bronze layer must ingest them incrementally,
keep the raw values untouched, and record ingestion metadata for lineage.

### Options considered
1. **COPY INTO** — SQL, idempotent, incremental. Sufficient for thousands of files.
   Easier to reload a specific subset of re-uploaded files.
2. **Auto Loader (cloudFiles)** — Structured Streaming source with file-state tracking
   in a checkpoint. Better schema inference/evolution and cheaper file discovery at scale.
3. **Batch overwrite with spark.read** — simplest, but reprocesses everything on each run
   and has no notion of "new files".

### Decision
Auto Loader with `trigger(availableNow=True)`, all columns as strings
(`inferColumnTypes=false`), `schemaEvolutionMode=rescue`, and the metadata columns
`_source_file`, `_source_file_name`, `_file_modified_at`, `_ingested_at`.

### Rationale
- Incremental by design: re-running the job does not duplicate rows (verified by
  running twice and comparing counts).
- `availableNow` gives batch-style cost: process new files, then stop.
- New or unexpected columns land in `_rescued_data` instead of breaking the pipeline.
- It is the standard Bronze ingestion pattern on Databricks.

### Consequences / trade-offs
- COPY INTO would also have been sufficient at this volume (tens of files); the choice
  favors the production-standard pattern over minimal complexity.
- Reprocessing a selected subset of files is harder: it requires a new checkpoint or a
  targeted COPY INTO. Checkpoint and schema locations must be managed (kept in the volume).
- Streaming semantics add concepts (checkpoint, trigger) a plain batch job would not need.

### Validation
Row count matches the local DuckDB count; `_rescued_data` is empty; second run
adds 0 rows; a new monthly file adds only its own rows.
