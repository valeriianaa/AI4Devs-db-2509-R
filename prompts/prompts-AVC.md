# Prompts utilizados para este ejercicio

**Este ejercicio fue resuelto con Visual Studio Code con GitHub Copilot y Claude Sonnet 4**

## Prompt 1
@workspace Act as a senior backend engineer specialized in Prisma and PostgreSQL.
Analyze all the files where models, entities, or database configurations are defined (backend/, backend/prisma/schema.prisma, backend/prisma/migrations/) and explain the following:

What tables exist.
How they are related to each other.
Are there any inconsistencies or do you have any suggestions about how to optimize the database following good practices?

## Prompt 2
Ok this is great answer, but I want to update the database in another way. I have to add new entities to the database. To do so I have an ERD in mermaid with the new entities and their relations. Take into account that this ERD is not normalized or indexed and I want you @workspace to update the Prisma schema in this way:
1. Analyze the mermaid script I’ll provide.
2. How the new entities will relate to the existing ones?
3. Given there is no normalization just yet you can suggest new entities if that will make the new database schema more optimized.
4. Create indexes where necessary.
5. Look for inconsistencies and correct them.
6. Transform all of this steps and suggestions into a new prisma schema.

Do not create any code just yet, just show me step by step how you would change the existing database with the mermaid script I’ll provide and how the new schema would look.
But I would like you to make a new mermaid script with the new database proposal so I can analyze better the new entities and their relations.
Here’s the mermaid script with the new entities to include in the database:

```mermaid
erDiagram
     COMPANY {
         int id PK
         string name
     }
     EMPLOYEE {
         int id PK
         int company_id FK
         string name
         string email
         string role
         boolean is_active
     }
     POSITION {
         int id PK
         int company_id FK
         int interview_flow_id FK
         string title
         text description
         string status
         boolean is_visible
         string location
         text job_description
         text requirements
         text responsibilities
         numeric salary_min
         numeric salary_max
         string employment_type
         text benefits
         text company_description
         date application_deadline
         string contact_info
     }
     INTERVIEW_FLOW {
         int id PK
         string description
     }
     INTERVIEW_STEP {
         int id PK
         int interview_flow_id FK
         int interview_type_id FK
         string name
         int order_index
     }
     INTERVIEW_TYPE {
         int id PK
         string name
         text description
     }
     CANDIDATE {
         int id PK
         string firstName
         string lastName
         string email
         string phone
         string address
     }
     APPLICATION {
         int id PK
         int position_id FK
         int candidate_id FK
         date application_date
         string status
         text notes
     }
     INTERVIEW {
         int id PK
         int application_id FK
         int interview_step_id FK
         int employee_id FK
         date interview_date
         string result
         int score
         text notes
     }

     COMPANY ||--o{ EMPLOYEE : employs
     COMPANY ||--o{ POSITION : offers
     POSITION ||--|| INTERVIEW_FLOW : assigns
     INTERVIEW_FLOW ||--o{ INTERVIEW_STEP : contains
     INTERVIEW_STEP ||--|| INTERVIEW_TYPE : uses
     POSITION ||--o{ APPLICATION : receives
     CANDIDATE ||--o{ APPLICATION : submits
     APPLICATION ||--o{ INTERVIEW : has
     INTERVIEW ||--|| INTERVIEW_STEP : consists_of
     EMPLOYEE ||--o{ INTERVIEW : conducts
```

## Prompt 3

This new schema proposal is excellent. What are the next steps to implement the new updated database?

