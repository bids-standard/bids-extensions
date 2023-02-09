# Parts of the Schema Explained

The structure of the BIDS schema as seen from a top-level file view:

```
schema
├── BIDS_VERSION
├── SCHEMA_VERSION
├── meta
│   ├── context.yaml
│   └── expression_tests.yaml
├── objects
│   ├── columns.yaml
│   ├── ...
│   └── suffixes.yaml
└── rules
    ├── checks
    ├── ...
    └── tabular_data
```

The BIDS Schema can be be divided into three major components:

1) [Objects](https://github.com/bids-standard/bids-specification/blob/master/src/schema/objects) (`objects.*`)
    - Definitions of BIDS concepts
    - Source of definitions rendered in the specification e.g. in the [glossary](https://bids-specification.readthedocs.io/en/stable/glossary.html)
2) [Rules](https://github.com/bids-standard/bids-specification/tree/master/src/schema/rules) (`rules.*`)
    - Validation rules for entity ordering, sidecar values, and both common and modality specific rules
    - Source of filename tempaltes and tables describing sidecar fields and TSV Columns
3) [Meta](https://github.com/bids-standard/bids-specification/tree/master/src/schema/meta) (`meta.*`)
    - Defines a "context" object to which rules can be applied
    - Expandable to definitions, tests, or rules related to the schema itself

>_Note: object dot notation e.g. `rules.checks.*` is used when referencing parts of the schema throughout this document. Bracket notation such as `objects['columns'][*]` is equally valid, but more difficult to read._
>
>_The character: `*`, represents a [glob](https://man7.org/linux/man-pages/man7/glob.7.html) and is used to denote matching any string/object_.


## Objects

The Objects portion of the schema contains the following schema files:

```
src/schema/objects
├── columns.yaml
├── common_principles.yaml
├── datatypes.yaml
├── entities.yaml
├── extensions.yaml
├── files.yaml
├── formats.yaml
├── metadata.yaml
├── modalities.yaml
└── suffixes.yaml
```

### BIDS terms

- [`objects.common_principles`](https://github.com/bids-standard/bids-specification/blob/master/src/schema/objects/common_principles.yaml): Terms, such as "data acquisition" or "entity", used throughout BIDS

### Name/value terms

- [`objects.entities`](https://github.com/bids-standard/bids-specification/blob/master/src/schema/objects/entities.yaml): Entities (sub-01)
- [`objects.metadata`](https://github.com/bids-standard/bids-specification/blob/master/src/schema/objects/metadata.yaml): Sidecar fields ("PhaseEncodingDirection": "j")
- [`objects.columns`](https://github.com/bids-standard/bids-specification/blob/master/src/schema/objects/columns.yaml): TSV columns ("participant_label", ["sub-01", "sub-02", …])

### Value terms

- [`objects.datatypes`](https://github.com/bids-standard/bids-specification/blob/master/src/schema/objects/datatypes.yaml) (func)
- [`objects.suffixes`](https://github.com/bids-standard/bids-specification/blob/master/src/schema/objects/suffixes.yaml) (bold)
- [`objects.extensions`](https://github.com/bids-standard/bids-specification/blob/master/src/schema/objects/extensions.yaml) (.nii.gz)

### Formats

- [`objects.formats`](https://github.com/bids-standard/bids-specification/blob/master/src/schema/objects/formats.yaml): Types of values objects referenced in the schema can take (label, index, boolean)

### Files

- [`objects.files`](https://github.com/bids-standard/bids-specification/blob/master/src/schema/objects/files.yaml): One-off files, not constructed from path components, like README

## Rules

### rules.files

- [`rules.files`](https://github.com/bids-standard/bids-specification/tree/master/src/schema/rules/files): Filename construction rules
- [`rules.files.common`](https://github.com/bids-standard/bids-specification/tree/master/src/schema/rules/files/common): Top-level (dataset_description.json) and tables (participants.tsv)
- [`rules.files.raw`](https://github.com/bids-standard/bids-specification/tree/master/src/schema/rules/files/raw): Data files defined in "BIDS-Raw"
- [`rules.files.deriv`](https://github.com/bids-standard/bids-specification/tree/master/src/schema/rules/files/deriv): Data files defined in "BIDS-Derivatives"

e.g. this entry for [`rules.files.raw.beh.noncontinuous`](https://github.com/bids-standard/bids-specification/tree/master/src/schema/rules/files/raw/beh.yaml):

```yaml
# Non-continuous data
noncontinuous:
  suffixes:
    - beh
  extensions:
    - .tsv
    - .json
  datatypes:
    - beh
  entities:
    subject: required
    session: optional
    task: required
    acquisition: optional
    run: optional
```

![rendered image of template see slide 7 in schema sprint intro]()

---

### rules.sidecars

- [`rules.sidecars`](https://github.com/bids-standard/bids-specification/tree/master/src/schema/rules/sidecars): Sidecar construction rules

e.g. this entry for [`rules.sidecars.anat.MRIAnatomyCommonMetadataFields`](https://github.com/bids-standard/bids-specification/tree/master/src/schema/rules/sidecars/anat.yaml)

```yaml
MRIAnatomyCommonMetadataFields:
  selectors:
    - modality == "mri"
    - datatype == "anat"
  fields:
    ContrastBolusIngredient: optional
    RepetitionTimeExcitation: optional
    RepetitionTimePreparation: optional
```

![rendered image of rules.sidecars.anat.MRIAnatomyCommonMetadataFields]()

---

### rules.tabular_data

- [`rules.tabular_data`](): TSV construction rules

```yaml
TaskEvents:
  selectors:
    - '"task" in entities'
    - suffix == "events"
  columns:
    onset: required
    duration: required
    trial_type: optional
    ...
  additional_columns: allowed
  initial_columns:
    - 'onset'
    - 'duration'
```

![rendered image of task events]()

---

### rules.checks

- [`rules.checks`](https://github.com/bids-standard/bids-specification/tree/master/src/schema/rules/checks): Dataset consistency rules, relies heavily on an expression language
If all selectors are true, the rule applies to a file; if any check fails, the issue is raised.

```yaml
DWIVolumeCount:
  issue:
    code: VOLUME_COUNT_MISMATCH
    message: |
      The number of volumes in this scan does not match the number
      of volumes in the corresponding .bvec and .bval files.
    level: error
  selectors:
    - suffix == "dwi"
    - '"bval" in associations'
    - '"bvec" in associations'
  checks:
    - associations.bval.n_cols == nifti_header.dim[4]
    - associations.bvec.n_cols == nifti_header.dim[4]
```

---

### One-off rules

- [`rules.entities`](https://github.com/bids-standard/bids-specification/blob/master/src/schema/rules/entities.yaml): Global order of entities
- [`rules.dataset_metadata`]https://github.com/bids-standard/bids-specification/blob/master/src/schema/rules/dataset_metadata.yaml): Contents of dataset_description.json and genetic_info.json, constructed similarly to [rules.sidecars](#rulessidecars)
- [`rules.modalities`](https://github.com/bids-standard/bids-specification/blob/master/src/schema/rules/modalities.yaml): Grouping of data types by "modality" (somewhat ill-defined)
- [`rules.common_principles`](https://github.com/bids-standard/bids-specification/blob/master/src/schema/rules/common_principles.yaml): Order of BIDS terms listed in "Common principles" section
- [`rules.errors`](https://github.com/bids-standard/bids-specification/blob/master/src/schema/rules/errors.yaml): Error messages found in the validator that cannot be schematized (e.g., NIFTI_HEADER_UNREADABLE), intended to promote consistency across tooling

## Context

[`meta.context`](https://github.com/bids-standard/bids-specification/blob/master/src/schema/meta/context.yaml) describes a context object that makes information available to rules

```yaml
context:
  schema:		The BIDS specification schema
  dataset:		Properties and contents of the entire dataset
  subject:		Properties and contents of the current subject

  path:		Path of the current file
  entities:		Entities parsed from the current filename
  datatype:		Datatype of current file, for example, anat
  suffix:		Suffix of current file
  extension:	Extension of current file
  modality:		Modality of current file, for example, MRI

  sidecar:		Sidecar metadata constructed via the inheritance principle
  …
  associations:	Associated files, indexed by suffix, selected according to the inheritance principle
    events:
    aslcontext:
    bval:
    bvec:
    …
  columns:		TSV columns, indexed by column header, values are arrays with column contents
  json:		Contents of the current JSON file
  gzip:		Parsed contents of gzip header
  nifti_header:	Parsed contents of NIfTI header
```
