# Position Template ER Diagram

The following ER diagram mirrors the entities implied by the position template APIs. It focuses on the main tables and their relationships so the API and database layers stay in sync.

```mermaid
erDiagram
    POSITION ||--o{ POSITION_NAME : "has translations"
    POSITION ||--o{ POSITION_TAG : "is tagged by"
    POSITION ||--o{ POSITION_FILE : "has attachments"
    POSITION }o--o{ POSITION_RELATION : "parent/child"
    POSITION ||--|| STYLE : "styled by"
    POSITION ||--o{ POSITION_TITLE : "title detail"
    POSITION ||--o{ POSITION_OUTLINE : "outline detail"
    POSITION ||--o{ POSITION_SUMMARY : "summary detail"
    POSITION ||--o{ POSITION_ASSET : "asset detail"
    POSITION ||--o{ POSITION_FLOW : "flow detail"
    POSITION ||--o{ POSITION_QUALITATIVE : "qualitative detail"
    POSITION ||--o{ POSITION_INDICATOR : "indicator detail"
    POSITION_INDICATOR ||--o{ INDICATOR_FORMULA_TOKEN : "formula tokens"
    POSITION_QUALITATIVE ||--o{ QUALITATIVE_OPTION : "has options"
    POSITION_FILE ||--|| STORAGE_FILE : "references"
    POSITION_TAG }o--|| TAG : "tag metadata"

    POSITION {
        UUID id
        UUID tenant_id
        POSITION_TYPE type
        BOOLEAN is_active
        UUID style_id FK
        UUID parent_id FK
        INT sorting_level
        DATE start_term
        DATE end_term
        TEXT description
        TEXT code
        TEXT display_id
        BOOLEAN quantity_required
        UUID created_by
        TIMESTAMP created_at
        UUID updated_by
        TIMESTAMP updated_at
    }

    POSITION_NAME {
        UUID position_id FK
        VARCHAR locale
        TEXT name
        TEXT tagline
        TEXT summary
    }

    POSITION_TAG {
        UUID position_id FK
        UUID tag_id FK
    }

    TAG {
        UUID id PK
        UUID tenant_id
        TEXT code
        TEXT label
    }

    POSITION_FILE {
        UUID position_id FK
        UUID file_id FK
        VARCHAR display_name
        BOOLEAN soft_deleted
        TIMESTAMP created_at
    }

    STORAGE_FILE {
        UUID id PK
        TEXT path
        TEXT content_type
        BIGINT size_bytes
        UUID uploaded_by
        TIMESTAMP uploaded_at
    }

    POSITION_RELATION {
        UUID parent_id FK
        UUID child_id FK
        INT sort_order
    }

    POSITION_TITLE {
        UUID position_id FK
        JSONB question_texts
    }

    POSITION_OUTLINE {
        UUID position_id FK
        BOOLEAN use_in_report
    }

    POSITION_SUMMARY {
        UUID position_id FK
        BOOLEAN use_in_report
        UUID quantity_id
    }

    POSITION_ASSET {
        UUID position_id FK
        UUID quantity_id
        NUMERIC allowed_deviation_percent
        BOOLEAN require_comment
        BOOLEAN priority
        BOOLEAN overview_aggregation
        BOOLEAN previous_value
        BOOLEAN use_in_report
    }

    POSITION_FLOW {
        UUID position_id FK
        UUID quantity_id
        NUMERIC allowed_deviation_percent
        BOOLEAN require_comment
        BOOLEAN priority
        BOOLEAN overview_aggregation
        BOOLEAN previous_value
        BOOLEAN use_in_report
    }

    POSITION_QUALITATIVE {
        UUID position_id FK
        JSONB question_texts
        VARCHAR choice_of_answer
        INT min_selected
        INT max_selected
        BOOLEAN require_comment
    }

    QUALITATIVE_OPTION {
        UUID qualitative_id FK
        UUID option_id
        INT sort_order
        JSONB labels
        BOOLEAN is_active
    }

    POSITION_INDICATOR {
        UUID position_id FK
        UUID quantity_id
        VARCHAR aggregation_interval
        VARCHAR calculation
        UUID unit_id
    }

    INDICATOR_FORMULA_TOKEN {
        UUID indicator_id FK
        INT position
        VARCHAR token_type
        TEXT token_value
    }

    STYLE {
        UUID id PK
        UUID tenant_id
        TEXT code
        TEXT font_family
        INT font_size
        VARCHAR font_weight
    }

```