**NOTA: Adjunto aqui el script en mermaid que le pedí al LLM que creara. Un nuevo ERD de la nueva base de datos.**
```mermaid
erDiagram
    COMPANY {
        int id PK
        string name
        string description
        string website
        string email
        string phone
        string address
        boolean isActive
        datetime createdAt
        datetime updatedAt
    }
    
    EMPLOYEE {
        int id PK
        int companyId FK
        int roleId FK
        string firstName
        string lastName
        string email UK
        string phone
        boolean isActive
        datetime createdAt
        datetime updatedAt
    }
    
    EMPLOYEE_ROLE {
        int id PK
        string name UK
        string description
        string permissions
    }
    
    LOCATION {
        int id PK
        string city
        string state
        string country
        string timezone
    }
    
    EMPLOYMENT_TYPE {
        int id PK
        string name UK
        string description
    }
    
    POSITION {
        int id PK
        int companyId FK
        int interviewFlowId FK
        int locationId FK
        int employmentTypeId FK
        string title
        text description
        string status
        boolean isVisible
        text jobDescription
        text requirements
        text responsibilities
        decimal salaryMin
        decimal salaryMax
        text benefits
        date applicationDeadline
        string contactInfo
        datetime createdAt
        datetime updatedAt
    }
    
    INTERVIEW_FLOW {
        int id PK
        int companyId FK
        string name
        text description
        boolean isActive
        datetime createdAt
        datetime updatedAt
    }
    
    INTERVIEW_TYPE {
        int id PK
        string name UK
        text description
        int estimatedDuration
        boolean isActive
    }
    
    INTERVIEW_STEP {
        int id PK
        int interviewFlowId FK
        int interviewTypeId FK
        string name
        text description
        int orderIndex
        boolean isRequired
        int estimatedDuration
    }
    
    CANDIDATE {
        int id PK
        string firstName
        string lastName
        string email UK
        string phone
        string address
        datetime createdAt
        datetime updatedAt
        datetime deletedAt
    }
    
    EDUCATION {
        int id PK
        int candidateId FK
        string institution
        string title
        string fieldOfStudy
        date startDate
        date endDate
        string grade
        text description
    }
    
    WORK_EXPERIENCE {
        int id PK
        int candidateId FK
        string company
        string position
        text description
        date startDate
        date endDate
        boolean isCurrent
    }
    
    RESUME {
        int id PK
        int candidateId FK
        string fileName
        string filePath
        string fileType
        int fileSize
        boolean isPrimary
        datetime uploadDate
    }
    
    APPLICATION_STATUS {
        int id PK
        string name UK
        string description
        string color
        int orderIndex
    }
    
    APPLICATION {
        int id PK
        int positionId FK
        int candidateId FK
        int applicationStatusId FK
        date applicationDate
        text coverLetter
        text notes
        decimal expectedSalary
        date availabilityDate
        datetime createdAt
        datetime updatedAt
    }
    
    INTERVIEW_RESULT {
        int id PK
        string name UK
        string description
        string color
    }
    
    INTERVIEW {
        int id PK
        int applicationId FK
        int interviewStepId FK
        int interviewerId FK
        datetime scheduledDate
        datetime actualDate
        int interviewResultId FK
        int score
        text feedback
        text candidateNotes
        text interviewerNotes
        int duration
        datetime createdAt
        datetime updatedAt
    }

    %% Relationships
    COMPANY ||--o{ EMPLOYEE : employs
    COMPANY ||--o{ POSITION : offers
    COMPANY ||--o{ INTERVIEW_FLOW : defines
    
    EMPLOYEE_ROLE ||--o{ EMPLOYEE : assigns
    LOCATION ||--o{ POSITION : locatedAt
    EMPLOYMENT_TYPE ||--o{ POSITION : hasType
    
    POSITION ||--|| INTERVIEW_FLOW : uses
    POSITION ||--o{ APPLICATION : receives
    
    INTERVIEW_FLOW ||--o{ INTERVIEW_STEP : contains
    INTERVIEW_TYPE ||--o{ INTERVIEW_STEP : defines
    
    CANDIDATE ||--o{ APPLICATION : submits
    CANDIDATE ||--o{ EDUCATION : has
    CANDIDATE ||--o{ WORK_EXPERIENCE : has
    CANDIDATE ||--o{ RESUME : uploads
    
    APPLICATION ||--|| APPLICATION_STATUS : hasStatus
    APPLICATION ||--o{ INTERVIEW : schedules
    
    INTERVIEW ||--|| INTERVIEW_STEP : basedOn
    INTERVIEW ||--|| INTERVIEW_RESULT : hasResult
    EMPLOYEE ||--o{ INTERVIEW : conducts
```
## Prompt 4 (troubleshooting)

I got this error when running: npx prisma migrate reset:
✔ Are you sure you want to reset your database? All data will be lost. … yes

Applying migration 20251115173741_db_avc
Error: P3018

A migration failed to apply. New migrations cannot be applied before the error is recovered from. Read more about how to resolve migration issues in a production database: https://pris.ly/d/migrate-resolve

Migration name: 20251115173741_db_avc

Database error code: 42P01

Database error:
ERROR: relation "Education" does not exist

DbError { severity: "ERROR", parsed_severity: Some(Error), code: SqlState(E42P01), message: "relation "Education" does not exist", detail: None, hint: None, position: None, where_: None, schema: None, table: None, column: None, datatype: None, constraint: None, file: Some("namespace.c"), line: Some(636), routine: Some("RangeVarGetRelidExtended") }

