# Complete Salesforce Project Guide - Step by Step

## Project Requirements Summary
- Create College, Course, and Student objects with proper relationships
- When College is deleted, Courses should be deleted
- When Course is deleted, Students should remain
- Count of Courses in College
- Student with DOB and auto-calculated Age
- Student skills fields (10th, 12th, BTech percentages) with aggregate
- Personal and Graduation details sections
- Two users: Admin and Restricted user
- Network access restrictions

---

## STEP 1: CREATE CUSTOM OBJECTS

### 1.1 Create College Object
1. **Navigate**: Setup (gear icon) → Object Manager
2. **Click**: Create → Custom Object
3. **Fill Details**:
   - Label: `College`
   - Plural Label: `Colleges`
   - Object Name: `College` (auto-filled)
   - Record Name: `College Name`
   - Data Type: `Text`
4. **Optional Features** (check these):
   - ✅ Allow Reports
   - ✅ Allow Activities  
   - ✅ Track Field History
5. **Click**: Save

### 1.2 Create Course Object
1. **Navigate**: Setup → Object Manager
2. **Click**: Create → Custom Object
3. **Fill Details**:
   - Label: `Course`
   - Plural Label: `Courses`
   - Object Name: `Course`
   - Record Name: `Course Name`
   - Data Type: `Text`
4. **Optional Features**: Same as above
5. **Click**: Save

### 1.3 Create Student Object
1. **Navigate**: Setup → Object Manager
2. **Click**: Create → Custom Object
3. **Fill Details**:
   - Label: `Student`
   - Plural Label: `Students`
   - Object Name: `Student`
   - Record Name: `Student Name`
   - Data Type: `Text`
4. **Optional Features**: Same as above
5. **Click**: Save

---

## STEP 2: CREATE RELATIONSHIPS

### 2.1 Create College-Course Relationship (Master-Detail)
1. **Navigate**: Setup → Object Manager → Course
2. **Click**: Fields & Relationships → New
3. **Select**: Master-Detail Relationship → Next
4. **Related To**: College → Next
5. **Field Details**:
   - Field Label: `College`
   - Field Name: `College` (auto-filled)
6. **Click**: Next → Next → Next
7. **Field-Level Security**: Keep default → Next
8. **Page Layouts**: Add to layout → Save

### 2.2 Create Course-Student Relationship (Lookup)
1. **Navigate**: Setup → Object Manager → Student
2. **Click**: Fields & Relationships → New
3. **Select**: Lookup Relationship → Next
4. **Related To**: Course → Next
5. **Field Details**:
   - Field Label: `Course`
   - Field Name: `Course`
6. **Click**: Next → Next → Next
7. **Field-Level Security**: Keep default → Next
8. **Page Layouts**: Add to layout → Save

---

## STEP 3: CREATE STUDENT FIELDS

### 3.1 Date of Birth Field
1. **Navigate**: Setup → Object Manager → Student
2. **Click**: Fields & Relationships → New
3. **Select**: Date → Next
4. **Field Details**:
   - Field Label: `Date of Birth`
   - Field Name: `Date_of_Birth`
5. **Click**: Next → Next → Next → Save

### 3.2 Age Formula Field
1. **Navigate**: Setup → Object Manager → Student
2. **Click**: Fields & Relationships → New
3. **Select**: Formula → Next
4. **Field Details**:
   - Field Label: `Age`
   - Field Name: `Age`
   - Formula Return Type: `Number`
   - Decimal Places: `0`
5. **Formula**:
```
IF(
  ISBLANK(Date_of_Birth__c),
  NULL,
  FLOOR((TODAY() - Date_of_Birth__c) / 365.25)
)
```
6. **Click**: Next → Next → Next → Save

### 3.3 Academic Percentage Fields
**Create these three fields one by one:**

#### 3.3.1 Tenth Percentage
1. **Navigate**: Setup → Object Manager → Student
2. **Click**: Fields & Relationships → New
3. **Select**: Number → Next
4. **Field Details**:
   - Field Label: `10th (%)`
   - Field Name: `Tenth`
   - Length: `5`
   - Decimal Places: `2`
5. **Click**: Next → Next → Next → Save

#### 3.3.2 Twelfth Percentage
1. **Repeat above steps** with:
   - Field Label: `12th (%)`
   - Field Name: `Twelfth`

#### 3.3.3 BTech Percentage
1. **Repeat above steps** with:
   - Field Label: `BTech (%)`
   - Field Name: `BTech`

### 3.4 Aggregate Formula Field
1. **Navigate**: Setup → Object Manager → Student
2. **Click**: Fields & Relationships → New
3. **Select**: Formula → Next
4. **Field Details**:
   - Field Label: `Aggregate %`
   - Field Name: `Aggregate`
   - Formula Return Type: `Number`
   - Decimal Places: `2`
5. **Formula**:
```
IF(
  ( IF( NOT(ISBLANK(Tenth__c)), 1, 0 ) + IF( NOT(ISBLANK(Twelfth__c)), 1, 0 ) + IF( NOT(ISBLANK(BTech__c)), 1, 0 ) ) = 0,
  NULL,
  ( BLANKVALUE(Tenth__c,0) + BLANKVALUE(Twelfth__c,0) + BLANKVALUE(BTech__c,0) ) /
    ( IF( NOT(ISBLANK(Tenth__c)), 1, 0 ) + IF( NOT(ISBLANK(Twelfth__c)), 1, 0 ) + IF( NOT(ISBLANK(BTech__c)), 1, 0 ) )
)
```
6. **Click**: Next → Next → Next → Save

