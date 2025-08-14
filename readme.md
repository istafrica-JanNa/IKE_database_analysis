# IKE Enrollment System - Entity Relationship Diagram

## Core Enrollment Tables ERD

```mermaid
erDiagram
    %% Person Tables
    prn_person {
        bigint id_ PK
        varchar civicno_
        varchar firstname_
        varchar middlename_
        varchar lastname_
        timestamp birthdate_
        char gender_
        bigint objversion_
    }
    
    prn_person_ike {
        bigint fk_prn_person PK,FK
        varchar ssid_
        timestamp schoolenteredstate_
        boolean schoolinginus_
        char pdhispaniclatino_
        bigint fk_pd_prn_basicrace_ike FK
        bigint fk_pd_prn_calpadsrace_ike FK
    }
    
    %% Core Member Framework
    mbr_member {
        bigint id_ PK
        bigint fk_prn_person FK
        bigint fk_grp_group FK
        bigint fk_rol_role FK
        varchar fk_sys_role FK
        timestamp startdate_
        timestamp enddate_
        boolean loginenabled_
        bigint objversion_
    }
    
    %% Enrollment Tables - Modern System
    unt_agemember {
        bigint fk_mbr_member PK,FK
        bigint fk_unt_agegroup FK
        bigint fk_unt_withdrawaltype FK
        bigint fk_cur_curriculum FK
        varchar memberno_
        timestamp enrollmentdate_
        timestamp withdrawaldate_
        char activestatus_
        boolean primaryschool_
    }
    
    unt_agemember_ike {
        bigint fk_unt_agemember PK,FK
        bigint fk_cur_schoolyear FK
        bigint fk_cur_schoolyear_next FK
        bigint fk_calp_enrollmenttype_ike FK
        bigint fk_unt_studentexitstatus_ike FK
        varchar externalid_
        boolean lunchprogram_
        boolean migranteducation_
        boolean specialeducation_
        boolean enrollmentcompleted_
        varchar ud_string1_
        varchar ud_string2_
        varchar ud_string3_
        varchar ud_string4_
        varchar ud_string5_
        date ud_date1_
        date ud_date2_
        bigint ud_int1_
        bigint ud_int2_
    }
    
    %% Group Structure
    grp_group {
        bigint id_ PK
        bigint fk_grp_grouptype FK
        bigint fk_org_element FK
        bigint fk_prd_period_start FK
        bigint fk_prd_period_end FK
        varchar name_
        varchar description_
        integer minmembers_
        integer maxmembers_
        bigint objversion_
    }
    
    unt_agegroup {
        bigint fk_grp_group PK,FK
        bigint fk_cur_curriculum FK
    }
    
    grp_groupperiod {
        bigint id_ PK
        bigint fk_grp_group FK
        bigint fk_cur_schoolyear FK
        bigint fk_prd_period FK
        varchar name_
        timestamp startdate_
        timestamp enddate_
        bigint objversion_
    }
    
    %% Grade/School Year
    cur_schoolyear {
        bigint id_ PK
        varchar systypeid_
        integer orderby_
        varchar name_
        varchar description_
        varchar code_
        boolean default_
    }
    
    %% Organization Structure
    org_element {
        bigint id_ PK
        varchar name_
        varchar code_
        varchar description_
        bigint fk_org_element FK
        bigint fk_prd_financialyear FK
    }
    
    %% Class Assignment Tables
    unt_classmember {
        bigint fk_mbr_member PK,FK
        bigint fk_unt_classgroup FK
    }
    
    unt_classmember_ike {
        bigint fk_unt_classmember PK,FK
        varchar externalid_
    }
    
    unt_classgroup {
        bigint fk_grp_group PK,FK
        bigint fk_res_room FK
    }
    
    %% Course Assignment Tables
    unt_educationmember {
        bigint fk_mbr_member PK,FK
        bigint fk_unt_educationgroup FK
        bigint fk_cur_schoolyear FK
        boolean primary_
    }
    
    unt_educationgroup {
        bigint fk_grp_group PK,FK
        bigint fk_cur_item FK
    }
    
    unt_educationgroup_ike {
        bigint fk_unt_educationgroup PK,FK
        varchar blockpattern_
        bigint externalid_
        bigint sectionnumber_
        boolean pdindependentstudy_
        boolean pddistancelearning_
    }
    
    %% Course Structure
    cur_course {
        bigint id_ PK
        bigint fk_cur_subject FK
        bigint fk_cur_coursetype FK
        varchar name_
        varchar coursecode_
        double credits_
        integer hours_
        timestamp startdate_
        timestamp enddate_
    }
    
    cur_courseitem {
        bigint fk_cur_item PK,FK
        bigint fk_cur_course FK
    }
    
    cur_course_ike {
        bigint fk_cur_course PK,FK
        boolean honorscourse_
        boolean academiccourse_
        boolean collegeprepcourse_
        boolean meetshighschoolreq_
    }
    
    %% Legacy Table (unused in modern system)
    unt_enrollment {
        bigint id_ PK
        bigint fk_prn_person FK
        bigint fk_unt_agegroup FK
        bigint fk_unt_unit FK
        boolean active_
        timestamp enrollmentdate_
        timestamp withdrawaldate_
        varchar status_
    }
    
    %% Program Assignment
    prgm_agememberprogram {
        bigint fk_unt_agemember PK,FK
        bigint fk_prgm_program FK
        timestamp participationstartdate_
        timestamp participationenddate_
    }
    
    %% Relationships
    prn_person ||--|| prn_person_ike : "extends"
    prn_person ||--o{ mbr_member : "person has members"
    
    mbr_member ||--|| unt_agemember : "member is age group member"
    unt_agemember ||--|| unt_agemember_ike : "extends with IKE data"
    
    grp_group ||--o| unt_agegroup : "group is age group"
    grp_group ||--o{ grp_groupperiod : "group has periods"
    grp_group ||--o{ mbr_member : "group has members"
    
    unt_agemember }o--|| unt_agegroup : "enrolled in age group"
    unt_agemember_ike }o--|| cur_schoolyear : "assigned to grade"
    grp_groupperiod }o--|| cur_schoolyear : "period for grade"
    
    org_element ||--o{ grp_group : "school contains groups"
    org_element ||--|| unt_enrollment : "legacy enrollment"
    
    mbr_member ||--o| unt_classmember : "class membership"
    unt_classmember ||--|| unt_classmember_ike : "extends"
    unt_classmember }o--|| unt_classgroup : "member of class"
    grp_group ||--o| unt_classgroup : "group is class"
    
    mbr_member ||--o{ unt_educationmember : "course enrollments"
    unt_educationmember }o--|| unt_educationgroup : "enrolled in course section"
    unt_educationgroup ||--|| unt_educationgroup_ike : "extends"
    grp_group ||--o| unt_educationgroup : "group is course section"
    unt_educationgroup }o--|| cur_courseitem : "teaches course"
    cur_courseitem }o--|| cur_course : "item of course"
    cur_course ||--|| cur_course_ike : "extends"
    
    unt_agemember ||--o{ prgm_agememberprogram : "program assignments"
```

