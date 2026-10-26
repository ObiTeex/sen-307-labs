# VUA-SEN307 Lab 2: Normalization (1NF to 3NF)

**Name:** Obi Celestine Tawo | **Matric No:** VUG/SEN/24/11868 | **Course:** VUA-SEN307 Database Systems

## 0. Unnormalized Table

| matric_no | student_name | student_dept | course_codes | course_titles | lecturers | lecturer_depts | session |
|---|---|---|---|---|---|---|---|
| 20231234 | Ada Obi | Software Engineering | SEN307, SEN301 | Database Systems, Software Architecture | Dr A. Okafor, Dr M. Bello | Software Eng., Software Eng. | 2026/2027 |
| 20231235 | John Musa | Computer Science | SEN307, COS301 | Database Systems, Operating Systems | Dr A. Okafor, Mrs F. Eze | Software Eng., Computer Sci. | 2026/2027 |
| 20231236 | Grace Okon | Software Engineering | SEN301 | Software Architecture | Dr M. Bello | Software Eng. | 2026/2027 |

## 1. First Normal Form (1NF)

Primary key: **(matric_no, course_code)**

| matric_no | student_name | student_dept | course_code | course_title | lecturer | lecturer_dept | session |
|---|---|---|---|---|---|---|---|
| 20231234 | Ada Obi | Software Engineering | SEN307 | Database Systems | Dr A. Okafor | Software Eng. | 2026/2027 |
| 20231234 | Ada Obi | Software Engineering | SEN301 | Software Architecture | Dr M. Bello | Software Eng. | 2026/2027 |
| 20231235 | John Musa | Computer Science | SEN307 | Database Systems | Dr A. Okafor | Software Eng. | 2026/2027 |
| 20231235 | John Musa | Computer Science | COS301 | Operating Systems | Mrs F. Eze | Computer Sci. | 2026/2027 |
| 20231236 | Grace Okon | Software Engineering | SEN301 | Software Architecture | Dr M. Bello | Software Eng. | 2026/2027 |

*Fixed: I split the lists so every cell holds one value, giving each student–course pair its own row.*

## 2. Second Normal Form (2NF)

**Student** (PK: matric_no)

| matric_no | student_name | student_dept |
|---|---|---|
| 20231234 | Ada Obi | Software Engineering |
| 20231235 | John Musa | Computer Science |
| 20231236 | Grace Okon | Software Engineering |

**Course** (PK: course_code)

| course_code | course_title |
|---|---|
| SEN307 | Database Systems |
| SEN301 | Software Architecture |
| COS301 | Operating Systems |

**Enrolment** (PK: matric_no + course_code)

| matric_no | course_code | session |
|---|---|---|
| 20231234 | SEN307 | 2026/2027 |
| 20231234 | SEN301 | 2026/2027 |
| 20231235 | SEN307 | 2026/2027 |
| 20231235 | COS301 | 2026/2027 |
| 20231236 | SEN301 | 2026/2027 |

**Course_Lecturer** (PK: course_code + lecturer)

| course_code | lecturer | lecturer_dept | session |
|---|---|---|---|
| SEN307 | Dr A. Okafor | Software Eng. | 2026/2027 |
| SEN301 | Dr M. Bello | Software Eng. | 2026/2027 |
| COS301 | Mrs F. Eze | Computer Sci. | 2026/2027 |

*Fixed: I removed partial dependencies, so student details depend only on matric_no and course and lecturer details depend only on the course, not on the full (matric_no, course_code) key.*

## 3. Third Normal Form (3NF)

**Department** (PK: dept_id)

| dept_id | dept_name |
|---|---|
| 1 | Software Engineering |
| 2 | Computer Science |

**Student** (PK: student_id; FK: dept_id)

| student_id | matric_no | full_name | dept_id |
|---|---|---|---|
| 1 | 20231234 | Ada Obi | 1 |
| 2 | 20231235 | John Musa | 2 |
| 3 | 20231236 | Grace Okon | 1 |

**Lecturer** (PK: lecturer_id; FK: dept_id)

| lecturer_id | lecturer_name | dept_id |
|---|---|---|
| 1 | Dr A. Okafor | 1 |
| 2 | Dr M. Bello | 1 |
| 3 | Mrs F. Eze | 2 |

**Course** (PK: course_id)

| course_id | course_code | course_title |
|---|---|---|
| 1 | SEN307 | Database Systems |
| 2 | SEN301 | Software Architecture |
| 3 | COS301 | Operating Systems |

**Enrolment** (PK: enrolment_id; FK: student_id, course_id)

| enrolment_id | student_id | course_id | session |
|---|---|---|---|
| 1 | 1 | 1 | 2026/2027 |
| 2 | 1 | 2 | 2026/2027 |
| 3 | 2 | 1 | 2026/2027 |
| 4 | 2 | 3 | 2026/2027 |
| 5 | 3 | 2 | 2026/2027 |

**Teaching** (PK: teaching_id; FK: course_id, lecturer_id)

| teaching_id | course_id | lecturer_id | session |
|---|---|---|---|
| 1 | 1 | 1 | 2026/2027 |
| 2 | 2 | 2 | 2026/2027 |
| 3 | 3 | 3 | 2026/2027 |

*Fixed: I removed transitive dependencies by moving department names into their own Department table (also fixing "Software Engineering" vs "Software Eng."), so renaming a department is now one update.*

## 4. Final Schema

```
Department (dept_id PK, dept_name)
Student    (student_id PK, matric_no, full_name, dept_id FK -> Department)
Lecturer   (lecturer_id PK, lecturer_name, dept_id FK -> Department)
Course     (course_id PK, course_code, course_title)
Enrolment  (enrolment_id PK, student_id FK -> Student, course_id FK -> Course, session)
Teaching   (teaching_id PK, course_id FK -> Course, lecturer_id FK -> Lecturer, session)
```

Student ↔ Course is many-to-many, resolved by **Enrolment**. Course ↔ Lecturer is many-to-many, resolved by **Teaching**.
