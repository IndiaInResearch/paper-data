# Paper Datasets

Extracted paper and author data across multiple domains of science which powers indiainresearch.org. 

To view a subset of the data, visit: [indiainresearch.org](https://www.indiainresearch.org/)

## Features

1. Data enriched using OpenAlex, arXiv, PDF and conference website parsing using LLMs.
2. 1:1 mapping between paper authors and associated institutions
3. Author rank (undergrad, postgrad, faculty, industry) available for certain venues.
4. Author affiliation history available.
5. Standard topics for every paper assigned from OpenAlex and LLMs.
6. Author and affiliation matching done using LLMs.

## Data

### Computer Science

#### Artificial Intelligence

| Subfield | Venue  | Data |
| ------------- | ------------- | ------------- |
| Machine Learning | NeurIPS  | [2024](https://github.com/IndiaInResearch/paper-data/blob/main/data/cs/neurips/2024.json)  |
|  | ICML | [2024](https://github.com/IndiaInResearch/paper-data/blob/main/data/cs/icml/2024.json)  |
|  | ICLR | [2024](https://github.com/IndiaInResearch/paper-data/blob/main/data/cs/iclr/2024.json)  |
| Computer Vision | CVPR  | [2024](https://github.com/IndiaInResearch/paper-data/blob/main/data/cs/cvpr/2024.json)  |
|  | WACV | [2024](https://github.com/IndiaInResearch/paper-data/blob/main/data/cs/wacv/2024.json)  |
|  | ECCV | [2024](https://github.com/IndiaInResearch/paper-data/blob/main/data/cs/eccv/2024.json)  |
| Natural Language Processing | EMNLP | [2024](https://github.com/IndiaInResearch/paper-data/blob/main/data/cs/emnlp/2024.json)  |

#### Systems and Networks

| Subfield | Venue  | Data |
| ------------- | ------------- | ------------- |
| Mobile Computing | MobiSys | [2024](https://github.com/IndiaInResearch/paper-data/blob/main/data/cs/mobisys/2024.json)  |
| Databases | VLDB | [2024](https://github.com/IndiaInResearch/paper-data/blob/main/data/cs/vldb/2024.json)  |
| Operating Systems | OSDI | [2024](https://github.com/IndiaInResearch/paper-data/blob/main/data/cs/osdi/2024.json)  |
| Computer Networks | NSDI | [2024](https://github.com/IndiaInResearch/paper-data/blob/main/data/cs/nsdi/2024.json)  |
|  | SIGCOMM | [2024](https://github.com/IndiaInResearch/paper-data/blob/main/data/cs/sigcomm/2024.json)  |

#### Theory

| Subfield | Venue  | Data |
| ------------- | ------------- | ------------- |
| Algorithms and Complexity | STOC  | [2024](https://github.com/IndiaInResearch/paper-data/blob/main/data/cs/stoc/2024.json)  |
|  | FOCS | [2024](https://github.com/IndiaInResearch/paper-data/blob/main/data/cs/focs/2024.json)  |

## Schema

Each paper has the following schema and can be parsed as the following pydantic models:

### Paper model
```python
class Paper(BaseModel):
    id: str                                                         # unique ID for the paper, unused for now
    openalex_id: str | None = None                                  # corresponding ID from OpenAlex database
    doi: str | None = None                                          # DOI if present
    conf_id: str | None = None                                      # ID used by the corresponding conference
    title: str | None = None                                        # Paper title
    authorships: list[AuthorLink] = []                              # list of paper, author relations
    primary_location: dict | None = None                            # https://docs.openalex.org/api-entities/works/work-object#primary_location
    open_access: dict | None = None                                 # https://docs.openalex.org/api-entities/works/work-object#the-openaccess-object
    best_oa_location: dict | None = None                            # https://docs.openalex.org/api-entities/works/work-object#best_oa_location
    citation_normalized_percentile: dict | None = None              # FWCI percentile
    fwci: float | None = None                                       # FWCI
    primary_topic: TopicLink | None = None                          # top ranked topic
    publication_venue: str                                          # unique code for publication venue, usually same as conference name
    publication_year: int                                           # publication or conference year
    related_works: list[str] = []                                   # OpenAlex IDs of related works
    topics: list[TopicLink] = []                                    # top ranked topics. upto 3
    keywords: list[dict] = []                                       # keywords from OpenAlex
    link: str | None = None                                         # Primary webpage for the paper. (prefer this)
    pdf_link: str | None = None                                     # Primary PDF for the paper if open access (prefer this)

    status: str | None = None                                       # Oral, Poster, Spotlight (from Paper Copilot)
    track: str | None = None                                        # Conference track
    github_link: str | None = None                                   
    project_link: str | None = None
    video_link: str | None = None
    openaccess_link: str | None = None
    poster_link: str | None = None
    openreview_link: str | None = None
    arxiv_link: str | None = None
    proceeding_link: str | None = None

    author_names_from_paper: list[str] = []                         # list of author names by scraping PDF opr website or from Paper Copilot. (avoid using)
    aff_names_from_paper: list[str] = []                            # list of author affiliations by scraping PDF opr website or from Paper Copilot. (avoid using)
    aff_domains_from_paper: list[str] = []                          # list of author domains by scraping PDF opr website or from Paper Copilot. (avoid using)
    author_rank_from_paper: list[str] = []                          # undergrad, postgrad, faculty, researcher, engineer etc. (avoid using)
    openreview_ids_from_paper: list[str] = []                       # OpenReview IDs (avoid using)

    keywords_from_paper: list[str] = []                             # keywords from conference or pdf scraping
    primary_area_from_paper: str | None = None                      # primary area from paper
    overall_rating_from_paper: list[int] = []
    percent_overall_rating_from_paper: list[float] = []
    novelty_from_paper: list[int] = []
    percent_novelty_from_paper: list[float] = []
```

### Author model
```python
class Institution(BaseModel):
    id: str                                                         # unique ID for the institution, unused for now
    openalex_id: str | None = None                                  # OpenAlex ID for the institution
    display_name: str                                               # https://docs.openalex.org/api-entities/institutions/institution-object
    display_name_acronyms: list[str] = []
    display_name_alternatives: list[str] = []
    ror: str | None = None
    homepage_url: str | None = None
    country_code: str | None = None
    type: InstitutionType                                           # institute type as a custom type
    latlon: tuple[float, float] | None = None                       # latitude and longitude

class AuthorInstitutionLink(BaseModel):
    rank: AuthorRank | None = None                                  # author rank as undergrad, postgrad, faculty, insdustry etc.
    institution: Institution | None = None                          # instituion affiliation of author
    years: list[int] = []                                           # known years associated with institute

class InstitutionLink(BaseModel):
    rank: AuthorRank | None = None                                  # author rank as undergrad, postgrad, faculty, insdustry etc.
    institution: Institution | None = None                          # institution of author used in association with this corresponding paper

class Author(BaseModel):
    id: str                                                         # unique ID for the author, unused for now
    openalex_id: str | None = None                                  # OpenAlex ID for the institution
    orcid: str | None = None                                        # ORCID (preferred)
    openreview_id: str | None = None                                # OpenReview (preferred)
    name: str
    email: str | None = None
    work: list[AuthorInstitutionLink] = []                          # work history, unused for now
    education: list[AuthorInstitutionLink] = []                     # education history, unused for now
    affiliations: list[AuthorInstitutionLink] = []                  # paper affiliation history of the author

class AuthorLink(BaseModel):
    position: AuthorPosition | None = None                          # first, middle or last author
    author: Author                                                  # author model
    institutions: list[InstitutionLink] = []                        # institutions of author used in association with this corresponding paper
    countries: list[str] = []                                       # countries these institutions belong to

class Topic(BaseModel):                                             # same as https://docs.openalex.org/api-entities/topics
    id: str
    openalex_id: str
    display_name: str
    subfield: dict
    field: dict
    domain: dict

class TopicLink(BaseModel):                                         # same as https://docs.openalex.org/api-entities/topics
    score: float
    topic: Topic
```

### Types

```python
class InstitutionType(str, Enum):
    EDUCATION = "education"
    HEALTHCARE = "healthcare"
    COMPANY = "company"
    ARCHIVE = "archive"
    NONPROFIT = "nonprofit"
    GOVERNMENT = "government"
    FACILITY = "facility"
    FUNDER = "funder"
    OTHER = "other"

class AuthorRank(str, Enum):
    UNDERGRAD = "undergrad"
    POSTGRAD = "postgrad"
    POSTDOC = "postdoc"
    FACULTY = "faculty"
    INDUSTRY = "industry"

class AuthorPosition(str, Enum):
    FIRST = "first"
    MIDDLE = "middle"
    LAST = "last"
```
## Sources

[OpenAlex](https://openalex.org/), [arXiv](https://arxiv.org/), [dblp](https://dblp.org/), [Paper Copilot](https://github.com/papercopilot), [ACL Anthology](https://aclanthology.org/).

Note: Some data has been analyzed by AI and may be incorrect.

## Need assistance?

Custom requests for scraping and licensing can be sent to contact[AT]indiainresearch.org.

## License and Attribution

If you find this useful, please consider starring and sharing the repository.

Shield: [![CC BY-NC-SA 4.0][cc-by-nc-sa-shield]][cc-by-nc-sa]

This work is licensed under a
[Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License][cc-by-nc-sa].

[![CC BY-NC-SA 4.0][cc-by-nc-sa-image]][cc-by-nc-sa]

[cc-by-nc-sa]: http://creativecommons.org/licenses/by-nc-sa/4.0/
[cc-by-nc-sa-image]: https://licensebuttons.net/l/by-nc-sa/4.0/88x31.png
[cc-by-nc-sa-shield]: https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-lightgrey.svg