## Table Categories

### Primary Enrollment Flow (Modern System)
1. **Person** → prn_person + prn_person_ike
2. **Member** → mbr_member  
3. **Enrollment** → unt_agemember + unt_agemember_ike (PRIMARY)
4. **Grade Assignment** → cur_schoolyear
5. **School Assignment** → unt_agegroup → grp_group → org_element

### Secondary Assignments (Optional)
- **Class Assignment** → unt_classmember + unt_classmember_ike
- **Course Enrollment** → unt_educationmember → unt_educationgroup
- **Program Assignment** → prgm_agememberprogram

### Legacy System (Unused)
- **unt_enrollment** - Original Atlas enrollment table (superseded by age group member system)

## Key Relationships

1. **Student Identity**: prn_person ↔ prn_person_ike
2. **Core Enrollment**: unt_agemember ↔ unt_agemember_ike (THE PRIMARY ENROLLMENT)
3. **Grade Level**: unt_agemember_ike → cur_schoolyear
4. **School**: unt_agegroup → grp_group → org_element
5. **Class**: unt_classmember → unt_classgroup
6. **Courses**: unt_educationmember → unt_educationgroup → cur_courseitem → cur_course

## Data Flow Summary

```
Person → Member → Age Group Member (Enrollment) → Grade + School + Class + Courses
  ↓         ↓            ↓                           ↓      ↓       ↓       ↓
prn_*   mbr_member  unt_agemember_ike        cur_schoolyear org_* unt_class* unt_education*
```

The **unt_agemember_ike** table is the heart of the enrollment system, containing all meaningful enrollment data and linking to all other enrollment-related entities.
