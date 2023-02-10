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

Examples of Objects, Rules, and Meta are detailed below. 

Examples are hyperlinked to parts of the schema (source code) where applicable, simply click on the blue 
links to reach those parts of the schema. 

Specific examples from the schema can be expanded or hidden by clicking on blue _boxes_ to show or hide them.


## Objects

The Objects portion of the schema contains the following collection of schema files:

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

Objects contain selectors, issues and error messages to raise during validation, descriptors, messages, and units.
  Every property needed describe a an entity in BIDS contained within an object's contents. We'll take a closer 
  look below at a few specific objects to better introduce the structure that they represent and take on. 

### BIDS terms

[`objects.common_principles`](https://github.com/bids-standard/bids-specification/blob/master/src/schema/objects/common_principles.yaml): Terms, such as "data acquisition" or "datatype", used throughout BIDS

??? "data acquisition"

    ```yaml
    data_acquisition:
    display_name: Data acquisition
    description: |
        A continuous uninterrupted block of time during which a brain scanning instrument was acquiring data according to
        particular scanning sequence/protocol.
    ```

??? "data type"

    ```yaml
    data_type:
    display_name: Data type
    description: |
        A functional group of different types of data.
        Data files are contained in a directory named for the data type.
        In raw datasets, the data type directory is nested inside subject and (optionally) session directories.
        BIDS defines the following data types:
            1.  `func` (task based and resting state functional MRI)
            2.  `dwi` (diffusion weighted imaging)
            3.  `fmap` (field inhomogeneity mapping data such as field maps)
            4.  `anat` (structural imaging such as T1, T2, PD, and so on)
            5.  `perf` (perfusion)
            6.  `meg` (magnetoencephalography)
            7.  `eeg` (electroencephalography)
            8.  `ieeg` (intracranial electroencephalography)
            9.  `beh` (behavioral)
            10. `pet` (positron emission tomography)
            11. `micr` (microscopy)
            12. `nirs` (near infrared spectroscopy)
    ```

### Name/value terms

[`objects.entities`](https://github.com/bids-standard/bids-specification/blob/master/src/schema/objects/entities.yaml): Entities (sub-01)

???+ subject

    ```yaml
    subject:
    name: sub
    display_name: Subject
    description: |
        A person or animal participating in the study.
    type: string
    format: label
    ```

[`objects.metadata`](https://github.com/bids-standard/bids-specification/blob/master/src/schema/objects/metadata.yaml): Sidecar fields ("PhaseEncodingDirection": "j")

??? "PhaseEncodingDirection"

    ```yaml
    PhaseEncodingDirection:
    name: PhaseEncodingDirection
    display_name: Phase Encoding Direction
    description: |
        The letters `i`, `j`, `k` correspond to the first, second and third axis of
        the data in the NIFTI file.
        The polarity of the phase encoding is assumed to go from zero index to
        maximum index unless `-` sign is present
        (then the order is reversed - starting from the highest index instead of
        zero).
        `PhaseEncodingDirection` is defined as the direction along which phase is was
        modulated which may result in visible distortions.
        Note that this is not the same as the DICOM term
        `InPlanePhaseEncodingDirection` which can have `ROW` or `COL` values.
    type: string
    enum:
        - i
        - j
        - k
        - i-
        - j-
        - k-
    ```

[`objects.columns`](https://github.com/bids-standard/bids-specification/blob/master/src/schema/objects/columns.yaml): TSV columns ("participant_id", ["sub-01", "sub-02", …])

??? "partcipant_id"

    ```yaml
    participant_id:
    name: participant_id
    display_name: Participant ID
    description: |
        A participant identifier of the form `sub-<label>`,
        matching a participant entity found in the dataset.
    type: string
    pattern: ^sub-[0-9a-zA-Z]+$
    ```

### Value terms

[`objects.datatypes`](https://github.com/bids-standard/bids-specification/blob/master/src/schema/objects/datatypes.yaml) (func)

???+ "func"

    ```yaml
    func:
    value: func
    display_name: Task-Based Magnetic Resonance Imaging
    description: |
        Task (including resting state) imaging data
    ```

[`objects.suffixes`](https://github.com/bids-standard/bids-specification/blob/master/src/schema/objects/suffixes.yaml) (bold)

??? "object.sufixes.bold"

    ```yaml
    bold:
    value: bold
    display_name: Blood-Oxygen-Level Dependent image
    description: |
        Blood-Oxygen-Level Dependent contrast (specialized T2\* weighting)
    ```

[`objects.extensions`](https://github.com/bids-standard/bids-specification/blob/master/src/schema/objects/extensions.yaml) (.nii.gz)

### Formats

[`objects.formats`](https://github.com/bids-standard/bids-specification/blob/master/src/schema/objects/formats.yaml): Types of values objects referenced in the schema can take (label, index, boolean)

???+ "boolean"

    ```yaml
    boolean:
    display_name: Boolean
    description: |
        A boolean.
        Must be either "true" or "false".
    pattern: '(true|false)'
    ```

### Files

[`objects.files`](https://github.com/bids-standard/bids-specification/blob/master/src/schema/objects/files.yaml): One-off files, not constructed from path components, like README

??? "README"

    ```yaml
    README:
    display_name: README
    file_type: regular
    description: |
        A REQUIRED text file, `README`, SHOULD describe the dataset in more detail.
        The `README` file MUST be either in ASCII or UTF-8 encoding and MAY have one of the extensions:
        `.md` ([Markdown](https://www.markdownguide.org/)),
        `.rst` ([reStructuredText](https://docutils.sourceforge.io/rst.html)),
        or `.txt`.
        A BIDS dataset MUST NOT contain more than one `README` file (with or without extension)
        at its root directory.
        BIDS does not make any recommendations with regards to the
        [Markdown flavor](https://www.markdownguide.org/extended-syntax/#lightweight-markup-languages)
        and does not validate the syntax of Markdown and reStructuredText.
        The `README` file SHOULD be structured such that its contents can be easily understood
        even if the used format is not rendered.
        A guideline for creating a good `README` file can be found in the
        [bids-starter-kit](https://github.com/bids-standard/bids-starter-kit/blob/master/templates/README).
    ```

## Rules

### rules.files

[`rules.files`](https://github.com/bids-standard/bids-specification/tree/master/src/schema/rules/files): Filename construction rules

[`rules.files.common`](https://github.com/bids-standard/bids-specification/tree/master/src/schema/rules/files/common): Top-level (dataset_description.json) and tables (participants.tsv)

???+ "dataset_description"

    ```yaml
    dataset_description:
    level: required
    path: dataset_description.json
    ```

??? "partcipants"

    ```yaml
    participants:
    level: optional
    stem: participants
    extensions:
        - .tsv
        - .json
    ```


[`rules.files.raw`](https://github.com/bids-standard/bids-specification/tree/master/src/schema/rules/files/raw): Data files defined in "BIDS-Raw"

??? "rules.files.raw.anat.nonparametric"

    ```yaml
    nonparametric:
    suffixes:
        - T1w
        - T2w
        - PDw
        - T2starw
        - FLAIR
        - inplaneT1
        - inplaneT2
        - PDT2
        - angio
        - T2star # deprecated
        - FLASH # deprecated
        - PD # deprecated
    extensions:
        - .nii.gz
        - .nii
        - .json
    datatypes:
        - anat
    entities:
        subject: required
        session: optional
        task: optional
        acquisition: optional
        ceagent: optional
        reconstruction: optional
        run: optional
        part: optional
    ```

[`rules.files.deriv`](https://github.com/bids-standard/bids-specification/tree/master/src/schema/rules/files/deriv): Data files defined in "BIDS-Derivatives"

[`rules.files.raw.beh.noncontinuous`](https://github.com/bids-standard/bids-specification/tree/master/src/schema/rules/files/raw/beh.yaml):

??? "beh_noncontinous"

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

    ![image of template rendered in spec](assets/img/noncontinuous_beh_rendered_template.png)

---

### rules.sidecars

- [`rules.sidecars`](https://github.com/bids-standard/bids-specification/tree/master/src/schema/rules/sidecars): Sidecar construction rules

e.g. this entry for [`rules.sidecars.anat.MRIAnatomyCommonMetadataFields`](https://github.com/bids-standard/bids-specification/tree/master/src/schema/rules/sidecars/anat.yaml)

??? "MRIAnatomyCommonMetadataFields"

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

    ![rendered image of rules.sidecars.anat.MRIAnatomyCommonMetadataFields](assets/img/rules_sidecars_anat_MRIAnatomyCommonMetadataFields.png)

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

![rendered image of task events](assets/img/tasks_events_tablular_data_rendered.png)

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
- [`rules.dataset_metadata`](https://github.com/bids-standard/bids-specification/blob/master/src/schema/rules/dataset_metadata.yaml): Contents of dataset_description.json and genetic_info.json, constructed similarly to [rules.sidecars](#rulessidecars)
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
