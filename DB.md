
## سوال ۱

### الف) 

```text
σ AirlineName='Caspian'(Flight)
```

---

### ب) 

```text
σ IsAvailable=false(Seat)
```

---

### ج) 

```text
Π Name(Passenger) - Π Name(
    (Passenger ⨝ Ticket ⨝ Flight)
    WHERE Destination='Yazd'
)
```

---

## سوال ۲

### داده‌ها:

### الف) 

```text
Π Name(
    (Member ⨝ Borrow ⨝ Book)
    WHERE Publisher='McGraw-Hill'
)
```

---

### ب) 

```text
Π Name(Member)
-
Π Name(
    (Member ⨝ Borrow ⨝ Book)
    WHERE Publisher='Wiley'
)
```

---

### ج)


```text
Π Name(Member)
WHERE COUNT(Borrow.ISBN) > 1
```

اینطوری هم میشه نوشتش:
```text
Member ⨝ (γ MemberId; COUNT(ISBN)>1 (Borrow))
```

---

## سوال ۳


### الف) 
```text
Π StudentName(Student)
÷
Π CourseId(σ Type=4(Course))
```

---

### ب) 

```text
Π StudentName(Student)
-
Π StudentName(
    (Student ⨝ Take ⨝ Course)
    WHERE Unit IN (2,3) AND Type=4
)
```

---

### ج) 

```text
Π TeacherName(
    (Teacher ⨝ Present ⨝ Course)
    WHERE Unit=2 OR Unit=3
)
```

و باید مطمئن بشیم که استاد هیچ درس دیگه‌ای تدریس نکرده:

```text
Π TeacherName(σ Unit IN (2,3)(Teacher ⨝ Present ⨝ Course))
-
Π TeacherName(σ Unit NOT IN (2,3)(Teacher ⨝ Present ⨝ Course))
```

---

### د) 

```text
Π CourseName(Course)
-
Π CourseName(
    (Course ⨝ Present)
    WHERE Semester=5
)
```