---

## STEP 4: CREATE ROLL-UP SUMMARY

### 4.1 Course Count on College
1. **Navigate**: Setup → Object Manager → College
2. **Click**: Fields & Relationships → New
3. **Select**: Roll-Up Summary → Next
4. **Field Details**:
   - Field Label: `Course Count`
   - Field Name: `Course_Count`
5. **Summarized Object**: Course
6. **Roll-Up Type**: COUNT
7. **Click**: Next → Next → Next → Save

### 4.2 Student Count Field on Course (for Flow)
1. **Navigate**: Setup → Object Manager → Course
2. **Click**: Fields & Relationships → New
3. **Select**: Number → Next
4. **Field Details**:
   - Field Label: `Student Count`
   - Field Name: `Student_Count`
   - Length: `8`
   - Decimal Places: `0`
   - Default Value: `0`
5. **Click**: Next → Next → Next → Save

---

## STEP 5: ORGANIZE PAGE LAYOUTS

### 5.1 Student Page Layout
1. **Navigate**: Setup → Object Manager → Student
2. **Click**: Page Layouts → Student Layout
3. **Edit Layout**
4. **Create Sections**:
   - **Personal Details Section**: Add Name, Date of Birth, Age
   - **Academic Details Section**: Add Course, 10th (%), 12th (%), BTech (%), Aggregate %
5. **Save**

---

## STEP 6: CREATE USERS AND PROFILES

### 6.1 Create Restricted Profile
1. **Navigate**: Setup → Profiles
2. **Click**: New Profile
3. **Clone From**: Standard User
4. **Profile Name**: `Restricted User`
5. **Click**: Save
6. **Edit the Profile**:
   - **Object Settings** → Set appropriate permissions for College, Course, Student
   - **Field-Level Security** → Restrict sensitive fields
7. **Save**

### 6.2 Create Admin User
1. **Navigate**: Setup → Users
2. **Click**: New User
3. **Fill Details** (use your email variations):
   - First Name: `Admin`
   - Last Name: `User`
   - Email: `your.email+admin@domain.com`
   - Username: `admin.user@yourorg.com`
   - Profile: `System Administrator`
4. **Click**: Save

### 6.3 Create Restricted User
1. **Navigate**: Setup → Users
2. **Click**: New User
3. **Fill Details**:
   - First Name: `Restricted`
   - Last Name: `User`
   - Email: `your.email+restricted@domain.com`
   - Username: `restricted.user@yourorg.com`
   - Profile: `Restricted User`
4. **Click**: Save

---

## STEP 7: CONFIGURE NETWORK ACCESS

### 7.1 Trusted IP Ranges
1. **Navigate**: Setup → Network Access
2. **Click**: New
3. **Add IP Ranges** (example):
   - Start IP: `192.168.1.1`
   - End IP: `192.168.1.255`
   - Description: `Office Network`
4. **Save**

### 7.2 Profile-Level IP Restrictions (Optional)
1. **Navigate**: Setup → Profiles → Restricted User
2. **Edit**
3. **Login IP Ranges** → Add IP restrictions for this profile
4. **Save**

---

## STEP 8: CREATE FLOW FOR STUDENT COUNT

### 8.1 Create Record-Triggered Flow
1. **Navigate**: Setup → Flows
2. **Click**: New Flow → Record-Triggered Flow
3. **Object**: Student
4. **Trigger**: Create, Update, Delete
5. **Add Elements**:
   - **Get Records**: Get Course record
   - **Assignment**: Count students for that course
   - **Update Records**: Update Course.Student_Count__c
6. **Activate** the Flow

---

## STEP 9: TESTING CHECKLIST

### ✅ Test Cascade Delete
- [ ] Create College → Create Course under it → Delete College (Course should delete)
- [ ] Create Course → Create Student under it → Delete Course (Student should remain)

### ✅ Test Counts
- [ ] Verify College shows correct Course count
- [ ] Verify Course shows correct Student count (if Flow is working)

### ✅ Test Auto-Calculations
- [ ] Enter Date of Birth → Verify Age calculates automatically
- [ ] Enter academic percentages → Verify Aggregate calculates

### ✅ Test User Access
- [ ] Login as Admin → Verify full access
- [ ] Login as Restricted User → Verify limited access
- [ ] Test network restrictions (if configured)

---

## TROUBLESHOOTING TIPS

1. **If relationships don't work**: Check field API names end with `__c`
2. **If formulas fail**: Check field references use API names with `__c`
3. **If users can't login**: Check Profile permissions and IP restrictions
4. **If counts don't update**: Check Roll-up summary configuration

---

## COMPLETION CHECKLIST

- [ ] College object created
- [ ] Course object created with College master-detail
- [ ] Student object created with Course lookup
- [ ] DOB and Age fields working
- [ ] Academic percentage fields created
- [ ] Aggregate formula working
- [ ] Course count roll-up on College
- [ ] Student count field on Course
- [ ] Page layouts organized
- [ ] Two users created with different profiles
- [ ] Network access configured
- [ ] All testing completed successfully

**Congratulations! Your Salesforce project is complete!** 🎉
