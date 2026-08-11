# invenio-subjects-mesh-lite

*MeSH topical subject terms without qualifiers for InvenioRDM*

<a href="https://pypi.org/project/invenio-subjects-mesh-lite/">
  <img src="https://img.shields.io/pypi/v/invenio-subjects-mesh-lite.svg">
</a>

Install this extension to get Medical Subject Headings topics without qualifiers into your instance.

If you are looking for a larger MeSH vocabulary with qualifiers, use
[invenio-subjects-mesh](https://github.com/galterlibrary/invenio-subjects-mesh) instead.

> [!NOTE]
> Both extensions map the `MeSH` subject id, so you can only install one of them. However, they
> use the same pattern for subject ids, so you can switch from one to the other easily if your
> needs change in the future.

## Installation

From your instance directory:

```bash
uv add invenio-subjects-mesh-lite
```

## Versions

**From 2025.1.15.2 onwards**

This package follows the following format for versions: `YYYY.mm.dd.patch` where

- `YYYY.mm.dd` is the date of retrieval of the MeSH terms with `mm` and `dd` NOT being 0-prefixed.
- `patch` is the patch number (0-indexed) so that multiple releases can be done on the same day (bug/security fixes) and non-subject related releases can be done as well.

This follows [calendar versioning](https://calver.org/) (for year, month, and day) and adds a patch number at the end. The package is typically updated on a quarterly basis. The following are illustrative (fictitious) examples of how to understand the versioning of this distribution package:

| Last MeSH update included | patch number | version of this project |
| ------------------------- | ------------ | ----------------------- |
| 2025-06-31                | 2            | 2025.6.31.2             |
| 2025-12-01                | 0            | 2025.12.1.0             |

**Prior to 2025.1.15.2**

This repository follows [calendar versioning](https://calver.org/) for year and month. It does a "best effort" attempt at tracking the MeSH updates in an *up-to-and-including* version date manner. The following are illustrative cases of how to understand the versioning of this distribution package:

| Last MeSH update included | version of this project | date of release of this project |
| ------------------------- | ----------------------- | ------------------------------- |
| 2024-01-31                | 2024.1.X                | any time after 2024-01-31       |
| 2023-12-31                | 2023.12.X               | any time after 2023-12-31       |


`2021.06.18` is both a valid semantic version and an indicator of the year-month corresponding to the loaded terms.
`18` here is a patch number (not a day).

## Usage

There are 2 types of users for this package. Instance operators and package maintainers.

### Instance operators

For instance operators, after you have installed the extension as per the steps above, you will want to reload your instance's fixtures: `pipenv run invenio rdm-records fixtures`. This will install the new terms in your instance.

Alternatively, or if you want to update your already loaded subjects to a new listing (e.g. from one year's list to another), you can update your instance's MeSH subjects as per below. Updating subjects this way takes care of everything for you: the subjects themselves and the records/drafts using those subjects. **WARNING** This operation can _remove_ subjects.

```bash
# In your instance's project
# Download up-to-date listings
uv run invenio galter_subjects mesh download -d /path/to/downloads/storage/ -y YEAR
# Generate file containg deltas to transition your instance to the downloaded listing
uv run invenio galter_subjects mesh deltas -d /path/to/downloads/storage/ -y YEAR -f topic -o deltas_mesh.csv
# Update your instance - *this operation will modify your instance*
uv run invenio galter_subjects update deltas_mesh.csv
```

Look at the help text for these commands to see additional options that can be passed.
In particular, options for `galter_subjects update` allow you to store renamed, replaced or removed subjects on records according to a template of your choice.


### Maintainers

When a new list of MeSH term comes out, this package should be updated. Here we show how.

**Pre-requisite/Context**

[Install the distribution package for development](#development) before you do anything.

**Commands**

Once you have it installed, you can run the following commands in the isolated virtualenv:

```bash
# In this project
# Download up-to-date listings
uv run invenio galter_subjects mesh download -d /path/to/downloads/storage/ -y YEAR
# Generate file containing initial listing
uv run invenio galter_subjects mesh file -d /path/to/downloads/storage/ -y YEAR -f topic -o invenio_subjects_mesh_lite/vocabularies/subjects_mesh.csv
```

Sorting the resulting csv is a nice touch for humans to better parse the changes between versions.

When you are happy with the list, bump the version in `pyproject.toml` and release it.


## Development

Install the project in editable mode with `dev` dependencies in an isolated virtualenv:

```bash
uv pip install -e .[dev]
```

Run tests:

```bash
uv run inv test
```

Test compatibility with the pre-release version of InvenioRDM (invenio-app-rdm):

```bash
uv run --extra dev_pre --prerelease=allow inv test
```

Clean out artefacts:

```bash
uv run inv clean
```
